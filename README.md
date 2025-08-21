# REACT-REDUX-Middleware-and-Thunks

## Intro 
#### In Redux asynchronous actions to update state (Like long API calls) involves:
1. The Middleware Function - applyMiddleware( )
2. Thunks
3. Promise lifecycle
#### REDUX toolkit functions are configureStore( ), createSlice( ), createAsyncThunk( )   
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
           "PENDIGN"  -- NOTIFIES THE STORE THE DATA has been requested
              try{
              "FULFILLED"  -- NOTIFIES the STORE THE DATA is updated or
              } catch(err){
              "REJECTED"   -- NOTIFIES THE STORE THE DATA failed to fetch 
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

- createAsyncThunk( ) function returns an thunk action creator which will dispatch actions --> pending/fulfilled/rejected.

## Passing Arguments to Thunks/Payload Creator
- THE 2 arguments of  `createAsyncThunk('actiontype', async function() )
- THE 2 arguments of THE async( arg, thunkAPI ) function:
- arg - can take one argument

         async( arg, thunkAPI ) => { }
- arg - or multiple arguments using an object

         async( { arg1, arg2 }, thunkAPI ) => { }  

          
 - To unpack the object USE E6 DESTRUCTURING -->

               createAsyncThunk( 'actionType',
                    async({ value1,value2 }, thunkAPI)
- thunkAPI: The payload creator - Is an object with methods: store's dispatch, getState()
 
            //action creator
            const fetchUserById = createAsyncThunk(
                   'users/fetchUserById',              // action type
                   async(userId, thunkAPI) => {      // payload creator
                          const response = await fetchUser(userId)
                          return response.data
                         }
               )      
## Dispatched Actions Generated by createAsyncThunk('ActionType', asynFnc)
- THE ACTIONS DISPATCHED BY `createAsyncThunk()` - are the promise lifecycle STATES:
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
#### - extraReducers allows createSlice() to respond to action types generated elsewhere

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

