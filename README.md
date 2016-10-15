#Webtask token authentication

>Small repos to show how it is swift and easy to deploy a JSON web token authentication with the [Webtask.io](https://webtask.io/) FaaS

:buld: Free functions expires after 1 month

getToken.js

```js
const jwt = require('jsonwebtoken');
module.exports = (context, cb) =>  cb(null, jwt.sign({ username: context.data.username }, 'YOUR_SECRET', { expiresIn: '1h' }));
```

`wt create getToken.js`

Then retrieve with `fetch()`

```js
const getToken = {
    url: 'https://wt-artgreg-outlook-fr-0.run.webtask.io/getToken?webtask_no_cache=1',
    headers: {
        method: 'POST',
        headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({username: 'chisNaN'})
    }
};

fetch(getToken.url, getToken.headers).then(r => r.text())
.then(token => console.log(token)).catch(e => console.warn(e));
```

checkToken.js

```js
const jwt = require('jsonwebtoken');

module.exports = (context, cb) => {
  const token = context.headers.authorization;
  jwt.verify(token, 'YOUR_SECRET', (err, decoded) => {
    if(err) {
      cb(null, err);
    }
    if(decoded && decoded.username === 'chisNaN') {
      cb(null, `User found => ${decoded.username}`);
    }else {
      cb(null, 'User not found');
    }
  });
}
```

`wt create checkToken.js`

Then check with `fetch()`

```js
const checkToken = {
    url: 'https://wt-artgreg-outlook-fr-0.run.webtask.io/checkToken?webtask_no_cache=1',
    headers: {
        method: 'GET',
        headers: {
            'Authorization': 'YOUR_TOKEN'
        }
    }
};

fetch(checkToken.url, checkToken.headers).then(r => r.text())
.then(loginStatus => console.log(loginStatus)).catch(e => console.warn(e));
```