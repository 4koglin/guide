## Getting Started
Follow this 5 minute quickstart, to setup a new web project with Baqend. We will build a simple message wall.

 <ol class="getting-started-list">
<li>
###Create a Baqend account
To build a new application, first [create a Baqend account](http://dashboard.baqend.com/). Type in a name for your new application and a dedicated server instance will be deployed and hooked up to the caching infrastructure (typically in 20 seconds).
</li>
<li>
###Download Boilerplate Web Project
We'll start with [this empty HTML5 Bootstrap project](http://www.initializr.com/builder?boot-hero&html5shiv&simplehtmltag&izr-emptyscript&boot-css). Unzip it, to get the following folder structure:
<pre>
**index.html**          <-- here we will put some HTML to accept input data
js
    **main.js**         <-- our application logic hooked up to Baqend
    vendor          <-- JavaScript libraries
css
    main.css        <-- our style
    bootstrap.css   <-- [Bootstrap](http://getbootstrap.com/) style
img, fonts          <-- assets
</pre>
</li>
<li>
###Install Baqend
To install Baqend to the application, just add the CDN-hosted Baqend SDK in the `<head>` section of the index.html:
```html
<script src="http://www.baqend.com/js-sdk/latest/baqend.min.js"></script>
```
Other installation methods (e.g. npm) are explained [on Github](https://github.com/Baqend/js-sdk/blob/master/README.md).
</li>
<li>
###Connect to the Cloud
In the `main.js`, add the following lines to connect to Baqend Cloud:
```js
DB.connect("<your-app-name>", function() {
	showMessages();
});
```
The callback is invoked when the connection is established. We will use that to display the message wall in `showMessages`, but first we need some data.
</li>
<li>
###Define the Data Model
In the [dashboard](https://dashboard.baqend.com/apps) enter your App and create a new table named `Message` in the **Tables** menu on the left. In the schema tab that is now open, give it three properties:
<table class="table">
<tr><th>Attribute Name</th><th>Type</th></tr>
<tr><td>name</td><td>String</td></tr>
<tr><td>message</td><td>String</td></tr>
<tr><td>date</td><td>DateTime</td></tr>
</table>
Now go to the **Data** tab and click **Add** to insert a dummy message to the database.

To learn more about data modeling in Baqend, see the [Schema and Types documentation](/#schema-and-types).
</li>
<li>
###Save Data
Now, let's enhance the `index.html` with an input for name and message as well as post button. Replace all the code starting before `<div class="jumbotron">` just until the `<hr>` with this:
```html
<div class="jumbotron">
  <form onsubmit="leaveMessage(this.name.value, this.message.value);
  this.reset(); return false;" class="form-inline text-center container">
  <input class="form-control" name="name" placeholder="Name">
  <input class="form-control" name="message" placeholder="Message">
  <button type="submit" class="btn btn-primary">Leave Message</button>
</form>
</div>
<div class="container"><div class="row" id="messages"></div></div>
```
So when hitting enter or the button, our `leaveMessage` function is called. Let's add it to `main.js`:
```js
function leaveMessage(name, message) {
    //Create new message object
	var msg = new DB.Message();
	//Set the properties
	msg.name = name;
	msg.message = message;
	msg.date = new Date();
	//Insert it to the database
	msg.insert().then(showMessages);
}
```
So now we insert a new message, whenever the HTML form is submitted.

See the [**C**reate **R**ead **U**pdate **D**elete documentation](/#crud) to learn more about saving and loading data.
</li>
<li>
###Query Data
Now, let's display the stored data. To show the 30 newest messages, ordered by time stamp perform a simple query:
```js
function showMessages() {
  DB.Message.find()
    .descending("date")
    .limit(30)
    .resultList()
    .then(function(result) {
        var html = "";
        result.forEach(function(msg) {
            html += '<div class="col-md-4"><h2>';
            html += msg.name + '</h2><p>' + msg.message + '</p></div>';
        });
        document.getElementById("messages").innerHTML = html;
    });
}
```
At this point the application is fully working. Just open the `index.html` in the browser to use your app. If something is not working, press `F12` to see any error messages.

Queries allow you do complex filtering and sorting, see the [Query Docs](/#queris). All data loaded from Baqend Cloud is served with low latency from a global CDN.
</li>
<li>
###Protect Your Data
By default, public access to the `Message` table is allowed. Let's restrict that to only allow *inserts*, *reads* and *queries* but disallow any *updates* and *deletes*. Go to the **ACL** (Access Control Lists) tab in the dashboard and revoke delete and update rights from the Public role.

Access rights can be granted and denied both at table level and at object level. This explained in detail in the [User, Roles and Permissions documentation](/#users-roles-and-permissions).

Baqend has full SSL support. If you want the Baqend connection to be SSL-encrypted by default, add `true` as the second parameter of the `DB.connect` call.
</li>
<li>
###Start Building
Use the app you just built as a baseline and learn about other features:
<ul>
    <li>Bla</li>
    <li>If your starting from scratch, have a look at frontend bootstraping tools: [Initializr](http://www.initializr.com/) (used here), [HTML5 Boilerplate](https://html5boilerplate.com/), [Bootstrap](http://getbootstrap.com/), [Yeoman](http://yeoman.io/)</li>
</ul>
</li>
</ol>

<style>
.getting-started-list {
    list-style: none;
    counter-reset: cnt;
    margin-left: 0;
    margin-top: 40px;
}

.getting-started-list h3 {
    //margin-top: -110px;
}

.getting-started-list>li {
    position: relative;
    border-left: 2px solid #1967CC;
    padding: 0 0 60px 50px;
    /* disable collapsed margin */
    display: inline-block;
    width: 100%;
}

.getting-started-list>li:last-child {
    border: none;
}

.getting-started-list>li:before {
    counter-increment: cnt;
    content: counter(cnt);
    position: absolute;
    left: -18px;
    border-radius: 50%;
    background-color: #FFFFFF;
    display: block;
    width: 35px;
    height: 35px;
    line-height: 31px;
    color: #1967CC;
    border: 2px solid #1967CC;
    text-align: center;
    font-size: 21px;
}
</style>