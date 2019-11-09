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


# Cross-site Request Forgery (CSRF)

You can protect yourself against cross-site request forgery by generating a unique token when the user is presented with a form 
and then validating that token before the POST data is processed. There’s a middleware to help you out here as well:

```
// server.js
const csrf = require('csurf')

middlewares = [
  // ...
  csrf({ cookie: true })
]

```

In the GET request we generate a token:

```
// routes.js
router.get('/contact', (req, res) => {
  res.render('contact', {
    data: {},
    errors: {},
    csrfToken: req.csrfToken()
  })
})
```

And also in the validation errors response:

```
router.post('/contact', [
  // validations ...
], (req, res) => {
  const errors = validationResult(req)
  if (!errors.isEmpty()) {
    return res.render('contact', {
      data: req.body,
      errors: errors.mapped(),
      csrfToken: req.csrfToken()
    })
  }

  // ...
})
```

Then we just need include the token in a hidden input:
```
<!-- view/contact.ejs -->
<form method="post" action="/contact" novalidate>
  <input type="hidden" name="_csrf" value="<%= csrfToken %>">
  <!-- ... -->
</form>
```

That’s all that’s required.

We don’t need to modify our POST request handler as all POST requests will now require a valid token by the csurf middleware. If a valid CSRF token isn’t provided, a ForbiddenError error will be thrown, which can be handled by the error handler defined at the end of server.js.

You can test this out yourself by editing or removing the token from the form with your browser’s developer tools and submitting.


# Cross-site Scripting (XSS)

