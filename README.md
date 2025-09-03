# REACT-REDUX-Middleware-and-Thunks

## Intro 
#### In Redux `asynchronous actions` to update the state (Lengthy API calls) involves:
1. The Middleware Function - applyMiddleware( )
2. Thunks
3. Promise lifecycle

|CORE REDUX | FUNCTIONS|
|----|-----|
|Function|Purpose|
|||
|createStore()|Creates the Redux store that holds the state tree of your application.|
|combineReducers()|Merges multiple reducer functions into a single root reducer.|
|applyMiddleware()|Enhances the store with middleware for handling side effects.|

|REDUX toolkit| FUNCTIONS |
|----|----|
|Function|Purpose|
|||
|configureStore( )|wraps createStore, applyMiddleware, and combineReducers| 
|createSlice( )|auto-generates reducers and action creators.|
|createAsyncThunk( )|andles async logic with built-in lifecycle actions.|
	
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
- To __*ADD*__ `middleware` to app use redux function applyMiddleware:

      import { applyMiddleware, createStore } from 'redux'
  
- __*INPUT*__ the `middleware` function into `createStore( )` with applyMiddleware:

      creates_A_SliceReducer = createstore(
              reducer,
              intialState, 
              applyMiddleware( middleware1, middleware2, middleware3 )
        )
- __*DIPSATCH ACTIONS*__ The Dispatched Action is passed thru the middleware pipeline:

      actions move: middleware ---> to middleware ---> before app reducer
#### Once middleware is added a call to dispatch is a call to middleware

      actions --> dispatch --> all MiddleWares --> reducer

#### *** MIDDLEWARE STRUCTURE (A NESTED PIPLINE OF THESE FUNCTIONS BELOW ): ***
- Middleware has access to: `storeAPI` see below:
- storeAPI functions: `dispatch`, `getState`, `next` middleware, and dispatched `action`

      const exampleMiddlewareFunction = storeAPI => next =>
        action => {
           store.API.getState()    // Function Body Performs the Task to get state
        return next(action);     //THIS IS A call to next middleware function
      }                        // ACTION IS PASSED to next middleware in pipeline
            
- WHEN ACTION REACHES the last middleware in pipeline:

        next(action) ---> becomes storeAPI.dispatch(action) - an ACTION dipatched to the store
##### To dispatch an action: after store created

      store.dispatch({'The action to dispatch can be a `message`'})
      store.dispatch(action={type:"some_value", payload:"some_value"})
## Intro to Thunks
- Thunks are higher-order functions which wraps code and passes it on to compute later

         const add = (x,y) => {      -->THE ADD FUNCTION RETURNS A THUNK
           return () => {            -->THUNK
             return x + y;           --> The FUNCTION RETURNED BY THUNK
           } 
         }

## Promise Lifecycle Actions
- A Promise lifecycle :
   - STARTS with __*pending*__ and moves to __*fulfilled*__ or __*rejected STATES*__.
- The PROMISE LIFECYCLE helps to shows the __*STATE*__ of the async requests.

        const fetchrUserById =(id) => {
           "PENDING"  -- NOTIFIES THE STORE THE DATA has been requested
              try{
                "FULFILLED"  -- NOTIFIES the STORE THE DATA is updated or
              } catch(err){
                 "REJECTED"   -- NOTIFIES THE STORE THE DATA failed to fetch 
              }   
           }

## createAsyncThunk(param1, param2)
- THE REDUX TOOLKIT FUNCTION to manage asynchronous actions (eg. API calls)
- It generates action types `pending/fulfilled/rejected` for the promise lifecycle. 
- __*createAsyncThunk( actionType, async callback )*__ --> returns __thunk Action Creator__

        import { createAsyncThunk } from '@reduxjs/toolkit'; //1st import
           //2nd call createAsyncThunk with 2 arguments
        const actionCreatorName = createAsyncThunk( 'SliceName/ActionName', //1. 'Action Type'
              async (arg, thunkAPI) => {               //2. Async callback fnc or Payload Creator
                  // Body of the payload creator returns a promise
            })

- createAsyncThunk( ) function returns an thunk action creator which will dispatch actions --> pending/fulfilled/rejected.
- createAsyncThunk() accepts action type string and a callback function
- It generates promise lifecycle action types and returns a thunk action creator.
- The Thunk will run the promise callback and dispatch the lifecycle actions based on the returned promise.
- The callback returns a promise. 

## Passing Arguments to Thunks (async callback/Payload Creator)
- THE 2 arguments of  `createAsyncThunk('actiontype', async callback func )
- THE 2 arguments of THE `Thunk` --> async( arg, thunkAPI ) function:
- `arg` - can take one argument

         async( arg, thunkAPI ) => { }
- `arg` - or multiple arguments using an object

         async( { arg1, arg2 }, thunkAPI ) => { }  

          
 - To unpack the object USE E6 DESTRUCTURING -->

               createAsyncThunk( 'actionType',
                    async({ value1,value2 }, thunkAPI)
- `thunkAPI`: The payload creator - Is an { object } with methods: store's dispatch, getState()
 
            //action creator
            const fetchUserById = createAsyncThunk(
                   'users/fetchUserById',              // action type
                   async(userId, thunkAPI) => {      // payload creator
                          const response = await fetchUser(userId)
                          return response.data
                         }
               )      
## Dispatched Actions Generated by createAsyncThunk('ActionType', asynFnc)
- THE ACTIONS DISPATCHED - are the promise lifecycle STATES:
#### $~~~~~~~~~~~~~~~~~~~~~$*** PENDING, FULFILLED, REJECTED ****
#### EG. AN ACTION TYPE 'resource/actionType' PASSED in to `createAsyncThunk('resourceType/actionType', ASYNC)`
#### --> THE 3 DISPATCHED ACTION TYPES:
                    
- Eg. `createAsyncThunk('users/fetchUserById')`

                'resourceType/actionType/pending' -- 'users/fetchUserById/pending'
                                                     'users/fetchUserById/fulfilled'
                                                     'users/fetchUserById/rejected'
- To access these action creators USE DOT NOTATION:

                  `actionType/pending` -- `fetchUserById.pending`
                                          `fetchUserById.fulfilled`
                                          `fetchUserById.rejected`
## Using createSlice( )'s `extraReducer` with Async (Thunk) Action Creators
#### - NOTE* `createSlice( {accepts an OPTIONS argument} ) => creates_A_Slice_Of_Store ` </br>
   - `{ OPTIONS is an Object }` --> { contains config params = name, initialState, reducers, `EXTRA REDUCERS`}
      --> The Parameters generate a Slice of the store with functions to update SLICE'S STATE (action creators, action types).</br>
#### - The extraReducer property allows createSlice() to respond to action types generated anywhere else not only in the Slice
#### - SLICE'S RESPOND TO THEIR OWN  ACTION CREATORS: `slicesName.actions.actionType` they are NOT AFFECTED BY Thunk ACTION CREATORS

         const usersSlice = createSlice({
                       ==> responds to ACTION CREATORS ==> `usersSlice.actions.addUser`
             reducers: {           
                 addUser:(state,action)=> { //code  }        
            },  })
##### - FOR THIS SLICE to respond to Thunk ACTION TYPE below.

       const actionCreatorName = createAsyncThunk(
               'actionType',
               async (arg, thunkAPI) => {  //Eg. Payload Creator
                      // function body fetches data
                    }
       );

#### ADD THE 3 ACTION TYPES CREATED BY THE Thunk TO A SLICE'S extraReducer property

            const configObject = {           **Configuration Object 
               name:, initialState:, reducers:{ caseReducer1:(state, action)=>{ }, },
               extraReducer:{
                        //actions dispatched by action creator name `actionCreator`
                    [actonCreatorName.pending]:(state,action) => { // function body },
                    [actonCreatorName.fulfilled]:(state,action) =>{ // function body },
                    [actonCreatorName.rejected]:(state,action) =>{ // function body },
                 }
             }
       const SliceofStateName = createSlice(configObject)
#### - extraReducers allows createSlice() to respond to action types generated anywhere else

|REVEIW NOTES|
|---|
|Redux toolkit tools createSlice( ) and createAsyncThunk( )|
|The STORE uses Middleware to write asynchronous actions|
|REDUX Middleware is a function that sits between dispatching an action and the moment it reaches the reducer|
|The actions can be intercepted, modified, delayed, or cancelled before they affect|
|Applying REDUX middleware provides additional store functionality|
|A thunk is a function returned by another function|
|Thunks are useful for delaying computation until it is needed by an application it is executed later when the function is called|
|Creating a store with configureStore( ) includes middleware internally |
|Redux Toolkit's createAsyncThunk() uses middleware and thunks internally|
|Eg Thunk middleware --> createAsyncThunk( )|
|creates thunk action creators that handle asynchronous logic like API calls.|
|createAsyncThunk() function accepts 2 arguments: 'action type' string and a function (called action creator)|
|thunkAPI - thunk argument with access to dispatch and getState|
|3 promise lifecycle actions: pending, fulfilled, rejected states|
|No need for boilerplate logic to manage State manually|
|Creates action creators|
|ExtraReducers property allows createSlice() to respond to action types generated elsewhere|
|How to make SLICE's respond to createAsyncThunk( ) action types?|
|Pass the extraReducers property to createSlice|

