# How-to-connect-the-TrueGear-suit

In order to realize the connection of TrueGear suit,Truegear_player provides a WebSocket server address,"ws://127.0.0.1:18233/v1/tact/".By connecting to this WebSocket link, the corresponding request can be sent to cause the suit to produce a vibration or EMS effect.
The information transmitted by this link is in JSON.
The request message format is as follows:
```
{
    "Method": "play_effect_by_content", 
    "Body": "Body"
}

{
    "Method": "play_effect_by_content",
    "Result": "Body"
}
```


The main parameters of the method are: "play_no_registered". The Body and Result data in the request body must be base64 encoded.

Basic conception

EffectObject class
```
struct EffectObject {
		string eventName;
		vector<TrackObject> trackList;
	};
```


In this article, we will detail a structure called EffectObject, which contains two member variables: eventName and trackList. These two member variables represent the trigger event name and the action, respectively.
eventName, which is a member variable of type string that is used to trigger the event name. In practical applications, we can perform corresponding operations according to different trigger event names.
Next, let's look at the trackList, which is an array of type TrackObject that stores a collection of actions.

TrackObject class
The TrackObject class is used to describe objects with the same action pattern and contains the following properties:
```
struct TrackObject {
	ActionType action_type;
	IntensityMode intensity_mode;
	bool once; (Only with EMS)
	int interval;  (Only with EMS)
	int start_time;
	int end_time;
	int start_intensity;
	int end_intensity; 
	vector<int> index; (Action id of motor or EMS)
	};
```


● action_type: Used to represent the class of action of an object, such as vibration, electrical, etc.

● intensity_mode: which is used to represent how the intensity of an object changes, such as gradient, mutation, etc.

● once: Boolean type, which indicates whether the object performs an action only once and is effective only when EMS is used.

● interval: Integer type, that represents the interval between the actions of an object and is valid only when electrical stimulation is used.

● start_time: Integer type, indicating the start time of an object action.

● end_time：Integer type,indicating the end time of an object action.

● start_intensity：Integer type,Represents the initial intensity of the object's action.

● end_intensity：Integer type,Represents the termination intensity of the object's action, and when the intensity mode is FadeInAndOut, this value represents the peak or trough intensity.

● index：A vector of type integer that represents the relevant ID of the object's action, such as the ID of a motor or EMS.

```
enum ActionType {
 Shake, 
Electrical 
};  


enum IntensityMode { 
 Const,
 Fade, 
 FadeInAndOut 
}; 
```
![](https://static.truegear.cn/bbs/dev/image1.png)
![](https://static.truegear.cn/bbs/dev/image2.png)


index indicates the location


![](https://static.truegear.cn/bbs/dev/image3.png)
The procedure for connecting to the WebSocket server is as follows:
1. First, install a client library that supports the WebSocket protocol on the device. For example, in JavaScript, you can use the browser's built-in WebSocket object; In Python, you can use websocket libraries such as websockets or websocket-client.

2. After importing the client library, create a WebSocket object and pass the server address as a parameter. For example, in JavaScript, you can create a WebSocket object like this:

`const ws = new WebSocket('ws://127.0.0.1:18233/v1/tact/');`

3. When the WebSocket object successfully connects to the server, the 'open' event is triggered. When the connection is established, a request can be sent to control the vibration or electrical stimulation effect of the garment.

The following is an example of sending a vibration and electrical stimulation

The body of the json sent is:

 `{"Method":"play_no_registered","Body":"eyJuYW1lIjoiTGVmdEhhbmRQaWNrdXBJdGVtIiwidXVpZCI6IkxlZnRIYW5kUGlja3VwSXRlbSIsImtlZXAiOiJGYWxzZSIsInByaW9yaXR5IjowLCJ0cmFja3MiOlt7InN0YXJ0X3RpbWUiOjAsImVuZF90aW1lIjoxMDAsInN0b3BfbmFtZSI6IiIsInN0YXJ0X2ludGVuc2l0eSI6MzAsImVuZF9pbnRlbnNpdHkiOjUsImludGVuc2l0eV9tb2RlIjoiQ29uc3QiLCJhY3Rpb25fdHlwZSI6IlNoYWtlIiwib25jZSI6IkZhbHNlIiwiaW50ZXJ2YWwiOjAsImluZGV4IjpbMCw0XX0seyJzdGFydF90aW1lIjowLCJlbmRfdGltZSI6MjAwLCJzdG9wX25hbWUiOiIiLCJzdGFydF9pbnRlbnNpdHkiOjEwLCJlbmRfaW50ZW5zaXR5Ijo1LCJpbnRlbnNpdHlfbW9kZSI6IkNvbnN0IiwiYWN0aW9uX3R5cGUiOiJFbGVjdHJpY2FsIiwib25jZSI6IkZhbHNlIiwiaW50ZXJ2YWwiOjEwLCJpbmRleCI6WzAsMTAwXX1dfQ=="} `

After unraveling base64, the specific data can be obtained

```
{
    "name": "LeftHandPickupItem",
    "uuid": "LeftHandPickupItem",
    "keep": "False",
    "priority": 0,
    "tracks": [
        {
            "start_time": 0,
            "end_time": 100,
            "stop_name": "",
            "start_intensity": 30,
            "end_intensity": 5,
            "intensity_mode": "Const",
            "action_type": "Shake",
            "once": "False",
            "interval": 0,
            "index": [
                0,
                4
            ]
        },
        {
            "start_time": 0,
            "end_time": 200,
            "stop_name": "",
            "start_intensity": 10,
            "end_intensity": 5,
            "intensity_mode": "Const",
            "action_type": "Electrical",
            "once": "False",
            "interval": 10,
            "index": [
                0,
                100
            ]
        }
    ]
}
```


4.You can close the WebSocket connection when you no longer need to control the suit.



Test code:
```
// To install the ws module, run: npm install ws
const WebSocket = require('ws');

const ws = new WebSocket('ws://127.0.0.1:18233/v1/tact');

ws.on('error', console.error);

ws.on('open', function open() {
  console.log('ws open');
  ws.send('{"Method":"play_no_registered","Body":"eyJuYW1lIjoiTGVmdEhhbmRQaWNrdXBJdGVtIiwidXVpZCI6IkxlZnRIYW5kUGlja3VwSXRlbSIsImtlZXAiOiJGYWxzZSIsInByaW9yaXR5IjowLCJ0cmFja3MiOlt7InN0YXJ0X3RpbWUiOjAsImVuZF90aW1lIjoxMDAsInN0b3BfbmFtZSI6IiIsInN0YXJ0X2ludGVuc2l0eSI6MzAsImVuZF9pbnRlbnNpdHkiOjUsImludGVuc2l0eV9tb2RlIjoiQ29uc3QiLCJhY3Rpb25fdHlwZSI6IlNoYWtlIiwib25jZSI6IkZhbHNlIiwiaW50ZXJ2YWwiOjAsImluZGV4IjpbMCw0XX0seyJzdGFydF90aW1lIjowLCJlbmRfdGltZSI6MjAwLCJzdG9wX25hbWUiOiIiLCJzdGFydF9pbnRlbnNpdHkiOjEwLCJlbmRfaW50ZW5zaXR5Ijo1LCJpbnRlbnNpdHlfbW9kZSI6IkNvbnN0IiwiYWN0aW9uX3R5cGUiOiJFbGVjdHJpY2FsIiwib25jZSI6IkZhbHNlIiwiaW50ZXJ2YWwiOjEwLCJpbmRleCI6WzAsMTAwXX1dfQ=="} ');
});

ws.on('message', function message(data) {
  console.log('received: %s', data);
});
```


