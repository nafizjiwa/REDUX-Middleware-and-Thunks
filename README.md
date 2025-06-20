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
- __*NOW*__ within createStore( ) add applyMiddleware( )

      createstore( applyMiddleWare( ) )
- __*THEN*__ calls to dispatch are calls to the middleware pipeline

      actions pass: middleware ---> to middleware ---> before app reducer
- Middlewares follow specific nested function structure to work as a pipeline

      const exampleMiddleware = storeAPI => next
      => action => {
              // Body Performs the Task
              // Then calls the next middleware with the current action
        return next(action);        
      }       // which passes the action on to the pipeline's next middleware 
- Middlewares have access to: `storeAPI` Which give them access to:

        `dispatch`, `getState`, `next` middleware, and dispatched `action`
- Actions are passed down the pipeline untill the last middleware
- Last middleware call --> next(action) --> results in dispatching to the store.

        next(action) ---> becomes storeAPI.dispatch
  
## Intro to Thunks
