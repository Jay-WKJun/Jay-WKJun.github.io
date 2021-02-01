---
layout: post
title: React Routing
tags: [react, javascript, routing]
excerpt_separator: <!--more-->
---

# Routing

React는 기본적으로 SPA(Single Page App)가 기본이지만 MPA(Multi Page App)처럼 다른 주소에 다른 page를 보여줄 수 있다.

React는 하나의 HTML에 각 주소에 Routing된 Component들을 통해 새롭게 rendering하여 여러 Page가 존재하는 것 처럼 보여줄 수 있다.

<!--more-->

![MultiPageInSPA]({{ "/assets/img/aboutReact/MultiPageInSPA.PNG" | relative_url }})

![ReactRouting]({{ "/assets/img/aboutReact/ReactRouting.PNG" | relative_url }})

## server와 React의 통신 원리

![RoutingAndtheServer]({{ "/assets/img/aboutReact/RoutingAndtheServer.PNG" | relative_url }})

Server의 (모든 404Error 포함), routing을 설정한 <b>"base path"의 "index.html"</b>로 연결하여 보여준다.

basepath를 설정해도 맞지 않는 경우가 있으니 basepath의 index.html을 바라보도록 따로 설정해줘야하는 경우도 있다.

```javascript
//basepath설정 방법
<BrowserRouter basename="/react">
</BrowserRouter>
```

```javascript
//별도로 install이 필요
npm install --save react-router react-router-dom
/* web-app개발에는 react-router-dom만 필요하다 */
```

# Set Route

BrowserRouter를 import해 감싸면 해당 자식들이 Routing의 대상이 된다.

```javascript
import { BrowserRouter } from 'react-router-dom';

class App extends Component {
  render() {
    return (
      <BrowserRouter>
        <div className="App">
          <Blog />
        </div>
      </BrowserRouter>
    );
  }
}
export default App;
```

<b>props -> state 전달</b>

원래 props로 여러 data를 전달하던 것을 

route로 나누게 되면 component끼리 state를 통째로 전달하게 된다.

즉 각 한 route에 하나의 중심 stateful Component가 존재하게 된다는 것.

# react-route-dom components

## Route component

Path 별로 render할 JSX나 Component를 지정할때 사용한다.

```javascript
import { Route } from 'react-router-dom';
class Blog extends Component {
    render () {
        return (
            <div>
                <header className="Blog">
                    <nav>
                        <ul>
                            <li><a href="/">Home</a></li>
                            <li><a href="/new-post">New Post</a></li>
                        </ul>
                    </nav>
                </header>
                {/* path attribute로 path 지정 */}
                {/* exact는 정확히 "/"의 주소일때만 render하도록한다. */}
                <Route path="/" exact render={() => <h1>Home</h1>} />
                {/* Home 2는 그래서 "/"하위의 모든 주소에 render된다. */}
                <Route path="/" render={() => <h1>Home 2</h1>} />
                {/* 직접 render하지 않고 component를 붙이면 해당 component가 render된다. */}
                <Route path="/" component={Posts} />
                <Route path="/new-post" exact component={NewPost} />
            </div>
        );
    }
}
export default Blog;
```

## Link component

HTML의 <\a href=""> 즉, anchor tag를 대체한다.

anchor tag는 클릭 할때마다 새로운 요청을 보내 page를 계속 만들어낸다. 따라서 <b><u>state가 유지 되지 않는다.</b></u>

반면, Link는 바뀌는 부분만 re-render하고 완전 새로 reload하지 않기 때문에 <b><u>state가 유지된다.</b></u>

```javascript
import { Route, Link } from 'react-router-dom';
class Blog extends Component {
    render () {
        return (
            <div>
                <header className="Blog">
                    <nav>
                        <ul>
                            <li><Link to="/">Home</Link></li>
                            {/*
                                상대경로 설정(default=절대경로)
                                this.props.match.url은 내가 항상 내가 접속한 Domain주소를 가지므로 아래와 같이 설정해서 상대주소 형식으로 만들 수 있다.
                            */}
                            <li><Link to={{
                                pathname: this.props.match.url + "/"
                            }}>Relative Path</Link></li>
                            {/*object로 configure가능*/}
                            <li><Link to={{
                                pathname: "/new-post",
                                hash: '#submit',
                                search: '?quick-submit=true'
                            }}>New Post</Link></li>
                        </ul>
                    </nav>
                </header>
                <Route path="/" exact render={() => <h1>Home</h1>} />
                <Route path="/" render={() => <h1>Home 2</h1>} />
                <Route path="/" component={Posts} />
                <Route path="/new-post" exact component={NewPost} />
            </div>
        );
    }
}
export default Blog;
```

- Routing property

Link를 사용함으로서 props 혹은 this.props에 Routing 관련 property가 따라 붙는다.(history, location, match)

설정한 path와 path와 exact-same인지 config한 hash와 search 등의 정보를 볼 수 있다.

하지만 component tree에 따라 내려가지 않기 때문에 해당 Link에 해당하는 component에서만 볼 수 있다. 

property를 내려보고 싶다면 react-router-dom에서 제공하는 'withRouter'를 사용하거나 직접 props를 내려주면 된다.

```javascript
import { withRouter } from 'react-router-dom'

const post = () => {
    return (<h1>Post Component Terminal</h1>);
}

export default withRouter(post);
```

## NavLink

Link와 다른점은 active를 구별할 수 있다는 것이다.

```javascript
//현재 Domain/new-post에 있다고 가정한다.
import { Route, NavLink } from 'react-router-dom';
class Blog extends Component {
    render () {
        return (
            <div>
                {/*Link와 같이 자식 path에 있을 때, 부모 path들은 active상태이다.*/}
                <NavLink to="/">Home</NavLink>

                {/*
                configure (activeStyle로 직접 style을 넣어줘도 되고 .active로 css에서 설정해줘도 된다.)
                
                exact가 있으므로 활성화되지 않는다.
                */}
                <NavLink 
                to="/" 
                exact 
                activeClassName="my-active"
                activeStyle={{color: 'red'}}>Home 2</NavLink>

                {/*무조건 active*/}
                <NavLink to="/new-post">New Post</NavLink>
            </div>
        );
    };
};
export default Blog;
```

## Route Parameters

Route에 parameter를 심어서 보내는 방법

먼저 <b>Passing Route Parameter</b>

```javascript
//Posts.js
//request 역할 (1. Link를 이용)
class Posts extends Component{
    state = {
        posts: []
    }

    postSelectedHandler = (id) => {
        this.setState({selectedPostId: id});
    }

    componentDidMount () {
        axios.get( '/posts' )
            .then( response => {
                const posts = response.data.slice(0, 4);
                const updatedPosts = posts.map(post => {
                    return {
                        ...post,
                        author: 'Max'
                    }
                });
                this.setState({posts: updatedPosts});
                // console.log( response );
            } )
            .catch(error => {
                // console.log(error);
                this.setState({error: true});
            });
    }

    render(){
        let posts = <p style={{textAlign: 'center'}}>Something went wrong!</p>;
        if (!this.state.error) {
            posts = this.state.posts.map(post => {
                //Passing Route Parameters : Link안에 post.id심어 request
                return <Link to={"/" + post.id} key={post.id}>
                            <Post 
                            title={post.title} 
                            author={post.author}
                            clicked={() => this.postSelectedHandler(post.id)} />;
                        </Link>
            });
        }
        return(
            <section className="Posts">
                {posts}
            </section>
        );
    }
}
export default Posts;
```

Request 보내기 다른 방법

```javascript
//Posts.js
//request 역할 (2. JS를 통해 직접 link를 active하는 방법)
postSelectedHandler = (id) => {
        /*Link로 url을 심어두지 않고 직접 link를 active하는 방법*/
        //this.setState({selectedPostId: id});
        this.props.history.push({pathname: '/' + id});
}

return <Post 
        key={post.id}
        title={post.title} 
        author={post.author}
        clicked={() => this.postSelectedHandler(post.id)} />;
});
```

```javascript
//blog.js
//response 역할
import React, { Component } from 'react';
import './Blog.css';
import Posts from './Posts/Posts';
import NewPost from './NewPost/NewPost';
import FullPost from './FullPost/FullPost';
import { Route, Link } from 'react-router-dom';

class Blog extends Component {
    render () {
        return (
            <div>
                <header className="Blog">
                    <nav>
                        <ul>
                        <li><Link to="/">Home</Link></li>
                            <li><Link to={{
                                pathname: "/new-post",
                                hash: '#submit',
                                search: '?quick-submit=true'
                            }}>New Post</Link></li>
                        </ul>
                    </nav>
                </header>
                <Route path="/" exact component={Posts} />
                <Route path="/new-post" exact component={NewPost} />
                {/* Passing Route Parameters : response시에 /뒤에 온 string을 parameter로 받고 FullPost component를 rendering */}
                <Route path="/:id" exact component={FullPost} />
            </div>
        );
    }
}
export default Blog;
```

<b>Extract Route Parameter</b>

```javascript
import React, { Component } from 'react';
import axios from 'axios';
import './FullPost.css';

class FullPost extends Component {
    state = {
        loadedPost: null
    }

    componentDidMount () {
        //if ( this.props.id ) {
        //Extracting Route Parameters : 주소 /:id로 부터 받아온 id parameter를 props로부터 가져온다.
        if ( this.props.match.params.id ) {
            if ( !this.state.loadedPost || (this.state.loadedPost && this.state.loadedPost.id !== this.props.id) ) {
                axios.get( '/posts/' + this.props.match.params.id )
                    .then( response => {
                        // console.log(response);
                        this.setState( { loadedPost: response.data } );
                    } );
            }
        }
    }

    deletePostHandler = () => {
        axios.delete('/posts/' + this.props.id)
            .then(response => {
                console.log(response);
            });
    }

    render () {
        let post = <p style={{ textAlign: 'center' }}>Please select a Post!</p>;
        if ( this.props.id ) {
            post = <p style={{ textAlign: 'center' }}>Loading...!</p>;
        }
        if ( this.state.loadedPost ) {
            post = (
                <div className="FullPost">
                    <h1>{this.state.loadedPost.title}</h1>
                    <p>{this.state.loadedPost.body}</p>
                    <div className="Edit">
                        <button onClick={this.deletePostHandler} className="Delete">Delete</button>
                    </div>
                </div>

            );
        }
        return post;
    }
}

export default FullPost;
```

## Switch Component

기본적으로 하나의 path에서 해당 path에 active한 Route가 있다면 모두 render된다.

이것을 막아 단 하나의 Route만 render하는 것이 Switch Component이다.

```javascript
import { Route, Switch } from 'react-router-dom';
    //Switch 밖이기 때문에 항상 고려된다.
    <Route path="/" exact component={Posts} />
    /*
        :id는 어떤 string이든 받아들이기 때문에 /new-post와 언제나 충돌을 일으키지만
        Switch를 통해 해결할 수 있다.
        위에서 아래로 해당하는 Route가 있다면 그 Route를 render하고 나머지는 무시한다.
    */
    <Switch>
        {/*순서가 바뀌어도 문제 없다.*/}
        <Route path="/:id" exact component={FullPost} />
        <Route path="/new-post" component={NewPost} />
    </Switch>
```

## Redirect

1. Redirect Component (\<Redirect from="/" to="/posts"/>)
2. this.props.hitory.replace() or this.props.hitory.push()

```javascript
import { Redirect } from 'react-router-dom';
class NewPost extends Component {
    state = {
        submitted: false
    }

    postDataHandler = () => {
        axios.post('/posts', data)
            .then(response => {
                console.log(response);
                //방법1 : JSX와 Redirect Component 사용
                this.setState({
                    submitted: true
                })
                /*
                //방법2 : history사용
                this.props.history.replace("/posts")
                this.props.history.push("/posts")
                */
            });
    }

    render () {
        //방법1 : JSX와 Redirect Component 사용
        let redirect = null;
        if(this.state.submitted){
            redirect = <Redirect to="/posts"/>
        }
        return (
            <div className="NewPost">
                {/*<Redirect to="/posts"/> 여기서 redirect되면 아래의 content는 render되지 않는다.*/}
                {redirect}
                <button onClick={this.postDataHandler}>Add Post</button>
            </div>
        );
    }
}
export default NewPost;
```

# Handling 404 case

경로를 찾지 못했습니다! 에러를 다뤄보자

```javascript
//path를 지정안해주면, 경로가 없을 시 모두 이곳으로 온다.
<Route render={() => <h1>NOT Found!!!</h1>}>
```

# Optimizing! by Loading Routes Lazily

webPack은 import를 지정하면 해당 file을 global하게 사용할 수 있도록 가장 최상부에 올려놓는다.

하지만 그렇게 되면 bundle자체의 무게가 커져 performance에 영향이 있으므로 필요할때만 해당 code를 불러오도록 하는 방법이 있다.

Routing에선 해당 Route에 접속할때, 해당하는 code를 불러오는 식이다.(aka.splitting code)

많은 부분에서 기본 webPack의 기능을 활용한다.

React v.16.6부터 자체적인 Lazy기능이 생겼다.

## v.16.6 이후

```javascript
import React, { Component, Suspense } from 'react';
import { BrowserRouter, Route, NavLink } from 'react-router-dom';

import User from './containers/User';
import Welcome from './containers/Welcome';

//미리 import하는게 아니라 Posts를 불럿을때 import하도록 바꾼다.
const Posts = React.lazy(() => import('./container/Posts'))

class App extends Component {
  render() {
    return (
      <BrowserRouter>
        <React.Fragment>
          <nav>
            <NavLink to="/user">User Page</NavLink> |&nbsp;
            <NavLink to="/posts">Posts Page</NavLink>
          </nav>
          <Route path="/" component={Welcome} exact />
          <Route path="/user" component={User} />
          {/*해당 path를 요청했을 때만 Posts.js를 import한다.*/}
          <Route path="/posts" render={() => (
              {/*Suspense를 통해 Posts의 download와 render가 오래 걸릴경우 그 대체로 띄울것을 설정해줄 수 있다.*/}
              <Suspense fallback={<div>Loading...</div>}>
                <Posts />
              </Suspense>
          )} />
        </React.Fragment>
      </BrowserRouter>
    );
  }
}
```

## v.16.6 이전

직접 lazy한 js파일을 만들어 사용한다.

```javascript
//AsyncComponent.js
import React, { Component } from 'react';

const asyncComponent = (importComponent) => {
    return class extends Component {
        state = {
            component: null
        }
        componentDidMount(){
            //import는 promise를 return한다.
            importComponent()
                .then(cmp => {
                    this.setState({
                        component: cmp.default
                    })
                });
        }
        /*render와 props까지 그대로 return한다.(일반 Component와 동일)*/
        render() {
            const C = this.state.component

            return C ? <C {...this.props}></C> : null;
        }
    }
}

export default asyncComponent;
```

```javascript
import React, { Component } from 'react';
import './Blog.css';
import Posts from './Posts/Posts';

/*import를 dynamic하게 바꾸었다.*/
//import NewPost from './NewPost/NewPost';
import asyncComponent from '../../hoc/AsyncComponent'

const AsyncNewPost = asyncComponent(() => {
    return import('./NewPost/NewPost');
})
/*import를 dynamic하게 바꾸었다.*/

import FullPost from './FullPost/FullPost';
import { Route, Link, Switch, Redirect } from 'react-router-dom';
class Blog extends Component {
    render () {
        return (
            <div>
                <header className="Blog">
                    <nav>
                        <ul>
                        <li><Link to="/posts">Posts</Link></li>
                            <li><Link to={{
                                pathname: "/new-post",
                                hash: '#submit',
                                search: '?quick-submit=true'
                            }}>New Post</Link></li>
                        </ul>
                    </nav>
                </header>
                <Switch>
                    {/*/new-post route에 오면 AsyncNewPost를 통해 새로 import되고 import된 js는 render된다.*/}
                    <Route path="/new-post" exact component={AsyncNewPost} />
                    <Route path="/posts" component={Posts} />
                    <Redirect from="/" to="/posts" />
                </Switch>
            </div>
        );
    }
}
export default Blog;
```