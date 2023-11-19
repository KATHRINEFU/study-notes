# TypeScript

## 介绍

TypeScript 在运行前需要先编译为 JavaScript，而在编译阶段就会进行类型检查，所以 **TypeScript 是静态类型**

大部分 JavaScript 代码都只需要经过少量的修改（或者完全不用修改）就变成 TypeScript 代码，这得益于 TypeScript 强大的[类型推论][]，即使不去手动声明变量 `foo` 的类型，也能在变量初始化时自动推论出它是一个 `number` 类型。

什么是 TypeScript？

- TypeScript 是添加了类型系统的 JavaScript，适用于任何规模的项目。
- TypeScript 是一门静态类型、弱类型的语言。
- TypeScript 是完全兼容 JavaScript 的，它不会修改 JavaScript 运行时的特性。
- TypeScript 可以编译为 JavaScript，然后运行在浏览器、Node.js 等任何能运行 JavaScript 的环境中。
- TypeScript 拥有很多编译选项，类型检查的严格程度由你决定。
- TypeScript 可以和 JavaScript 共存，这意味着 JavaScript 项目能够渐进式的迁移到 TypeScript。
- TypeScript 增强了编辑器（IDE）的功能，提供了代码补全、接口提示、跳转到定义、代码重构等能力。
- TypeScript 拥有活跃的社区，大多数常用的第三方库都提供了类型声明。
- TypeScript 与标准同步发展，符合最新的 ECMAScript 标准（stage 3）

**TypeScript 编译的时候即使报错了，还是会生成编译结果**，我们仍然可以使用这个编译之后的文件。

如果要在报错的时候终止 js 文件的生成，可以在 `tsconfig.json` 中配置 `noEmitOnError` 即可。

## 基础

### 原始数据类型

1. boolean

```ts
let isDone: boolean = false;
```

事实上 `new Boolean()` 返回的是一个 `Boolean` 对象：

```ts
let createdByNewBoolean: Boolean = new Boolean(1);
```

直接调用 `Boolean` 也可以返回一个 `boolean` 类型：

```ts
let createdByBoolean: boolean = Boolean(1);
```

2. number

   ```typescript
   let decLiteral: number = 6;
   let hexLiteral: number = 0xf00d;
   // ES6 中的二进制表示法
   let binaryLiteral: number = 0b1010;
   // ES6 中的八进制表示法
   let octalLiteral: number = 0o744;
   let notANumber: number = NaN;
   let infinityNumber: number = Infinity;
   ```

3. 空值

   JavaScript 没有空值（Void）的概念，在 TypeScript 中，可以用 `void` 表示没有任何返回值的函数：

   ```ts
   function alertName(): void {
       alert('My name is Tom');
   }
   ```

   声明一个 `void` 类型的变量没有什么用，因为你只能将它赋值为 `undefined` 和 `null`（只在 --strictNullChecks 未指定时）：

   ```ts
   let unusable: void = undefined;
   ```

4. Null and Undefined

   在 TypeScript 中，可以使用 `null` 和 `undefined` 来定义这两个原始数据类型：

   ```ts
   let u: undefined = undefined;
   let n: null = null;
   ```

   与 `void` 的区别是，`undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量：

### 任意值

但如果是 `any` 类型，则允许被赋值为任意类型。

```ts
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```

在任意值上访问任何属性都是允许的，也允许调用任何方法， **声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**。**变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型**：

### 联合类型

联合类型使用 `|` 分隔每个类型。

这里的 `let myFavoriteNumber: string | number` 的含义是，允许 `myFavoriteNumber` 的类型是 `string` 或者 `number`，但是不能是其他类型。

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**：

### 接口

TypeScript 中的接口是一个非常灵活的概念，除了可用于[对类的一部分行为进行抽象](https://ts.xcatliu.com/advanced/class-and-interfaces.html#类实现接口)以外，也常用于对「对象的形状（Shape）」进行描述。

```ts
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```

接口一般首字母大写。[有的编程语言中会建议接口的名称加上 `I` 前缀](https://msdn.microsoft.com/en-us/library/8bc1fexb(v=vs.71).aspx)。

**赋值的时候，变量的形状必须和接口的形状保持一致**。

有时我们希望不要完全匹配一个形状，那么可以用可选属性：

```ts
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom'
};
```

有时候我们希望一个接口允许有任意的属性

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

一个接口中只能定义一个任意属性。如果接口中有多个类型的属性，则可以在任意属性中使用联合类型：

有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 `readonly` 定义只读属性：

```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}
```

**只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候**：

### 数组

最简单的方法是使用「类型 + 方括号」来表示数组：

```ts
let fibonacci: number[] = [1, 1, 2, 3, 5];
```

我们也可以使用数组泛型（Array Generic） `Array<elemType>` 来表示数组：

```ts
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

接口也可以用来描述数组：

```ts
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

`NumberArray` 表示：只要索引的类型是数字时，那么值的类型必须是数字。

虽然接口也可以用来描述数组，但是我们一般不会这么做，因为这种方式比前两种方式复杂多了。

不过有一种情况例外，那就是它常用来表示类数组。

# Redux

https://www.freecodecamp.org/news/what-is-redux-store-actions-reducers-explained/

Being a state management library, Redux will basically store and manage all the application's states

State is **Read-only** in Redux. What makes Redux predictable is that to make a change in the state of the application, we need to dispatch an action which describes what changes we want to make in the state. These actions are then consumed by something known as reducers, whose sole job is to accept two things (the action and the current state of the application) and return a new updated instance of the state.

reducers do not change any part of the state. Rather a reducer produces a new instance of the state with all the necessary updates.

Given an initial state, with a specific list of **actions** in a specific order, it'll always provide us with the exact same final state of the entity. 

## Concepts

### Provider

We use Provider in order to pass **the store** as an attribute. By passing the store as an attribute in the Provider component, we are avoiding having to store **the store** as props.

```
import { Provider } from "react-redux"

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider >,
  document.getElementById('root')
)
```

1. Import Provider component from the 'react-redux' node package.
2. Using ReactDOM.render() we pass in two (2) arguments:
   - Provider wrapping our top-level component 'App'.
   - the HTML element in which we are rendering the first argument.
3. The Provider component wraps our top-level component, so as to say "every child component of your App component will have access to the store".
4. We pass our "store" constant to a store attribute. (The creation of our store constant is not pictured)..

### Store

The Redux store is the main, central bucket which stores all the states of an application. It should be considered and maintained as a **single source of truth** for the state of the application.

If the `store` is provided to the **App.js** (by wrapping the `App` component within the `<Provider>` `</Provider>` tag) as shown in the code snippet below, then all its children (children components of `App.js`) can also access the state of the application from the store. This makes it act as a global state.

A store is a JavaScript object with a few special functions and abilities that make it different than a plain global object:

- You must never directly modify or change the state that is kept inside the Redux store
- Instead, the only way to cause an update to the state is to create a plain **action** object that describes "something that happened in the application", and then **dispatch** the action to the store to tell it what happened.
- When an action is dispatched, the store runs the root **reducer** function, and lets it calculate the new state based on the old state and the action
- Finally, the store notifies **subscribers** that the state has been updated so the UI can be updated with the new data.

we can create a **store** instance by calling the Redux library `createStore` API.

const store = Redux.createStore(counterReducer), `createStore` can also accept a `preloadedState` value as its second argument. You could use this to add initial data when the store is created

store.getState(): get the latest state

`store.subscribe()` and pass a subscriber callback function that will be called every time the store is updated. So, we can pass our `render` function as the subscriber, and know that each time the store updates, we can update the UI with the latest value.

store.dispatch(action): the store runs the reducer, calculates the updated state, and runs the subscribers to update the UI.

The store has several responsibilities:

- Holds the current application state inside
- Allows access to the current state via [`store.getState()`](https://redux.js.org/api/store#getState);
- Allows state to be updated via [`store.dispatch(action)`](https://redux.js.org/api/store#dispatch);
- Registers listener callbacks via [`store.subscribe(listener)`](https://redux.js.org/api/store#subscribe);
- Handles unregistering of listeners via the `unsubscribe` function returned by [`store.subscribe(listener)`](https://redux.js.org/api/store#subscribe).

### Actions

The only way to change the state is to emit an action, which is an object describing what happened

we dispatch something known as an **action creator** – that is, the function `addItemToCart()`. This in turn returns an `action` which is a plain JS object describing the purpose of the action denoted by the `type` key along with any other data required for the state change. In this case, it's the name of the book to be added to the cart denoted by the `payload` key.

**Every action must have at least** a `type` associated with it.

We usually write that type string like `"domain/eventName"`, where the first part is the feature or category that this action belongs to, and the second part is the specific thing that happened.

### Reducers

Reducers, as the name suggests, take in two things: **previous state** and **an action**. Then they reduce it (read it return) to one entity: the **new updated instance of state**.

Whenever an action is dispatched, **all the reducers are activated**. Each reducer filters out the action using a switch statement switching on the **action type**. 

every reducer should handle the `default` case

**You can think of a reducer as an event listener which handles events based on the received action (event) type.**

- They should only calculate the new state value based on the `state` and `action` arguments
- They are not allowed to modify the existing `state`. Instead, they must make *immutable updates*, by copying the existing `state` and making changes to the copied values.
- They must not do any asynchronous logic, calculate random values, or cause other "side effects"

### dispatch

The Redux store has a method called `dispatch`. **The only way to update the state is to call `store.dispatch()` and pass in an action object**. 

### selectors

**Selectors** are functions that know how to extract specific pieces of information from a store state value

### configureStore()

```
import { configureStore } from '@reduxjs/toolkit'

export default configureStore({
  reducer: {} //add reducers here
})
```

The **configureStore** here **replaces** the original **createStore** from Redux. Unlike *createStore*, *configureStore* from Redux Toolkit not only creates a store but can also accept reducer functions as arguments and automatically sets up the Redux DevTools Extension for easy debugging.

## Design

### Design state values

**your UI should be based on your state**. So, one approach to designing an application is to first think of all the state needed to describe how the application works. 

### design state structure

With Redux, **our application state is always kept in plain JavaScript objects and arrays**. That means you may not put other things into the Redux state - no class instances, built-in JS types like `Map` / `Set` / `Promise` / `Date`, functions, or anything else that is not plain JS data.

```javascript
const todoAppState = {
  todos: [
    { id: 0, text: 'Learn React', completed: true },
    { id: 1, text: 'Learn Redux', completed: false, color: 'purple' },
    { id: 2, text: 'Build something fun!', completed: false, color: 'blue' }
  ],
  filters: {
    status: 'Active',
    colors: ['red', 'blue']
  }
}
```

**it's okay to have other state values outside of Redux**

### design actions

### write reducers

1. create root reducer: **A Redux app really only has one reducer function: the "root reducer" function** that you will pass to `createStore` later on. 
2. handling actions
3. splitting reducers

**We recommend organizing your Redux app folders and files based on "features"** - code that relates to a specific concept or area of your application. **The Redux code for a particular feature is usually written as a single file, known as a "slice" file**, which contains all the reducer logic and all of the action-related code for that part of your app state.

**reducer composition**, and it's the fundamental pattern of building Redux apps： **his file only has to update the todos-related state - it's not nested any more!** 

4. combining reducers

   import { combineReducers } from 'redux'


## Toolkit functions

### createSlice

createSlice is a higher order function that accepts an initial state, an object full of reducer functions and a slice name. It automatically generates action creators and action types that correspond to the reducers and state.

#### Step 1: Implement createSilce method and export actions and reducer.

```javascript
import { createSlice } from '@reduxjs/toolkit';
const locationSlice = createSlice({
    name: "location",
    initialState: {
        location: ['Bangalore', 'Hyderabad', 'Delhi'],
    },
    reducers: {
        save: (state, param) => {
            const { payload } = param;
            state.location = [...state.location, payload];
        },
    }
});
const { actions, reducer } = locationSlice
export const { save } = actions;
export default reducer;
```

#### Step 2: Dispatch action by making use of react hooks in functional component.

useSelector hook will provide us the redux-store object and can be de-structured to get the state values.

useDispatch hook has to be invoked to set up the dispatcher.

```react
import { useDispatch, useSelector } from "react-redux";
import { save } from "./locationSlice";

const { location } = useSelector(state=>state)
const dispatch = useDispatch();
```

### TypedUseSelector

typescript override

```react
import {useSelector, TypedUseSelectorHook}  from "react-redux";
import {RootState} from "../state";

export const useTypedSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### Query

RTK Query

- [`createApi()`](https://redux-toolkit.js.org/rtk-query/api/createApi): The core of RTK Query's functionality. It allows you to define a set of endpoints describe how to retrieve data from a series of endpoints, including configuration of how to fetch and transform that data. In most cases, you should use this once per app, with "one API slice per base URL" as a rule of thumb.
- [`fetchBaseQuery()`](https://redux-toolkit.js.org/rtk-query/api/fetchBaseQuery): A small wrapper around [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) that aims to simplify requests. Intended as the recommended `baseQuery` to be used in `createApi` for the majority of users.
- [``](https://redux-toolkit.js.org/rtk-query/api/ApiProvider): Can be used as a `Provider` if you **do not already have a Redux store**.
- [`setupListeners()`](https://redux-toolkit.js.org/rtk-query/api/setupListeners): A utility used to enable `refetchOnMount` and `refetchOnReconnect` behaviors.

### Steps

#### create api slice

```react
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react'
import type { Pokemon } from './types'

// Define a service using a base URL and expected endpoints
export const pokemonApi = createApi({
  reducerPath: 'pokemonApi',
  baseQuery: fetchBaseQuery({ baseUrl: 'https://pokeapi.co/api/v2/' }),
  endpoints: (builder) => ({
    getPokemonByName: builder.query<Pokemon, string>({
      query: (name) => `pokemon/${name}`,
    }),
  }),
})

// Export hooks for usage in functional components, which are
// auto-generated based on the defined endpoints
export const { useGetPokemonByNameQuery } = pokemonApi
```

#### configure the store

The "API slice" also contains an auto-generated Redux slice reducer and a custom middleware that manages subscription lifetimes. Both of those need to be added to the Redux store:

```react
import { configureStore } from '@reduxjs/toolkit'
// Or from '@reduxjs/toolkit/query/react'
import { setupListeners } from '@reduxjs/toolkit/query'
import { pokemonApi } from './services/pokemon'

export const store = configureStore({
  reducer: {
    // Add the generated reducer as a specific top-level slice
    [pokemonApi.reducerPath]: pokemonApi.reducer,
  },
  // Adding the api middleware enables caching, invalidation, polling,
  // and other useful features of `rtk-query`.
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(pokemonApi.middleware),
})

// optional, but required for refetchOnFocus/refetchOnReconnect behaviors
// see `setupListeners` docs - takes an optional callback as the 2nd arg for customization
setupListeners(store.dispatch)
```

#### use hooks in components

 import the auto-generated React hooks from the API slice into your component file, and call the hooks in your component with any needed parameters. RTK Query will automatically fetch data on mount, re-fetch when parameters change, provide `{data, isFetching}` values in the result, and re-render the component as those values change:

```react
import * as React from 'react'
import { useGetPokemonByNameQuery } from './services/pokemon'

export default function App() {
  // Using a query hook automatically fetches data and returns query values
  const { data, error, isLoading } = useGetPokemonByNameQuery('bulbasaur')
  // Individual hooks are also accessible under the generated endpoints:
  // const { data, error, isLoading } = pokemonApi.endpoints.getPokemonByName.useQuery('bulbasaur')
  
  // render UI based on data and loading state
}
```

### createApi

```react
const api = createApi({
  baseQuery: fetchBaseQuery({ baseUrl: '/' }),
  endpoints: (build) => ({
    // ...
  }),
})

type Api = {
  // Redux integration
  reducerPath: string
  reducer: Reducer
  middleware: Middleware

  // Endpoint interactions
  endpoints: Record<string, EndpointDefinition>

  // Code splitting and generation
  injectEndpoints: (options: InjectEndpointsOptions) => UpdatedApi
  enhanceEndpoints: (options: EnhanceEndpointsOptions) => UpdatedApi

  // Utilities
  utils: {
    updateQueryData: UpdateQueryDataThunk
    patchQueryData: PatchQueryDataThunk
    prefetch: PrefetchThunk
    invalidateTags: ActionCreatorWithPayload<
      Array<TagTypes | FullTagDescription<TagTypes>>,
      string
    >
    selectInvalidatedBy: (
      state: FullState,
      tags: Array<TagTypes | FullTagDescription<TagTypes>>
    ) => Array<{
      endpointName: string
      originalArgs: any
      queryCacheKey: string
    }>
    resetApiState: ActionCreator<ResetAction>
    getRunningQueryThunk(
      endpointName: EndpointName,
      args: QueryArg
    ): ThunkWithReturnValue<QueryActionCreatorResult | undefined>
    getRunningMutationThunk(
      endpointName: EndpointName,
      fixedCacheKeyOrRequestId: string
    ): ThunkWithReturnValue<MutationActionCreatorResult | undefined>
    getRunningQueriesThunk(): ThunkWithReturnValue<
      Array<QueryActionCreatorResult<any>>
    >
    getRunningMutationsThunk(): ThunkWithReturnValue<
      Array<MutationActionCreatorResult<any>>
    >
  }

  // Internal actions
  internalActions: InternalActions

  // React hooks (if applicable)
  [key in GeneratedReactHooks]: GeneratedReactHooks[key]
}
```



### fetchBaseQuery

`fetchBaseQuery` is a factory function that generates a data fetching method compatible with RTK Query's `baseQuery` configuration option. It takes all standard options from fetch's [`RequestInit`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch) interface, as well as `baseUrl`, a `prepareHeaders` function, an optional `fetch` function, a `paramsSerializer` function, and a `timeout`.

Sending Authorization Bearer Token Header: A Bearer Token is a cryptic string typically generated by the server in response to a login request.

```react
import { fetchBaseQuery } from '@reduxjs/toolkit/query'
import type { RootState } from './store'

const baseQuery = fetchBaseQuery({
  baseUrl: '/',
  prepareHeaders: (headers, { getState }) => {
    const token = (getState() as RootState).auth.token

    // If we have a token set in state, let's assume that we should be passing it.
    if (token) {
      headers.set('authorization', `Bearer ${token}`)
    }

    return headers
  },
})
```



### get Query

```react
getUserProfile: builder.query<User, { id?: string }>({
            query: ({ id }) => ({
                url: `/user/${id}`,
                method: HTTP.GET,
            }),
            transformResponse: (response: any) => response.data,
            providesTags: ['User']
        }),
```



### update query

```react
updateUserProfile: builder.mutation<IUserPreferences[], { id?: string, body: any}>({
            query: ({ id, body }) => ({
                url: `/user/profile/${id}`,
                method: HTTP.PUT,
                body
            }),
            transformResponse: (response: any) => response.data,
            invalidatesTags: ['User']
        }),
```



# React

## Hooks

Hooks are JavaScript functions that manage the state's behaviour and side effects by isolating them from a component.

React provides a bunch of standard in-built hooks:

- `useState`: To manage states. Returns a stateful value and an updater function to update it.
- `useEffect`: To manage side-effects like API calls, subscriptions, timers, mutations, and more.
- `useContext`: To return the current value for a context.
- `useReducer`: A `useState` alternative to help with complex state management.
- `useCallback`: It returns a memorized version of a callback to help a child component not re-render unnecessarily.
- `useMemo`: It returns a memoized value that helps in performance optimizations.
- `useRef`: It returns a ref object with a `.current` property. The ref object is mutable. It is mainly used to access a child component imperatively.
- `useLayoutEffect`: It fires at the end of all DOM mutations. It's best to use `useEffect` as much as possible over this one as the `useLayoutEffect` fires synchronously.
- `useDebugValue`: Helps to display a label in React DevTools for custom hooks.

### useLocation

The `useLocation` **hook** returns the location object from the current URL, which includes the following:

- `pathname`: This is the path of the URL.
- `search`: This is the query string `(?)` included in the URL.
- `hash`: This is the result of the hash fragment `(#)` from the URL.

For example, if I have a URL, http://localhost:3000/products/school/?name=bags, the result from the `useLocation` object will be the following:

{pathname: ‘/products/school/’, search: ‘?bags’, hash: ‘’, 

state: undefined}hash: “”pathname: “/products/school/”

search: “?bags”state: undefined

Please note that the `useLocation` object will update each time when the URL changes. Hence, it is very useful in the cases when we want to trigger a function based on the change of our URL. 

### useNavigate

```react
import { useNavigate } from "react-router-dom";

const navigate = useNavigate();

const submitHandler = async (event) => {
  event.preventDefault();

  try {
    await submitForm();
    navigate("/success"); // Omit optional second argument
  } catch (error) {
    navigate("/error", { state: { message: "Failed to submit form" } }); // Pass optional second argument
  }
};
```

The optional second argument looks like the object below. You can pass data as the value of the `state` property. If you set the value of `replace` to `true`, React router will replace the current entry in the history stack instead of adding a new one. Both `state` and `replace` fields are optional.

```tsx
{
  state: { message: "Failed to submit form" },
  replace: false,
}
```

After navigation, the data you passed using `navigate` is accessible inside the matching route's rendered component using the `useLocation` hook.

```tsx
const location = useLocation();
console.log(location.state) 
```

### useSelector

useSelector is a function that takes the current state as an argument and  return any value as a result, including directly returning a value that was nested inside `state`, or deriving new values. 

When an action is dispatched, `useSelector()` will do a reference comparison of the previous selector result value and the current result value. If they are different, the component will be forced to re-render. If they are the same, the component will not re-render. `useSelector()` uses strict `===` reference equality checks by default, not shallow equality

The selector is approximately equivalent to the [`mapStateToProps` argument to `connect`](https://react-redux.js.org/using-react-redux/connect-mapstate) conceptually.

You may call `useSelector()` multiple times within a single function component. Each call to `useSelector()` creates an individual subscription to the Redux store. dispatched action that causes multiple `useSelector()`s in the same component to return new values *should* only result in a single re-render.

### useDispatch

dispatch any action to the store by simply adding an action as an argument to the new variable.

```react
import React from 'react'
import { useDispatch } from 'react-redux'

export const CounterComponent = ({ value }) => {
  const dispatch = useDispatch()

  return (
    <div>
      <span>{value}</span>
      <button onClick={() => dispatch({ type: 'increment-counter' })}>
        Increment counter
      </button>
    </div>
  )
}
```

### useParams

The `useParams` hook returns an object of key/value pairs of the dynamic params from the current URL that were matched by the `<Route path>`. Child routes inherit all params from their parent routes.

```react
// Get the userId param from the URL.
let { userId } = useParams();
```

## Helmet

SEO-friendly content is **the type of content that's created in a way that helps the search engines rank it high**

The two components we'll be importing from **react-helmet-async** are called **Helmet** and **HelmetProvider**.

1. **HelmetProvider** will wrap the entire app component in order to create context and prevent memory leaks. Therefore, this component will only need to be imported in the root **App** component.
2. **Helmet** will be imported into any page component where you want to implement meta tags. Think of **<Helmet>** as the **<head>** tag for the page in question.

```jsp
<Helmet>
<title>Learning React Helmet!</title>
<meta name='description' content='Beginner friendly page for learning React Helmet.' />
</Helmet>
```

# antd

## Modal

When requiring users to interact with the application, but without jumping to a new page and interrupting the user's workflow, you can use `Modal` to create a new floating layer over the current page to get user feedback or display information.

https://ant.design/components/modal

## Input

- addonAfter: The label text displayed after 
