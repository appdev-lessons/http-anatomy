# Anatomy of an HTTP request

[Here is a video for this lesson](https://share.descript.com/view/jql3w76ls7h). You should not rely entirely on the video. PLEASE READ the below lesson as you are going through the steps, since there is much more detail in the text than in the video.

## The simplest possible HTTP request

The fundamental unit of a web application is the **HTTP request**. Any time you type a URL into your browser and press <kbd>return</kbd>, or click on a link, or submit a form, you are placing an HTTP request (or just "request", for short).

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1688554262/request-response_i0gzs7.png)
{: .bleed-full }

- The word "**client**" refers to the program that _sends_ the HTTP request.

  "Client" is also often used to refer to the computer that the program is running on.
- The word "**server**" refers to the program that _receives_ the HTTP request, and sends back an **HTTP response** (or just "response", for short).

    "Server" is also often used to refer to the computer that the program is running on.
- These two computers are able to talk to each other no matter where they are in the world because they are part of a giant network of interconnected computers: "the internet".

An HTTP request is a plain text file that looks like this:

```http
GET /wiki/Chicago HTTP/1.1
Host: en.wikipedia.org
```

- The first line is called the **request line**. It has three parts:
	- The **verb** — in this example, `GET`. The verb indicates what the client is trying to _do_ — read some information (`GET`), create some information (`POST`), update some information (`PATCH`), or delete some information (`DELETE`). (There are a few other HTTP verbs, but these four cover 99.99% of our needs.)
	- The **resource path** (or  just "path", for short) — in this example, `/wiki/Chicago`.
	- The **HTTP version** — in this example, `HTTP/1.1`. (This is the predominant version and we don't need to worry about any others right now.)
- Starting on the second line are a list of key-value pairs called **headers**. The `Host` header is required: it specifies the address of the server that we're sending the request to — in this example, `en.wikipedia.org`.
		
If you send this request, it will find its way to Wikipedia's server through a system known as the Domain Name System (DNS). Wikipedia will look at the path, and based on that decide what to send back as the response. The response is also a plain text file — in this example, it would look something like this:

<aside markdown="1">
We're not going to delve into the workings of DNS for now; we'll just trust that it works. [If you're curious, though, you can read more about DNS here.](https://www.cloudflare.com/learning/dns/what-is-dns/).
</aside>

```http
HTTP/1.1 200 OK
Content-Type: text/html

<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Chicago - Wikipedia</title>

  <!-- Hundreds more lines of HTML describing Chicago -->
```

- The first line of the response is the **status line**.  It has three parts:
    - The HTTP version — in this example, `HTTP/1.1`.
    - A **status code** — in this example, `200`. Other common status codes are `404` (resource not found), `500` (internal server error), `403` (don't have permission to access), `302` ("redirect", or "keep on moving to a different URL"), etc.
    - A **reason phrase** that gives a short textual description of the status code — in this example, `OK`. 
- Starting on the second line are a list of key-value pairs called **headers**, just as there were in the request. The `Content-Type` header should be included to let the client know what kind of document is being returned — in this example, text that is HTML.
- After the headers comes a blank line.
- Everything after the blank line is the **body** of the response. In this example, the source code for a whole HTML document.

Voilà! A complete HTTP **request** and **response** cycle — it turns out that both are just plain text files sent back and forth!

If the body of the response contains HTML source code, and if the client happens to know how to interpret it (like a browser does), it will render it in a human friendly way:

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1688454683/wikipedia-chicago_zxh58m.jpg)

Here, [you try it](https://en.wikipedia.org/wiki/Chicago)!

- Select all that are true:
- `GET` is an HTTP "verb".
  - Yes!
- An HTTP response contains a body with the requested page's HTML.
  - Yes!
- We send HTTP responses when we visit a URL in our browser
  - Not quite. Re-read above.
- An example resource path is `www.google.com`
  - Not quite. Re-read above.
- The request line in an HTTP request contains a verb, the resource path, and the HTTP version
  - Yes!
- The request line in an HTTP request contains a verb, the resource path, and the HTML body
  - Not quite. Re-read above.
{: .choose_all #http_quiz title="HTTP quiz" points="3" answer="[1,2,5]" }

## Placing requests with a browser

So: when we type a URL (Uniform Resource Locator, also known as Uniform Resource Identifier — URI) like `https://rubyweekly.com/issues/660` into a browser's address bar and press <kbd>return</kbd>, or click on an `<a>` tag, what all is the browser doing?

First, the browser parses the URL into parts:

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1688556191/anatomy-of-url_arrpms.png)

- The browser starts on the left and looks for `://`. Everything before that is the **scheme**.
 
  The scheme can either be `http` or `https`. With `http`, the request is sent from client to server in plain text. That means all the computers along the way could, in theory, read the contents of the request; which isn't very secure.

  With `https`, the request is first encrypted by the client and then decrypted by the server upon arrival, so the computers along the way can't read the contents (except for the `Host` header, which is required to send the request along to the right place). Nowadays, we should almost always be using `https`.
- After the `://` up until the first slash (`/`) it encounters is the **host**.
- Everything from the first `/` onwards is the **resource path**.

<aside markdown="1">
There are other schemes, like `ftp` (for transferring files), `ssh` (for securely connecting to the command line on a remote computer), and `ws` (WebSockets, for two way real-time communication between client and server). We don't need to worry about those for now.
</aside>

With the host and the resource path, the browser can then assemble an HTTP request file:

```http
GET /issues/660 HTTP/1.1
Host: rubyweekly.com
```

Since we used `https` rather than `http`, the browser will encrypt the request and send it along. It then replaces the contents of the viewport with the HTML from the response (if the request was successful).

But wait — how did the browser know the **verb** should be `GET`?

It turns out that the address bars of browsers can _only_ place `GET` requests. Similarly, when a user clicks on an `<a>` link, the browser assembles a `GET` request out of the value of the `href` attribute, sends it, and replaces the contents of the viewport with the HTML from the body of the response.

This makes sense because we're usually trying to _read_ information when we're navigating around the web by clicking on links. But eventually we'll want to make `POST`, `PATCH`, and `DELETE` requests as well (to _create_, _update_, and _delete_, respectively).

To make these other kinds of requests, we'll have to use other clients besides the browser's address bar and `<a>` tags.

- The URL bar in a browser can place... 
- HTTP `GET` requests.
  - Yes!
- HTTP `POST` requests.
  - Not quite.
- Any request.
  - Not quite.
{: .choose_best #get_request title="Browsers can place..." points="1" answer="1" }

- What is a `PATCH` request used for?
- Creating information.
  - Not quite. That's `POST`.
- Reading information.
  - Not quite. That's `GET`.
- Updating information.
  - Yes!
- Deleting information.
  - Not quite. That's `DELETE`.
{: .choose_best #patch_request title="A PATCH request is..." points="1" answer="3" }

## Placing requests with Ruby

Ruby has many handy libraries for placing HTTP requests. I like the [http.rb gem](https://github.com/httprb/http). Let's see how it works:

```ruby
# Pull in the HTTP class
require "http"

# Place the request and store the return value in x
x = HTTP.get("https://en.wikipedia.org/wiki/Chicago")

# Print the body of the response
puts x.to_s
```
{: .repl #http_rb_1 title="http gem" points="1"}

- The http.rb gem includes a class, `HTTP`, that has a class method `get` (and `post`, `patch`, `delete`, etc, for the other verbs).
- The argument to `get()` should be a `String` containing the full URL of the resource you want to request.
- The `get` method returns an object that represents the HTTP response.
- The `HTTP::Response` object has a method called `to_s` which will return the contents of the body portion of the response as a `String`.

The `HTTP::Response` object also has methods that allow us to inspect the `headers`, status `code`, etc. But we're mostly interested in the contents of the body, so `to_s` is what we'll use most often.

The ability to place HTTP requests with Ruby is _incredibly_ powerful. It allows our programs to interact with the outside world.

Now that we know how to _send_ HTTP requests with Ruby, it's time to learn how to _receive_ HTTP requests and send back responses — in other words, how to build a web app. That's next.

---
