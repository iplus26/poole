# Navigator

译自 [Navigator - React Native 官方文档](https://facebook.github.io/react-native/docs/navigator.html#content)。

使用 `Navigator` 在不同的场景中切换，

## 基本使用

~~~javascript
<Navigator
    initialRoute={{name: 'My First Scene', index: 0}}
    renderScene={(route, navigator) =>
      <MySceneComponent
        name={route.name}
        onForward={() => {
          var nextIndex = route.index + 1;
          navigator.push({
            name: 'Scene ' + nextIndex,
            index: nextIndex,
          });
        }}
        onBack={() => {
          if (route.index > 0) {
            navigator.pop();
          }
        }}
      />
    }
  />
~~~

## Navigator 的方法
如果你有一个 Navigator 元素的引用 (ref) 的话，你可以使用以下方法来触发导航：

* `getCurrentRoutes()` -  返回当前的路由列表
* `jumpBack()` - “跳回”之前的场景，而不摧毁当前场景
* `jumpForward()` - “向前跳”到路由栈 (route stack) 的下一个场景
* `jumpTo(route)` - 转换到一个存在的场景中，而不摧毁当前的场景
* `push(route)` - 
* `pop()` - 跳转回去，并且摧毁当前的场景
* `replace(route)` - 用一个新的 *route* 来替换当前场景
* `replaceAtIndex(route, index)` - 根据特定的序号 *index* 替换一个场景
* `immediatelyResetRouteStack(routeStack)` - 用一个 *route* 的数组

## 属性 (Props)
