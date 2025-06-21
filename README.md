# REACT-REDUX-Middleware-and-Thunks

## Intro 
- When we perform an asynchronous operation(API call), based on the call then update the state
## Middleware in Redux
- Helps customize Redux frameworks: 
   - [Middleware intercepts an action dispatched and passes that action to the reducer]
   - Redux actions are dispatche to the store and processed by reducers to provide a new state, components that reference the new state are then upate or re-rendered.
- Common Tasks Middlewares Perform
  - logging,
  - caching,
  - adding auth tokens to request headers,
  - crash reporting,
  - routing, and 
  - making asynchronous requests for data
- A Redux Toolkit function make asynchronous requests possible:

      createAsychThunk() and its extraReducers property
## Write Your Own Middleware
- To __*ADD*__ middleware to a project import Redux's function: `import { applyMiddleware, createStore } from 'redux'`
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
- The Lifecycle of a Promise:
   - begins as __*pending*__ and moves to either __*fulfilled*__ or __*rejected STATES*__.
- A Good user experience requires advising the user of the __*STATE*__ of the async requests at any moment
- For asynchronous operations:

        A DISPATCHED ACTION is:
           a "Pending" action before the operation --> the result operation is "FulFilled" or "Rejected" ## createAsyncThunk( )
- A function `createThunk( param1, param2 )`
  - Param1 - __Action Type__ 'string' --> *"resourceType/actionName"*
  - Param2 - __Payload Creator__ Asynchronous Thunk Function (callback) generates a __thunk Action Creator__
      - __Action Creator__ dispatches __Promise lifecycle Actions__ ---> [pending/fulfilled/rejected]

        import { createAsyncThunk } from '@reduxjs/toolkit';
- **Call the function with 2 arguements

         const variableName = createAsyncThunk( 'resourceType/actionName', payloadCreator)


