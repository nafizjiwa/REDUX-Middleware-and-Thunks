# REACT-REDUX-Middleware-and-Thunks

## Intro 
#### Redux tools used to write asynchronous actions (Long API calls) and update state
- The Middleware Function - applyMiddleware( )
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
- Thunks are higher-order functions which wrap and pass the code we want to compute later

         const add = (x,y) => {      -->THE ADD FUNCTION RETURNS A THUNK
           return () => {            -->THUNK
             return x + y;           --> The FUNCTION RETURNED BY THUNK
           } 
         }

## Promise Lifecycle Actions
- A Promise lifecycle :
   - STARTS with __*pending*__ and moves to __*fulfilled*__ or __*rejected STATES*__.
- A Good user experience is showing the uswer the __*STATE*__ of the async requests.

        const fetchrUserById =(id) => {
           "Pending"  -- NOTIFIES THE STORE THE DATA has been requested
              try{
              "FulFilled"  -- NOTIFIES the STORE THE DATA is updated or
              } catch(err){
              "Rejected"   -- NOTIFIES THE STORE THE DATA failed to fetch 
              }   
           }

## createAsyncThunk(2 params)
- THE REDUX TOOLKIT FUNCTION to manage asynchronous actions (eg. API calls)
- It generates action types `pending/fulfilled/rejected` for the promise lifecycle. 
- __*createAsyncThunk( param1, param2 )*__ --> returns __thunk Action Creator__

        import { createAsyncThunk } from '@reduxjs/toolkit'; //1st import
           //2nd call createAsyncThunk with 2 arguments
        const actionCreatorName = createAsyncThunk( 'SliceName/ActionName', //1. 'Action Type'
              async (arg,thunkAPI) => {               //2. Async callback fnc or Payload Creator
                  // Body of the payload creator returns a promise
            })

- Pass an asynchronous thunk function into createAsyncThunk( ) function returns an action creator that dispatches the action types/actions --> pending/fulfilled/rejected.

## Passing Arguments to Thunks/Payload Creator
- 2 arguments of  `createAsyncThunk('actiontype', async function() )
- ALSO 2 arguments of async function/action creator:

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
## Dispatched Actions Generated by createAsyncThunk('ActionType', asynFnc)
- THE ACTIONS DISPATCHED BY `createAsyncThunk()` - are the promise lifecycle STATES
#### $~~~~~~~~~~~~~~~~~~~~~~~~~$*** PENDING, FULFILLED, REJECTED ***</pre>
#### SO ACTION TYPE 'resource/actionType' PASSED in `createAsyncThunk('resourceType/actionType', ASYNC)`
#### --> THE 3 DISPATCHED ACTION TYPES:
                    
- Eg. `createAsyncThunk('users/fetchUserById')`

                'resourceType/actionType/pending' -- 'users/fetchUserById/pending'
                                                     'users/fetchUserById/fulfilled'
                                                     'users/fetchUserById/rejected'
- To access these action creators USE DOT NOTATION:

                  `actionType/pending` -- `fetchUserById.pending`
                                          `fetchUserById.fulfilled`
                                          `fetchUserById.rejected`
## Using createSlice( ) with Async Action Creators
#### - REFRESH NOTE* `createSlice( { accepts an OPTION argument} ) = const creates_A_Slice_Of_Store ` </br>
   - `{ OPTIONS is an Object }` --> { contains config params = name, initialState, reducers, `EXTRA REDUCERS`}
      --> Parameters are used to generate a Slice of the store and functions to update SLICE (action creators, action types).</br>
#### - A SLICE will RESPOND TO ACTION CREATORS with a type: `slicesName.actions.actionType`
##### - The SLICE `usersSlice` will respond to ACTION CREATORS ==> `usersSlice.actions.addUser` but if won't be affected by action creators of a Thunk

         const usersSlice = createSlice({
            //code
             reducers: {           
                 addUser:(state,action)=> { //code  }        
            },  })
##### - How does this SLICE respond to ACTION TYPES created by a `createAsyncThunk('users/fetchUserById' )`?
- Using the `extraReducers property` of createSlice().
##### EG.: THIS async Action Creator created by this Thunk will generate 3 action types

       const actionCreatorName = createAsyncThunk(
               'actionType',
               async (arg, thunkAPI) => {  //Eg. Payload Creator
                      // function body fetches data
                    }
       );

#### For any SLICE to respond the action types of a Thunk the actions must be added into the SLICE'S extra reducer property *BELOW*
            const configObject = {           **Configuration Object 
               name:, initialState:,
               reducers:{
                  caseReducer1:(state, action)=>{ },
               },
               extraReducer:{//actions dispatched by action creator with name actionCreator
                    [actonCreatorName.pending]:(state,action) => { // function body },
                    [actonCreatorName.fulfilled]:(state,action) =>{ // function body },
                    [actonCreatorName.rejected]:(state,action) =>{ // function body },
               }
       }
  
       const SliceofStateName = createSlice(configObject)
##### - extraReducers allows createSlice() to respond to action types generated elsewhere

|REVEIW NOTES|
|---|
|The STORE uses Middleware to write asynchronous actions|
|REDUX Middleware is a function that sits between dispatching an action and the moment it reaches the reducer|
|The actions can be intercepted, modified, delayed, or cancelled before they affect|
|A thunk is a function returned by another function|
|Thunks are useful for delaying computation. Instead of executing a bit of code immediately, we bundle it up into a function so that we can execute it later when the function is called|
|Thunks are useful for delaying computation|
|Creating a store with configureStore( ) includes default middleware |
|Redux Toolkit use middleware and thunks internally when we use createAsyncThunk()|
|Eg Thunk middleware --> createAsyncThunk( )|
|creates thunk action creators that handle asynchronous logic like API calls.|
|createAsyncThunk() function accepts 2 arguments: 'action type' string and a function (called action creator)|
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

