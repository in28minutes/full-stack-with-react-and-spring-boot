# React 5 vs React 6 - Step by Step Changes

- [Step 02](#step-02)
- [Step 07](#step-07)
- [Step 08](#step-08)
- [Step 09](#step-09)
- [Step 10](#step-10)
- [Step 12](#step-12)
- [Step 18](#step-18)
- [Step 19](#step-19)
- [Step 47](#step-47)


## Step 02

### Step 02 - Using Create React App to Create and Launch a React Application

#### Recommended Versions For React 6

```
node --version
v16.13.2

npm --version
8.1.2
```

#### Commands to upgrade (If you have old version of Node or npm)

```
(Make sure that you login into command prompt as admin or use sudo)
npm cache clean -f
npm install -g n
n stable
```

## React V6+ upgrades

React V6 takes a different to routing and passing parameters compared to React V5. 

A number of learners still use React V5 in their projects. 
- Video lectures show code as in React V5
- Text lectures explain differences between React V5 and next versions!

Here are few quick references:
- [Step by step changes - React 6 vs React 5](https://github.com/in28minutes/full-stack-with-react-and-spring-boot/blob/master/00000-react-6-updates/react6-vs-react5-step-by-step-changes.md)
- Code Backups for this section: [Before Refactoring](https://github.com/in28minutes/full-stack-with-react-and-spring-boot/blob/master/00000-react-6-updates/react-6-01-single-component-for-all.md), [After Refactoring](https://github.com/in28minutes/full-stack-with-react-and-spring-boot/blob/master/00000-react-6-updates/react-6-02-after-refactoring.md)

At the end of each step, we show how you can do the same thing in React V6.

Are you excited for the journey?

Let's get started.


## Step 07

### Step 07 - Implementing Routing for Login and Welcome Components with React Route

#### Change Overview

1. You do NOT need to use `exact` anymore!
2. You should use `element={<LoginComponent />}` instead of `component=LoginComponent`

#### Final Code for TodoApp

```
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom'

class TodoApp extends Component {
    render() {
        return (
            <div className="TodoApp">
                <Router>
                    <Routes>
                        <Route path="/" element={<LoginComponent />} />
                        <Route path="/login" element={<LoginComponent />} />
                        <Route path="/welcome" element={<WelcomeComponent />} />
                    </Routes>
                </Router>
            </div>
        )
    }
}
```
## Step 08

### Step 08 - Implementing Routing from Login to Welcome Component

#### Change Overview

##### Change 1: New File - WithNavigation.jsx

To enable navigation from one page to another we need to use `useNavigate`. We create a separate functional component so that we can decorate any component that needs navigation!

```
import { useNavigate } from "react-router-dom";

function withNavigation(Component) {
  return props => <Component {...props} navigate={useNavigate()} />;
}

export default withNavigation

```

##### Change 2: Use LoginComponentWithNavigation instead of LoginComponent

Use `LoginComponentWithNavigation` instead of `LoginComponent`

```
const LoginComponentWithNavigation = withNavigation(LoginComponent);

<Route path="/login" element={<LoginComponentWithNavigation />} />

//<Route path="/login" component={LoginComponent}/> //REACT-5

```

##### Change 3: Use navigate instead of history.push

```
//this.props.history.push(`/welcome`)
this.props.navigate(`/welcome`)
```

##### Complete TodoApp after this step

```
import withNavigation from './WithNavigation.jsx'   

class TodoApp extends Component {
    render() {
        const LoginComponentWithNavigation = withNavigation(LoginComponent);
        return (
            <div className="TodoApp">
                <Router>
                    <Routes>
                        <Route path="/" element={<LoginComponentWithNavigation />} />
                        <Route path="/login" element={<LoginComponentWithNavigation />} />
                        <Route path="/welcome" element={<WelcomeComponent />} />
                    </Routes>
                </Router>
            </div>
        )
    }
}
```



## Step 09

### Step 09 - Adding an Error Component for Invalid URIs

#### Change Overview

1. No need to use a `Switch`
2. Use `<Route path="*" element={<ErrorComponent />} />`


```
    <Route path="*" element={<ErrorComponent />} />
    //<Route component={ErrorComponent}/>

```

##### Complete TodoApp after this step

```
import React, {Component} from 'react'

import { BrowserRouter as Router, Route, Routes } from 'react-router-dom'

import withNavigation from './WithNavigation.jsx'   

class TodoApp extends Component {
    render() {
        const LoginComponentWithNavigation = withNavigation(LoginComponent);
        return (
            <div className="TodoApp">
                <Router>
                    <Routes>
                        <Route path="/" element={<LoginComponentWithNavigation />} />
                        <Route path="/login" element={<LoginComponentWithNavigation />} />
                        <Route path="/welcome" element={<WelcomeComponent />} />
                        <Route path="*" element={<ErrorComponent />} />
                    </Routes>
                </Router>
            </div>
        )
    }
}

class LoginComponent extends Component {
    constructor(props){
        super(props)
        this.state = {
            username: 'in28minutes',
            password: '',
            hasLoginFailed: false,
            showSuccessMessage: false
        }
        this.handleChange = this.handleChange.bind(this)
        this.loginClicked = this.loginClicked.bind(this)
    }

    handleChange(event){
        this.setState({
            [event.target.name]:event.target.value
        })
    }

    loginClicked() {
        if(this.state.username==='in28minutes' && this.state.password==='dummy'){           
            this.props.navigate(`/welcome`)
        }
        else {
            this.setState({showSuccessMessage:false})
            this.setState({hasLoginFailed:true})
        }
        console.log(this.state)
    }

    render() {
        return (
            <div>
                {this.state.hasLoginFailed && <div className="alert alert-warning">Invalid Credentials</div>}
                {this.state.showSuccessMessage && <div>Login Sucessful</div>}
                <div className="TodoApp">
                    User Name: <input type="text" name="username" value={this.state.username} onChange={this.handleChange}/>
                    Password: <input type="password" name="password" value={this.state.password} onChange={this.handleChange}/>
                    <button onClick={this.loginClicked}>Login</button>
                </div>
            </div>
        )
    }
}

function ErrorComponent() {
    return <div>An Error Occurred. I don't know what to do! Contact support at abcd-efgh-ijkl</div>
}

class WelcomeComponent extends Component {
    render() {
        return (
                <div>Welcome in28minutes</div>
        )        
    }
}

export default TodoApp
```

## Step 10

### Step 10 - Adding Route Parameter for Welcome Component

#### Change Overview

##### Change 1: New File - withParams.jsx

To enable passing parameters to a component we need to use `withParams`. We create a separate functional component so that we can decorate any component that needs parameters!


```
import { useParams } from "react-router-dom";

function withParams(Component) {
  return props => <Component {...props} params={useParams()} />;
}

export default withParams
```

##### Change 2: Update TodoApp.jsx, render() method to use withParams

```
        const WelcomeComponentWithParams = withParams(WelcomeComponent);
        
        <Route path="/welcome/:name" element={<WelcomeComponentWithParams />} />

        //element={<WelcomeComponent />}
```

##### Change 3: Use this.props.params.name in WelcomeComponent render() method

```
<div>Welcome {this.props.params.name}</div>
//<div>Welcome {this.props.match.params.name}</div>
```

##### Complete TodoApp after this step

```
import React, {Component} from 'react'

import { BrowserRouter as Router, Route, Routes } from 'react-router-dom'

import withNavigation from './WithNavigation.jsx'   
import withParams from './WithParams.jsx'

class TodoApp extends Component {
    render() {
        const LoginComponentWithNavigation = withNavigation(LoginComponent);

        const WelcomeComponentWithParams = withParams(WelcomeComponent);
        return (
            <div className="TodoApp">
                <Router>
                    <Routes>
                        <Route path="/" element={<LoginComponentWithNavigation />} />
                        <Route path="/login" element={<LoginComponentWithNavigation />} />
                        <Route path="/welcome/:name" element={<WelcomeComponentWithParams />} />
                        <Route path="*" element={<ErrorComponent />} />
                    </Routes>
                </Router>
            </div>
        )
    }
}

class LoginComponent extends Component {
    constructor(props){
        super(props)
        this.state = {
            username: 'in28minutes',
            password: '',
            hasLoginFailed: false,
            showSuccessMessage: false
        }
        this.handleChange = this.handleChange.bind(this)
        this.loginClicked = this.loginClicked.bind(this)
    }

    handleChange(event){
        this.setState({
            [event.target.name]:event.target.value
        })
    }

    loginClicked() {
        if(this.state.username==='in28minutes' && this.state.password==='dummy'){           
            this.props.navigate(`/welcome/${this.state.username}`)
        }
        else {
            this.setState({showSuccessMessage:false})
            this.setState({hasLoginFailed:true})
        }
        console.log(this.state)
    }

    render() {
        return (
            <div>
                {this.state.hasLoginFailed && <div className="alert alert-warning">Invalid Credentials</div>}
                {this.state.showSuccessMessage && <div>Login Sucessful</div>}
                <div className="TodoApp">
                    User Name: <input type="text" name="username" value={this.state.username} onChange={this.handleChange}/>
                    Password: <input type="password" name="password" value={this.state.password} onChange={this.handleChange}/>
                    <button onClick={this.loginClicked}>Login</button>
                </div>
            </div>
        )
    }
}

function ErrorComponent() {
    return <div>An Error Occurred. I don't know what to do! Contact support at abcd-efgh-ijkl</div>
}

class WelcomeComponent extends Component {
    render() {
        return (
            <div>Welcome {this.props.params.name}</div>
        )        
    }
}

export default TodoApp
```

##  Step 12

###  Step 12 - Adding Bootstrap Framework and Creating Components for Header 

#### Change Overview

##### Change 1: Structure of HeaderComponent and FooterComponent under Router

```
    <div className="TodoApp">
        <Router>
            <HeaderComponent/>
            <Routes>
                <Route path="/" element={<LoginComponentWithNavigation />} />
                <Route path="/login" element={<LoginComponentWithNavigation />} />
                <Route path="/welcome/:name" element={<WelcomeComponentWithParams />} />
                <Route path="/todos" element={<ListTodosComponent />} />
                <Route path="*" element={<ErrorComponent />} />
            </Routes>
            <FooterComponent/>
        </Router>
    </div>
```

## Step 18

### Step 18 - Enabling Menu Links Based on User Authentication Token

#### Change Overview

##### Change 1: Use HeaderComponentWithNavigation instead of HeaderComponent

`render()` method in `TodoApp`
```
    const HeaderComponentWithNavigation = withNavigation(HeaderComponent);

    <HeaderComponentWithNavigation/>
    //<HeaderComponent/>
```


##### TodoApp after this step

```
class TodoApp extends Component {
    render() {
        const LoginComponentWithNavigation = withNavigation(LoginComponent);

        const WelcomeComponentWithParams = withParams(WelcomeComponent);

        const HeaderComponentWithNavigation = withNavigation(HeaderComponent);

        return (
            <div className="TodoApp">
                <Router>
                    <HeaderComponentWithNavigation/>
                    <Routes>
                        <Route path="/" element={<LoginComponentWithNavigation />} />
                        <Route path="/login" element={<LoginComponentWithNavigation />} />
                        <Route path="/welcome/:name" element={<WelcomeComponentWithParams />} />
                        <Route path="/todos" element={<ListTodosComponent />} />
                        <Route path="/logout" element={<LogoutComponent />} />
                        <Route path="*" element={<ErrorComponent />} />
                    </Routes>
                    <FooterComponent/>
                </Router>
            </div>
        )
    }
}

```

## Step 19

### Step 19 - Securing Components using Authenticated Route

#### Change Overview

##### Change 1: Use this.props.children in AuthenticatedRoute.jsx
```
return {...this.props.children}
//return <Route {...this.props} /> //REACT-5

```

##### Change 2: Use Navigate instead of Redirect
```
            return <Navigate to="/login" /> 
            //return <Redirect to="/login" /> //REACT-5
```

##### AuthenticatedRoute component (with changes)

```
import React, { Component } from 'react'
import { Navigate } from 'react-router-dom'
//import { Route, Redirect } from 'react-router-dom' //REACT-5
import AuthenticationService from './AuthenticationService.js'

class AuthenticatedRoute extends Component {
    render() {
        if (AuthenticationService.isUserLoggedIn()) {
            return {...this.props.children}
            //return <Route {...this.props} /> //REACT-5
        } else {
            return <Navigate to="/login" /> 
            //return <Redirect to="/login" /> //REACT-5
        }
    }
}

export default AuthenticatedRoute
```

##### Change 3: Usage of AuthenticatedRoute

Use `AuthenticatedRoute` inside the `element` attribute

```
//<AuthenticatedRoute path="/welcome/:name" component={WelcomeComponent}/>  //REACT-5

<Route path="/welcome/:name" element={
<AuthenticatedRoute>
  <WelcomeComponentWithParams />
</AuthenticatedRoute>
} /> 
```

###### class TodoApp (with changes)

```
//<AuthenticatedRoute path="/welcome/:name" component={WelcomeComponent}/>  //REACT-5

<Route path="/welcome/:name" element={
<AuthenticatedRoute>
  <WelcomeComponentWithParams />
</AuthenticatedRoute>
} /> 

//<AuthenticatedRoute path="/todos" component={ListTodosComponent}/>  //REACT-5
<Route path="/todos" element={
<AuthenticatedRoute>
  <ListTodosComponent />
</AuthenticatedRoute>
} />


//<AuthenticatedRoute path="/logout" component={LogoutComponent}/>  //REACT-5
<Route path="/logout" element={
<AuthenticatedRoute>
  <LogoutComponent />
</AuthenticatedRoute>
} />

```

## Step 47

### Step 47 - Creating Todo Component and Handle Routing

#### Change Overview

##### Change 1: TodoApp.jsx - Enable Navigation for ListTodosComponent

```
//REACT-6
const ListTodosComponentWithNavigation = withNavigation(ListTodosComponent) 

//REACT-6
<Route path="/todos" element={
    <AuthenticatedRoute>
        <ListTodosComponentWithNavigation /> 
    </AuthenticatedRoute>
} />

```

##### Change 2: ListTodosComponent - Use this.props.navigate

```
updateTodoClicked(id) {
    this.props.navigate(`/todos/${id}`)//REACT-6
    //this.props.history.push(`/todos/${id}`)
```

##### Change 3: TodoComponent - Enable Params and Navigation

```
class TodoApp extends Component {
//REACT-6
const TodoComponentWithParamsAndNavigation = withParams(withNavigation(TodoComponent));


//REACT-6
<Route path="/todos/:id" element={ 
    <AuthenticatedRoute>
        <TodoComponentWithParamsAndNavigation />
    </AuthenticatedRoute>
} />

```

##### Change 4: TodoComponent constructor - Use this.props.params

```
class TodoComponent extends Component {
    constructor(props) {
        super(props)

        this.state = {
            id: this.props.params.id, //REACT-6
            //id: this.props.match.params.id,
```

##### Change 5: TodoComponent onSubmit - Use this.props.navigate
```
onSubmit(values) {

    //OTHER CODE

    if (this.state.id === -1) {
        TodoDataService.createTodo(username, todo)
            .then(() => this.props.navigate('/todos')) //REACT-6
        //this.props.history.push('/todos')
    } else {
        TodoDataService.updateTodo(username, this.state.id, todo)
            .then(() => this.props.navigate('/todos'))//REACT-6
        //this.props.history.push('/todos')
    }

```

## End of this section
- Code Backups 
    - [Section 4 - Step 19 - Before Refactoring](https://github.com/in28minutes/full-stack-with-react-and-spring-boot/blob/master/00000-react-6-updates/react-6-01-single-component-for-all.md)
    - [Section 4 - Step 20 - After Refactoring](https://github.com/in28minutes/full-stack-with-react-and-spring-boot/blob/master/00000-react-6-updates/react-6-02-after-refactoring.md)
    - [Final - End of Course](https://github.com/in28minutes/full-stack-with-react-and-spring-boot/blob/master/00000-react-6-updates/react6-03-final.md)
