---
layout: post
title: Redux解决了什么问题？
summary: 对于这个问题，可能你会说————状态管理。这个答案没错，但是...太笼统了！
featured-img: react
categories: React
---


对于这个问题，可能你会说————状态管理。这个答案没错，但是...太笼统了！

让我们具体一点！

假设，在一个应用里面，不同层级的多个组件，都用到相同的一组数据，比如，用户头像。就像这样：

![]({{site.url}}{{site.baseurl}}/assets/img/no_subject/user_info.jpg)

基于这样的需求，代码应该是这样：

```html
<App>
  <Header avatar={avatar}>
    <UserMeta avatar={avatar}></UserMeta>
  </Header>
  <Body avatar={avatar}>
    <NavBar></NavBar>
    <Content avatar={avatar}>
      <UserInfo avatar={avatar}></UserInfo>
    </Content>
  </Body>
</App>
```

我们需要将`avatar`数据一层层传下去。

这样做的问题是什么？很多中间的层级，需要传递他们本身并不需要的数据！试想一下，一个大型应用，层级可不止这么简单。如果还像这样传递数据，那将是一场噩梦！（我知道`context`，其实，react-redux也是基于`context`来实现的）

这就是Redux要解决的问题！

Redux使得任何组件都可以直接获取store里面的数据，而不必通过层层传递。

当然，Redux还解决了很多其他的问题，比如使得debug更加容易、支持时间旅行式的调试、从长远来看使你的应用更好维护。但是，使得任何组件都可以直接获取store里面的数据，是它解决的最大的问题。

来看看Redux是怎么做的：

```javascript
import React from 'react';
import { connect } from 'react-redux';

const UserInfo = ({ avatar }) => (
  <img src={avatar}/>
);

const mapStateToProps = state => ({
  avatar: state.avatar
});

export { UserInfo };
export default connect(mapStateToProps)(UserInfo);
```

不需要层层传递，只需要使用`connect`就可以使得组件可以获取store里面的数据！简洁明了！

这就是Redux解决的最大的问题！

以上就是本文的全部内容了，感谢阅读！