# Week 3 — Decentralized Authentication    
     
## Installing AWS Amplify    
    
```sh
npm i aws-amplify --save
```    
Using AWS console we will create a Cognito user group   
     
## Configure Amplify      
     
We will link our Cognito user group to our code. For this we need to do following changes in `App.js`     
     
```js
import { Amplify } from 'aws-amplify';    
    
Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_APP_AWS_PROJECT_REGION,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_AWS_USER_POOLS_WEB_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});   
```    
    
Update docker-compose file with the required changes (under frontend-react-js) for Cognito      
    
```yml
REACT_APP_AWS_PROJECT_REGION: "${AWS_DEFAULT_REGION}"
REACT_APP_AWS_COGNITO_REGION: "${AWS_DEFAULT_REGION}"
REACT_APP_AWS_USER_POOLS_ID: "user_pool_id"
REACT_APP_CLIENT_ID: "client_id"    
```    
     
## Conditionally show components based on logged in or logged out     
     
Upadate/Add following inside our `HomeFeedPage.js`   
    
```js
import { Auth } from 'aws-amplify';   

  // check if we are authenicated
const checkAuth = async () => {
  Auth.currentAuthenticatedUser({
    // Optional, By default is false. 
    // If set to true, this call will send a 
    // request to Cognito to get the latest user data
    bypassCache: false 
  })
  .then((user) => {
    console.log('user',user);
    return Auth.currentAuthenticatedUser()
  }).then((cognito_user) => {
      setUser({
        display_name: cognito_user.attributes.name,
        handle: cognito_user.attributes.preferred_username
      })
  })
  .catch((err) => console.log(err));
};

// check when the page loads if we are authenicated
React.useEffect(()=>{
//prevent double call
  if (dataFetchedRef.current) return;
  dataFetchedRef.current = true;

  loadData();
  checkAuth();
}, [])    
```      
     


For force change password    
```sh
aws cognito-idp admin-set-user-password \    
  --user-pool-id <your-user-pool-id> \    
  --username <username> \
  --password <password> \
  --permanent   
```
