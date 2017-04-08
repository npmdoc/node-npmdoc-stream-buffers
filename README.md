# api documentation for  [stream-buffers (v3.0.1)](https://github.com/samcday/node-stream-buffer#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-stream-buffers.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-stream-buffers) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-stream-buffers.svg)](https://travis-ci.org/npmdoc/node-npmdoc-stream-buffers)
#### Buffer-backed Streams for reading and writing.

[![NPM](https://nodei.co/npm/stream-buffers.png?downloads=true)](https://www.npmjs.com/package/stream-buffers)

[![apidoc](https://npmdoc.github.io/node-npmdoc-stream-buffers/build/screenCapture.buildNpmdoc.browser.%252Fhome%252Ftravis%252Fbuild%252Fnpmdoc%252Fnode-npmdoc-stream-buffers%252Ftmp%252Fbuild%252Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-stream-buffers/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-stream-buffers/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-stream-buffers/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Sam Day",
        "email": "me@samcday.com.au"
    },
    "bugs": {
        "url": "https://github.com/samcday/node-stream-buffer/issues"
    },
    "dependencies": {},
    "description": "Buffer-backed Streams for reading and writing.",
    "devDependencies": {
        "chai": "^3.4.1",
        "eslint": "^1.9.0",
        "istanbul": "^0.4.0",
        "mocha": "^2.3.4"
    },
    "directories": {},
    "dist": {
        "shasum": "68a38c5faadeded79ff79988d368e3fb1325ef06",
        "tarball": "https://registry.npmjs.org/stream-buffers/-/stream-buffers-3.0.1.tgz"
    },
    "engines": {
        "node": ">= 0.10.0"
    },
    "gitHead": "f9f81526bf967c6cbcac4dc97307a08ca2edf768",
    "homepage": "https://github.com/samcday/node-stream-buffer#readme",
    "keywords": [
        "memory streams",
        "streams",
        "buffer streams"
    ],
    "license": "Unlicense",
    "main": "./lib/streambuffer.js",
    "maintainers": [
        {
            "name": "samcday",
            "email": "sam.c.day@gmail.com"
        }
    ],
    "name": "stream-buffers",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/samcday/node-stream-buffer.git"
    },
    "scripts": {
        "lint": "eslint .",
        "test": "istanbul test _mocha"
    },
    "version": "3.0.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module stream-buffers](#apidoc.module.stream-buffers)
1.  [function <span class="apidocSignatureSpan">stream-buffers.</span>ReadableStreamBuffer (opts)](#apidoc.element.stream-buffers.ReadableStreamBuffer)
1.  [function <span class="apidocSignatureSpan">stream-buffers.</span>WritableStreamBuffer (opts)](#apidoc.element.stream-buffers.WritableStreamBuffer)
1.  number <span class="apidocSignatureSpan">stream-buffers.</span>DEFAULT_CHUNK_SIZE
1.  number <span class="apidocSignatureSpan">stream-buffers.</span>DEFAULT_FREQUENCY
1.  number <span class="apidocSignatureSpan">stream-buffers.</span>DEFAULT_INCREMENT_AMOUNT
1.  number <span class="apidocSignatureSpan">stream-buffers.</span>DEFAULT_INITIAL_SIZE

#### [module stream-buffers.ReadableStreamBuffer](#apidoc.module.stream-buffers.ReadableStreamBuffer)
1.  [function <span class="apidocSignatureSpan">stream-buffers.</span>ReadableStreamBuffer (opts)](#apidoc.element.stream-buffers.ReadableStreamBuffer.ReadableStreamBuffer)
1.  [function <span class="apidocSignatureSpan">stream-buffers.ReadableStreamBuffer.</span>super_ (options)](#apidoc.element.stream-buffers.ReadableStreamBuffer.super_)

#### [module stream-buffers.WritableStreamBuffer](#apidoc.module.stream-buffers.WritableStreamBuffer)
1.  [function <span class="apidocSignatureSpan">stream-buffers.</span>WritableStreamBuffer (opts)](#apidoc.element.stream-buffers.WritableStreamBuffer.WritableStreamBuffer)
1.  [function <span class="apidocSignatureSpan">stream-buffers.WritableStreamBuffer.</span>super_ (options)](#apidoc.element.stream-buffers.WritableStreamBuffer.super_)



# <a name="apidoc.module.stream-buffers"></a>[module stream-buffers](#apidoc.module.stream-buffers)

#### <a name="apidoc.element.stream-buffers.ReadableStreamBuffer"></a>[function <span class="apidocSignatureSpan">stream-buffers.</span>ReadableStreamBuffer (opts)](#apidoc.element.stream-buffers.ReadableStreamBuffer)
- description and source-code
```javascript
ReadableStreamBuffer = function (opts) {
  var that = this;
  opts = opts || {};

  stream.Readable.call(this, opts);

  this.stopped = false;

  var frequency = opts.hasOwnProperty('frequency') ? opts.frequency : constants.DEFAULT_FREQUENCY;
  var chunkSize = opts.chunkSize || constants.DEFAULT_CHUNK_SIZE;
  var initialSize = opts.initialSize || constants.DEFAULT_INITIAL_SIZE;
  var incrementAmount = opts.incrementAmount || constants.DEFAULT_INCREMENT_AMOUNT;

  var size = 0;
  var buffer = new Buffer(initialSize);

  var sendData = function() {
    var amount = Math.min(chunkSize, size);
    var sendMore = false;

    if (amount > 0) {
      var chunk = null;
      chunk = new Buffer(amount);
      buffer.copy(chunk, 0, 0, amount);

      sendMore = that.push(chunk) !== false;

      buffer.copy(buffer, 0, amount, size);
      size -= amount;
    }

    if(size === 0 && that.stopped) {
      that.push(null);
    }

    if (sendMore) {
      sendData.timeout = setTimeout(sendData, frequency);
    }
    else {
      sendData.timeout = null;
    }
  };

  this.stop = function() {
    if (this.stopped) {
      throw new Error('stop() called on already stopped ReadableStreamBuffer');
    }
    this.stopped = true;

    if (size === 0) {
      this.push(null);
    }
  };

  this.size = function() {
    return size;
  };

  this.maxSize = function() {
    return buffer.length;
  };

  var increaseBufferIfNecessary = function(incomingDataSize) {
    if((buffer.length - size) < incomingDataSize) {
      var factor = Math.ceil((incomingDataSize - (buffer.length - size)) / incrementAmount);

      var newBuffer = new Buffer(buffer.length + (incrementAmount * factor));
      buffer.copy(newBuffer, 0, 0, size);
      buffer = newBuffer;
    }
  };

  this.put = function(data, encoding) {
    if (that.stopped) {
      throw new Error('Tried to write data to a stopped ReadableStreamBuffer');
    }

    if(Buffer.isBuffer(data)) {
      increaseBufferIfNecessary(data.length);
      data.copy(buffer, size, 0);
      size += data.length;
    }
    else {
      data = data + '';
      var dataSizeInBytes = Buffer.byteLength(data);
      increaseBufferIfNecessary(dataSizeInBytes);
      buffer.write(data, size, encoding || 'utf8');
      size += dataSizeInBytes;
    }
  };

  this._read = function() {
    if (!sendData.timeout) {
      sendData.timeout = setTimeout(sendData, frequency);
    }
  };
}
```
- example usage
```shell
...
### ReadableStreamBuffer

'ReadableStreamBuffer' implements the standard ['stream.Readable'](https://nodejs.org/api/stream.html#stream_class_stream_readable
), but can have data inserted into it. This data will then be pumped out in chunks as readable events. The data to be sent out is
 held in a Buffer, which can grow in much the same way as a 'WritableStreamBuffer' does, if data is being put in Buffer faster than
 it is being pumped out.

The frequency in which chunks are pumped out, and the size of the chunks themselves can be configured in the constructor. The initial
 size and increment amount of internal Buffer can be configured too. In the following example 2kb chunks will be output every 10
 milliseconds:

'''js
var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
	frequency: 10,   // in milliseconds.
	chunkSize: 2048  // in bytes.
});
'''

Default frequency and chunk size:
...
```

#### <a name="apidoc.element.stream-buffers.WritableStreamBuffer"></a>[function <span class="apidocSignatureSpan">stream-buffers.</span>WritableStreamBuffer (opts)](#apidoc.element.stream-buffers.WritableStreamBuffer)
- description and source-code
```javascript
WritableStreamBuffer = function (opts) {
  opts = opts || {};
  opts.decodeStrings = true;

  stream.Writable.call(this, opts);

  var initialSize = opts.initialSize || constants.DEFAULT_INITIAL_SIZE;
  var incrementAmount = opts.incrementAmount || constants.DEFAULT_INCREMENT_AMOUNT;

  var buffer = new Buffer(initialSize);
  var size = 0;

  this.size = function() {
    return size;
  };

  this.maxSize = function() {
    return buffer.length;
  };

  this.getContents = function(length) {
    if(!size) return false;

    var data = new Buffer(Math.min(length || size, size));
    buffer.copy(data, 0, 0, data.length);

    if(data.length < size)
      buffer.copy(buffer, 0, data.length);

    size -= data.length;

    return data;
  };

  this.getContentsAsString = function(encoding, length) {
    if(!size) return false;

    var data = buffer.toString(encoding || 'utf8', 0, Math.min(length || size, size));
    var dataLength = Buffer.byteLength(data);

    if(dataLength < size)
      buffer.copy(buffer, 0, dataLength);

    size -= dataLength;
    return data;
  };

  var increaseBufferIfNecessary = function(incomingDataSize) {
    if((buffer.length - size) < incomingDataSize) {
      var factor = Math.ceil((incomingDataSize - (buffer.length - size)) / incrementAmount);

      var newBuffer = new Buffer(buffer.length + (incrementAmount * factor));
      buffer.copy(newBuffer, 0, 0, size);
      buffer = newBuffer;
    }
  };

  this._write = function(chunk, encoding, callback) {
    increaseBufferIfNecessary(chunk.length);
    chunk.copy(buffer, size, 0);
    size += chunk.length;
    callback();
  };
}
```
- example usage
```shell
...
'''

### WritableStreamBuffer

'WritableStreamBuffer' implements the standard ['stream.Writable'](https://nodejs.org/api/stream.html#stream_class_stream_writable
) interface. All writes to this stream will accumulate in an internal ['Buffer'](https://nodejs.org/api/buffer.html). If the internal
 buffer overflows it will be resized automatically. The initial size of the Buffer and the amount in which it grows can be configured
 in the constructor.

'''js
var myWritableStreamBuffer = new streamBuffers.WritableStreamBuffer({
	initialSize: (100 * 1024),   // start at 100 kilobytes.
	incrementAmount: (10 * 1024) // grow by 10 kilobytes each time buffer overflows.
});
'''

The default initial size and increment amount are stored in the following constants:
...
```



# <a name="apidoc.module.stream-buffers.ReadableStreamBuffer"></a>[module stream-buffers.ReadableStreamBuffer](#apidoc.module.stream-buffers.ReadableStreamBuffer)

#### <a name="apidoc.element.stream-buffers.ReadableStreamBuffer.ReadableStreamBuffer"></a>[function <span class="apidocSignatureSpan">stream-buffers.</span>ReadableStreamBuffer (opts)](#apidoc.element.stream-buffers.ReadableStreamBuffer.ReadableStreamBuffer)
- description and source-code
```javascript
ReadableStreamBuffer = function (opts) {
  var that = this;
  opts = opts || {};

  stream.Readable.call(this, opts);

  this.stopped = false;

  var frequency = opts.hasOwnProperty('frequency') ? opts.frequency : constants.DEFAULT_FREQUENCY;
  var chunkSize = opts.chunkSize || constants.DEFAULT_CHUNK_SIZE;
  var initialSize = opts.initialSize || constants.DEFAULT_INITIAL_SIZE;
  var incrementAmount = opts.incrementAmount || constants.DEFAULT_INCREMENT_AMOUNT;

  var size = 0;
  var buffer = new Buffer(initialSize);

  var sendData = function() {
    var amount = Math.min(chunkSize, size);
    var sendMore = false;

    if (amount > 0) {
      var chunk = null;
      chunk = new Buffer(amount);
      buffer.copy(chunk, 0, 0, amount);

      sendMore = that.push(chunk) !== false;

      buffer.copy(buffer, 0, amount, size);
      size -= amount;
    }

    if(size === 0 && that.stopped) {
      that.push(null);
    }

    if (sendMore) {
      sendData.timeout = setTimeout(sendData, frequency);
    }
    else {
      sendData.timeout = null;
    }
  };

  this.stop = function() {
    if (this.stopped) {
      throw new Error('stop() called on already stopped ReadableStreamBuffer');
    }
    this.stopped = true;

    if (size === 0) {
      this.push(null);
    }
  };

  this.size = function() {
    return size;
  };

  this.maxSize = function() {
    return buffer.length;
  };

  var increaseBufferIfNecessary = function(incomingDataSize) {
    if((buffer.length - size) < incomingDataSize) {
      var factor = Math.ceil((incomingDataSize - (buffer.length - size)) / incrementAmount);

      var newBuffer = new Buffer(buffer.length + (incrementAmount * factor));
      buffer.copy(newBuffer, 0, 0, size);
      buffer = newBuffer;
    }
  };

  this.put = function(data, encoding) {
    if (that.stopped) {
      throw new Error('Tried to write data to a stopped ReadableStreamBuffer');
    }

    if(Buffer.isBuffer(data)) {
      increaseBufferIfNecessary(data.length);
      data.copy(buffer, size, 0);
      size += data.length;
    }
    else {
      data = data + '';
      var dataSizeInBytes = Buffer.byteLength(data);
      increaseBufferIfNecessary(dataSizeInBytes);
      buffer.write(data, size, encoding || 'utf8');
      size += dataSizeInBytes;
    }
  };

  this._read = function() {
    if (!sendData.timeout) {
      sendData.timeout = setTimeout(sendData, frequency);
    }
  };
}
```
- example usage
```shell
...
### ReadableStreamBuffer

'ReadableStreamBuffer' implements the standard ['stream.Readable'](https://nodejs.org/api/stream.html#stream_class_stream_readable
), but can have data inserted into it. This data will then be pumped out in chunks as readable events. The data to be sent out is
 held in a Buffer, which can grow in much the same way as a 'WritableStreamBuffer' does, if data is being put in Buffer faster than
 it is being pumped out.

The frequency in which chunks are pumped out, and the size of the chunks themselves can be configured in the constructor. The initial
 size and increment amount of internal Buffer can be configured too. In the following example 2kb chunks will be output every 10
 milliseconds:

'''js
var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
	frequency: 10,   // in milliseconds.
	chunkSize: 2048  // in bytes.
});
'''

Default frequency and chunk size:
...
```

#### <a name="apidoc.element.stream-buffers.ReadableStreamBuffer.super_"></a>[function <span class="apidocSignatureSpan">stream-buffers.ReadableStreamBuffer.</span>super_ (options)](#apidoc.element.stream-buffers.ReadableStreamBuffer.super_)
- description and source-code
```javascript
function Readable(options) {
  if (!(this instanceof Readable))
    return new Readable(options);

  this._readableState = new ReadableState(options, this);

  // legacy
  this.readable = true;

  if (options && typeof options.read === 'function')
    this._read = options.read;

  Stream.call(this);
}
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.stream-buffers.WritableStreamBuffer"></a>[module stream-buffers.WritableStreamBuffer](#apidoc.module.stream-buffers.WritableStreamBuffer)

#### <a name="apidoc.element.stream-buffers.WritableStreamBuffer.WritableStreamBuffer"></a>[function <span class="apidocSignatureSpan">stream-buffers.</span>WritableStreamBuffer (opts)](#apidoc.element.stream-buffers.WritableStreamBuffer.WritableStreamBuffer)
- description and source-code
```javascript
WritableStreamBuffer = function (opts) {
  opts = opts || {};
  opts.decodeStrings = true;

  stream.Writable.call(this, opts);

  var initialSize = opts.initialSize || constants.DEFAULT_INITIAL_SIZE;
  var incrementAmount = opts.incrementAmount || constants.DEFAULT_INCREMENT_AMOUNT;

  var buffer = new Buffer(initialSize);
  var size = 0;

  this.size = function() {
    return size;
  };

  this.maxSize = function() {
    return buffer.length;
  };

  this.getContents = function(length) {
    if(!size) return false;

    var data = new Buffer(Math.min(length || size, size));
    buffer.copy(data, 0, 0, data.length);

    if(data.length < size)
      buffer.copy(buffer, 0, data.length);

    size -= data.length;

    return data;
  };

  this.getContentsAsString = function(encoding, length) {
    if(!size) return false;

    var data = buffer.toString(encoding || 'utf8', 0, Math.min(length || size, size));
    var dataLength = Buffer.byteLength(data);

    if(dataLength < size)
      buffer.copy(buffer, 0, dataLength);

    size -= dataLength;
    return data;
  };

  var increaseBufferIfNecessary = function(incomingDataSize) {
    if((buffer.length - size) < incomingDataSize) {
      var factor = Math.ceil((incomingDataSize - (buffer.length - size)) / incrementAmount);

      var newBuffer = new Buffer(buffer.length + (incrementAmount * factor));
      buffer.copy(newBuffer, 0, 0, size);
      buffer = newBuffer;
    }
  };

  this._write = function(chunk, encoding, callback) {
    increaseBufferIfNecessary(chunk.length);
    chunk.copy(buffer, size, 0);
    size += chunk.length;
    callback();
  };
}
```
- example usage
```shell
...
'''

### WritableStreamBuffer

'WritableStreamBuffer' implements the standard ['stream.Writable'](https://nodejs.org/api/stream.html#stream_class_stream_writable
) interface. All writes to this stream will accumulate in an internal ['Buffer'](https://nodejs.org/api/buffer.html). If the internal
 buffer overflows it will be resized automatically. The initial size of the Buffer and the amount in which it grows can be configured
 in the constructor.

'''js
var myWritableStreamBuffer = new streamBuffers.WritableStreamBuffer({
	initialSize: (100 * 1024),   // start at 100 kilobytes.
	incrementAmount: (10 * 1024) // grow by 10 kilobytes each time buffer overflows.
});
'''

The default initial size and increment amount are stored in the following constants:
...
```

#### <a name="apidoc.element.stream-buffers.WritableStreamBuffer.super_"></a>[function <span class="apidocSignatureSpan">stream-buffers.WritableStreamBuffer.</span>super_ (options)](#apidoc.element.stream-buffers.WritableStreamBuffer.super_)
- description and source-code
```javascript
function Writable(options) {
  // Writable ctor is applied to Duplexes, too.
  // 'realHasInstance' is necessary because using plain 'instanceof'
  // would return false, as no '_writableState' property is attached.

  // Trying to use the custom 'instanceof' for Writable here will also break the
  // Node.js LazyTransform implementation, which has a non-trivial getter for
  // '_writableState' that would lead to infinite recursion.
  if (!(realHasInstance.call(Writable, this)) &&
      !(this instanceof Stream.Duplex)) {
    return new Writable(options);
  }

  this._writableState = new WritableState(options, this);

  // legacy.
  this.writable = true;

  if (options) {
    if (typeof options.write === 'function')
      this._write = options.write;

    if (typeof options.writev === 'function')
      this._writev = options.writev;
  }

  Stream.call(this);
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
