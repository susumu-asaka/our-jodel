# Our Jodel: Conceptual Design
## Data Model

### Entity-Relationship Diagram

![Data Model](./Data_Model.png)

### Data Dictionary

#### Post

| Attribute   | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| message     | Text can have length up to 240 characters.                   |
| imageBase64 | Image encoded base 64.                                       |
| color       | The color has no actual meaning. The color is selected randomly when the user press the &ldquo;+&rdquo; button.<br /><br/>Posts can have seven colors:<br />![colors](./colors.png) |
| latitude    | Signed latitude of the location of posting, in degrees, with precision of 5 decimal places. |
| longitude   | Signed longitude of the location of posting, in degrees, with precision of 5 decimal places. |
| distance    | One of the following:<br />&bull; **here**: less than 1 km;<br />&bull; **very-close**: between 1 and 2 km;<br />&bull; **close**: between 2 and 10 km;<br />&bull; **far**: more than 10 km;<br />&bullet; **hometown**: if posted using *hometown* feature.<br/>The *hometown* feature allows users to read and write posts in a place they are not currently in. However, this location must be set once by the user and then it can not be changed. Only the current location of the user can be set as his hometown.<br/><br/>At first, only posts posted within a 10 km radius are shown in the feed. When there are many users in the area, the users enjoy a lot of activity; less populated areas, on the contrary, can spot tumbleweed in their less engaged feeds. To mitigate this issue we introduce the *dynamic radius* feature. With the dynamic radius the radius will be increased in 10 km steps, up the maximum of 100 km, until the area has 150 posts.<br/> <br/>The distance in kilometers between two points located at (&phi;<sub>0</sub>, &lambda;<sub>0</sub>) and (&phi;<sub>1</sub>, &lambda;<sub>1</sub>), where latitude &phi; and longitude &lambda; are in degrees,  can be calculated by the following approximate formula:<br/><br/><a href="https://www.codecogs.com/eqnedit.php?latex=d&space;=&space;111.2&space;\sqrt{(\varphi_1-\varphi_0)^2&plus;((\lambda_1-\lambda_0)\cos\varphi_0)^2}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?d&space;=&space;111.2&space;\sqrt{(\varphi_1-\varphi_0)^2&plus;((\lambda_1-\lambda_0)\cos\varphi_0)^2}" title="d = 111.2 \sqrt{(\varphi_1-\varphi_0)^2+((\lambda_1-\lambda_0)\cos\varphi_0)^2}" /></a><br/><br/>This approximation is very fast and produces fairly accurate result for small distances. Also in ordering locations by distance, it is much faster to order by squared distance eliminating the need for computing the square root.<br/>For instance, the distance between <br/>Praça da Sé in São Paulo, at (-23.5503&deg;, -46.6334&deg;) and <br/>Praça XV in Rio de Janeiro, at (-22.9028&deg;, -43.1733&deg;), <br/>using the above formula, is 360.0 km. <br/>Using an [accurate geodesic calculator](https://geographiclib.sourceforge.io/cgi-bin/GeodSolve?type=I&input=-23.5503+-46.6334+-22.9028+-43.1733&format=g&azi2=f&unroll=r&prec=3&radius=6378137&flattening=1%2F298.257223563&option=Submit), the distance is 361.1 km. |
| city        | Name of the city. e.g.: São Paulo                            |
| createdAt   | Date-time of the post                                        |
| childCount  | For original post, it is the number of replies.<br />For replies, it is 0. |
| voteCount   | number of upvotes minus number of downvotes. <br />When a post has voteCount of -5 it disappears. |

#### User

| Attribute      | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| **email**      | Email address.                                               |
| activationCode | Activation code.                                             |
| latitude       | Signed latitude of user's location, in degrees, with precision of 5 decimal places. |
| longitude      | Signed longitude of the user's location, in degrees, with precision of 5 decimal places. |
| location       | Name of the user's location, normally equals to the name of the city. |
| city           | Name of the city of the the user's location, e.g.: São Paulo |
| country        | Two letter country code, e.g. BR                             |
| type           | User's type. Users can have one of 6 types:<br />&bull; **Aprendiz**<br />&bull; **Funcionário**<br />&bull; **Colegial**<br />&bull; **Vestibulando**<br />&bull; **Universitário**<br />&bull; **Outros** |
| gender         | &ldquo;m&rdquo; or &ldquo;f&rdquo;                           |
| birthyear      | User's birthyear with 4 digits.                              |
| karma          | User's score: <br /> &bull; The user earns (looses) 2 karma for upvoting (downvoting) on a post<br /> &bull; The user earns (looses) 10 karma when he receives an upvote (downvote)<br /> &bull; The user earns 1 karma for thanking another user who replies to his post<br /> &bull; The replier earns 5 karma for receiving thanks. |

#### Remark

| Attribute | Description                                                  |
| --------- | ------------------------------------------------------------ |
| kind      | One of the following:<br />&bull; **upvote**<br />&bullet; **downvote**<br />&bullet; **subscribe**: when the user posts an original post, reply or pin a thread it gets automatically subscribed<br />&bullet; **thank**: the author can thank for a reply from another user<br />&bullet; **pin**. |

#### Notification

| Attribute | Description                                                  |
| --------- | ------------------------------------------------------------ |
| type      | One of the following:<br />&bullet; **ReplyOnReply**: a reply was posted on a thread, after the user has replied on the same thread<br />&bullet; **ReplyOnOriginal**: a reply was posted on a thread originated from a user's post<br />&bullet; **VoteOnPost**: vote on a post of this user<br />&bullet; **ReplyOnPin**: a reply was posted on a thread pinned by this user |
| read      | Flag to signal that the notification has been read.          |
| time      | Date and time of the event.                                  |

#### BoostedPostBooking

![Boosted Post](./boosted_post.png)

| Attribute | Description                                                  |
| --------- | ------------------------------------------------------------ |
| headline  | The headline of the url. E.g.: **Jetzt Freikarte sichern!** <br/><br/>The post will be boosted in the area of a 10 km radius circle around the point of placement.<br/>In each area, there can be only one boosted post at a time. Therefore the distance between two simultaneous boosted posts should always be longer than 20 km.<br/>The boosted post appears first in the User's feed.<br/>Each boosted post is a regular post, thus Users can vote or reply on it (replying can be optionally disabled by the poster). Only difference: if the User downvote it, the post will disappear for the User. |
| url       | The URL of the ad. E.g.: https://www.vodafone.de/freikarten/callya-digital |
| startTime | Date and time of the booking.                                |
| endTime   | Date and time of the removal.                                |
| latitude  | This is the signed latitude of the point of placement, in degrees, with precision of 5 decimal places. |
| longitude | This is the signed longitude of the point of the placement, in degrees, with precision of 5 decimal places. |
| canReply  | Flag to enable replies to this placement.                    |

#### Channel

| Attribute     | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| **name**      | Unique identifier of channel, CamelCase style. <br />Users will not be able to create their own channels, but will be able to follow the ones we will create.<br/>User's feed will show the original posts with no channel and those from the channels followed by him. |
| description   | Description of this channel.                                 |
| followerCount | Count of the number of users in the local area that are currently following this channel. |

## Functional Processes

### User Account Management

#### Signup

| Functional<br/>User | Sub-process Description                                      | Data Group                   | Data<br/>Mvmt<br/>Type | CFP  |
| ------------------- | ------------------------------------------------------------ | ---------------------------- | ---------------------- | ---- |
| User                | User enters his details                                      | User                         | E                      | 1    |
|                     | Server validates the entered data and checks if the User already exists | User                         | R                      | 1    |
| E-mail<br/>provider | Server sends activation code to the User's email             | Activation record            | X                      | 1    |
|                     | Server creates a new User                                    | User                         | W                      | 1    |
| User                | App displays confirmation/error message                      | Confirmation  /Error message | X                      | 1    |

**Total: 5  CFP**

#### Activate App

| Functional<br/>User | Sub-process Description                    | Data Group                    | Data<br/>Mvmt<br/>Type | CFP  |
| ------------------- | ------------------------------------------ | ----------------------------- | ---------------------- | ---- |
| User                | User enters Activation Record              | Activation Record             | E                      | 1    |
|                     | Server authenticates the Activation Record | Activation Record             | R                      | 1    |
|                     | App save Activation Record in the device   | Activation Record             | W                      | 1    |
| User                | App displays confirmation/error message    | Confirmation  / Error message | X                      | 1    |

**Total: 4 CFP**

#### Initialize App

| Functional<br/> User | Sub-process Description                                      | Data Group         | Data<br/>Mvmt<br/>Type | CFP  |
| -------------------- | ------------------------------------------------------------ | ------------------ | ---------------------- | ---- |
| User                 | User starts App.                                             | Control Command    | E                      | 1    |
|                      | App retrieves the Activation Record.                         | Activation Record  | R                      | 1    |
|                      | Server authenticates the User's Activation Record and returns a JWT Access Token, valid for 24 hours. | User, Access Token | R                      | 1    |
|                      | App saves the Access Token in the device storage.<br/>The App will send the Access Token in the authentication header of the HTTP requests: &ldquo;Authentication: Bearer {token}&rdquo;. This authorizes the User for seeing posts, voting, posting etc. | Access Token       | W                      | 1    |
| Location Service     | App gets User Location                                       | User Location      | E                      | 1    |
|                      | Server updates User Location                                 | Device Location    | W                      | 1    |
| User                 | App displays error message                                   | Error message      | X                      | 1    |

**Total: 7 CFP**

### Reading Posts

#### Display Feed

![Feed](./feed.png)



![Channels](./channels.png)

| Functional<br/>User | Sub-process Description                                      | Data Group         | Data<br/>Mvmt<br/>Type | CFP  |
| ------------------- | ------------------------------------------------------------ | ------------------ | ---------------------- | ---- |
| User                | User can choose to read his entire feed (default) or of one of his channels by clicking the channel button and selecting the channel from the list of his channels.<br/>User can choose to read feed from his current GPS location or hometown by selecting location on the city drop-down button.<br/>User can select order of posts by clicking one of buttons on the bottom:<br/> &bullet; &#x1f553; **Newest**: reverse chronological;<br/> &bullet; &#x1f4ac; **Most discussed**: reply count descending;<br/> &bullet;  &#x2303; **Loudest**: vote count descending. | Location,  Channel | E                      | 3    |
|                     | App queries 10 posts at a time as the user scrolls down through the feed screen.<br/>If there is a boosted post in the area and the User has not downvoted it, put it at the top of the feed. | Post               | R                      | 2    |
| User                | Display the list of Posts.                                   | Post               | X                      | 1    |
| User                | System displays error message                                | Error message      | X                      | 1    |

**Total: 7 CFP**

### Display My Votes Feed

| Functional<br/>User | Sub-process Description                                      | Data Group      | Data<br/>Mvmt<br/>Type | CFP  |
| ------------------- | ------------------------------------------------------------ | --------------- | ---------------------- | ---- |
| User                | User selects to see My Votes Feed                            | Control Command | E                      | 1    |
|                     | Server gets original posts upvoted by the User ordered by date-time descending.<br/>Load 10 posts at a time as the user scrolls through the feed screen. | Post            | R                      | 2    |
| User                | Display the list of Posts                                    | Post            | X                      | 1    |
| User                | App displays error message                                   | Error message   | X                      | 1    |

**Total: 5 CFP**

### Display My Replies Feed

| Functional<br/>User | Sub-process Description                                      | Data Group      | Data<br/>Mvmt<br/>Type | CFP  |
| ------------------- | ------------------------------------------------------------ | --------------- | ---------------------- | ---- |
| User                | User selects to see My Replies Feed                          | Control Command | E                      | 1    |
|                     | Server gets original posts that the User replied ordered by date-time descending.<br/>Load 10 posts at a time as the user scrolls through the feed screen. | Post            | R                      | 2    |
| User                | Display the list of Posts. <br/>Don't display figures, only “Hold to view” button. | Post            | X                      | 1    |
| User                | App displays error message                                   | Error message   | X                      | 1    |

**Total: 5 CFP**

### Display My Pins Feed

| Functional<br/>User | Sub-process Description                                      | Data Group      | Data<br/>Mvmt<br/>Type | CFP  |
| ------------------- | ------------------------------------------------------------ | --------------- | ---------------------- | ---- |
| User                | User selects to see My Pins Feed                             | Control Command | E                      | 1    |
|                     | Server gets original posts the User pinned ordered by date-time descending.<br/>Load 10 posts at a time as the user scrolls through the feed screen. | Post            | R                      | 2    |
| User                | Display the list of Posts. Don't display figures, only “Hold to view” button. | Post            | X                      | 1    |
| User                | System displays error message                                | Error message   | X                      | 1    |

**Total 5 CFP**

### Display Channel Feed

| Functional<br/>User | Sub-process Description                                      | Data Group    | Data<br/>Mvmt<br/>Type | CFP  |
| ------------------- | ------------------------------------------------------------ | ------------- | ---------------------- | ---- |
| User                | User selects to see posts of a Channel                       | Channel       | E                      | 1    |
|                     | Server gets original posts of a channel, posted close to the User, ordered by date-time descending.<br/>Load 10 posts at a time as the user scrolls through the feed screen.<br/>If there is less than 10 close posts, append far posts.<br/>If there is a boosted post of the channel in the area, put it on top. | Post          | R                      | 4    |
| User                | Display the list of Posts. <br/>Don't display figures, only “Hold to view” button. | Post          | X                      | 1    |
| User                | System displays error message                                | Error message | X                      | 1    |

**Total 7 CFP**

