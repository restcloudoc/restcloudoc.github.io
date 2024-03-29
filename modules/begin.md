上次修改时间: {docsify-updated}

# 快速开始
## 创建第一个编排应用
1. 首先进入到 RestCloud 的首页，点击 `服务集成中心`，`API编排`，点击 `创建新应用` 按钮。  

2. 在 `应用名称` 处填写应用的默认名称，例如： `我的第一个编排应用`。  
`应用Id` 可以随便起一个（如： `first_dev_mod`），但只能用 `英文字母`，`数字`，`下划线`。  

3. 填好之后，点击 `提交` ，你的第一个编排应用就创建好了。  

## 创建第一个编排流程
1. 点击进入刚刚创建好的编排应用，然后点击 `服务编排`，`所有编排流程`，点击 `新增流程` 按钮，选择 `手动新增`。  

2. 在 `新增流程` 页面， `所属应用` 处就是我们刚刚创建好的编排应用，然后在 `流程名称` 处填写要创建的流程名字，如： `我的第一个流程`。  

3. 然后点击 `保存` 按钮，你的第一个编排流程就创建好了。  

## 组装第一个编排流程
在 RestCloud 中，整个流程的组装就像是设计一条流程线一样，利用不同的 `组件` 进行组装，来将输入的数据处理成需要的效果。  


1. 点击刚刚创建好的流程中的 `流程设计` 字样（蓝色文字），进入到流程设计界面。  
初始的画布只有 `开始` 和 `结束` 两个节点，然后我们就可以从左边的组件库中拖入节点到画布。  


2. 首先从 流程基本组件 中拖入 `变量设置` 组件到画布上，然后双击该组件。  
在 节点名称 处设置该节点相关的名称，如： `进行传入数据的全局化处理`。  
然后点击 变量设置 标签，点击 新增变量，我们将设置一个传入的 `HTTP变量` 为 `全局变量`。  
在 变量Id 处填入 `myFirstVar`， 变量值 填入 `$.http.test`，然后点击 保存。  


3. 接着鼠标浮动到 开始 标签上，四条线上会出现四个圈，用鼠标左键随便点击一个圈，然后拉出一条线，连接到刚刚 进行传入数据的全局化处理 的节点上，这样两个节点之间就有了关系。  
从箭头的方向来看，是从 开始 节点走向 进行传入数据的全局化处理 节点。  


4. 然后我们再从 数据转换组件 中拉一个 `结果数据裁剪` 组件到画布。  
双击进入组件， 在 节点名称 处填写 `最终数据裁剪输出` 。  
点击 裁剪配置 标签，点击 添加字段，在 字段ID 处输入 `result`， 字段值 填写 `$.global.myFirstVar`，然后点击 保存。  
这里就用到了刚刚我们在 进行传入数据的全局化处理 节点中设置的 `全局变量`。  


5. 接着再从 进行传入数据的全局化处理 节点上拉一条线连接到 最终数据裁剪输出 节点，然后从 最终数据裁剪输出 节点拉一条线到 结束 结点，这个简单的流程组装就完成了。  

## 创建第一个API
1. 进入到刚刚创建 `我的第一个流程` 的那个页面，然后点击 `操作`，`创建API`。


2. 在 API名称 处可以填写 `我的第一个API`  
URL 可以将 `esb_flow_xxx` 的字样改为自己的路径，例如 `myFirstApi`  
然后点击 `提交`，第一个API 就创建好了。  

## 进行第一次API测试
1. 进入到刚刚创建 `我的第一个流程` 的那个页面，然后点击流程左边的加号，点击 创建的API 。


2. 点击 API那行 中的 `测试` 按钮，然后向下滑动，在 `RequestBody请求中` 粘贴如下 JSON  
```json
{
    "test": "hello"
}
```


3. 继续向下滑动，点击开始测试，可以在 `测试结果` 的 `响应体` 中看到刚刚设置的 `result` 值已经出现了。

## 进行第一个API发布
1. 进入到刚刚创建 `我的第一个流程` 的那个页面，然后点击流程左边的加号，点击 创建的API 。


2. 点击 API那行 中的 `操作` 按钮，再点击 `发布`，`提交`。


3. 这样之后，API即发布成功，可以在 `首页` ，`API门户` 中找到刚刚发布的 API。  




至此，第一个 `流程处理` 的学习就完成了。接下来可以继续学习以下内容：  

* [API编排](/modules/arrange)
* [API组件](/modules/unit)


-------------------------

上次修改时间: {docsify-updated}
