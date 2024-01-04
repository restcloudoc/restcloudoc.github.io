上次修改时间: {docsify-updated}

# 杂项描述

这里用于记录一些 RestCloud 中比较简单的一些功能。

# 一些常用路径
## 验证规则添加

基础路径  

```bash
/restcloud/admin/gateway/pluginMgr/pluginMgrContent?beanType=Validate&interface=cn.restcloud.framework.core.base.IBaseValidateBean
```

## 菜单路径修改

基础路径  

```bash
/restcloud/admin/designer?appid=core&appName=服务注册与发现
```

# 路径修改步骤
## API开发的版本管理
首先进入 [菜单修改路径](#菜单路径修改) ，然后进入 `菜单管理`， `iPaaS标准版本`， `服务集成中心`， `API开发`  
然后加一个 `版本管理` 子菜单，菜单URL 设置为 `/application/APPDesigner/AppVersionManager`  
由于添加了 子菜单 之后，原本的路径就无法再访问了，所以可以再加一个 子菜单 ，命名为 `开发服务中心`  
URL 设置为 `/application/APPDesigner/ListApplications?categoryId=*`  

-------------------------

上次修改时间: {docsify-updated}
