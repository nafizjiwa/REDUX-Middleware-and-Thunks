# REACT-REDUX-Middleware-and-Thunks

## Intro 
- In Redux middleware performs asynchronous operation (as API calls) and then updates the state
- Asynchronous tasks are tasks that take time to complete
## Middleware in Redux
- Helps with Redux side effects: 
   - [Middleware intercepts an action dispatched and passes that action to the reducer]
   - Redux actions are dispatched to the store and processed by reducers to provide a new state, components that reference the new state are then upate or re-rendered.
- Common Tasks Middlewares Perform
  - logging,
  - caching,
  - adding auth tokens to request headers,
  - crash reporting,
  - routing, and 
  - making asynchronous requests for data
- In the Redux Toolkit a redux-Thunk function makes asynchronous requests possible:

      createAsychThunk() and its extraReducers property
## Write Your Own Middleware
- To __*ADD*__ middleware import the Redux function: `import { applyMiddleware, createStore } from 'redux'`
- __*NOW*__ Along with the reducer within createStore( sliceReducer ) add applyMiddleware( )

      createstore( applyMiddleWare( ) )
- __*THEN*__ calls to dispatch are calls to the middleware pipeline

      actions pass: middleware ---> to middleware ---> before app reducer
#### MIDDLEWARE STRUCTURE [A pipeline of neSted functions ] 

                  *** MIDDLEWARE FUNCTION: ***
      const exampleMiddleware = storeAPI => next =>
        action => {
                 // Body Performs the Task
        return next(action);       
      }      // This function call to the next middleware with the current action
            // Passes the action on to the pipeline's next middleware
- Middlewares have access to: `storeAPI` Which give them access to:

        `dispatch`, `getState`, `next` middleware, and dispatched `action`
- Actions are passed down the pipeline untill the last middleware
- Last middleware function call --> next(action) --> results in dispatching to the store.

        next(action) ---> becomes storeAPI.dispatch [a call to stores contents]
#### MIDDLEWARE ARE PASSED INTO createStore with 3 arguments:

      const store = createStore( arg1=reducer, arg2=initialValueOfTheStore, arg3=applyMiddleware(createMiddleware))
##### To dispatch an action after store created

      store.dispatch({'The action to dispatch can be a `message`'})
      store.dispatch(action={type:"some_value", action:"some_value"})
## Intro to Thunks
- Thunks allow asynchronous functionality in REDUX.
- A thunk is a higher-order function that wraps the computation we want to perform later.
- Thunks bundle bits of code into packages to compute later
## Promise Lifecycle Actions
- A Promise lifecycle :
   - begins as __*pending*__ and moves to either __*fulfilled*__ or __*rejected STATES*__.
- A Good user experience requires advising the user of the __*STATE*__ of the async requests at any moment
- For asynchronous operations:

        A DISPATCHED ACTION is:
           a "Pending" action before the operation --> the result operation is "FulFilled" or "Rejected"

## createAsyncThunk( )
- `createAsyncThunk( param1, param2 )` a function --> returns __thunk Action Creator__
  - Param1 - __Action Type__ 'String Value' --> *'nameOfResourceType/nameOfActionType'*
  - Param2 - __Payload Creator__ Asynchronous Thunk Function (callback) 
      - __Action Creator__ dispatches __Promise lifecycle Actions__ ---> [pending/fulfilled/rejected]

        import { createAsyncThunk } from '@reduxjs/toolkit';
- **Call the function with 2 arguements

         const variableName = createAsyncThunk( 'ResourceType/ActionType', actionNamePayloadCreator)

- Pass an asynchronous thunk function into createAsyncThunk( ) function returns an action creator that dispatches pending/fulfilled/rejected actions.

         const variableName = createAsyncThunk( 'resourceType/actionName',
              async (arg,thunkAPI) => {
                              // Body of the payload creator  })
## Passing Arguments to Thunks
- Payload Creator receives 2 arguments
  
           async ( arg & thunkAPI )=>{}
   - arg - first argument passed to thunk action creator
         - can be single value or multiple value

          Single Value --> createAsyncThunk(actionCreatorName(), )
          Multiple Values --> createAsyncThunk(actionCreatorName(), )
  

