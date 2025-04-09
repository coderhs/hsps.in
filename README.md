# hsps.in

Source code to generate hsps.in

The website uses the theme: [Even](https://github.com/olOwOlo/hugo-theme-even)

Instalation

```sh
 git clone https://github.com/olOwOlo/hugo-theme-even themes/even
```

To run website run the command:

note:
Install hugo

```sh
sudo apt-get install hugo
```

```sh
hugo serve
```

Add an article

```sh
hugo new post/new-article-name.md
```

To deploy (meant for only Harisankar P S 😎😁)

Initial setup:

Clone coderhs.github.io inside your project.
Delete the public folder, rename 'coderhs.github.io' to folder named 'public'.

```sh
rm -Rf ./public
git clone git@github.com:coderhs/coderhs.github.io.git public
```

Run the script `./deploy.sh`.

The final output or live site can be seen at hsps.in after this.
