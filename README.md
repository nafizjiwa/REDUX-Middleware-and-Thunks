# REACT-REDUX-Middleware-and-Thunks

## Intro 
- Redux Middleware performs asynchronous actions (API calls) to update the state
- Asynchronous tasks take time to complete
#### Redux tools to write asynchronous logic
- Middleware and thunks in REDUX
- Promise lifecycle in REDUX
- Redux toolkit allows asynchronous logic 
## Middleware in Redux
- Helps with Redux side effects:
   - Middleware runs after an action is dispatched and before the action is passed to the reducer
   - Redux actions are dispatched to the store, processed by reducers to provide a new state
   - Components that reference the new state are then upate or re-rendered the new state.
- Common Tasks Middlewares Perform
  - logging, caching, routing, asynchronous requests for data, adding auth tokens to request headers.
- To make asynchronous requests Redux Toolkit uses:

        1. createAsychThunk(asynchronous requests)
                 - uses middleware and thunks
        2. createSlice's extraReducers property
  
## Write Your Own Middleware
- To __*ADD*__ middleware:

     import { applyMiddleware, createStore } from 'redux {functions}'
- __*INPUT*__ in sliceReducers createStore:

      sliceReducer = createstore( reducer, '', applyMiddleWare(PASSED IN MIDDLEWARE ) )
                              *refers to initial state as empty state value
      sliceReducer = createstore( reducer, '', applyMiddleWare(PASSED IN MIDDLEWARE ) )
                              *refers to no value so if default value is in reducer that value
                                    would be used as initial state value
- __*DIPSATCH ACTIONS*__ Actions dispatched will be passed thru the middleware pipeline:

      actions move: middleware ---> to middleware ---> before app reducer
#### MIDDLEWARE STRUCTURE [IS A PIPELINE of NESTED functions ] 

                  *** MIDDLEWARE FUNCTION STRUCTURE: ***
      const exampleMiddleware = storeAPI => next =>
        action => {
                 // Body Performs the TaskS
        return next(action);       
      }      // This function call to the next middleware with the current action
            // Passes the action on to the pipeline's next middleware
- Middlewares have access to: `storeAPI` Which give them access to:

        `dispatch`, `getState`, `next` middleware, and dispatched `action`
- Actions are passed down the pipeline untill the last middleware
- Last middleware function call --> next(action) --> results in dispatching to the store.

        next(action) ---> becomes storeAPI.dispatch [a call to stores contents]
#### MIDDLEWARE IS PASSED INTO createStore with 3 arguments:

      const store = createStore( arg1=reducer, arg2=initialValueOfTheStore, arg3=applyMiddleware(createMiddleware))
      const store = createStore(ReducerName, '', applyMiddleware(middlewareFunction));
-Dispatching actions to store:   

      store.dispatch({type: 'stringValue', payload:})
- First taken in by reducer
##### To dispatch an action after store created

      store.dispatch({'The action to dispatch can be a `message`'})
      store.dispatch(action={type:"some_value", action:"some_value"})
## Intro to Thunks
- Thunks allow asynchronous requests in REDUX.
- Thunks are higher-order functions which wraps the code we want to compute later

         const add = (x,y) => {      -->ADD FUNCTION RETURNS THUNK
           return () => {            -->THUNK
             return x + y;            --> FUNCTION RETURNED BY THUNK
           } 
         }

## Promise Lifecycle Actions
- A Promise lifecycle :
   - begins as __*pending*__ and moves to either __*fulfilled*__ or __*rejected STATES*__.
- A Good user experience requires advising the user of the __*STATE*__ of the async requests at any moment
- For asynchronous operations:

        A DISPATCHED ACTION is:
           a "Pending" action before the operation --> the result operation is "FulFilled" or "Rejected"

## createAsyncThunk( )
- Redux Tookit provides a function to help with asynchronous tasks in a slice

         const ThunkActionCreator = createAsyncThunk( 'actionType string' , PayloadCreator)

- __*createAsyncThunk( param1, param2 )*__ --> returns __thunk Action Creator__

        import { createAsyncThunk } from '@reduxjs/toolkit';
        
        const actionName = createAsyncThunk( 'SliceName/ActionName', //Action Type string
              async (arg,thunkAPI) => {                  //Async Thunk function
                              // Body of the payload creator
                               // returns a promise })

- Pass an asynchronous thunk function into createAsyncThunk( ) function returns an action creator that dispatches the action types/actions --> pending/fulfilled/rejected.

## Passing Arguments to Thunks/Payload Creator
- The async function passed to `createAsyncThunk` receives 2 arguments
  
           async ( arg & thunkAPI )=>{ payload creator arguments}
   - arg - # of arguments passed to thunk action creator
     
          Single Value --> createAsyncThunk(actionCreatorName(value1), )
          Multiple Values an object --> createAsyncThunk(actionCreatorName({key1: value1, key2: value2}), )
          E6 Destructure --> createAsyncThunk(actionCreatorName({ value1,value2 },)
  
   - thunkAPI - An object containing methods store's dispatch, getState()
 
            //action creator
            const fetchUserById = createAsyncThunk(
                   'users/fetchUserById', // action type
                   async (userId, thunkAPI) => { // payload creator
                          const response = await fetchUser(userId)
                          return response.data
                         }
               )      
## Actions Generated by createAsyncThunk()
- `createAsyncThunk(ACTION TYPE STRING)` dispatches actions for each of the promise lifecycle STATES

      `createAsyncThunk('resourceType/actionType')`--> dispatched ACTION TYPES
  
                       'resourceType/actionType/pending'
                       'resourceType/actionType/fulfilled'
                       'resourceType/actionType/rejected'
- Eg. `createAsyncThunk('users/fetchUserById')` --> 3 ACTION TYPES

                  'users/fetchUserById/pending'
                  'users/fetchUserById/fulfilled'
                  'users/fetchUserById/rejected'
- To access these action creators using dot notation

                  `fetchUserById.pending`
                  `fetchUserById.fulfilled`
                  `fetchUserById.rejected`
## Using createSlice( ) with Async Action Creators
- NOTE* `cont createASliceofStore = createSlice( { OPTION } )` </br>
      - `{ OPTIONS }--> { name, initialState, reducers }` --> action creators and action types to update that Slice's State </br>
      - Slices responds to an action creator:
  
            const [usersSlice] = createSlice({
                 name: 'users',
                 initialState: { users:  [] },
                 [reducers]: {
                      [addUser]: (state, action) => {
                      state.users.push(action.payload) 
                         }        
                 },
            })
  
- Here the action creator is --> `[usersSlice.actions.addUser]`  
- How do SLICE'S respond to action types generated by ACTION CREATORS of `createAsyncThunk( )`?
- The `extraReducers property` in the config object passed to createSlice() allows it to respond to action type generated elsewhere (Here For example: in the Thunk action creator)

       **ActionCreatorCreatedByThunk
       const actionCreatorName = createAsyncThunk(
               '[sliceName]/actionType',
               async (arg, thunkAPI) => {  //Eg. Payload Creator
                      // function body of Action Creator maybe fetch data
                    }
       );
       **Configuration Object 
       const configObject = {
               name: "[sliceName]",
               initialState: [],
               reducers:{
                  caseReducer1:(state, action)=>{ },
               },
               extraReducer:{//actions dispatched by action creator with name actionCreator
                    [actonCreatorName.pending]:(state,action) => { // function body },
                    [actonCreatorName.fulfilled]:(state,action) =>{ // function body },
                    [actonCreatorName.rejected]:(state,action) =>{ // function body },
               }
       }
       **Store will update in response to each action dispatched by the action creator 
       **Slice of State store
       const SliceofStateName = createSlice(configObject)
  

|REVEIW NOTES|
|---|
|Middleware allows the store to write asynchronous logic|
|They sit between the dispatching of an action and when it reaches the reducer|
|This allows the action to be intercepted, modified, delayed, or even cancelled before they affect your state|
|Redux allows you to add additional functionality to the store by applying middleware|
|A thunk is a function returned by another function|
|Thunks are useful for delaying computation. Instead of executing a bit of code immediately, we bundle it up into a function so that we can execute it later when the function is called|
|Thunks are useful for delaying computation|
|Creating a store with configureStore( ) includes defaut middleware |
|Redux Toolkit use middleware and thunks internally when we use createAsyncThunk()|
|Eg Thunk middleware --> createAsyncThunk( )|
|creates thunk action creators that handle asynchronous logic like API calls.|
|createAsyncThunk() is a function that accepts two arguments: a string action type and a function (called action creator)|
|thunkAPI - thunk arguments such as dispatch and getState|
|3 promise lifecycle actions: pending, fulfilled, rejected|
|Simplifies handling the lifecycle of a promise|
|Promise LifeCycle Action Types = pending, fulfilled, and rejected states|
|No need for boilerplate logic to manage State manually|
|createAsyncThunk() has reusable pattern logic to check for loading, catch errors, update state|
|Creates action creators|
|ExtraReducers property allows createSlice() to respond to action types generated elsewhere|
|How to make slices respond to actions generated by calls to createAsyncThunk()?|
|Pass the extraReducers property to createSlice|
|Redux allows you to add additional functionality to the store by applying middleware|
|---------------------------------------------------------------------------------------------------------------------------------------------------------|

