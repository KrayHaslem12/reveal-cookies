# Cookies

## Managing browser cookies, using js-cookie, and fetching data securely with Flask

???
Use this slide to introduce yourself and give an overview of the topic. Emphasize that you'wll cover::

- How cookies work in the browser
- Using js-cookie for the front-end management
- Setting cookies in Flask on the back-end
- Fetching data with cookies securely (CORS, credentials)

---

# Agenda

1. **Cookies 101**
2. **Setting & Managing Cookies in JavaScript**
3. **js-cookie Library**
4. **Flask and Cookie Integration**
5. **Fetching Data with Cookkies**
6. **Q & A**

???
Briefly introduce teams and mention how each part builds on the last.

---

# Cookies 101

(From 14.1 Cookies)

- \*_Definition_: Small pieces of data stored in the browser
- \*_Persistence_: Either session-only or with an expiration date
- \*_Transport_: Exchanged via HTTP request/response headers

???
Cookies are for session management, user preferences, and authentication.

---

- **Key Properties**:
  - Name/Value
  - Expires/Max-Age
  - Path
  - Secure
  - HttpOnly

???
Cookies are set up with a Name Value relationship, can be given an expiration (without are a session based cookie), can be set with a path and only available when that path is active, secure cookies will not work over http, and a cookie with the HttpOnly attribute can't be accessed by JavaScript

---

# Manual Cookie Handling (JS)

Create/Update
document.cookie = "username=JohnDoe";

Read all cookies
console.log(document.cookie);

Overwrite
document.cookie = "username=JaneDoe";

Delete (expire in the past)
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC";

???
Easy to make mistakes with string formatting
Must manually handle flags (expires, secure, path)
Show how manual cookie handling can be cumbersome. This sets the stage for using a helper library.

---

# Introducing js-cookie

(From 14.2 js-cookie)

- Popular library for simplified cookie CRUD

* No manual string manipulation

- Basic usage:

```bash
npm install js-cookie
```

or include via a CDN script in HTML

???
Highlight js-cookie’s widespread usage, active maintenance, and minimal dependencies.

---

# Using js-cookie

```javascript
// 1. Set a cookie (with optional expiration in days)
Cookies.set("token", "abc123", { expires: 7 });

// 2. Get a cookie
const token = Cookies.get("platform"); // "abc123"

// 3. Remove a cookie
Cookies.remove(" token");
```

**Other Options**

- `path` : scope to a specific route
- `secure` : only send over HTTPS
- `samesite` : control cross-site usage

???
Show how easy it is to set or retrieve cookies with certain flags. Emphasize security flags for sensitive data.

---

# Flask Basics with Cookies

(From 14.3 Flask with Cookies)

request.cookies.get: read cookies from client
make_response(content).set_cookie(): set cookies in response

---

```prognython
from flask import Flask, request, make_response


app = Flask(__name__)

@app.route("/set-cookie")
def set_cookie():
    res = make_response("Cookie is set!")
    res.set_cookie("sampleName", "sampleValue", httponly=True, secure=True)
    return res

@app.route("/get-cookie")
def get_cookie():
    val = request.cookies.get("sampleName")
    return f"Cookie value: "{val}"

```

---

# Why it matters

HttpOnly

- Cookie is inaccessible via JavaScript
- Mitigates XSS (Cross-Site Scripting)

???
We have now a cookie that can be used by our back-end application that javascript can't get its hands on. When the user accesses our front-end in their browser, our application's back-end can utilize the browser's stored cookies to make decisions and send the appropriate information. Your back-end is the gatekeeper that decides if information should be sent to the front-end. The back-end will provide cookies and then read those cookies to provide the correct information to the front-end.

---

# Why it matters

Secure

- Only sent over HTTPS
- Mitigates intercepting the cookie over plain HTTP

???
Strongly encourage using these flags for authentication cookies. An example is that a Session Cookie should merely use these flags by default.

---

# Fetching Data with Cookies

From 14.4 Fetch with Cookies

1. Flask: `CORS(app, supports_credentials=True)`
2. Front-end: `fetch(..,  { credentials:"include" })`
3. `set_cookie(..,  secure=True, httponly=true, samesite="None")`

???
Browsers block cross-site cookies unless these requirements are met. CORS Cross-Origin Resource Sharing can control what is allowed when sharing information. Supporting and including credentials (cookies) in cross-origin requests will allow the information to pass from the BE to the FE and back to the FE and same site none allows the cookie to be sent in cross-origin requests.

---

<table data-start="1864" data-end="2665" style="font-size: 29px;"><thead data-start="1864" data-end="1908"><tr data-start="1864" data-end="1908"><th data-start="1864" data-end="1875"><strong data-start="1866" data-end="1874">Step</strong></th><th data-start="1875" data-end="1888"><strong data-start="1877" data-end="1887">Action</strong></th><th data-start="1888" data-end="1908"><strong data-start="1890" data-end="1906">What Happens</strong></th></tr></thead><tbody data-start="1953" data-end="2665"><tr data-start="1953" data-end="2082"><td>1️⃣</td><td><strong data-start="1961" data-end="1997">Frontend makes request (<code data-start="1987" data-end="1994">fetch</code>)</strong></td><td>The browser includes cookies (if any exist) because of <code data-start="2055" data-end="2079">credentials: "include"</code>.</td></tr><tr data-start="2083" data-end="2250"><td>2️⃣</td><td><strong data-start="2091" data-end="2124">CORS Preflight (if necessary)</strong></td><td>If it's a <code data-start="2137" data-end="2143">POST</code>, <code data-start="2145" data-end="2150">PUT</code>, or <code data-start="2155" data-end="2163">DELETE</code>, the browser first sends an <code data-start="2192" data-end="2201">OPTIONS</code> request. Flask CORS must handle this properly.</td></tr><tr data-start="2251" data-end="2390"><td>3️⃣</td><td><strong data-start="2259" data-end="2286">Flask Processes Request</strong></td><td>If the user has a valid session cookie (<code data-start="2329" data-end="2344">session_token</code>), Flask verifies it and returns a response.</td></tr><tr data-start="2391" data-end="2519"><td>4️⃣</td><td><strong data-start="2399" data-end="2428">Browser Receives Response</strong></td><td>If a new cookie is set (<code data-start="2455" data-end="2467">Set-Cookie</code> header), the browser stores it <strong data-start="2499" data-end="2510">only if</strong> it's:</td></tr><tr data-start="2520" data-end="2568"><td></td><td></td><td>✅ <code data-start="2528" data-end="2543">SameSite=None</code> (allows cross-origin)</td></tr><tr data-start="2569" data-end="2609"><td></td><td></td><td>✅ <code data-start="2577" data-end="2590">Secure=True</code> (must be HTTPS)</td></tr><tr data-start="2610" data-end="2665"><td></td><td></td><td>✅ <code data-start="2618" data-end="2633">HttpOnly=True</code> (prevents JavaScript access)</td></tr></tbody></table>

---

# Common Pitfalls

- Missing `credentials: "include"` -> no cookies in requests
- Not setting `supports_credentials=True` in Flask halts
  Can't access cross-site requests
- `samesite="None"` and secure=True needed for cross-site cookies
- Time zone confusion (UTC vs local)
- Overwriting cookies if same name + different path

---

# Key Takeaways:

1. Cookies store essential data on the browser.
2. js-cookie simplifies client-side cookie management
3. Flask can set & read cookies with `request.cookies` & `make_response`.
4. Cross-origin cookies require proper CORS & secure flags
5. Always use `Secure` and `HttpOnly` for sensitive information.

---

# Additional Resources

- [MDN: Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
- [js-cookie](https://www.npmjs.com/package/js-cookie)
- [Flask: Cookies](https://flask.palletsprojects.com/en/2.2.x/quickstart/#cookies)
  - 14.1 Cookies
  - 14.2 js-cookie
  - 14.3 Flask with Cookies
  - 14.4 Fetch with Cookies

???
Encourage people to check official docs or more details. Mention code repos.

---

# Thank You!
