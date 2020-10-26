## Store  
> `redux`  
> `redux-devtool-extension`  
> `next-redux-wrapper`  

1. { createStore } (`redux`)  
2. { createWrapper } (`next-redux-wrapper`)  
3. { compse, applyMiddleware} (`redux`)  
4. { composeWithDevTools } (`redux-devtool-extension`)  
5. wrap _app.js

`configureStore.js`
```javascript
import { createWrapper } from 'next-redux-wrapper';
import { applyMiddleware, compose, createStore } from 'redux';
import rootReducer from '../reducers';
import { composeWithDevTools} from 'redux-devtools-extension';

const configureStroe = () => {
    const middlewares = [];
    const enhancer = (process.env.NODE_ENV === 'production')
        ? compose(applyMiddleware(...middlewares))
        : composeWithDevTools(applyMiddleware(...middlewares))

    const store = createStore(rootReducer, enhancer);
    return store;
}

const wrapper = createWrapper(configureStroe, {
    debug: process.env.NODE_ENV === 'development',
});

export default wrapper;
```
`_app.js`
```javascript
...
import wrapper from "../store/configureStore";


class MyApp extends App { ... }


export default wrapper.withRedux(MyApp);
```

## Reducer

> `redux`  
> `next-redux-wrapper`

1. { combineReducers } (`redux`)
2. { HYDRATE } (`next-redux-wrapper`)

`reducers/index.js`
```javascript
import { combineReducers } from 'redux';
import { HYDRATE } from 'next-redux-wrapper'; // redux ssr위해

import user from './user';
import post from './post';

const initialState = {
    user: {

    },
    post: {

    }
}

const rootReducer = combineReducers({
    index: (state = {}, action) => {
        switch (action.type){
            case 'HYDRATE' :
                console.log("HYDRATE", HYDRATE)
                return {
                ...state,
                ...action.payload
                }       
            default: 
                return state;
        }        
    },
    user,
    post,
})
export default rootReducer;
```

## Action Creator
`/reducers/user.js`
```javascript
const isLoggin = (data) => {
    return {
        type: 'LOGGIN',
        data,
    }
}
```

## State
> `react-redux`

1. { useSelector } (`react-redux`)  
2. { useDispatch } (`react-redux`)

```javascript
import {loginAction} from '../reducers/user';
...
 // state 가져오기
const {isLogginIn} = useselector((state) => state.user);

 // dispatch해서 state 업데이트 하기
const dispatch = useDispatch();
const onSubmitForm = useCallback(() => {
    dispatch(loginRequestAction({email, password}));        
}, [email, password]);
```