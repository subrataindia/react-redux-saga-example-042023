# react-redux-saga-example-042023

Redux Saga is a middleware library that provides an easy and effective way to handle side-effects like asynchronous data fetching, and is often used in React applications. In this example, we'll demonstrate how to use Redux Saga in a simple React app that fetches and displays data from an API.

We'll assume that you have already installed Redux and React-Redux in your project. If not, you can install them by running the following command:

```js
npm install redux react-redux
```

Let's get started by creating the following files in your project:


- src/store.js: The Redux store configuration file.
- src/index.js: The main entry point of the React app.
- src/actions.js: The Redux actions file.
- src/reducers.js: The Redux reducers file.
- src/sagas.js: The Redux Saga file.
- src/App.js: The main app file
- src/UserList.js: The component file

## Step 1: Create the Redux Store

```js
import { createStore, applyMiddleware } from 'redux';
import createSagaMiddleware from 'redux-saga';
import rootReducer from './reducers';
import rootSaga from './sagas';

const sagaMiddleware = createSagaMiddleware();

const store = createStore(
  rootReducer,
  applyMiddleware(sagaMiddleware)
);

sagaMiddleware.run(rootSaga);

export default store;
```

Here, we import `createSagaMiddleware` from `redux-saga` and use it to create the `sagaMiddleware`. We then pass the `sagaMiddleware` to `applyMiddleware` when creating the store.

We also import the `rootSaga` function from `./sagas` and run it using `sagaMiddleware.run(rootSaga)`.


## Step 2: index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import App from './App';
import store from './store';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

## Step 3: actions.js

- This file contains all the redux actions

```js
export const FETCH_DATA_REQUEST = 'FETCH_DATA_REQUEST';
export const FETCH_DATA_SUCCESS = 'FETCH_DATA_SUCCESS';
export const FETCH_DATA_FAILURE = 'FETCH_DATA_FAILURE';

export const fetchDataRequest = () => ({
  type: FETCH_DATA_REQUEST,
});

export const fetchDataSuccess = (data) => ({
  type: FETCH_DATA_SUCCESS,
  payload: data,
});

export const fetchDataFailure = (error) => ({
  type: FETCH_DATA_FAILURE,
  payload: error,
});
```

## Step 4:  reducers.js

```js
import { FETCH_DATA_REQUEST, FETCH_DATA_SUCCESS, FETCH_DATA_FAILURE } from './actions';

const initialState = {
  data: [],
  loading: false,
  error: null,
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case FETCH_DATA_REQUEST:
      return {
        ...state,
        loading: true,
      };
    case FETCH_DATA_SUCCESS:
      return {
        ...state,
        loading: false,
        data: action.payload,
      };
    case FETCH_DATA_FAILURE:
      return {
        ...state,
        loading: false,
        error: action.payload,
      };
    default:
      return state;
  }
};

export default reducer;
```

