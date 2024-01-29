# Webapp in Flask
#### Video Demo:  <URL HERE>
#### Description: 

> Content on this website made in flask: register account, hashed passwords, login/logout, sessions, admin user, create posts, delete posts, edit posts, bootstrap menu, footer and buttons. On index page, posts are shown in reversed order. Only admin user and post creator can delete/edit post. Javascript for menu link to change color of background. 

1. First thing to do was basic index.html page, layout.html and app.py setup with imported Flask module and initialization. Static folder was used for bootstrap sticky footer css.
```
app = Flask(__name__, static_folder='static')
app.config["TEMPLATES_AUTO_RELOAD"] = True
```
2. Next step is to build database with two tables, users and posts. Module from cs50 import SQL was used. Table posts, user_id column was foreign key of table users, id column. Users table admin column was added later, so during build process databases were dropped and rebuilt. Admin functionality was used because in that time of project building it seemed easier to allow delete functionality for users with specific role, than to detect user who is also post creator.
```
CREATE TABLE users (id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, username TEXT NOT NULL, hash TEXT NOT NULL);
CREATE TABLE posts (id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, user_id INTEGER NOT NULL, title TEXT NOT NULL, post TEXT NOT NULL, time DATE, admin INTEGER DEFAULT 0, FOREIGN KEY (user_id) REFERENCES users (id));
```
3. With database set we can build first webapp functionality - register users. Registration form take username, password and password confirmation fields. Users cant register without username or password. Before saving username and password to database, password is hashed with werkzeug.security module: generate_password_hash.
```
hashed_password = generate_password_hash(password, method='pbkdf2', salt_length=16)
```
4. Login user functionality was added next and for that we used Flask module session. If login form is mission username or password it will return warning message. Pasword from log in form was checked against hashed hashword from database with check_password_hash function. Module from flask_login import current_user was used to help with managing logged users object, for example to call current_user.id, .
5. After login, logout route was added with session.clear().
6. Next step was to implement route for posting new post, with post.html and layout.html. Layout.html is built with bootstrap navbar and sticky footer. Also in layout.html different menu content is served depending if user is logged in or not. Logged in users see additional content like their username and link to Post and Logout, while users that are not logged in see Login and Register links. Post.html was simple form with two input elements "title" and "post". Module from datetime import datetime was used to track post creation time. After submitting, post is saved in posts table. All posts are displayed on index page in reversed order so that newest are on top. On index.html page "for" loop was used to iterate over all posts and display them, with additional information like post creator username and posting datetime.
7. Admin user functionality is added.
```
def is_admin(user_id):    
    result = db.execute("SELECT admin FROM posts WHERE id = :user_id", user_id=user_id)
    return result[0]['admin'] == 1 if result else False
```
8. Delete post functionality is added for admins and post creators.
```
@app.route("/delete/<int:post_id>", methods=["POST"])
```
9. Edit post functionality is added for admins and post creator. After clicking on edit post, edit form is filled with content of post. After saving edited post, new changes are updated for that post in database.
```
@app.route("/edit/<int:post_id>", methods=["GET", "POST"])
```
10. Login required function is added @login_required, which is stored in helpers.py, and impored in app.py with from helpers import login_required. @login_required function is used for create post page so that non logged users cant hardcode /post link to create new posts. 
```
from flask import session, redirect, url_for
def login_required(f):
    def wrapper(*args, **kwargs):
        if session.get("user_id") is None:
            return redirect("/login")
        return f(*args, **kwargs)
    return wrapper
```
11. Finally small javascript code is added to the page, so that when user click on link "JS Change color", background color is changed, and returned to default value if clicked on link again. JS script is added to layout page and it is available for all users on all pages.
```
<script>
        // Function to change background color to green
        function changeBackgroundColorToGreen(event) {
            event.preventDefault();  // Prevent default link behavior
            var currentColor = document.body.style.backgroundColor;
            if (currentColor === "grey") {
                document.body.style.backgroundColor = ""; // Set to default color
            } else {
                document.body.style.backgroundColor = "grey";
            }
        }
        // Click event listener to the link
        document.getElementById("changeColorLink").addEventListener("click", changeBackgroundColorToGreen);
```

### Webapp in Flask content
- **[Project Github repository](https://github.com/trndav/cs50/tree/main/final_project)**
- **Register account**
- **Hashed passwords**
- **Login/logout**
- **Sessions**
- **Different menu content for users**
- **Admin user**
- **Create posts**
- **Delete posts**
- **Edit posts**
- **Bootstrap menu, footer and buttons**
- **Javascript for menu link to change color of background**