# redux-thunk-it
Beautify the format of stores, actions, reducers in react-redux with redux-thunk

## Installation
Install with npm:
```javascript
npm install redux-thunk-it
```

## API
```es6
import thunk, { combineReducers, thunkActions } from 'react-thunk-it'
```

1. **combineReducers**: see [Example](#example) below
2. **thunkActions**: see [Example](#example) below
3. **thunk**: [Redux Thunk](https://github.com/gaearon/redux-thunk)
4. **thunk.withExtraArgument**: [Injecting a Custom Argument](https://github.com/gaearon/redux-thunk#injecting-a-custom-argument) MUST using {} as parameter
5. **dispatch**
    - type: 
      1. `${store_name}/${reducer_name}` to dispatch to a specific reducer in a specific store
      2. `/${reducer_name}` to dispatch to a specific reducer in every store
    - payload: anything
```
// dispatch to a specific reducer
dispatch({
  type: 'test/save',
  payload: {
    ...
  }
})

// dispatch to a action in this store
dispatch(this.xxx())

// dispatch to a action in other store
import { Test2 } from './'
dispatch(Test2.xxx())
```

## Example

1. ./index.js
```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore, applyMiddleware, combineReducers } from 'redux'
import { Provider } from 'react-redux'
import thunk from 'redux-thunk-it'

import App from './App'
import stores from './stores'

const store = createStore(
  combineReducers(stores),
  applyMiddleware(thunk),
)

ReactDOM.render((
  <Provider store={store}>
    <App />
  </Provider>
), document.getElementById('root'))
```

2. ./stores/index.js
```es6
import { combineReducers, thunkActions } from 'redux-thunk-it'

import test1 from './test1'
import test2 from './test2'

export default combineReducers({
  test1,
  test2
})

export const Test1 = thunkActions(test1)
export const Test2 = thunkActions(test2)
```

3. ./stores/test1.js *&* ./stores/test2.js
```es6
export default {
  state: {
    data: {},
    loading: false,
    err: ''
  },

  actions: {
    get_something: function (message = '') {
      const { dispatch } = this.props
      
      // const { custom arguments injected by withExtraArgument...  } = this

      dispatch({
        type: 'test/save',
        payload: {
          loading: true
        }
      })

      fetch('http://localhost/something')
      .then(res => res.json())
      .then(data => {
        dispatch({
          type: 'test/save',
          payload: {
            data,
            loading: false
          }
        })
      })
      .catch(err => {
        dispatch({
          type: 'test/save',
          payload: {
            loading: false
            err,
          }
        })
      })

      
    }
  },

  reducers: {
    save: (state, payload) => {
      return {
        ...state,
        ...payload
      }
    }
  }
}
```

4. ./App.js
```jsx
import { connect } from 'react-redux'
import { Test1, Test2 } from './stores'

class App extends Component {
  componentDidMount() {
    const { dispatch } = this.props

    dispatch(Test1.get_something('xxx'))
  }
  render() {
    const { test1, test2 } = this.props

    console.log(test1, test2)

    return (
      <div />
    );
  }
}

export default connect(state => state)(App)
```
