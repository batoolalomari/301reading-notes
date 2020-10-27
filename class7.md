
# SuperAgent
SuperAgent is light-weight progressive ajax API crafted for flexibility, readability, and a low learning curve after being frustrated with many of the existing request APIs. It also works with Node.js

![](super.png)

# test documentation
## request

    res.statusCode
    //should set statusCode
    request.get(`${uri}/login`, (err, res) => {
       try {
    assert.strictEqual(res.statusCode, 200);
   
         } catch (err_) {
           done(err_);
         }
        });
        

      assert.ifError(err);
      assert.strictEqual(res.status, 500);
      assert(!calledErrorEvent);
      assert(calledOKHandler);
      done();
    } catch (err_) {
      done(err_);
    }
 
A request can be initiated by invoking the appropriate method on the request object, then calling .then() (or .end() or await) to send the request. For example a simple GET request:

![](super2.png)

HTTP method may also be passed as a string:
      
      request('GET', '/search').then(success, failure);
      
Absolute URLs can be used. In web browsers absolute URLs work only if the server implements **CORS**.      
![](super3.png)

# GET requests

The .query() method accepts objects, which when used with the GET method will form a query-string. The following will produce the path /search?query=Manny&range=1..5&order=desc.
![](super4.png)

Or as a single object:
![](super5.png)

The .query() method accepts strings as well:
![](super6.png)

Or joined:
![](super7.png)

# POST / PUT requests

A typical JSON POST request might look a little like the following, where we set the Content-Type header field appropriately, and "write" some data, in this case just a JSON string.

![](post.png)

Since JSON is undoubtedly the most common, it's the default! The following example is equivalent to the previous.
![](post2.png)

Or using multiple .send() calls:
![](post3.png)

By default sending strings will set the Content-Type to application/x-www-form-urlencoded, multiple calls will be concatenated with &, here resulting in name=tj&pet=tobi:
![](post4.png)

SuperAgent formats are extensible, however by default "json" and "form" are supported. To send the data as application/x-www-form-urlencoded simply invoke .type() with "form", where the default is "json". This request will POST the body "name=tj&pet=tobi".

![](post5.png)

Sending a FormData object is also supported. The following example will POST the content of the HTML form identified by id="myForm":
![](post6.png)

# Setting the Content-Type

The obvious solution is to use the .set() method:
![](set.png)

As a short-hand the .type() method is also available, accepting the canonicalized MIME type name complete with type/subtype, or simply the extension name such as "xml", "json", "png", etc:

![](type.png)

# Query strings
req.query(obj) is a method which may be used to build up a query-string. For example populating ?format=json&dest=/login on a POST:

![](q.png)

By default the query string is not assembled in any particular order. An asciibetically-sorted query string can be enabled with req.sortQuery(). You may also provide a custom sorting comparison function with req.sortQuery(myComparisonFn). The comparison function should take 2 arguments and return a negative/zero/positive integer.

![](q2.png)

# Response properties

Many helpful flags and properties are set on the Response object, ranging from the response text, parsed response body, header fields, status flags and more.

## Response text
The res.text property contains the unparsed response body string. This property is always present for the client API, and only when the mime type matches "text/", "/json", or "x-www-form-urlencoded" by default for node. The reasoning is to conserve memory, as buffering text of large bodies such as multipart files or images is extremely inefficient. To force buffering see the "Buffering responses" section.

## Response body
Much like SuperAgent can auto-serialize request data, it can also automatically parse it. When a parser is defined for the Content-Type, it is parsed, which by default includes "application/json" and "application/x-www-form-urlencoded". The parsed object is then available via res.body.

## Response header fields
The res.header contains an object of parsed header fields, lowercasing field names much like node does. For example res.header['content-length'].

## Response Content-Type
The Content-Type response header is special-cased, providing res.type, which is void of the charset (if any). For example the Content-Type of "text/html; charset=utf8" will provide "text/html" as res.type, and the res.charset property would then contain "utf8".

## Response status
The response status flags help determine if the request was a success, among other useful information, making SuperAgent ideal for interacting with RESTful web services. These flags are currently defined as:

![](res.png)

# Aborting requests
To abort requests simply invoke the req.abort() method.

## Timeouts
Sometimes networks and servers get "stuck" and never respond after accepting a request. Set timeouts to avoid requests waiting forever.

req.timeout({deadline:ms}) or req.timeout(ms) (where ms is a number of milliseconds > 0) sets a deadline for the entire request (including all uploads, redirects, server processing time) to complete. If the response isn't fully downloaded within that time, the request will be aborted.

req.timeout({response:ms}) sets maximum time to wait for the first byte to arrive from the server, but it does not limit how long the entire download can take. Response timeout should be at least few seconds longer than just the time it takes the server to respond, because it also includes time to make DNS lookup, TCP/IP and TLS connections, and time to upload request data.

You should use both deadline and response timeouts. This way you can use a short response timeout to detect unresponsive networks quickly, and a long deadline to give time for downloads on slow, but reliable, networks. Note that both of these timers limit how long uploads of attached files are allowed to take. Use long timeouts if you're uploading files.

        request
            .get('/big-file?network=slow')
            .timeout({
                  response: 5000,  // Wait 5 seconds for the server to start sending,
                  deadline: 60000, // but allow 1 minute for the file to finish loading.
                    })
            .then(res => {
              /* responded in time */
                         }, err => {
                           if (err.timeout) { /* timed out! */ } else { /* other error */ }
                                        });


## Authentication
In both Node and browsers auth available via the .auth() method:

              request
                   .get('http://local')
                   .auth('tobi', 'learnboost')
                   .then(callback);
In the Node client Basic auth can be in the URL as "user:pass":

request.get('http://tobi:learnboost@local').then(callback);
By default only Basic auth is used. In browser you can add {type:'auto'} to enable all methods built-in in the browser (Digest, NTLM, etc.):

      request.auth('digest', 'secret', {type:'auto'})

The auth method also supports a type of bearer, to specify token-based authentication:

     request.auth('my_token', { type: 'bearer' })


























      
      
      
      
      
