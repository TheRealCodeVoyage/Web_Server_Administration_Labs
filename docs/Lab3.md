# Lab 3: Exploring HTTP Headers and Protocols with cURL and Browser

## Lab Setup
1.	Ensure you have a Web Browser installed (In this Lab Google Chrome has been used).
2.	Ensure you have cURL installed on your system. You can check this by running the following command in the terminal: 

```sh
curl --version
```

HTTP headers are key-value pairs that carry essential information between the client (browser or cURL) and the server. They provide metadata like content type, content length, authorization, and caching.

## Step 1: Viewing HTTP Headers Using cURL
1.	Open your terminal.
2.	Run the following cURL command to make a `GET` request to a sample website (e.g., example.com): 

    ```sh
    curl -I https://www.example.com
    ```
3.	The `-I` option tells cURL to fetch only the HTTP headers. The response will show headers such as HTTP/1.1 200 OK, Content-Type, Date, Server, etc.
4. Can you find the Content-Type of the response? What server is the website using?

## Step 2: Exploring Different HTTP Methods with cURL
1.	GET Request: Run the following command:

```sh
curl -X GET https://jsonplaceholder.typicode.com/posts/1
```

This sends a GET request to fetch the data for post ID 1.

2. POST Request: Send a POST request to the same URL, adding some data:

```sh
curl -X POST https://jsonplaceholder.typicode.com/posts -d "title=Test&body=This is a test post."
```

This simulates creating a new post.


3. PUT Request: Update the post using the following command:

```sh
curl -X PUT https://jsonplaceholder.typicode.com/posts/1 -d "title=Updated Title&body=Updated body"
```

4. DELETE Request: Delete the post by running:

```sh
curl -X DELETE https://jsonplaceholder.typicode.com/posts/1
```

#### Questions:
- What differences do you observe in the headers for each HTTP method?
- How does the server respond to a DELETE request?


## Step 3: Exploring HTTP Headers with Google Chrome DevTools
Viewing Request Headers in Chrome
1.	Open Google Chrome and navigate to https://www.example.com.
2.	Right-click on the page and select Inspect to open DevTools.
3.	Navigate to the Network tab.
4.	Reload the page to capture network activity.
5.	Click on the first request in the Name column (usually the main HTML document).
6.	In the right-hand panel, click on the Headers tab.

#### Observation:
7.	Request Headers: Examine the headers sent by the browser, such as `User-Agent`, `Accept`, `Host`, etc.
8.	Response Headers: Examine the headers sent by the server, such as `Content-Type`, `Cache-Control`, `Server`, etc.

#### Questions:
- What is the User-Agent string in the request headers?
- What type of caching mechanism is the server using (if any)?

Manipulating Headers Using Chrome DevTools
1.	In the Network tab, find the Filter box and type image to display only image-related requests.
2.	Select one of the image files from the Name column.
3.	In the Headers tab, observe the headers such as Content-Length, Content-Type, and Cache-Control.
4.	Click on Preview to view the image and its details.

#### Questions:
- What is the Content-Type of the image file?
- What is the Content-Length of the image?


## Step 4: Practical Tasks with cURL and Chrome DevTools
Checking for Redirection
1.	Use both command and compare the results using curl and using the web browser

```sh
curl -I https://httpstat.us/301
curl -IL https://httpstat.us/301
```
Observing HTTPS Handshake
1.	Use cURL to fetch detailed SSL information:

```sh
curl -v https://www.example.com
```

1.	The `-v` option gives a verbose output, including details of the HTTPS handshake.
2.	In Chrome, observe the security information:
o	Open the Security tab in DevTools.
o	Click on View Certificate to examine the SSL certificate details.

#### Questions:
- What type of SSL certificate is the site using?
- What is the TLS version displayed by cURL?


Observing Redirection in Google Chrome DevTools
1.	Open Google Chrome and navigate to a website that performs a redirection (e.g., https://httpstat.us/301).
2.	Open DevTools (Right-click -> Inspect -> Network tab).
3.	Reload the page to capture the network activity.
4.	In the Name column, select the first request (the one that initiates the redirection).
5.	In the right panel, under the Headers tab, check for the Location header to see the redirection target.
6.	Look at the Status Code in the response headers to confirm whether the server is performing a 301 Moved Permanently or a 302 Found redirection.

#### Questions:
    - What is the initial status code that indicates the redirection?
    - Does the redirection change the HTTP method (e.g., from POST to GET)?

## Step 6: Understanding and Manipulating Cookies
HTTP cookies are small pieces of data stored on the client side, usually sent by the server to maintain the session state or store user preferences.

Inspecting Cookies Using cURL
1.	Send a cURL request to a website that sets cookies in the response:

```sh
curl -I https://www.example.com
```

Look for the Set-Cookie header in the response. This header sends cookies from the server to the client.

#### Observation:
- The Set-Cookie header might contain attributes like Expires, Path, Domain, Secure, and HttpOnly.
Use the -c option to store the cookies in a file:

    ```sh
    curl -c cookies.txt https://www.example.com
    ```
    This will save the cookies sent by the server into a file named cookies.txt.
    Send a subsequent request using the saved cookies:
    
    ```sh
    curl -b cookies.txt https://www.example.com
    ```
    This uses the stored cookies in cookies.txt to maintain the session.

#### Questions:
- What cookies are set by the server?
- Do the cookies have an expiration date? If so, what is it?

Viewing and Manipulating Cookies in Chrome DevTools
- Open Google Chrome and navigate to a website that sets cookies (e.g., https://www.example.com).
- Open DevTools and go to the Application tab.
- In the left-hand panel, under the Storage section, click on Cookies to see the list of cookies set by the website.
- Select the website domain to view the cookies stored by the browser for that domain. Examine attributes like Name, Value, Domain, Path, Expires/Max-Age, and HttpOnly.

#### Observation:
- Cookies with the Secure flag can only be transmitted over HTTPS connections.
- Cookies with the HttpOnly flag are not accessible via JavaScript and are designed for security.

**Deleting and Modifying Cookies in Chrome DevTools**
1.	In the Cookies section of DevTools, right-click on any cookie and delete it.
2.	Refresh the page and observe whether the cookie is re-sent by the server.
3.	Modify the value of an existing cookie by double-clicking on its value field and entering a new value.
4.	Reload the page and check if the modified value persists.

#### Questions:
- What happens if you delete a cookie and reload the page?
- Does the server send the cookie again? Why or why not?
- How does modifying a cookie value affect the website behavior?
