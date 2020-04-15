<!---
Current Directory : /in28Minutes/git/full-stack-with-react-and-spring-boot
-->

## Complete Code Example


### /frontend/todo-app/public/index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, shrink-to-fit=no"
    />
    <meta name="theme-color" content="#000000" />
    <!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See https://developers.google.com/web/fundamentals/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.

      Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
    <title>My Todo Application</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
  </body>
</html>
```
---

### /frontend/todo-app/public/manifest.json

```json
{
  "short_name": "React App",
  "name": "Create React App Sample",
  "icons": [
    {
      "src": "favicon.ico",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#000000",
  "background_color": "#ffffff"
}
```
---

### /frontend/todo-app/src/bootstrap.css

```css
@import url(https://unpkg.com/bootstrap@4.1.0/dist/css/bootstrap.min.css)
```
---

### /frontend/todo-app/src/App.css

```css
.footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 40px;
  background-color: #222222;
}

.App {
  text-align: center;
}

.App-logo {
  animation: App-logo-spin infinite 20s linear;
  height: 40vmin;
  pointer-events: none;
}

.App-header {
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
  color: white;
}

.App-link {
  color: #61dafb;
}

@keyframes App-logo-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
```
---

### /frontend/todo-app/src/index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```
---

### /frontend/todo-app/src/index.css

```css
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
}
```
---

### /frontend/todo-app/src/components/todo/WelcomeComponent.jsx

```
import React, {Component} from 'react'
import {Link} from 'react-router-dom'
import HelloWorldService from '../../api/todo/HelloWorldService.js'

class WelcomeComponent extends Component {
    
    constructor(props) {
        super(props)
        this.retrieveWelcomeMessage = this.retrieveWelcomeMessage.bind(this)
        this.state = {
            welcomeMessage : ''
        }
        this.handleSuccessfulResponse = this.handleSuccessfulResponse.bind(this)
        this.handleError = this.handleError.bind(this)
    }

    render() {
        return (
            <>
                <h1>Welcome!</h1>
                <div className="container">
                    Welcome {this.props.match.params.name}. 
                    You can manage your todos <Link to="/todos">here</Link>.
                </div>
                <div className="container">
                    Click here to get a customized welcome message.
                    <button onClick={this.retrieveWelcomeMessage} 
                        className="btn btn-success">Get Welcome Message</button>
                </div>
                <div className="container">
                    {this.state.welcomeMessage}
                </div>
                
            </>
        )        
    }

    retrieveWelcomeMessage() {
        // HelloWorldService.executeHelloWorldService()
        // .then( response => this.handleSuccessfulResponse(response) )

        // HelloWorldService.executeHelloWorldBeanService()
        // .then( response => this.handleSuccessfulResponse(response) )

        HelloWorldService.executeHelloWorldPathVariableService(this.props.match.params.name)
        .then( response => this.handleSuccessfulResponse(response) )
        .catch( error => this.handleError(error) )
    }

    handleSuccessfulResponse(response) {
        console.log(response)
        this.setState({welcomeMessage: response.data.message})
    }

    handleError(error) {
        console.log(error.response)
        let errorMessage = '';
        
        if(error.message) 
            errorMessage += error.message

        if(error.response && error.response.data) {
            errorMessage += error.response.data.message
        }

        this.setState({welcomeMessage: errorMessage})
    }

}


export default WelcomeComponent
```
---

### /frontend/todo-app/src/components/todo/ListTodosComponent.jsx

```
import React, {Component} from 'react'
import TodoDataService from '../../api/todo/TodoDataService.js'
import AuthenticationService from './AuthenticationService.js'
import moment from 'moment'

class ListTodosComponent extends Component {

    constructor(props){
        console.log('constructor')
        super(props)
        this.state = {
            todos : [],
            message : null
        }
        this.deleteTodoClicked = this.deleteTodoClicked.bind(this)   
        this.updateTodoClicked = this.updateTodoClicked.bind(this)   
        this.addTodoClicked = this.addTodoClicked.bind(this)
        this.refreshTodos = this.refreshTodos.bind(this)
    }

    componentWillUnmount() {
        console.log('componentWillUnmount')
    }
    
    shouldComponentUpdate(nextProps, nextState) {
        console.log('shouldComponentUpdate')
        console.log(nextProps)
        console.log(nextState)
        return true
    }

    componentDidMount() {
        console.log('componentDidMount')
        this.refreshTodos();
        console.log(this.state)
    }

    refreshTodos() {
        let username = AuthenticationService.getLoggedInUserName()
        TodoDataService.retrieveAllTodos(username)
          .then(
              response => {
                  //console.log(response);
                  this.setState({todos : response.data})
              }
          ) 
    }

    deleteTodoClicked(id) {
        let username = AuthenticationService.getLoggedInUserName()
        //console.log(id + " " + username);
        TodoDataService.deleteTodo(username, id)
         .then (
             response => {
                this.setState({message : `Delete of todo ${id} Successful`})
                this.refreshTodos()
             }
         )
        
    }

    addTodoClicked() {
        this.props.history.push(`/todos/-1`)
    }

    updateTodoClicked(id) {
        console.log('update ' + id)
        this.props.history.push(`/todos/${id}`)
        // /todos/${id}
        // let username = AuthenticationService.getLoggedInUserName()
        // //console.log(id + " " + username);
        // TodoDataService.deleteTodo(username, id)
        //  .then (
        //      response => {
        //         this.setState({message : `Delete of todo ${id} Successful`})
        //         this.refreshTodos()
        //      }
        //  )
        
    }

    render() {
        console.log('render')
        return (
            <div>
                 <h1>List Todos</h1>
                 {this.state.message && <div class="alert alert-success">{this.state.message}</div>}
                 <div className="container">
                    <table className="table">
                        <thead>
                            <tr>
                                <th>Description</th>
                                <th>Target Date</th>
                                <th>IsCompleted?</th>
                                <th>Update</th>
                                <th>Delete</th>
                            </tr>
                        </thead>
                        <tbody>
                        {
                            this.state.todos.map (
                                todo =>
                                    <tr key={todo.id}>
                                        <td>{todo.description}</td>
                                        <td>{todo.done.toString()}</td>
                                        <td>{moment(todo.targetDate).format('YYYY-MM-DD')}</td>
                                        <td><button className="btn btn-success" onClick={() => this.updateTodoClicked(todo.id)}>Update</button></td>
                                        <td><button className="btn btn-warning" onClick={() => this.deleteTodoClicked(todo.id)}>Delete</button></td>
                                    </tr>
                            )
                            }
                        </tbody>
                    </table>
                    <div className="row">
                        <button className="btn btn-success" onClick={this.addTodoClicked}>Add</button>
                    </div>
                 </div>
            </div>
        )
    }
}

export default ListTodosComponent
```
---

### /frontend/todo-app/src/components/todo/HeaderComponent.jsx

```
import React, {Component} from 'react'
import {Link} from 'react-router-dom'
import AuthenticationService from './AuthenticationService.js'


class HeaderComponent extends Component {
    render() {
        const isUserLoggedIn = AuthenticationService.isUserLoggedIn();
        //console.log(isUserLoggedIn);

        return (
            <header>
                <nav className="navbar navbar-expand-md navbar-dark bg-dark">
                    <div><a href="http://www.in28minutes.com" className="navbar-brand">in28Minutes</a></div>
                    <ul className="navbar-nav">
                        {isUserLoggedIn && <li><Link className="nav-link" to="/welcome/in28minutes">Home</Link></li>}
                        {isUserLoggedIn && <li><Link className="nav-link" to="/todos">Todos</Link></li>}
                    </ul>
                    <ul className="navbar-nav navbar-collapse justify-content-end">
                        {!isUserLoggedIn && <li><Link className="nav-link" to="/login">Login</Link></li>}
                        {isUserLoggedIn && <li><Link className="nav-link" to="/logout" onClick={AuthenticationService.logout}>Logout</Link></li>}
                    </ul>
                </nav>
            </header>
        )
    }
}

export default HeaderComponent
```
---

### /frontend/todo-app/src/components/todo/AuthenticatedRoute.jsx

```
import React, {Component} from 'react'
import {Route, Redirect} from 'react-router-dom'
import AuthenticationService from './AuthenticationService.js'

class AuthenticatedRoute extends Component {    
    render() {
        if(AuthenticationService.isUserLoggedIn()) {
            return <Route {...this.props}/>
        } else {
            return <Redirect to="/login"/>
        }

    }
}

export default AuthenticatedRoute
```
---

### /frontend/todo-app/src/components/todo/FooterComponent.jsx

```
import React, {Component} from 'react'

class FooterComponent extends Component {
    render() {
        return (
            <footer className="footer">
                <span className="text-muted">All Rights Reserved 2018 @in28minutes</span>
            </footer>
        )
    }
}

export default FooterComponent
```
---

### /frontend/todo-app/src/components/todo/LoginComponent.jsx

```
import React, {Component} from 'react'
import AuthenticationService from './AuthenticationService.js'

class LoginComponent extends Component {
    
    constructor(props) {
        super(props)
        
        this.state = {
            username: 'in28minutes',
            password: '',
            hasLoginFailed: false,
            showSuccessMessage: false
        }
        // this.handleUsernameChange = this.handleUsernameChange.bind(this)
        // this.handlePasswordChange = this.handlePasswordChange.bind(this)
        this.handleChange = this.handleChange.bind(this)
        this.loginClicked = this.loginClicked.bind(this)
    }

    handleChange(event) {
        //console.log(this.state);
        this.setState(
            {
                [event.target.name]
                  :event.target.value
            }
        )
    }

    // handleUsernameChange(event) {
    //     console.log(event.target.name);
    //     this.setState(
    //         {
    //             [event.target.name]
    //               :event.target.value
    //         }
    //     )
    // }

    // handlePasswordChange(event) {
    //     console.log(event.target.value);
    //     this.setState({password:event.target.value})
    // }

    loginClicked() {
        //in28minutes,dummy
        // if(this.state.username==='in28minutes' && this.state.password==='dummy'){
        //     AuthenticationService.registerSuccessfulLogin(this.state.username,this.state.password)
        //     this.props.history.push(`/welcome/${this.state.username}`)
        //     //this.setState({showSuccessMessage:true})
        //     //this.setState({hasLoginFailed:false})
        // }
        // else {
        //     this.setState({showSuccessMessage:false})
        //     this.setState({hasLoginFailed:true})
        // }

        AuthenticationService
        .executeBasicAuthenticationService(this.state.username, this.state.password)
        .then(() => {
            AuthenticationService.registerSuccessfulLogin(this.state.username,this.state.password)
            this.props.history.push(`/welcome/${this.state.username}`)
        }).catch( () =>{
            this.setState({showSuccessMessage:false})
            this.setState({hasLoginFailed:true})
        })
    }

    render() {
        return (
            <div>
                <h1>Login</h1>
                <div className="container">
                    {/*<ShowInvalidCredentials hasLoginFailed={this.state.hasLoginFailed}/>*/}
                    {this.state.hasLoginFailed && <div className="alert alert-warning">Invalid Credentials</div>}
                    {this.state.showSuccessMessage && <div>Login Sucessful</div>}
                    {/*<ShowLoginSuccessMessage showSuccessMessage={this.state.showSuccessMessage}/>*/}
                    User Name: <input type="text" name="username" value={this.state.username} onChange={this.handleChange}/>
                    Password: <input type="password" name="password" value={this.state.password}  onChange={this.handleChange}/>
                    <button className="btn btn-success" onClick={this.loginClicked}>Login</button>
                </div>
            </div>
        )
    }
}

export default LoginComponent
```
---

### /frontend/todo-app/src/components/todo/TodoComponent.jsx

```
import React, {Component} from 'react'
import moment from 'moment'
import { Formik, Form, Field, ErrorMessage } from 'formik';
import TodoDataService from '../../api/todo/TodoDataService.js'
import AuthenticationService from './AuthenticationService.js'

class TodoComponent extends Component {

    constructor(props) {
        super(props)

        this.state = {
            id : this.props.match.params.id,
            description : '',
            targetDate : moment(new Date()).format('YYYY-MM-DD')
        }

        this.onSubmit = this.onSubmit.bind(this)
        this.validate = this.validate.bind(this)

    }

    componentDidMount() {

        if(this.state.id===-1) {
            return
        }
        
        let username = AuthenticationService.getLoggedInUserName()
        
        TodoDataService.retrieveTodo(username, this.state.id)
             .then(response => this.setState({
                description: response.data.description,
                targetDate: moment(response.data.targetDate).format('YYYY-MM-DD')
             }))
    }

    validate(values) {
        let errors = {}
        if(!values.description) {
            errors.description = 'Enter a Description'
        } else if(values.description.length<5) {
            errors.description = 'Enter atleast 5 Characters in Description'
        }

        if(!moment(values.targetDate).isValid()) {
            errors.targetDate = 'Enter a valid Target Date'
        }

        return errors

    }

    onSubmit(values) {
        let username = AuthenticationService.getLoggedInUserName()

        let todo = {
            id: this.state.id,
            description: values.description,
            targetDate: values.targetDate
        }

        if (this.state.id === -1) {
            TodoDataService.createTodo(username, todo)
                .then(() => this.props.history.push('/todos'))
        } else {
            TodoDataService.updateTodo(username, this.state.id, todo)
                .then(() => this.props.history.push('/todos'))
        }
        
        console.log(values);
    }

    render() {
        
        let {description,targetDate} = this.state
        //let targetDate = this.state.targetDate

        return (
            <div>
                <h1>Todo</h1>
                <div className="container">
                    <Formik 
                        initialValues={{description,targetDate}}
                        onSubmit={this.onSubmit}
                        validateOnChange={false}
                        validateOnBlur={false}
                        validate={this.validate}
                        enableReinitialize={true}
                    >
                        {
                            (props) => (
                                <Form>
                                    <ErrorMessage name="description" component="div" 
                                                                className="alert alert-warning"/>
                                    <ErrorMessage name="targetDate" component="div" 
                                                                className="alert alert-warning"/>
                                    <fieldset className="form-group">
                                        <label>Description</label>
                                        <Field className="form-control" type="text" name="description"/>
                                    </fieldset>
                                    <fieldset className="form-group">
                                        <label>Target Date</label>
                                        <Field className="form-control" type="date" name="targetDate"/>
                                    </fieldset>
                                    <button className="btn btn-success" type="submit">Save</button>
                                </Form>
                            )
                        }
                    </Formik>
                
                </div>                
            </div>
        )
    }
}

export default TodoComponent
```
---

### /frontend/todo-app/src/components/todo/AuthenticationService.js

```js
import axios from 'axios'

class AuthenticationService {

    executeBasicAuthenticationService(username, password) {
        return axios.get('http://localhost:8080/basicauth', 
            {headers: {authorization: this.createBasicAuthToken(username,password)}})
    }

    createBasicAuthToken(username,password) {
        return 'Basic ' +  window.btoa(username + ":" + password)
    }

    registerSuccessfulLogin(username,password){
        //let basicAuthHeader = 'Basic ' +  window.btoa(username + ":" + password)
        //console.log('registerSuccessfulLogin')
        sessionStorage.setItem('authenticatedUser', username)
        this.setupAxiosInterceptors(this.createBasicAuthToken(username,password))
    }

    logout() {
        sessionStorage.removeItem('authenticatedUser');
    }

    isUserLoggedIn() {
        let user = sessionStorage.getItem('authenticatedUser')
        if(user===null) return false
        return true
    }

    getLoggedInUserName() {
        let user = sessionStorage.getItem('authenticatedUser')
        if(user===null) return ''
        return user
    }

    setupAxiosInterceptors(basicAuthHeader) {

        axios.interceptors.request.use(
            (config) => {
                if(this.isUserLoggedIn()) {
                    config.headers.authorization = basicAuthHeader
                }
                return config
            }
        )
    }
}

export default new AuthenticationService()
```
---

### /frontend/todo-app/src/components/todo/ErrorComponent.jsx

```
import React from 'react'

function ErrorComponent() {
    return <div>An Error Occurred. I don't know what to do! Contact support at abcd-efgh-ijkl</div>
}

export default ErrorComponent
```
---

### /frontend/todo-app/src/components/todo/TodoApp.jsx

```
import React, {Component} from 'react'
import {BrowserRouter as Router, Route, Switch} from 'react-router-dom'
import AuthenticatedRoute from './AuthenticatedRoute.jsx'
import LoginComponent from './LoginComponent.jsx'
import ListTodosComponent from './ListTodosComponent.jsx'
import ErrorComponent from './ErrorComponent.jsx'
import HeaderComponent from './HeaderComponent.jsx'
import FooterComponent from './FooterComponent.jsx'
import LogoutComponent from './LogoutComponent.jsx'
import WelcomeComponent from './WelcomeComponent.jsx'
import TodoComponent from './TodoComponent.jsx'

class TodoApp extends Component {
    render() {
        return (
            <div className="TodoApp">
                <Router>
                    <>
                        <HeaderComponent/>
                        <Switch>
                            <Route path="/" exact component={LoginComponent}/>
                            <Route path="/login" component={LoginComponent}/>
                            <AuthenticatedRoute path="/welcome/:name" component={WelcomeComponent}/>
                            <AuthenticatedRoute path="/todos/:id" component={TodoComponent}/>
                            <AuthenticatedRoute path="/todos" component={ListTodosComponent}/>
                            <AuthenticatedRoute path="/logout" component={LogoutComponent}/>
                            
                            <Route component={ErrorComponent}/>
                        </Switch>
                        <FooterComponent/>
                    </>
                </Router>
                {/*<LoginComponent/>
                <WelcomeComponent/>*/}
            </div>
        )
    }
}

export default TodoApp
```
---

### /frontend/todo-app/src/components/todo/LogoutComponent.jsx

```
import React, {Component} from 'react'

class LogoutComponent extends Component {
    render() {
        return (
            <>
                <h1>You are logged out</h1>
                <div className="container">
                    Thank You for Using Our Application.
                </div>
            </>
        )
    }
}

export default LogoutComponent
```
---

### /frontend/todo-app/src/components/learning-examples/FirstComponent.jsx

```
import React, { Component } from 'react'

//Class Component
class FirstComponent extends Component {
    render() {
      return (
        <div className="firstComponent">
           FirstComponent
        </div>
      )
    }
  }

export default FirstComponent
```
---

### /frontend/todo-app/src/components/learning-examples/ThirdComponent.jsx

```
import React from 'react'

function ThirdComponent() {
  return (
    <div className="thirdComponent">
       Third Component
    </div>
  )
}

export default ThirdComponent
```
---

### /frontend/todo-app/src/components/learning-examples/SecondComponent.jsx

```
import React, { Component } from 'react'

class SecondComponent extends Component {
    render() {
      return (
        <div className="secondComponent">
           Second Component
        </div>
      )
    }
}

export default SecondComponent
```
---

### /frontend/todo-app/src/components/counter/Counter.css

```css
/*
button {
    background-color: green;
    font-size : 16px;
    padding : 15px 30px;
    color : white;
    width : 100px;
}

.count {
    font-size : 50px;
    padding : 15px 30px;
}

.reset {
    background-color: red;
    width : 200px;
}

body {
    padding : 15px 30px;
}
*/
```
---

### /frontend/todo-app/src/components/counter/Counter.jsx

```
import React, {Component} from 'react'
import PropTypes from 'prop-types'
import './Counter.css'

class Counter extends Component {

    constructor() {

        super(); //Error 1
  
        this.state = {
            counter : 0
        }

        this.increment = this.increment.bind(this);
        this.decrement = this.decrement.bind(this);
        this.reset = this.reset.bind(this);
    }
  
    render() {
        return (
          <div className="counter">
             <CounterButton by={1} incrementMethod={this.increment} decrementMethod={this.decrement}/>
             <CounterButton by={5} incrementMethod={this.increment}  decrementMethod={this.decrement}/>
             <CounterButton by={10} incrementMethod={this.increment}  decrementMethod={this.decrement}/>
             <span className="count">{this.state.counter}</span> 
             <div><button className="reset" onClick={this.reset}>Reset</button></div>
          </div>
        );
    }    

    reset() {
        this.setState( {counter: 0});
    }

    increment(by) { 
        //console.log(`increment from child - ${by}`)
        this.setState(
            (prevState) => {
             return {counter: prevState.counter + by}
            }
        );
    }

    decrement(by) { 
        //console.log(`increment from child - ${by}`)
        this.setState(
            (prevState) => {
             return {counter: prevState.counter - by}
            }
        );
    }

}

class CounterButton extends Component {
  //Define the initial state in a constructor
  //state => counter 0
  //constructor() {
  //    super(); //Error 1

    //   this.state = {
    //       counter : 0
    //   }

    //   this.increment = this.increment.bind(this);
    //   this.decrement = this.decrement.bind(this);
  //}
  
  render()  {
  //render = () =>  {
    //const style = {fontSize : "50px", padding : "15px 30px"};
    return (
        <div className="counter">
            <button onClick={() => this.props.incrementMethod(this.props.by)} >+{this.props.by}</button>
            <button onClick={() => this.props.decrementMethod(this.props.by)} >-{this.props.by}</button>
            {/*<span className="count" 
            >{this.state.counter}</span>*/}
        </div>
    )
  }
  
//   increment() { //Update state - counter++
//     //console.log('increment');
//     //this.state.counter++; //Bad Practice
//     this.setState({
//         counter: this.state.counter + this.props.by
//     });
    
//     this.props.incrementMethod(this.props.by);
//   }

//   decrement () {
//     this.setState({
//         counter: this.state.counter - this.props.by
//     });
    
//     this.props.decrementMethod(this.props.by);
//   }
}

CounterButton.defaultProps = {
    by : 1
}

CounterButton.propTypes = {
    by : PropTypes.number
}

export default Counter
```
---

### /frontend/todo-app/src/App.test.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});
```
---

### /frontend/todo-app/src/api/todo/TodoDataService.js

```js
import axios from 'axios'

class TodoDataService {
    retrieveAllTodos(name) {
        //console.log('executed service')
        return axios.get(`http://localhost:8080/users/${name}/todos`);
    }

    retrieveTodo(name, id) {
        //console.log('executed service')
        return axios.get(`http://localhost:8080/users/${name}/todos/${id}`);
    }

    deleteTodo(name, id) {
        //console.log('executed service')
        return axios.delete(`http://localhost:8080/users/${name}/todos/${id}`);
    }

    updateTodo(name, id, todo) {
        //console.log('executed service')
        return axios.put(`http://localhost:8080/users/${name}/todos/${id}`, todo);
    }

    createTodo(name, todo) {
        //console.log('executed service')
        return axios.post(`http://localhost:8080/users/${name}/todos/`, todo);
    }

}

export default new TodoDataService()
```
---

### /frontend/todo-app/src/api/todo/HelloWorldService.js

```js
import axios from 'axios'

class HelloWorldService {
    
    executeHelloWorldService() {
        //console.log('executed service')
        return axios.get('http://localhost:8080/hello-world');        
    }

    executeHelloWorldBeanService() {
        //console.log('executed service')
        return axios.get('http://localhost:8080/hello-world-bean');        
    }

    executeHelloWorldPathVariableService(name) {
        //console.log('executed service')
        // let username = 'in28minutes'
        // let password = 'dummy'

        // let basicAuthHeader = 'Basic ' +  window.btoa(username + ":" + password)

        return axios.get(`http://localhost:8080/hello-world/path-variable/${name}`
        // , 
        //     {
        //         headers : {
        //             authorization: basicAuthHeader
        //         }
        //     }
        );        
    }

}

export default new HelloWorldService()
```
---

### /frontend/todo-app/src/serviceWorker.js

```js
// This optional code is used to register a service worker.
// register() is not called by default.

// This lets the app load faster on subsequent visits in production, and gives
// it offline capabilities. However, it also means that developers (and users)
// will only see deployed updates on subsequent visits to a page, after all the
// existing tabs open on the page have been closed, since previously cached
// resources are updated in the background.

// To learn more about the benefits of this model and instructions on how to
// opt-in, read https://bit.ly/CRA-PWA

const isLocalhost = Boolean(
  window.location.hostname === 'localhost' ||
    // [::1] is the IPv6 localhost address.
    window.location.hostname === '[::1]' ||
    // 127.0.0.1/8 is considered localhost for IPv4.
    window.location.hostname.match(
      /^127(?:\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)){3}$/
    )
);

export function register(config) {
  if (process.env.NODE_ENV === 'production' && 'serviceWorker' in navigator) {
    // The URL constructor is available in all browsers that support SW.
    const publicUrl = new URL(process.env.PUBLIC_URL, window.location.href);
    if (publicUrl.origin !== window.location.origin) {
      // Our service worker won't work if PUBLIC_URL is on a different origin
      // from what our page is served on. This might happen if a CDN is used to
      // serve assets; see https://github.com/facebook/create-react-app/issues/2374
      return;
    }

    window.addEventListener('load', () => {
      const swUrl = `${process.env.PUBLIC_URL}/service-worker.js`;

      if (isLocalhost) {
        // This is running on localhost. Let's check if a service worker still exists or not.
        checkValidServiceWorker(swUrl, config);

        // Add some additional logging to localhost, pointing developers to the
        // service worker/PWA documentation.
        navigator.serviceWorker.ready.then(() => {
          console.log(
            'This web app is being served cache-first by a service ' +
              'worker. To learn more, visit https://bit.ly/CRA-PWA'
          );
        });
      } else {
        // Is not localhost. Just register service worker
        registerValidSW(swUrl, config);
      }
    });
  }
}

function registerValidSW(swUrl, config) {
  navigator.serviceWorker
    .register(swUrl)
    .then(registration => {
      registration.onupdatefound = () => {
        const installingWorker = registration.installing;
        if (installingWorker == null) {
          return;
        }
        installingWorker.onstatechange = () => {
          if (installingWorker.state === 'installed') {
            if (navigator.serviceWorker.controller) {
              // At this point, the updated precached content has been fetched,
              // but the previous service worker will still serve the older
              // content until all client tabs are closed.
              console.log(
                'New content is available and will be used when all ' +
                  'tabs for this page are closed. See https://bit.ly/CRA-PWA.'
              );

              // Execute callback
              if (config && config.onUpdate) {
                config.onUpdate(registration);
              }
            } else {
              // At this point, everything has been precached.
              // It's the perfect time to display a
              // "Content is cached for offline use." message.
              console.log('Content is cached for offline use.');

              // Execute callback
              if (config && config.onSuccess) {
                config.onSuccess(registration);
              }
            }
          }
        };
      };
    })
    .catch(error => {
      console.error('Error during service worker registration:', error);
    });
}

function checkValidServiceWorker(swUrl, config) {
  // Check if the service worker can be found. If it can't reload the page.
  fetch(swUrl)
    .then(response => {
      // Ensure service worker exists, and that we really are getting a JS file.
      const contentType = response.headers.get('content-type');
      if (
        response.status === 404 ||
        (contentType != null && contentType.indexOf('javascript') === -1)
      ) {
        // No service worker found. Probably a different app. Reload the page.
        navigator.serviceWorker.ready.then(registration => {
          registration.unregister().then(() => {
            window.location.reload();
          });
        });
      } else {
        // Service worker found. Proceed as normal.
        registerValidSW(swUrl, config);
      }
    })
    .catch(() => {
      console.log(
        'No internet connection found. App is running in offline mode.'
      );
    });
}

export function unregister() {
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.ready.then(registration => {
      registration.unregister();
    });
  }
}
```
---

### /frontend/todo-app/src/logo.svg

```
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 841.9 595.3">
    <g fill="#61DAFB">
        <path d="M666.3 296.5c0-32.5-40.7-63.3-103.1-82.4 14.4-63.6 8-114.2-20.2-130.4-6.5-3.8-14.1-5.6-22.4-5.6v22.3c4.6 0 8.3.9 11.4 2.6 13.6 7.8 19.5 37.5 14.9 75.7-1.1 9.4-2.9 19.3-5.1 29.4-19.6-4.8-41-8.5-63.5-10.9-13.5-18.5-27.5-35.3-41.6-50 32.6-30.3 63.2-46.9 84-46.9V78c-27.5 0-63.5 19.6-99.9 53.6-36.4-33.8-72.4-53.2-99.9-53.2v22.3c20.7 0 51.4 16.5 84 46.6-14 14.7-28 31.4-41.3 49.9-22.6 2.4-44 6.1-63.6 11-2.3-10-4-19.7-5.2-29-4.7-38.2 1.1-67.9 14.6-75.8 3-1.8 6.9-2.6 11.5-2.6V78.5c-8.4 0-16 1.8-22.6 5.6-28.1 16.2-34.4 66.7-19.9 130.1-62.2 19.2-102.7 49.9-102.7 82.3 0 32.5 40.7 63.3 103.1 82.4-14.4 63.6-8 114.2 20.2 130.4 6.5 3.8 14.1 5.6 22.5 5.6 27.5 0 63.5-19.6 99.9-53.6 36.4 33.8 72.4 53.2 99.9 53.2 8.4 0 16-1.8 22.6-5.6 28.1-16.2 34.4-66.7 19.9-130.1 62-19.1 102.5-49.9 102.5-82.3zm-130.2-66.7c-3.7 12.9-8.3 26.2-13.5 39.5-4.1-8-8.4-16-13.1-24-4.6-8-9.5-15.8-14.4-23.4 14.2 2.1 27.9 4.7 41 7.9zm-45.8 106.5c-7.8 13.5-15.8 26.3-24.1 38.2-14.9 1.3-30 2-45.2 2-15.1 0-30.2-.7-45-1.9-8.3-11.9-16.4-24.6-24.2-38-7.6-13.1-14.5-26.4-20.8-39.8 6.2-13.4 13.2-26.8 20.7-39.9 7.8-13.5 15.8-26.3 24.1-38.2 14.9-1.3 30-2 45.2-2 15.1 0 30.2.7 45 1.9 8.3 11.9 16.4 24.6 24.2 38 7.6 13.1 14.5 26.4 20.8 39.8-6.3 13.4-13.2 26.8-20.7 39.9zm32.3-13c5.4 13.4 10 26.8 13.8 39.8-13.1 3.2-26.9 5.9-41.2 8 4.9-7.7 9.8-15.6 14.4-23.7 4.6-8 8.9-16.1 13-24.1zM421.2 430c-9.3-9.6-18.6-20.3-27.8-32 9 .4 18.2.7 27.5.7 9.4 0 18.7-.2 27.8-.7-9 11.7-18.3 22.4-27.5 32zm-74.4-58.9c-14.2-2.1-27.9-4.7-41-7.9 3.7-12.9 8.3-26.2 13.5-39.5 4.1 8 8.4 16 13.1 24 4.7 8 9.5 15.8 14.4 23.4zM420.7 163c9.3 9.6 18.6 20.3 27.8 32-9-.4-18.2-.7-27.5-.7-9.4 0-18.7.2-27.8.7 9-11.7 18.3-22.4 27.5-32zm-74 58.9c-4.9 7.7-9.8 15.6-14.4 23.7-4.6 8-8.9 16-13 24-5.4-13.4-10-26.8-13.8-39.8 13.1-3.1 26.9-5.8 41.2-7.9zm-90.5 125.2c-35.4-15.1-58.3-34.9-58.3-50.6 0-15.7 22.9-35.6 58.3-50.6 8.6-3.7 18-7 27.7-10.1 5.7 19.6 13.2 40 22.5 60.9-9.2 20.8-16.6 41.1-22.2 60.6-9.9-3.1-19.3-6.5-28-10.2zM310 490c-13.6-7.8-19.5-37.5-14.9-75.7 1.1-9.4 2.9-19.3 5.1-29.4 19.6 4.8 41 8.5 63.5 10.9 13.5 18.5 27.5 35.3 41.6 50-32.6 30.3-63.2 46.9-84 46.9-4.5-.1-8.3-1-11.3-2.7zm237.2-76.2c4.7 38.2-1.1 67.9-14.6 75.8-3 1.8-6.9 2.6-11.5 2.6-20.7 0-51.4-16.5-84-46.6 14-14.7 28-31.4 41.3-49.9 22.6-2.4 44-6.1 63.6-11 2.3 10.1 4.1 19.8 5.2 29.1zm38.5-66.7c-8.6 3.7-18 7-27.7 10.1-5.7-19.6-13.2-40-22.5-60.9 9.2-20.8 16.6-41.1 22.2-60.6 9.9 3.1 19.3 6.5 28.1 10.2 35.4 15.1 58.3 34.9 58.3 50.6-.1 15.7-23 35.6-58.4 50.6zM320.8 78.4z"/>
        <circle cx="420.9" cy="296.5" r="45.7"/>
        <path d="M520.5 78.1z"/>
    </g>
</svg>
```
---

### /frontend/todo-app/src/App.js

```js
import React, { Component } from 'react';
//import FirstComponent from './components/learning-examples/FirstComponent'
//import SecondComponent from './components/learning-examples/SecondComponent'
//import ThirdComponent from './components/learning-examples/ThirdComponent'
//import Counter from './components/counter/Counter'
import TodoApp from './components/todo/TodoApp'
import './App.css';
import './bootstrap.css';

class App extends Component {
  render() {
    return (
      <div className="App">
         {/*<Counter/>*/}
         <TodoApp/>
      </div>
    );
  }
}

// class LearningComponents extends Component {
//   render() {
//     return (
//       <div className="LearningComponents">
//          My Hello World
//          <FirstComponent></FirstComponent>
//          <SecondComponent></SecondComponent>
//          <ThirdComponent></ThirdComponent>
//       </div>
//     );
//   }
// }

export default App;
```
---

### /frontend/todo-app/package.json

```json
{
  "name": "todo-app",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "axios": "^0.18.0",
    "formik": "^1.5.1",
    "moment": "^2.24.0",
    "react": "^16.8.4",
    "react-dom": "^16.8.4",
    "react-router-dom": "^4.3.1",
    "react-scripts": "2.1.8"
  },
  "scripts": {
    "start": "PORT=4200 react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": [
    ">0.2%",
    "not dead",
    "not ie <= 11",
    "not op_mini all"
  ]
}
```
---

### /diff.txt

```
diff --git a/frontend/todo-app/src/api/todo/HelloWorldService.js b/frontend/todo-app/src/api/todo/HelloWorldService.js
index 48b4f31..bbfe00a 100644
--- a/frontend/todo-app/src/api/todo/HelloWorldService.js
+++ b/frontend/todo-app/src/api/todo/HelloWorldService.js
@@ -1,20 +1,32 @@
-import axios from 'axios'
+import axiosAuthorized from './AxiosAuthorized'
+
+// import axios from 'axios'
+
+// axios.interceptors.request.use(
+//     config => {
+//       config.headers.authorization =  'Basic ' + window.btoa('in28minutes' + ':' + 'dummy');
+//       return config;
+//     }
+//   )
 
 class HelloWorldService {
     
     executeHelloWorldService() {
         //console.log('executed service')
-        return axios.get('http://localhost:8080/hello-world');        
+        return axiosAuthorized.get('http://localhost:8080/hello-world');        
     }
 
     executeHelloWorldBeanService() {
         //console.log('executed service')
-        return axios.get('http://localhost:8080/hello-world-bean');        
+        return axiosAuthorized.get('http://localhost:8080/hello-world-bean');        
     }
 
     executeHelloWorldPathVariableService(name) {
         //console.log('executed service')
-        return axios.get(`http://localhost:8080/hello-world/path-variable/${name}`);        
+        let basicAuthHeaderString = 'Basic ' + window.btoa('in28minutes' + ':' + 'dummy');
+        return axiosAuthorized.get(`http://localhost:8080/hello-world/path-variable/${name}`
+        //,{ headers: { authorization: basicAuthHeaderString } }
+        );        
     }
 }
 
diff --git a/frontend/todo-app/src/api/todo/TodoDataService.js b/frontend/todo-app/src/api/todo/TodoDataService.js
index 74b6739..f036186 100644
--- a/frontend/todo-app/src/api/todo/TodoDataService.js
+++ b/frontend/todo-app/src/api/todo/TodoDataService.js
@@ -1,6 +1,7 @@
 import axios from 'axios'
 
 class TodoDataService {
+
     retrieveAllTodos(name) {
         //console.log('executed service')
         return axios.get(`http://localhost:8080/users/${name}/todos`);
diff --git a/frontend/todo-app/src/components/todo/AuthenticationService.js b/frontend/todo-app/src/components/todo/AuthenticationService.js
index 55bb4ff..a8487b9 100644
--- a/frontend/todo-app/src/components/todo/AuthenticationService.js
+++ b/frontend/todo-app/src/components/todo/AuthenticationService.js
@@ -1,8 +1,17 @@
+import axios from 'axios'
+
 class AuthenticationService {
 
+    executeBasicAuthenticationService(username, password) {
+        let basicAuthHeaderString = 'Basic ' + window.btoa(username + ':' + password);
+        return axios.get(`http://localhost:8080/basicauth`, { headers: { authorization: basicAuthHeaderString } });
+    }
+    
     registerSuccessfulLogin(username,password){
         console.log('registerSuccessfulLogin')
         sessionStorage.setItem('authenticatedUser', username);
+        sessionStorage.setItem('token', 'Basic ' + window.btoa(username + ':' + password));
+        this.setupAxiosInterceptors();
     }
 
     logout() {
@@ -20,6 +29,21 @@ class AuthenticationService {
         if(user===null) return ''
         return user
     }
+
+    getAuthenticationToken() {
+        let user = sessionStorage.getItem('token')
+        if(user===null) return ''
+        return user
+    }
+
+    setupAxiosInterceptors() {
+        axios.interceptors.request.use(config => {
+            if (this.isUserLoggedIn()) {
+                config.headers.authorization = this.getAuthenticationToken();
+            }
+            return config;
+        })
+    }
 }
 
 export default new AuthenticationService()
\ No newline at end of file
diff --git a/frontend/todo-app/src/components/todo/LoginComponent.jsx b/frontend/todo-app/src/components/todo/LoginComponent.jsx
index 1352032..9b3d571 100644
--- a/frontend/todo-app/src/components/todo/LoginComponent.jsx
+++ b/frontend/todo-app/src/components/todo/LoginComponent.jsx
@@ -45,16 +45,36 @@ class LoginComponent extends Component {
 
     loginClicked() {
         //in28minutes,dummy
-        if(this.state.username==='in28minutes' && this.state.password==='dummy'){
-            AuthenticationService.registerSuccessfulLogin(this.state.username,this.state.password)
-            this.props.history.push(`/welcome/${this.state.username}`)
-            //this.setState({showSuccessMessage:true})
-            //this.setState({hasLoginFailed:false})
-        }
-        else {
-            this.setState({showSuccessMessage:false})
-            this.setState({hasLoginFailed:true})
-        }
+        
+        // if(this.state.username==='in28minutes' && this.state.password==='dummy'){
+        //     AuthenticationService.registerSuccessfulLogin(this.state.username,this.state.password)
+        //     this.props.history.push(`/welcome/${this.state.username}`)
+        //     //this.setState({showSuccessMessage:true})
+        //     //this.setState({hasLoginFailed:false})
+        // }
+        // else {
+        //     this.setState({showSuccessMessage:false})
+        //     this.setState({hasLoginFailed:true})
+        // }
+
+        AuthenticationService.executeBasicAuthenticationService(this.state.username, this.state.password)
+         .then(
+             () => {
+                AuthenticationService.registerSuccessfulLogin(this.state.username,this.state.password)
+                this.props.history.push(`/welcome/${this.state.username}`)
+                //this.setState({showSuccessMessage:true})
+                //this.setState({hasLoginFailed:false})
+    
+             }
+         )
+         .catch(
+            () => {
+                this.setState({showSuccessMessage:false})
+                this.setState({hasLoginFailed:true})                     
+            }
+
+         )
+
     }
 
     render() {
diff --git a/frontend/todo-app/src/components/todo/WelcomeComponent.jsx b/frontend/todo-app/src/components/todo/WelcomeComponent.jsx
index 8a38b78..d595982 100644
--- a/frontend/todo-app/src/components/todo/WelcomeComponent.jsx
+++ b/frontend/todo-app/src/components/todo/WelcomeComponent.jsx
@@ -52,10 +52,15 @@ class WelcomeComponent extends Component {
     }
 
     handleError(error) {
-        console.log(error.response)
-        this.setState({welcomeMessage: error.response.data.message})
+        let welcomeMessage = ""
+        console.log(error)
+        if(error.message) 
+            welcomeMessage = welcomeMessage + error.message
+        if(error && error.response && error.response.data) {
+            welcomeMessage = welcomeMessage + error.response.data.message;
+        }
+        this.setState({welcomeMessage})
     }
-
 }
 
 
diff --git a/restful-web-services/pom.xml b/restful-web-services/pom.xml
index f928221..98f8d55 100644
--- a/restful-web-services/pom.xml
+++ b/restful-web-services/pom.xml
@@ -38,6 +38,11 @@
 
 		<dependency>
 			<groupId>org.springframework.boot</groupId>
+			<artifactId>spring-boot-starter-security</artifactId>
+		</dependency>
+
+		<dependency>
+			<groupId>org.springframework.boot</groupId>
 			<artifactId>spring-boot-devtools</artifactId>
 			<scope>runtime</scope>
 		</dependency>
diff --git a/restful-web-services/src/main/resources/application.properties b/restful-web-services/src/main/resources/application.properties
index 5cbf315..435c2ac 100644
--- a/restful-web-services/src/main/resources/application.properties
+++ b/restful-web-services/src/main/resources/application.properties
@@ -1 +1,4 @@
-logging.level.org.springframework = info
\ No newline at end of file
+logging.level.org.springframework = info
+
+spring.security.user.name=in28minutes
+spring.security.user.password=dummy
\ No newline at end of file


package com.in28minutes.rest.webservices.restfulwebservices.basic.auth;
public class AuthenticationBean {

    private String message;

    public AuthenticationBean(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    @Override
    public String toString() {
        return String.format("HelloWorldBean [message=%s]", message);
    }

}

package com.in28minutes.rest.webservices.restfulwebservices.basic.auth;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

//Controller
@CrossOrigin(origins="http://localhost:4200")
@RestController
public class BasicAuthenticationController {

    @GetMapping(path = "/basicauth")
    public AuthenticationBean helloWorldBean() {
        //throw new RuntimeException("Some Error has Happened! Contact Support at ***-***");
        return new AuthenticationBean("You are authenticated");
    }   
}

package com.in28minutes.rest.webservices.restfulwebservices.basic.auth;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SpringSecurityConfigurationBasicAuth extends WebSecurityConfigurerAdapter{
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
        .csrf().disable()   
        .authorizeRequests()
        .antMatchers(HttpMethod.OPTIONS,"/**").permitAll()
                .anyRequest().authenticated()
                .and()
            //.formLogin().and()
            .httpBasic();
    }
}

```
---



### /restful-web-services/src/test/java/com/in28minutes/rest/webservices/restfulwebservices/RestfulWebServicesApplicationTests.java

```java
package com.in28minutes.rest.webservices.restfulwebservices;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class RestfulWebServicesApplicationTests {

	@Test
	public void contextLoads() {
	}

}
```
---

### /restful-web-services/src/main/resources/application.properties

```properties
logging.level.org.springframework = info

spring.security.user.name=in28minutes
spring.security.user.password=dummy
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/helloworld/HelloWorldController.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.helloworld;

import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

//Controller
@RestController
@CrossOrigin(origins="http://localhost:4200")
//
public class HelloWorldController {

	@GetMapping(path = "/hello-world")
	public String helloWorld() {
		return "Hello World";
	}

	@GetMapping(path = "/hello-world-bean")
	public HelloWorldBean helloWorldBean() {
		return new HelloWorldBean("Hello World");
	}
	
	///hello-world/path-variable/in28minutes
	@GetMapping(path = "/hello-world/path-variable/{name}")
	public HelloWorldBean helloWorldPathVariable(@PathVariable String name) {
		//throw new RuntimeException("Something went wrong");
		return new HelloWorldBean(String.format("Hello World, %s", name));
	}
}
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/helloworld/HelloWorldBean.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.helloworld;

public class HelloWorldBean {

	private String message;

	public HelloWorldBean(String message) {
		this.message = message;
	}

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}

	@Override
	public String toString() {
		return String.format("HelloWorldBean [message=%s]", message);
	}

}
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/basic/auth/AuthenticationBean.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.basic.auth;
public class AuthenticationBean {

    private String message;

    public AuthenticationBean(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    @Override
    public String toString() {
        return String.format("HelloWorldBean [message=%s]", message);
    }

}

```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/basic/auth/BasicAuthenticationController.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.basic.auth;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

//Controller
@CrossOrigin(origins="http://localhost:4200")
@RestController
public class BasicAuthenticationController {

    @GetMapping(path = "/basicauth")
    public AuthenticationBean helloWorldBean() {
        //throw new RuntimeException("Some Error has Happened! Contact Support at ***-***");
        return new AuthenticationBean("You are authenticated");
    }   
}
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/basic/auth/SpringSecurityConfigurationBasicAuth.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.basic.auth;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SpringSecurityConfigurationBasicAuth extends WebSecurityConfigurerAdapter{
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
        .csrf().disable()   
        .authorizeRequests()
        .antMatchers(HttpMethod.OPTIONS,"/**").permitAll()
                .anyRequest().authenticated()
                .and()
            //.formLogin().and()
            .httpBasic();
    }
}
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/todo/TodoHardcodedService.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.todo;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import org.springframework.stereotype.Service;

@Service
public class TodoHardcodedService {

	private static List<Todo> todos = new ArrayList<>();
	private static int idCounter = 0;

	static {
		todos.add(new Todo(++idCounter, "in28minutes", "Learn to Dance 2", new Date(), false));
		todos.add(new Todo(++idCounter, "in28minutes", "Learn about Microservices 2", new Date(), false));
		todos.add(new Todo(++idCounter, "in28minutes", "Learn about Angular", new Date(), false));
	}

	public List<Todo> findAll() {
		return todos;
	}
	
	public Todo save(Todo todo) {
		if(todo.getId()==-1 || todo.getId()==0) {
			todo.setId(++idCounter);
			todos.add(todo);
		} else {
			deleteById(todo.getId());
			todos.add(todo);
		}
		return todo;
	}

	public Todo deleteById(long id) {
		Todo todo = findById(id);

		if (todo == null)
			return null;

		if (todos.remove(todo)) {
			return todo;
		}

		return null;
	}

	public Todo findById(long id) {
		for (Todo todo : todos) {
			if (todo.getId() == id) {
				return todo;
			}
		}

		return null;
	}
}
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/todo/TodoResource.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.todo;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

@CrossOrigin(origins = "http://localhost:4200")
@RestController
public class TodoResource {

	@Autowired
	private TodoHardcodedService todoService;

	@GetMapping("/users/{username}/todos")
	public List<Todo> getAllTodos(@PathVariable String username) {
		// Thread.sleep(3000);
		return todoService.findAll();
	}
	
	@GetMapping("/users/{username}/todos/{id}")
	public Todo getTodo(@PathVariable String username, @PathVariable long id) {
		// Thread.sleep(3000);
		return todoService.findById(id);
	}


	// DELETE /users/{username}/todos/{id}
	@DeleteMapping("/users/{username}/todos/{id}")
	public ResponseEntity<Void> deleteTodo(@PathVariable String username, @PathVariable long id) {

		Todo todo = todoService.deleteById(id);

		if (todo != null) {
			return ResponseEntity.noContent().build();
		}

		return ResponseEntity.notFound().build();
	}
	
	//Edit/Update a Todo
	//PUT /users/{user_name}/todos/{todo_id}
	@PutMapping("/users/{username}/todos/{id}")
	public ResponseEntity<Todo> updateTodo(
			@PathVariable String username,
			@PathVariable long id, @RequestBody Todo todo){
		
		Todo todoUpdated = todoService.save(todo);
		
		return new ResponseEntity<Todo>(todo, HttpStatus.OK);
	}
	
	@PostMapping("/users/{username}/todos")
	public ResponseEntity<Void> updateTodo(
			@PathVariable String username, @RequestBody Todo todo){
		
		Todo createdTodo = todoService.save(todo);
		
		//Location
		//Get current resource url
		///{id}
		URI uri = ServletUriComponentsBuilder.fromCurrentRequest()
				.path("/{id}").buildAndExpand(createdTodo.getId()).toUri();
		
		return ResponseEntity.created(uri).build();
	}
		
}
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/todo/Todo.java

```java
package com.in28minutes.rest.webservices.restfulwebservices.todo;

import java.util.Date;

public class Todo {
	private long id;
	private String username;
	private String description;
	private Date targetDate;
	private boolean isDone;
	
	public Todo() {
		
	}

	public Todo(long id, String username, String description, Date targetDate, boolean isDone) {
		super();
		this.id = id;
		this.username = username;
		this.description = description;
		this.targetDate = targetDate;
		this.isDone = isDone;
	}

	public long getId() {
		return id;
	}

	public void setId(long id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public Date getTargetDate() {
		return targetDate;
	}

	public void setTargetDate(Date targetDate) {
		this.targetDate = targetDate;
	}

	public boolean isDone() {
		return isDone;
	}

	public void setDone(boolean isDone) {
		this.isDone = isDone;
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + (int) (id ^ (id >>> 32));
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Todo other = (Todo) obj;
		if (id != other.id)
			return false;
		return true;
	}

	
}
```
---

### /restful-web-services/src/main/java/com/in28minutes/rest/webservices/restfulwebservices/RestfulWebServicesApplication.java

```java
package com.in28minutes.rest.webservices.restfulwebservices;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class RestfulWebServicesApplication {

	public static void main(String[] args) {
		SpringApplication.run(RestfulWebServicesApplication.class, args);
	}
}
```
---
