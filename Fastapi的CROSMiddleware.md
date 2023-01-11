
[[CROS跨域资源共享]]

[[中间件Middleware]]



You can configure it in your **FastAPI** application using the `CORSMiddleware`.
-   Import `CORSMiddleware`.
-   Create a list of allowed origins (as strings).
-   Add it as a "middleware" to your **FastAPI** application.


You can also specify if your backend allows:
-   Credentials (Authorization headers, Cookies, etc).
-   Specific HTTP methods (`POST`, `PUT`) or all of them with the wildcard `"*"`.
-   Specific HTTP headers or all of them with the wildcard `"*"`.

```python
from fastapi import FastAPI

from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

  

origins = [

	"http://localhost.tiangolo.com",
	
	"https://localhost.tiangolo.com",
	
	"http://localhost",
	
	"http://localhost:8080",

]

  

app.add_middleware(

	CORSMiddleware,
	
	allow_origins=origins,
	
	allow_credentials=True,
	
	allow_methods=["*"],
	
	allow_headers=["*"],

)

  
  

@app.get("/")

async def main():

	return {"message": "Hello World"}
```

The default parameters used by the `CORSMiddleware` implementation are restrictive by default, so you'll need to explicitly enable particular origins, methods, or headers, in order for browsers to be permitted to use them in a Cross-Domain context.

The following arguments are supported:

-   `allow_origins` - A list of origins that should be permitted to make cross-origin requests. E.g. `['https://example.org', 'https://www.example.org']`. You can use `['*']` to allow any origin.
-   `allow_origin_regex` - A regex string to match against origins that should be permitted to make cross-origin requests. e.g. `'https://.*\.example\.org'`.
-   `allow_methods` - A list of HTTP methods that should be allowed for cross-origin requests. Defaults to `['GET']`. You can use `['*']` to allow all standard methods.
-   `allow_headers` - A list of HTTP request headers that should be supported for cross-origin requests. Defaults to `[]`. You can use `['*']` to allow all headers. The `Accept`, `Accept-Language`, `Content-Language` and `Content-Type` headers are always allowed for [simple CORS requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#simple_requests).
-   `allow_credentials` - Indicate that cookies should be supported for cross-origin requests. Defaults to `False`. Also, `allow_origins` cannot be set to `['*']` for credentials to be allowed, origins must be specified.
-   `expose_headers` - Indicate any response headers that should be made accessible to the browser. Defaults to `[]`.
-   `max_age` - Sets a maximum time in seconds for browsers to cache CORS responses. Defaults to `600`.

The middleware responds to two particular types of HTTP request...