# Store compressed text in Firestore databases

Unishox text compression can be used to store more data than the 1GB free limit in Firestore database by storing text content as a Blob.  For example, 

```Javascript
var usx = require("./unishox2.js")
var my_str = "The quick brown fox jumped over the lazy dog";
var out_buf = new Uint8Array(100); // A buffer with arbitrary length
var out_len = usx.unishox2_compress_simple(my_str, my_str.length, out_buf);
firebase.firestore().collection("/my-collection").add({
  myCompressedBlob: firebase.firestore.Blob.fromUint8Array(out_buf.slice(0, out_len))
});
```

This library can be installed from npm package repository using `npm i unishox2.siara.cc`.  The source code can be found at https://github.com/siara-cc/unishox_js.

Since the size of compressed content will be smaller than the input text, more than 1GB data can be compressed and stored into Firestore.  The compression ratio will depend on the type of text that is being compressed.

Unishox can get good compression for any type of UTF-8 text compositions such as English sentences, Latin and other languages.  The picture shown below gives an idea of how much compression can be achieved for each language:

![Promo Picture](https://github.com/siara-cc/unishox_js/blob/master/demo/Banner1.png?raw=true)

# How it works

Unishox is an hybrid encoder (entropy, dictionary and delta coding).  It works by assigning fixed prefix-free codes for each letter in the above Character Set (entropy coding).  It also encodes repeating letter sets separately (dictionary coding).  For Unicode characters, delta coding is used.

The model used for arriving at the prefix-free code is shown below:

![Promo video](https://github.com/siara-cc/Unishox/blob/master/demo/model.png?raw=true)

The complete specification can be found in this article: [Unishox 2 - Guaranteed Configurable Compression for Short Strings using Entropy, Dictionary and Delta encoding techniques](https://github.com/siara-cc/Unishox/blob/master/Unishox_Article_2.pdf?raw=true).

## Using it in your application

To compress and decompress strings in your application, import `unishox2.js`:

```Javascript
var usx = require("./unishox2.js");
```

The function expects a Javascript `string` or `Uint8Array` as Input and Output an `Uint8Array`.

### Simple API

In its simplest form, just pass the string you want to compress, its length and a UintArray to receive the compressed contents.  While the library can make the output array, it is faster to supply it.  Since this technology guarantees compression, the output buffer needs be only as lengthy as the input string 99.99% of times:

```Javascript
var usx = require("./unishox2.js")
var my_str = "The quick brown fox jumped over the lazy dog";
var out_buf = new Uint8Array(100); // A buffer with arbitrary length
var out_len = usx.unishox2_compress_simple(my_str, my_str.length, out_buf);
var out_str = usx.unishox2_decompress_simple(out_buf, out_len);
console.log(out_str);
```

As shown above, the original string can be obtained by passing the UintArray and length to `unishox2_decompress_simple`.

# Issues

In case of any issues, please email the Author (Arundale Ramanathan) at arun@siara.cc or create GitHub issue.
