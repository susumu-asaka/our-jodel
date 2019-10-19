# Our Jodel: Conceptual Design
## Data Model

### Entity-Relationship Diagram

![alt text](./Data_Model.png)

### Data Dictionary

#### Post

| Attribute   | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| message     | Text can have length up to 240 characters.                   |
| imageBase64 | Image encoded base 64.                                       |
| color       | The color has no actual meaning. The color is selected randomly when the user press the &ldquo;+&rdquo; button.<br /><br/>Posts can have six colors:<br />&bull; <span style="background-color: #FF9908;color: white"> orange (#ff9908) </span><br />&bull; <span style="background-color: #FFBA00;color: white"> yellow (#ffba00) </span><br />&bull; <span style="background-color: #dd5f5f;color: white"> red (#dd5f5f) </span><br />&bull; <span style="background-color: #06a3cb;color: white"> blue (#06a3cb) </span><br />&bull; <span style="background-color: #8abdb0;color: white">  teal (#8abdb0) </span><br />&bull; <span style="background-color: #9ec41c;color: white"> green (#9ec41c) </span> |
| latitude    | Signed latitude of the location of posting, in degrees, with precision of 5 decimal places. |
| longitude   | Signed longitude of the location of posting, in degrees, with precision of 5 decimal places. |
| distance    | One of the following:<br />&bull; here (less than 1 km)<br />&bull; very-close (between 1 and 2 km)<br />&bull; close (between 2 and 10 km)<br />&bull; far (more than 10 km)<br />&bull; hometown (posted from a different location using hometown feature) |
| city        | Name of the city. e.g.: São Paulo                            |
| createdAt   | Date-time of the post                                        |
| childCount  | For original post, it is the number of replies.<br />For replies, it is 0. |
| voteCount   | number of upvotes minus number of downvotes. <br />When a post has voteCount of -5 it disappears. |

#### User

| Attribute    | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| **username** | Login e-mail address.                                        |
| password     | Login password.                                              |
| latitude     | Signed latitude of user's location, in degrees, with precision of 5 decimal places. |
| longitude    | Signed longitude of the user's location, in degrees, with precision of 5 decimal places. |
| location     | Name of the user's location, normally equals to the name of the city. |
| city         | Name of the city of the the user's location, e.g.: São Paulo |
| country      | Two letter country code, e.g. BR                             |
| type         | User's type. Users can have one of 6 types:<br />&bull; Aprendiz<br />&bull; Funcionário<br />&bull; Colegial<br />&bull; Vestibulando<br />&bull; Universitário<br />&bull; Outros |
| gender       | &ldquo;m&rdquo; or &ldquo;f&rdquo;                           |
| birthyear    | User's birthyear with 4 digits.                              |
| karma        | User's score: <br /> &bull; The user earns (looses) 2 karma for upvoting (downvoting) on a post<br /> &bull; The user earns (looses) 10 karma when he receives an upvote (downvote)<br /> &bull; The user earns 1 karma for thanking another user who replies to his post<br /> &bull; The replier earns 5 karma for receiving thanks. |

#### Remark

| Attribute | Description                                                  |
| --------- | ------------------------------------------------------------ |
| kind      | One of the following:<br />&bull; upvote: an upvote cancels previous downvote from the user<br />&bullet; downvote: an downvote cancels previous upvote from the user<br />&bullet; subscribe: when the user posts an original post, reply or pin a thread he gets automatically subscribed<br />&bullet; thank: the author can thank for a reply from another user<br />&bullet; pin. |

#### Notification

| Attribute | Description                                                  |
| --------- | ------------------------------------------------------------ |
| type      | One of the following:<br />&bullet; ReplyOnReply: a reply was posted on a thread, after the user has replied on the same thread<br />&bullet; ReplyOnOriginal: a reply was posted on a thread originated from a user's post<br />&bullet; VoteOnPost: vote on a post of this user<br />&bullet; ReplyOnPin: a reply was posted on a thread pinned by this user |
| read      | Flag to signal that the notification has been read.          |
| time      | Date and time of the event.                                  |

#### BoostedPostPlacement

| Attribute | Description                                                  |
| --------- | ------------------------------------------------------------ |
| title     | The title of the boosted post.<br/><br/>The post will be boosted in the area of a 10 km radius circle around the point of placement.<br/>In each area, there can be only one boosted post at a time.<br/>Therefore the distance between two simultaneous boosted posts should always be longer than 20 km. |
| url       | The URL of the ad.                                           |
| startTime | Date and time of the placement.                              |
| endTime   | Date and time of the removal.                                |
| latitude  | This is the signed latitude of the point of placement, in degrees, with precision of 5 decimal places. |
| longitude | This is the signed longitude of the point of the placement, in degrees, with precision of 5 decimal places. |
| canReply  | Flag to enable replies to this placement.                    |

#### Channel

| Attribute     | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| **name**      | Unique identifier of channel, CamelCase style. <br />At first, users will not be able to create their own channels, but will be able to post and follow the ones we will create.<br/>Like everything in Jodel, the channels will also have local coverage. When a user follows a channel, everything others post to the channel within the 10km radius circle will automatically appear in his home feed.<br/>Here are some channels we consider important for every local community:<br/>&bullet; **PerguntasLocais**: This is a part of this local community that aims to deliver a great local question & answer experience. What was the loud noise 2 minutes ago all about? Why do all people nearby suddenly wear red sneakers? This is a place to help each other and satisfy users' curiosity.<br/>&bullet; **CommunidadeAnimal** is the home for all of our pet lovers. Here users can get their daily dose of cuteness to get them through a rough day.<br/>&bullet; **AcontecendoHoje!** is the place where fellow users keep each other updated about cool stuff to do in their area. Is there any awesome show in your neighborhood? A new shop is opening up? |
| description   | Description of this channel.                                 |
| followerCount | Count of the number of users in the local area that are currently following this channel. |

