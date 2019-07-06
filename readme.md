## 个人博客
[![blog](https://img.shields.io/badge/build-passed-green.svg)](https://github.com/caoweiju/tc_blog)
[![blog](https://img.shields.io/badge/version-v0.0.1-orange.svg)](https://www.caoweiju.com/)

- [x] 区分个人git config和公司git config
- [x] 完成搭建
- [x] 完成私有域名的映射和https的支持
- [ ] 更新文章内容
- [ ] 完善文章内容

地址：[caoweiju的个人博客](https://www.caoweiju.com)
````
.
├── _config.yml
├── db.json
├── imgs
├── package-lock.json
├── package.json
├── readme.md
├── scaffolds
│   ├── draft.md
│   ├── page.md
│   └── post.md
├── source
│   ├── _data  // 个性化配置
│   ├── _posts // md文章目录
│   ├── categories
│   └── tags
├── themes
│   ├── landscape
│   └── next
└── yarn.lock

````
地址：[个人博客](https://www.caoweiju.com)
使用hexo+next主题+netlify部署完成，里面主要是包含个人的一些技术文章之类的，包括一些比较琐碎的内容。

* [关于hexo](https://github.com/hexojs/hexo)
* [关于hexo-theme-next](https://github.com/iissnan/hexo-theme-next)
* 关于简单定制next主题部分，本人使用[Theme configurations using Hexo data files](https://github.com/iissnan/hexo-theme-next/issues/328)
* 需要深度定制的人：可以先`fork`一份`hexo-theme-next`，然后通过`submodule||subtreee`来把`next`主题当做子项目引入，不过这需要用户能够管理好`fork`分支，跟进`master`的`update`和`bugfix`
* 关于服务部署[netlify部署](https://app.netlify.com)，参考教程[step-by-step搭建流程](https://www.netlify.com/blog/2015/10/26/a-step-by-step-guide-hexo-on-netlify/)

[简单徽章制作来源](https://shields.io/)