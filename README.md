To install the plugin, do the following:

```javascript

import VueSession from 'vue-session'
const app = createApp(App); // your normal Vue 3 app code
app.use(VueSession)

```

Now you can inject this inside your component.

```javascript
const $session = inject('$vsession');
$session.get('your_key');
```
## Options

VueSession can be started with some options that will change its behavior.

- `persist` A Boolean value to determine whether the data stored in the session may persist between tabs and browser instances. Defaults to `false`.

Pass the options in the `use` method:

```javascript
var options = {
    persist: true
}

app.use(VueSession, options)
```

## Reference

- `$session.getAll()`, returns all data stored in the Session.
- `$session.set(key,value)`, sets a single value to the Session.
- `$session.get(key)`, returns the value attributed to the given key.
- `$session.start()`, initializes a session with a 'session-id'. If you attempt to save a value without having started a new session, the plugin will automatically start a new $session.
- `$session.exists()`, checks whether a session has been initialized or not.
- `$session.has(key)`, checks whether the key exists in the Session
- `$session.remove(key)`, removes the given key from the Session
- `$session.clear()`, clear all keys in the Session, except for 'session-id', keeping the Session alive
- `$session.destroy()`, destroys the Session
- `$session.id()`, returns the 'session-id'
- `$session.renew(session_id)`, allows a user to renew a previous session by manually inputting the session_id

### Flash

Flash allows you to save data until you read them without having to start a regular Session.

- `$session.flash.set(key, value)`, sets a flash value
- `$session.flash.get(key)`, reads and removes a flash value
- `$session.flash.remove(key)`, removes a flash value


## Example

Your login method could look like this:

```javascript
import { inject } from "vue";

export default {
  setup(props, { emit }) { //props, context
    // Inside vue-session we are using provide, so you can inject it further
    // app.provide('$vsession', $vsession);

    const $session = inject('$vsession');
    const handleSubmit = () => {
      // your validation code
      // your method code
      // your API logics...
      
      // sample code
      axios.post(YOUR_API_URL + '?_format=json', JSON.stringify(YOUR_JSON), {
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
            'X-CSRF-Token': sessionToken
          },
          withCredentials: true
        })
          .then(userResponse => {
            $session.start()
            $session.set('token', userResponse.csrf_token)
            $session.set('isLoggedIn', true)
            resolve(userResponse)
          })
          .catch(error => {
            // handle any errors
            if (error.response.status == 403) {
              console.log('User already logged in! ' + $session.get('token'))
            }
            let message = error.response.data.message
            reject(message)
          })
    }
    return {
      handleSubmit
    };
}
```

In your logged-in area, you can check whether or not a session is started and destroy it when the user wants to logout.
