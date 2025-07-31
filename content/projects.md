---
title: "Projects | Open Source and Others"
date: 2022-06-05T15:22:34-07:00
lastmod: 2020-06-05T15:22:34-07:00
draft: false
keywords: []
description: "Harisankar P S"
tags: ["projects"]
categories: ["projects"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

# Projects

<p class="projects-intro">Link to projects I worked on over the years. There are more in <a href="https://github.com/coderhs" target="_blank" rel="noopener">Github</a>.</p>

<div class="projects-grid">
  <!-- Wifi QR -->
  <div class="project-card">
    <div class="project-header">
      <h3><a href="https://wifi-qr.hsps.in" target="_blank" rel="noopener">Wifi-qr</a></h3>
      <div class="project-tech">Next.js</div> <div class="project-tech">Vercel</div>
    </div>
    <div class="project-description">
      A simple next.js application hosted on vercel, that will generate a sharable QR code of your wifi connection. Using the mobile camera app one can autojoin to this network without needing to type.
    </div>
    <div class="project-links">
      <a href="https://wifi-qr.hsps.in" target="_blank" rel="noopener" class="project-link demo">Live Demo</a>
      <a href="https://github.com/coderhs/wifi-qr" target="_blank" rel="noopener" class="project-link code">GitHub</a>
    </div>
  </div>

  <!-- Column to Array -->
  <div class="project-card">
    <div class="project-header">
      <h3><a href="https://hsps.in/column_to_array/" target="_blank" rel="noopener">Column to Array</a></h3>
      <div class="project-tech">Ruby</div> <div class="project-tech">WebAssembly</div>
    </div>
    <div class="project-description">
      Convert column copied from a spreadsheet to array. The site runs on web assembly executing ruby in your browser.
    </div>
    <div class="project-links">
      <a href="https://hsps.in/column_to_array/" target="_blank" rel="noopener" class="project-link demo">Live Demo</a>
      <a href="https://github.com/coderhs/column_to_array" target="_blank" rel="noopener" class="project-link code">GitHub</a>
    </div>
  </div>

  <!-- CSVSqlWeb -->
  <div class="project-card">
    <div class="project-header">
      <h3><a href="https://hsps.in/csvsqlweb/" target="_blank" rel="noopener">CSVSqlWeb</a></h3>
      <div class="project-tech">Web Assembly</div> <div class="project-tech">Javascript</div>
    </div>
    <div class="project-description">
      Run SQL queries on your CSV file from the web.
    </div>
    <div class="project-links">
      <a href="https://hsps.in/csvsqlweb/" target="_blank" rel="noopener" class="project-link demo">Live Demo</a>
      <a href="https://github.com/coderhs/csvsqlweb" target="_blank" rel="noopener" class="project-link code">GitHub</a>
    </div>
  </div>

  <!-- Step Counter -->
  <div class="project-card">
    <div class="project-header">
      <h3><a href="https://step-counter.hsps.in/" target="_blank" rel="noopener">Step Counter</a></h3>
      <div class="project-tech">Next.js</div> <div class="project-tech">Vercel</div>
    </div>
    <div class="project-description">
      Count the number of steps you need to walk to lose weight.
    </div>
    <div class="project-links">
      <a href="https://step-counter.hsps.in/" target="_blank" rel="noopener" class="project-link demo">Live Demo</a>
      <a href="https://github.com/coderhs/step_counter" target="_blank" rel="noopener" class="project-link code">GitHub</a>
    </div>
  </div>
</div>

<style>
.projects-intro {
  font-size: 1.1em;
  color: #666;
  margin-bottom: 2rem;
  font-style: italic;
}

.projects-grid {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
  margin-top: 1.5rem;
}

.project-card {
  border: 1px solid #e1e5e9;
  border-radius: 6px;
  padding: 1rem;
  background: #ffffff;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
  position: relative;
}

.project-card:hover {
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  transform: translateY(-2px);
}

.project-header {
  margin-bottom: 0.75rem;
}

.project-header h3 {
  margin: 0 0 0.3rem 0;
  font-size: 1.1em;
}

.project-header h3 a {
  color: #333;
  text-decoration: none;
}

.project-header h3 a:hover {
  color: #007acc;
}

.project-tech {
  font-size: 0.75em;
  color: #007acc;
  font-weight: 500;
  background: #f0f8ff;
  display: inline-block;
  padding: 0.15rem 0.4rem;
  border-radius: 3px;
  margin-right: 0.3rem;
}

.project-description {
  line-height: 1.5;
  margin-bottom: 1rem;
  font-size: 0.9em;
}

.project-links {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}

.project-link {
  text-decoration: none;
  padding: 0.4rem 0.8rem;
  border-radius: 4px;
  font-size: 0.8em;
  font-weight: 500;
  transition: all 0.2s ease;
  border: 1px solid;
}

.project-link.demo {
  color: white;
  border-color: #007acc;
}

.project-link.demo:hover {
  background: #005fa3;
  border-color: #005fa3;
}

.project-link.code {
  background: transparent;
  color: #333;
  border-color: #ddd;
}

.project-link.code:hover {
  background: #f5f5f5;
  border-color: #bbb;
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
  .project-card {
    border-color: #4a5568;
  }

  .project-header h3 a {
    color: #e2e8f0;
  }

  .project-description {
  }

  .project-tech {
    background: #2a4a6b;
    color: #90cdf4;
  }

  .project-link.code {
    color: #e2e8f0;
    border-color: #4a5568;
  }

  .project-link.code:hover {
    background: #4a5568;
    border-color: #718096;
  }
}

/* Mobile responsiveness */
@media (max-width: 768px) {
  .projects-grid {
    gap: 1rem;
  }

  .project-card {
    padding: 0.75rem;
  }

  .project-links {
    justify-content: center;
  }
}
</style>

