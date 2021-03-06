# react-keepalive-router


## 一 介绍

基于`react 16.8+` ,`react-router 4+` 开发的`react`缓存组件，可以用于缓存页面组件，类似`vue`的`keepalive`包裹`vue-router`的效果功能。

采用`react hooks`全新`api`,支持缓存路由，手动解除缓存，增加了**缓存的状态周期**，监听函数等。

后续版本会完善其他功能。


### demo

#### 缓存组件 + 监听


## 二 快速上手


### 下载

```bash
npm install react-keepalive-router --save
# or
yarn add react-keepalive-router
```


### 使用

### 1 基本用法


#### KeepaliveRouterSwitch


`KeepaliveRouterSwitch`可以理解为常规的Switch,也可以理解为 `keepaliveScope`,我们**确保整个缓存作用域，只有一个 `KeepaliveRouterSwitch` 就可以了**。

#### 常规用法

````jsx
import { BrowserRouter as Router, Route, Redirect ,useHistory  } from 'react-router-dom'
import { KeepaliveRouterSwitch ,KeepaliveRoute ,addKeeperListener } from 'react-keepalive-router'

const index = () => {
  useEffect(()=>{
    /* 增加缓存监听器 */
    addKeeperListener((history,cacheKey)=>{
      if(history)console.log('当前激活状态缓存组件：'+ cacheKey )
    })
  },[])
  return <div >
    <div >
      <Router  >
      <Meuns/>
      <KeepaliveRouterSwitch>
          <Route path={'/index'} component={Index} ></Route>
          <Route path={'/list'} component={List} ></Route>
          { /* 我们将详情页加入缓存 */ }
          <KeepaliveRoute path={'/detail'} component={ Detail } ></KeepaliveRoute>
          <Redirect from='/*' to='/index' />
       </KeepaliveRouterSwitch>
      </Router>
    </div>
  </div>
}
````


这里应该注意⚠️的是对于复杂的路由结构。或者KeepaliveRouterSwitch 包裹的子组件不是Route ,我们要给 `KeepaliveRouterSwitch` 增加特有的属性 `withoutRoute` 就可以了。如下例子🌰🌰🌰：

**例子一**

````jsx
<KeepaliveRouterSwitch withoutRoute >
  <div>
     <Route path="/a" component={ComponentA}  />
     <Route path="/b" component={ComponentB}  />
     <KeepaliveRoute path={'/detail'} component={ Detail } ></KeepaliveRoute>
  </div>
</KeepaliveRouterSwitch>

````

**例子二**

或者我们可以使用 `renderRoutes` 等`api`配合 `KeepliveRouterSwitch` 使用 。

````jsx
import {renderRoutes} from "react-router-config"
<KeepliveRouterSwitch withoutRoute  >{ renderRoutes(routes) }</KeepliveRouterSwitch> 
````


#### KeepaliveRoute

`KeepaliveRoute` 基本使用和 `Route`没有任何区别。


**在当前版本中⚠️⚠️⚠️如果 `KeepaliveRoute` 如果没有被 `KeepaliveRouterSwitch`包裹就会失去缓存作用。**

**效果**


![demo演示](https://raw.githubusercontent.com/AlienZhaolin/react-keepalive-router/master/md/111.gif)



### 2 其他功能



#### 1 缓存组件激活监听器

如果我们希望对当前激活的组件，有一些额外的操作，我们可以添加监听器，用来监听缓存组件的激活状态。

````js
addKeeperListener((history,cacheKey)=>{
  if(history)console.log('当前激活状态缓存组件：'+ cacheKey )
})
````
第一个参数未history对象，第二个参数为当前缓存路由的唯一标识cacheKey

#### 2 清除缓存

缓存的组件，或是被`route`包裹的组件，会在`props`增加额外的方法`cacheDispatch`用来清除缓存。

如果props没有`cacheDispatch`方法，可以通过


````js


import React from 'react'
import { useCacheDispatch } from 'react-keepalive-router'

function index(){
    const cacheDispatch = useCacheDispatch()
    return <div>我是首页
        <button onClick={()=> cacheDispatch({ type:'reset' }) } >清除缓存</button>
    </div>
}

export default index
````

**1 清除所有缓存**

````js
cacheDispatch({ type:'reset' }) 
````

**2 清除单个缓存**

````js
cacheDispatch({ type:'reset',payload:'cacheId' }) 
````

**3 清除多个缓存**

````js
cacheDispatch({ type:'reset',payload:['cacheId1'，'cacheId2'] }) 
````