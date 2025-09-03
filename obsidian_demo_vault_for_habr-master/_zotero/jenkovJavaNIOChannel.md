---
date: 
tags: 
authors: Jakob Jenkov

Abstract:  This tutorial explains how Java NIO Channels are used to open network connections and connections to files.
---

### Java NIO Channel
[]

### Notes

**Java NIO Channel**

Java NIO Channels are similar to streams with a few differences:

You can both read and write to a Channels. Streams are typically one-way (read or write).
Channels can be read and written asynchronously.
Channels always read to, or write from, a Buffer.

As mentioned above, you read data from a channel into a buffer, and write data from a buffer into a channel. Here is an illustration of that:

# Java NIO: Channels read data into Buffers, and Buffers write data into Channels
([| link to the section](zotero://open-pdf/library/items/QGGXNEXQ?page=&annotation=6VZUYY2F))

## Basic Channel Example
([](zotero://open-pdf/library/items/QGGXNEXQ?page=&annotation=BSE6MM84))

Here is a basic example that uses a

`FileChannel`

to read some data into a

`Buffer`

:

```
RandomAccessFile aFile = new RandomAccessFile(&quot;data/nio-data.txt&quot;, &quot;rw&quot;); FileChannel inChannel = aFile.getChannel(); ByteBuffer buf = ByteBuffer.allocate(48); int bytesRead = inChannel.read(buf); while (bytesRead != -1) { System.out.println(&quot;Read &quot; + bytesRead); buf.flip(); while(buf.hasRemaining()){ System.out.print((char) buf.get()); } buf.clear(); bytesRead = inChannel.read(buf); } aFile.close();
```

Notice the

`buf.flip()`

call. First you read into a Buffer. Then you flip it. Then you read out of it. I&#39;ll get into more detail about that in the next text about

`Buffer`

&#39;s.
