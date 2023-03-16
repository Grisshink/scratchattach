Scratch API wrapper with support for almost all site features

This library can set cloud variables, follow Scratchers, post comments and do so much more! It has special features that make it easy to transmit data through cloud variables.

**Some functions require logging in to Scratch.**

**You also need to be familiar with python and pip to use this library.**

The project is maintained by Grisshink: 
- https://scratch.mit.edu/users/ttt999/
- https://scratch.mit.edu/users/Grisshink/

[![PyPI status](https://img.shields.io/pypi/status/scratchattach.svg)](https://pypi.python.org/pypi/scratchattach/)
[![PyPI download month](https://img.shields.io/pypi/dm/scratchattach.svg)](https://pypi.python.org/pypi/scratchattach/)
[![PyPI version shields.io](https://img.shields.io/pypi/v/scratchattach.svg)](https://pypi.python.org/pypi/scratchattach/)
[![GitHub license](https://badgen.net/github/license/TimMcCool/scratchattach)](https://github.com/TimMcCool/scratchattach/blob/master/LICENSE)

# Installation

Run the following command in your command prompt / shell:
```
pip install -U scratchattach
```
This will also update your exising library to the newest version.

# Logging in  `scratch3.Session`

**Logging in with username / password:**

```python
import scratchattach as scratch3

session = scratch3.login("username", "password")
```

`login()` returns a `Session` object that saves your login

**Logging in with a sessionId:**
*You can get your session id from your browser's cookies. [More info](https://github.com/TimMcCool/scratchattach/#get-your-session-id)*

```python
import scratchattach as scratch3

session = scratch3.Session("sessionId", username="username")
```

**Attributes:**
```python
session.session_id # Returns the associated session id
session.xtoken # Returned after getting session id
session.email # Returns the email address associated with the account
session.new_scratcher # Returns True if the associated account is a New Scratcher
session.mute_status # Documentation needed
session.banned # Returns True if the associated account is banned
```

# Cloud variables  `scratch3.CloudConnection`

**Connect to the Scratch cloud:**

```python
conn = session.connect_cloud("project_id")

# You can also get cloud connection with session id and username, but the first variant is more preferred way 
conn = scratch3.CloudConnection(project_id = "project_id", username="username", session_id="sessionId")
```

This will open connection to clouddata websocket of specified project, which can be optionally closed with `disconnect()` method.

**Connect to the TurboWarp cloud:**

Does not require a session.

```python
conn = scratch3.TwCloudConnection(project_id = "project_id", username="username")  
# Optional argument: cloud_host="wss://clouddata.turbowarp.org"
# To connect to forkphorus's cloud server, use cloud_host="wss://stratus.turbowarp.org"
```

**Working with cloud:**

New Scratchers can set Scratch cloud variables too.

```python
conn.set_var("variable", "value") # Note: the variable name is specified without the cloud emoji

value = conn.get_var("variable")
```

**Miscellaneous methods:**

Note: These methods does not require a connection / session.

```python
# Gets cloud variable value directly from clouddata logs. Can be inaccurate, because of clouddata logs limits
value = scratch3.get_var("project_id", "variable")

# Gets all cloud variable values from clouddata logs and stores them in a dict. Can be inaccurate, because of clouddata logs limits
variables = scratch3.get_cloud("project_id")

# Returns the cloud logs as list (100 entries max)
logs = scratch3.get_cloud_logs("project_id")
```

# Encoding / Decoding  `scratch3.Encoding`

Scratchattach has a built in encoder. Scratch sprite to decode texts encoded with scratchattach: https://scratch3-assets.1tim.repl.co/Encoder.sprite3

```python
from scratchattach import Encoding

Encoding.encode("input") # will return the encoded text
Encoding.decode("encoded") # will decode an encoded text
```

# Cloud events  `scratch3.CloudEvents`

*Cloud events allow reacting to cloud events in real time. If a Scratcher
sets / creates / deletes a cloud var on the given project, an
event will be called.*

They do not require a session.

**Example:**

```python
import scratchattach as scratch3

events = scratch3.CloudEvents("project_id")

@events.event
def on_set(event): # Called when a cloud var is set
    print(f"{event.user} set the variable {event.var} to the value {event.value} at {event.timestamp}")

@events.event
def on_del(event):
    print(f"{event.user} deleted variable {event.var}")

@events.event
def on_create(event):
    print(f"{event.user} created variable {event.var}")

@events.event # Called when the event listener is ready
def on_ready():
   print("Event listener ready!")

events.start()
```

**Using events in TurboWarp:** (new in v0.4.7)

```python
import scratchattach as scratch3

events = scratch3.TwCloudEvents("project_id")

...
```

**Functions:**
```python
# Starts cloud events. Needs to be called after function definitions
events.start(thread=True)
# Pauses cloud events. Can be resumed with resume() method
events.pause()
# Resumes cloud events
events.resume()
# Stops cloud events. Cannot be resumed after calling this method
events.stop()
```

# Cloud Requests  `scratch3.CloudRequests`

*Docs rework wip*

Cloud Requests Framework (inspired by discord.py) that allows Scratch projects and Python to interact

*This makes it possible to access data like message counts, user stats and more from Scratch projects! Uses cloud variables to transmit data.*

**[Cloud Requests are documented on this page:](https://github.com/TimMcCool/scratchattach/wiki/Cloud-Requests)**

[https://github.com/TimMcCool/scratchattach/blob/main/CLOUD_REQUESTS.md](https://github.com/TimMcCool/scratchattach/wiki/Cloud-Requests)

If you want to access external information in Scratch projects or store data on an external database, scratchattach's Cloud Requests are ideal for your project:
- Similar to cloud events, but send back data to the project
- Automatically encode / decode sent data
- Tons of extra features

# Users  `scratch3.User`

**Getting a user:**
```python
user = session.connect_user("username")

# You can also get a user directly from scratch, but it's more limited in functionality
user = scratch3.get_user("username")

# This will get your profile as user object
user = session.get_linked_user()
```

**Attributes:**
```python
user.join_date
user.about_me # Returns the user's 'About me' section
user.wiwo # Returns the user's 'What I'm working on' section
user.country # Returns the country from the user profile
user.icon_url # Returns the link to the user's pfp (90x90)
user.id # Returns the id of the user
user.scratchteam # Retuns True if the user is in the Scratch team
# ----- ----- #
user.update() # Updates the above data
```

**Methods:**
```python
user.message_count() # Returns current message count of user
user.featured_data() # Returns info on the user's featured project as dict

user.follower_count() # Returns the user's exact follower count
user.following_count() # Returns the amount of users this user is following
user.project_count() # Returns the user's project count
user.favorites_count() # Returns the amount of projects the user has favorited
user.studio_count() # Returns the amount of studios the user is curating
user.studio_following_count() # Returns the amount of studios this user is following

user.followers(limit=40, offset=0) # Returns the followers as list of user objects
user.following(limit=40, offset=0) # Returns the people the user is following as list of user objects
user.projects(limit=None, offset=0) # Returns the projects the user has shared as list of project objects
user.favorites(limit=None, offset=0) # Returns the projects the user has favorited as list of project objects
user.studios(limit=None, offset=0) # Returns the studios the user is curating as list of dicts

user.viewed_projects(limit=24, offset=0) # To use this you need to be logged in as the user. Returns the projects the user has recently viewed as list of project objects
user.activity_html(limit=1000) # Returns the user's activity as HTML document

user.follow() # Follows this user
user.unfollow() # Unfollows this user
user.is_following("username") # Returns True if user is following the specified Scratcher
user.is_followed_by("username") # Returns True if user is followed by the specified Scratcher

user.comments(limit=20, page=1) # Returns the user's profile comments
user.post_comment("comment content", parent_id="", commentee_id="") # Posts a comment on the user's profile. Requires logging in. Returns the info of the posted commented.
user.reply_comment("comment content", parent_id="parent_id") # Replies to a specified profile comment. Requires logging in. Returns the info of the posted commented.
user.delete_comment(comment_id="comment_id") # Deletes specified comment
user.report_comment(comment_id="comment_id") # Reports specified comment

user.toggle_commenting() # Toggles commenting in the specified user
user.set_bio(text) # Changes the 'About me' section of the user
user.set_wiwo(text) # Changes the 'What I'm working on' section of the user
user.set_featured("project_id", label="") # Changes the featured project

user.stats() # Returns the user's statistics as dict. Fetched from ScratchDB
user.ranks() # Returns the user's ranks as dict. Fetched from ScratchDB
user.followers_over_time(segment=1, range=30) # Fetched from ScratchDB

user.forum_posts(page=0, order="newest") # Returns a list of forum post objects. Fetched from ScratchDB
user.forum_counts() # Returns the amount of posts a user has written different forums as dict. Fetched from ScratchDB
user.forum_counts_over_time() # Fetched from ScratchDB
user.forum_signature() # Fetched from ScratchDB
user.forum_signature_history() # A change log for the user's forum history. Fetched from ScratchDB

user.ocular_status() # Returns information about the user's ocular status, like the status text, the color, and the time of the last update.
```

# Projects  `scratch3.Project`

**Getting a project:**
```python
project = session.connect_project("project_id")

# You can also get a project directly from scratch, but it's more limited in functionality and also you cannot get any unshared projects out of this method
project = scratch3.get_project("project_id")
```

**Attributes:**
```python
project.id  # Returns the project id
project.url  # Returns the project url
project.author  # Returns the username of the author
project.comments_allowed  # Returns True if comments are enabled
project.title # Returns the project title
project.instructions # Returns the 'Instructions' section
project.notes  # Returns the 'Notes and Credits' section
project.created  # Returns the date of the project creation
project.last_modified  # Returns the date when the project was modified the last time
project.share_date # Returns the date when the project was shared on scratch
project.thumbnail_url # Returns a link to the thumbnail of the project
project.remix_parent # If the project is a remix of another project, returns an id of that project
project.remix_root # If the project is a remix of another project, returns an id of the root project in a remix tree
project.loves  # Returns the love count
project.favorites # Returns the project's favorite count
project.remix_count  # Returns the number of remixes
project.views  # Returns the view count
project.project_token # Returns the project token. As of recent changes in scratch API, you cannont access any unshared projects without this token
# ----- ----- #
project.update()  # Updates the above data
```

**Methods:**
```python
project.get_author()  # Returns the author as user object
project.ranks()  # Returns the project's ranks. Fetched from ScratchDB
project.moderation_status() # Returns the project's moderation status (either "safe" or "notsafe" (nfe)). Fetched from jeffalo.net

project.comments(limit=40, offset=0)  # Fetches all project comments except for comment replies
project.get_comment_replies(comment_id="comment_id", limit=40, offset=0)  # Fetches the replies to a specific comment
project.post_comment(content="comment content", parent_id="", commentee_id="")  # Returns the info of the posted commented.
project.reply_comment(content="comment content", parent_id="parent_id")  # Returns the info of the posted commented.
project.delete_comment(comment_id="comment_id") # Deletes specified comment. Requires session
project.report_comment(comment_id="comment_id") # Reports specified comment. Requires session

project.love() # Flags the project as loved, which increases love count of the project by 1
project.unlove() # Same as the previous method, but in reverse
project.favorite() # Flags the project as favorited, which increases favorite count of the project by 1
project.unfavorite() # Same as the previous method, but in reverse
project.post_view() # Flags the project as viewed, which increases view count of the project by 1

project.set_title("new title") # Sets title of the project to specified value. Requires session
project.set_instructions("new instructions") # Sets "Instructions" section of the project to specified value. Requires session
project.set_notes("new notes and credits")  # Sets "Notes and credits" section of the project to specified value. Requires session
project.set_thumbnail(file="filename.png") # Sets the project thumbnail. Filename must have a .png extension and fit Scratch's thumbnail guidelines
project.share() # Shares the project. Requires session
project.unshare() # Shares the project. Requires session

# These methods are self explanatory. Note that all of them requie session to work
project.turn_off_commenting()
project.turn_on_commenting()
project.toggle_commenting()

project.remixes(limit=40, offset=0) # Returns the remixes as list of project objects
project.studios(limit=40, offset=0) # Returns the studios the project is in as list of dicts (Raw scratch API response)

project.download(filename="project_name.sb3", dir="") # Downloads the project to your computer. The downloaded file will only work in the online editor.

project.get_raw_json() # Does not work. Don't use
project.get_creator_agent() # Does not work. Don't use
```

# Unshared projects  `scratch3.PartialProject`

When connecting / getting a project that you can't access, a `PartialProject` object is returned instead.

**Most attributes and most functions don't work for such projects. However, these still work:**
```python
project.remixes(limit=40, offset=0)

project.download(filename="project_name.sb3", dir="/")
```

# Studios  `scratch3.Studio`

**Getting a studio:**
```python
studio = session.connect_studio("studio_id")

# You can also get a studio directly from scratch, but it's more limited in functionality
studio = scratch3.get_studio("studio_id")
```

**Attributes:**
```python
studio.id # Returns the studio id
studio.title # Returns the studio title
studio.description # Returns the studio 'Description' section
studio.host_id # Returns the user id of the studio host
studio.open_to_all # Returns true whether everyone is allowed to add projects
studio.comments_allowed # Returns true whether everyone can comment in the studio
studio.image_url # Returns the url to the thumbnail of the studio
studio.created # Returns the date this studio was created
studio.modified # Returns the date this studio was modified
studio.follower_count # Returns the studio follower count
studio.manager_count # Returns the studio manager count
studio.project_count # Returns the studio project count. Can be inaccurate
# ----- ----- #
studio.update()  # Updates the above data
```

**Methods:**
```python
studio.follow() # Follows the studio. Requires session
studio.unfollow() # Unfollows the studio. Requires session

studio.comments(limit=40, offset=0)  # Fetches all project comments except for comment replies
studio.get_comment_replies(comment_id="comment_id", limit=40, offset=0)  # Fetches the replies to a specific comment
studio.post_comment(content="comment content", parent_id="", commentee_id="")  # Returns the info of the posted commented.
studio.reply_comment(content="comment content", parent_id="parent_id")  # Returns the info of the posted commented.

studio.add_project("project_id") # Adds specified project to the studio. Requires session
studio.remove_project("project_id") # Removes specified project from the studio. Requires session

studio.set_description("new description") # Sets the studio 'Description' section. Requires session
studio.set_title("new title") # Sets the studio title. Requires session
studio.open_projects() # Allows everyone to add projects
studio.close_projects() # Same as previous method, but in reverse

# These methods are self explanatory. Note that all of them requie session to work
studio.turn_off_commenting()
studio.turn_on_commenting()
studio.toggle_commenting()

studio.invite_curator("username") # Invites specified user to the studio. Requires session
studio.promote_curator("username") # Promotes specified user. Requires session
studio.remove_curator("username") # Removes specified curator from the studio. Requires session
studio.leave() # Removes yourself from the studio. Requires session

studio.projects(limit=40, offset=0) # Returns projects from the studio as raw scratch API response
studio.curators(limit=24, offset=0) # Returns the curators as list of user objects
studio.managers(limit=24, offset=0) # Returns the managers as list of user objects
```

# Search / Explore page

**Search:**
```python
# Returns scratch's search results as a list of project objects
session.search_projects(query="query", mode="trending", language="en", limit=40, offset=0)
scratch3.search_projects(query="query", mode="trending", language="en", limit=40, offset=0)

# Returns scratch's search results as raw scratch API response
scratch3.search_studios(query="query", mode="trending", language="en", limit=40, offset=0)

# Returns forum search results as a list of forum post objects. Fetched from ScratchDB
session.search_posts(query="query", order="newest", page=0)
scratch3.search_posts(query="query", order="newest", page=0)
```

**Explore page:**
```python
# Returns scratch's explore page as a list of project objects
session.explore_projects(query="*", mode="trending", language="en", limit=40, offset=0)
scratch3.explore_projects(query="*", mode="trending", language="en", limit=40, offset=0)

# Returns scratch's explore page as raw scratch API response
scratch3.explore_studios(query="*", mode="trending", language="en", limit=40, offset=0)
```

# Messages / My stuff page

```python
session.mystuff_projects("all", page=1, sort_by="") # Returns the projects from "My stuff" page as list of project objects

session.messages(limit=40, offset=0) # Returns your messages as raw scratch API response
session.clear_messages() # Marks your messages as read
session.get_message_count() # Returns your message count
```

# Frontpage

```python
scratch3.get_news(limit=10, offset=0) # Returns the news from the Scratch front page as raw scratch API response

# All these methods use the same API endpoint, so all of them return raw API response
scratch3.featured_projects()
scratch3.featured_studios()
scratch3.top_loved()
scratch3.top_remixed()
scratch3.newest_projects() # This list is not present on the Scratch home page, but the API still provides it.
scratch3.design_studio_projects()

session.get_feed(limit=20, offset=0) # Returns your "What's happening" section from the Scratch front page as raw scratch API response
session.loved_by_followed_users(limit=40, offset=0) # Returns the projects loved by users you are following as raw scratch API response
```

# Forum topics `scratch3.ForumTopic`
All of this data is fetched from ScratchDB v3, therefore it may be slighty off.

**Getting a forum topic:**
```python
topic = session.connect_topic("topic_id")
topic = scratch3.get_topic("topic_id")
```

**Get a list of the topics in a category:**
```python
topic_list = session.connect_topic_list("category_name", page=0)
topic_list = scratch3.get_topic_list("category_name", page=0)
```

**Attributes:**
```python
topic.title # Returns the forum topic title
topic.category # Returns the forum topic category
topic.deleted # Returns true whether this forum topic is deleted
# ----- ----- #
topic.update()  # Updates the above data
```

**Functions:**
```python
topic.posts(page=0, order="oldest") # Returns the topic posts as list of forum post objects. Possible parameters for "order" are "oldest" and "newest"
topic.first_post() # Returns the first topic post as forum post object

topic.post_count_by_user("username") # ???
topic.activity() # Returns an activity / change log for the topic as raw ScratchDB response
```

# Forum posts `scratch3.ForumPost`
All of this data is fetched from ScratchDB v3, therefore it may be slighty off.

**Getting a forum post:**
```python
post = session.connect_post("post_id")
post = scratch3.get_post("post_id")
```

**Search for forum posts:**
```python
post_list = session.search_posts(query="query", order="newest", page=0) # Returns a list of forum post objects
post_list = scratch3.search_posts(query="query", order="newest", page=0) # Returns a list of forum post objects
```

**Attributes:**
```python
post.id # Returns the forum post id
post.author # Returns the forum post author username
post.posted # The date the forum post was made
post.edited # The date of the most recent forum post edit. If the forum post wasn't edited this is None
post.edited_by # The user who made the most recent edit. If the forum post wasn't edited this is None
post.deleted # Returns True whether the forum post was deleted
post.html_content # Returns the forum post content as HTML
post.bb_content # Returns the forum post content as BBCode
post.topic_id # Returns the id of the forum topic the forum post is in
post.topic_name # Returns the name of the forum topic the forum post is in
post.topic_category # Returns the name of the category the forum topic is in
# ----- ----- #
post.update()  # Updates the above data
```

**Methods:**
```python
post.get_topic() # Returns the forum topic the post is in as forum topic object
post.get_author() # Returns the post author as user object

post.edit(new_content) # Edits the forum post. Requires session

post.ocular_reactions() # Returns the forum topic ocular reactions as raw Ocular API response
```

# Site stats and health

```python
scratch3.total_site_stats() # Returns the total site stats as raw scratch API response
scratch3.monthly_site_traffic() # Returns last month's site traffic as raw scratch API response
scratch3.country_counts() # Returns the amount of Scratch users in each country as raw scratch API response
scratch3.age_distribution() # Returns age distribution section of scratch statistics as raw scratch API response

scratch3.get_health() # Returns Scratch's health data as raw scratch API response
```

# Backpack

```python
session.backpack(limit=20, offset=0) # Returns the contents of your backpack as raw scratch API response
session.delete_from_backpack("asset id") # Deletes an asset from your backpack
```

# Getting your session id

This section explains how to get your Scratch session id from your browser cookies.

1. Open scratch.mit.edu in your browser
2. Click the 🔒 icon in the URL bar, then click "Cookies"
3. Then find a cookie called `scratchsessionid` (in the "scratch.mit.edu" » "Cookies" folder). The content of this cookie is your Scratch session id

![](https://scratch3-assets.1tim.repl.co/template/cookies.png)

# Contributors

- See the GitHub repository for full list of contributors.
- Create a pull request to contribute code yourself.
