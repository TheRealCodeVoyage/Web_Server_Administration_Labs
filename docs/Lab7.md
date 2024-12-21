# Lab 7: Implementation of OAuth with Express.js

## Task 1: Implement OAuth (token-based authentication mechanism)
### Part I: Set up a express.js / node.js project

#### Steps:
1)	Turn on a Linux shell (could be on your Mac, Linux OS, in Linux VM, or AWS)
In this Lab I have used Multipass as a linux VM.

![multipass launch](images/lab7-fig1.png)

Make sure your VM has Public IP:

![Find Multipass public IP](images/lab7-fig2.png)

2)	We need to add our VM IP to the host file as a new domain (wsa-lab7.com) we about to use.

![Add Virtual Host](images/lab7-fig3.png)

Host file in Windows OS: `C:\Windows\System32\Drivers\etc\hosts`
Host file in Linux/MacOs: `/etc/hosts`

3)	Enter the VM’s Terminal by:

![Launch Multipass Shell](images/lab7-fig4.png)

4)	To install NPM and Express globally, run commands below:

![Install NPM](images/lab7-fig5.png)

```sh
sudo apt install npm
sudo npm install -g express-generator
sudo express lab7-oauth
cd lab7-oauth && sudo npm install
```

5)	Turn on the project with command:

```sh
sudo npm start
```

![start NPM](images/lab7-fig6.png)
To verify: Open a browser in your Host Machine and hit: 

[http://wsa-lab7.com:3000/](http://wsa-lab7.com:3000/)

We should see this:

![Express default page](images/lab7-fig7.png)

6)	Stop the Server and Update the project (Change `app.js` content and save the file)
Press CTRL + C to stop the Express Server
![lab directory and files](images/lab7-fig9.png)
Before change:
![app.js before change](images/lab7-fig10.png)  
After change:
![app.js after change](images/lab7-fig11.png)

If you have followed the instruction and used nano as your text editor, you can use these combinations to save and exit:
`CTRL + S` to save
`CTRL + X` to exit

7)	Now we need to install ejs as a new View Engine.
 
![Install ejs](images/lab7-fig12.png)

8)	In the folder “views”, rename two files using `mv` command:
- index.jade -> index.ejs
- error.jade -> error.ejs

![rename files](images/lab7-fig13.png)

9)	Change `index.ejs` contents to:

```html
<html>
    <body>
        <%=title %>
    </body>
</html>
```

![index.ejs content](images/lab7-fig14.png) 

10)	In the folder “routes”, change `index.js` contents
Change the title from 'Express' to ' wsa-lab7'
![change title](images/lab7-fig15.png)
 

11)	After save (`CTRL + S`) and exit (`CTRL + X`), it’s time to restart the server by running:
![restart nom server](images/lab7-fig16.png)
 

12)	Verify the change in your browser with [http://wsa-lab7.com:3000](http://wsa-lab7.com:3000)

13)	Make a screenshot of your browser for demo


### Part II: Get Google dev credentials:
#### Steps:
1)	Open a browser, login with your Gmail, and then hit [https://console.developers.google.com/project](https://console.developers.google.com/project)

When you see the page below, push the button to create a new project
![GCP project page](images/lab7-fig17.png)

2)	In the next page as below, put in project name as “wsa-lab7”, and then push the “CREATE” button
![GCP new project](images/lab7-fig18.png)

3)	Wait till you see the notification as below, and then click the “SELECT PROJECT” link
![Navigate to the project](images/lab7-fig19.png)

4)	In the page below, choose “Go to APIs overview”
![Go to APIs overview](images/lab7-fig20.png)

5)	Enable APIs and services
![Enable API and Services](images/lab7-fig21.png)

6)	Use the search bar to look for “google+”, in the result list, choose “Google + API”
![Select Google+ API](images/lab7-fig22.png)
![Enable Google+ API](images/lab7-fig23.png)
7)	Search for “contacts” and from the results choose “contacts API” 
![Select Contacts API](images/lab7-fig24.png)
Enable it:
![Enable Contacts API](images/lab7-fig25.png)
 
8)	Click the Google Cloud logo to get back to main dashboard, Then from Quick access, click on APIs and Services 
![GCP Main Dashboard](images/lab7-fig26.png)
9)	In the sidebar, choose “OAuth consent screen”, then check “External”, “CREATE”
![OAuth consent screen](images/lab7-fig27.png)

10)	Fill in “App name”, “User support email” and “Developer contact info”
![OAuth consent screen details](images/lab7-fig28.png)
![Developer Contact Info](images/lab7-fig29.png)
11)	Use all default settings for the following two pages to finish the creation
12)	In the sidebar menu, choose Credentials, and then click “+CREATE CREDENTIALS”
In the dropdown, choose “OAuth client ID”
![get OAuth client ID](images/lab7-fig30.png)

13)	Choose “Web application” as the type as below
![Choose Web Application](images/lab7-fig31.png)

14)	Fill in listed info
- Name:	wsa-lab7
- For Authorized JavaScript origins
    - ADD URI:	http://wsa-lab7.com 
- For Authorized redirect URIs
    - ADD URI:	http://wsa-lab7.com:3000/auth/google/callback        
- And then click “CREATE” button

![Fill rest of the form](images/lab7-fig32.png)

15)	Now we have the Client ID and Client Secret in the popup

![OAuth Client Details](images/lab7-fig33.png)

### Part III: Install middleware passport and Google strategy
#### Steps:
1)	Open a Linux shell (Or Ctrl+C to stop the project), and then run:

![Install requirements](images/lab7-fig34.png)

```sh
sudo npm install passport express-session passport-google-oauth20 --save
```

2)	In `app.js`, around line #10 in the importing section, add codes below. 

Note: if you use `nano`, you use `nano -l` to display line numbers. 

![Nano line number](images/lab7-fig35.png)
 
```js
var passport = require('passport');
var session = require('express-session');
var GoogleStrategy = require('passport-google-oauth20').Strategy;
passport.use(new GoogleStrategy({
    clientID: '', //you get this from Google, refer to your Part II
    clientSecret: '', //you get this from Google, refer to your Part II
    callbackURL: 'http://wsa-lab7.com:3000/auth/google/callback'},

  function(accessToken, refreshToken, profile, cb) {
cb(null, profile);
  }
));
```

![code change screenshot](images/lab7-fig36.png)
 

Around line #27, right after express app has been defined, add:

```js
app.use(session({secret: 'cat'}));
app.use(passport.initialize());
app.use(passport.session());
passport.serializeUser(function(user, done) {
    done(null, user);
});
passport.deserializeUser(function(user, done) {
    done(null, user);
});
```

![express app modification](images/lab7-fig37.png)

3)	Save the file (`CTRL + S`) and (`CTRL + X`) get back to the shell.
4)	We also need to setup our custom domain in host file in our VM. The multipass VM also need to know what IP does wsa-lab7.com refers to!

![change hosts file](images/lab7-fig38.png)

### Part IV: Set up Auth Router and create the view
#### Steps:
1)	In file `app.js` Under other routes related imports we need to add the auth.js file which we will create in the next step.

Around Line #9, in the importing section:

```js
var auth = require ('./routes/auth');
```

![import auth](images/lab7-fig39.png)

around Line #39, where rest of the views are being defined for express app:

```js
app.use('/auth', auth);
```

![add auth to express app](images/lab7-fig40.png)

Don’t Forget: Save and Exit.

2)	In “routes” folder, create a file called `auth.js`
3)	Put contents below into the `auth.js` file

```js
var express = require('express');
var passport = require('passport');
var router = express.Router();

router.route('/google/callback').get(
  passport.authenticate('google', { failureRedirect: '/error/' }),
  function(req, res) {
    res.redirect('/');
  });

router.route('/google').get(
    passport.authenticate('google', {
        scope:['https://www.googleapis.com/auth/userinfo.profile', 
                'https://www.googleapis.com/auth/userinfo.email']
}));

module.exports = router;
```

![add oauth routes](images/lab7-fig41.png)

Save and Exit.

4)	In directory/folder named views, create a file named `users.ejs`

```html
<html>
    <head></head>
    <body>
        <div>
            Hi <%=username%>
        </div>
</body>
</html> 
```

![change users.ejs](images/lab7-fig42.png)
    
Save and Exit.


5)	Change `index.js` contents to: Note: you can find it in your routes directory.

```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  if(req.user && req.user.displayName){
    const displayName = req.user.displayName;
    req.user.displayName = null;
    res.render('users', { username: displayName });
  } else{
    res.render('index', { title: 'wsa-lab7', salut:'please log in' });
  }
});

module.exports = router;
```

![home page route](images/lab7-fig43.png)

6)	Change `index.ejs` contents to:

```html
<html><body>  
<%=title %><br/>
<%=salut%>
<ul>
  <li>
    <a href = "/auth/google" >
      Google
    </a>
  </li>
</ul>
</body></html>
```

![login page](images/lab7-fig44.png)
 
7)	 Restart the app, and then verify it in a browser.



