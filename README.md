#Pili server-side library for JAVA

##Installation
You can download **pili-sdk-java-v1.4.0.jar** file in the **release** folder.

##dependency
You also need [okhttp][1], [okio][2], [Gson][3]

[1]: http://square.github.io/okhttp/
[2]: https://github.com/square/okio
[3]: https://code.google.com/p/google-gson/downloads/detail?name=google-gson-2.2.4-release.zip&

##Runtime Requirement
For Java, the minimum requirement is 1.7.

If you want to run the SDK on JDK 1.6 environment, you can download the compatible jar of  [okhttp](https://raw.githubusercontent.com/qiniu/java-sdk/master/libs/okhttp-2.3.0-SNAPSHOT.jar) and [okio](https://raw.githubusercontent.com/qiniu/java-sdk/master/libs/okio-1.3.0-SNAPSHOT.jar).

##Usage
###Configuration
```JAVA
  // Replace with your keys
  public static final String ACCESS_KEY = "QiniuAccessKey";
  public static final String SECRET_KEY = "QiniuSecretKey";
  
  // Replace with your hub name
  public static final String HUB = "hubName";
```

###Instantiate a Pili client
```JAVA
import com.pili.Pili;
...

  Pili mPili = new Pili(ACCESS_KEY, SECRET_KEY, HUB);

```

###Create a new stream
```JAVA
import com.pili.Stream;
import com.pili.PiliException;

...
  String title           = null;            // optional, default is auto-generated. Setting title to null or "" or " ", default you choosed. The length of title should be at least 5 and at most 200. A title should be unique.
  String publishKey      = null;            // optional, a secret key for signing the <publishToken>, default is   auto-generated. Setting publishKey to null or "" or " ", default you choosed.
  String publishSecurity = null;            // optional, can be "dynamic" or "static", default is "dynamic"
  try {
    Stream stream = mPili.createStream(title, publishKey, publishSecurity);
  } catch (PiliException e) {
    e.printStackTrace();
  }
```
or
```JAVA
  try {
    Stream stream = mPili.createStream();
    printStream(stream);
  } catch (PiliException e) {
    e.printStackTrace();
  }
```

###Get an exist stream
```JAVA
  try {
    Stream retStream = mPili.getStream(mStream.getStreamId());
    printStream(retStream);
  } catch (PiliException e) {
    e.printStackTrace();
  }
```

###List streams
```JAVA
import com.pili.Stream.StreamList;
  ...
  String marker = null;          // optional. Setting marker to null or "" or " ", default you choosed.
  long limit    = 0;             // optional. Setting limit to value(<=0), default you choosed.
  
  try {
      StreamList list = mPili.listStreams(marker, limit);
      if (list != null) {
          for (Stream stream : list.getStreams()) {
              printStream(stream);
          }
      }
  } catch (PiliException e) {
      e.printStackTrace();
  }
```
or
```JAVA
  try {
      StreamList list = mPili.listStreams();
      if (list != null) {
          for (Stream stream : list.getStreams()) {
              printStream(stream);
          }
      }
  } catch (PiliException e) {
      e.printStackTrace();
  }
```

###Get recording segments from an exist stream
```JAVA
import com.pili.Stream.SegmentList;
...
  long startSecond = 0; // optional. Setting startSecond to value(<=0), default you choosed.
  long endSecond  = 0;  // optional. Setting endSecond to value(<=0), default you choosed.
  try {
      SegmentList ssList = mStream.segments(startSecond, endSecond);
      if (ssList != null) {
          List<Segment> list = ssList.getSegmentList();
          for (Segment ss : list) {
              System.out.println(ss.getStart() + "," + ss.getEnd());
          }
      }
  } catch (PiliException e) {
      e.printStackTrace();
  }
```
or
```JAVA
  try {
      SegmentList ssList = mStream.segments();
      if (ssList != null) {
          List<Segment> list = ssList.getSegmentList();
          for (Segment ss : list) {
              System.out.println(ss.getStart() + "," + ss.getEnd());
          }
      }
  } catch (PiliException e) {
      e.printStackTrace();
  }
```

###Get Stream Status
```JAVA
  try {
      Status streamStatus = mStream.status();
      System.out.println("addr:" + streamStatus.getAddr() + ", status:" + streamStatus.getStatus());
  } catch (PiliException e) {
      e.printStackTrace();
  }
```
###Update an exist stream
```JAVA
  String newPublishKey      = "new_secret_words";
  String newPublishSecurity = "dynamic";
  boolean disabled = false;

  try {
      Stream retStream = mStream.updateStream(newPublishKey, newPublishSecurity, disabled);
      printStream(retStream);
  } catch (PiliException e) {
      e.printStackTrace();
  }
```
###Delete stream
```JAVA
  try {
      String retValue = mStream.delete();
  } catch (PiliException e) {
      e.printStackTrace();
  }
```

###Enable a stream
```JAVA
try {
    stream.enable();
} catch (PiliException e) {
    e.printStackTrace();
}
```

###Disable a stream
```JAVA
try {
    stream.disable();
} catch (PiliException e) {
    e.printStackTrace();
}
```

###Generate RTMP publish URL
```JAVA
  try {
      String publishUrl = mStream.rtmpPublishUrl();
  } catch (PiliException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
  }
```

###Generate RTMP live play URLs
```JAVA
  Map<String, String> rtmpLiveUrls = mStream.rtmpLiveUrls();
  String originRtmpLiveUrl = rtmpLiveUrls.get(Stream.ORIGIN);     // Get original RTMP live url
  for (String key : rtmpLiveUrls.keySet()) {
    System.out.println("key:" + key + ", rtmpLiveUrl:" + rtmpLiveUrls.get(key));
  }
```

###Generate HLS live play URLs
```JAVA
  Map<String, String> hlsLiveUrls = mStream.hlsLiveUrls();
  String originHlsLiveUrl = hlsLiveUrls.get(Stream.ORIGIN);       // Get original HLS live url
  for (String key : hlsLiveUrls.keySet()) {
    System.out.println("key:" + key + ", hlsLiveUrl:" + hlsLiveUrls.get(key));
  }
```

###Generate HLS playback URLs
```JAVA
  // startSecond and endSecond should be llegal(>0) and startSecond < endSecond, otherwise PiliException will be thrown
  // the unit of startSecond and endSecond is second.
  try {
    Map<String, String> hlsPlaybackUrls = mStream.hlsPlaybackUrls(startSecond, endSecond);
    String originPlaybackUrl = hlsPlaybackUrls.get(Stream.ORIGIN); // Get original HLS playback url
    for (String key : hlsPlaybackUrls.keySet()) {
      System.out.println("key:" + key + ", hlsPlaybackUrls:" + hlsPlaybackUrls.get(key));
    }
  } catch (PiliException e) {
    e.printStackTrace();
  }
```

###Generate FLV URLs
```JAVA
  Map<String, String> flvLiveUrls = mStream.httpFlvLiveUrls();
  String originFlvLiveUrl = flvLiveUrls.get(Stream.ORIGIN);       // Get original FLV live url
  for (String key : flvLiveUrls.keySet()) {
    System.out.println("key:" + key + ", flvLiveUrls:" + flvLiveUrls.get(key));
  }
```

httpFlvLiveUrls

###To JSON String
```JAVA
String streamJsonStr = mStream.toJsonString();
System.out.println(streamJsonStr);
```
###Save Stream as
```JAVA
String fileName;
String format;
long startSecond, endSecond;

String notifyUrl = "http://your_notify_url";

try {
    SaveAsResponse resp = stream.saveAs(fileName, format, startSecond, endSecond, notifyUrl);

    // You can get processing state via Qiniu fop service by persistentId.
    // API: `curl -D GET http://api.qiniu.com/status/get/prefop?id=<PersistentId>`
    // Doc reference: `http://developer.qiniu.com/docs/v6/api/overview/fop/persistent-fop.html#pfop-status`
    System.out.println("saveAs resp.getUrl:" + resp.getUrl() + ",resp.getTargetUrl:" + resp.getTargetUrl() + ",resp.getPersistentId:" + resp.getPersistentId());
} catch (PiliException e) {
    e.printStackTrace();
}
```
or
```JAVA
String fileName;
String format;
long startSecond, endSecond;
try {
    SaveAsResponse resp = stream.saveAs(fileName, format, startSecond, endSecond);
    System.out.println("saveAs resp.getUrl:" + resp.getUrl() + ",resp.getTargetUrl:" + resp.getTargetUrl() + ",resp.getPersistentId:" + resp.getPersistentId());
} catch (PiliException e) {
    e.printStackTrace();
}
```

###Snapshot stream
```JAVA
String fileName;
String format;
long time; // second

String notifyUrl = "http://your_notify_url";
SnapshotResponse resp;
try {
  SnapshotResponse resp = stream.snapshot(fileName, format);
  // You can get processing state via Qiniu fop service by persistentId.
  // API: `curl -D GET http://api.qiniu.com/status/get/prefop?id=<PersistentId>`
  // Doc reference: `http://developer.qiniu.com/docs/v6/api/overview/fop/persistent-fop.html#pfop-status`
  System.out.println("snapshot resp.getTargetUrl:" + resp.getTargetUrl() + ",resp.getPersistentId:" + resp.getPersistentId());
} catch (PiliException e) {
    e.printStackTrace();
}
```
or

```JAVA
String fileName;
String format;
long time;

try {
    SnapshotResponse resp = stream.snapshot(fileName, format, time, notifyUrl);
    System.out.println("snapshot resp.getTargetUrl:" + resp.getTargetUrl() + ",resp.getPersistentId:" + resp.getPersistentId());
} catch (PiliException e) {
    e.printStackTrace();
}
```

##History
- 1.4.0
  - Update `Stream` class
  - Update `Stream$Status` class
  - Add `Stream$SnapshotResponse` class
  - Add `Stream$FramesPerSecond` class
  - Add `snapshot` interface
  - Add `enable` interface
  - Add `disable` interface
  - Add `httpFlvLiveUrls` interface
  - Add the test code for the new API
