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
- To add middleware to a project use Redux's: `applyMiddleware`
- Then within createStore( ) add applyMiddleware( )

      createstore( applyMiddleWare( ) )
- 
## Intro to Thunks
