# TrueGear Suit Connection Tutorial

## Connection Abstructs 
Truegear_player provides a WebSocket server address,"ws://127.0.0.1:18233/v1/tact/" to connect to TrueGear suit. After connecting to this WebSocket link, the suit will produce a vibration or EMS effect based on corresponding request from Truegear_player.
The request message transmitted by this link is in JSON format as following:

- The main parameters of the method is: "play_no_registered". 
- The Body and Result data in the request body must be base64 encoded Json Object.

    ```Json
        {
            'Method': "play_effect_by_content', 
            'Body': Body
        }
    ```
    ```Json
        {
            'Method': 'play_effect_by_content',
            'Result': Body
        }
    ```


## Basic Concepts 
### Important class
#### EffectObject class
EffectObject contains two member variables: eventName and trackList. These two member variables represent the trigger event name and the action, respectively.
- An eff_asset belongs to an EffectObject. 
- An EffectObject is a collection of multiple TrackObjects 
    ```cpp
        struct EffectObject {
            string eventName;
            vector<TrackObject> trackList;
        };
    ```
- eventName: A member variable of type string that is used to trigger the event name. In practical applications, we can perform corresponding operations according to different trigger event names.
- trackList: An array of type TrackObject that stores a collection of actions.

#### TrackObject Class
- TrackObject objects have the same action mode.
- The TrackObject class is used to describe objects with the same action pattern and contains the following properties:
    ```cpp
        #Action type
        enum ActionType { 
            Shake,              // vibration 
            Electrical,         // electrical stimulation
        };
    ```
- action_type: Used to represent the class of action of an object, such as vibration, electrical, etc.
    ![](https://static.truegear.cn/bbs/dev/image1.png)
    ![](https://static.truegear.cn/bbs/dev/image2.png)
    ```cpp
        enum IntensityMode {    // Intensity mode 
            Const,              // constant 
            Fade,               // fade in or fade out 
            FadeInAndOut        // fade in and out, or fade out and in 
        };
    ```
- intensity_mode: which is used to represent how the intensity of an object changes, such as gradient, mutation, etc.
    ```cpp
        struct TrackObject { 
            ActionType action_type;         // Action type
            IntensityMode intensity_mode;   // Intensity mode
            bool once;                      // Is it once only for electrical stimulation  
            int interval;                   // Interval (only for electrical stimulation)
            int start_time;                 // Start time 
            int end_time;                   // End time 
            int start_intensity;            // Start intensity 
            int end_intensity;              // End intensity (For FadeInAndOut, it's peak or trough) 
            vector index;                   // IDs for the motors or electrical stimulations
    }
    ```
- once: Boolean type, which indicates whether the object performs an action only once and is effective only when EMS is used.
- interval: Integer type, that represents the interval between the actions of an object and is valid only when electrical stimulation is used.
- start_time: Integer type, indicating the start time of an object action.
- end_time：Integer type,indicating the end time of an object action.
- start_intensity：Integer type,Represents the initial intensity of the object's action.
- end_intensity：Integer type,Represents the termination intensity of the object's action, and when the intensity mode is FadeInAndOut, this value represents the peak or trough intensity.
- index：A vector of type integer that represents the relevant ID of the object's action, such as the ID of a motor or EMS.

### Effector Location:
![](https://static.truegear.cn/bbs/dev/image3.png)

## Connection Approch & Example
The procedure for connecting to the WebSocket server is as follows:
1. First, install a client library that supports the WebSocket protocol on the device. 
    - JavaScript: you can use the browser's built-in WebSocket object; 
    - Python: you can use websocket libraries such as websockets or websocket-client.
2. After importing the client library, create a WebSocket object and pass the server address as a parameter. 
    - JavaScript: you can create a WebSocket object like this:
    ```js
        const ws = new WebSocket('ws://127.0.0.1:18233/v1/tact/');
    ```
3. When the WebSocket object successfully connects to the server, the 'open' event is triggered. When the connection is established, a request can be sent to control the vibration or electrical stimulation effect of the garment.
    An example of sending a vibration and electrical stimulation
    - The body of the json sent is:
    ```Json
    {"Method":"play_no_registered","Body":"eyJuYW1lIjoiTGVmdEhhbmRQaWNrdXBJdGVtIiwidXVpZCI6IkxlZnRIYW5kUGlja3VwSXRlbSIsImtlZXAiOiJGYWxzZSIsInByaW9yaXR5IjowLCJ0cmFja3MiOlt7InN0YXJ0X3RpbWUiOjAsImVuZF90aW1lIjoxMDAsInN0b3BfbmFtZSI6IiIsInN0YXJ0X2ludGVuc2l0eSI6MzAsImVuZF9pbnRlbnNpdHkiOjUsImludGVuc2l0eV9tb2RlIjoiQ29uc3QiLCJhY3Rpb25fdHlwZSI6IlNoYWtlIiwib25jZSI6IkZhbHNlIiwiaW50ZXJ2YWwiOjAsImluZGV4IjpbMCw0XX0seyJzdGFydF90aW1lIjowLCJlbmRfdGltZSI6MjAwLCJzdG9wX25hbWUiOiIiLCJzdGFydF9pbnRlbnNpdHkiOjEwLCJlbmRfaW50ZW5zaXR5Ijo1LCJpbnRlbnNpdHlfbW9kZSI6IkNvbnN0IiwiYWN0aW9uX3R5cGUiOiJFbGVjdHJpY2FsIiwib25jZSI6IkZhbHNlIiwiaW50ZXJ2YWwiOjEwLCJpbmRleCI6WzAsMTAwXX1dfQ=="}
    ```
    After unraveling base64, the specific data of the body can be obtained
    ```json
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
4. You can close the WebSocket connection when you no longer need to control the suit.
    Test code:
    ```js
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
## Connection Approch (Java)
### Import Files to Project
- if UPL_Android.xml exist, add the aar file to the project
- if UPL_Android.xml does not exist, create `Build.cs` and add corresponding codes:	
    ```C#
    if (Target.Platform == UnrealTargetPlatform.Android){
        var manifestFile = Path.Combine(ModuleDirectory, "UPL.xml");
        AdditionalPropertiesForReceipt.Add("AndroidPlugin", manifestFile);
        }
    ```
### Add APK Privilege 
- Under `Project Settings-Platform-Android-Advanced APK Packaging-Extra Permissions`, add following：
    ```java
    android.permission.WRITE EXTERNAL STORAGE
    android.permission.READ_EXTERNAL_STORAGE
    android.permission.BLUETOOTH_CONNECT
    android.permission.BLUETOOTH_SCAN
    android.permission.BLUETOOTH_PRIVILEGED
    android.permission.BLUETOOTH
    android.permission.BLUETOOTH_ADMIN
    android.permission.BLUETOOTH_ADVERTISE
    android.permission.BLUETOOTH_CONNECT
    ```
    ![](image.png)

### Key Functions: 
- Initialize SDK Configuration
    ```java 
    unrealInitialized(Context context);
    ```
- Register Effect，and export eff file:
    `registeEffect_Eff(eventName, textFile_bytes);`

    | Parameter          | Description                   | Required |
    |--------------------|-------------------------------|----------|
    | `eventName`        | Effect Name                   | Required |
    | `textFile_bytes`   | Bytes data of the file        | Required |

- Customized Json Data
    ```java
    private string jsonStr = @"{""name"":""MindDeathWave"",""uuid"":""MindDeathWave"",""keep"":""False"",""tracks"":[{""start_time"":0,""end_time"":100,""stop_name"":"""",""start_intensity"":20,""end_intensity"":40,""intensity_mode"":""Const"",""action_type"":""Shake"",""once"":""False"",""interval"":0,""index"":[5,6,3,2,7,1,0,4,8,9,10,11,15,14,13,12,16,17,19,18,116,117,118,119,115,114,113,112,108,109,110,111,107,106,104,105,100,101,102,103]},{""start_time"":0,""end_time"":0,""stop_name"":"""",""start_intensity"":30,""end_intensity"":20,""intensity_mode"":""Const"",""action_type"":""Electrical"",""once"":""True"",""interval"":1,""index"":[0,100]}]}";
    ```
    ```java
    registeEffect(eventName, jsonStr);
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `eventName`      | Effect Name                   | Required |
    | `jsonStr`        | Effect Data                   | Required |

- Bluetooth Availability Detection:
    ```java
    isAvailable()
    // Returns 1 if available, -1 if not available
    // -1 is not bluetooth privilege
    ```

- Avaliable Device Scan:
    ```java 
    startScanning();
    ```

- Get Avaliable Device:
    ```java 
    getDeviceList()
    ```

- Connect to Device:
    ```java
    connectToDevice(macAddress);
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `macAddress`     | Device's macAddress           | Required |

- Disconnect Device（java）
    ```java
    disconnectFromDevice();
    ```

- Send Event message to Trigger Effect:
    ```java
    sendPlayByEventName(eventName );
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `eventName`      | Registered Effect Name        | Required |

- Send Customized Json Data to Trigger Effect:
    ```java
    string jsonStr = @"{""name"":""MindDeathWave"",""uuid"":""MindDeathWave"",""keep"":""False"",""tracks"":[{""start_time"":0,""end_time"":100,""stop_name"":"""",""start_intensity"":20,""end_intensity"":40,""intensity_mode"":""Const"",""action_type"":""Shake"",""once"":""False"",""interval"":0,""index"":[5,6,3,2,7,1,0,4,8,9,10,11,15,14,13,12,16,17,19,18,116,117,118,119,115,114,113,112,108,109,110,111,107,106,104,105,100,101,102,103]},{""start_time"":0,""end_time"":0,""stop_name"":"""",""start_intensity"":30,""end_intensity"":20,""intensity_mode"":""Const"",""action_type"":""Electrical"",""once"":""True"",""interval"":1,""index"":[0,100]}]}";
    sendPlayByContent( jsonStr );
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `jsonStr `       | Effect Json Data              | Required |

## Connection Approch (C#)
### Import Files to Project
- Add the aar file to the project
### Add APK Privilege 
```java
    android.permission.WRITE EXTERNAL STORAGE
    android.permission.READ_EXTERNAL_STORAGE
    android.permission.BLUETOOTH_CONNECT
    android.permission.BLUETOOTH_SCAN
    android.permission.BLUETOOTH_PRIVILEGED
    android.permission.BLUETOOTH
    android.permission.BLUETOOTH_ADMIN
    android.permission.BLUETOOTH_ADVERTISE
    android.permission.BLUETOOTH_CONNECT
```
### Add Gradle Config:
```java
    dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.11.0'
    implementation 'com.google.flatbuffers:flatbuffers-java:24.3.25'**DEPS**}
```

- Initialize SDK Config
    load Script to the senario
    ```C#
    TruegearAndroidConnector androidConnector = new TruegearAndroidConnector();
    ```

- Register Effect，and export eff file:
    ```c#
    byte[] textFile_bytes = File.ReadAllBytes(filePath);
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    androidConnector.RegisterEffect_Eff(eventName, textFile_bytes);
    ```

    | Parameter          | Description                   | Required |
    |--------------------|-------------------------------|----------|
    | `eventName`        | Effect Name                   | Required |
    | `textFile_bytes`   | Bytes data of the file        | Required |

- Customized Json Data
    ```C#
    private string jsonStr = @"{""name"":""MindDeathWave"",""uuid"":""MindDeathWave"",""keep"":""False"",""tracks"":[{""start_time"":0,""end_time"":100,""stop_name"":"""",""start_intensity"":20,""end_intensity"":40,""intensity_mode"":""Const"",""action_type"":""Shake"",""once"":""False"",""interval"":0,""index"":[5,6,3,2,7,1,0,4,8,9,10,11,15,14,13,12,16,17,19,18,116,117,118,119,115,114,113,112,108,109,110,111,107,106,104,105,100,101,102,103]},{""start_time"":0,""end_time"":0,""stop_name"":"""",""start_intensity"":30,""end_intensity"":20,""intensity_mode"":""Const"",""action_type"":""Electrical"",""once"":""True"",""interval"":1,""index"":[0,100]}]}";
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    androidConnector.RegisterEffect(eventName, jsonStr);
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `eventName`      | Effect Name                   | Required |
    | `jsonStr`        | Effect Data                   | Required |

- Bluetooth Availability Detection:
    ```c#
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    Int res = androidConnector.IsAvailable()
    // Returns 1 if available, -1 if not available
    // -1 is not bluetooth privilege
    ```

- Avaliable Device Scan:
    ```c# 
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    androidConnector.StartScan();
    ```

- Get Avaliable Device:
    ```C#
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    public List<DeviceData> res = androidConnector.GetScanedDevices()
    ```

- Connect to Device:
    ```C#
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    androidConnector.ConnectToDevice(macAddress);
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `macAddress`     | Device's macAddress           | Required |

- Disconnect Device
    ```c#
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    androidConnector.DisconnectFromDevice();
    ```

- Send Event message to Trigger Effect:
    ```c#
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    androidConnector.SendPlayByEventName(eventName );
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `eventName`      | Registered Effect Name        | Required |

- Send Customized Json Data to Trigger Effect:
    ```C#
    string jsonStr = @"{""name"":""MindDeathWave"",""uuid"":""MindDeathWave"",""keep"":""False"",""tracks"":[{""start_time"":0,""end_time"":100,""stop_name"":"""",""start_intensity"":20,""end_intensity"":40,""intensity_mode"":""Const"",""action_type"":""Shake"",""once"":""False"",""interval"":0,""index"":[5,6,3,2,7,1,0,4,8,9,10,11,15,14,13,12,16,17,19,18,116,117,118,119,115,114,113,112,108,109,110,111,107,106,104,105,100,101,102,103]},{""start_time"":0,""end_time"":0,""stop_name"":"""",""start_intensity"":30,""end_intensity"":20,""intensity_mode"":""Const"",""action_type"":""Electrical"",""once"":""True"",""interval"":1,""index"":[0,100]}]}";    
    TruegearAndroidConnector androidConnector = TruegearAndroidConnector.Instance;
    androidConnector.SendPlayEffectByContent( jsonStr );
    ```
    | Parameter        | Description                   | Required |
    |------------------|-------------------------------|----------|
    | `jsonStr `       | Effect Json Data              | Required |