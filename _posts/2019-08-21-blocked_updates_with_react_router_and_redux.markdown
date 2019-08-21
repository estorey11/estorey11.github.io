---
layout: post
title:      "Blocked updates with React Router and Redux"
date:       2019-08-21 19:54:09 +0000
permalink:  blocked_updates_with_react_router_and_redux
---


Most of the time, React Router and Redux work together with no propblems. However, you can run into cases where a link can cause your url to change, but it does not trigger your app to update. If this is happening, but manually changing your url does cause the app to update, then your problem is likely blocked updates. This happens when you have a component connected to Redux via `connect()(component)`  but is not routed with React Router as a route component, for example

`<Route path="path" render={routerProps => <Component {...routerProps} } /> }/>` 

instead of 

`<Route path='/path' component={Component}/>`

If this happens, then Redux does not know that the router props have changed, and does not know to re-render. The easiest fix for this is importing withRouter from React Router,  and wrapping it around wherever you connect that component to Redux.

```
import { withRouter } from 'react-router-dom'

export default withRouter(connect(mapStateToProps, mapDispatchToProps)(Component))
```
