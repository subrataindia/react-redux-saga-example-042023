# react-redux-saga-example-042023

Redux Saga is a middleware library that provides an easy and effective way to handle side-effects like asynchronous data fetching, and is often used in React applications. In this example, we'll demonstrate how to use Redux Saga in a simple React app that fetches and displays data from an API.

We'll assume that you have already installed Redux and React-Redux in your project. If not, you can install them by running the following command:

```js
npm install redux react-redux redux-saga immer
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

- For React 18

```js
import React from "react";
import { Provider } from "react-redux";
import App from "./App";
import store from "./store";
import { createRoot } from "react-dom/client";

const rootElement = document.getElementById("root");
const root = createRoot(rootElement);

root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

Here, we import `Provider` from `react-redux` and wrap the `App` component with it, passing in the `store` as a prop.

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

Here, we define three action types `FETCH_DATA_REQUEST`, `FETCH_DATA_SUCCESS`, and `FETCH_DATA_FAILURE`, and three action creators `fetchDataRequest`, `fetchDataSuccess`, and `fetchDataFailure`. These actions will be dispatched by our Saga to fetch data from the API.

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

or

```js
import produce from "immer";
import {
  FETCH_DATA_REQUEST,
  FETCH_DATA_SUCCESS,
  FETCH_DATA_FAILURE
} from "./actions";

const initialState = {
  data: [],
  loading: false,
  error: null
};

export default function rootReducer(state = initialState, action) {
  switch (action.type) {
    case FETCH_DATA_REQUEST:
      return produce(state, (draftState) => {
        draftState.loading = true;
        draftState.error = null;
      });
    case FETCH_DATA_SUCCESS:
      return produce(state, (draftState) => {
        draftState.loading = false;
        draftState.data = action.payload;
      });
    case FETCH_DATA_FAILURE:
      return produce(state, (draftState) => {
        draftState.loading = false;
        draftState.error = action.payload;
      });
    default:
      return state;
  }
}
```

Here, we import the `produce` function from the `immer` library and the FETCH_DATA_REQUEST, FETCH_DATA_SUCCESS, and FETCH_DATA_FAILURE actions from ./actions.

We define an initial state object with three properties: users, loading, and error.

We then define the rootReducer function, which takes the state and action arguments and returns a new state object based on the action type.

For the FETCH_DATA_REQUEST action, we use produce to update the loading property to true and reset the error property to null.

For the FETCH_DATA_SUCCESS action, we use produce to update the loading property to false and replace the users property with the payload property of the action.

For the FETCH_DATA_FAILURE action, we use produce to update the loading property to false and set the error property to the payload property of the action.

For any other action type, we simply return the current state.

The produce function from the immer library allows us to write our reducer logic in a mutable way, and automatically produces a new immutable state object that we can return. This makes it easier to write clean, readable code that is less error-prone than manually managing immutability.

## Step 5: sagas.js

```js
import { put, call, takeLatest } from 'redux-saga/effects';
import { fetchDataSuccess, fetchDataFailure, FETCH_DATA_REQUEST } from './actions';

function* fetchData() {
  try {
    const response = yield call(fetch, 'https://jsonplaceholder.typicode.com/users');
    const data = yield response.json();
    yield put(fetchDataSuccess(data));
  } catch (error) {
    yield put(fetchDataFailure(error));
  }
}

export default function* rootSaga() {
  yield takeLatest(FETCH_DATA_REQUEST, fetchData);
}
```

Here, we import put, call, and takeLatest from redux-saga/effects, and import the fetchDataSuccess, fetchDataFailure, and FETCH_DATA_REQUEST actions from ./actions.

We define a generator function fetchData that uses call to fetch data from the API, and then dispatches either fetchDataSuccess or fetchDataFailure actions using put, depending on whether the API call succeeds or fails.

We then define the rootSaga generator function that listens for FETCH_DATA_REQUEST actions using takeLatest, and runs the fetchData function whenever it receives one.

That's it! With these files in place, your React app should now be able to fetch and display data from the API using Redux Saga.


## Step 6: App.js

```js
import React from "react";
import { useDispatch } from "react-redux";
import { fetchDataRequest } from "./actions";
import UserList from "./UserList";

function App() {
  const dispatch = useDispatch();

  React.useEffect(() => {
    dispatch(fetchDataRequest());
  }, [dispatch]);

  return (
    <div className="App">
      <UserList />
    </div>
  );
}

export default App;
```

Here, we import useDispatch from react-redux and the fetchDataRequest action from ./actions.

We use useEffect to dispatch the fetchDataRequest action when the component mounts, and then render the UserList component.

Next, we'll create the UserList.js file:

## Step 7: UserList.js

```js
import React from "react";
import { useSelector } from "react-redux";

function UserList() {
  const users = useSelector((state) => state.data);

  return (
    <div>
      <h1>User List</h1>
      <ul>
        {users.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default UserList;
```

Here, we import useSelector from react-redux and select the users array from the Redux store.

We then render the list of users in an unordered list using the map method.

That's it! With these files in place, your React app should now be able to fetch and display the list of users from the API using Redux Saga.

## Extra clerifications

### Difference between takeEvery and takeLatest

- takeEvery allows concurrent actions to be handled. For example, the user clicks on a Load User button 2 consecutive times at a rapid rate, the 2nd click will dispatch a USER_REQUESTED action while the fetchUser fired on the first one hasn't yet terminated.
takeEvery doesn't handle out of order responses from tasks. There is no guarantee that the tasks will terminate in the same order they were started. To handle out of order responses, you may consider takeLatest.

- takeLatest instead start a new fetchUser task on each dispatched USER_REQUESTED action. Since takeLatest cancels any pending task started previously, we ensure that if a user triggers multiple consecutive USER_REQUESTED actions rapidly, we'll only conclude with the latest action

### What is the difference between put and call ?

Both call() and put() are effect creator functions. call() function is used to create effect description, which instructs middleware to call the promise. put() function creates an effect, which instructs middleware to dispatch an action to the store.
