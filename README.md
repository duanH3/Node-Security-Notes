# Node-Security-Notes

# TLS over HTTPS

Always use TLS encryption over https:// when working with forms so that the submitted data is encrypted when it’s sent 
across the Internet. If you send form data over http://, it’s sent in plain text and can be visible to anyone eavesdropping 
on those packets as they journey across the Internet.

# Wear Your Helmet

There’s a neat little middleware called helmet that adds some security from HTTP headers. 
It’s best to include right at the top of your middlewares and is super easy to include:

```
// server.js
const helmet = require('helmet')

middlewares = [
  helmet()
  // ...
]
```

