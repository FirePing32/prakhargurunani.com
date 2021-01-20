# prakhargurunani.com

This is the source code of my personal website. Quick links -

* Home: www.prakhargurunani.com
* Blog: https://www.prakhargurunani.com/blog/
  * Tech posts: https://www.prakhargurunani.com/categories/tech/
  * Non-Tech posts: https://www.prakhargurunani.com/categories/non-tech/
* Contact: https://www.prakhargurunani.com/#contact

Powered by the [Academic theme](https://sourcethemes.com/academic/) for [Hugo](https://gohugo.io/). Hosted on Netlify.

Current Academic theme version v4 and hugo v0.6

# Usage

The theme is a submodule inside `themes/academic`.

If the repository is cloned for the first time, do the following to to fetch the hugo theme.
```
git submodule update --init
```

To update theme
```
git submodule update --remote --merge
cd themes/academic
git checkout <VERSION>
```

Follow https://github.com/gcushen/hugo-academic/releases and look at breaking changes section before updating.

Compile and update website
```
hugo
git add -A
git commit -S -m "Commit message"
git push
```

Run local server
```
hugo server -D
```

Display available updates to Academic.
```
cd themes/academic
git fetch
git log --pretty=oneline --abbrev-commit --decorate HEAD..origin/master
cd ../../
```