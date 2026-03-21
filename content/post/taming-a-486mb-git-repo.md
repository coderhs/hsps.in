---
title: "Taming a 486MB Git Repo"
date: 2026-03-21T11:56:26+05:30
lastmod: 2026-03-21T11:56:26+05:30
draft: false
keywords: ["git", "git lfs", "git filter-repo", "repository size", "image optimization", "webp", "devops"]
description: "How I diagnosed and fixed a 486MB git repo where the actual code was only 6MB. Finding bloat, rewriting history, and choosing between Git LFS and compression."
tags: ["git", "devops", "performance"]
categories: ["DevOps"]
author: "Harisankar P S"

comment: true
toc: true
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false
hideHeaderAndFooter: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

I recently cloned a repository and noticed it was **486MB**. The actual source code? About **6MB**. That's a 98.7% overhead. Here's how I found the bloat and fixed it.

<!--more-->

## Where Did the Space Go?

The first step is understanding where the bytes are. Two commands tell you most of the story:

```bash
# Size of git's internal database (history, objects, packs)
du -sh .git
# 285M    .git

# Size of the working directory (what's actually checked out)
du -sh --exclude=.git .
# 194M    .
```

So 285MB was git history and 194MB was the working tree — mostly images. The code, templates, and config files were about 6MB total.

## Finding the Largest Files

Git stores every version of every file as a blob. To find the biggest ones:

```bash
git rev-list --objects --all \
  | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' \
  | awk '/^blob/ {print $3, $4}' \
  | sort -rn \
  | head -20
```

Output (trimmed):

```
10485760  public/images/treatments/shirodhara5.JPG
 9437184  public/images/temp/0L5A7347.JPG
 9306112  public/images/temp/0L5A7309.JPG
 8912896  public/images/treatments/abhyangam3.JPG
 7340032  public/images/temp/0L5A7218.JPG
 7168000  public/images/temp/0L5A7317.JPG
 ...
```

The pattern was clear: **raw camera photos at 7-10MB each**. Some were treatment photos used on the site. Others were in a `temp/` folder that had already been deleted.

## The Ghost Files Problem

Here's the thing that surprises people: **deleting a file from git doesn't reclaim the space**.

Six photos (~50MB total) had been removed from `public/images/temp/` months ago. They didn't exist in the working directory anymore. But git faithfully preserved every byte in its object database — because that's what git does. It keeps the full history so you can go back to any commit.

Running `git rm` and committing only adds a new commit that says "this file no longer exists." The old blob stays in `.git/objects/` forever.

```bash
# These files are gone from disk...
ls public/images/temp/
# ls: cannot access 'public/images/temp/': No such file or directory

# ...but still in git's object database
git log --all --full-history -- "public/images/temp/"
# commit fbcc4cf ... Clean up unused template files
```

## Finding Orphaned Images

Beyond deleted files in git history, there were images on disk that no code referenced anymore. A quick check:

```bash
# List all images
find public/images -type f \( -name "*.jpg" -o -name "*.png" -o -name "*.webp" \) > /tmp/all-images.txt

# For each image, check if it's referenced in source code
while read img; do
  basename=$(basename "$img")
  if ! grep -rq "$basename" --include="*.js" --include="*.jsx" --include="*.ts" --include="*.tsx" --include="*.astro" --include="*.html" .; then
    echo "ORPHANED: $img ($(du -h "$img" | cut -f1))"
  fi
done < /tmp/all-images.txt
```

This found five treatment images (~35MB) that were commented out in the data file but still sitting on disk.

## The Fix: Compression First, Then History

My initial instinct was to reach for Git LFS. But I stopped and thought about it:

**The images were 7-10MB each because they were unprocessed camera photos.** No web page needs a 10MB JPEG. The site was already slow because browsers had to download these massive files.

### Step 1: Compress the images

```bash
# Install ImageMagick if you don't have it
# sudo apt install imagemagick webp

# Resize to max 1200px wide and convert to WebP
for img in public/images/treatments/*.JPG public/images/treatments/*.jpg; do
  output="${img%.*}.webp"
  convert "$img" -resize '1200x>' -quality 80 "$output"
  echo "$(du -h "$img" | cut -f1) -> $(du -h "$output" | cut -f1)  $img"
done
```

Results:

```
9.4M -> 84K   public/images/treatments/shirodhara5.JPG
8.5M -> 76K   public/images/treatments/abhyangam3.JPG
7.8M -> 71K   public/images/treatments/kizhi2.JPG
...
```

**192MB of images became ~5MB.** A 97% reduction, and the site got faster too.

### Step 2: Remove deleted files from history

Now for the 285MB git database. `git filter-repo` is the modern replacement for the older `git filter-branch`:

```bash
# Install git-filter-repo
pip install git-filter-repo

# Remove the deleted temp folder from all history
git filter-repo --path public/images/temp/ --invert-paths

# Also remove the original large JPGs (now replaced with WebP)
git filter-repo --path-glob '*.JPG' --invert-paths
```

After a `git gc --aggressive`:

```
# Before
du -sh .git
# 285M

# After
du -sh .git
# 12M
```

### Step 3: Remove orphaned images and commit

```bash
# Delete the orphans found earlier
rm public/images/treatments/upanaham.jpg
rm public/images/treatments/kati-vasti.jpg
# ... etc

# Remove the old JPGs (replaced with WebP)
rm public/images/treatments/*.JPG

git add -A
git commit -m "Replace raw camera JPGs with optimized WebP, remove orphaned images"
```

## Do You Actually Need Git LFS?

Git LFS (Large File Storage) replaces large files in your repo with small pointer files. The actual content is stored on a separate LFS server. It's the go-to recommendation for large binary files.

But consider the trade-offs:

| | Git LFS | Compression |
|--|---------|-------------|
| **Setup complexity** | Everyone needs `git-lfs` installed | One-time conversion |
| **Cost** | GitHub: 1GB free, then $5/50GB/month | Free |
| **Clone speed** | Fast clone, then LFS fetches on checkout | Fast clone, small files |
| **CI/CD** | Needs LFS configured in CI | Just works |
| **Offline access** | Needs LFS server for file content | Everything is local |
| **History rewrite** | `git lfs migrate` rewrites history | `git filter-repo` rewrites history |

### When LFS makes sense

- **Binary assets that can't be compressed** (compiled binaries, fonts, videos)
- **Files that change frequently** (design files, large datasets)
- **Large teams** where you want to control who downloads what

### When compression is the better answer

- **Images for a website** — they should be optimized anyway for page speed
- **One-time cleanup** — if the files won't be re-added at their original size
- **Small teams / personal projects** — LFS adds complexity you don't need

In our case, compression was clearly the right call. The images *needed* to be smaller for the website to perform well. LFS would have just hidden the problem — visitors would still be downloading 10MB JPEGs.

### The hybrid approach

If you do expect large binaries in the future, set up LFS *after* cleaning up:

```bash
# Track future large images with LFS
git lfs install
git lfs track "*.psd"
git lfs track "*.raw"
git lfs track "*.mp4"
git add .gitattributes
git commit -m "Configure Git LFS for design and video files"
```

And add a pre-commit hook to catch oversized files before they enter history:

```bash
#!/bin/bash
# .git/hooks/pre-commit

MAX_SIZE=500000  # 500KB in bytes

for file in $(git diff --cached --name-only --diff-filter=ACM); do
  size=$(wc -c < "$file" 2>/dev/null || echo 0)
  if [ "$size" -gt "$MAX_SIZE" ]; then
    echo "ERROR: $file is $(( size / 1024 ))KB (max ${MAX_SIZE}KB)"
    echo "Compress the file or add it to Git LFS"
    exit 1
  fi
done
```

## The Caveats

Rewriting history with `git filter-repo` (or `git lfs migrate`) is a **destructive operation**:

- You'll need to **force-push** to the remote
- All collaborators must **re-clone** (their local history won't match)
- **Open PRs** will have conflicts
- **Commit hashes change** — any references to specific commits break

For a small team or personal project, this is fine. For a large team, coordinate first. Do it on a weekend. Announce it. Have everyone push their branches before you rewrite.

## The Result

| | Before | After |
|--|--------|-------|
| `.git` directory | 285 MB | 12 MB |
| Working directory | 194 MB | 5 MB |
| **Total** | **486 MB** | **17 MB** |
| Clone time | ~45s | ~2s |
| Largest image | 10 MB | 84 KB |
| Page load (images) | Painful | Fast |

A 96% reduction in repo size, and the website got significantly faster as a side effect.

The lesson: before reaching for Git LFS, ask yourself — **should these files be this large in the first place?**
