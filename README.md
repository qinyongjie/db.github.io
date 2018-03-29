#About
---
This is a hexo-next site.
---
# How to go on the site

```
$ npm install hexo-cli -g
$ npm install hexo --save
# add git support
$ npm install hexo-deployer-git --save
# add local search
$ npm install hexo-generator-search --save
$ npm install hexo-generator-searchdb --save

# others
$ npm install hexo-generator-index --save
$ npm install hexo-generator-archive --save
$ npm install hexo-generator-category --save
$ npm install hexo-generator-tag --save
$ npm install hexo-server --save
$ npm install hexo-deployer-git --save
$ npm install hexo-deployer-heroku --save
$ npm install hexo-deployer-rsync --save
$ npm install hexo-deployer-openshift --save
$ npm install hexo-renderer-marked@0.2 --save
$ npm install hexo-renderer-stylus@0.2 --save
$ npm install hexo-generator-feed@1 --save
$ npm install hexo-generator-sitemap@1 --save
```

---

# Hexo commands

```
$ hexo init
$ hexo clean
# hexo generate
# hexo deploy
$ hexo g -d
```

---

# Custom domain  
./_config.yml  
```
url: http://www.yourdomain
root: /
```
./source/CNAME
```
www.yourdomain
```