# REACT-REDUX-Middleware-and-Thunks

## Intro 
- Redux Middleware performs asynchronous actions (like lenghty API calls) to update state
#### Redux tools used to write asynchronous actions
- Middleware Function
- Thunks
- Promise lifecycle
## Middleware in Redux
- Helps with Redux side effects:
   - Middleware runs after an action is dispatched and before the action is passed to the reducer
   - Redux actions are dispatched to the store, processed by reducers to provide a new state
   - Components which need the new state are re-rendered with the new state.
#### Common Tasks Middlewares Perform - logging, caching, routing, asynchronous data requests, token authoritzations.
- To make asynchronous requests the Redux Toolkit uses:

        1. createAsychThunk(input asynchronous request)
                 - uses middleware and thunks
        2. createSlice's extraReducers property
  
## Write Your Own Middleware
- __*ADD*__ `middleware` to app:

      import { applyMiddleware, createStore } from 'redux {functions}'
  
- __*INPUT*__ `middleware` into `createStore( )`:

      creates_A_SliceReducer = createstore( reducer, '', applyMiddleWare(PASSED IN MIDDLEWARE ) )
                              ' ' = initial state is empty hence reducers default value will equal initial state value

- __*DIPSATCH ACTIONS*__ The Dispatched Action is passed thru the middleware pipeline:

      actions move: middleware ---> to middleware ---> before app reducer
#### Once middleware is added a call to dispatch is a call to middleware

      actions --> dispatch --> all MiddleWares --> reducer
#### MIDDLEWARE STRUCTURE (It'S A PIPELINE of NESTED functions )
- Middlewares have access to: `storeAPI` and the stores functions:

        `dispatch`, `getState`, `next` middleware, and dispatched `action`

   #### *** MIDDLEWARE STRUCTURE: ***
      const exampleMiddlewareFunction = storeAPI => next =>
        action => {
              // Function Body Performs the Task
        return next(action);   //Here a call to next middleware function with action
      }           // Passes the action to next middleware in pipeline
            
- Actions are passed down the pipeline untill the last middleware
- The last middleware function call --> next(action) --> results in a dispatch to the store.

        next(action) ---> becomes storeAPI.dispatch(a call to the store and its funcitons)
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
- Redux uses Thunks for asynchronous requests.
- Thunks are higher-order functions which wrap and passes the code we want to compute later

         const add = (x,y) => {      -->THE ADD FUNCTION RETURNS A THUNK
           return () => {            -->THUNK
             return x + y;           --> The FUNCTION RETURNED BY THUNK
           } 
         }

## Promise Lifecycle Actions
- A Promise lifecycle :
   - begins as __*pending*__ and moves to either __*fulfilled*__ or __*rejected STATES*__.
- A Good user experience is advising the user of the __*STATE*__ of the async requests:
  `with a message, a loading sign or error message`
- For asynchronous operations:

        A DISPATCHED ACTION to the store is:
        const fetchrUserById =(id) => {
           "Pending" store shows data requested
              try{
              "FulFilled" store data updated or
              } catch(err){
              "Rejected"  store data failed to fetch
              }
           }

## createAsyncThunk(2 params)
- The Redux Tookit function to manage asynchronous operations like API calls
- It automatically generates action types for the promise lifecycle: pending/fulfilled/rejected
- __*createAsyncThunk( param1, param2 )*__ --> returns __thunk Action Creator__

        import { createAsyncThunk } from '@reduxjs/toolkit'; //1st import
           //2nd call createAsyncThunk with 2 arguments
        const actionCreatorName = createAsyncThunk( 'SliceName/ActionName', //1. 'Action Type'
              async (arg,thunkAPI) => {               //2. Async callback fnc or Payload Creator
                  // Body of the payload creator returns a promise
            })

- Pass an asynchronous thunk function into createAsyncThunk( ) function returns an action creator that dispatches the action types/actions --> pending/fulfilled/rejected.

## Passing Arguments to Thunks/Payload Creator
- 2 arguments of  `createAsyncThunk('actiontype',async function() )
- 2 arguments of async function/action creator:

           async( arg, thunkAPI ) => { }
- arg - can take one arguments

           async( arg1, thunkAPI ) => { }
- arg - or multiple arguments using an object

           async( { arg1, arg2 }, thunkAPI ) => { }  
 - To unpack the object E6 Destructure -->

               createAsyncThunk( 'actionType',
                    async{ value1,value2 }, thunkAPI)
- The payload creator 2nd arg thunkAPI - Is an object containing methods: store's dispatch, getState()
 
            //action creator
            const fetchUserById = createAsyncThunk(
                   'users/fetchUserById',              // action type
                   async(userId, thunkAPI) => {      // payload creator
                          const response = await fetchUser(userId)
                          return response.data
                         }
               )      
## Actions Generated by createAsyncThunk()
- `createAsyncThunk('AType', asynFnc)` dispatches actions for each of the promise lifecycle STATES - pending, fulfilled, rejected
#### PASS AN ACTION TYPE STRING - 'resource/actionType' to createAsyncThunk
#### `createAsyncThunk('resourceType/actionType', ASYNC)`--> The ACTION TYPES dispatched:
                    
- Eg. `createAsyncThunk('users/fetchUserById')` --> 3 ACTION TYPES

                'resourceType/actionType/pending' -- 'users/fetchUserById/pending'
                                                     'users/fetchUserById/fulfilled'
                                                     'users/fetchUserById/rejected'
- To access these action creators USE DOT NOTATION:

                  `actionType/pending` -- `fetchUserById.pending`
                                          `fetchUserById.fulfilled`
                                          `fetchUserById.rejected`
## Using createSlice( ) with Async Action Creators
#### - REFRESH NOTE* `cont creates_A_Slice_Of_Store = createSlice( { accepts an OPTION argument} )` </br>
   - `{ OPTIONS Object }--> { contains config params = name, initialState, reducers, extra reducers }`
      --> Parameters are used to generate a Slice of the store and functions to update SLICE (action creators, action types).</br>
#### - SLICES RESPOND TO ACTION CREATORS: `slicesName.actions.actionType`
#####  --> This SLICE responds to (usersSlice.actions.addUser) ACTION CREATORS
  
         const usersSlice = createSlice({
            //code
             reducers: {           
                 addUser: (state, action) => { //code
                   }        
            },  })
- So How do SLICE'S respond to action types generated by ACTION CREATORS of `createAsyncThunk( )`?
- The `extraReducers property` of createSlice() responds to action type generated elsewhere.
##### For example: Action Creator created by Thunk

       const actionCreatorName = createAsyncThunk(
               '[sliceName]/actionType',
               async (arg, thunkAPI) => {  //Eg. Payload Creator
                      // function body fetches data
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

