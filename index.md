<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="description" content="Universal Open Source Client-Side Paper Wallet Generator for BitCoins and other cryptocurrencies. Create your own paper wallet in a few easy steps : Generate, Print and Fold !">
  <meta name="keywords" content="universal, paper, wallet, generator, cryptocurrencies, bitcoin, litecoin, dogecoin" />

  <!--

Notice of Copyrights and Licenses:
---------------------------------------
The WalletGenerator.net project, software and embedded resources are copyright WalletGenerator.net.
The WalletGenerator.net name and logo are not part of the open source license.

Portions of the all-in-one HTML document contain JavaScript codes that are the copyrights of others.
The individual copyrights are included throughout the document along with their licenses. Included
JavaScript libraries are separated with HTML script tags.

Summary of JavaScript functions with a redistributable license:
JavaScript function   License
-------------------   --------------
Array.prototype.map   Public Domain
window.Crypto         BSD License
window.SecureRandom   BSD License
window.EllipticCurve  BSD License
window.BigInteger     BSD License
window.QRCode         MIT License
window.Bitcoin        MIT License
jsqrcode              Apache License, 2.0


The WalletGenerator.net software is available under The MIT License (MIT)
Copyright (c) 2014 WalletGenerator.net

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and
associated documentation files (the "Software"), to deal in the Software without restriction,
including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense,
and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial
portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT
NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


  GitHub Repository: https://github.com/MichaelMure/WalletGenerator.net
  -->

  <title>WalletGenerator.net - Universal Paper wallet generator for Bitcoin and other cryptocurrencies</title>

  <script type="text/javascript">
// Array.prototype.map function is in the public domain.
// Production steps of ECMA-262, Edition 5, 15.4.4.19
// Reference: http://es5.github.com/#x15.4.4.19
if (!Array.prototype.map) {
	Array.prototype.map = function (callback, thisArg) {
		var T, A, k;
		if (this == null) {
			throw new TypeError(" this is null or not defined");
		}
		// 1. Let O be the result of calling ToObject passing the |this| value as the argument.
		var O = Object(this);
		// 2. Let lenValue be the result of calling the Get internal method of O with the argument "length".
		// 3. Let len be ToUint32(lenValue).
		var len = O.length >>> 0;
		// 4. If IsCallable(callback) is false, throw a TypeError exception.
		// See: http://es5.github.com/#x9.11
		if ({}.toString.call(callback) != "[object Function]") {
			throw new TypeError(callback + " is not a function");
		}
		// 5. If thisArg was supplied, let T be thisArg; else let T be undefined.
		if (thisArg) {
			T = thisArg;
		}
		// 6. Let A be a new array created as if by the expression new Array(len) where Array is
		// the standard built-in constructor with that name and len is the value of len.
		A = new Array(len);
		// 7. Let k be 0
		k = 0;
		// 8. Repeat, while k < len
		while (k < len) {
			var kValue, mappedValue;
			// a. Let Pk be ToString(k).
			//   This is implicit for LHS operands of the in operator
			// b. Let kPresent be the result of calling the HasProperty internal method of O with argument Pk.
			//   This step can be combined with c
			// c. If kPresent is true, then
			if (k in O) {
				// i. Let kValue be the result of calling the Get internal method of O with argument Pk.
				kValue = O[k];
				// ii. Let mappedValue be the result of calling the Call internal method of callback
				// with T as the this value and argument list containing kValue, k, and O.
				mappedValue = callback.call(T, kValue, k, O);
				// iii. Call the DefineOwnProperty internal method of A with arguments
				// Pk, Property Descriptor {Value: mappedValue, Writable: true, Enumerable: true, Configurable: true},
				// and false.
				// In browsers that support Object.defineProperty, use the following:
				// Object.defineProperty(A, Pk, { value: mappedValue, writable: true, enumerable: true, configurable: true });
				// For best browser support, use the following:
				A[k] = mappedValue;
			}
			// d. Increase k by 1.
			k++;
		}
		// 9. return A
		return A;
	};
}
  </script>
  <script type="text/javascript">
/*!
* Crypto-JS v2.5.4	Crypto.js
* http://code.google.com/p/crypto-js/
* Copyright (c) 2009-2013, Jeff Mott. All rights reserved.
* http://code.google.com/p/crypto-js/wiki/License
*/
if (typeof Crypto == "undefined" || !Crypto.util) {
	(function () {

		var base64map = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";

		// Global Crypto object
		var Crypto = window.Crypto = {};

		// Crypto utilities
		var util = Crypto.util = {

			// Bit-wise rotate left
			rotl: function (n, b) {
				return (n << b) | (n >>> (32 - b));
			},

			// Bit-wise rotate right
			rotr: function (n, b) {
				return (n << (32 - b)) | (n >>> b);
			},

			// Swap big-endian to little-endian and vice versa
			endian: function (n) {

				// If number given, swap endian
				if (n.constructor == Number) {
					return util.rotl(n, 8) & 0x00FF00FF |
			    util.rotl(n, 24) & 0xFF00FF00;
				}

				// Else, assume array and swap all items
				for (var i = 0; i < n.length; i++)
					n[i] = util.endian(n[i]);
				return n;

			},

			// Generate an array of any length of random bytes
			randomBytes: function (n) {
				for (var bytes = []; n > 0; n--)
					bytes.push(Math.floor(Math.random() * 256));
				return bytes;
			},

			// Convert a byte array to big-endian 32-bit words
			bytesToWords: function (bytes) {
				for (var words = [], i = 0, b = 0; i < bytes.length; i++, b += 8)
					words[b >>> 5] |= (bytes[i] & 0xFF) << (24 - b % 32);
				return words;
			},

			// Convert big-endian 32-bit words to a byte array
			wordsToBytes: function (words) {
				for (var bytes = [], b = 0; b < words.length * 32; b += 8)
					bytes.push((words[b >>> 5] >>> (24 - b % 32)) & 0xFF);
				return bytes;
			},

			// Convert a byte array to a hex string
			bytesToHex: function (bytes) {
				for (var hex = [], i = 0; i < bytes.length; i++) {
					hex.push((bytes[i] >>> 4).toString(16));
					hex.push((bytes[i] & 0xF).toString(16));
				}
				return hex.join("");
			},

			// Convert a hex string to a byte array
			hexToBytes: function (hex) {
				for (var bytes = [], c = 0; c < hex.length; c += 2)
					bytes.push(parseInt(hex.substr(c, 2), 16));
				return bytes;
			},

			// Convert a byte array to a base-64 string
			bytesToBase64: function (bytes) {
				for (var base64 = [], i = 0; i < bytes.length; i += 3) {
					var triplet = (bytes[i] << 16) | (bytes[i + 1] << 8) | bytes[i + 2];
					for (var j = 0; j < 4; j++) {
						if (i * 8 + j * 6 <= bytes.length * 8)
							base64.push(base64map.charAt((triplet >>> 6 * (3 - j)) & 0x3F));
						else base64.push("=");
					}
				}

				return base64.join("");
			},

			// Convert a base-64 string to a byte array
			base64ToBytes: function (base64) {
				// Remove non-base-64 characters
				base64 = base64.replace(/[^A-Z0-9+\/]/ig, "");

				for (var bytes = [], i = 0, imod4 = 0; i < base64.length; imod4 = ++i % 4) {
					if (imod4 == 0) continue;
					bytes.push(((base64map.indexOf(base64.charAt(i - 1)) & (Math.pow(2, -2 * imod4 + 8) - 1)) << (imod4 * 2)) |
			        (base64map.indexOf(base64.charAt(i)) >>> (6 - imod4 * 2)));
				}

				return bytes;
			}

		};

		// Crypto character encodings
		var charenc = Crypto.charenc = {};

		// UTF-8 encoding
		var UTF8 = charenc.UTF8 = {

			// Convert a string to a byte array
			stringToBytes: function (str) {
				return Binary.stringToBytes(unescape(encodeURIComponent(str)));
			},

			// Convert a byte array to a string
			bytesToString: function (bytes) {
				return decodeURIComponent(escape(Binary.bytesToString(bytes)));
			}

		};

		// Binary encoding
		var Binary = charenc.Binary = {

			// Convert a string to a byte array
			stringToBytes: function (str) {
				for (var bytes = [], i = 0; i < str.length; i++)
					bytes.push(str.charCodeAt(i) & 0xFF);
				return bytes;
			},

			// Convert a byte array to a string
			bytesToString: function (bytes) {
				for (var str = [], i = 0; i < bytes.length; i++)
					str.push(String.fromCharCode(bytes[i]));
				return str.join("");
			}

		};

	})();
}
  </script>
  <script type="text/javascript">
/*!
* Crypto-JS v2.5.4	SHA256.js
* http://code.google.com/p/crypto-js/
* Copyright (c) 2009-2013, Jeff Mott. All rights reserved.
* http://code.google.com/p/crypto-js/wiki/License
*/
(function () {

	// Shortcuts
	var C = Crypto,
		util = C.util,
		charenc = C.charenc,
		UTF8 = charenc.UTF8,
		Binary = charenc.Binary;

	// Constants
	var K = [0x428A2F98, 0x71374491, 0xB5C0FBCF, 0xE9B5DBA5,
        0x3956C25B, 0x59F111F1, 0x923F82A4, 0xAB1C5ED5,
        0xD807AA98, 0x12835B01, 0x243185BE, 0x550C7DC3,
        0x72BE5D74, 0x80DEB1FE, 0x9BDC06A7, 0xC19BF174,
        0xE49B69C1, 0xEFBE4786, 0x0FC19DC6, 0x240CA1CC,
        0x2DE92C6F, 0x4A7484AA, 0x5CB0A9DC, 0x76F988DA,
        0x983E5152, 0xA831C66D, 0xB00327C8, 0xBF597FC7,
        0xC6E00BF3, 0xD5A79147, 0x06CA6351, 0x14292967,
        0x27B70A85, 0x2E1B2138, 0x4D2C6DFC, 0x53380D13,
        0x650A7354, 0x766A0ABB, 0x81C2C92E, 0x92722C85,
        0xA2BFE8A1, 0xA81A664B, 0xC24B8B70, 0xC76C51A3,
        0xD192E819, 0xD6990624, 0xF40E3585, 0x106AA070,
        0x19A4C116, 0x1E376C08, 0x2748774C, 0x34B0BCB5,
        0x391C0CB3, 0x4ED8AA4A, 0x5B9CCA4F, 0x682E6FF3,
        0x748F82EE, 0x78A5636F, 0x84C87814, 0x8CC70208,
        0x90BEFFFA, 0xA4506CEB, 0xBEF9A3F7, 0xC67178F2];

	// Public API
	var SHA256 = C.SHA256 = function (message, options) {
		var digestbytes = util.wordsToBytes(SHA256._sha256(message));
		return options && options.asBytes ? digestbytes :
	    options && options.asString ? Binary.bytesToString(digestbytes) :
	    util.bytesToHex(digestbytes);
	};

	// The core
	SHA256._sha256 = function (message) {

		// Convert to byte array
		if (message.constructor == String) message = UTF8.stringToBytes(message);
		/* else, assume byte array already */

		var m = util.bytesToWords(message),
		l = message.length * 8,
		H = [0x6A09E667, 0xBB67AE85, 0x3C6EF372, 0xA54FF53A,
				0x510E527F, 0x9B05688C, 0x1F83D9AB, 0x5BE0CD19],
		w = [],
		a, b, c, d, e, f, g, h, i, j,
		t1, t2;

		// Padding
		m[l >> 5] |= 0x80 << (24 - l % 32);
		m[((l + 64 >> 9) << 4) + 15] = l;

		for (var i = 0; i < m.length; i += 16) {

			a = H[0];
			b = H[1];
			c = H[2];
			d = H[3];
			e = H[4];
			f = H[5];
			g = H[6];
			h = H[7];

			for (var j = 0; j < 64; j++) {

				if (j < 16) w[j] = m[j + i];
				else {

					var gamma0x = w[j - 15],
				gamma1x = w[j - 2],
				gamma0 = ((gamma0x << 25) | (gamma0x >>> 7)) ^
				            ((gamma0x << 14) | (gamma0x >>> 18)) ^
				            (gamma0x >>> 3),
				gamma1 = ((gamma1x << 15) | (gamma1x >>> 17)) ^
				            ((gamma1x << 13) | (gamma1x >>> 19)) ^
				            (gamma1x >>> 10);

					w[j] = gamma0 + (w[j - 7] >>> 0) +
				    gamma1 + (w[j - 16] >>> 0);

				}

				var ch = e & f ^ ~e & g,
			maj = a & b ^ a & c ^ b & c,
			sigma0 = ((a << 30) | (a >>> 2)) ^
			            ((a << 19) | (a >>> 13)) ^
			            ((a << 10) | (a >>> 22)),
			sigma1 = ((e << 26) | (e >>> 6)) ^
			            ((e << 21) | (e >>> 11)) ^
			            ((e << 7) | (e >>> 25));


				t1 = (h >>> 0) + sigma1 + ch + (K[j]) + (w[j] >>> 0);
				t2 = sigma0 + maj;

				h = g;
				g = f;
				f = e;
				e = (d + t1) >>> 0;
				d = c;
				c = b;
				b = a;
				a = (t1 + t2) >>> 0;

			}

			H[0] += a;
			H[1] += b;
			H[2] += c;
			H[3] += d;
			H[4] += e;
			H[5] += f;
			H[6] += g;
			H[7] += h;

		}

		return H;

	};

	// Package private blocksize
	SHA256._blocksize = 16;

	SHA256._digestsize = 32;

})();
  </script>
  <script type="text/javascript">
/*!
* Crypto-JS v2.5.4	PBKDF2.js
* http://code.google.com/p/crypto-js/
* Copyright (c) 2009-2013, Jeff Mott. All rights reserved.
* http://code.google.com/p/crypto-js/wiki/License
*/
(function () {

	// Shortcuts
	var C = Crypto,
		util = C.util,
		charenc = C.charenc,
		UTF8 = charenc.UTF8,
		Binary = charenc.Binary;

	C.PBKDF2 = function (password, salt, keylen, options) {

		// Convert to byte arrays
		if (password.constructor == String) password = UTF8.stringToBytes(password);
		if (salt.constructor == String) salt = UTF8.stringToBytes(salt);
		/* else, assume byte arrays already */

		// Defaults
		var hasher = options && options.hasher || C.SHA1,
			iterations = options && options.iterations || 1;
			hasher = C.SHA256;
			console.warn(hasher);

		// Pseudo-random function
		function PRF(password, salt) {
			return C.HMAC(hasher, salt, password, { asBytes: true });
		}

		// Generate key
		var derivedKeyBytes = [],
			blockindex = 1;
		while (derivedKeyBytes.length < keylen) {
			var block = PRF(password, salt.concat(util.wordsToBytes([blockindex])));
			for (var u = block, i = 1; i < iterations; i++) {
				u = PRF(password, u);
				for (var j = 0; j < block.length; j++) block[j] ^= u[j];
			}
			derivedKeyBytes = derivedKeyBytes.concat(block);
			blockindex++;
		}

		// Truncate excess bytes
		derivedKeyBytes.length = keylen;

		return options && options.asBytes ? derivedKeyBytes :
		options && options.asString ? Binary.bytesToString(derivedKeyBytes) :
		util.bytesToHex(derivedKeyBytes);

	};

})();
  </script>
  <script type="text/javascript">
/*!
* Crypto-JS v2.5.4	HMAC.js
* http://code.google.com/p/crypto-js/
* Copyright (c) 2009-2013, Jeff Mott. All rights reserved.
* http://code.google.com/p/crypto-js/wiki/License
*/
(function () {

	// Shortcuts
	var C = Crypto,
		util = C.util,
		charenc = C.charenc,
		UTF8 = charenc.UTF8,
		Binary = charenc.Binary;

	C.HMAC = function (hasher, message, key, options) {

		// Convert to byte arrays
		if (message.constructor == String) message = UTF8.stringToBytes(message);
		if (key.constructor == String) key = UTF8.stringToBytes(key);
		/* else, assume byte arrays already */

		// Allow arbitrary length keys
		if (key.length > hasher._blocksize * 4)
			key = hasher(key, { asBytes: true });

		// XOR keys with pad constants
		var okey = key.slice(0),
			ikey = key.slice(0);
		for (var i = 0; i < hasher._blocksize * 4; i++) {
			okey[i] ^= 0x5C;
			ikey[i] ^= 0x36;
		}

		var hmacbytes = hasher(okey.concat(hasher(ikey.concat(message), { asBytes: true })), { asBytes: true });

		return options && options.asBytes ? hmacbytes :
		options && options.asString ? Binary.bytesToString(hmacbytes) :
		util.bytesToHex(hmacbytes);

	};

})();
  </script>
  <script type="text/javascript">
/*!
* Crypto-JS v2.5.4	AES.js
* http://code.google.com/p/crypto-js/
* Copyright (c) 2009-2013, Jeff Mott. All rights reserved.
* http://code.google.com/p/crypto-js/wiki/License
*/
(function () {

	// Shortcuts
	var C = Crypto,
		util = C.util,
		charenc = C.charenc,
		UTF8 = charenc.UTF8;

	// Precomputed SBOX
	var SBOX = [0x63, 0x7c, 0x77, 0x7b, 0xf2, 0x6b, 0x6f, 0xc5,
            0x30, 0x01, 0x67, 0x2b, 0xfe, 0xd7, 0xab, 0x76,
            0xca, 0x82, 0xc9, 0x7d, 0xfa, 0x59, 0x47, 0xf0,
            0xad, 0xd4, 0xa2, 0xaf, 0x9c, 0xa4, 0x72, 0xc0,
            0xb7, 0xfd, 0x93, 0x26, 0x36, 0x3f, 0xf7, 0xcc,
            0x34, 0xa5, 0xe5, 0xf1, 0x71, 0xd8, 0x31, 0x15,
            0x04, 0xc7, 0x23, 0xc3, 0x18, 0x96, 0x05, 0x9a,
            0x07, 0x12, 0x80, 0xe2, 0xeb, 0x27, 0xb2, 0x75,
            0x09, 0x83, 0x2c, 0x1a, 0x1b, 0x6e, 0x5a, 0xa0,
            0x52, 0x3b, 0xd6, 0xb3, 0x29, 0xe3, 0x2f, 0x84,
            0x53, 0xd1, 0x00, 0xed, 0x20, 0xfc, 0xb1, 0x5b,
            0x6a, 0xcb, 0xbe, 0x39, 0x4a, 0x4c, 0x58, 0xcf,
            0xd0, 0xef, 0xaa, 0xfb, 0x43, 0x4d, 0x33, 0x85,
            0x45, 0xf9, 0x02, 0x7f, 0x50, 0x3c, 0x9f, 0xa8,
            0x51, 0xa3, 0x40, 0x8f, 0x92, 0x9d, 0x38, 0xf5,
            0xbc, 0xb6, 0xda, 0x21, 0x10, 0xff, 0xf3, 0xd2,
            0xcd, 0x0c, 0x13, 0xec, 0x5f, 0x97, 0x44, 0x17,
            0xc4, 0xa7, 0x7e, 0x3d, 0x64, 0x5d, 0x19, 0x73,
            0x60, 0x81, 0x4f, 0xdc, 0x22, 0x2a, 0x90, 0x88,
            0x46, 0xee, 0xb8, 0x14, 0xde, 0x5e, 0x0b, 0xdb,
            0xe0, 0x32, 0x3a, 0x0a, 0x49, 0x06, 0x24, 0x5c,
            0xc2, 0xd3, 0xac, 0x62, 0x91, 0x95, 0xe4, 0x79,
            0xe7, 0xc8, 0x37, 0x6d, 0x8d, 0xd5, 0x4e, 0xa9,
            0x6c, 0x56, 0xf4, 0xea, 0x65, 0x7a, 0xae, 0x08,
            0xba, 0x78, 0x25, 0x2e, 0x1c, 0xa6, 0xb4, 0xc6,
            0xe8, 0xdd, 0x74, 0x1f, 0x4b, 0xbd, 0x8b, 0x8a,
            0x70, 0x3e, 0xb5, 0x66, 0x48, 0x03, 0xf6, 0x0e,
            0x61, 0x35, 0x57, 0xb9, 0x86, 0xc1, 0x1d, 0x9e,
            0xe1, 0xf8, 0x98, 0x11, 0x69, 0xd9, 0x8e, 0x94,
            0x9b, 0x1e, 0x87, 0xe9, 0xce, 0x55, 0x28, 0xdf,
            0x8c, 0xa1, 0x89, 0x0d, 0xbf, 0xe6, 0x42, 0x68,
            0x41, 0x99, 0x2d, 0x0f, 0xb0, 0x54, 0xbb, 0x16];

	// Compute inverse SBOX lookup table
	for (var INVSBOX = [], i = 0; i < 256; i++) INVSBOX[SBOX[i]] = i;

	// Compute multiplication in GF(2^8) lookup tables
	var MULT2 = [],
		MULT3 = [],
		MULT9 = [],
		MULTB = [],
		MULTD = [],
		MULTE = [];

	function xtime(a, b) {
		for (var result = 0, i = 0; i < 8; i++) {
			if (b & 1) result ^= a;
			var hiBitSet = a & 0x80;
			a = (a << 1) & 0xFF;
			if (hiBitSet) a ^= 0x1b;
			b >>>= 1;
		}
		return result;
	}

	for (var i = 0; i < 256; i++) {
		MULT2[i] = xtime(i, 2);
		MULT3[i] = xtime(i, 3);
		MULT9[i] = xtime(i, 9);
		MULTB[i] = xtime(i, 0xB);
		MULTD[i] = xtime(i, 0xD);
		MULTE[i] = xtime(i, 0xE);
	}

	// Precomputed RCon lookup
	var RCON = [0x00, 0x01, 0x02, 0x04, 0x08, 0x10, 0x20, 0x40, 0x80, 0x1b, 0x36];

	// Inner state
	var state = [[], [], [], []],
		keylength,
		nrounds,
		keyschedule;

	var AES = C.AES = {

		/**
		* Public API
		*/

		encrypt: function (message, password, options) {
			options = options || {};

			// Determine mode
			var mode = options.mode || new C.mode.OFB;

			// Allow mode to override options
			if (mode.fixOptions) mode.fixOptions(options);

			var

			// Convert to bytes if message is a string
		m = (
			message.constructor == String ?
			UTF8.stringToBytes(message) :
			message
		),

			// Generate random IV
		iv = options.iv || util.randomBytes(AES._blocksize * 4),

			// Generate key
		k = (
			password.constructor == String ?
			// Derive key from pass-phrase
			C.PBKDF2(password, iv, 32, { asBytes: true }) :
			// else, assume byte array representing cryptographic key
			password
		);

			// Encrypt
			AES._init(k);
			mode.encrypt(AES, m, iv);

			// Return ciphertext
			m = options.iv ? m : iv.concat(m);
			return (options && options.asBytes) ? m : util.bytesToBase64(m);

		},

		decrypt: function (ciphertext, password, options) {
			options = options || {};

			// Determine mode
			var mode = options.mode || new C.mode.OFB;

			// Allow mode to override options
			if (mode.fixOptions) mode.fixOptions(options);

			var

			// Convert to bytes if ciphertext is a string
		c = (
			ciphertext.constructor == String ?
			util.base64ToBytes(ciphertext) :
			ciphertext
		),

			// Separate IV and message
		iv = options.iv || c.splice(0, AES._blocksize * 4);

			// Generate key
		var k = (
			password.constructor == String ?
			// Derive key from pass-phrase
			C.PBKDF2(password, iv, 32, { asBytes: true }) :
			// else, assume byte array representing cryptographic key
			password
		);
		console.warn(iv);

			// Decrypt
			AES._init(k);
			mode.decrypt(AES, c, iv);

			// Return plaintext
			return (options && options.asBytes) ? c : UTF8.bytesToString(c);

		},


		/**
		* Package private methods and properties
		*/

		_blocksize: 4,

		_encryptblock: function (m, offset) {

			// Set input
			for (var row = 0; row < AES._blocksize; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] = m[offset + col * 4 + row];
			}

			// Add round key
			for (var row = 0; row < 4; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] ^= keyschedule[col][row];
			}

			for (var round = 1; round < nrounds; round++) {

				// Sub bytes
				for (var row = 0; row < 4; row++) {
					for (var col = 0; col < 4; col++)
						state[row][col] = SBOX[state[row][col]];
				}

				// Shift rows
				state[1].push(state[1].shift());
				state[2].push(state[2].shift());
				state[2].push(state[2].shift());
				state[3].unshift(state[3].pop());

				// Mix columns
				for (var col = 0; col < 4; col++) {

					var s0 = state[0][col],
				s1 = state[1][col],
				s2 = state[2][col],
				s3 = state[3][col];

					state[0][col] = MULT2[s0] ^ MULT3[s1] ^ s2 ^ s3;
					state[1][col] = s0 ^ MULT2[s1] ^ MULT3[s2] ^ s3;
					state[2][col] = s0 ^ s1 ^ MULT2[s2] ^ MULT3[s3];
					state[3][col] = MULT3[s0] ^ s1 ^ s2 ^ MULT2[s3];

				}

				// Add round key
				for (var row = 0; row < 4; row++) {
					for (var col = 0; col < 4; col++)
						state[row][col] ^= keyschedule[round * 4 + col][row];
				}

			}

			// Sub bytes
			for (var row = 0; row < 4; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] = SBOX[state[row][col]];
			}

			// Shift rows
			state[1].push(state[1].shift());
			state[2].push(state[2].shift());
			state[2].push(state[2].shift());
			state[3].unshift(state[3].pop());

			// Add round key
			for (var row = 0; row < 4; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] ^= keyschedule[nrounds * 4 + col][row];
			}

			// Set output
			for (var row = 0; row < AES._blocksize; row++) {
				for (var col = 0; col < 4; col++)
					m[offset + col * 4 + row] = state[row][col];
			}

		},

		_decryptblock: function (c, offset) {

			// Set input
			for (var row = 0; row < AES._blocksize; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] = c[offset + col * 4 + row];
			}

			// Add round key
			for (var row = 0; row < 4; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] ^= keyschedule[nrounds * 4 + col][row];
			}

			for (var round = 1; round < nrounds; round++) {

				// Inv shift rows
				state[1].unshift(state[1].pop());
				state[2].push(state[2].shift());
				state[2].push(state[2].shift());
				state[3].push(state[3].shift());

				// Inv sub bytes
				for (var row = 0; row < 4; row++) {
					for (var col = 0; col < 4; col++)
						state[row][col] = INVSBOX[state[row][col]];
				}

				// Add round key
				for (var row = 0; row < 4; row++) {
					for (var col = 0; col < 4; col++)
						state[row][col] ^= keyschedule[(nrounds - round) * 4 + col][row];
				}

				// Inv mix columns
				for (var col = 0; col < 4; col++) {

					var s0 = state[0][col],
				s1 = state[1][col],
				s2 = state[2][col],
				s3 = state[3][col];

					state[0][col] = MULTE[s0] ^ MULTB[s1] ^ MULTD[s2] ^ MULT9[s3];
					state[1][col] = MULT9[s0] ^ MULTE[s1] ^ MULTB[s2] ^ MULTD[s3];
					state[2][col] = MULTD[s0] ^ MULT9[s1] ^ MULTE[s2] ^ MULTB[s3];
					state[3][col] = MULTB[s0] ^ MULTD[s1] ^ MULT9[s2] ^ MULTE[s3];

				}

			}

			// Inv shift rows
			state[1].unshift(state[1].pop());
			state[2].push(state[2].shift());
			state[2].push(state[2].shift());
			state[3].push(state[3].shift());

			// Inv sub bytes
			for (var row = 0; row < 4; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] = INVSBOX[state[row][col]];
			}

			// Add round key
			for (var row = 0; row < 4; row++) {
				for (var col = 0; col < 4; col++)
					state[row][col] ^= keyschedule[col][row];
			}

			// Set output
			for (var row = 0; row < AES._blocksize; row++) {
				for (var col = 0; col < 4; col++)
					c[offset + col * 4 + row] = state[row][col];
			}

		},


		/**
		* Private methods
		*/

		_init: function (k) {
			keylength = k.length / 4;
			nrounds = keylength + 6;
			AES._keyexpansion(k);
		},

		// Generate a key schedule
		_keyexpansion: function (k) {

			keyschedule = [];

			for (var row = 0; row < keylength; row++) {
				keyschedule[row] = [
			k[row * 4],
			k[row * 4 + 1],
			k[row * 4 + 2],
			k[row * 4 + 3]
		];
			}

			for (var row = keylength; row < AES._blocksize * (nrounds + 1); row++) {

				var temp = [
			keyschedule[row - 1][0],
			keyschedule[row - 1][1],
			keyschedule[row - 1][2],
			keyschedule[row - 1][3]
		];

				if (row % keylength == 0) {

					// Rot word
					temp.push(temp.shift());

					// Sub word
					temp[0] = SBOX[temp[0]];
					temp[1] = SBOX[temp[1]];
					temp[2] = SBOX[temp[2]];
					temp[3] = SBOX[temp[3]];

					temp[0] ^= RCON[row / keylength];

				} else if (keylength > 6 && row % keylength == 4) {

					// Sub word
					temp[0] = SBOX[temp[0]];
					temp[1] = SBOX[temp[1]];
					temp[2] = SBOX[temp[2]];
					temp[3] = SBOX[temp[3]];

				}

				keyschedule[row] = [
			keyschedule[row - keylength][0] ^ temp[0],
			keyschedule[row - keylength][1] ^ temp[1],
			keyschedule[row - keylength][2] ^ temp[2],
			keyschedule[row - keylength][3] ^ temp[3]
		];

			}

		}

	};

})();
  </script>
  <script type="text/javascript">
/*!
* Crypto-JS 2.5.4 BlockModes.js
* contribution from Simon Greatrix
*/

(function (C) {

	// Create pad namespace
	var C_pad = C.pad = {};

	// Calculate the number of padding bytes required.
	function _requiredPadding(cipher, message) {
		var blockSizeInBytes = cipher._blocksize * 4;
		var reqd = blockSizeInBytes - message.length % blockSizeInBytes;
		return reqd;
	}

	// Remove padding when the final byte gives the number of padding bytes.
	var _unpadLength = function (cipher, message, alg, padding) {
		var pad = message.pop();
		if (pad == 0) {
			throw new Error("Invalid zero-length padding specified for " + alg
			+ ". Wrong cipher specification or key used?");
		}
		var maxPad = cipher._blocksize * 4;
		if (pad > maxPad) {
			throw new Error("Invalid padding length of " + pad
			+ " specified for " + alg
			+ ". Wrong cipher specification or key used?");
		}
		for (var i = 1; i < pad; i++) {
			var b = message.pop();
			if (padding != undefined && padding != b) {
				throw new Error("Invalid padding byte of 0x" + b.toString(16)
				+ " specified for " + alg
				+ ". Wrong cipher specification or key used?");
			}
		}
	};

	// No-operation padding, used for stream ciphers
	C_pad.NoPadding = {
		pad: function (cipher, message) { },
		unpad: function (cipher, message) { }
	};

	// Zero Padding.
	//
	// If the message is not an exact number of blocks, the final block is
	// completed with 0x00 bytes. There is no unpadding.
	C_pad.ZeroPadding = {
		pad: function (cipher, message) {
			var blockSizeInBytes = cipher._blocksize * 4;
			var reqd = message.length % blockSizeInBytes;
			if (reqd != 0) {
				for (reqd = blockSizeInBytes - reqd; reqd > 0; reqd--) {
					message.push(0x00);
				}
			}
		},

		unpad: function (cipher, message) {
			while (message[message.length - 1] == 0) {
				message.pop();
			}
		}
	};

	// ISO/IEC 7816-4 padding.
	//
	// Pads the plain text with an 0x80 byte followed by as many 0x00
	// bytes are required to complete the block.
	C_pad.iso7816 = {
		pad: function (cipher, message) {
			var reqd = _requiredPadding(cipher, message);
			message.push(0x80);
			for (; reqd > 1; reqd--) {
				message.push(0x00);
			}
		},

		unpad: function (cipher, message) {
			var padLength;
			for (padLength = cipher._blocksize * 4; padLength > 0; padLength--) {
				var b = message.pop();
				if (b == 0x80) return;
				if (b != 0x00) {
					throw new Error("ISO-7816 padding byte must be 0, not 0x" + b.toString(16) + ". Wrong cipher specification or key used?");
				}
			}
			throw new Error("ISO-7816 padded beyond cipher block size. Wrong cipher specification or key used?");
		}
	};

	// ANSI X.923 padding
	//
	// The final block is padded with zeros except for the last byte of the
	// last block which contains the number of padding bytes.
	C_pad.ansix923 = {
		pad: function (cipher, message) {
			var reqd = _requiredPadding(cipher, message);
			for (var i = 1; i < reqd; i++) {
				message.push(0x00);
			}
			message.push(reqd);
		},

		unpad: function (cipher, message) {
			_unpadLength(cipher, message, "ANSI X.923", 0);
		}
	};

	// ISO 10126
	//
	// The final block is padded with random bytes except for the last
	// byte of the last block which contains the number of padding bytes.
	C_pad.iso10126 = {
		pad: function (cipher, message) {
			var reqd = _requiredPadding(cipher, message);
			for (var i = 1; i < reqd; i++) {
				message.push(Math.floor(Math.random() * 256));
			}
			message.push(reqd);
		},

		unpad: function (cipher, message) {
			_unpadLength(cipher, message, "ISO 10126", undefined);
		}
	};

	// PKCS7 padding
	//
	// PKCS7 is described in RFC 5652. Padding is in whole bytes. The
	// value of each added byte is the number of bytes that are added,
	// i.e. N bytes, each of value N are added.
	C_pad.pkcs7 = {
		pad: function (cipher, message) {
			var reqd = _requiredPadding(cipher, message);
			for (var i = 0; i < reqd; i++) {
				message.push(reqd);
			}
		},

		unpad: function (cipher, message) {
			_unpadLength(cipher, message, "PKCS 7", message[message.length - 1]);
		}
	};

	// Create mode namespace
	var C_mode = C.mode = {};

	/**
	* Mode base "class".
	*/
	var Mode = C_mode.Mode = function (padding) {
		if (padding) {
			this._padding = padding;
		}
	};

	Mode.prototype = {
		encrypt: function (cipher, m, iv) {
			this._padding.pad(cipher, m);
			this._doEncrypt(cipher, m, iv);
		},

		decrypt: function (cipher, m, iv) {
			this._doDecrypt(cipher, m, iv);
			this._padding.unpad(cipher, m);
		},

		// Default padding
		_padding: C_pad.iso7816
	};


	/**
	* Electronic Code Book mode.
	*
	* ECB applies the cipher directly against each block of the input.
	*
	* ECB does not require an initialization vector.
	*/
	var ECB = C_mode.ECB = function () {
		// Call parent constructor
		Mode.apply(this, arguments);
	};

	// Inherit from Mode
	var ECB_prototype = ECB.prototype = new Mode;

	// Concrete steps for Mode template
	ECB_prototype._doEncrypt = function (cipher, m, iv) {
		var blockSizeInBytes = cipher._blocksize * 4;
		// Encrypt each block
		for (var offset = 0; offset < m.length; offset += blockSizeInBytes) {
			cipher._encryptblock(m, offset);
		}
	};
	ECB_prototype._doDecrypt = function (cipher, c, iv) {
		var blockSizeInBytes = cipher._blocksize * 4;
		// Decrypt each block
		for (var offset = 0; offset < c.length; offset += blockSizeInBytes) {
			cipher._decryptblock(c, offset);
		}
	};

	// ECB never uses an IV
	ECB_prototype.fixOptions = function (options) {
		options.iv = [];
	};


	/**
	* Cipher block chaining
	*
	* The first block is XORed with the IV. Subsequent blocks are XOR with the
	* previous cipher output.
	*/
	var CBC = C_mode.CBC = function () {
		// Call parent constructor
		Mode.apply(this, arguments);
	};

	// Inherit from Mode
	var CBC_prototype = CBC.prototype = new Mode;

	// Concrete steps for Mode template
	CBC_prototype._doEncrypt = function (cipher, m, iv) {
		var blockSizeInBytes = cipher._blocksize * 4;

		// Encrypt each block
		for (var offset = 0; offset < m.length; offset += blockSizeInBytes) {
			if (offset == 0) {
				// XOR first block using IV
				for (var i = 0; i < blockSizeInBytes; i++)
					m[i] ^= iv[i];
			} else {
				// XOR this block using previous crypted block
				for (var i = 0; i < blockSizeInBytes; i++)
					m[offset + i] ^= m[offset + i - blockSizeInBytes];
			}
			// Encrypt block
			cipher._encryptblock(m, offset);
		}
	};
	CBC_prototype._doDecrypt = function (cipher, c, iv) {
		var blockSizeInBytes = cipher._blocksize * 4;

		// At the start, the previously crypted block is the IV
		var prevCryptedBlock = iv;

		// Decrypt each block
		for (var offset = 0; offset < c.length; offset += blockSizeInBytes) {
			// Save this crypted block
			var thisCryptedBlock = c.slice(offset, offset + blockSizeInBytes);
			// Decrypt block
			cipher._decryptblock(c, offset);
			// XOR decrypted block using previous crypted block
			for (var i = 0; i < blockSizeInBytes; i++) {
				c[offset + i] ^= prevCryptedBlock[i];
			}
			prevCryptedBlock = thisCryptedBlock;
		}
	};


	/**
	* Cipher feed back
	*
	* The cipher output is XORed with the plain text to produce the cipher output,
	* which is then fed back into the cipher to produce a bit pattern to XOR the
	* next block with.
	*
	* This is a stream cipher mode and does not require padding.
	*/
	var CFB = C_mode.CFB = function () {
		// Call parent constructor
		Mode.apply(this, arguments);
	};

	// Inherit from Mode
	var CFB_prototype = CFB.prototype = new Mode;

	// Override padding
	CFB_prototype._padding = C_pad.NoPadding;

	// Concrete steps for Mode template
	CFB_prototype._doEncrypt = function (cipher, m, iv) {
		var blockSizeInBytes = cipher._blocksize * 4,
    keystream = iv.slice(0);

		// Encrypt each byte
		for (var i = 0; i < m.length; i++) {

			var j = i % blockSizeInBytes;
			if (j == 0) cipher._encryptblock(keystream, 0);

			m[i] ^= keystream[j];
			keystream[j] = m[i];
		}
	};
	CFB_prototype._doDecrypt = function (cipher, c, iv) {
		var blockSizeInBytes = cipher._blocksize * 4,
			keystream = iv.slice(0);

		// Encrypt each byte
		for (var i = 0; i < c.length; i++) {

			var j = i % blockSizeInBytes;
			if (j == 0) cipher._encryptblock(keystream, 0);

			var b = c[i];
			c[i] ^= keystream[j];
			keystream[j] = b;
		}
	};


	/**
	* Output feed back
	*
	* The cipher repeatedly encrypts its own output. The output is XORed with the
	* plain text to produce the cipher text.
	*
	* This is a stream cipher mode and does not require padding.
	*/
	var OFB = C_mode.OFB = function () {
		// Call parent constructor
		Mode.apply(this, arguments);
	};

	// Inherit from Mode
	var OFB_prototype = OFB.prototype = new Mode;

	// Override padding
	OFB_prototype._padding = C_pad.NoPadding;

	// Concrete steps for Mode template
	OFB_prototype._doEncrypt = function (cipher, m, iv) {

		var blockSizeInBytes = cipher._blocksize * 4,
			keystream = iv.slice(0);

		// Encrypt each byte
		for (var i = 0; i < m.length; i++) {

			// Generate keystream
			if (i % blockSizeInBytes == 0)
				cipher._encryptblock(keystream, 0);

			// Encrypt byte
			m[i] ^= keystream[i % blockSizeInBytes];

		}
	};
	OFB_prototype._doDecrypt = OFB_prototype._doEncrypt;

	/**
	* Counter
	* @author Gergely Risko
	*
	* After every block the last 4 bytes of the IV is increased by one
	* with carry and that IV is used for the next block.
	*
	* This is a stream cipher mode and does not require padding.
	*/
	var CTR = C_mode.CTR = function () {
		// Call parent constructor
		Mode.apply(this, arguments);
	};

	// Inherit from Mode
	var CTR_prototype = CTR.prototype = new Mode;

	// Override padding
	CTR_prototype._padding = C_pad.NoPadding;

	CTR_prototype._doEncrypt = function (cipher, m, iv) {
		var blockSizeInBytes = cipher._blocksize * 4;
		var counter = iv.slice(0);

		for (var i = 0; i < m.length; ) {
			// do not lose iv
			var keystream = counter.slice(0);

			// Generate keystream for next block
			cipher._encryptblock(keystream, 0);

			// XOR keystream with block
			for (var j = 0; i < m.length && j < blockSizeInBytes; j++, i++) {
				m[i] ^= keystream[j];
			}

			// Increase counter
			if (++(counter[blockSizeInBytes - 1]) == 256) {
				counter[blockSizeInBytes - 1] = 0;
				if (++(counter[blockSizeInBytes - 2]) == 256) {
					counter[blockSizeInBytes - 2] = 0;
					if (++(counter[blockSizeInBytes - 3]) == 256) {
						counter[blockSizeInBytes - 3] = 0;
						++(counter[blockSizeInBytes - 4]);
					}
				}
			}
		}
	};
	CTR_prototype._doDecrypt = CTR_prototype._doEncrypt;

})(Crypto);
  </script>
  <script type="text/javascript">
/*!
* Crypto-JS v2.0.0  RIPEMD-160
* http://code.google.com/p/crypto-js/
* Copyright (c) 2009, Jeff Mott. All rights reserved.
* http://code.google.com/p/crypto-js/wiki/License
*
* A JavaScript implementation of the RIPEMD-160 Algorithm
* Version 2.2 Copyright Jeremy Lin, Paul Johnston 2000 - 2009.
* Other contributors: Greg Holt, Andrew Kepert, Ydnar, Lostinet
* Distributed under the BSD License
* See http://pajhome.org.uk/crypt/md5 for details.
* Also http://www.ocf.berkeley.edu/~jjlin/jsotp/
* Ported to Crypto-JS by Stefan Thomas.
*/

(function () {
	// Shortcuts
	var C = Crypto,
	util = C.util,
	charenc = C.charenc,
	UTF8 = charenc.UTF8,
	Binary = charenc.Binary;

	// Convert a byte array to little-endian 32-bit words
	util.bytesToLWords = function (bytes) {

		var output = Array(bytes.length >> 2);
		for (var i = 0; i < output.length; i++)
			output[i] = 0;
		for (var i = 0; i < bytes.length * 8; i += 8)
			output[i >> 5] |= (bytes[i / 8] & 0xFF) << (i % 32);
		return output;
	};

	// Convert little-endian 32-bit words to a byte array
	util.lWordsToBytes = function (words) {
		var output = [];
		for (var i = 0; i < words.length * 32; i += 8)
			output.push((words[i >> 5] >>> (i % 32)) & 0xff);
		return output;
	};

	// Public API
	var RIPEMD160 = C.RIPEMD160 = function (message, options) {
		var digestbytes = util.lWordsToBytes(RIPEMD160._rmd160(message));
		return options && options.asBytes ? digestbytes :
			options && options.asString ? Binary.bytesToString(digestbytes) :
			util.bytesToHex(digestbytes);
	};

	// The core
	RIPEMD160._rmd160 = function (message) {
		// Convert to byte array
		if (message.constructor == String) message = UTF8.stringToBytes(message);

		var x = util.bytesToLWords(message),
			len = message.length * 8;

		/* append padding */
		x[len >> 5] |= 0x80 << (len % 32);
		x[(((len + 64) >>> 9) << 4) + 14] = len;

		var h0 = 0x67452301;
		var h1 = 0xefcdab89;
		var h2 = 0x98badcfe;
		var h3 = 0x10325476;
		var h4 = 0xc3d2e1f0;

		for (var i = 0; i < x.length; i += 16) {
			var T;
			var A1 = h0, B1 = h1, C1 = h2, D1 = h3, E1 = h4;
			var A2 = h0, B2 = h1, C2 = h2, D2 = h3, E2 = h4;
			for (var j = 0; j <= 79; ++j) {
				T = safe_add(A1, rmd160_f(j, B1, C1, D1));
				T = safe_add(T, x[i + rmd160_r1[j]]);
				T = safe_add(T, rmd160_K1(j));
				T = safe_add(bit_rol(T, rmd160_s1[j]), E1);
				A1 = E1; E1 = D1; D1 = bit_rol(C1, 10); C1 = B1; B1 = T;
				T = safe_add(A2, rmd160_f(79 - j, B2, C2, D2));
				T = safe_add(T, x[i + rmd160_r2[j]]);
				T = safe_add(T, rmd160_K2(j));
				T = safe_add(bit_rol(T, rmd160_s2[j]), E2);
				A2 = E2; E2 = D2; D2 = bit_rol(C2, 10); C2 = B2; B2 = T;
			}
			T = safe_add(h1, safe_add(C1, D2));
			h1 = safe_add(h2, safe_add(D1, E2));
			h2 = safe_add(h3, safe_add(E1, A2));
			h3 = safe_add(h4, safe_add(A1, B2));
			h4 = safe_add(h0, safe_add(B1, C2));
			h0 = T;
		}
		return [h0, h1, h2, h3, h4];
	}

	function rmd160_f(j, x, y, z) {
		return (0 <= j && j <= 15) ? (x ^ y ^ z) :
			(16 <= j && j <= 31) ? (x & y) | (~x & z) :
			(32 <= j && j <= 47) ? (x | ~y) ^ z :
			(48 <= j && j <= 63) ? (x & z) | (y & ~z) :
			(64 <= j && j <= 79) ? x ^ (y | ~z) :
			"rmd160_f: j out of range";
	}
	function rmd160_K1(j) {
		return (0 <= j && j <= 15) ? 0x00000000 :
			(16 <= j && j <= 31) ? 0x5a827999 :
			(32 <= j && j <= 47) ? 0x6ed9eba1 :
			(48 <= j && j <= 63) ? 0x8f1bbcdc :
			(64 <= j && j <= 79) ? 0xa953fd4e :
			"rmd160_K1: j out of range";
	}
	function rmd160_K2(j) {
		return (0 <= j && j <= 15) ? 0x50a28be6 :
			(16 <= j && j <= 31) ? 0x5c4dd124 :
			(32 <= j && j <= 47) ? 0x6d703ef3 :
			(48 <= j && j <= 63) ? 0x7a6d76e9 :
			(64 <= j && j <= 79) ? 0x00000000 :
			"rmd160_K2: j out of range";
	}
	var rmd160_r1 = [
		0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15,
		7, 4, 13, 1, 10, 6, 15, 3, 12, 0, 9, 5, 2, 14, 11, 8,
		3, 10, 14, 4, 9, 15, 8, 1, 2, 7, 0, 6, 13, 11, 5, 12,
		1, 9, 11, 10, 0, 8, 12, 4, 13, 3, 7, 15, 14, 5, 6, 2,
		4, 0, 5, 9, 7, 12, 2, 10, 14, 1, 3, 8, 11, 6, 15, 13
	];
	var rmd160_r2 = [
		5, 14, 7, 0, 9, 2, 11, 4, 13, 6, 15, 8, 1, 10, 3, 12,
		6, 11, 3, 7, 0, 13, 5, 10, 14, 15, 8, 12, 4, 9, 1, 2,
		15, 5, 1, 3, 7, 14, 6, 9, 11, 8, 12, 2, 10, 0, 4, 13,
		8, 6, 4, 1, 3, 11, 15, 0, 5, 12, 2, 13, 9, 7, 10, 14,
		12, 15, 10, 4, 1, 5, 8, 7, 6, 2, 13, 14, 0, 3, 9, 11
	];
	var rmd160_s1 = [
		11, 14, 15, 12, 5, 8, 7, 9, 11, 13, 14, 15, 6, 7, 9, 8,
		7, 6, 8, 13, 11, 9, 7, 15, 7, 12, 15, 9, 11, 7, 13, 12,
		11, 13, 6, 7, 14, 9, 13, 15, 14, 8, 13, 6, 5, 12, 7, 5,
		11, 12, 14, 15, 14, 15, 9, 8, 9, 14, 5, 6, 8, 6, 5, 12,
		9, 15, 5, 11, 6, 8, 13, 12, 5, 12, 13, 14, 11, 8, 5, 6
	];
	var rmd160_s2 = [
		8, 9, 9, 11, 13, 15, 15, 5, 7, 7, 8, 11, 14, 14, 12, 6,
		9, 13, 15, 7, 12, 8, 9, 11, 7, 7, 12, 7, 6, 15, 13, 11,
		9, 7, 15, 11, 8, 6, 6, 14, 12, 13, 5, 14, 13, 13, 7, 5,
		15, 5, 8, 11, 14, 14, 6, 14, 6, 9, 12, 9, 12, 5, 15, 8,
		8, 5, 12, 9, 12, 5, 14, 6, 8, 13, 6, 5, 15, 13, 11, 11
	];

	/*
	* Add integers, wrapping at 2^32. This uses 16-bit operations internally
	* to work around bugs in some JS interpreters.
	*/
	function safe_add(x, y) {
		var lsw = (x & 0xFFFF) + (y & 0xFFFF);
		var msw = (x >> 16) + (y >> 16) + (lsw >> 16);
		return (msw << 16) | (lsw & 0xFFFF);
	}

	/*
	* Bitwise rotate a 32-bit number to the left.
	*/
	function bit_rol(num, cnt) {
		return (num << cnt) | (num >>> (32 - cnt));
	}
})();
  </script>
  <script type="text/javascript">
/*!
* Random number generator with ArcFour PRNG
*
* NOTE: For best results, put code like
* <body onclick='SecureRandom.seedTime();' onkeypress='SecureRandom.seedTime();'>
* in your main HTML document.
*
* Copyright Tom Wu, bitaddress.org  BSD License.
* http://www-cs-students.stanford.edu/~tjw/jsbn/LICENSE
*/
(function () {

	// Constructor function of Global SecureRandom object
	var sr = window.SecureRandom = function () { };

	// Properties
	sr.state;
	sr.pool;
	sr.pptr;

	// Pool size must be a multiple of 4 and greater than 32.
	// An array of bytes the size of the pool will be passed to init()
	sr.poolSize = 256;

	// --- object methods ---

	// public method
	// ba: byte array
	sr.prototype.nextBytes = function (ba) {
		var i;
		if (window.crypto && window.crypto.getRandomValues && window.Uint8Array) {
			try {
				var rvBytes = new Uint8Array(ba.length);
				window.crypto.getRandomValues(rvBytes);
				for (i = 0; i < ba.length; ++i)
					ba[i] = sr.getByte() ^ rvBytes[i];
				return;
			} catch (e) {
				alert(e);
			}
		}
		for (i = 0; i < ba.length; ++i) ba[i] = sr.getByte();
	};


	// --- static methods ---

	// Mix in the current time (w/milliseconds) into the pool
	// NOTE: this method should be called from body click/keypress event handlers to increase entropy
	sr.seedTime = function () {
		sr.seedInt(new Date().getTime());
	}

	sr.getByte = function () {
		if(!ninja.seeder.isDone()) {
			alert("Premature initialisation of the random generator. Something is really wrong, do not generate wallets.");
			return NaN;
		}

		if (sr.state == null) {
			sr.seedTime();
			sr.state = sr.ArcFour(); // Plug in your RNG constructor here
			sr.state.init(sr.pool);
			sr.pptr = 0;
		}
		// TODO: allow reseeding after first request
		return sr.state.next();
	}

	// Mix in a 32-bit integer into the pool
	sr.seedInt = function (x) {
		sr.seedInt8(x);
		sr.seedInt8((x >> 8));
		sr.seedInt8((x >> 16));
		sr.seedInt8((x >> 24));
	}

	// Mix in a 16-bit integer into the pool
	sr.seedInt16 = function (x) {
		sr.seedInt8(x);
		sr.seedInt8((x >> 8));
	}

	// Mix in a 8-bit integer into the pool
	sr.seedInt8 = function (x) {
		sr.pool[sr.pptr++] ^= x & 255;
		if (sr.pptr >= sr.poolSize) sr.pptr -= sr.poolSize;
	}

	// Arcfour is a PRNG
	sr.ArcFour = function () {
		function Arcfour() {
			this.i = 0;
			this.j = 0;
			this.S = new Array();
		}

		// Initialize arcfour context from key, an array of ints, each from [0..255]
		function ARC4init(key) {
			var i, j, t;
			for (i = 0; i < 256; ++i)
				this.S[i] = i;
			j = 0;
			for (i = 0; i < 256; ++i) {
				j = (j + this.S[i] + key[i % key.length]) & 255;
				t = this.S[i];
				this.S[i] = this.S[j];
				this.S[j] = t;
			}
			this.i = 0;
			this.j = 0;
		}

		function ARC4next() {
			var t;
			this.i = (this.i + 1) & 255;
			this.j = (this.j + this.S[this.i]) & 255;
			t = this.S[this.i];
			this.S[this.i] = this.S[this.j];
			this.S[this.j] = t;
			return this.S[(t + this.S[this.i]) & 255];
		}

		Arcfour.prototype.init = ARC4init;
		Arcfour.prototype.next = ARC4next;

		return new Arcfour();
	};


	// Initialize the pool with junk if needed.
	if (sr.pool == null) {
		sr.pool = new Array();
		sr.pptr = 0;
		var t;
		if (window.crypto && window.crypto.getRandomValues && window.Uint8Array) {
			try {
				// Use webcrypto if available
				var ua = new Uint8Array(sr.poolSize);
				window.crypto.getRandomValues(ua);
				for (t = 0; t < sr.poolSize; ++t)
					sr.pool[sr.pptr++] = ua[t];
			} catch (e) { alert(e); }
		}
		while (sr.pptr < sr.poolSize) {  // extract some randomness from Math.random()
			t = Math.floor(65536 * Math.random());
			sr.pool[sr.pptr++] = t >>> 8;
			sr.pool[sr.pptr++] = t & 255;
		}
		sr.pptr = Math.floor(sr.poolSize * Math.random());
		sr.seedTime();
		// entropy
		var entropyStr = "";
		// screen size and color depth: ~4.8 to ~5.4 bits
		entropyStr += (window.screen.height * window.screen.width * window.screen.colorDepth);
		entropyStr += (window.screen.availHeight * window.screen.availWidth * window.screen.pixelDepth);
		// time zone offset: ~4 bits
		var dateObj = new Date();
		var timeZoneOffset = dateObj.getTimezoneOffset();
		entropyStr += timeZoneOffset;
		// user agent: ~8.3 to ~11.6 bits
		entropyStr += navigator.userAgent;
		// browser plugin details: ~16.2 to ~21.8 bits
		var pluginsStr = "";
		for (var i = 0; i < navigator.plugins.length; i++) {
			pluginsStr += navigator.plugins[i].name + " " + navigator.plugins[i].filename + " " + navigator.plugins[i].description + " " + navigator.plugins[i].version + ", ";
		}
		var mimeTypesStr = "";
		for (var i = 0; i < navigator.mimeTypes.length; i++) {
			mimeTypesStr += navigator.mimeTypes[i].description + " " + navigator.mimeTypes[i].type + " " + navigator.mimeTypes[i].suffixes + ", ";
		}
		entropyStr += pluginsStr + mimeTypesStr;
		// cookies and storage: 1 bit
		entropyStr += navigator.cookieEnabled + typeof (sessionStorage) + typeof (localStorage);
		// language: ~7 bit
		entropyStr += navigator.language;
		// history: ~2 bit
		entropyStr += window.history.length;
		// location
		entropyStr += window.location;

		var entropyBytes = Crypto.SHA256(entropyStr, { asBytes: true });
		for (var i = 0 ; i < entropyBytes.length ; i++) {
			sr.seedInt8(entropyBytes[i]);
		}
	}
})();
  </script>
  <script type="text/javascript">
//https://raw.github.com/bitcoinjs/bitcoinjs-lib/faa10f0f6a1fff0b9a99fffb9bc30cee33b17212/src/ecdsa.js
/*!
* Basic Javascript Elliptic Curve implementation
* Ported loosely from BouncyCastle's Java EC code
* Only Fp curves implemented for now
*
* Copyright Tom Wu, bitaddress.org  BSD License.
* http://www-cs-students.stanford.edu/~tjw/jsbn/LICENSE
*/
(function () {

	// Constructor function of Global EllipticCurve object
	var ec = window.EllipticCurve = function () { };


	// ----------------
	// ECFieldElementFp constructor
	// q instanceof BigInteger
	// x instanceof BigInteger
	ec.FieldElementFp = function (q, x) {
		this.x = x;
		// TODO if(x.compareTo(q) >= 0) error
		this.q = q;
	};

	ec.FieldElementFp.prototype.equals = function (other) {
		if (other == this) return true;
		return (this.q.equals(other.q) && this.x.equals(other.x));
	};

	ec.FieldElementFp.prototype.toBigInteger = function () {
		return this.x;
	};

	ec.FieldElementFp.prototype.negate = function () {
		return new ec.FieldElementFp(this.q, this.x.negate().mod(this.q));
	};

	ec.FieldElementFp.prototype.add = function (b) {
		return new ec.FieldElementFp(this.q, this.x.add(b.toBigInteger()).mod(this.q));
	};

	ec.FieldElementFp.prototype.subtract = function (b) {
		return new ec.FieldElementFp(this.q, this.x.subtract(b.toBigInteger()).mod(this.q));
	};

	ec.FieldElementFp.prototype.multiply = function (b) {
		return new ec.FieldElementFp(this.q, this.x.multiply(b.toBigInteger()).mod(this.q));
	};

	ec.FieldElementFp.prototype.square = function () {
		return new ec.FieldElementFp(this.q, this.x.square().mod(this.q));
	};

	ec.FieldElementFp.prototype.divide = function (b) {
		return new ec.FieldElementFp(this.q, this.x.multiply(b.toBigInteger().modInverse(this.q)).mod(this.q));
	};

	ec.FieldElementFp.prototype.getByteLength = function () {
		return Math.floor((this.toBigInteger().bitLength() + 7) / 8);
	};

	// D.1.4 91
	/**
	* return a sqrt root - the routine verifies that the calculation
	* returns the right value - if none exists it returns null.
	*
	* Copyright (c) 2000 - 2011 The Legion Of The Bouncy Castle (http://www.bouncycastle.org)
	* Ported to JavaScript by bitaddress.org
	*/
	ec.FieldElementFp.prototype.sqrt = function () {
		if (!this.q.testBit(0)) throw new Error("even value of q");

		// p mod 4 == 3
		if (this.q.testBit(1)) {
			// z = g^(u+1) + p, p = 4u + 3
			var z = new ec.FieldElementFp(this.q, this.x.modPow(this.q.shiftRight(2).add(BigInteger.ONE), this.q));
			return z.square().equals(this) ? z : null;
		}

		// p mod 4 == 1
		var qMinusOne = this.q.subtract(BigInteger.ONE);
		var legendreExponent = qMinusOne.shiftRight(1);
		if (!(this.x.modPow(legendreExponent, this.q).equals(BigInteger.ONE))) return null;
		var u = qMinusOne.shiftRight(2);
		var k = u.shiftLeft(1).add(BigInteger.ONE);
		var Q = this.x;
		var fourQ = Q.shiftLeft(2).mod(this.q);
		var U, V;

		do {
			var rand = new SecureRandom();
			var P;
			do {
				P = new BigInteger(this.q.bitLength(), rand);
			}
			while (P.compareTo(this.q) >= 0 || !(P.multiply(P).subtract(fourQ).modPow(legendreExponent, this.q).equals(qMinusOne)));

			var result = ec.FieldElementFp.fastLucasSequence(this.q, P, Q, k);

			U = result[0];
			V = result[1];
			if (V.multiply(V).mod(this.q).equals(fourQ)) {
				// Integer division by 2, mod q
				if (V.testBit(0)) {
					V = V.add(this.q);
				}
				V = V.shiftRight(1);
				return new ec.FieldElementFp(this.q, V);
			}
		}
		while (U.equals(BigInteger.ONE) || U.equals(qMinusOne));

		return null;
	};

	/*
	* Copyright (c) 2000 - 2011 The Legion Of The Bouncy Castle (http://www.bouncycastle.org)
	* Ported to JavaScript by bitaddress.org
	*/
	ec.FieldElementFp.fastLucasSequence = function (p, P, Q, k) {
		// TODO Research and apply "common-multiplicand multiplication here"

		var n = k.bitLength();
		var s = k.getLowestSetBit();
		var Uh = BigInteger.ONE;
		var Vl = BigInteger.TWO;
		var Vh = P;
		var Ql = BigInteger.ONE;
		var Qh = BigInteger.ONE;

		for (var j = n - 1; j >= s + 1; --j) {
			Ql = Ql.multiply(Qh).mod(p);
			if (k.testBit(j)) {
				Qh = Ql.multiply(Q).mod(p);
				Uh = Uh.multiply(Vh).mod(p);
				Vl = Vh.multiply(Vl).subtract(P.multiply(Ql)).mod(p);
				Vh = Vh.multiply(Vh).subtract(Qh.shiftLeft(1)).mod(p);
			}
			else {
				Qh = Ql;
				Uh = Uh.multiply(Vl).subtract(Ql).mod(p);
				Vh = Vh.multiply(Vl).subtract(P.multiply(Ql)).mod(p);
				Vl = Vl.multiply(Vl).subtract(Ql.shiftLeft(1)).mod(p);
			}
		}

		Ql = Ql.multiply(Qh).mod(p);
		Qh = Ql.multiply(Q).mod(p);
		Uh = Uh.multiply(Vl).subtract(Ql).mod(p);
		Vl = Vh.multiply(Vl).subtract(P.multiply(Ql)).mod(p);
		Ql = Ql.multiply(Qh).mod(p);

		for (var j = 1; j <= s; ++j) {
			Uh = Uh.multiply(Vl).mod(p);
			Vl = Vl.multiply(Vl).subtract(Ql.shiftLeft(1)).mod(p);
			Ql = Ql.multiply(Ql).mod(p);
		}

		return [Uh, Vl];
	};

	// ----------------
	// ECPointFp constructor
	ec.PointFp = function (curve, x, y, z, compressed) {
		this.curve = curve;
		this.x = x;
		this.y = y;
		// Projective coordinates: either zinv == null or z * zinv == 1
		// z and zinv are just BigIntegers, not fieldElements
		if (z == null) {
			this.z = BigInteger.ONE;
		}
		else {
			this.z = z;
		}
		this.zinv = null;
		// compression flag
		this.compressed = !!compressed;
	};

	ec.PointFp.prototype.getX = function () {
		if (this.zinv == null) {
			this.zinv = this.z.modInverse(this.curve.q);
		}
		var r = this.x.toBigInteger().multiply(this.zinv);
		this.curve.reduce(r);
		return this.curve.fromBigInteger(r);
	};

	ec.PointFp.prototype.getY = function () {
		if (this.zinv == null) {
			this.zinv = this.z.modInverse(this.curve.q);
		}
		var r = this.y.toBigInteger().multiply(this.zinv);
		this.curve.reduce(r);
		return this.curve.fromBigInteger(r);
	};

	ec.PointFp.prototype.equals = function (other) {
		if (other == this) return true;
		if (this.isInfinity()) return other.isInfinity();
		if (other.isInfinity()) return this.isInfinity();
		var u, v;
		// u = Y2 * Z1 - Y1 * Z2
		u = other.y.toBigInteger().multiply(this.z).subtract(this.y.toBigInteger().multiply(other.z)).mod(this.curve.q);
		if (!u.equals(BigInteger.ZERO)) return false;
		// v = X2 * Z1 - X1 * Z2
		v = other.x.toBigInteger().multiply(this.z).subtract(this.x.toBigInteger().multiply(other.z)).mod(this.curve.q);
		return v.equals(BigInteger.ZERO);
	};

	ec.PointFp.prototype.isInfinity = function () {
		if ((this.x == null) && (this.y == null)) return true;
		return this.z.equals(BigInteger.ZERO) && !this.y.toBigInteger().equals(BigInteger.ZERO);
	};

	ec.PointFp.prototype.negate = function () {
		return new ec.PointFp(this.curve, this.x, this.y.negate(), this.z);
	};

	ec.PointFp.prototype.add = function (b) {
		if (this.isInfinity()) return b;
		if (b.isInfinity()) return this;

		// u = Y2 * Z1 - Y1 * Z2
		var u = b.y.toBigInteger().multiply(this.z).subtract(this.y.toBigInteger().multiply(b.z)).mod(this.curve.q);
		// v = X2 * Z1 - X1 * Z2
		var v = b.x.toBigInteger().multiply(this.z).subtract(this.x.toBigInteger().multiply(b.z)).mod(this.curve.q);


		if (BigInteger.ZERO.equals(v)) {
			if (BigInteger.ZERO.equals(u)) {
				return this.twice(); // this == b, so double
			}
			return this.curve.getInfinity(); // this = -b, so infinity
		}

		var THREE = new BigInteger("3");
		var x1 = this.x.toBigInteger();
		var y1 = this.y.toBigInteger();
		var x2 = b.x.toBigInteger();
		var y2 = b.y.toBigInteger();

		var v2 = v.square();
		var v3 = v2.multiply(v);
		var x1v2 = x1.multiply(v2);
		var zu2 = u.square().multiply(this.z);

		// x3 = v * (z2 * (z1 * u^2 - 2 * x1 * v^2) - v^3)
		var x3 = zu2.subtract(x1v2.shiftLeft(1)).multiply(b.z).subtract(v3).multiply(v).mod(this.curve.q);
		// y3 = z2 * (3 * x1 * u * v^2 - y1 * v^3 - z1 * u^3) + u * v^3
		var y3 = x1v2.multiply(THREE).multiply(u).subtract(y1.multiply(v3)).subtract(zu2.multiply(u)).multiply(b.z).add(u.multiply(v3)).mod(this.curve.q);
		// z3 = v^3 * z1 * z2
		var z3 = v3.multiply(this.z).multiply(b.z).mod(this.curve.q);

		return new ec.PointFp(this.curve, this.curve.fromBigInteger(x3), this.curve.fromBigInteger(y3), z3);
	};

	ec.PointFp.prototype.twice = function () {
		if (this.isInfinity()) return this;
		if (this.y.toBigInteger().signum() == 0) return this.curve.getInfinity();

		// TODO: optimized handling of constants
		var THREE = new BigInteger("3");
		var x1 = this.x.toBigInteger();
		var y1 = this.y.toBigInteger();

		var y1z1 = y1.multiply(this.z);
		var y1sqz1 = y1z1.multiply(y1).mod(this.curve.q);
		var a = this.curve.a.toBigInteger();

		// w = 3 * x1^2 + a * z1^2
		var w = x1.square().multiply(THREE);
		if (!BigInteger.ZERO.equals(a)) {
			w = w.add(this.z.square().multiply(a));
		}
		w = w.mod(this.curve.q);
		//this.curve.reduce(w);
		// x3 = 2 * y1 * z1 * (w^2 - 8 * x1 * y1^2 * z1)
		var x3 = w.square().subtract(x1.shiftLeft(3).multiply(y1sqz1)).shiftLeft(1).multiply(y1z1).mod(this.curve.q);
		// y3 = 4 * y1^2 * z1 * (3 * w * x1 - 2 * y1^2 * z1) - w^3
		var y3 = w.multiply(THREE).multiply(x1).subtract(y1sqz1.shiftLeft(1)).shiftLeft(2).multiply(y1sqz1).subtract(w.square().multiply(w)).mod(this.curve.q);
		// z3 = 8 * (y1 * z1)^3
		var z3 = y1z1.square().multiply(y1z1).shiftLeft(3).mod(this.curve.q);

		return new ec.PointFp(this.curve, this.curve.fromBigInteger(x3), this.curve.fromBigInteger(y3), z3);
	};

	// Simple NAF (Non-Adjacent Form) multiplication algorithm
	// TODO: modularize the multiplication algorithm
	ec.PointFp.prototype.multiply = function (k) {
		if (this.isInfinity()) return this;
		if (k.signum() == 0) return this.curve.getInfinity();

		var e = k;
		var h = e.multiply(new BigInteger("3"));

		var neg = this.negate();
		var R = this;

		var i;
		for (i = h.bitLength() - 2; i > 0; --i) {
			R = R.twice();

			var hBit = h.testBit(i);
			var eBit = e.testBit(i);

			if (hBit != eBit) {
				R = R.add(hBit ? this : neg);
			}
		}

		return R;
	};

	// Compute this*j + x*k (simultaneous multiplication)
	ec.PointFp.prototype.multiplyTwo = function (j, x, k) {
		var i;
		if (j.bitLength() > k.bitLength())
			i = j.bitLength() - 1;
		else
			i = k.bitLength() - 1;

		var R = this.curve.getInfinity();
		var both = this.add(x);
		while (i >= 0) {
			R = R.twice();
			if (j.testBit(i)) {
				if (k.testBit(i)) {
					R = R.add(both);
				}
				else {
					R = R.add(this);
				}
			}
			else {
				if (k.testBit(i)) {
					R = R.add(x);
				}
			}
			--i;
		}

		return R;
	};

	// patched by bitaddress.org and Casascius for use with Bitcoin.ECKey
	// patched by coretechs to support compressed public keys
	ec.PointFp.prototype.getEncoded = function (compressed) {
		var x = this.getX().toBigInteger();
		var y = this.getY().toBigInteger();
		var len = 32; // integerToBytes will zero pad if integer is less than 32 bytes. 32 bytes length is required by the Bitcoin protocol.
		var enc = ec.integerToBytes(x, len);

		// when compressed prepend byte depending if y point is even or odd
		if (compressed) {
			if (y.isEven()) {
				enc.unshift(0x02);
			}
			else {
				enc.unshift(0x03);
			}
		}
		else {
			enc.unshift(0x04);
			enc = enc.concat(ec.integerToBytes(y, len)); // uncompressed public key appends the bytes of the y point
		}
		return enc;
	};

	ec.PointFp.decodeFrom = function (curve, enc) {
		var type = enc[0];
		var dataLen = enc.length - 1;

		// Extract x and y as byte arrays
		var xBa = enc.slice(1, 1 + dataLen / 2);
		var yBa = enc.slice(1 + dataLen / 2, 1 + dataLen);

		// Prepend zero byte to prevent interpretation as negative integer
		xBa.unshift(0);
		yBa.unshift(0);

		// Convert to BigIntegers
		var x = new BigInteger(xBa);
		var y = new BigInteger(yBa);

		// Return point
		return new ec.PointFp(curve, curve.fromBigInteger(x), curve.fromBigInteger(y));
	};

	ec.PointFp.prototype.add2D = function (b) {
		if (this.isInfinity()) return b;
		if (b.isInfinity()) return this;

		if (this.x.equals(b.x)) {
			if (this.y.equals(b.y)) {
				// this = b, i.e. this must be doubled
				return this.twice();
			}
			// this = -b, i.e. the result is the point at infinity
			return this.curve.getInfinity();
		}

		var x_x = b.x.subtract(this.x);
		var y_y = b.y.subtract(this.y);
		var gamma = y_y.divide(x_x);

		var x3 = gamma.square().subtract(this.x).subtract(b.x);
		var y3 = gamma.multiply(this.x.subtract(x3)).subtract(this.y);

		return new ec.PointFp(this.curve, x3, y3);
	};

	ec.PointFp.prototype.twice2D = function () {
		if (this.isInfinity()) return this;
		if (this.y.toBigInteger().signum() == 0) {
			// if y1 == 0, then (x1, y1) == (x1, -y1)
			// and hence this = -this and thus 2(x1, y1) == infinity
			return this.curve.getInfinity();
		}

		var TWO = this.curve.fromBigInteger(BigInteger.valueOf(2));
		var THREE = this.curve.fromBigInteger(BigInteger.valueOf(3));
		var gamma = this.x.square().multiply(THREE).add(this.curve.a).divide(this.y.multiply(TWO));

		var x3 = gamma.square().subtract(this.x.multiply(TWO));
		var y3 = gamma.multiply(this.x.subtract(x3)).subtract(this.y);

		return new ec.PointFp(this.curve, x3, y3);
	};

	ec.PointFp.prototype.multiply2D = function (k) {
		if (this.isInfinity()) return this;
		if (k.signum() == 0) return this.curve.getInfinity();

		var e = k;
		var h = e.multiply(new BigInteger("3"));

		var neg = this.negate();
		var R = this;

		var i;
		for (i = h.bitLength() - 2; i > 0; --i) {
			R = R.twice();

			var hBit = h.testBit(i);
			var eBit = e.testBit(i);

			if (hBit != eBit) {
				R = R.add2D(hBit ? this : neg);
			}
		}

		return R;
	};

	ec.PointFp.prototype.isOnCurve = function () {
		var x = this.getX().toBigInteger();
		var y = this.getY().toBigInteger();
		var a = this.curve.getA().toBigInteger();
		var b = this.curve.getB().toBigInteger();
		var n = this.curve.getQ();
		var lhs = y.multiply(y).mod(n);
		var rhs = x.multiply(x).multiply(x).add(a.multiply(x)).add(b).mod(n);
		return lhs.equals(rhs);
	};

	ec.PointFp.prototype.toString = function () {
		return '(' + this.getX().toBigInteger().toString() + ',' + this.getY().toBigInteger().toString() + ')';
	};

	/**
	* Validate an elliptic curve point.
	*
	* See SEC 1, section 3.2.2.1: Elliptic Curve Public Key Validation Primitive
	*/
	ec.PointFp.prototype.validate = function () {
		var n = this.curve.getQ();

		// Check Q != O
		if (this.isInfinity()) {
			throw new Error("Point is at infinity.");
		}

		// Check coordinate bounds
		var x = this.getX().toBigInteger();
		var y = this.getY().toBigInteger();
		if (x.compareTo(BigInteger.ONE) < 0 || x.compareTo(n.subtract(BigInteger.ONE)) > 0) {
			throw new Error('x coordinate out of bounds');
		}
		if (y.compareTo(BigInteger.ONE) < 0 || y.compareTo(n.subtract(BigInteger.ONE)) > 0) {
			throw new Error('y coordinate out of bounds');
		}

		// Check y^2 = x^3 + ax + b (mod n)
		if (!this.isOnCurve()) {
			throw new Error("Point is not on the curve.");
		}

		// Check nQ = 0 (Q is a scalar multiple of G)
		if (this.multiply(n).isInfinity()) {
			// TODO: This check doesn't work - fix.
			throw new Error("Point is not a scalar multiple of G.");
		}

		return true;
	};




	// ----------------
	// ECCurveFp constructor
	ec.CurveFp = function (q, a, b) {
		this.q = q;
		this.a = this.fromBigInteger(a);
		this.b = this.fromBigInteger(b);
		this.infinity = new ec.PointFp(this, null, null);
		this.reducer = new Barrett(this.q);
	}

	ec.CurveFp.prototype.getQ = function () {
		return this.q;
	};

	ec.CurveFp.prototype.getA = function () {
		return this.a;
	};

	ec.CurveFp.prototype.getB = function () {
		return this.b;
	};

	ec.CurveFp.prototype.equals = function (other) {
		if (other == this) return true;
		return (this.q.equals(other.q) && this.a.equals(other.a) && this.b.equals(other.b));
	};

	ec.CurveFp.prototype.getInfinity = function () {
		return this.infinity;
	};

	ec.CurveFp.prototype.fromBigInteger = function (x) {
		return new ec.FieldElementFp(this.q, x);
	};

	ec.CurveFp.prototype.reduce = function (x) {
		this.reducer.reduce(x);
	};

	// for now, work with hex strings because they're easier in JS
	// compressed support added by bitaddress.org
	ec.CurveFp.prototype.decodePointHex = function (s) {
		var firstByte = parseInt(s.substr(0, 2), 16);
		switch (firstByte) { // first byte
			case 0:
				return this.infinity;
			case 2: // compressed
			case 3: // compressed
				var yTilde = firstByte & 1;
				var xHex = s.substr(2, s.length - 2);
				var X1 = new BigInteger(xHex, 16);
				return this.decompressPoint(yTilde, X1);
			case 4: // uncompressed
			case 6: // hybrid
			case 7: // hybrid
				var len = (s.length - 2) / 2;
				var xHex = s.substr(2, len);
				var yHex = s.substr(len + 2, len);

				return new ec.PointFp(this,
					this.fromBigInteger(new BigInteger(xHex, 16)),
					this.fromBigInteger(new BigInteger(yHex, 16)));

			default: // unsupported
				return null;
		}
	};

	ec.CurveFp.prototype.encodePointHex = function (p) {
		if (p.isInfinity()) return "00";
		var xHex = p.getX().toBigInteger().toString(16);
		var yHex = p.getY().toBigInteger().toString(16);
		var oLen = this.getQ().toString(16).length;
		if ((oLen % 2) != 0) oLen++;
		while (xHex.length < oLen) {
			xHex = "0" + xHex;
		}
		while (yHex.length < oLen) {
			yHex = "0" + yHex;
		}
		return "04" + xHex + yHex;
	};

	/*
	* Copyright (c) 2000 - 2011 The Legion Of The Bouncy Castle (http://www.bouncycastle.org)
	* Ported to JavaScript by bitaddress.org
	*
	* Number yTilde
	* BigInteger X1
	*/
	ec.CurveFp.prototype.decompressPoint = function (yTilde, X1) {
		var x = this.fromBigInteger(X1);
		var alpha = x.multiply(x.square().add(this.getA())).add(this.getB());
		var beta = alpha.sqrt();
		// if we can't find a sqrt we haven't got a point on the curve - run!
		if (beta == null) throw new Error("Invalid point compression");
		var betaValue = beta.toBigInteger();
		var bit0 = betaValue.testBit(0) ? 1 : 0;
		if (bit0 != yTilde) {
			// Use the other root
			beta = this.fromBigInteger(this.getQ().subtract(betaValue));
		}
		return new ec.PointFp(this, x, beta, null, true);
	};


	ec.fromHex = function (s) { return new BigInteger(s, 16); };

	ec.integerToBytes = function (i, len) {
		var bytes = i.toByteArrayUnsigned();
		if (len < bytes.length) {
			bytes = bytes.slice(bytes.length - len);
		} else while (len > bytes.length) {
			bytes.unshift(0);
		}
		return bytes;
	};


	// Named EC curves
	// ----------------
	// X9ECParameters constructor
	ec.X9Parameters = function (curve, g, n, h) {
		this.curve = curve;
		this.g = g;
		this.n = n;
		this.h = h;
	}
	ec.X9Parameters.prototype.getCurve = function () { return this.curve; };
	ec.X9Parameters.prototype.getG = function () { return this.g; };
	ec.X9Parameters.prototype.getN = function () { return this.n; };
	ec.X9Parameters.prototype.getH = function () { return this.h; };

	// secp256k1 is the Curve used by Bitcoin
	ec.secNamedCurves = {
		// used by Bitcoin
		"secp256k1": function () {
			// p = 2^256 - 2^32 - 2^9 - 2^8 - 2^7 - 2^6 - 2^4 - 1
			var p = ec.fromHex("FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F");
			var a = BigInteger.ZERO;
			var b = ec.fromHex("7");
			var n = ec.fromHex("FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141");
			var h = BigInteger.ONE;
			var curve = new ec.CurveFp(p, a, b);
			var G = curve.decodePointHex("04"
					+ "79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798"
					+ "483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8");
			return new ec.X9Parameters(curve, G, n, h);
		}
	};

	// secp256k1 called by Bitcoin's ECKEY
	ec.getSECCurveByName = function (name) {
		if (ec.secNamedCurves[name] == undefined) return null;
		return ec.secNamedCurves[name]();
	}
})();
  </script>
  <script type="text/javascript">
/*!
* Basic JavaScript BN library - subset useful for RSA encryption. v1.3
*
* Copyright (c) 2005  Tom Wu
* All Rights Reserved.
* BSD License
* http://www-cs-students.stanford.edu/~tjw/jsbn/LICENSE
*
* Copyright Stephan Thomas
* Copyright bitaddress.org
*/

(function () {

	// (public) Constructor function of Global BigInteger object
	var BigInteger = window.BigInteger = function BigInteger(a, b, c) {
		if (a != null)
			if ("number" == typeof a) this.fromNumber(a, b, c);
			else if (b == null && "string" != typeof a) this.fromString(a, 256);
			else this.fromString(a, b);
	};

	// Bits per digit
	var dbits;

	// JavaScript engine analysis
	var canary = 0xdeadbeefcafe;
	var j_lm = ((canary & 0xffffff) == 0xefcafe);

	// return new, unset BigInteger
	function nbi() { return new BigInteger(null); }

	// am: Compute w_j += (x*this_i), propagate carries,
	// c is initial carry, returns final carry.
	// c < 3*dvalue, x < 2*dvalue, this_i < dvalue
	// We need to select the fastest one that works in this environment.

	// am1: use a single mult and divide to get the high bits,
	// max digit bits should be 26 because
	// max internal value = 2*dvalue^2-2*dvalue (< 2^53)
	function am1(i, x, w, j, c, n) {
		while (--n >= 0) {
			var v = x * this[i++] + w[j] + c;
			c = Math.floor(v / 0x4000000);
			w[j++] = v & 0x3ffffff;
		}
		return c;
	}
	// am2 avoids a big mult-and-extract completely.
	// Max digit bits should be <= 30 because we do bitwise ops
	// on values up to 2*hdvalue^2-hdvalue-1 (< 2^31)
	function am2(i, x, w, j, c, n) {
		var xl = x & 0x7fff, xh = x >> 15;
		while (--n >= 0) {
			var l = this[i] & 0x7fff;
			var h = this[i++] >> 15;
			var m = xh * l + h * xl;
			l = xl * l + ((m & 0x7fff) << 15) + w[j] + (c & 0x3fffffff);
			c = (l >>> 30) + (m >>> 15) + xh * h + (c >>> 30);
			w[j++] = l & 0x3fffffff;
		}
		return c;
	}
	// Alternately, set max digit bits to 28 since some
	// browsers slow down when dealing with 32-bit numbers.
	function am3(i, x, w, j, c, n) {
		var xl = x & 0x3fff, xh = x >> 14;
		while (--n >= 0) {
			var l = this[i] & 0x3fff;
			var h = this[i++] >> 14;
			var m = xh * l + h * xl;
			l = xl * l + ((m & 0x3fff) << 14) + w[j] + c;
			c = (l >> 28) + (m >> 14) + xh * h;
			w[j++] = l & 0xfffffff;
		}
		return c;
	}
	if (j_lm && (navigator.appName == "Microsoft Internet Explorer")) {
		BigInteger.prototype.am = am2;
		dbits = 30;
	}
	else if (j_lm && (navigator.appName != "Netscape")) {
		BigInteger.prototype.am = am1;
		dbits = 26;
	}
	else { // Mozilla/Netscape seems to prefer am3
		BigInteger.prototype.am = am3;
		dbits = 28;
	}

	BigInteger.prototype.DB = dbits;
	BigInteger.prototype.DM = ((1 << dbits) - 1);
	BigInteger.prototype.DV = (1 << dbits);

	var BI_FP = 52;
	BigInteger.prototype.FV = Math.pow(2, BI_FP);
	BigInteger.prototype.F1 = BI_FP - dbits;
	BigInteger.prototype.F2 = 2 * dbits - BI_FP;

	// Digit conversions
	var BI_RM = "0123456789abcdefghijklmnopqrstuvwxyz";
	var BI_RC = new Array();
	var rr, vv;
	rr = "0".charCodeAt(0);
	for (vv = 0; vv <= 9; ++vv) BI_RC[rr++] = vv;
	rr = "a".charCodeAt(0);
	for (vv = 10; vv < 36; ++vv) BI_RC[rr++] = vv;
	rr = "A".charCodeAt(0);
	for (vv = 10; vv < 36; ++vv) BI_RC[rr++] = vv;

	function int2char(n) { return BI_RM.charAt(n); }
	function intAt(s, i) {
		var c = BI_RC[s.charCodeAt(i)];
		return (c == null) ? -1 : c;
	}



	// return bigint initialized to value
	function nbv(i) { var r = nbi(); r.fromInt(i); return r; }


	// returns bit length of the integer x
	function nbits(x) {
		var r = 1, t;
		if ((t = x >>> 16) != 0) { x = t; r += 16; }
		if ((t = x >> 8) != 0) { x = t; r += 8; }
		if ((t = x >> 4) != 0) { x = t; r += 4; }
		if ((t = x >> 2) != 0) { x = t; r += 2; }
		if ((t = x >> 1) != 0) { x = t; r += 1; }
		return r;
	}







	// (protected) copy this to r
	BigInteger.prototype.copyTo = function (r) {
		for (var i = this.t - 1; i >= 0; --i) r[i] = this[i];
		r.t = this.t;
		r.s = this.s;
	};


	// (protected) set from integer value x, -DV <= x < DV
	BigInteger.prototype.fromInt = function (x) {
		this.t = 1;
		this.s = (x < 0) ? -1 : 0;
		if (x > 0) this[0] = x;
		else if (x < -1) this[0] = x + this.DV;
		else this.t = 0;
	};

	// (protected) set from string and radix
	BigInteger.prototype.fromString = function (s, b) {
		var k;
		if (b == 16) k = 4;
		else if (b == 8) k = 3;
		else if (b == 256) k = 8; // byte array
		else if (b == 2) k = 1;
		else if (b == 32) k = 5;
		else if (b == 4) k = 2;
		else { this.fromRadix(s, b); return; }
		this.t = 0;
		this.s = 0;
		var i = s.length, mi = false, sh = 0;
		while (--i >= 0) {
			var x = (k == 8) ? s[i] & 0xff : intAt(s, i);
			if (x < 0) {
				if (s.charAt(i) == "-") mi = true;
				continue;
			}
			mi = false;
			if (sh == 0)
				this[this.t++] = x;
			else if (sh + k > this.DB) {
				this[this.t - 1] |= (x & ((1 << (this.DB - sh)) - 1)) << sh;
				this[this.t++] = (x >> (this.DB - sh));
			}
			else
				this[this.t - 1] |= x << sh;
			sh += k;
			if (sh >= this.DB) sh -= this.DB;
		}
		if (k == 8 && (s[0] & 0x80) != 0) {
			this.s = -1;
			if (sh > 0) this[this.t - 1] |= ((1 << (this.DB - sh)) - 1) << sh;
		}
		this.clamp();
		if (mi) BigInteger.ZERO.subTo(this, this);
	};


	// (protected) clamp off excess high words
	BigInteger.prototype.clamp = function () {
		var c = this.s & this.DM;
		while (this.t > 0 && this[this.t - 1] == c) --this.t;
	};

	// (protected) r = this << n*DB
	BigInteger.prototype.dlShiftTo = function (n, r) {
		var i;
		for (i = this.t - 1; i >= 0; --i) r[i + n] = this[i];
		for (i = n - 1; i >= 0; --i) r[i] = 0;
		r.t = this.t + n;
		r.s = this.s;
	};

	// (protected) r = this >> n*DB
	BigInteger.prototype.drShiftTo = function (n, r) {
		for (var i = n; i < this.t; ++i) r[i - n] = this[i];
		r.t = Math.max(this.t - n, 0);
		r.s = this.s;
	};


	// (protected) r = this << n
	BigInteger.prototype.lShiftTo = function (n, r) {
		var bs = n % this.DB;
		var cbs = this.DB - bs;
		var bm = (1 << cbs) - 1;
		var ds = Math.floor(n / this.DB), c = (this.s << bs) & this.DM, i;
		for (i = this.t - 1; i >= 0; --i) {
			r[i + ds + 1] = (this[i] >> cbs) | c;
			c = (this[i] & bm) << bs;
		}
		for (i = ds - 1; i >= 0; --i) r[i] = 0;
		r[ds] = c;
		r.t = this.t + ds + 1;
		r.s = this.s;
		r.clamp();
	};


	// (protected) r = this >> n
	BigInteger.prototype.rShiftTo = function (n, r) {
		r.s = this.s;
		var ds = Math.floor(n / this.DB);
		if (ds >= this.t) { r.t = 0; return; }
		var bs = n % this.DB;
		var cbs = this.DB - bs;
		var bm = (1 << bs) - 1;
		r[0] = this[ds] >> bs;
		for (var i = ds + 1; i < this.t; ++i) {
			r[i - ds - 1] |= (this[i] & bm) << cbs;
			r[i - ds] = this[i] >> bs;
		}
		if (bs > 0) r[this.t - ds - 1] |= (this.s & bm) << cbs;
		r.t = this.t - ds;
		r.clamp();
	};


	// (protected) r = this - a
	BigInteger.prototype.subTo = function (a, r) {
		var i = 0, c = 0, m = Math.min(a.t, this.t);
		while (i < m) {
			c += this[i] - a[i];
			r[i++] = c & this.DM;
			c >>= this.DB;
		}
		if (a.t < this.t) {
			c -= a.s;
			while (i < this.t) {
				c += this[i];
				r[i++] = c & this.DM;
				c >>= this.DB;
			}
			c += this.s;
		}
		else {
			c += this.s;
			while (i < a.t) {
				c -= a[i];
				r[i++] = c & this.DM;
				c >>= this.DB;
			}
			c -= a.s;
		}
		r.s = (c < 0) ? -1 : 0;
		if (c < -1) r[i++] = this.DV + c;
		else if (c > 0) r[i++] = c;
		r.t = i;
		r.clamp();
	};


	// (protected) r = this * a, r != this,a (HAC 14.12)
	// "this" should be the larger one if appropriate.
	BigInteger.prototype.multiplyTo = function (a, r) {
		var x = this.abs(), y = a.abs();
		var i = x.t;
		r.t = i + y.t;
		while (--i >= 0) r[i] = 0;
		for (i = 0; i < y.t; ++i) r[i + x.t] = x.am(0, y[i], r, i, 0, x.t);
		r.s = 0;
		r.clamp();
		if (this.s != a.s) BigInteger.ZERO.subTo(r, r);
	};


	// (protected) r = this^2, r != this (HAC 14.16)
	BigInteger.prototype.squareTo = function (r) {
		var x = this.abs();
		var i = r.t = 2 * x.t;
		while (--i >= 0) r[i] = 0;
		for (i = 0; i < x.t - 1; ++i) {
			var c = x.am(i, x[i], r, 2 * i, 0, 1);
			if ((r[i + x.t] += x.am(i + 1, 2 * x[i], r, 2 * i + 1, c, x.t - i - 1)) >= x.DV) {
				r[i + x.t] -= x.DV;
				r[i + x.t + 1] = 1;
			}
		}
		if (r.t > 0) r[r.t - 1] += x.am(i, x[i], r, 2 * i, 0, 1);
		r.s = 0;
		r.clamp();
	};



	// (protected) divide this by m, quotient and remainder to q, r (HAC 14.20)
	// r != q, this != m.  q or r may be null.
	BigInteger.prototype.divRemTo = function (m, q, r) {
		var pm = m.abs();
		if (pm.t <= 0) return;
		var pt = this.abs();
		if (pt.t < pm.t) {
			if (q != null) q.fromInt(0);
			if (r != null) this.copyTo(r);
			return;
		}
		if (r == null) r = nbi();
		var y = nbi(), ts = this.s, ms = m.s;
		var nsh = this.DB - nbits(pm[pm.t - 1]); // normalize modulus
		if (nsh > 0) { pm.lShiftTo(nsh, y); pt.lShiftTo(nsh, r); }
		else { pm.copyTo(y); pt.copyTo(r); }
		var ys = y.t;
		var y0 = y[ys - 1];
		if (y0 == 0) return;
		var yt = y0 * (1 << this.F1) + ((ys > 1) ? y[ys - 2] >> this.F2 : 0);
		var d1 = this.FV / yt, d2 = (1 << this.F1) / yt, e = 1 << this.F2;
		var i = r.t, j = i - ys, t = (q == null) ? nbi() : q;
		y.dlShiftTo(j, t);
		if (r.compareTo(t) >= 0) {
			r[r.t++] = 1;
			r.subTo(t, r);
		}
		BigInteger.ONE.dlShiftTo(ys, t);
		t.subTo(y, y); // "negative" y so we can replace sub with am later
		while (y.t < ys) y[y.t++] = 0;
		while (--j >= 0) {
			// Estimate quotient digit
			var qd = (r[--i] == y0) ? this.DM : Math.floor(r[i] * d1 + (r[i - 1] + e) * d2);
			if ((r[i] += y.am(0, qd, r, j, 0, ys)) < qd) {	// Try it out
				y.dlShiftTo(j, t);
				r.subTo(t, r);
				while (r[i] < --qd) r.subTo(t, r);
			}
		}
		if (q != null) {
			r.drShiftTo(ys, q);
			if (ts != ms) BigInteger.ZERO.subTo(q, q);
		}
		r.t = ys;
		r.clamp();
		if (nsh > 0) r.rShiftTo(nsh, r); // Denormalize remainder
		if (ts < 0) BigInteger.ZERO.subTo(r, r);
	};


	// (protected) return "-1/this % 2^DB"; useful for Mont. reduction
	// justification:
	//         xy == 1 (mod m)
	//         xy =  1+km
	//   xy(2-xy) = (1+km)(1-km)
	// x[y(2-xy)] = 1-k^2m^2
	// x[y(2-xy)] == 1 (mod m^2)
	// if y is 1/x mod m, then y(2-xy) is 1/x mod m^2
	// should reduce x and y(2-xy) by m^2 at each step to keep size bounded.
	// JS multiply "overflows" differently from C/C++, so care is needed here.
	BigInteger.prototype.invDigit = function () {
		if (this.t < 1) return 0;
		var x = this[0];
		if ((x & 1) == 0) return 0;
		var y = x & 3; 	// y == 1/x mod 2^2
		y = (y * (2 - (x & 0xf) * y)) & 0xf; // y == 1/x mod 2^4
		y = (y * (2 - (x & 0xff) * y)) & 0xff; // y == 1/x mod 2^8
		y = (y * (2 - (((x & 0xffff) * y) & 0xffff))) & 0xffff; // y == 1/x mod 2^16
		// last step - calculate inverse mod DV directly;
		// assumes 16 < DB <= 32 and assumes ability to handle 48-bit ints
		y = (y * (2 - x * y % this.DV)) % this.DV; 	// y == 1/x mod 2^dbits
		// we really want the negative inverse, and -DV < y < DV
		return (y > 0) ? this.DV - y : -y;
	};


	// (protected) true iff this is even
	BigInteger.prototype.isEven = function () { return ((this.t > 0) ? (this[0] & 1) : this.s) == 0; };


	// (protected) this^e, e < 2^32, doing sqr and mul with "r" (HAC 14.79)
	BigInteger.prototype.exp = function (e, z) {
		if (e > 0xffffffff || e < 1) return BigInteger.ONE;
		var r = nbi(), r2 = nbi(), g = z.convert(this), i = nbits(e) - 1;
		g.copyTo(r);
		while (--i >= 0) {
			z.sqrTo(r, r2);
			if ((e & (1 << i)) > 0) z.mulTo(r2, g, r);
			else { var t = r; r = r2; r2 = t; }
		}
		return z.revert(r);
	};


	// (public) return string representation in given radix
	BigInteger.prototype.toString = function (b) {
		if (this.s < 0) return "-" + this.negate().toString(b);
		var k;
		if (b == 16) k = 4;
		else if (b == 8) k = 3;
		else if (b == 2) k = 1;
		else if (b == 32) k = 5;
		else if (b == 4) k = 2;
		else return this.toRadix(b);
		var km = (1 << k) - 1, d, m = false, r = "", i = this.t;
		var p = this.DB - (i * this.DB) % k;
		if (i-- > 0) {
			if (p < this.DB && (d = this[i] >> p) > 0) { m = true; r = int2char(d); }
			while (i >= 0) {
				if (p < k) {
					d = (this[i] & ((1 << p) - 1)) << (k - p);
					d |= this[--i] >> (p += this.DB - k);
				}
				else {
					d = (this[i] >> (p -= k)) & km;
					if (p <= 0) { p += this.DB; --i; }
				}
				if (d > 0) m = true;
				if (m) r += int2char(d);
			}
		}
		return m ? r : "0";
	};


	// (public) -this
	BigInteger.prototype.negate = function () { var r = nbi(); BigInteger.ZERO.subTo(this, r); return r; };

	// (public) |this|
	BigInteger.prototype.abs = function () { return (this.s < 0) ? this.negate() : this; };

	// (public) return + if this > a, - if this < a, 0 if equal
	BigInteger.prototype.compareTo = function (a) {
		var r = this.s - a.s;
		if (r != 0) return r;
		var i = this.t;
		r = i - a.t;
		if (r != 0) return (this.s < 0) ? -r : r;
		while (--i >= 0) if ((r = this[i] - a[i]) != 0) return r;
		return 0;
	}

	// (public) return the number of bits in "this"
	BigInteger.prototype.bitLength = function () {
		if (this.t <= 0) return 0;
		return this.DB * (this.t - 1) + nbits(this[this.t - 1] ^ (this.s & this.DM));
	};

	// (public) this mod a
	BigInteger.prototype.mod = function (a) {
		var r = nbi();
		this.abs().divRemTo(a, null, r);
		if (this.s < 0 && r.compareTo(BigInteger.ZERO) > 0) a.subTo(r, r);
		return r;
	}

	// (public) this^e % m, 0 <= e < 2^32
	BigInteger.prototype.modPowInt = function (e, m) {
		var z;
		if (e < 256 || m.isEven()) z = new Classic(m); else z = new Montgomery(m);
		return this.exp(e, z);
	};

	// "constants"
	BigInteger.ZERO = nbv(0);
	BigInteger.ONE = nbv(1);







	// Copyright (c) 2005-2009  Tom Wu
	// All Rights Reserved.
	// See "LICENSE" for details.
	// Extended JavaScript BN functions, required for RSA private ops.
	// Version 1.1: new BigInteger("0", 10) returns "proper" zero
	// Version 1.2: square() API, isProbablePrime fix


	// return index of lowest 1-bit in x, x < 2^31
	function lbit(x) {
		if (x == 0) return -1;
		var r = 0;
		if ((x & 0xffff) == 0) { x >>= 16; r += 16; }
		if ((x & 0xff) == 0) { x >>= 8; r += 8; }
		if ((x & 0xf) == 0) { x >>= 4; r += 4; }
		if ((x & 3) == 0) { x >>= 2; r += 2; }
		if ((x & 1) == 0) ++r;
		return r;
	}

	// return number of 1 bits in x
	function cbit(x) {
		var r = 0;
		while (x != 0) { x &= x - 1; ++r; }
		return r;
	}

	var lowprimes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103, 107, 109, 113, 127, 131, 137, 139, 149, 151, 157, 163, 167, 173, 179, 181, 191, 193, 197, 199, 211, 223, 227, 229, 233, 239, 241, 251, 257, 263, 269, 271, 277, 281, 283, 293, 307, 311, 313, 317, 331, 337, 347, 349, 353, 359, 367, 373, 379, 383, 389, 397, 401, 409, 419, 421, 431, 433, 439, 443, 449, 457, 461, 463, 467, 479, 487, 491, 499, 503, 509, 521, 523, 541, 547, 557, 563, 569, 571, 577, 587, 593, 599, 601, 607, 613, 617, 619, 631, 641, 643, 647, 653, 659, 661, 673, 677, 683, 691, 701, 709, 719, 727, 733, 739, 743, 751, 757, 761, 769, 773, 787, 797, 809, 811, 821, 823, 827, 829, 839, 853, 857, 859, 863, 877, 881, 883, 887, 907, 911, 919, 929, 937, 941, 947, 953, 967, 971, 977, 983, 991, 997];
	var lplim = (1 << 26) / lowprimes[lowprimes.length - 1];



	// (protected) return x s.t. r^x < DV
	BigInteger.prototype.chunkSize = function (r) { return Math.floor(Math.LN2 * this.DB / Math.log(r)); };

	// (protected) convert to radix string
	BigInteger.prototype.toRadix = function (b) {
		if (b == null) b = 10;
		if (this.signum() == 0 || b < 2 || b > 36) return "0";
		var cs = this.chunkSize(b);
		var a = Math.pow(b, cs);
		var d = nbv(a), y = nbi(), z = nbi(), r = "";
		this.divRemTo(d, y, z);
		while (y.signum() > 0) {
			r = (a + z.intValue()).toString(b).substr(1) + r;
			y.divRemTo(d, y, z);
		}
		return z.intValue().toString(b) + r;
	};

	// (protected) convert from radix string
	BigInteger.prototype.fromRadix = function (s, b) {
		this.fromInt(0);
		if (b == null) b = 10;
		var cs = this.chunkSize(b);
		var d = Math.pow(b, cs), mi = false, j = 0, w = 0;
		for (var i = 0; i < s.length; ++i) {
			var x = intAt(s, i);
			if (x < 0) {
				if (s.charAt(i) == "-" && this.signum() == 0) mi = true;
				continue;
			}
			w = b * w + x;
			if (++j >= cs) {
				this.dMultiply(d);
				this.dAddOffset(w, 0);
				j = 0;
				w = 0;
			}
		}
		if (j > 0) {
			this.dMultiply(Math.pow(b, j));
			this.dAddOffset(w, 0);
		}
		if (mi) BigInteger.ZERO.subTo(this, this);
	};

	// (protected) alternate constructor
	BigInteger.prototype.fromNumber = function (a, b, c) {
		if ("number" == typeof b) {
			// new BigInteger(int,int,RNG)
			if (a < 2) this.fromInt(1);
			else {
				this.fromNumber(a, c);
				if (!this.testBit(a - 1))	// force MSB set
					this.bitwiseTo(BigInteger.ONE.shiftLeft(a - 1), op_or, this);
				if (this.isEven()) this.dAddOffset(1, 0); // force odd
				while (!this.isProbablePrime(b)) {
					this.dAddOffset(2, 0);
					if (this.bitLength() > a) this.subTo(BigInteger.ONE.shiftLeft(a - 1), this);
				}
			}
		}
		else {
			// new BigInteger(int,RNG)
			var x = new Array(), t = a & 7;
			x.length = (a >> 3) + 1;
			b.nextBytes(x);
			if (t > 0) x[0] &= ((1 << t) - 1); else x[0] = 0;
			this.fromString(x, 256);
		}
	};

	// (protected) r = this op a (bitwise)
	BigInteger.prototype.bitwiseTo = function (a, op, r) {
		var i, f, m = Math.min(a.t, this.t);
		for (i = 0; i < m; ++i) r[i] = op(this[i], a[i]);
		if (a.t < this.t) {
			f = a.s & this.DM;
			for (i = m; i < this.t; ++i) r[i] = op(this[i], f);
			r.t = this.t;
		}
		else {
			f = this.s & this.DM;
			for (i = m; i < a.t; ++i) r[i] = op(f, a[i]);
			r.t = a.t;
		}
		r.s = op(this.s, a.s);
		r.clamp();
	};

	// (protected) this op (1<<n)
	BigInteger.prototype.changeBit = function (n, op) {
		var r = BigInteger.ONE.shiftLeft(n);
		this.bitwiseTo(r, op, r);
		return r;
	};

	// (protected) r = this + a
	BigInteger.prototype.addTo = function (a, r) {
		var i = 0, c = 0, m = Math.min(a.t, this.t);
		while (i < m) {
			c += this[i] + a[i];
			r[i++] = c & this.DM;
			c >>= this.DB;
		}
		if (a.t < this.t) {
			c += a.s;
			while (i < this.t) {
				c += this[i];
				r[i++] = c & this.DM;
				c >>= this.DB;
			}
			c += this.s;
		}
		else {
			c += this.s;
			while (i < a.t) {
				c += a[i];
				r[i++] = c & this.DM;
				c >>= this.DB;
			}
			c += a.s;
		}
		r.s = (c < 0) ? -1 : 0;
		if (c > 0) r[i++] = c;
		else if (c < -1) r[i++] = this.DV + c;
		r.t = i;
		r.clamp();
	};

	// (protected) this *= n, this >= 0, 1 < n < DV
	BigInteger.prototype.dMultiply = function (n) {
		this[this.t] = this.am(0, n - 1, this, 0, 0, this.t);
		++this.t;
		this.clamp();
	};

	// (protected) this += n << w words, this >= 0
	BigInteger.prototype.dAddOffset = function (n, w) {
		if (n == 0) return;
		while (this.t <= w) this[this.t++] = 0;
		this[w] += n;
		while (this[w] >= this.DV) {
			this[w] -= this.DV;
			if (++w >= this.t) this[this.t++] = 0;
			++this[w];
		}
	};

	// (protected) r = lower n words of "this * a", a.t <= n
	// "this" should be the larger one if appropriate.
	BigInteger.prototype.multiplyLowerTo = function (a, n, r) {
		var i = Math.min(this.t + a.t, n);
		r.s = 0; // assumes a,this >= 0
		r.t = i;
		while (i > 0) r[--i] = 0;
		var j;
		for (j = r.t - this.t; i < j; ++i) r[i + this.t] = this.am(0, a[i], r, i, 0, this.t);
		for (j = Math.min(a.t, n); i < j; ++i) this.am(0, a[i], r, i, 0, n - i);
		r.clamp();
	};


	// (protected) r = "this * a" without lower n words, n > 0
	// "this" should be the larger one if appropriate.
	BigInteger.prototype.multiplyUpperTo = function (a, n, r) {
		--n;
		var i = r.t = this.t + a.t - n;
		r.s = 0; // assumes a,this >= 0
		while (--i >= 0) r[i] = 0;
		for (i = Math.max(n - this.t, 0); i < a.t; ++i)
			r[this.t + i - n] = this.am(n - i, a[i], r, 0, 0, this.t + i - n);
		r.clamp();
		r.drShiftTo(1, r);
	};

	// (protected) this % n, n < 2^26
	BigInteger.prototype.modInt = function (n) {
		if (n <= 0) return 0;
		var d = this.DV % n, r = (this.s < 0) ? n - 1 : 0;
		if (this.t > 0)
			if (d == 0) r = this[0] % n;
			else for (var i = this.t - 1; i >= 0; --i) r = (d * r + this[i]) % n;
		return r;
	};


	// (protected) true if probably prime (HAC 4.24, Miller-Rabin)
	BigInteger.prototype.millerRabin = function (t) {
		var n1 = this.subtract(BigInteger.ONE);
		var k = n1.getLowestSetBit();
		if (k <= 0) return false;
		var r = n1.shiftRight(k);
		t = (t + 1) >> 1;
		if (t > lowprimes.length) t = lowprimes.length;
		var a = nbi();
		for (var i = 0; i < t; ++i) {
			//Pick bases at random, instead of starting at 2
			a.fromInt(lowprimes[Math.floor(Math.random() * lowprimes.length)]);
			var y = a.modPow(r, this);
			if (y.compareTo(BigInteger.ONE) != 0 && y.compareTo(n1) != 0) {
				var j = 1;
				while (j++ < k && y.compareTo(n1) != 0) {
					y = y.modPowInt(2, this);
					if (y.compareTo(BigInteger.ONE) == 0) return false;
				}
				if (y.compareTo(n1) != 0) return false;
			}
		}
		return true;
	};



	// (public)
	BigInteger.prototype.clone = function () { var r = nbi(); this.copyTo(r); return r; };

	// (public) return value as integer
	BigInteger.prototype.intValue = function () {
		if (this.s < 0) {
			if (this.t == 1) return this[0] - this.DV;
			else if (this.t == 0) return -1;
		}
		else if (this.t == 1) return this[0];
		else if (this.t == 0) return 0;
		// assumes 16 < DB < 32
		return ((this[1] & ((1 << (32 - this.DB)) - 1)) << this.DB) | this[0];
	};


	// (public) return value as byte
	BigInteger.prototype.byteValue = function () { return (this.t == 0) ? this.s : (this[0] << 24) >> 24; };

	// (public) return value as short (assumes DB>=16)
	BigInteger.prototype.shortValue = function () { return (this.t == 0) ? this.s : (this[0] << 16) >> 16; };

	// (public) 0 if this == 0, 1 if this > 0
	BigInteger.prototype.signum = function () {
		if (this.s < 0) return -1;
		else if (this.t <= 0 || (this.t == 1 && this[0] <= 0)) return 0;
		else return 1;
	};


	// (public) convert to bigendian byte array
	BigInteger.prototype.toByteArray = function () {
		var i = this.t, r = new Array();
		r[0] = this.s;
		var p = this.DB - (i * this.DB) % 8, d, k = 0;
		if (i-- > 0) {
			if (p < this.DB && (d = this[i] >> p) != (this.s & this.DM) >> p)
				r[k++] = d | (this.s << (this.DB - p));
			while (i >= 0) {
				if (p < 8) {
					d = (this[i] & ((1 << p) - 1)) << (8 - p);
					d |= this[--i] >> (p += this.DB - 8);
				}
				else {
					d = (this[i] >> (p -= 8)) & 0xff;
					if (p <= 0) { p += this.DB; --i; }
				}
				if ((d & 0x80) != 0) d |= -256;
				if (k == 0 && (this.s & 0x80) != (d & 0x80)) ++k;
				if (k > 0 || d != this.s) r[k++] = d;
			}
		}
		return r;
	};

	BigInteger.prototype.equals = function (a) { return (this.compareTo(a) == 0); };
	BigInteger.prototype.min = function (a) { return (this.compareTo(a) < 0) ? this : a; };
	BigInteger.prototype.max = function (a) { return (this.compareTo(a) > 0) ? this : a; };

	// (public) this & a
	function op_and(x, y) { return x & y; }
	BigInteger.prototype.and = function (a) { var r = nbi(); this.bitwiseTo(a, op_and, r); return r; };

	// (public) this | a
	function op_or(x, y) { return x | y; }
	BigInteger.prototype.or = function (a) { var r = nbi(); this.bitwiseTo(a, op_or, r); return r; };

	// (public) this ^ a
	function op_xor(x, y) { return x ^ y; }
	BigInteger.prototype.xor = function (a) { var r = nbi(); this.bitwiseTo(a, op_xor, r); return r; };

	// (public) this & ~a
	function op_andnot(x, y) { return x & ~y; }
	BigInteger.prototype.andNot = function (a) { var r = nbi(); this.bitwiseTo(a, op_andnot, r); return r; };

	// (public) ~this
	BigInteger.prototype.not = function () {
		var r = nbi();
		for (var i = 0; i < this.t; ++i) r[i] = this.DM & ~this[i];
		r.t = this.t;
		r.s = ~this.s;
		return r;
	};

	// (public) this << n
	BigInteger.prototype.shiftLeft = function (n) {
		var r = nbi();
		if (n < 0) this.rShiftTo(-n, r); else this.lShiftTo(n, r);
		return r;
	};

	// (public) this >> n
	BigInteger.prototype.shiftRight = function (n) {
		var r = nbi();
		if (n < 0) this.lShiftTo(-n, r); else this.rShiftTo(n, r);
		return r;
	};

	// (public) returns index of lowest 1-bit (or -1 if none)
	BigInteger.prototype.getLowestSetBit = function () {
		for (var i = 0; i < this.t; ++i)
			if (this[i] != 0) return i * this.DB + lbit(this[i]);
		if (this.s < 0) return this.t * this.DB;
		return -1;
	};

	// (public) return number of set bits
	BigInteger.prototype.bitCount = function () {
		var r = 0, x = this.s & this.DM;
		for (var i = 0; i < this.t; ++i) r += cbit(this[i] ^ x);
		return r;
	};

	// (public) true iff nth bit is set
	BigInteger.prototype.testBit = function (n) {
		var j = Math.floor(n / this.DB);
		if (j >= this.t) return (this.s != 0);
		return ((this[j] & (1 << (n % this.DB))) != 0);
	};

	// (public) this | (1<<n)
	BigInteger.prototype.setBit = function (n) { return this.changeBit(n, op_or); };
	// (public) this & ~(1<<n)
	BigInteger.prototype.clearBit = function (n) { return this.changeBit(n, op_andnot); };
	// (public) this ^ (1<<n)
	BigInteger.prototype.flipBit = function (n) { return this.changeBit(n, op_xor); };
	// (public) this + a
	BigInteger.prototype.add = function (a) { var r = nbi(); this.addTo(a, r); return r; };
	// (public) this - a
	BigInteger.prototype.subtract = function (a) { var r = nbi(); this.subTo(a, r); return r; };
	// (public) this * a
	BigInteger.prototype.multiply = function (a) { var r = nbi(); this.multiplyTo(a, r); return r; };
	// (public) this / a
	BigInteger.prototype.divide = function (a) { var r = nbi(); this.divRemTo(a, r, null); return r; };
	// (public) this % a
	BigInteger.prototype.remainder = function (a) { var r = nbi(); this.divRemTo(a, null, r); return r; };
	// (public) [this/a,this%a]
	BigInteger.prototype.divideAndRemainder = function (a) {
		var q = nbi(), r = nbi();
		this.divRemTo(a, q, r);
		return new Array(q, r);
	};

	// (public) this^e % m (HAC 14.85)
	BigInteger.prototype.modPow = function (e, m) {
		var i = e.bitLength(), k, r = nbv(1), z;
		if (i <= 0) return r;
		else if (i < 18) k = 1;
		else if (i < 48) k = 3;
		else if (i < 144) k = 4;
		else if (i < 768) k = 5;
		else k = 6;
		if (i < 8)
			z = new Classic(m);
		else if (m.isEven())
			z = new Barrett(m);
		else
			z = new Montgomery(m);

		// precomputation
		var g = new Array(), n = 3, k1 = k - 1, km = (1 << k) - 1;
		g[1] = z.convert(this);
		if (k > 1) {
			var g2 = nbi();
			z.sqrTo(g[1], g2);
			while (n <= km) {
				g[n] = nbi();
				z.mulTo(g2, g[n - 2], g[n]);
				n += 2;
			}
		}

		var j = e.t - 1, w, is1 = true, r2 = nbi(), t;
		i = nbits(e[j]) - 1;
		while (j >= 0) {
			if (i >= k1) w = (e[j] >> (i - k1)) & km;
			else {
				w = (e[j] & ((1 << (i + 1)) - 1)) << (k1 - i);
				if (j > 0) w |= e[j - 1] >> (this.DB + i - k1);
			}

			n = k;
			while ((w & 1) == 0) { w >>= 1; --n; }
			if ((i -= n) < 0) { i += this.DB; --j; }
			if (is1) {	// ret == 1, don't bother squaring or multiplying it
				g[w].copyTo(r);
				is1 = false;
			}
			else {
				while (n > 1) { z.sqrTo(r, r2); z.sqrTo(r2, r); n -= 2; }
				if (n > 0) z.sqrTo(r, r2); else { t = r; r = r2; r2 = t; }
				z.mulTo(r2, g[w], r);
			}

			while (j >= 0 && (e[j] & (1 << i)) == 0) {
				z.sqrTo(r, r2); t = r; r = r2; r2 = t;
				if (--i < 0) { i = this.DB - 1; --j; }
			}
		}
		return z.revert(r);
	};

	// (public) 1/this % m (HAC 14.61)
	BigInteger.prototype.modInverse = function (m) {
		var ac = m.isEven();
		if ((this.isEven() && ac) || m.signum() == 0) return BigInteger.ZERO;
		var u = m.clone(), v = this.clone();
		var a = nbv(1), b = nbv(0), c = nbv(0), d = nbv(1);
		while (u.signum() != 0) {
			while (u.isEven()) {
				u.rShiftTo(1, u);
				if (ac) {
					if (!a.isEven() || !b.isEven()) { a.addTo(this, a); b.subTo(m, b); }
					a.rShiftTo(1, a);
				}
				else if (!b.isEven()) b.subTo(m, b);
				b.rShiftTo(1, b);
			}
			while (v.isEven()) {
				v.rShiftTo(1, v);
				if (ac) {
					if (!c.isEven() || !d.isEven()) { c.addTo(this, c); d.subTo(m, d); }
					c.rShiftTo(1, c);
				}
				else if (!d.isEven()) d.subTo(m, d);
				d.rShiftTo(1, d);
			}
			if (u.compareTo(v) >= 0) {
				u.subTo(v, u);
				if (ac) a.subTo(c, a);
				b.subTo(d, b);
			}
			else {
				v.subTo(u, v);
				if (ac) c.subTo(a, c);
				d.subTo(b, d);
			}
		}
		if (v.compareTo(BigInteger.ONE) != 0) return BigInteger.ZERO;
		if (d.compareTo(m) >= 0) return d.subtract(m);
		if (d.signum() < 0) d.addTo(m, d); else return d;
		if (d.signum() < 0) return d.add(m); else return d;
	};


	// (public) this^e
	BigInteger.prototype.pow = function (e) { return this.exp(e, new NullExp()); };

	// (public) gcd(this,a) (HAC 14.54)
	BigInteger.prototype.gcd = function (a) {
		var x = (this.s < 0) ? this.negate() : this.clone();
		var y = (a.s < 0) ? a.negate() : a.clone();
		if (x.compareTo(y) < 0) { var t = x; x = y; y = t; }
		var i = x.getLowestSetBit(), g = y.getLowestSetBit();
		if (g < 0) return x;
		if (i < g) g = i;
		if (g > 0) {
			x.rShiftTo(g, x);
			y.rShiftTo(g, y);
		}
		while (x.signum() > 0) {
			if ((i = x.getLowestSetBit()) > 0) x.rShiftTo(i, x);
			if ((i = y.getLowestSetBit()) > 0) y.rShiftTo(i, y);
			if (x.compareTo(y) >= 0) {
				x.subTo(y, x);
				x.rShiftTo(1, x);
			}
			else {
				y.subTo(x, y);
				y.rShiftTo(1, y);
			}
		}
		if (g > 0) y.lShiftTo(g, y);
		return y;
	};

	// (public) test primality with certainty >= 1-.5^t
	BigInteger.prototype.isProbablePrime = function (t) {
		var i, x = this.abs();
		if (x.t == 1 && x[0] <= lowprimes[lowprimes.length - 1]) {
			for (i = 0; i < lowprimes.length; ++i)
				if (x[0] == lowprimes[i]) return true;
			return false;
		}
		if (x.isEven()) return false;
		i = 1;
		while (i < lowprimes.length) {
			var m = lowprimes[i], j = i + 1;
			while (j < lowprimes.length && m < lplim) m *= lowprimes[j++];
			m = x.modInt(m);
			while (i < j) if (m % lowprimes[i++] == 0) return false;
		}
		return x.millerRabin(t);
	};


	// JSBN-specific extension

	// (public) this^2
	BigInteger.prototype.square = function () { var r = nbi(); this.squareTo(r); return r; };


	// NOTE: BigInteger interfaces not implemented in jsbn:
	// BigInteger(int signum, byte[] magnitude)
	// double doubleValue()
	// float floatValue()
	// int hashCode()
	// long longValue()
	// static BigInteger valueOf(long val)



	// Copyright Stephan Thomas (start) --- //
	// https://raw.github.com/bitcoinjs/bitcoinjs-lib/07f9d55ccb6abd962efb6befdd37671f85ea4ff9/src/util.js
	// BigInteger monkey patching
	BigInteger.valueOf = nbv;

	/**
	* Returns a byte array representation of the big integer.
	*
	* This returns the absolute of the contained value in big endian
	* form. A value of zero results in an empty array.
	*/
	BigInteger.prototype.toByteArrayUnsigned = function () {
		var ba = this.abs().toByteArray();
		if (ba.length) {
			if (ba[0] == 0) {
				ba = ba.slice(1);
			}
			return ba.map(function (v) {
				return (v < 0) ? v + 256 : v;
			});
		} else {
			// Empty array, nothing to do
			return ba;
		}
	};

	/**
	* Turns a byte array into a big integer.
	*
	* This function will interpret a byte array as a big integer in big
	* endian notation and ignore leading zeros.
	*/
	BigInteger.fromByteArrayUnsigned = function (ba) {
		if (!ba.length) {
			return ba.valueOf(0);
		} else if (ba[0] & 0x80) {
			// Prepend a zero so the BigInteger class doesn't mistake this
			// for a negative integer.
			return new BigInteger([0].concat(ba));
		} else {
			return new BigInteger(ba);
		}
	};

	/**
	* Converts big integer to signed byte representation.
	*
	* The format for this value uses a the most significant bit as a sign
	* bit. If the most significant bit is already occupied by the
	* absolute value, an extra byte is prepended and the sign bit is set
	* there.
	*
	* Examples:
	*
	*      0 =>     0x00
	*      1 =>     0x01
	*     -1 =>     0x81
	*    127 =>     0x7f
	*   -127 =>     0xff
	*    128 =>   0x0080
	*   -128 =>   0x8080
	*    255 =>   0x00ff
	*   -255 =>   0x80ff
	*  16300 =>   0x3fac
	* -16300 =>   0xbfac
	*  62300 => 0x00f35c
	* -62300 => 0x80f35c
	*/
	BigInteger.prototype.toByteArraySigned = function () {
		var val = this.abs().toByteArrayUnsigned();
		var neg = this.compareTo(BigInteger.ZERO) < 0;

		if (neg) {
			if (val[0] & 0x80) {
				val.unshift(0x80);
			} else {
				val[0] |= 0x80;
			}
		} else {
			if (val[0] & 0x80) {
				val.unshift(0x00);
			}
		}

		return val;
	};

	/**
	* Parse a signed big integer byte representation.
	*
	* For details on the format please see BigInteger.toByteArraySigned.
	*/
	BigInteger.fromByteArraySigned = function (ba) {
		// Check for negative value
		if (ba[0] & 0x80) {
			// Remove sign bit
			ba[0] &= 0x7f;

			return BigInteger.fromByteArrayUnsigned(ba).negate();
		} else {
			return BigInteger.fromByteArrayUnsigned(ba);
		}
	};
	// Copyright Stephan Thomas (end) --- //




	// ****** REDUCTION ******* //

	// Modular reduction using "classic" algorithm
	var Classic = window.Classic = function Classic(m) { this.m = m; }
	Classic.prototype.convert = function (x) {
		if (x.s < 0 || x.compareTo(this.m) >= 0) return x.mod(this.m);
		else return x;
	};
	Classic.prototype.revert = function (x) { return x; };
	Classic.prototype.reduce = function (x) { x.divRemTo(this.m, null, x); };
	Classic.prototype.mulTo = function (x, y, r) { x.multiplyTo(y, r); this.reduce(r); };
	Classic.prototype.sqrTo = function (x, r) { x.squareTo(r); this.reduce(r); };





	// Montgomery reduction
	var Montgomery = window.Montgomery = function Montgomery(m) {
		this.m = m;
		this.mp = m.invDigit();
		this.mpl = this.mp & 0x7fff;
		this.mph = this.mp >> 15;
		this.um = (1 << (m.DB - 15)) - 1;
		this.mt2 = 2 * m.t;
	}
	// xR mod m
	Montgomery.prototype.convert = function (x) {
		var r = nbi();
		x.abs().dlShiftTo(this.m.t, r);
		r.divRemTo(this.m, null, r);
		if (x.s < 0 && r.compareTo(BigInteger.ZERO) > 0) this.m.subTo(r, r);
		return r;
	}
	// x/R mod m
	Montgomery.prototype.revert = function (x) {
		var r = nbi();
		x.copyTo(r);
		this.reduce(r);
		return r;
	};
	// x = x/R mod m (HAC 14.32)
	Montgomery.prototype.reduce = function (x) {
		while (x.t <= this.mt2)	// pad x so am has enough room later
			x[x.t++] = 0;
		for (var i = 0; i < this.m.t; ++i) {
			// faster way of calculating u0 = x[i]*mp mod DV
			var j = x[i] & 0x7fff;
			var u0 = (j * this.mpl + (((j * this.mph + (x[i] >> 15) * this.mpl) & this.um) << 15)) & x.DM;
			// use am to combine the multiply-shift-add into one call
			j = i + this.m.t;
			x[j] += this.m.am(0, u0, x, i, 0, this.m.t);
			// propagate carry
			while (x[j] >= x.DV) { x[j] -= x.DV; x[++j]++; }
		}
		x.clamp();
		x.drShiftTo(this.m.t, x);
		if (x.compareTo(this.m) >= 0) x.subTo(this.m, x);
	};
	// r = "xy/R mod m"; x,y != r
	Montgomery.prototype.mulTo = function (x, y, r) { x.multiplyTo(y, r); this.reduce(r); };
	// r = "x^2/R mod m"; x != r
	Montgomery.prototype.sqrTo = function (x, r) { x.squareTo(r); this.reduce(r); };





	// A "null" reducer
	var NullExp = window.NullExp = function NullExp() { }
	NullExp.prototype.convert = function (x) { return x; };
	NullExp.prototype.revert = function (x) { return x; };
	NullExp.prototype.mulTo = function (x, y, r) { x.multiplyTo(y, r); };
	NullExp.prototype.sqrTo = function (x, r) { x.squareTo(r); };





	// Barrett modular reduction
	var Barrett = window.Barrett = function Barrett(m) {
		// setup Barrett
		this.r2 = nbi();
		this.q3 = nbi();
		BigInteger.ONE.dlShiftTo(2 * m.t, this.r2);
		this.mu = this.r2.divide(m);
		this.m = m;
	}
	Barrett.prototype.convert = function (x) {
		if (x.s < 0 || x.t > 2 * this.m.t) return x.mod(this.m);
		else if (x.compareTo(this.m) < 0) return x;
		else { var r = nbi(); x.copyTo(r); this.reduce(r); return r; }
	};
	Barrett.prototype.revert = function (x) { return x; };
	// x = x mod m (HAC 14.42)
	Barrett.prototype.reduce = function (x) {
		x.drShiftTo(this.m.t - 1, this.r2);
		if (x.t > this.m.t + 1) { x.t = this.m.t + 1; x.clamp(); }
		this.mu.multiplyUpperTo(this.r2, this.m.t + 1, this.q3);
		this.m.multiplyLowerTo(this.q3, this.m.t + 1, this.r2);
		while (x.compareTo(this.r2) < 0) x.dAddOffset(1, this.m.t + 1);
		x.subTo(this.r2, x);
		while (x.compareTo(this.m) >= 0) x.subTo(this.m, x);
	};
	// r = x*y mod m; x,y != r
	Barrett.prototype.mulTo = function (x, y, r) { x.multiplyTo(y, r); this.reduce(r); };
	// r = x^2 mod m; x != r
	Barrett.prototype.sqrTo = function (x, r) { x.squareTo(r); this.reduce(r); };

})();
  </script>
  <script type="text/javascript">
//---------------------------------------------------------------------
// QRCode for JavaScript
//
// Copyright (c) 2009 Kazuhiko Arase
//
// URL: http://www.d-project.com/
//
// Licensed under the MIT license:
//   http://www.opensource.org/licenses/mit-license.php
//
// The word "QR Code" is registered trademark of
// DENSO WAVE INCORPORATED
//   http://www.denso-wave.com/qrcode/faqpatent-e.html
//
//---------------------------------------------------------------------

(function () {
	//---------------------------------------------------------------------
	// QRCode
	//---------------------------------------------------------------------

	var QRCode = window.QRCode = function (typeNumber, errorCorrectLevel) {
		this.typeNumber = typeNumber;
		this.errorCorrectLevel = errorCorrectLevel;
		this.modules = null;
		this.moduleCount = 0;
		this.dataCache = null;
		this.dataList = new Array();
	}

	QRCode.prototype = {

		addData: function (data) {
			var newData = new QRCode.QR8bitByte(data);
			this.dataList.push(newData);
			this.dataCache = null;
		},

		isDark: function (row, col) {
			if (row < 0 || this.moduleCount <= row || col < 0 || this.moduleCount <= col) {
				throw new Error(row + "," + col);
			}
			return this.modules[row][col];
		},

		getModuleCount: function () {
			return this.moduleCount;
		},

		make: function () {
			this.makeImpl(false, this.getBestMaskPattern());
		},

		makeImpl: function (test, maskPattern) {

			this.moduleCount = this.typeNumber * 4 + 17;
			this.modules = new Array(this.moduleCount);

			for (var row = 0; row < this.moduleCount; row++) {

				this.modules[row] = new Array(this.moduleCount);

				for (var col = 0; col < this.moduleCount; col++) {
					this.modules[row][col] = null; //(col + row) % 3;
				}
			}

			this.setupPositionProbePattern(0, 0);
			this.setupPositionProbePattern(this.moduleCount - 7, 0);
			this.setupPositionProbePattern(0, this.moduleCount - 7);
			this.setupPositionAdjustPattern();
			this.setupTimingPattern();
			this.setupTypeInfo(test, maskPattern);

			if (this.typeNumber >= 7) {
				this.setupTypeNumber(test);
			}

			if (this.dataCache == null) {
				this.dataCache = QRCode.createData(this.typeNumber, this.errorCorrectLevel, this.dataList);
			}

			this.mapData(this.dataCache, maskPattern);
		},

		setupPositionProbePattern: function (row, col) {

			for (var r = -1; r <= 7; r++) {

				if (row + r <= -1 || this.moduleCount <= row + r) continue;

				for (var c = -1; c <= 7; c++) {

					if (col + c <= -1 || this.moduleCount <= col + c) continue;

					if ((0 <= r && r <= 6 && (c == 0 || c == 6))
						|| (0 <= c && c <= 6 && (r == 0 || r == 6))
						|| (2 <= r && r <= 4 && 2 <= c && c <= 4)) {
						this.modules[row + r][col + c] = true;
					} else {
						this.modules[row + r][col + c] = false;
					}
				}
			}
		},

		getBestMaskPattern: function () {

			var minLostPoint = 0;
			var pattern = 0;

			for (var i = 0; i < 8; i++) {

				this.makeImpl(true, i);

				var lostPoint = QRCode.Util.getLostPoint(this);

				if (i == 0 || minLostPoint > lostPoint) {
					minLostPoint = lostPoint;
					pattern = i;
				}
			}

			return pattern;
		},

		createMovieClip: function (target_mc, instance_name, depth) {

			var qr_mc = target_mc.createEmptyMovieClip(instance_name, depth);
			var cs = 1;

			this.make();

			for (var row = 0; row < this.modules.length; row++) {

				var y = row * cs;

				for (var col = 0; col < this.modules[row].length; col++) {

					var x = col * cs;
					var dark = this.modules[row][col];

					if (dark) {
						qr_mc.beginFill(0, 100);
						qr_mc.moveTo(x, y);
						qr_mc.lineTo(x + cs, y);
						qr_mc.lineTo(x + cs, y + cs);
						qr_mc.lineTo(x, y + cs);
						qr_mc.endFill();
					}
				}
			}

			return qr_mc;
		},

		setupTimingPattern: function () {

			for (var r = 8; r < this.moduleCount - 8; r++) {
				if (this.modules[r][6] != null) {
					continue;
				}
				this.modules[r][6] = (r % 2 == 0);
			}

			for (var c = 8; c < this.moduleCount - 8; c++) {
				if (this.modules[6][c] != null) {
					continue;
				}
				this.modules[6][c] = (c % 2 == 0);
			}
		},

		setupPositionAdjustPattern: function () {

			var pos = QRCode.Util.getPatternPosition(this.typeNumber);

			for (var i = 0; i < pos.length; i++) {

				for (var j = 0; j < pos.length; j++) {

					var row = pos[i];
					var col = pos[j];

					if (this.modules[row][col] != null) {
						continue;
					}

					for (var r = -2; r <= 2; r++) {

						for (var c = -2; c <= 2; c++) {

							if (r == -2 || r == 2 || c == -2 || c == 2
								|| (r == 0 && c == 0)) {
								this.modules[row + r][col + c] = true;
							} else {
								this.modules[row + r][col + c] = false;
							}
						}
					}
				}
			}
		},

		setupTypeNumber: function (test) {

			var bits = QRCode.Util.getBCHTypeNumber(this.typeNumber);

			for (var i = 0; i < 18; i++) {
				var mod = (!test && ((bits >> i) & 1) == 1);
				this.modules[Math.floor(i / 3)][i % 3 + this.moduleCount - 8 - 3] = mod;
			}

			for (var i = 0; i < 18; i++) {
				var mod = (!test && ((bits >> i) & 1) == 1);
				this.modules[i % 3 + this.moduleCount - 8 - 3][Math.floor(i / 3)] = mod;
			}
		},

		setupTypeInfo: function (test, maskPattern) {

			var data = (this.errorCorrectLevel << 3) | maskPattern;
			var bits = QRCode.Util.getBCHTypeInfo(data);

			// vertical
			for (var i = 0; i < 15; i++) {

				var mod = (!test && ((bits >> i) & 1) == 1);

				if (i < 6) {
					this.modules[i][8] = mod;
				} else if (i < 8) {
					this.modules[i + 1][8] = mod;
				} else {
					this.modules[this.moduleCount - 15 + i][8] = mod;
				}
			}

			// horizontal
			for (var i = 0; i < 15; i++) {

				var mod = (!test && ((bits >> i) & 1) == 1);

				if (i < 8) {
					this.modules[8][this.moduleCount - i - 1] = mod;
				} else if (i < 9) {
					this.modules[8][15 - i - 1 + 1] = mod;
				} else {
					this.modules[8][15 - i - 1] = mod;
				}
			}

			// fixed module
			this.modules[this.moduleCount - 8][8] = (!test);

		},

		mapData: function (data, maskPattern) {

			var inc = -1;
			var row = this.moduleCount - 1;
			var bitIndex = 7;
			var byteIndex = 0;

			for (var col = this.moduleCount - 1; col > 0; col -= 2) {

				if (col == 6) col--;

				while (true) {

					for (var c = 0; c < 2; c++) {

						if (this.modules[row][col - c] == null) {

							var dark = false;

							if (byteIndex < data.length) {
								dark = (((data[byteIndex] >>> bitIndex) & 1) == 1);
							}

							var mask = QRCode.Util.getMask(maskPattern, row, col - c);

							if (mask) {
								dark = !dark;
							}

							this.modules[row][col - c] = dark;
							bitIndex--;

							if (bitIndex == -1) {
								byteIndex++;
								bitIndex = 7;
							}
						}
					}

					row += inc;

					if (row < 0 || this.moduleCount <= row) {
						row -= inc;
						inc = -inc;
						break;
					}
				}
			}

		}

	};

	QRCode.PAD0 = 0xEC;
	QRCode.PAD1 = 0x11;

	QRCode.createData = function (typeNumber, errorCorrectLevel, dataList) {

		var rsBlocks = QRCode.RSBlock.getRSBlocks(typeNumber, errorCorrectLevel);

		var buffer = new QRCode.BitBuffer();

		for (var i = 0; i < dataList.length; i++) {
			var data = dataList[i];
			buffer.put(data.mode, 4);
			buffer.put(data.getLength(), QRCode.Util.getLengthInBits(data.mode, typeNumber));
			data.write(buffer);
		}

		// calc num max data.
		var totalDataCount = 0;
		for (var i = 0; i < rsBlocks.length; i++) {
			totalDataCount += rsBlocks[i].dataCount;
		}

		if (buffer.getLengthInBits() > totalDataCount * 8) {
			throw new Error("code length overflow. ("
			+ buffer.getLengthInBits()
			+ ">"
			+ totalDataCount * 8
			+ ")");
		}

		// end code
		if (buffer.getLengthInBits() + 4 <= totalDataCount * 8) {
			buffer.put(0, 4);
		}

		// padding
		while (buffer.getLengthInBits() % 8 != 0) {
			buffer.putBit(false);
		}

		// padding
		while (true) {

			if (buffer.getLengthInBits() >= totalDataCount * 8) {
				break;
			}
			buffer.put(QRCode.PAD0, 8);

			if (buffer.getLengthInBits() >= totalDataCount * 8) {
				break;
			}
			buffer.put(QRCode.PAD1, 8);
		}

		return QRCode.createBytes(buffer, rsBlocks);
	};

	QRCode.createBytes = function (buffer, rsBlocks) {

		var offset = 0;

		var maxDcCount = 0;
		var maxEcCount = 0;

		var dcdata = new Array(rsBlocks.length);
		var ecdata = new Array(rsBlocks.length);

		for (var r = 0; r < rsBlocks.length; r++) {

			var dcCount = rsBlocks[r].dataCount;
			var ecCount = rsBlocks[r].totalCount - dcCount;

			maxDcCount = Math.max(maxDcCount, dcCount);
			maxEcCount = Math.max(maxEcCount, ecCount);

			dcdata[r] = new Array(dcCount);

			for (var i = 0; i < dcdata[r].length; i++) {
				dcdata[r][i] = 0xff & buffer.buffer[i + offset];
			}
			offset += dcCount;

			var rsPoly = QRCode.Util.getErrorCorrectPolynomial(ecCount);
			var rawPoly = new QRCode.Polynomial(dcdata[r], rsPoly.getLength() - 1);

			var modPoly = rawPoly.mod(rsPoly);
			ecdata[r] = new Array(rsPoly.getLength() - 1);
			for (var i = 0; i < ecdata[r].length; i++) {
				var modIndex = i + modPoly.getLength() - ecdata[r].length;
				ecdata[r][i] = (modIndex >= 0) ? modPoly.get(modIndex) : 0;
			}

		}

		var totalCodeCount = 0;
		for (var i = 0; i < rsBlocks.length; i++) {
			totalCodeCount += rsBlocks[i].totalCount;
		}

		var data = new Array(totalCodeCount);
		var index = 0;

		for (var i = 0; i < maxDcCount; i++) {
			for (var r = 0; r < rsBlocks.length; r++) {
				if (i < dcdata[r].length) {
					data[index++] = dcdata[r][i];
				}
			}
		}

		for (var i = 0; i < maxEcCount; i++) {
			for (var r = 0; r < rsBlocks.length; r++) {
				if (i < ecdata[r].length) {
					data[index++] = ecdata[r][i];
				}
			}
		}

		return data;

	};

	//---------------------------------------------------------------------
	// QR8bitByte
	//---------------------------------------------------------------------
	QRCode.QR8bitByte = function (data) {
		this.mode = QRCode.Mode.MODE_8BIT_BYTE;
		this.data = data;
	}

	QRCode.QR8bitByte.prototype = {
		getLength: function (buffer) {
			return this.data.length;
		},

		write: function (buffer) {
			for (var i = 0; i < this.data.length; i++) {
				// not JIS ...
				buffer.put(this.data.charCodeAt(i), 8);
			}
		}
	};


	//---------------------------------------------------------------------
	// QRMode
	//---------------------------------------------------------------------
	QRCode.Mode = {
		MODE_NUMBER: 1 << 0,
		MODE_ALPHA_NUM: 1 << 1,
		MODE_8BIT_BYTE: 1 << 2,
		MODE_KANJI: 1 << 3
	};

	//---------------------------------------------------------------------
	// QRErrorCorrectLevel
	//---------------------------------------------------------------------
	QRCode.ErrorCorrectLevel = {
		L: 1,
		M: 0,
		Q: 3,
		H: 2
	};


	//---------------------------------------------------------------------
	// QRMaskPattern
	//---------------------------------------------------------------------
	QRCode.MaskPattern = {
		PATTERN000: 0,
		PATTERN001: 1,
		PATTERN010: 2,
		PATTERN011: 3,
		PATTERN100: 4,
		PATTERN101: 5,
		PATTERN110: 6,
		PATTERN111: 7
	};

	//---------------------------------------------------------------------
	// QRUtil
	//---------------------------------------------------------------------

	QRCode.Util = {

		PATTERN_POSITION_TABLE: [
		[],
		[6, 18],
		[6, 22],
		[6, 26],
		[6, 30],
		[6, 34],
		[6, 22, 38],
		[6, 24, 42],
		[6, 26, 46],
		[6, 28, 50],
		[6, 30, 54],
		[6, 32, 58],
		[6, 34, 62],
		[6, 26, 46, 66],
		[6, 26, 48, 70],
		[6, 26, 50, 74],
		[6, 30, 54, 78],
		[6, 30, 56, 82],
		[6, 30, 58, 86],
		[6, 34, 62, 90],
		[6, 28, 50, 72, 94],
		[6, 26, 50, 74, 98],
		[6, 30, 54, 78, 102],
		[6, 28, 54, 80, 106],
		[6, 32, 58, 84, 110],
		[6, 30, 58, 86, 114],
		[6, 34, 62, 90, 118],
		[6, 26, 50, 74, 98, 122],
		[6, 30, 54, 78, 102, 126],
		[6, 26, 52, 78, 104, 130],
		[6, 30, 56, 82, 108, 134],
		[6, 34, 60, 86, 112, 138],
		[6, 30, 58, 86, 114, 142],
		[6, 34, 62, 90, 118, 146],
		[6, 30, 54, 78, 102, 126, 150],
		[6, 24, 50, 76, 102, 128, 154],
		[6, 28, 54, 80, 106, 132, 158],
		[6, 32, 58, 84, 110, 136, 162],
		[6, 26, 54, 82, 110, 138, 166],
		[6, 30, 58, 86, 114, 142, 170]
	],

		G15: (1 << 10) | (1 << 8) | (1 << 5) | (1 << 4) | (1 << 2) | (1 << 1) | (1 << 0),
		G18: (1 << 12) | (1 << 11) | (1 << 10) | (1 << 9) | (1 << 8) | (1 << 5) | (1 << 2) | (1 << 0),
		G15_MASK: (1 << 14) | (1 << 12) | (1 << 10) | (1 << 4) | (1 << 1),

		getBCHTypeInfo: function (data) {
			var d = data << 10;
			while (QRCode.Util.getBCHDigit(d) - QRCode.Util.getBCHDigit(QRCode.Util.G15) >= 0) {
				d ^= (QRCode.Util.G15 << (QRCode.Util.getBCHDigit(d) - QRCode.Util.getBCHDigit(QRCode.Util.G15)));
			}
			return ((data << 10) | d) ^ QRCode.Util.G15_MASK;
		},

		getBCHTypeNumber: function (data) {
			var d = data << 12;
			while (QRCode.Util.getBCHDigit(d) - QRCode.Util.getBCHDigit(QRCode.Util.G18) >= 0) {
				d ^= (QRCode.Util.G18 << (QRCode.Util.getBCHDigit(d) - QRCode.Util.getBCHDigit(QRCode.Util.G18)));
			}
			return (data << 12) | d;
		},

		getBCHDigit: function (data) {

			var digit = 0;

			while (data != 0) {
				digit++;
				data >>>= 1;
			}

			return digit;
		},

		getPatternPosition: function (typeNumber) {
			return QRCode.Util.PATTERN_POSITION_TABLE[typeNumber - 1];
		},

		getMask: function (maskPattern, i, j) {

			switch (maskPattern) {

				case QRCode.MaskPattern.PATTERN000: return (i + j) % 2 == 0;
				case QRCode.MaskPattern.PATTERN001: return i % 2 == 0;
				case QRCode.MaskPattern.PATTERN010: return j % 3 == 0;
				case QRCode.MaskPattern.PATTERN011: return (i + j) % 3 == 0;
				case QRCode.MaskPattern.PATTERN100: return (Math.floor(i / 2) + Math.floor(j / 3)) % 2 == 0;
				case QRCode.MaskPattern.PATTERN101: return (i * j) % 2 + (i * j) % 3 == 0;
				case QRCode.MaskPattern.PATTERN110: return ((i * j) % 2 + (i * j) % 3) % 2 == 0;
				case QRCode.MaskPattern.PATTERN111: return ((i * j) % 3 + (i + j) % 2) % 2 == 0;

				default:
					throw new Error("bad maskPattern:" + maskPattern);
			}
		},

		getErrorCorrectPolynomial: function (errorCorrectLength) {

			var a = new QRCode.Polynomial([1], 0);

			for (var i = 0; i < errorCorrectLength; i++) {
				a = a.multiply(new QRCode.Polynomial([1, QRCode.Math.gexp(i)], 0));
			}

			return a;
		},

		getLengthInBits: function (mode, type) {

			if (1 <= type && type < 10) {

				// 1 - 9

				switch (mode) {
					case QRCode.Mode.MODE_NUMBER: return 10;
					case QRCode.Mode.MODE_ALPHA_NUM: return 9;
					case QRCode.Mode.MODE_8BIT_BYTE: return 8;
					case QRCode.Mode.MODE_KANJI: return 8;
					default:
						throw new Error("mode:" + mode);
				}

			} else if (type < 27) {

				// 10 - 26

				switch (mode) {
					case QRCode.Mode.MODE_NUMBER: return 12;
					case QRCode.Mode.MODE_ALPHA_NUM: return 11;
					case QRCode.Mode.MODE_8BIT_BYTE: return 16;
					case QRCode.Mode.MODE_KANJI: return 10;
					default:
						throw new Error("mode:" + mode);
				}

			} else if (type < 41) {

				// 27 - 40

				switch (mode) {
					case QRCode.Mode.MODE_NUMBER: return 14;
					case QRCode.Mode.MODE_ALPHA_NUM: return 13;
					case QRCode.Mode.MODE_8BIT_BYTE: return 16;
					case QRCode.Mode.MODE_KANJI: return 12;
					default:
						throw new Error("mode:" + mode);
				}

			} else {
				throw new Error("type:" + type);
			}
		},

		getLostPoint: function (qrCode) {

			var moduleCount = qrCode.getModuleCount();

			var lostPoint = 0;

			// LEVEL1

			for (var row = 0; row < moduleCount; row++) {

				for (var col = 0; col < moduleCount; col++) {

					var sameCount = 0;
					var dark = qrCode.isDark(row, col);

					for (var r = -1; r <= 1; r++) {

						if (row + r < 0 || moduleCount <= row + r) {
							continue;
						}

						for (var c = -1; c <= 1; c++) {

							if (col + c < 0 || moduleCount <= col + c) {
								continue;
							}

							if (r == 0 && c == 0) {
								continue;
							}

							if (dark == qrCode.isDark(row + r, col + c)) {
								sameCount++;
							}
						}
					}

					if (sameCount > 5) {
						lostPoint += (3 + sameCount - 5);
					}
				}
			}

			// LEVEL2

			for (var row = 0; row < moduleCount - 1; row++) {
				for (var col = 0; col < moduleCount - 1; col++) {
					var count = 0;
					if (qrCode.isDark(row, col)) count++;
					if (qrCode.isDark(row + 1, col)) count++;
					if (qrCode.isDark(row, col + 1)) count++;
					if (qrCode.isDark(row + 1, col + 1)) count++;
					if (count == 0 || count == 4) {
						lostPoint += 3;
					}
				}
			}

			// LEVEL3

			for (var row = 0; row < moduleCount; row++) {
				for (var col = 0; col < moduleCount - 6; col++) {
					if (qrCode.isDark(row, col)
						&& !qrCode.isDark(row, col + 1)
						&& qrCode.isDark(row, col + 2)
						&& qrCode.isDark(row, col + 3)
						&& qrCode.isDark(row, col + 4)
						&& !qrCode.isDark(row, col + 5)
						&& qrCode.isDark(row, col + 6)) {
						lostPoint += 40;
					}
				}
			}

			for (var col = 0; col < moduleCount; col++) {
				for (var row = 0; row < moduleCount - 6; row++) {
					if (qrCode.isDark(row, col)
						&& !qrCode.isDark(row + 1, col)
						&& qrCode.isDark(row + 2, col)
						&& qrCode.isDark(row + 3, col)
						&& qrCode.isDark(row + 4, col)
						&& !qrCode.isDark(row + 5, col)
						&& qrCode.isDark(row + 6, col)) {
						lostPoint += 40;
					}
				}
			}

			// LEVEL4

			var darkCount = 0;

			for (var col = 0; col < moduleCount; col++) {
				for (var row = 0; row < moduleCount; row++) {
					if (qrCode.isDark(row, col)) {
						darkCount++;
					}
				}
			}

			var ratio = Math.abs(100 * darkCount / moduleCount / moduleCount - 50) / 5;
			lostPoint += ratio * 10;

			return lostPoint;
		}

	};


	//---------------------------------------------------------------------
	// QRMath
	//---------------------------------------------------------------------

	QRCode.Math = {

		glog: function (n) {

			if (n < 1) {
				throw new Error("glog(" + n + ")");
			}

			return QRCode.Math.LOG_TABLE[n];
		},

		gexp: function (n) {

			while (n < 0) {
				n += 255;
			}

			while (n >= 256) {
				n -= 255;
			}

			return QRCode.Math.EXP_TABLE[n];
		},

		EXP_TABLE: new Array(256),

		LOG_TABLE: new Array(256)

	};

	for (var i = 0; i < 8; i++) {
		QRCode.Math.EXP_TABLE[i] = 1 << i;
	}
	for (var i = 8; i < 256; i++) {
		QRCode.Math.EXP_TABLE[i] = QRCode.Math.EXP_TABLE[i - 4]
		^ QRCode.Math.EXP_TABLE[i - 5]
		^ QRCode.Math.EXP_TABLE[i - 6]
		^ QRCode.Math.EXP_TABLE[i - 8];
	}
	for (var i = 0; i < 255; i++) {
		QRCode.Math.LOG_TABLE[QRCode.Math.EXP_TABLE[i]] = i;
	}

	//---------------------------------------------------------------------
	// QRPolynomial
	//---------------------------------------------------------------------

	QRCode.Polynomial = function (num, shift) {

		if (num.length == undefined) {
			throw new Error(num.length + "/" + shift);
		}

		var offset = 0;

		while (offset < num.length && num[offset] == 0) {
			offset++;
		}

		this.num = new Array(num.length - offset + shift);
		for (var i = 0; i < num.length - offset; i++) {
			this.num[i] = num[i + offset];
		}
	}

	QRCode.Polynomial.prototype = {

		get: function (index) {
			return this.num[index];
		},

		getLength: function () {
			return this.num.length;
		},

		multiply: function (e) {

			var num = new Array(this.getLength() + e.getLength() - 1);

			for (var i = 0; i < this.getLength(); i++) {
				for (var j = 0; j < e.getLength(); j++) {
					num[i + j] ^= QRCode.Math.gexp(QRCode.Math.glog(this.get(i)) + QRCode.Math.glog(e.get(j)));
				}
			}

			return new QRCode.Polynomial(num, 0);
		},

		mod: function (e) {

			if (this.getLength() - e.getLength() < 0) {
				return this;
			}

			var ratio = QRCode.Math.glog(this.get(0)) - QRCode.Math.glog(e.get(0));

			var num = new Array(this.getLength());

			for (var i = 0; i < this.getLength(); i++) {
				num[i] = this.get(i);
			}

			for (var i = 0; i < e.getLength(); i++) {
				num[i] ^= QRCode.Math.gexp(QRCode.Math.glog(e.get(i)) + ratio);
			}

			// recursive call
			return new QRCode.Polynomial(num, 0).mod(e);
		}
	};

	//---------------------------------------------------------------------
	// QRRSBlock
	//---------------------------------------------------------------------

	QRCode.RSBlock = function (totalCount, dataCount) {
		this.totalCount = totalCount;
		this.dataCount = dataCount;
	}

	QRCode.RSBlock.RS_BLOCK_TABLE = [

	// L
	// M
	// Q
	// H

	// 1
	[1, 26, 19],
	[1, 26, 16],
	[1, 26, 13],
	[1, 26, 9],

	// 2
	[1, 44, 34],
	[1, 44, 28],
	[1, 44, 22],
	[1, 44, 16],

	// 3
	[1, 70, 55],
	[1, 70, 44],
	[2, 35, 17],
	[2, 35, 13],

	// 4
	[1, 100, 80],
	[2, 50, 32],
	[2, 50, 24],
	[4, 25, 9],

	// 5
	[1, 134, 108],
	[2, 67, 43],
	[2, 33, 15, 2, 34, 16],
	[2, 33, 11, 2, 34, 12],

	// 6
	[2, 86, 68],
	[4, 43, 27],
	[4, 43, 19],
	[4, 43, 15],

	// 7
	[2, 98, 78],
	[4, 49, 31],
	[2, 32, 14, 4, 33, 15],
	[4, 39, 13, 1, 40, 14],

	// 8
	[2, 121, 97],
	[2, 60, 38, 2, 61, 39],
	[4, 40, 18, 2, 41, 19],
	[4, 40, 14, 2, 41, 15],

	// 9
	[2, 146, 116],
	[3, 58, 36, 2, 59, 37],
	[4, 36, 16, 4, 37, 17],
	[4, 36, 12, 4, 37, 13],

	// 10
	[2, 86, 68, 2, 87, 69],
	[4, 69, 43, 1, 70, 44],
	[6, 43, 19, 2, 44, 20],
	[6, 43, 15, 2, 44, 16]

];

	QRCode.RSBlock.getRSBlocks = function (typeNumber, errorCorrectLevel) {

		var rsBlock = QRCode.RSBlock.getRsBlockTable(typeNumber, errorCorrectLevel);

		if (rsBlock == undefined) {
			throw new Error("bad rs block @ typeNumber:" + typeNumber + "/errorCorrectLevel:" + errorCorrectLevel);
		}

		var length = rsBlock.length / 3;

		var list = new Array();

		for (var i = 0; i < length; i++) {

			var count = rsBlock[i * 3 + 0];
			var totalCount = rsBlock[i * 3 + 1];
			var dataCount = rsBlock[i * 3 + 2];

			for (var j = 0; j < count; j++) {
				list.push(new QRCode.RSBlock(totalCount, dataCount));
			}
		}

		return list;
	};

	QRCode.RSBlock.getRsBlockTable = function (typeNumber, errorCorrectLevel) {

		switch (errorCorrectLevel) {
			case QRCode.ErrorCorrectLevel.L:
				return QRCode.RSBlock.RS_BLOCK_TABLE[(typeNumber - 1) * 4 + 0];
			case QRCode.ErrorCorrectLevel.M:
				return QRCode.RSBlock.RS_BLOCK_TABLE[(typeNumber - 1) * 4 + 1];
			case QRCode.ErrorCorrectLevel.Q:
				return QRCode.RSBlock.RS_BLOCK_TABLE[(typeNumber - 1) * 4 + 2];
			case QRCode.ErrorCorrectLevel.H:
				return QRCode.RSBlock.RS_BLOCK_TABLE[(typeNumber - 1) * 4 + 3];
			default:
				return undefined;
		}
	};

	//---------------------------------------------------------------------
	// QRBitBuffer
	//---------------------------------------------------------------------

	QRCode.BitBuffer = function () {
		this.buffer = new Array();
		this.length = 0;
	}

	QRCode.BitBuffer.prototype = {

		get: function (index) {
			var bufIndex = Math.floor(index / 8);
			return ((this.buffer[bufIndex] >>> (7 - index % 8)) & 1) == 1;
		},

		put: function (num, length) {
			for (var i = 0; i < length; i++) {
				this.putBit(((num >>> (length - i - 1)) & 1) == 1);
			}
		},

		getLengthInBits: function () {
			return this.length;
		},

		putBit: function (bit) {

			var bufIndex = Math.floor(this.length / 8);
			if (this.buffer.length <= bufIndex) {
				this.buffer.push(0);
			}

			if (bit) {
				this.buffer[bufIndex] |= (0x80 >>> (this.length % 8));
			}

			this.length++;
		}
	};
})();
  </script>
  <script type="text/javascript">
/*
Copyright (c) 2011 Stefan Thomas

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
*/

//https://raw.github.com/bitcoinjs/bitcoinjs-lib/1a7fc9d063f864058809d06ef4542af40be3558f/src/bitcoin.js
(function (exports) {
	var Bitcoin = exports;
})(
	'object' === typeof module ? module.exports : (window.Bitcoin = {})
);
  </script>
  <script type="text/javascript">
//https://raw.github.com/bitcoinjs/bitcoinjs-lib/c952aaeb3ee472e3776655b8ea07299ebed702c7/src/base58.js
(function (Bitcoin) {
	Bitcoin.Base58 = {
		alphabet: "123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz",
		validRegex: /^[1-9A-HJ-NP-Za-km-z]+$/,
		base: BigInteger.valueOf(58),

		/**
		* Convert a byte array to a base58-encoded string.
		*
		* Written by Mike Hearn for BitcoinJ.
		*   Copyright (c) 2011 Google Inc.
		*
		* Ported to JavaScript by Stefan Thomas.
		*/
		encode: function (input) {
			var bi = BigInteger.fromByteArrayUnsigned(input);
			var chars = [];

			while (bi.compareTo(B58.base) >= 0) {
				var mod = bi.mod(B58.base);
				chars.unshift(B58.alphabet[mod.intValue()]);
				bi = bi.subtract(mod).divide(B58.base);
			}
			chars.unshift(B58.alphabet[bi.intValue()]);

			// Convert leading zeros too.
			for (var i = 0; i < input.length; i++) {
				if (input[i] == 0x00) {
					chars.unshift(B58.alphabet[0]);
				} else break;
			}

			return chars.join('');
		},

		/**
		* Convert a base58-encoded string to a byte array.
		*
		* Written by Mike Hearn for BitcoinJ.
		*   Copyright (c) 2011 Google Inc.
		*
		* Ported to JavaScript by Stefan Thomas.
		*/
		decode: function (input) {
			var bi = BigInteger.valueOf(0);
			var leadingZerosNum = 0;
			for (var i = input.length - 1; i >= 0; i--) {
				var alphaIndex = B58.alphabet.indexOf(input[i]);
				if (alphaIndex < 0) {
					throw "Invalid character";
				}
				bi = bi.add(BigInteger.valueOf(alphaIndex)
								.multiply(B58.base.pow(input.length - 1 - i)));

				// This counts leading zero bytes
				if (input[i] == "1") leadingZerosNum++;
				else leadingZerosNum = 0;
			}
			var bytes = bi.toByteArrayUnsigned();

			// Add leading zeros
			while (leadingZerosNum-- > 0) bytes.unshift(0);

			return bytes;
		}
	};

	var B58 = Bitcoin.Base58;
})(
	'undefined' != typeof Bitcoin ? Bitcoin : module.exports
);
  </script>
  <script type="text/javascript">
//https://raw.github.com/bitcoinjs/bitcoinjs-lib/09e8c6e184d6501a0c2c59d73ca64db5c0d3eb95/src/address.js
Bitcoin.Address = function (bytes) {
	if ("string" == typeof bytes) {
		bytes = Bitcoin.Address.decodeString(bytes);
	}
	this.hash = bytes;
};

/**
* Serialize this object as a standard currency address.
*
* Returns the address as a base58-encoded string in the standardized format.
*/
Bitcoin.Address.prototype.toString = function () {
	// Get a copy of the hash
	var hash = this.hash.slice(0);

	// Version
	hash.unshift(janin.currency.networkVersion());
	var checksum = Crypto.SHA256(Crypto.SHA256(hash, { asBytes: true }), { asBytes: true });
	var bytes = hash.concat(checksum.slice(0, 4));
	return Bitcoin.Base58.encode(bytes);
};

Bitcoin.Address.prototype.getHashBase64 = function () {
	return Crypto.util.bytesToBase64(this.hash);
};

/**
* Parse a Bitcoin address contained in a string.
*/
Bitcoin.Address.decodeString = function (string) {
	var bytes = Bitcoin.Base58.decode(string);
	var hash = bytes.slice(0, 21);
	var checksum = Crypto.SHA256(Crypto.SHA256(hash, { asBytes: true }), { asBytes: true });

	if (checksum[0] != bytes[21] ||
			checksum[1] != bytes[22] ||
			checksum[2] != bytes[23] ||
			checksum[3] != bytes[24]) {
		throw "Checksum validation failed!";
	}

	return hash;
};
  </script>
  <script type="text/javascript">
//https://raw.github.com/bitcoinjs/bitcoinjs-lib/e90780d3d3b8fc0d027d2bcb38b80479902f223e/src/ecdsa.js
Bitcoin.ECDSA = (function () {
	var ecparams = EllipticCurve.getSECCurveByName("secp256k1");
	var rng = new SecureRandom();

	var P_OVER_FOUR = null;

	function implShamirsTrick(P, k, Q, l) {
		var m = Math.max(k.bitLength(), l.bitLength());
		var Z = P.add2D(Q);
		var R = P.curve.getInfinity();

		for (var i = m - 1; i >= 0; --i) {
			R = R.twice2D();

			R.z = BigInteger.ONE;

			if (k.testBit(i)) {
				if (l.testBit(i)) {
					R = R.add2D(Z);
				} else {
					R = R.add2D(P);
				}
			} else {
				if (l.testBit(i)) {
					R = R.add2D(Q);
				}
			}
		}

		return R;
	};

	var ECDSA = {
		getBigRandom: function (limit) {
			return new BigInteger(limit.bitLength(), rng)
				.mod(limit.subtract(BigInteger.ONE))
				.add(BigInteger.ONE);
		},
		sign: function (hash, priv) {
			var d = priv;
			var n = ecparams.getN();
			var e = BigInteger.fromByteArrayUnsigned(hash);

			do {
				var k = ECDSA.getBigRandom(n);
				var G = ecparams.getG();
				var Q = G.multiply(k);
				var r = Q.getX().toBigInteger().mod(n);
			} while (r.compareTo(BigInteger.ZERO) <= 0);

			var s = k.modInverse(n).multiply(e.add(d.multiply(r))).mod(n);

			return ECDSA.serializeSig(r, s);
		},

		verify: function (hash, sig, pubkey) {
			var r, s;
			if (Bitcoin.Util.isArray(sig)) {
				var obj = ECDSA.parseSig(sig);
				r = obj.r;
				s = obj.s;
			} else if ("object" === typeof sig && sig.r && sig.s) {
				r = sig.r;
				s = sig.s;
			} else {
				throw "Invalid value for signature";
			}

			var Q;
			if (pubkey instanceof ec.PointFp) {
				Q = pubkey;
			} else if (Bitcoin.Util.isArray(pubkey)) {
				Q = EllipticCurve.PointFp.decodeFrom(ecparams.getCurve(), pubkey);
			} else {
				throw "Invalid format for pubkey value, must be byte array or ec.PointFp";
			}
			var e = BigInteger.fromByteArrayUnsigned(hash);

			return ECDSA.verifyRaw(e, r, s, Q);
		},

		verifyRaw: function (e, r, s, Q) {
			var n = ecparams.getN();
			var G = ecparams.getG();

			if (r.compareTo(BigInteger.ONE) < 0 ||
          r.compareTo(n) >= 0)
				return false;

			if (s.compareTo(BigInteger.ONE) < 0 ||
          s.compareTo(n) >= 0)
				return false;

			var c = s.modInverse(n);

			var u1 = e.multiply(c).mod(n);
			var u2 = r.multiply(c).mod(n);

			// TODO(!!!): For some reason Shamir's trick isn't working with
			// signed message verification!? Probably an implementation
			// error!
			//var point = implShamirsTrick(G, u1, Q, u2);
			var point = G.multiply(u1).add(Q.multiply(u2));

			var v = point.getX().toBigInteger().mod(n);

			return v.equals(r);
		},

		/**
		* Serialize a signature into DER format.
		*
		* Takes two BigIntegers representing r and s and returns a byte array.
		*/
		serializeSig: function (r, s) {
			var rBa = r.toByteArraySigned();
			var sBa = s.toByteArraySigned();

			var sequence = [];
			sequence.push(0x02); // INTEGER
			sequence.push(rBa.length);
			sequence = sequence.concat(rBa);

			sequence.push(0x02); // INTEGER
			sequence.push(sBa.length);
			sequence = sequence.concat(sBa);

			sequence.unshift(sequence.length);
			sequence.unshift(0x30); // SEQUENCE

			return sequence;
		},

		/**
		* Parses a byte array containing a DER-encoded signature.
		*
		* This function will return an object of the form:
		*
		* {
		*   r: BigInteger,
		*   s: BigInteger
		* }
		*/
		parseSig: function (sig) {
			var cursor;
			if (sig[0] != 0x30)
				throw new Error("Signature not a valid DERSequence");

			cursor = 2;
			if (sig[cursor] != 0x02)
				throw new Error("First element in signature must be a DERInteger"); ;
			var rBa = sig.slice(cursor + 2, cursor + 2 + sig[cursor + 1]);

			cursor += 2 + sig[cursor + 1];
			if (sig[cursor] != 0x02)
				throw new Error("Second element in signature must be a DERInteger");
			var sBa = sig.slice(cursor + 2, cursor + 2 + sig[cursor + 1]);

			cursor += 2 + sig[cursor + 1];

			//if (cursor != sig.length)
			//	throw new Error("Extra bytes in signature");

			var r = BigInteger.fromByteArrayUnsigned(rBa);
			var s = BigInteger.fromByteArrayUnsigned(sBa);

			return { r: r, s: s };
		},

		parseSigCompact: function (sig) {
			if (sig.length !== 65) {
				throw "Signature has the wrong length";
			}

			// Signature is prefixed with a type byte storing three bits of
			// information.
			var i = sig[0] - 27;
			if (i < 0 || i > 7) {
				throw "Invalid signature type";
			}

			var n = ecparams.getN();
			var r = BigInteger.fromByteArrayUnsigned(sig.slice(1, 33)).mod(n);
			var s = BigInteger.fromByteArrayUnsigned(sig.slice(33, 65)).mod(n);

			return { r: r, s: s, i: i };
		},

		/**
		* Recover a public key from a signature.
		*
		* See SEC 1: Elliptic Curve Cryptography, section 4.1.6, "Public
		* Key Recovery Operation".
		*
		* http://www.secg.org/download/aid-780/sec1-v2.pdf
		*/
		recoverPubKey: function (r, s, hash, i) {
			// The recovery parameter i has two bits.
			i = i & 3;

			// The less significant bit specifies whether the y coordinate
			// of the compressed point is even or not.
			var isYEven = i & 1;

			// The more significant bit specifies whether we should use the
			// first or second candidate key.
			var isSecondKey = i >> 1;

			var n = ecparams.getN();
			var G = ecparams.getG();
			var curve = ecparams.getCurve();
			var p = curve.getQ();
			var a = curve.getA().toBigInteger();
			var b = curve.getB().toBigInteger();

			// We precalculate (p + 1) / 4 where p is if the field order
			if (!P_OVER_FOUR) {
				P_OVER_FOUR = p.add(BigInteger.ONE).divide(BigInteger.valueOf(4));
			}

			// 1.1 Compute x
			var x = isSecondKey ? r.add(n) : r;

			// 1.3 Convert x to point
			var alpha = x.multiply(x).multiply(x).add(a.multiply(x)).add(b).mod(p);
			var beta = alpha.modPow(P_OVER_FOUR, p);

			var xorOdd = beta.isEven() ? (i % 2) : ((i + 1) % 2);
			// If beta is even, but y isn't or vice versa, then convert it,
			// otherwise we're done and y == beta.
			var y = (beta.isEven() ? !isYEven : isYEven) ? beta : p.subtract(beta);

			// 1.4 Check that nR is at infinity
			var R = new EllipticCurve.PointFp(curve,
                            curve.fromBigInteger(x),
                            curve.fromBigInteger(y));
			R.validate();

			// 1.5 Compute e from M
			var e = BigInteger.fromByteArrayUnsigned(hash);
			var eNeg = BigInteger.ZERO.subtract(e).mod(n);

			// 1.6 Compute Q = r^-1 (sR - eG)
			var rInv = r.modInverse(n);
			var Q = implShamirsTrick(R, s, G, eNeg).multiply(rInv);

			Q.validate();
			if (!ECDSA.verifyRaw(e, r, s, Q)) {
				throw "Pubkey recovery unsuccessful";
			}

			var pubKey = new Bitcoin.ECKey();
			pubKey.pub = Q;
			return pubKey;
		},

		/**
		* Calculate pubkey extraction parameter.
		*
		* When extracting a pubkey from a signature, we have to
		* distinguish four different cases. Rather than putting this
		* burden on the verifier, Bitcoin includes a 2-bit value with the
		* signature.
		*
		* This function simply tries all four cases and returns the value
		* that resulted in a successful pubkey recovery.
		*/
		calcPubkeyRecoveryParam: function (address, r, s, hash) {
			for (var i = 0; i < 4; i++) {
				try {
					var pubkey = Bitcoin.ECDSA.recoverPubKey(r, s, hash, i);
					if (pubkey.getBitcoinAddress().toString() == address) {
						return i;
					}
				} catch (e) { }
			}
			throw "Unable to find valid recovery factor";
		}
	};

	return ECDSA;
})();
  </script>
  <script type="text/javascript">
//https://raw.github.com/pointbiz/bitcoinjs-lib/9b2f94a028a7bc9bed94e0722563e9ff1d8e8db8/src/eckey.js
Bitcoin.ECKey = (function () {
	var ECDSA = Bitcoin.ECDSA;
	var ecparams = EllipticCurve.getSECCurveByName("secp256k1");
	var rng = new SecureRandom();

	var ECKey = function (input) {
		if (!input) {
			// Generate new key
			var n = ecparams.getN();
			this.priv = ECDSA.getBigRandom(n);
		} else if (input instanceof BigInteger) {
			// Input is a private key value
			this.priv = input;
		} else if (Bitcoin.Util.isArray(input)) {
			// Prepend zero byte to prevent interpretation as negative integer
			this.priv = BigInteger.fromByteArrayUnsigned(input);
		} else if ("string" == typeof input) {
			var bytes = null;
			if (ECKey.isWalletImportFormat(input)) {
				bytes = ECKey.decodeWalletImportFormat(input);
			} else if (ECKey.isCompressedWalletImportFormat(input)) {
				bytes = ECKey.decodeCompressedWalletImportFormat(input);
				this.compressed = true;
			} else if (ECKey.isMiniFormat(input)) {
				bytes = Crypto.SHA256(input, { asBytes: true });
			} else if (ECKey.isHexFormat(input)) {
				bytes = Crypto.util.hexToBytes(input);
			} else if (ECKey.isBase64Format(input)) {
				bytes = Crypto.util.base64ToBytes(input);
			}

			if (ECKey.isBase6Format(input)) {
				this.priv = new BigInteger(input, 6);
			} else if (bytes == null || bytes.length != 32) {
				this.priv = null;
			} else {
				// Prepend zero byte to prevent interpretation as negative integer
				this.priv = BigInteger.fromByteArrayUnsigned(bytes);
			}
		}

		this.compressed = (this.compressed == undefined) ? !!ECKey.compressByDefault : this.compressed;
	};

	/**
	* Whether public keys should be returned compressed by default.
	*/
	ECKey.compressByDefault = false;

	/**
	* Set whether the public key should be returned compressed or not.
	*/
	ECKey.prototype.setCompressed = function (v) {
		this.compressed = !!v;
		if (this.pubPoint) this.pubPoint.compressed = this.compressed;
		return this;
	};

	/*
	* Return public key as a byte array in DER encoding
	*/
	ECKey.prototype.getPub = function () {
		if (this.compressed) {
			if (this.pubComp) return this.pubComp;
			return this.pubComp = this.getPubPoint().getEncoded(1);
		} else {
			if (this.pubUncomp) return this.pubUncomp;
			return this.pubUncomp = this.getPubPoint().getEncoded(0);
		}
	};

	/**
	* Return public point as ECPoint object.
	*/
	ECKey.prototype.getPubPoint = function () {
		if (!this.pubPoint) {
			this.pubPoint = ecparams.getG().multiply(this.priv);
			this.pubPoint.compressed = this.compressed;
		}
		return this.pubPoint;
	};

	ECKey.prototype.getPubKeyHex = function () {
		if (this.compressed) {
			if (this.pubKeyHexComp) return this.pubKeyHexComp;
			return this.pubKeyHexComp = Crypto.util.bytesToHex(this.getPub()).toString().toUpperCase();
		} else {
			if (this.pubKeyHexUncomp) return this.pubKeyHexUncomp;
			return this.pubKeyHexUncomp = Crypto.util.bytesToHex(this.getPub()).toString().toUpperCase();
		}
	};

	/**
	* Get the pubKeyHash for this key.
	*
	* This is calculated as RIPE160(SHA256([encoded pubkey])) and returned as
	* a byte array.
	*/
	ECKey.prototype.getPubKeyHash = function () {
		if (this.compressed) {
			if (this.pubKeyHashComp) return this.pubKeyHashComp;
			return this.pubKeyHashComp = Bitcoin.Util.sha256ripe160(this.getPub());
		} else {
			if (this.pubKeyHashUncomp) return this.pubKeyHashUncomp;
			return this.pubKeyHashUncomp = Bitcoin.Util.sha256ripe160(this.getPub());
		}
	};

	ECKey.prototype.getBitcoinAddress = function () {
		var hash = this.getPubKeyHash();
		var addr = new Bitcoin.Address(hash);
		return addr.toString();
	};

	/*
	* Takes a public point as a hex string or byte array
	*/
	ECKey.prototype.setPub = function (pub) {
		// byte array
		if (Bitcoin.Util.isArray(pub)) {
			pub = Crypto.util.bytesToHex(pub).toString().toUpperCase();
		}
		var ecPoint = ecparams.getCurve().decodePointHex(pub);
		this.setCompressed(ecPoint.compressed);
		this.pubPoint = ecPoint;
		return this;
	};

	// Sipa Private Key Wallet Import Format
	ECKey.prototype.getBitcoinWalletImportFormat = function () {
		var bytes = this.getBitcoinPrivateKeyByteArray();
		bytes.unshift(janin.currency.privateKeyPrefix()); // prepend private key prefix
		if (this.compressed) bytes.push(0x01); // append 0x01 byte for compressed format
		var checksum = Crypto.SHA256(Crypto.SHA256(bytes, { asBytes: true }), { asBytes: true });
		bytes = bytes.concat(checksum.slice(0, 4));
		var privWif = Bitcoin.Base58.encode(bytes);
		return privWif;
	};

	// Private Key Hex Format
	ECKey.prototype.getBitcoinHexFormat = function () {
		return Crypto.util.bytesToHex(this.getBitcoinPrivateKeyByteArray()).toString().toUpperCase();
	};

	// Private Key Base64 Format
	ECKey.prototype.getBitcoinBase64Format = function () {
		return Crypto.util.bytesToBase64(this.getBitcoinPrivateKeyByteArray());
	};

	ECKey.prototype.getBitcoinPrivateKeyByteArray = function () {
		// Get a copy of private key as a byte array
		var bytes = this.priv.toByteArrayUnsigned();
		// zero pad if private key is less than 32 bytes
		while (bytes.length < 32) bytes.unshift(0x00);
		return bytes;
	};

	ECKey.prototype.toString = function (format) {
		format = format || "";
		if (format.toString().toLowerCase() == "base64" || format.toString().toLowerCase() == "b64") {
			return this.getBitcoinBase64Format();
		}
		// Wallet Import Format
		else if (format.toString().toLowerCase() == "wif") {
			return this.getBitcoinWalletImportFormat();
		}
		else {
			return this.getBitcoinHexFormat();
		}
	};

	ECKey.prototype.sign = function (hash) {
		return ECDSA.sign(hash, this.priv);
	};

	ECKey.prototype.verify = function (hash, sig) {
		return ECDSA.verify(hash, sig, this.getPub());
	};

	/**
	* Parse a wallet import format private key contained in a string.
	*/
	ECKey.decodeWalletImportFormat = function (privStr) {
		var bytes = Bitcoin.Base58.decode(privStr);
		var hash = bytes.slice(0, 33);
		var checksum = Crypto.SHA256(Crypto.SHA256(hash, { asBytes: true }), { asBytes: true });
		if (checksum[0] != bytes[33] ||
					checksum[1] != bytes[34] ||
					checksum[2] != bytes[35] ||
					checksum[3] != bytes[36]) {
			throw "Checksum validation failed!";
		}
		var version = hash.shift();
        // TODO: detect currency
		if (version != janin.currency.privateKeyPrefix()) {
			throw "Version " + version + " not supported!";
		}
		return hash;
	};

	/**
	* Parse a compressed wallet import format private key contained in a string.
	*/
	ECKey.decodeCompressedWalletImportFormat = function (privStr) {
		var bytes = Bitcoin.Base58.decode(privStr);
		var hash = bytes.slice(0, 34);
		var checksum = Crypto.SHA256(Crypto.SHA256(hash, { asBytes: true }), { asBytes: true });
		if (checksum[0] != bytes[34] ||
					checksum[1] != bytes[35] ||
					checksum[2] != bytes[36] ||
					checksum[3] != bytes[37]) {
			throw "Checksum validation failed!";
		}
		var version = hash.shift();
        // TODO: detect currency
		if (version != janin.currency.privateKeyPrefix()) {
			throw "Version " + version + " not supported!";
		}
		hash.pop();
		return hash;
	};

	// 64 characters [0-9A-F]
	ECKey.isHexFormat = function (key) {
		key = key.toString();
		return /^[A-Fa-f0-9]{64}$/.test(key);
	};

	// 51 characters base58, always starts with a '5'
	ECKey.isWalletImportFormat = function (key) {
		key = key.toString();
		return janin.currency.WIF_RegEx().test(key);
	};

	// 52 characters base58
	ECKey.isCompressedWalletImportFormat = function (key) {
		key = key.toString();
		return janin.currency.CWIF_RegEx().test(key);
	};

	// 44 characters
	ECKey.isBase64Format = function (key) {
		key = key.toString();
		return (/^[ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789=+\/]{44}$/.test(key));
	};

	// 99 characters, 1=1, if using dice convert 6 to 0
	ECKey.isBase6Format = function (key) {
		key = key.toString();
		return (/^[012345]{99}$/.test(key));
	};

	// 22, 26 or 30 characters, always starts with an 'S'
	ECKey.isMiniFormat = function (key) {
		key = key.toString();
		var validChars22 = /^S[123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz]{21}$/.test(key);
		var validChars26 = /^S[123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz]{25}$/.test(key);
		var validChars30 = /^S[123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz]{29}$/.test(key);
		var testBytes = Crypto.SHA256(key + "?", { asBytes: true });

		return ((testBytes[0] === 0x00 || testBytes[0] === 0x01) && (validChars22 || validChars26 || validChars30));
	};

	return ECKey;
})();
  </script>
  <script type="text/javascript">
//https://raw.github.com/bitcoinjs/bitcoinjs-lib/09e8c6e184d6501a0c2c59d73ca64db5c0d3eb95/src/util.js
// Bitcoin utility functions
Bitcoin.Util = {
	/**
	* Cross-browser compatibility version of Array.isArray.
	*/
	isArray: Array.isArray || function (o) {
		return Object.prototype.toString.call(o) === '[object Array]';
	},
	/**
	* Create an array of a certain length filled with a specific value.
	*/
	makeFilledArray: function (len, val) {
		var array = [];
		var i = 0;
		while (i < len) {
			array[i++] = val;
		}
		return array;
	},
	/**
	* Turn an integer into a "var_int".
	*
	* "var_int" is a variable length integer used by Bitcoin's binary format.
	*
	* Returns a byte array.
	*/
	numToVarInt: function (i) {
		if (i < 0xfd) {
			// unsigned char
			return [i];
		} else if (i <= 1 << 16) {
			// unsigned short (LE)
			return [0xfd, i >>> 8, i & 255];
		} else if (i <= 1 << 32) {
			// unsigned int (LE)
			return [0xfe].concat(Crypto.util.wordsToBytes([i]));
		} else {
			// unsigned long long (LE)
			return [0xff].concat(Crypto.util.wordsToBytes([i >>> 32, i]));
		}
	},
	/**
	* Parse a Bitcoin value byte array, returning a BigInteger.
	*/
	valueToBigInt: function (valueBuffer) {
		if (valueBuffer instanceof BigInteger) return valueBuffer;

		// Prepend zero byte to prevent interpretation as negative integer
		return BigInteger.fromByteArrayUnsigned(valueBuffer);
	},
	/**
	* Format a Bitcoin value as a string.
	*
	* Takes a BigInteger or byte-array and returns that amount of Bitcoins in a
	* nice standard formatting.
	*
	* Examples:
	* 12.3555
	* 0.1234
	* 900.99998888
	* 34.00
	*/
	formatValue: function (valueBuffer) {
		var value = this.valueToBigInt(valueBuffer).toString();
		var integerPart = value.length > 8 ? value.substr(0, value.length - 8) : '0';
		var decimalPart = value.length > 8 ? value.substr(value.length - 8) : value;
		while (decimalPart.length < 8) decimalPart = "0" + decimalPart;
		decimalPart = decimalPart.replace(/0*$/, '');
		while (decimalPart.length < 2) decimalPart += "0";
		return integerPart + "." + decimalPart;
	},
	/**
	* Parse a floating point string as a Bitcoin value.
	*
	* Keep in mind that parsing user input is messy. You should always display
	* the parsed value back to the user to make sure we understood his input
	* correctly.
	*/
	parseValue: function (valueString) {
		// TODO: Detect other number formats (e.g. comma as decimal separator)
		var valueComp = valueString.split('.');
		var integralPart = valueComp[0];
		var fractionalPart = valueComp[1] || "0";
		while (fractionalPart.length < 8) fractionalPart += "0";
		fractionalPart = fractionalPart.replace(/^0+/g, '');
		var value = BigInteger.valueOf(parseInt(integralPart));
		value = value.multiply(BigInteger.valueOf(100000000));
		value = value.add(BigInteger.valueOf(parseInt(fractionalPart)));
		return value;
	},
	/**
	* Calculate RIPEMD160(SHA256(data)).
	*
	* Takes an arbitrary byte array as inputs and returns the hash as a byte
	* array.
	*/
	sha256ripe160: function (data) {
		return Crypto.RIPEMD160(Crypto.SHA256(data, { asBytes: true }), { asBytes: true });
	},
	// double sha256
	dsha256: function (data) {
		return Crypto.SHA256(Crypto.SHA256(data, { asBytes: true }), { asBytes: true });
	}
};
  </script>
  <script type="text/javascript">
/*
* Copyright (c) 2010-2011 Intalio Pte, All Rights Reserved
*
* Permission is hereby granted, free of charge, to any person obtaining a copy
* of this software and associated documentation files (the "Software"), to deal
* in the Software without restriction, including without limitation the rights
* to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
* copies of the Software, and to permit persons to whom the Software is
* furnished to do so, subject to the following conditions:
*
* The above copyright notice and this permission notice shall be included in
* all copies or substantial portions of the Software.
*
* THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
* IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
* FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
* AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
* LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
* OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
* THE SOFTWARE.
*/
// https://github.com/cheongwy/node-scrypt-js
(function () {

	var MAX_VALUE = 2147483647;
	var workerUrl = null;

	//function scrypt(byte[] passwd, byte[] salt, int N, int r, int p, int dkLen)
	/*
	* N = Cpu cost
	* r = Memory cost
	* p = parallelization cost
	*
	*/
	window.Crypto_scrypt = function (passwd, salt, N, r, p, dkLen, callback) {
		if (N == 0 || (N & (N - 1)) != 0) throw Error("N must be > 0 and a power of 2");

		if (N > MAX_VALUE / 128 / r) throw Error("Parameter N is too large");
		if (r > MAX_VALUE / 128 / p) throw Error("Parameter r is too large");

		var PBKDF2_opts = { iterations: 1, hasher: Crypto.SHA256, asBytes: true };

		var B = Crypto.PBKDF2(passwd, salt, p * 128 * r, PBKDF2_opts);

		try {
			var i = 0;
			var worksDone = 0;
			var makeWorker = function () {
				if (!workerUrl) {
					var code = '(' + scryptCore.toString() + ')()';
					var blob;
					try {
						blob = new Blob([code], { type: "text/javascript" });
					} catch (e) {
						window.BlobBuilder = window.BlobBuilder || window.WebKitBlobBuilder || window.MozBlobBuilder || window.MSBlobBuilder;
						blob = new BlobBuilder();
						blob.append(code);
						blob = blob.getBlob("text/javascript");
					}
					workerUrl = URL.createObjectURL(blob);
				}
				var worker = new Worker(workerUrl);
				worker.onmessage = function (event) {
					var Bi = event.data[0], Bslice = event.data[1];
					worksDone++;

					if (i < p) {
						worker.postMessage([N, r, p, B, i++]);
					}

					var length = Bslice.length, destPos = Bi * 128 * r, srcPos = 0;
					while (length--) {
						B[destPos++] = Bslice[srcPos++];
					}

					if (worksDone == p) {
						callback(Crypto.PBKDF2(passwd, B, dkLen, PBKDF2_opts));
					}
				};
				return worker;
			};
			var workers = [makeWorker(), makeWorker()];
			workers[0].postMessage([N, r, p, B, i++]);
			if (p > 1) {
				workers[1].postMessage([N, r, p, B, i++]);
			}
		} catch (e) {
			window.setTimeout(function () {
				scryptCore();
				callback(Crypto.PBKDF2(passwd, B, dkLen, PBKDF2_opts));
			}, 0);
		}

		// using this function to enclose everything needed to create a worker (but also invokable directly for synchronous use)
		function scryptCore() {
			var XY = [], V = [];

			if (typeof B === 'undefined') {
				onmessage = function (event) {
					var data = event.data;
					var N = data[0], r = data[1], p = data[2], B = data[3], i = data[4];

					var Bslice = [];
					arraycopy32(B, i * 128 * r, Bslice, 0, 128 * r);
					smix(Bslice, 0, r, N, V, XY);

					postMessage([i, Bslice]);
				};
			} else {
				for (var i = 0; i < p; i++) {
					smix(B, i * 128 * r, r, N, V, XY);
				}
			}

			function smix(B, Bi, r, N, V, XY) {
				var Xi = 0;
				var Yi = 128 * r;
				var i;

				arraycopy32(B, Bi, XY, Xi, Yi);

				for (i = 0; i < N; i++) {
					arraycopy32(XY, Xi, V, i * Yi, Yi);
					blockmix_salsa8(XY, Xi, Yi, r);
				}

				for (i = 0; i < N; i++) {
					var j = integerify(XY, Xi, r) & (N - 1);
					blockxor(V, j * Yi, XY, Xi, Yi);
					blockmix_salsa8(XY, Xi, Yi, r);
				}

				arraycopy32(XY, Xi, B, Bi, Yi);
			}

			function blockmix_salsa8(BY, Bi, Yi, r) {
				var X = [];
				var i;

				arraycopy32(BY, Bi + (2 * r - 1) * 64, X, 0, 64);

				for (i = 0; i < 2 * r; i++) {
					blockxor(BY, i * 64, X, 0, 64);
					salsa20_8(X);
					arraycopy32(X, 0, BY, Yi + (i * 64), 64);
				}

				for (i = 0; i < r; i++) {
					arraycopy32(BY, Yi + (i * 2) * 64, BY, Bi + (i * 64), 64);
				}

				for (i = 0; i < r; i++) {
					arraycopy32(BY, Yi + (i * 2 + 1) * 64, BY, Bi + (i + r) * 64, 64);
				}
			}

			function R(a, b) {
				return (a << b) | (a >>> (32 - b));
			}

			function salsa20_8(B) {
				var B32 = new Array(32);
				var x = new Array(32);
				var i;

				for (i = 0; i < 16; i++) {
					B32[i] = (B[i * 4 + 0] & 0xff) << 0;
					B32[i] |= (B[i * 4 + 1] & 0xff) << 8;
					B32[i] |= (B[i * 4 + 2] & 0xff) << 16;
					B32[i] |= (B[i * 4 + 3] & 0xff) << 24;
				}

				arraycopy(B32, 0, x, 0, 16);

				for (i = 8; i > 0; i -= 2) {
					x[4] ^= R(x[0] + x[12], 7); x[8] ^= R(x[4] + x[0], 9);
					x[12] ^= R(x[8] + x[4], 13); x[0] ^= R(x[12] + x[8], 18);
					x[9] ^= R(x[5] + x[1], 7); x[13] ^= R(x[9] + x[5], 9);
					x[1] ^= R(x[13] + x[9], 13); x[5] ^= R(x[1] + x[13], 18);
					x[14] ^= R(x[10] + x[6], 7); x[2] ^= R(x[14] + x[10], 9);
					x[6] ^= R(x[2] + x[14], 13); x[10] ^= R(x[6] + x[2], 18);
					x[3] ^= R(x[15] + x[11], 7); x[7] ^= R(x[3] + x[15], 9);
					x[11] ^= R(x[7] + x[3], 13); x[15] ^= R(x[11] + x[7], 18);
					x[1] ^= R(x[0] + x[3], 7); x[2] ^= R(x[1] + x[0], 9);
					x[3] ^= R(x[2] + x[1], 13); x[0] ^= R(x[3] + x[2], 18);
					x[6] ^= R(x[5] + x[4], 7); x[7] ^= R(x[6] + x[5], 9);
					x[4] ^= R(x[7] + x[6], 13); x[5] ^= R(x[4] + x[7], 18);
					x[11] ^= R(x[10] + x[9], 7); x[8] ^= R(x[11] + x[10], 9);
					x[9] ^= R(x[8] + x[11], 13); x[10] ^= R(x[9] + x[8], 18);
					x[12] ^= R(x[15] + x[14], 7); x[13] ^= R(x[12] + x[15], 9);
					x[14] ^= R(x[13] + x[12], 13); x[15] ^= R(x[14] + x[13], 18);
				}

				for (i = 0; i < 16; ++i) B32[i] = x[i] + B32[i];

				for (i = 0; i < 16; i++) {
					var bi = i * 4;
					B[bi + 0] = (B32[i] >> 0 & 0xff);
					B[bi + 1] = (B32[i] >> 8 & 0xff);
					B[bi + 2] = (B32[i] >> 16 & 0xff);
					B[bi + 3] = (B32[i] >> 24 & 0xff);
				}
			}

			function blockxor(S, Si, D, Di, len) {
				var i = len >> 6;
				while (i--) {
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];

					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];

					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];

					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];

					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];

					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];

					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];

					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
					D[Di++] ^= S[Si++]; D[Di++] ^= S[Si++];
				}
			}

			function integerify(B, bi, r) {
				var n;

				bi += (2 * r - 1) * 64;

				n = (B[bi + 0] & 0xff) << 0;
				n |= (B[bi + 1] & 0xff) << 8;
				n |= (B[bi + 2] & 0xff) << 16;
				n |= (B[bi + 3] & 0xff) << 24;

				return n;
			}

			function arraycopy(src, srcPos, dest, destPos, length) {
				while (length--) {
					dest[destPos++] = src[srcPos++];
				}
			}

			function arraycopy32(src, srcPos, dest, destPos, length) {
				var i = length >> 5;
				while (i--) {
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];

					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];

					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];

					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
					dest[destPos++] = src[srcPos++]; dest[destPos++] = src[srcPos++];
				}
			}
		} // scryptCore
	}; // window.Crypto_scrypt
})();
  </script>
  <script type="text/javascript">
/*
  Ported to JavaScript by Lazar Laszlo 2011

  lazarsoft@gmail.com, www.lazarsoft.info

*/

/*
*
* Copyright 2007 ZXing authors
*
* Licensed under the Apache License, Version 2.0 (the "License");
* you may not use this file except in compliance with the License.
* You may obtain a copy of the License at
*
*      http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

GridSampler = {};

GridSampler.checkAndNudgePoints=function( image,  points)
		{
			var width = qrcode.width;
			var height = qrcode.height;
			// Check and nudge points from start until we see some that are OK:
			var nudged = true;
			for (var offset = 0; offset < points.length && nudged; offset += 2)
			{
				var x = Math.floor (points[offset]);
				var y = Math.floor( points[offset + 1]);
				if (x < - 1 || x > width || y < - 1 || y > height)
				{
					throw "Error.checkAndNudgePoints ";
				}
				nudged = false;
				if (x == - 1)
				{
					points[offset] = 0.0;
					nudged = true;
				}
				else if (x == width)
				{
					points[offset] = width - 1;
					nudged = true;
				}
				if (y == - 1)
				{
					points[offset + 1] = 0.0;
					nudged = true;
				}
				else if (y == height)
				{
					points[offset + 1] = height - 1;
					nudged = true;
				}
			}
			// Check and nudge points from end:
			nudged = true;
			for (var offset = points.length - 2; offset >= 0 && nudged; offset -= 2)
			{
				var x = Math.floor( points[offset]);
				var y = Math.floor( points[offset + 1]);
				if (x < - 1 || x > width || y < - 1 || y > height)
				{
					throw "Error.checkAndNudgePoints ";
				}
				nudged = false;
				if (x == - 1)
				{
					points[offset] = 0.0;
					nudged = true;
				}
				else if (x == width)
				{
					points[offset] = width - 1;
					nudged = true;
				}
				if (y == - 1)
				{
					points[offset + 1] = 0.0;
					nudged = true;
				}
				else if (y == height)
				{
					points[offset + 1] = height - 1;
					nudged = true;
				}
			}
		}



GridSampler.sampleGrid3=function( image,  dimension,  transform)
		{
			var bits = new BitMatrix(dimension);
			var points = new Array(dimension << 1);
			for (var y = 0; y < dimension; y++)
			{
				var max = points.length;
				var iValue =  y + 0.5;
				for (var x = 0; x < max; x += 2)
				{
					points[x] =  (x >> 1) + 0.5;
					points[x + 1] = iValue;
				}
				transform.transformPoints1(points);
				// Quick check to see if points transformed to something inside the image;
				// sufficient to check the endpoints
				GridSampler.checkAndNudgePoints(image, points);
				try
				{
					for (var x = 0; x < max; x += 2)
					{
						var xpoint = (Math.floor( points[x]) * 4) + (Math.floor( points[x + 1]) * qrcode.width * 4);
                        var bit = image[Math.floor( points[x])+ qrcode.width* Math.floor( points[x + 1])];
						qrcode.imagedata.data[xpoint] = bit?255:0;
						qrcode.imagedata.data[xpoint+1] = bit?255:0;
						qrcode.imagedata.data[xpoint+2] = 0;
						qrcode.imagedata.data[xpoint+3] = 255;
						//bits[x >> 1][ y]=bit;
						if(bit)
							bits.set_Renamed(x >> 1, y);
					}
				}
				catch ( aioobe)
				{
					// This feels wrong, but, sometimes if the finder patterns are misidentified, the resulting
					// transform gets "twisted" such that it maps a straight line of points to a set of points
					// whose endpoints are in bounds, but others are not. There is probably some mathematical
					// way to detect this about the transformation that I don't know yet.
					// This results in an ugly runtime exception despite our clever checks above -- can't have
					// that. We could check each point's coordinates but that feels duplicative. We settle for
					// catching and wrapping ArrayIndexOutOfBoundsException.
					throw "Error.checkAndNudgePoints";
				}
			}
			return bits;
		}

GridSampler.sampleGridx=function( image,  dimension,  p1ToX,  p1ToY,  p2ToX,  p2ToY,  p3ToX,  p3ToY,  p4ToX,  p4ToY,  p1FromX,  p1FromY,  p2FromX,  p2FromY,  p3FromX,  p3FromY,  p4FromX,  p4FromY)
{
	var transform = PerspectiveTransform.quadrilateralToQuadrilateral(p1ToX, p1ToY, p2ToX, p2ToY, p3ToX, p3ToY, p4ToX, p4ToY, p1FromX, p1FromY, p2FromX, p2FromY, p3FromX, p3FromY, p4FromX, p4FromY);

	return GridSampler.sampleGrid3(image, dimension, transform);
}

function ECB(count,  dataCodewords)
{
	this.count = count;
	this.dataCodewords = dataCodewords;

	this.__defineGetter__("Count", function()
	{
		return this.count;
	});
	this.__defineGetter__("DataCodewords", function()
	{
		return this.dataCodewords;
	});
}

function ECBlocks( ecCodewordsPerBlock,  ecBlocks1,  ecBlocks2)
{
	this.ecCodewordsPerBlock = ecCodewordsPerBlock;
	if(ecBlocks2)
		this.ecBlocks = new Array(ecBlocks1, ecBlocks2);
	else
		this.ecBlocks = new Array(ecBlocks1);

	this.__defineGetter__("ECCodewordsPerBlock", function()
	{
		return this.ecCodewordsPerBlock;
	});

	this.__defineGetter__("TotalECCodewords", function()
	{
		return  this.ecCodewordsPerBlock * this.NumBlocks;
	});

	this.__defineGetter__("NumBlocks", function()
	{
		var total = 0;
		for (var i = 0; i < this.ecBlocks.length; i++)
		{
			total += this.ecBlocks[i].length;
		}
		return total;
	});

	this.getECBlocks=function()
			{
				return this.ecBlocks;
			}
}

function Version( versionNumber,  alignmentPatternCenters,  ecBlocks1,  ecBlocks2,  ecBlocks3,  ecBlocks4)
{
	this.versionNumber = versionNumber;
	this.alignmentPatternCenters = alignmentPatternCenters;
	this.ecBlocks = new Array(ecBlocks1, ecBlocks2, ecBlocks3, ecBlocks4);

	var total = 0;
	var ecCodewords = ecBlocks1.ECCodewordsPerBlock;
	var ecbArray = ecBlocks1.getECBlocks();
	for (var i = 0; i < ecbArray.length; i++)
	{
		var ecBlock = ecbArray[i];
		total += ecBlock.Count * (ecBlock.DataCodewords + ecCodewords);
	}
	this.totalCodewords = total;

	this.__defineGetter__("VersionNumber", function()
	{
		return  this.versionNumber;
	});

	this.__defineGetter__("AlignmentPatternCenters", function()
	{
		return  this.alignmentPatternCenters;
	});
	this.__defineGetter__("TotalCodewords", function()
	{
		return  this.totalCodewords;
	});
	this.__defineGetter__("DimensionForVersion", function()
	{
		return  17 + 4 * this.versionNumber;
	});

	this.buildFunctionPattern=function()
		{
			var dimension = this.DimensionForVersion;
			var bitMatrix = new BitMatrix(dimension);

			// Top left finder pattern + separator + format
			bitMatrix.setRegion(0, 0, 9, 9);
			// Top right finder pattern + separator + format
			bitMatrix.setRegion(dimension - 8, 0, 8, 9);
			// Bottom left finder pattern + separator + format
			bitMatrix.setRegion(0, dimension - 8, 9, 8);

			// Alignment patterns
			var max = this.alignmentPatternCenters.length;
			for (var x = 0; x < max; x++)
			{
				var i = this.alignmentPatternCenters[x] - 2;
				for (var y = 0; y < max; y++)
				{
					if ((x == 0 && (y == 0 || y == max - 1)) || (x == max - 1 && y == 0))
					{
						// No alignment patterns near the three finder paterns
						continue;
					}
					bitMatrix.setRegion(this.alignmentPatternCenters[y] - 2, i, 5, 5);
				}
			}

			// Vertical timing pattern
			bitMatrix.setRegion(6, 9, 1, dimension - 17);
			// Horizontal timing pattern
			bitMatrix.setRegion(9, 6, dimension - 17, 1);

			if (this.versionNumber > 6)
			{
				// Version info, top right
				bitMatrix.setRegion(dimension - 11, 0, 3, 6);
				// Version info, bottom left
				bitMatrix.setRegion(0, dimension - 11, 6, 3);
			}

			return bitMatrix;
		}
	this.getECBlocksForLevel=function( ecLevel)
	{
		return this.ecBlocks[ecLevel.ordinal()];
	}
}

Version.VERSION_DECODE_INFO = new Array(0x07C94, 0x085BC, 0x09A99, 0x0A4D3, 0x0BBF6, 0x0C762, 0x0D847, 0x0E60D, 0x0F928, 0x10B78, 0x1145D, 0x12A17, 0x13532, 0x149A6, 0x15683, 0x168C9, 0x177EC, 0x18EC4, 0x191E1, 0x1AFAB, 0x1B08E, 0x1CC1A, 0x1D33F, 0x1ED75, 0x1F250, 0x209D5, 0x216F0, 0x228BA, 0x2379F, 0x24B0B, 0x2542E, 0x26A64, 0x27541, 0x28C69);

Version.VERSIONS = buildVersions();

Version.getVersionForNumber=function( versionNumber)
{
	if (versionNumber < 1 || versionNumber > 40)
	{
		throw "ArgumentException";
	}
	return Version.VERSIONS[versionNumber - 1];
}

Version.getProvisionalVersionForDimension=function(dimension)
{
	if (dimension % 4 != 1)
	{
		throw "Error getProvisionalVersionForDimension";
	}
	try
	{
		return Version.getVersionForNumber((dimension - 17) >> 2);
	}
	catch ( iae)
	{
		throw "Error getVersionForNumber";
	}
}

Version.decodeVersionInformation=function( versionBits)
{
	var bestDifference = 0xffffffff;
	var bestVersion = 0;
	for (var i = 0; i < Version.VERSION_DECODE_INFO.length; i++)
	{
		var targetVersion = Version.VERSION_DECODE_INFO[i];
		// Do the version info bits match exactly? done.
		if (targetVersion == versionBits)
		{
			return this.getVersionForNumber(i + 7);
		}
		// Otherwise see if this is the closest to a real version info bit string
		// we have seen so far
		var bitsDifference = FormatInformation.numBitsDiffering(versionBits, targetVersion);
		if (bitsDifference < bestDifference)
		{
			bestVersion = i + 7;
			bestDifference = bitsDifference;
		}
	}
	// We can tolerate up to 3 bits of error since no two version info codewords will
	// differ in less than 4 bits.
	if (bestDifference <= 3)
	{
		return this.getVersionForNumber(bestVersion);
	}
	// If we didn't find a close enough match, fail
	return null;
}

function buildVersions()
{
	return new Array(new Version(1, new Array(), new ECBlocks(7, new ECB(1, 19)), new ECBlocks(10, new ECB(1, 16)), new ECBlocks(13, new ECB(1, 13)), new ECBlocks(17, new ECB(1, 9))),
	new Version(2, new Array(6, 18), new ECBlocks(10, new ECB(1, 34)), new ECBlocks(16, new ECB(1, 28)), new ECBlocks(22, new ECB(1, 22)), new ECBlocks(28, new ECB(1, 16))),
	new Version(3, new Array(6, 22), new ECBlocks(15, new ECB(1, 55)), new ECBlocks(26, new ECB(1, 44)), new ECBlocks(18, new ECB(2, 17)), new ECBlocks(22, new ECB(2, 13))),
	new Version(4, new Array(6, 26), new ECBlocks(20, new ECB(1, 80)), new ECBlocks(18, new ECB(2, 32)), new ECBlocks(26, new ECB(2, 24)), new ECBlocks(16, new ECB(4, 9))),
	new Version(5, new Array(6, 30), new ECBlocks(26, new ECB(1, 108)), new ECBlocks(24, new ECB(2, 43)), new ECBlocks(18, new ECB(2, 15), new ECB(2, 16)), new ECBlocks(22, new ECB(2, 11), new ECB(2, 12))),
	new Version(6, new Array(6, 34), new ECBlocks(18, new ECB(2, 68)), new ECBlocks(16, new ECB(4, 27)), new ECBlocks(24, new ECB(4, 19)), new ECBlocks(28, new ECB(4, 15))),
	new Version(7, new Array(6, 22, 38), new ECBlocks(20, new ECB(2, 78)), new ECBlocks(18, new ECB(4, 31)), new ECBlocks(18, new ECB(2, 14), new ECB(4, 15)), new ECBlocks(26, new ECB(4, 13), new ECB(1, 14))),
	new Version(8, new Array(6, 24, 42), new ECBlocks(24, new ECB(2, 97)), new ECBlocks(22, new ECB(2, 38), new ECB(2, 39)), new ECBlocks(22, new ECB(4, 18), new ECB(2, 19)), new ECBlocks(26, new ECB(4, 14), new ECB(2, 15))),
	new Version(9, new Array(6, 26, 46), new ECBlocks(30, new ECB(2, 116)), new ECBlocks(22, new ECB(3, 36), new ECB(2, 37)), new ECBlocks(20, new ECB(4, 16), new ECB(4, 17)), new ECBlocks(24, new ECB(4, 12), new ECB(4, 13))),
	new Version(10, new Array(6, 28, 50), new ECBlocks(18, new ECB(2, 68), new ECB(2, 69)), new ECBlocks(26, new ECB(4, 43), new ECB(1, 44)), new ECBlocks(24, new ECB(6, 19), new ECB(2, 20)), new ECBlocks(28, new ECB(6, 15), new ECB(2, 16))),
	new Version(11, new Array(6, 30, 54), new ECBlocks(20, new ECB(4, 81)), new ECBlocks(30, new ECB(1, 50), new ECB(4, 51)), new ECBlocks(28, new ECB(4, 22), new ECB(4, 23)), new ECBlocks(24, new ECB(3, 12), new ECB(8, 13))),
	new Version(12, new Array(6, 32, 58), new ECBlocks(24, new ECB(2, 92), new ECB(2, 93)), new ECBlocks(22, new ECB(6, 36), new ECB(2, 37)), new ECBlocks(26, new ECB(4, 20), new ECB(6, 21)), new ECBlocks(28, new ECB(7, 14), new ECB(4, 15))),
	new Version(13, new Array(6, 34, 62), new ECBlocks(26, new ECB(4, 107)), new ECBlocks(22, new ECB(8, 37), new ECB(1, 38)), new ECBlocks(24, new ECB(8, 20), new ECB(4, 21)), new ECBlocks(22, new ECB(12, 11), new ECB(4, 12))),
	new Version(14, new Array(6, 26, 46, 66), new ECBlocks(30, new ECB(3, 115), new ECB(1, 116)), new ECBlocks(24, new ECB(4, 40), new ECB(5, 41)), new ECBlocks(20, new ECB(11, 16), new ECB(5, 17)), new ECBlocks(24, new ECB(11, 12), new ECB(5, 13))),
	new Version(15, new Array(6, 26, 48, 70), new ECBlocks(22, new ECB(5, 87), new ECB(1, 88)), new ECBlocks(24, new ECB(5, 41), new ECB(5, 42)), new ECBlocks(30, new ECB(5, 24), new ECB(7, 25)), new ECBlocks(24, new ECB(11, 12), new ECB(7, 13))),
	new Version(16, new Array(6, 26, 50, 74), new ECBlocks(24, new ECB(5, 98), new ECB(1, 99)), new ECBlocks(28, new ECB(7, 45), new ECB(3, 46)), new ECBlocks(24, new ECB(15, 19), new ECB(2, 20)), new ECBlocks(30, new ECB(3, 15), new ECB(13, 16))),
	new Version(17, new Array(6, 30, 54, 78), new ECBlocks(28, new ECB(1, 107), new ECB(5, 108)), new ECBlocks(28, new ECB(10, 46), new ECB(1, 47)), new ECBlocks(28, new ECB(1, 22), new ECB(15, 23)), new ECBlocks(28, new ECB(2, 14), new ECB(17, 15))),
	new Version(18, new Array(6, 30, 56, 82), new ECBlocks(30, new ECB(5, 120), new ECB(1, 121)), new ECBlocks(26, new ECB(9, 43), new ECB(4, 44)), new ECBlocks(28, new ECB(17, 22), new ECB(1, 23)), new ECBlocks(28, new ECB(2, 14), new ECB(19, 15))),
	new Version(19, new Array(6, 30, 58, 86), new ECBlocks(28, new ECB(3, 113), new ECB(4, 114)), new ECBlocks(26, new ECB(3, 44), new ECB(11, 45)), new ECBlocks(26, new ECB(17, 21), new ECB(4, 22)), new ECBlocks(26, new ECB(9, 13), new ECB(16, 14))),
	new Version(20, new Array(6, 34, 62, 90), new ECBlocks(28, new ECB(3, 107), new ECB(5, 108)), new ECBlocks(26, new ECB(3, 41), new ECB(13, 42)), new ECBlocks(30, new ECB(15, 24), new ECB(5, 25)), new ECBlocks(28, new ECB(15, 15), new ECB(10, 16))),
	new Version(21, new Array(6, 28, 50, 72, 94), new ECBlocks(28, new ECB(4, 116), new ECB(4, 117)), new ECBlocks(26, new ECB(17, 42)), new ECBlocks(28, new ECB(17, 22), new ECB(6, 23)), new ECBlocks(30, new ECB(19, 16), new ECB(6, 17))),
	new Version(22, new Array(6, 26, 50, 74, 98), new ECBlocks(28, new ECB(2, 111), new ECB(7, 112)), new ECBlocks(28, new ECB(17, 46)), new ECBlocks(30, new ECB(7, 24), new ECB(16, 25)), new ECBlocks(24, new ECB(34, 13))),
	new Version(23, new Array(6, 30, 54, 74, 102), new ECBlocks(30, new ECB(4, 121), new ECB(5, 122)), new ECBlocks(28, new ECB(4, 47), new ECB(14, 48)), new ECBlocks(30, new ECB(11, 24), new ECB(14, 25)), new ECBlocks(30, new ECB(16, 15), new ECB(14, 16))),
	new Version(24, new Array(6, 28, 54, 80, 106), new ECBlocks(30, new ECB(6, 117), new ECB(4, 118)), new ECBlocks(28, new ECB(6, 45), new ECB(14, 46)), new ECBlocks(30, new ECB(11, 24), new ECB(16, 25)), new ECBlocks(30, new ECB(30, 16), new ECB(2, 17))),
	new Version(25, new Array(6, 32, 58, 84, 110), new ECBlocks(26, new ECB(8, 106), new ECB(4, 107)), new ECBlocks(28, new ECB(8, 47), new ECB(13, 48)), new ECBlocks(30, new ECB(7, 24), new ECB(22, 25)), new ECBlocks(30, new ECB(22, 15), new ECB(13, 16))),
	new Version(26, new Array(6, 30, 58, 86, 114), new ECBlocks(28, new ECB(10, 114), new ECB(2, 115)), new ECBlocks(28, new ECB(19, 46), new ECB(4, 47)), new ECBlocks(28, new ECB(28, 22), new ECB(6, 23)), new ECBlocks(30, new ECB(33, 16), new ECB(4, 17))),
	new Version(27, new Array(6, 34, 62, 90, 118), new ECBlocks(30, new ECB(8, 122), new ECB(4, 123)), new ECBlocks(28, new ECB(22, 45), new ECB(3, 46)), new ECBlocks(30, new ECB(8, 23), new ECB(26, 24)), new ECBlocks(30, new ECB(12, 15), 		new ECB(28, 16))),
	new Version(28, new Array(6, 26, 50, 74, 98, 122), new ECBlocks(30, new ECB(3, 117), new ECB(10, 118)), new ECBlocks(28, new ECB(3, 45), new ECB(23, 46)), new ECBlocks(30, new ECB(4, 24), new ECB(31, 25)), new ECBlocks(30, new ECB(11, 15), new ECB(31, 16))),
	new Version(29, new Array(6, 30, 54, 78, 102, 126), new ECBlocks(30, new ECB(7, 116), new ECB(7, 117)), new ECBlocks(28, new ECB(21, 45), new ECB(7, 46)), new ECBlocks(30, new ECB(1, 23), new ECB(37, 24)), new ECBlocks(30, new ECB(19, 15), new ECB(26, 16))),
	new Version(30, new Array(6, 26, 52, 78, 104, 130), new ECBlocks(30, new ECB(5, 115), new ECB(10, 116)), new ECBlocks(28, new ECB(19, 47), new ECB(10, 48)), new ECBlocks(30, new ECB(15, 24), new ECB(25, 25)), new ECBlocks(30, new ECB(23, 15), new ECB(25, 16))),
	new Version(31, new Array(6, 30, 56, 82, 108, 134), new ECBlocks(30, new ECB(13, 115), new ECB(3, 116)), new ECBlocks(28, new ECB(2, 46), new ECB(29, 47)), new ECBlocks(30, new ECB(42, 24), new ECB(1, 25)), new ECBlocks(30, new ECB(23, 15), new ECB(28, 16))),
	new Version(32, new Array(6, 34, 60, 86, 112, 138), new ECBlocks(30, new ECB(17, 115)), new ECBlocks(28, new ECB(10, 46), new ECB(23, 47)), new ECBlocks(30, new ECB(10, 24), new ECB(35, 25)), new ECBlocks(30, new ECB(19, 15), new ECB(35, 16))),
	new Version(33, new Array(6, 30, 58, 86, 114, 142), new ECBlocks(30, new ECB(17, 115), new ECB(1, 116)), new ECBlocks(28, new ECB(14, 46), new ECB(21, 47)), new ECBlocks(30, new ECB(29, 24), new ECB(19, 25)), new ECBlocks(30, new ECB(11, 15), new ECB(46, 16))),
	new Version(34, new Array(6, 34, 62, 90, 118, 146), new ECBlocks(30, new ECB(13, 115), new ECB(6, 116)), new ECBlocks(28, new ECB(14, 46), new ECB(23, 47)), new ECBlocks(30, new ECB(44, 24), new ECB(7, 25)), new ECBlocks(30, new ECB(59, 16), new ECB(1, 17))),
	new Version(35, new Array(6, 30, 54, 78, 102, 126, 150), new ECBlocks(30, new ECB(12, 121), new ECB(7, 122)), new ECBlocks(28, new ECB(12, 47), new ECB(26, 48)), new ECBlocks(30, new ECB(39, 24), new ECB(14, 25)),new ECBlocks(30, new ECB(22, 15), new ECB(41, 16))),
	new Version(36, new Array(6, 24, 50, 76, 102, 128, 154), new ECBlocks(30, new ECB(6, 121), new ECB(14, 122)), new ECBlocks(28, new ECB(6, 47), new ECB(34, 48)), new ECBlocks(30, new ECB(46, 24), new ECB(10, 25)), new ECBlocks(30, new ECB(2, 15), new ECB(64, 16))),
	new Version(37, new Array(6, 28, 54, 80, 106, 132, 158), new ECBlocks(30, new ECB(17, 122), new ECB(4, 123)), new ECBlocks(28, new ECB(29, 46), new ECB(14, 47)), new ECBlocks(30, new ECB(49, 24), new ECB(10, 25)), new ECBlocks(30, new ECB(24, 15), new ECB(46, 16))),
	new Version(38, new Array(6, 32, 58, 84, 110, 136, 162), new ECBlocks(30, new ECB(4, 122), new ECB(18, 123)), new ECBlocks(28, new ECB(13, 46), new ECB(32, 47)), new ECBlocks(30, new ECB(48, 24), new ECB(14, 25)), new ECBlocks(30, new ECB(42, 15), new ECB(32, 16))),
	new Version(39, new Array(6, 26, 54, 82, 110, 138, 166), new ECBlocks(30, new ECB(20, 117), new ECB(4, 118)), new ECBlocks(28, new ECB(40, 47), new ECB(7, 48)), new ECBlocks(30, new ECB(43, 24), new ECB(22, 25)), new ECBlocks(30, new ECB(10, 15), new ECB(67, 16))),
	new Version(40, new Array(6, 30, 58, 86, 114, 142, 170), new ECBlocks(30, new ECB(19, 118), new ECB(6, 119)), new ECBlocks(28, new ECB(18, 47), new ECB(31, 48)), new ECBlocks(30, new ECB(34, 24), new ECB(34, 25)), new ECBlocks(30, new ECB(20, 15), new ECB(61, 16))));
}

function PerspectiveTransform( a11,  a21,  a31,  a12,  a22,  a32,  a13,  a23,  a33)
{
	this.a11 = a11;
	this.a12 = a12;
	this.a13 = a13;
	this.a21 = a21;
	this.a22 = a22;
	this.a23 = a23;
	this.a31 = a31;
	this.a32 = a32;
	this.a33 = a33;
	this.transformPoints1=function( points)
		{
			var max = points.length;
			var a11 = this.a11;
			var a12 = this.a12;
			var a13 = this.a13;
			var a21 = this.a21;
			var a22 = this.a22;
			var a23 = this.a23;
			var a31 = this.a31;
			var a32 = this.a32;
			var a33 = this.a33;
			for (var i = 0; i < max; i += 2)
			{
				var x = points[i];
				var y = points[i + 1];
				var denominator = a13 * x + a23 * y + a33;
				points[i] = (a11 * x + a21 * y + a31) / denominator;
				points[i + 1] = (a12 * x + a22 * y + a32) / denominator;
			}
		}
	this. transformPoints2=function(xValues, yValues)
		{
			var n = xValues.length;
			for (var i = 0; i < n; i++)
			{
				var x = xValues[i];
				var y = yValues[i];
				var denominator = this.a13 * x + this.a23 * y + this.a33;
				xValues[i] = (this.a11 * x + this.a21 * y + this.a31) / denominator;
				yValues[i] = (this.a12 * x + this.a22 * y + this.a32) / denominator;
			}
		}

	this.buildAdjoint=function()
		{
			// Adjoint is the transpose of the cofactor matrix:
			return new PerspectiveTransform(this.a22 * this.a33 - this.a23 * this.a32, this.a23 * this.a31 - this.a21 * this.a33, this.a21 * this.a32 - this.a22 * this.a31, this.a13 * this.a32 - this.a12 * this.a33, this.a11 * this.a33 - this.a13 * this.a31, this.a12 * this.a31 - this.a11 * this.a32, this.a12 * this.a23 - this.a13 * this.a22, this.a13 * this.a21 - this.a11 * this.a23, this.a11 * this.a22 - this.a12 * this.a21);
		}
	this.times=function( other)
		{
			return new PerspectiveTransform(this.a11 * other.a11 + this.a21 * other.a12 + this.a31 * other.a13, this.a11 * other.a21 + this.a21 * other.a22 + this.a31 * other.a23, this.a11 * other.a31 + this.a21 * other.a32 + this.a31 * other.a33, this.a12 * other.a11 + this.a22 * other.a12 + this.a32 * other.a13, this.a12 * other.a21 + this.a22 * other.a22 + this.a32 * other.a23, this.a12 * other.a31 + this.a22 * other.a32 + this.a32 * other.a33, this.a13 * other.a11 + this.a23 * other.a12 +this.a33 * other.a13, this.a13 * other.a21 + this.a23 * other.a22 + this.a33 * other.a23, this.a13 * other.a31 + this.a23 * other.a32 + this.a33 * other.a33);
		}

}

PerspectiveTransform.quadrilateralToQuadrilateral=function( x0,  y0,  x1,  y1,  x2,  y2,  x3,  y3,  x0p,  y0p,  x1p,  y1p,  x2p,  y2p,  x3p,  y3p)
{

	var qToS = this.quadrilateralToSquare(x0, y0, x1, y1, x2, y2, x3, y3);
	var sToQ = this.squareToQuadrilateral(x0p, y0p, x1p, y1p, x2p, y2p, x3p, y3p);
	return sToQ.times(qToS);
}

PerspectiveTransform.squareToQuadrilateral=function( x0,  y0,  x1,  y1,  x2,  y2,  x3,  y3)
{
	 dy2 = y3 - y2;
	 dy3 = y0 - y1 + y2 - y3;
	if (dy2 == 0.0 && dy3 == 0.0)
	{
		return new PerspectiveTransform(x1 - x0, x2 - x1, x0, y1 - y0, y2 - y1, y0, 0.0, 0.0, 1.0);
	}
	else
	{
		 dx1 = x1 - x2;
		 dx2 = x3 - x2;
		 dx3 = x0 - x1 + x2 - x3;
		 dy1 = y1 - y2;
		 denominator = dx1 * dy2 - dx2 * dy1;
		 a13 = (dx3 * dy2 - dx2 * dy3) / denominator;
		 a23 = (dx1 * dy3 - dx3 * dy1) / denominator;
		return new PerspectiveTransform(x1 - x0 + a13 * x1, x3 - x0 + a23 * x3, x0, y1 - y0 + a13 * y1, y3 - y0 + a23 * y3, y0, a13, a23, 1.0);
	}
}

PerspectiveTransform.quadrilateralToSquare=function( x0,  y0,  x1,  y1,  x2,  y2,  x3,  y3)
{
	// Here, the adjoint serves as the inverse:
	return this.squareToQuadrilateral(x0, y0, x1, y1, x2, y2, x3, y3).buildAdjoint();
}

function DetectorResult(bits,  points)
{
	this.bits = bits;
	this.points = points;
}


function Detector(image)
{
	this.image=image;
	this.resultPointCallback = null;

	this.sizeOfBlackWhiteBlackRun=function( fromX,  fromY,  toX,  toY)
		{
			// Mild variant of Bresenham's algorithm;
			// see http://en.wikipedia.org/wiki/Bresenham's_line_algorithm
			var steep = Math.abs(toY - fromY) > Math.abs(toX - fromX);
			if (steep)
			{
				var temp = fromX;
				fromX = fromY;
				fromY = temp;
				temp = toX;
				toX = toY;
				toY = temp;
			}

			var dx = Math.abs(toX - fromX);
			var dy = Math.abs(toY - fromY);
			var error = - dx >> 1;
			var ystep = fromY < toY?1:- 1;
			var xstep = fromX < toX?1:- 1;
			var state = 0; // In black pixels, looking for white, first or second time
			for (var x = fromX, y = fromY; x != toX; x += xstep)
			{

				var realX = steep?y:x;
				var realY = steep?x:y;
				if (state == 1)
				{
					// In white pixels, looking for black
					if (this.image[realX + realY*qrcode.width])
					{
						state++;
					}
				}
				else
				{
					if (!this.image[realX + realY*qrcode.width])
					{
						state++;
					}
				}

				if (state == 3)
				{
					// Found black, white, black, and stumbled back onto white; done
					var diffX = x - fromX;
					var diffY = y - fromY;
					return  Math.sqrt( (diffX * diffX + diffY * diffY));
				}
				error += dy;
				if (error > 0)
				{
					if (y == toY)
					{
						break;
					}
					y += ystep;
					error -= dx;
				}
			}
			var diffX2 = toX - fromX;
			var diffY2 = toY - fromY;
			return  Math.sqrt( (diffX2 * diffX2 + diffY2 * diffY2));
		}


	this.sizeOfBlackWhiteBlackRunBothWays=function( fromX,  fromY,  toX,  toY)
		{

			var result = this.sizeOfBlackWhiteBlackRun(fromX, fromY, toX, toY);

			// Now count other way -- don't run off image though of course
			var scale = 1.0;
			var otherToX = fromX - (toX - fromX);
			if (otherToX < 0)
			{
				scale =  fromX /  (fromX - otherToX);
				otherToX = 0;
			}
			else if (otherToX >= qrcode.width)
			{
				scale =  (qrcode.width - 1 - fromX) /  (otherToX - fromX);
				otherToX = qrcode.width - 1;
			}
			var otherToY = Math.floor (fromY - (toY - fromY) * scale);

			scale = 1.0;
			if (otherToY < 0)
			{
				scale =  fromY /  (fromY - otherToY);
				otherToY = 0;
			}
			else if (otherToY >= qrcode.height)
			{
				scale =  (qrcode.height - 1 - fromY) /  (otherToY - fromY);
				otherToY = qrcode.height - 1;
			}
			otherToX = Math.floor (fromX + (otherToX - fromX) * scale);

			result += this.sizeOfBlackWhiteBlackRun(fromX, fromY, otherToX, otherToY);
			return result - 1.0; // -1 because we counted the middle pixel twice
		}



	this.calculateModuleSizeOneWay=function( pattern,  otherPattern)
		{
			var moduleSizeEst1 = this.sizeOfBlackWhiteBlackRunBothWays(Math.floor( pattern.X), Math.floor( pattern.Y), Math.floor( otherPattern.X), Math.floor(otherPattern.Y));
			var moduleSizeEst2 = this.sizeOfBlackWhiteBlackRunBothWays(Math.floor(otherPattern.X), Math.floor(otherPattern.Y), Math.floor( pattern.X), Math.floor(pattern.Y));
			if (isNaN(moduleSizeEst1))
			{
				return moduleSizeEst2 / 7.0;
			}
			if (isNaN(moduleSizeEst2))
			{
				return moduleSizeEst1 / 7.0;
			}
			// Average them, and divide by 7 since we've counted the width of 3 black modules,
			// and 1 white and 1 black module on either side. Ergo, divide sum by 14.
			return (moduleSizeEst1 + moduleSizeEst2) / 14.0;
		}


	this.calculateModuleSize=function( topLeft,  topRight,  bottomLeft)
		{
			// Take the average
			return (this.calculateModuleSizeOneWay(topLeft, topRight) + this.calculateModuleSizeOneWay(topLeft, bottomLeft)) / 2.0;
		}

	this.distance=function( pattern1,  pattern2)
	{
		xDiff = pattern1.X - pattern2.X;
		yDiff = pattern1.Y - pattern2.Y;
		return  Math.sqrt( (xDiff * xDiff + yDiff * yDiff));
	}
	this.computeDimension=function( topLeft,  topRight,  bottomLeft,  moduleSize)
		{

			var tltrCentersDimension = Math.round(this.distance(topLeft, topRight) / moduleSize);
			var tlblCentersDimension = Math.round(this.distance(topLeft, bottomLeft) / moduleSize);
			var dimension = ((tltrCentersDimension + tlblCentersDimension) >> 1) + 7;
			switch (dimension & 0x03)
			{

				// mod 4
				case 0:
					dimension++;
					break;
					// 1? do nothing

				case 2:
					dimension--;
					break;

				case 3:
					throw "Error";
				}
			return dimension;
		}

	this.findAlignmentInRegion=function( overallEstModuleSize,  estAlignmentX,  estAlignmentY,  allowanceFactor)
		{
			// Look for an alignment pattern (3 modules in size) around where it
			// should be
			var allowance = Math.floor (allowanceFactor * overallEstModuleSize);
			var alignmentAreaLeftX = Math.max(0, estAlignmentX - allowance);
			var alignmentAreaRightX = Math.min(qrcode.width - 1, estAlignmentX + allowance);
			if (alignmentAreaRightX - alignmentAreaLeftX < overallEstModuleSize * 3)
			{
				throw "Error";
			}

			var alignmentAreaTopY = Math.max(0, estAlignmentY - allowance);
			var alignmentAreaBottomY = Math.min(qrcode.height - 1, estAlignmentY + allowance);

			var alignmentFinder = new AlignmentPatternFinder(this.image, alignmentAreaLeftX, alignmentAreaTopY, alignmentAreaRightX - alignmentAreaLeftX, alignmentAreaBottomY - alignmentAreaTopY, overallEstModuleSize, this.resultPointCallback);
			return alignmentFinder.find();
		}

	this.createTransform=function( topLeft,  topRight,  bottomLeft, alignmentPattern, dimension)
		{
			var dimMinusThree =  dimension - 3.5;
			var bottomRightX;
			var bottomRightY;
			var sourceBottomRightX;
			var sourceBottomRightY;
			if (alignmentPattern != null)
			{
				bottomRightX = alignmentPattern.X;
				bottomRightY = alignmentPattern.Y;
				sourceBottomRightX = sourceBottomRightY = dimMinusThree - 3.0;
			}
			else
			{
				// Don't have an alignment pattern, just make up the bottom-right point
				bottomRightX = (topRight.X - topLeft.X) + bottomLeft.X;
				bottomRightY = (topRight.Y - topLeft.Y) + bottomLeft.Y;
				sourceBottomRightX = sourceBottomRightY = dimMinusThree;
			}

			var transform = PerspectiveTransform.quadrilateralToQuadrilateral(3.5, 3.5, dimMinusThree, 3.5, sourceBottomRightX, sourceBottomRightY, 3.5, dimMinusThree, topLeft.X, topLeft.Y, topRight.X, topRight.Y, bottomRightX, bottomRightY, bottomLeft.X, bottomLeft.Y);

			return transform;
		}

	this.sampleGrid=function( image,  transform,  dimension)
		{

			var sampler = GridSampler;
			return sampler.sampleGrid3(image, dimension, transform);
		}

	this.processFinderPatternInfo = function( info)
		{

			var topLeft = info.TopLeft;
			var topRight = info.TopRight;
			var bottomLeft = info.BottomLeft;

			var moduleSize = this.calculateModuleSize(topLeft, topRight, bottomLeft);
			if (moduleSize < 1.0)
			{
				throw "Error";
			}
			var dimension = this.computeDimension(topLeft, topRight, bottomLeft, moduleSize);
			var provisionalVersion = Version.getProvisionalVersionForDimension(dimension);
			var modulesBetweenFPCenters = provisionalVersion.DimensionForVersion - 7;

			var alignmentPattern = null;
			// Anything above version 1 has an alignment pattern
			if (provisionalVersion.AlignmentPatternCenters.length > 0)
			{

				// Guess where a "bottom right" finder pattern would have been
				var bottomRightX = topRight.X - topLeft.X + bottomLeft.X;
				var bottomRightY = topRight.Y - topLeft.Y + bottomLeft.Y;

				// Estimate that alignment pattern is closer by 3 modules
				// from "bottom right" to known top left location
				var correctionToTopLeft = 1.0 - 3.0 /  modulesBetweenFPCenters;
				var estAlignmentX = Math.floor (topLeft.X + correctionToTopLeft * (bottomRightX - topLeft.X));
				var estAlignmentY = Math.floor (topLeft.Y + correctionToTopLeft * (bottomRightY - topLeft.Y));

				// Kind of arbitrary -- expand search radius before giving up
				for (var i = 4; i <= 16; i <<= 1)
				{
					//try
					//{
						alignmentPattern = this.findAlignmentInRegion(moduleSize, estAlignmentX, estAlignmentY,  i);
						break;
					//}
					//catch (re)
					//{
						// try next round
					//}
				}
				// If we didn't find alignment pattern... well try anyway without it
			}

			var transform = this.createTransform(topLeft, topRight, bottomLeft, alignmentPattern, dimension);

			var bits = this.sampleGrid(this.image, transform, dimension);

			var points;
			if (alignmentPattern == null)
			{
				points = new Array(bottomLeft, topLeft, topRight);
			}
			else
			{
				points = new Array(bottomLeft, topLeft, topRight, alignmentPattern);
			}
			return new DetectorResult(bits, points);
		}



	this.detect=function()
	{
		var info =  new FinderPatternFinder().findFinderPattern(this.image);

		return this.processFinderPatternInfo(info);
	}
}

var FORMAT_INFO_MASK_QR = 0x5412;
var FORMAT_INFO_DECODE_LOOKUP = new Array(new Array(0x5412, 0x00), new Array(0x5125, 0x01), new Array(0x5E7C, 0x02), new Array(0x5B4B, 0x03), new Array(0x45F9, 0x04), new Array(0x40CE, 0x05), new Array(0x4F97, 0x06), new Array(0x4AA0, 0x07), new Array(0x77C4, 0x08), new Array(0x72F3, 0x09), new Array(0x7DAA, 0x0A), new Array(0x789D, 0x0B), new Array(0x662F, 0x0C), new Array(0x6318, 0x0D), new Array(0x6C41, 0x0E), new Array(0x6976, 0x0F), new Array(0x1689, 0x10), new Array(0x13BE, 0x11), new Array(0x1CE7, 0x12), new Array(0x19D0, 0x13), new Array(0x0762, 0x14), new Array(0x0255, 0x15), new Array(0x0D0C, 0x16), new Array(0x083B, 0x17), new Array(0x355F, 0x18), new Array(0x3068, 0x19), new Array(0x3F31, 0x1A), new Array(0x3A06, 0x1B), new Array(0x24B4, 0x1C), new Array(0x2183, 0x1D), new Array(0x2EDA, 0x1E), new Array(0x2BED, 0x1F));
var BITS_SET_IN_HALF_BYTE = new Array(0, 1, 1, 2, 1, 2, 2, 3, 1, 2, 2, 3, 2, 3, 3, 4);


function FormatInformation(formatInfo)
{
	this.errorCorrectionLevel = ErrorCorrectionLevel.forBits((formatInfo >> 3) & 0x03);
	this.dataMask =  (formatInfo & 0x07);

	this.__defineGetter__("ErrorCorrectionLevel", function()
	{
		return this.errorCorrectionLevel;
	});
	this.__defineGetter__("DataMask", function()
	{
		return this.dataMask;
	});
	this.GetHashCode=function()
	{
		return (this.errorCorrectionLevel.ordinal() << 3) |  dataMask;
	}
	this.Equals=function( o)
	{
		var other =  o;
		return this.errorCorrectionLevel == other.errorCorrectionLevel && this.dataMask == other.dataMask;
	}
}

FormatInformation.numBitsDiffering=function( a,  b)
{
	a ^= b; // a now has a 1 bit exactly where its bit differs with b's
	// Count bits set quickly with a series of lookups:
	return BITS_SET_IN_HALF_BYTE[a & 0x0F] + BITS_SET_IN_HALF_BYTE[(URShift(a, 4) & 0x0F)] + BITS_SET_IN_HALF_BYTE[(URShift(a, 8) & 0x0F)] + BITS_SET_IN_HALF_BYTE[(URShift(a, 12) & 0x0F)] + BITS_SET_IN_HALF_BYTE[(URShift(a, 16) & 0x0F)] + BITS_SET_IN_HALF_BYTE[(URShift(a, 20) & 0x0F)] + BITS_SET_IN_HALF_BYTE[(URShift(a, 24) & 0x0F)] + BITS_SET_IN_HALF_BYTE[(URShift(a, 28) & 0x0F)];
}

FormatInformation.decodeFormatInformation=function( maskedFormatInfo)
{
	var formatInfo = FormatInformation.doDecodeFormatInformation(maskedFormatInfo);
	if (formatInfo != null)
	{
		return formatInfo;
	}
	// Should return null, but, some QR codes apparently
	// do not mask this info. Try again by actually masking the pattern
	// first
	return FormatInformation.doDecodeFormatInformation(maskedFormatInfo ^ FORMAT_INFO_MASK_QR);
}
FormatInformation.doDecodeFormatInformation=function( maskedFormatInfo)
{
	// Find the int in FORMAT_INFO_DECODE_LOOKUP with fewest bits differing
	var bestDifference = 0xffffffff;
	var bestFormatInfo = 0;
	for (var i = 0; i < FORMAT_INFO_DECODE_LOOKUP.length; i++)
	{
		var decodeInfo = FORMAT_INFO_DECODE_LOOKUP[i];
		var targetInfo = decodeInfo[0];
		if (targetInfo == maskedFormatInfo)
		{
			// Found an exact match
			return new FormatInformation(decodeInfo[1]);
		}
		var bitsDifference = this.numBitsDiffering(maskedFormatInfo, targetInfo);
		if (bitsDifference < bestDifference)
		{
			bestFormatInfo = decodeInfo[1];
			bestDifference = bitsDifference;
		}
	}
	// Hamming distance of the 32 masked codes is 7, by construction, so <= 3 bits
	// differing means we found a match
	if (bestDifference <= 3)
	{
		return new FormatInformation(bestFormatInfo);
	}
	return null;
}

function ErrorCorrectionLevel(ordinal,  bits, name)
{
	this.ordinal_Renamed_Field = ordinal;
	this.bits = bits;
	this.name = name;
	this.__defineGetter__("Bits", function()
	{
		return this.bits;
	});
	this.__defineGetter__("Name", function()
	{
		return this.name;
	});
	this.ordinal=function()
	{
		return this.ordinal_Renamed_Field;
	}
}

ErrorCorrectionLevel.forBits=function( bits)
{
	if (bits < 0 || bits >= FOR_BITS.length)
	{
		throw "ArgumentException";
	}
	return FOR_BITS[bits];
}

var L = new ErrorCorrectionLevel(0, 0x01, "L");
var M = new ErrorCorrectionLevel(1, 0x00, "M");
var Q = new ErrorCorrectionLevel(2, 0x03, "Q");
var H = new ErrorCorrectionLevel(3, 0x02, "H");
var FOR_BITS = new Array( M, L, H, Q);

function BitMatrix( width,  height)
{
	if(!height)
		height=width;
	if (width < 1 || height < 1)
	{
		throw "Both dimensions must be greater than 0";
	}
	this.width = width;
	this.height = height;
	var rowSize = width >> 5;
	if ((width & 0x1f) != 0)
	{
		rowSize++;
	}
	this.rowSize = rowSize;
	this.bits = new Array(rowSize * height);
	for(var i=0;i<this.bits.length;i++)
		this.bits[i]=0;

	this.__defineGetter__("Width", function()
	{
		return this.width;
	});
	this.__defineGetter__("Height", function()
	{
		return this.height;
	});
	this.__defineGetter__("Dimension", function()
	{
		if (this.width != this.height)
		{
			throw "Can't call getDimension() on a non-square matrix";
		}
		return this.width;
	});

	this.get_Renamed=function( x,  y)
		{
			var offset = y * this.rowSize + (x >> 5);
			return ((URShift(this.bits[offset], (x & 0x1f))) & 1) != 0;
		}
	this.set_Renamed=function( x,  y)
		{
			var offset = y * this.rowSize + (x >> 5);
			this.bits[offset] |= 1 << (x & 0x1f);
		}
	this.flip=function( x,  y)
		{
			var offset = y * this.rowSize + (x >> 5);
			this.bits[offset] ^= 1 << (x & 0x1f);
		}
	this.clear=function()
		{
			var max = this.bits.length;
			for (var i = 0; i < max; i++)
			{
				this.bits[i] = 0;
			}
		}
	this.setRegion=function( left,  top,  width,  height)
		{
			if (top < 0 || left < 0)
			{
				throw "Left and top must be nonnegative";
			}
			if (height < 1 || width < 1)
			{
				throw "Height and width must be at least 1";
			}
			var right = left + width;
			var bottom = top + height;
			if (bottom > this.height || right > this.width)
			{
				throw "The region must fit inside the matrix";
			}
			for (var y = top; y < bottom; y++)
			{
				var offset = y * this.rowSize;
				for (var x = left; x < right; x++)
				{
					this.bits[offset + (x >> 5)] |= 1 << (x & 0x1f);
				}
			}
		}
}

function DataBlock(numDataCodewords,  codewords)
{
	this.numDataCodewords = numDataCodewords;
	this.codewords = codewords;

	this.__defineGetter__("NumDataCodewords", function()
	{
		return this.numDataCodewords;
	});
	this.__defineGetter__("Codewords", function()
	{
		return this.codewords;
	});
}

DataBlock.getDataBlocks=function(rawCodewords,  version,  ecLevel)
{

	if (rawCodewords.length != version.TotalCodewords)
	{
		throw "ArgumentException";
	}

	// Figure out the number and size of data blocks used by this version and
	// error correction level
	var ecBlocks = version.getECBlocksForLevel(ecLevel);

	// First count the total number of data blocks
	var totalBlocks = 0;
	var ecBlockArray = ecBlocks.getECBlocks();
	for (var i = 0; i < ecBlockArray.length; i++)
	{
		totalBlocks += ecBlockArray[i].Count;
	}

	// Now establish DataBlocks of the appropriate size and number of data codewords
	var result = new Array(totalBlocks);
	var numResultBlocks = 0;
	for (var j = 0; j < ecBlockArray.length; j++)
	{
		var ecBlock = ecBlockArray[j];
		for (var i = 0; i < ecBlock.Count; i++)
		{
			var numDataCodewords = ecBlock.DataCodewords;
			var numBlockCodewords = ecBlocks.ECCodewordsPerBlock + numDataCodewords;
			result[numResultBlocks++] = new DataBlock(numDataCodewords, new Array(numBlockCodewords));
		}
	}

	// All blocks have the same amount of data, except that the last n
	// (where n may be 0) have 1 more byte. Figure out where these start.
	var shorterBlocksTotalCodewords = result[0].codewords.length;
	var longerBlocksStartAt = result.length - 1;
	while (longerBlocksStartAt >= 0)
	{
		var numCodewords = result[longerBlocksStartAt].codewords.length;
		if (numCodewords == shorterBlocksTotalCodewords)
		{
			break;
		}
		longerBlocksStartAt--;
	}
	longerBlocksStartAt++;

	var shorterBlocksNumDataCodewords = shorterBlocksTotalCodewords - ecBlocks.ECCodewordsPerBlock;
	// The last elements of result may be 1 element longer;
	// first fill out as many elements as all of them have
	var rawCodewordsOffset = 0;
	for (var i = 0; i < shorterBlocksNumDataCodewords; i++)
	{
		for (var j = 0; j < numResultBlocks; j++)
		{
			result[j].codewords[i] = rawCodewords[rawCodewordsOffset++];
		}
	}
	// Fill out the last data block in the longer ones
	for (var j = longerBlocksStartAt; j < numResultBlocks; j++)
	{
		result[j].codewords[shorterBlocksNumDataCodewords] = rawCodewords[rawCodewordsOffset++];
	}
	// Now add in error correction blocks
	var max = result[0].codewords.length;
	for (var i = shorterBlocksNumDataCodewords; i < max; i++)
	{
		for (var j = 0; j < numResultBlocks; j++)
		{
			var iOffset = j < longerBlocksStartAt?i:i + 1;
			result[j].codewords[iOffset] = rawCodewords[rawCodewordsOffset++];
		}
	}
	return result;
}

function BitMatrixParser(bitMatrix)
{
	var dimension = bitMatrix.Dimension;
	if (dimension < 21 || (dimension & 0x03) != 1)
	{
		throw "Error BitMatrixParser";
	}
	this.bitMatrix = bitMatrix;
	this.parsedVersion = null;
	this.parsedFormatInfo = null;

	this.copyBit=function( i,  j,  versionBits)
	{
		return this.bitMatrix.get_Renamed(i, j)?(versionBits << 1) | 0x1:versionBits << 1;
	}

	this.readFormatInformation=function()
	{
			if (this.parsedFormatInfo != null)
			{
				return this.parsedFormatInfo;
			}

			// Read top-left format info bits
			var formatInfoBits = 0;
			for (var i = 0; i < 6; i++)
			{
				formatInfoBits = this.copyBit(i, 8, formatInfoBits);
			}
			// .. and skip a bit in the timing pattern ...
			formatInfoBits = this.copyBit(7, 8, formatInfoBits);
			formatInfoBits = this.copyBit(8, 8, formatInfoBits);
			formatInfoBits = this.copyBit(8, 7, formatInfoBits);
			// .. and skip a bit in the timing pattern ...
			for (var j = 5; j >= 0; j--)
			{
				formatInfoBits = this.copyBit(8, j, formatInfoBits);
			}

			this.parsedFormatInfo = FormatInformation.decodeFormatInformation(formatInfoBits);
			if (this.parsedFormatInfo != null)
			{
				return this.parsedFormatInfo;
			}

			// Hmm, failed. Try the top-right/bottom-left pattern
			var dimension = this.bitMatrix.Dimension;
			formatInfoBits = 0;
			var iMin = dimension - 8;
			for (var i = dimension - 1; i >= iMin; i--)
			{
				formatInfoBits = this.copyBit(i, 8, formatInfoBits);
			}
			for (var j = dimension - 7; j < dimension; j++)
			{
				formatInfoBits = this.copyBit(8, j, formatInfoBits);
			}

			this.parsedFormatInfo = FormatInformation.decodeFormatInformation(formatInfoBits);
			if (this.parsedFormatInfo != null)
			{
				return this.parsedFormatInfo;
			}
			throw "Error readFormatInformation";
	}
	this.readVersion=function()
		{

			if (this.parsedVersion != null)
			{
				return this.parsedVersion;
			}

			var dimension = this.bitMatrix.Dimension;

			var provisionalVersion = (dimension - 17) >> 2;
			if (provisionalVersion <= 6)
			{
				return Version.getVersionForNumber(provisionalVersion);
			}

			// Read top-right version info: 3 wide by 6 tall
			var versionBits = 0;
			var ijMin = dimension - 11;
			for (var j = 5; j >= 0; j--)
			{
				for (var i = dimension - 9; i >= ijMin; i--)
				{
					versionBits = this.copyBit(i, j, versionBits);
				}
			}

			this.parsedVersion = Version.decodeVersionInformation(versionBits);
			if (this.parsedVersion != null && this.parsedVersion.DimensionForVersion == dimension)
			{
				return this.parsedVersion;
			}

			// Hmm, failed. Try bottom left: 6 wide by 3 tall
			versionBits = 0;
			for (var i = 5; i >= 0; i--)
			{
				for (var j = dimension - 9; j >= ijMin; j--)
				{
					versionBits = this.copyBit(i, j, versionBits);
				}
			}

			this.parsedVersion = Version.decodeVersionInformation(versionBits);
			if (this.parsedVersion != null && this.parsedVersion.DimensionForVersion == dimension)
			{
				return this.parsedVersion;
			}
			throw "Error readVersion";
		}
	this.readCodewords=function()
		{

			var formatInfo = this.readFormatInformation();
			var version = this.readVersion();

			// Get the data mask for the format used in this QR Code. This will exclude
			// some bits from reading as we wind through the bit matrix.
			var dataMask = DataMask.forReference( formatInfo.DataMask);
			var dimension = this.bitMatrix.Dimension;
			dataMask.unmaskBitMatrix(this.bitMatrix, dimension);

			var functionPattern = version.buildFunctionPattern();

			var readingUp = true;
			var result = new Array(version.TotalCodewords);
			var resultOffset = 0;
			var currentByte = 0;
			var bitsRead = 0;
			// Read columns in pairs, from right to left
			for (var j = dimension - 1; j > 0; j -= 2)
			{
				if (j == 6)
				{
					// Skip whole column with vertical alignment pattern;
					// saves time and makes the other code proceed more cleanly
					j--;
				}
				// Read alternatingly from bottom to top then top to bottom
				for (var count = 0; count < dimension; count++)
				{
					var i = readingUp?dimension - 1 - count:count;
					for (var col = 0; col < 2; col++)
					{
						// Ignore bits covered by the function pattern
						if (!functionPattern.get_Renamed(j - col, i))
						{
							// Read a bit
							bitsRead++;
							currentByte <<= 1;
							if (this.bitMatrix.get_Renamed(j - col, i))
							{
								currentByte |= 1;
							}
							// If we've made a whole byte, save it off
							if (bitsRead == 8)
							{
								result[resultOffset++] =  currentByte;
								bitsRead = 0;
								currentByte = 0;
							}
						}
					}
				}
				readingUp ^= true; // readingUp = !readingUp; // switch directions
			}
			if (resultOffset != version.TotalCodewords)
			{
				throw "Error readCodewords";
			}
			return result;
		}
}

DataMask = {};

DataMask.forReference = function(reference)
{
	if (reference < 0 || reference > 7)
	{
		throw "System.ArgumentException";
	}
	return DataMask.DATA_MASKS[reference];
}

function DataMask000()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		return ((i + j) & 0x01) == 0;
	}
}

function DataMask001()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		return (i & 0x01) == 0;
	}
}

function DataMask010()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		return j % 3 == 0;
	}
}

function DataMask011()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		return (i + j) % 3 == 0;
	}
}

function DataMask100()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		return (((URShift(i, 1)) + (j / 3)) & 0x01) == 0;
	}
}

function DataMask101()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		var temp = i * j;
		return (temp & 0x01) + (temp % 3) == 0;
	}
}

function DataMask110()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		var temp = i * j;
		return (((temp & 0x01) + (temp % 3)) & 0x01) == 0;
	}
}
function DataMask111()
{
	this.unmaskBitMatrix=function(bits,  dimension)
	{
		for (var i = 0; i < dimension; i++)
		{
			for (var j = 0; j < dimension; j++)
			{
				if (this.isMasked(i, j))
				{
					bits.flip(j, i);
				}
			}
		}
	}
	this.isMasked=function( i,  j)
	{
		return ((((i + j) & 0x01) + ((i * j) % 3)) & 0x01) == 0;
	}
}

DataMask.DATA_MASKS = new Array(new DataMask000(), new DataMask001(), new DataMask010(), new DataMask011(), new DataMask100(), new DataMask101(), new DataMask110(), new DataMask111());

function ReedSolomonDecoder(field)
{
	this.field = field;
	this.decode=function(received,  twoS)
	{
			var poly = new GF256Poly(this.field, received);
			var syndromeCoefficients = new Array(twoS);
			for(var i=0;i<syndromeCoefficients.length;i++)syndromeCoefficients[i]=0;
			var dataMatrix = false;//this.field.Equals(GF256.DATA_MATRIX_FIELD);
			var noError = true;
			for (var i = 0; i < twoS; i++)
			{
				// Thanks to sanfordsquires for this fix:
				var eval = poly.evaluateAt(this.field.exp(dataMatrix?i + 1:i));
				syndromeCoefficients[syndromeCoefficients.length - 1 - i] = eval;
				if (eval != 0)
				{
					noError = false;
				}
			}
			if (noError)
			{
				return ;
			}
			var syndrome = new GF256Poly(this.field, syndromeCoefficients);
			var sigmaOmega = this.runEuclideanAlgorithm(this.field.buildMonomial(twoS, 1), syndrome, twoS);
			var sigma = sigmaOmega[0];
			var omega = sigmaOmega[1];
			var errorLocations = this.findErrorLocations(sigma);
			var errorMagnitudes = this.findErrorMagnitudes(omega, errorLocations, dataMatrix);
			for (var i = 0; i < errorLocations.length; i++)
			{
				var position = received.length - 1 - this.field.log(errorLocations[i]);
				if (position < 0)
				{
					throw "ReedSolomonException Bad error location";
				}
				received[position] = GF256.addOrSubtract(received[position], errorMagnitudes[i]);
			}
	}

	this.runEuclideanAlgorithm=function( a,  b,  R)
		{
			// Assume a's degree is >= b's
			if (a.Degree < b.Degree)
			{
				var temp = a;
				a = b;
				b = temp;
			}

			var rLast = a;
			var r = b;
			var sLast = this.field.One;
			var s = this.field.Zero;
			var tLast = this.field.Zero;
			var t = this.field.One;

			// Run Euclidean algorithm until r's degree is less than R/2
			while (r.Degree >= Math.floor(R / 2))
			{
				var rLastLast = rLast;
				var sLastLast = sLast;
				var tLastLast = tLast;
				rLast = r;
				sLast = s;
				tLast = t;

				// Divide rLastLast by rLast, with quotient in q and remainder in r
				if (rLast.Zero)
				{
					// Oops, Euclidean algorithm already terminated?
					throw "r_{i-1} was zero";
				}
				r = rLastLast;
				var q = this.field.Zero;
				var denominatorLeadingTerm = rLast.getCoefficient(rLast.Degree);
				var dltInverse = this.field.inverse(denominatorLeadingTerm);
				while (r.Degree >= rLast.Degree && !r.Zero)
				{
					var degreeDiff = r.Degree - rLast.Degree;
					var scale = this.field.multiply(r.getCoefficient(r.Degree), dltInverse);
					q = q.addOrSubtract(this.field.buildMonomial(degreeDiff, scale));
					r = r.addOrSubtract(rLast.multiplyByMonomial(degreeDiff, scale));
					//r.EXE();
				}

				s = q.multiply1(sLast).addOrSubtract(sLastLast);
				t = q.multiply1(tLast).addOrSubtract(tLastLast);
			}

			var sigmaTildeAtZero = t.getCoefficient(0);
			if (sigmaTildeAtZero == 0)
			{
				throw "ReedSolomonException sigmaTilde(0) was zero";
			}

			var inverse = this.field.inverse(sigmaTildeAtZero);
			var sigma = t.multiply2(inverse);
			var omega = r.multiply2(inverse);
			return new Array(sigma, omega);
		}
	this.findErrorLocations=function( errorLocator)
		{
			// This is a direct application of Chien's search
			var numErrors = errorLocator.Degree;
			if (numErrors == 1)
			{
				// shortcut
				return new Array(errorLocator.getCoefficient(1));
			}
			var result = new Array(numErrors);
			var e = 0;
			for (var i = 1; i < 256 && e < numErrors; i++)
			{
				if (errorLocator.evaluateAt(i) == 0)
				{
					result[e] = this.field.inverse(i);
					e++;
				}
			}
			if (e != numErrors)
			{
				throw "Error locator degree does not match number of roots";
			}
			return result;
		}
	this.findErrorMagnitudes=function( errorEvaluator,  errorLocations,  dataMatrix)
		{
			// This is directly applying Forney's Formula
			var s = errorLocations.length;
			var result = new Array(s);
			for (var i = 0; i < s; i++)
			{
				var xiInverse = this.field.inverse(errorLocations[i]);
				var denominator = 1;
				for (var j = 0; j < s; j++)
				{
					if (i != j)
					{
						denominator = this.field.multiply(denominator, GF256.addOrSubtract(1, this.field.multiply(errorLocations[j], xiInverse)));
					}
				}
				result[i] = this.field.multiply(errorEvaluator.evaluateAt(xiInverse), this.field.inverse(denominator));
				// Thanks to sanfordsquires for this fix:
				if (dataMatrix)
				{
					result[i] = this.field.multiply(result[i], xiInverse);
				}
			}
			return result;
		}
}

function GF256Poly(field,  coefficients)
{
	if (coefficients == null || coefficients.length == 0)
	{
		throw "System.ArgumentException";
	}
	this.field = field;
	var coefficientsLength = coefficients.length;
	if (coefficientsLength > 1 && coefficients[0] == 0)
	{
		// Leading term must be non-zero for anything except the constant polynomial "0"
		var firstNonZero = 1;
		while (firstNonZero < coefficientsLength && coefficients[firstNonZero] == 0)
		{
			firstNonZero++;
		}
		if (firstNonZero == coefficientsLength)
		{
			this.coefficients = field.Zero.coefficients;
		}
		else
		{
			this.coefficients = new Array(coefficientsLength - firstNonZero);
			for(var i=0;i<this.coefficients.length;i++)this.coefficients[i]=0;
			//Array.Copy(coefficients, firstNonZero, this.coefficients, 0, this.coefficients.length);
			for(var ci=0;ci<this.coefficients.length;ci++)this.coefficients[ci]=coefficients[firstNonZero+ci];
		}
	}
	else
	{
		this.coefficients = coefficients;
	}

	this.__defineGetter__("Zero", function()
	{
		return this.coefficients[0] == 0;
	});
	this.__defineGetter__("Degree", function()
	{
		return this.coefficients.length - 1;
	});
	this.__defineGetter__("Coefficients", function()
	{
		return this.coefficients;
	});

	this.getCoefficient=function( degree)
	{
		return this.coefficients[this.coefficients.length - 1 - degree];
	}

	this.evaluateAt=function( a)
	{
		if (a == 0)
		{
			// Just return the x^0 coefficient
			return this.getCoefficient(0);
		}
		var size = this.coefficients.length;
		if (a == 1)
		{
			// Just the sum of the coefficients
			var result = 0;
			for (var i = 0; i < size; i++)
			{
				result = GF256.addOrSubtract(result, this.coefficients[i]);
			}
			return result;
		}
		var result2 = this.coefficients[0];
		for (var i = 1; i < size; i++)
		{
			result2 = GF256.addOrSubtract(this.field.multiply(a, result2), this.coefficients[i]);
		}
		return result2;
	}

	this.addOrSubtract=function( other)
		{
			if (this.field != other.field)
			{
				throw "GF256Polys do not have same GF256 field";
			}
			if (this.Zero)
			{
				return other;
			}
			if (other.Zero)
			{
				return this;
			}

			var smallerCoefficients = this.coefficients;
			var largerCoefficients = other.coefficients;
			if (smallerCoefficients.length > largerCoefficients.length)
			{
				var temp = smallerCoefficients;
				smallerCoefficients = largerCoefficients;
				largerCoefficients = temp;
			}
			var sumDiff = new Array(largerCoefficients.length);
			var lengthDiff = largerCoefficients.length - smallerCoefficients.length;
			// Copy high-order terms only found in higher-degree polynomial's coefficients
			//Array.Copy(largerCoefficients, 0, sumDiff, 0, lengthDiff);
			for(var ci=0;ci<lengthDiff;ci++)sumDiff[ci]=largerCoefficients[ci];

			for (var i = lengthDiff; i < largerCoefficients.length; i++)
			{
				sumDiff[i] = GF256.addOrSubtract(smallerCoefficients[i - lengthDiff], largerCoefficients[i]);
			}

			return new GF256Poly(field, sumDiff);
	}
	this.multiply1=function( other)
		{
			if (this.field!=other.field)
			{
				throw "GF256Polys do not have same GF256 field";
			}
			if (this.Zero || other.Zero)
			{
				return this.field.Zero;
			}
			var aCoefficients = this.coefficients;
			var aLength = aCoefficients.length;
			var bCoefficients = other.coefficients;
			var bLength = bCoefficients.length;
			var product = new Array(aLength + bLength - 1);
			for (var i = 0; i < aLength; i++)
			{
				var aCoeff = aCoefficients[i];
				for (var j = 0; j < bLength; j++)
				{
					product[i + j] = GF256.addOrSubtract(product[i + j], this.field.multiply(aCoeff, bCoefficients[j]));
				}
			}
			return new GF256Poly(this.field, product);
		}
	this.multiply2=function( scalar)
		{
			if (scalar == 0)
			{
				return this.field.Zero;
			}
			if (scalar == 1)
			{
				return this;
			}
			var size = this.coefficients.length;
			var product = new Array(size);
			for (var i = 0; i < size; i++)
			{
				product[i] = this.field.multiply(this.coefficients[i], scalar);
			}
			return new GF256Poly(this.field, product);
		}
	this.multiplyByMonomial=function( degree,  coefficient)
		{
			if (degree < 0)
			{
				throw "System.ArgumentException";
			}
			if (coefficient == 0)
			{
				return this.field.Zero;
			}
			var size = this.coefficients.length;
			var product = new Array(size + degree);
			for(var i=0;i<product.length;i++)product[i]=0;
			for (var i = 0; i < size; i++)
			{
				product[i] = this.field.multiply(this.coefficients[i], coefficient);
			}
			return new GF256Poly(this.field, product);
		}
	this.divide=function( other)
		{
			if (this.field!=other.field)
			{
				throw "GF256Polys do not have same GF256 field";
			}
			if (other.Zero)
			{
				throw "Divide by 0";
			}

			var quotient = this.field.Zero;
			var remainder = this;

			var denominatorLeadingTerm = other.getCoefficient(other.Degree);
			var inverseDenominatorLeadingTerm = this.field.inverse(denominatorLeadingTerm);

			while (remainder.Degree >= other.Degree && !remainder.Zero)
			{
				var degreeDifference = remainder.Degree - other.Degree;
				var scale = this.field.multiply(remainder.getCoefficient(remainder.Degree), inverseDenominatorLeadingTerm);
				var term = other.multiplyByMonomial(degreeDifference, scale);
				var iterationQuotient = this.field.buildMonomial(degreeDifference, scale);
				quotient = quotient.addOrSubtract(iterationQuotient);
				remainder = remainder.addOrSubtract(term);
			}

			return new Array(quotient, remainder);
		}
}

function GF256( primitive)
{
	this.expTable = new Array(256);
	this.logTable = new Array(256);
	var x = 1;
	for (var i = 0; i < 256; i++)
	{
		this.expTable[i] = x;
		x <<= 1; // x = x * 2; we're assuming the generator alpha is 2
		if (x >= 0x100)
		{
			x ^= primitive;
		}
	}
	for (var i = 0; i < 255; i++)
	{
		this.logTable[this.expTable[i]] = i;
	}
	// logTable[0] == 0 but this should never be used
	var at0=new Array(1);at0[0]=0;
	this.zero = new GF256Poly(this, new Array(at0));
	var at1=new Array(1);at1[0]=1;
	this.one = new GF256Poly(this, new Array(at1));

	this.__defineGetter__("Zero", function()
	{
		return this.zero;
	});
	this.__defineGetter__("One", function()
	{
		return this.one;
	});
	this.buildMonomial=function( degree,  coefficient)
		{
			if (degree < 0)
			{
				throw "System.ArgumentException";
			}
			if (coefficient == 0)
			{
				return zero;
			}
			var coefficients = new Array(degree + 1);
			for(var i=0;i<coefficients.length;i++)coefficients[i]=0;
			coefficients[0] = coefficient;
			return new GF256Poly(this, coefficients);
		}
	this.exp=function( a)
		{
			return this.expTable[a];
		}
	this.log=function( a)
		{
			if (a == 0)
			{
				throw "System.ArgumentException";
			}
			return this.logTable[a];
		}
	this.inverse=function( a)
		{
			if (a == 0)
			{
				throw "System.ArithmeticException";
			}
			return this.expTable[255 - this.logTable[a]];
		}
	this.multiply=function( a,  b)
		{
			if (a == 0 || b == 0)
			{
				return 0;
			}
			if (a == 1)
			{
				return b;
			}
			if (b == 1)
			{
				return a;
			}
			return this.expTable[(this.logTable[a] + this.logTable[b]) % 255];
		}
}

GF256.QR_CODE_FIELD = new GF256(0x011D);
GF256.DATA_MATRIX_FIELD = new GF256(0x012D);

GF256.addOrSubtract=function( a,  b)
{
	return a ^ b;
}

Decoder={};
Decoder.rsDecoder = new ReedSolomonDecoder(GF256.QR_CODE_FIELD);

Decoder.correctErrors=function( codewordBytes,  numDataCodewords)
{
	var numCodewords = codewordBytes.length;
	// First read into an array of ints
	var codewordsInts = new Array(numCodewords);
	for (var i = 0; i < numCodewords; i++)
	{
		codewordsInts[i] = codewordBytes[i] & 0xFF;
	}
	var numECCodewords = codewordBytes.length - numDataCodewords;
	try
	{
		Decoder.rsDecoder.decode(codewordsInts, numECCodewords);
		//var corrector = new ReedSolomon(codewordsInts, numECCodewords);
		//corrector.correct();
	}
	catch ( rse)
	{
		throw rse;
	}
	// Copy back into array of bytes -- only need to worry about the bytes that were data
	// We don't care about errors in the error-correction codewords
	for (var i = 0; i < numDataCodewords; i++)
	{
		codewordBytes[i] =  codewordsInts[i];
	}
}

Decoder.decode=function(bits)
{
	var parser = new BitMatrixParser(bits);
	var version = parser.readVersion();
	var ecLevel = parser.readFormatInformation().ErrorCorrectionLevel;

	// Read codewords
	var codewords = parser.readCodewords();

	// Separate into data blocks
	var dataBlocks = DataBlock.getDataBlocks(codewords, version, ecLevel);

	// Count total number of data bytes
	var totalBytes = 0;
	for (var i = 0; i < dataBlocks.length; i++)
	{
		totalBytes += dataBlocks[i].NumDataCodewords;
	}
	var resultBytes = new Array(totalBytes);
	var resultOffset = 0;

	// Error-correct and copy data blocks together into a stream of bytes
	for (var j = 0; j < dataBlocks.length; j++)
	{
		var dataBlock = dataBlocks[j];
		var codewordBytes = dataBlock.Codewords;
		var numDataCodewords = dataBlock.NumDataCodewords;
		Decoder.correctErrors(codewordBytes, numDataCodewords);
		for (var i = 0; i < numDataCodewords; i++)
		{
			resultBytes[resultOffset++] = codewordBytes[i];
		}
	}

	// Decode the contents of that stream of bytes
	var reader = new QRCodeDataBlockReader(resultBytes, version.VersionNumber, ecLevel.Bits);
	return reader;
	//return DecodedBitStreamParser.decode(resultBytes, version, ecLevel);
}

qrcode = {};
qrcode.imagedata = null;
qrcode.width = 0;
qrcode.height = 0;
qrcode.qrCodeSymbol = null;
qrcode.debug = false;
qrcode.maxImgSize = 1024*1024;

qrcode.sizeOfDataLengthInfo =  [  [ 10, 9, 8, 8 ],  [ 12, 11, 16, 10 ],  [ 14, 13, 16, 12 ] ];

qrcode.callback = null;

qrcode.decode = function(src){

    if(arguments.length==0)
    {
        var canvas_qr = document.getElementById("qr-canvas");
        var context = canvas_qr.getContext('2d');
        qrcode.width = canvas_qr.width;
        qrcode.height = canvas_qr.height;
        qrcode.imagedata = context.getImageData(0, 0, qrcode.width, qrcode.height);
        qrcode.result = qrcode.process(context);
        if(qrcode.callback!=null)
            qrcode.callback(qrcode.result);
        return qrcode.result;
    }
    else
    {
        var image = new Image();
        image.onload=function(){
            //var canvas_qr = document.getElementById("qr-canvas");
            var canvas_qr = document.createElement('canvas');
            var context = canvas_qr.getContext('2d');
            var nheight = image.height;
            var nwidth = image.width;
            if(image.width*image.height>qrcode.maxImgSize)
            {
                var ir = image.width / image.height;
                nheight = Math.sqrt(qrcode.maxImgSize/ir);
                nwidth=ir*nheight;
            }

            canvas_qr.width = nwidth;
            canvas_qr.height = nheight;

            context.drawImage(image, 0, 0, canvas_qr.width, canvas_qr.height );
            qrcode.width = canvas_qr.width;
            qrcode.height = canvas_qr.height;
            try{
                qrcode.imagedata = context.getImageData(0, 0, canvas_qr.width, canvas_qr.height);
            }catch(e){
                qrcode.result = "Cross domain image reading not supported in your browser! Save it to your computer then drag and drop the file!";
                if(qrcode.callback!=null)
                    qrcode.callback(qrcode.result);
                return;
            }

            try
            {
                qrcode.result = qrcode.process(context);
            }
            catch(e)
            {
                console.log(e);
                qrcode.result = "error decoding QR Code";
            }
            if(qrcode.callback!=null)
                qrcode.callback(qrcode.result);
        }
        image.src = src;
    }
}

qrcode.isUrl = function(s)
{
    var regexp = /(ftp|http|https):\/\/(\w+:{0,1}\w*@)?(\S+)(:[0-9]+)?(\/|\/([\w#!:.?+=&%@!\-\/]))?/;
    return regexp.test(s);
}

qrcode.decode_url = function (s)
{
  var escaped = "";
  try{
    escaped = escape( s );
  }
  catch(e)
  {
    console.log(e);
    escaped = s;
  }
  var ret = "";
  try{
    ret = decodeURIComponent( escaped );
  }
  catch(e)
  {
    console.log(e);
    ret = escaped;
  }
  return ret;
}

qrcode.decode_utf8 = function ( s )
{
    if(qrcode.isUrl(s))
        return qrcode.decode_url(s);
    else
        return s;
}

qrcode.process = function(ctx){

    var start = new Date().getTime();

    var image = qrcode.grayScaleToBitmap(qrcode.grayscale());
    //var image = qrcode.binarize(128);

    if(qrcode.debug)
    {
        for (var y = 0; y < qrcode.height; y++)
        {
            for (var x = 0; x < qrcode.width; x++)
            {
                var point = (x * 4) + (y * qrcode.width * 4);
                qrcode.imagedata.data[point] = image[x+y*qrcode.width]?0:0;
                qrcode.imagedata.data[point+1] = image[x+y*qrcode.width]?0:0;
                qrcode.imagedata.data[point+2] = image[x+y*qrcode.width]?255:0;
            }
        }
        ctx.putImageData(qrcode.imagedata, 0, 0);
    }

    //var finderPatternInfo = new FinderPatternFinder().findFinderPattern(image);

    var detector = new Detector(image);

    var qRCodeMatrix = detector.detect();

    /*for (var y = 0; y < qRCodeMatrix.bits.Height; y++)
    {
        for (var x = 0; x < qRCodeMatrix.bits.Width; x++)
        {
            var point = (x * 4*2) + (y*2 * qrcode.width * 4);
            qrcode.imagedata.data[point] = qRCodeMatrix.bits.get_Renamed(x,y)?0:0;
            qrcode.imagedata.data[point+1] = qRCodeMatrix.bits.get_Renamed(x,y)?0:0;
            qrcode.imagedata.data[point+2] = qRCodeMatrix.bits.get_Renamed(x,y)?255:0;
        }
    }*/
    if(qrcode.debug)
        ctx.putImageData(qrcode.imagedata, 0, 0);

    var reader = Decoder.decode(qRCodeMatrix.bits);
    var data = reader.DataByte;
    var str="";
    for(var i=0;i<data.length;i++)
    {
        for(var j=0;j<data[i].length;j++)
            str+=String.fromCharCode(data[i][j]);
    }

    var end = new Date().getTime();
    var time = end - start;
    console.log(time);

    return qrcode.decode_utf8(str);
    //alert("Time:" + time + " Code: "+str);
}

qrcode.getPixel = function(x,y){
    if (qrcode.width < x) {
        throw "point error";
    }
    if (qrcode.height < y) {
        throw "point error";
    }
    point = (x * 4) + (y * qrcode.width * 4);
    p = (qrcode.imagedata.data[point]*33 + qrcode.imagedata.data[point + 1]*34 + qrcode.imagedata.data[point + 2]*33)/100;
    return p;
}

qrcode.binarize = function(th){
    var ret = new Array(qrcode.width*qrcode.height);
    for (var y = 0; y < qrcode.height; y++)
    {
        for (var x = 0; x < qrcode.width; x++)
        {
            var gray = qrcode.getPixel(x, y);

            ret[x+y*qrcode.width] = gray<=th?true:false;
        }
    }
    return ret;
}

qrcode.getMiddleBrightnessPerArea=function(image)
{
    var numSqrtArea = 4;
    //obtain middle brightness((min + max) / 2) per area
    var areaWidth = Math.floor(qrcode.width / numSqrtArea);
    var areaHeight = Math.floor(qrcode.height / numSqrtArea);
    var minmax = new Array(numSqrtArea);
    for (var i = 0; i < numSqrtArea; i++)
    {
        minmax[i] = new Array(numSqrtArea);
        for (var i2 = 0; i2 < numSqrtArea; i2++)
        {
            minmax[i][i2] = new Array(0,0);
        }
    }
    for (var ay = 0; ay < numSqrtArea; ay++)
    {
        for (var ax = 0; ax < numSqrtArea; ax++)
        {
            minmax[ax][ay][0] = 0xFF;
            for (var dy = 0; dy < areaHeight; dy++)
            {
                for (var dx = 0; dx < areaWidth; dx++)
                {
                    var target = image[areaWidth * ax + dx+(areaHeight * ay + dy)*qrcode.width];
                    if (target < minmax[ax][ay][0])
                        minmax[ax][ay][0] = target;
                    if (target > minmax[ax][ay][1])
                        minmax[ax][ay][1] = target;
                }
            }
            //minmax[ax][ay][0] = (minmax[ax][ay][0] + minmax[ax][ay][1]) / 2;
        }
    }
    var middle = new Array(numSqrtArea);
    for (var i3 = 0; i3 < numSqrtArea; i3++)
    {
        middle[i3] = new Array(numSqrtArea);
    }
    for (var ay = 0; ay < numSqrtArea; ay++)
    {
        for (var ax = 0; ax < numSqrtArea; ax++)
        {
            middle[ax][ay] = Math.floor((minmax[ax][ay][0] + minmax[ax][ay][1]) / 2);
            //Console.out.print(middle[ax][ay] + ",");
        }
        //Console.out.println("");
    }
    //Console.out.println("");

    return middle;
}

qrcode.grayScaleToBitmap=function(grayScale)
{
    var middle = qrcode.getMiddleBrightnessPerArea(grayScale);
    var sqrtNumArea = middle.length;
    var areaWidth = Math.floor(qrcode.width / sqrtNumArea);
    var areaHeight = Math.floor(qrcode.height / sqrtNumArea);
    var bitmap = new Array(qrcode.height*qrcode.width);

    for (var ay = 0; ay < sqrtNumArea; ay++)
    {
        for (var ax = 0; ax < sqrtNumArea; ax++)
        {
            for (var dy = 0; dy < areaHeight; dy++)
            {
                for (var dx = 0; dx < areaWidth; dx++)
                {
                    bitmap[areaWidth * ax + dx+ (areaHeight * ay + dy)*qrcode.width] = (grayScale[areaWidth * ax + dx+ (areaHeight * ay + dy)*qrcode.width] < middle[ax][ay])?true:false;
                }
            }
        }
    }
    return bitmap;
}

qrcode.grayscale = function(){
    var ret = new Array(qrcode.width*qrcode.height);
    for (var y = 0; y < qrcode.height; y++)
    {
        for (var x = 0; x < qrcode.width; x++)
        {
            var gray = qrcode.getPixel(x, y);

            ret[x+y*qrcode.width] = gray;
        }
    }
    return ret;
}




function URShift( number,  bits)
{
    if (number >= 0)
        return number >> bits;
    else
        return (number >> bits) + (2 << ~bits);
}


Array.prototype.remove = function(from, to) {
  var rest = this.slice((to || from) + 1 || this.length);
  this.length = from < 0 ? this.length + from : from;
  return this.push.apply(this, rest);
};

var MIN_SKIP = 3;
var MAX_MODULES = 57;
var INTEGER_MATH_SHIFT = 8;
var CENTER_QUORUM = 2;

qrcode.orderBestPatterns=function(patterns)
		{

			function distance( pattern1,  pattern2)
			{
				xDiff = pattern1.X - pattern2.X;
				yDiff = pattern1.Y - pattern2.Y;
				return  Math.sqrt( (xDiff * xDiff + yDiff * yDiff));
			}

			/// <summary> Returns the z component of the cross product between vectors BC and BA.</summary>
			function crossProductZ( pointA,  pointB,  pointC)
			{
				var bX = pointB.x;
				var bY = pointB.y;
				return ((pointC.x - bX) * (pointA.y - bY)) - ((pointC.y - bY) * (pointA.x - bX));
			}


			// Find distances between pattern centers
			var zeroOneDistance = distance(patterns[0], patterns[1]);
			var oneTwoDistance = distance(patterns[1], patterns[2]);
			var zeroTwoDistance = distance(patterns[0], patterns[2]);

			var pointA, pointB, pointC;
			// Assume one closest to other two is B; A and C will just be guesses at first
			if (oneTwoDistance >= zeroOneDistance && oneTwoDistance >= zeroTwoDistance)
			{
				pointB = patterns[0];
				pointA = patterns[1];
				pointC = patterns[2];
			}
			else if (zeroTwoDistance >= oneTwoDistance && zeroTwoDistance >= zeroOneDistance)
			{
				pointB = patterns[1];
				pointA = patterns[0];
				pointC = patterns[2];
			}
			else
			{
				pointB = patterns[2];
				pointA = patterns[0];
				pointC = patterns[1];
			}

			// Use cross product to figure out whether A and C are correct or flipped.
			// This asks whether BC x BA has a positive z component, which is the arrangement
			// we want for A, B, C. If it's negative, then we've got it flipped around and
			// should swap A and C.
			if (crossProductZ(pointA, pointB, pointC) < 0.0)
			{
				var temp = pointA;
				pointA = pointC;
				pointC = temp;
			}

			patterns[0] = pointA;
			patterns[1] = pointB;
			patterns[2] = pointC;
		}


function FinderPattern(posX, posY,  estimatedModuleSize)
{
	this.x=posX;
	this.y=posY;
	this.count = 1;
	this.estimatedModuleSize = estimatedModuleSize;

	this.__defineGetter__("EstimatedModuleSize", function()
	{
		return this.estimatedModuleSize;
	});
	this.__defineGetter__("Count", function()
	{
		return this.count;
	});
	this.__defineGetter__("X", function()
	{
		return this.x;
	});
	this.__defineGetter__("Y", function()
	{
		return this.y;
	});
	this.incrementCount = function()
	{
		this.count++;
	}
	this.aboutEquals=function( moduleSize,  i,  j)
		{
			if (Math.abs(i - this.y) <= moduleSize && Math.abs(j - this.x) <= moduleSize)
			{
				var moduleSizeDiff = Math.abs(moduleSize - this.estimatedModuleSize);
				return moduleSizeDiff <= 1.0 || moduleSizeDiff / this.estimatedModuleSize <= 1.0;
			}
			return false;
		}

}

function FinderPatternInfo(patternCenters)
{
	this.bottomLeft = patternCenters[0];
	this.topLeft = patternCenters[1];
	this.topRight = patternCenters[2];
	this.__defineGetter__("BottomLeft", function()
	{
		return this.bottomLeft;
	});
	this.__defineGetter__("TopLeft", function()
	{
		return this.topLeft;
	});
	this.__defineGetter__("TopRight", function()
	{
		return this.topRight;
	});
}

function FinderPatternFinder()
{
	this.image=null;
	this.possibleCenters = [];
	this.hasSkipped = false;
	this.crossCheckStateCount = new Array(0,0,0,0,0);
	this.resultPointCallback = null;

	this.__defineGetter__("CrossCheckStateCount", function()
	{
		this.crossCheckStateCount[0] = 0;
		this.crossCheckStateCount[1] = 0;
		this.crossCheckStateCount[2] = 0;
		this.crossCheckStateCount[3] = 0;
		this.crossCheckStateCount[4] = 0;
		return this.crossCheckStateCount;
	});

	this.foundPatternCross=function( stateCount)
		{
			var totalModuleSize = 0;
			for (var i = 0; i < 5; i++)
			{
				var count = stateCount[i];
				if (count == 0)
				{
					return false;
				}
				totalModuleSize += count;
			}
			if (totalModuleSize < 7)
			{
				return false;
			}
			var moduleSize = Math.floor((totalModuleSize << INTEGER_MATH_SHIFT) / 7);
			var maxVariance = Math.floor(moduleSize / 2);
			// Allow less than 50% variance from 1-1-3-1-1 proportions
			return Math.abs(moduleSize - (stateCount[0] << INTEGER_MATH_SHIFT)) < maxVariance && Math.abs(moduleSize - (stateCount[1] << INTEGER_MATH_SHIFT)) < maxVariance && Math.abs(3 * moduleSize - (stateCount[2] << INTEGER_MATH_SHIFT)) < 3 * maxVariance && Math.abs(moduleSize - (stateCount[3] << INTEGER_MATH_SHIFT)) < maxVariance && Math.abs(moduleSize - (stateCount[4] << INTEGER_MATH_SHIFT)) < maxVariance;
		}
	this.centerFromEnd=function( stateCount,  end)
		{
			return  (end - stateCount[4] - stateCount[3]) - stateCount[2] / 2.0;
		}
	this.crossCheckVertical=function( startI,  centerJ,  maxCount,  originalStateCountTotal)
		{
			var image = this.image;

			var maxI = qrcode.height;
			var stateCount = this.CrossCheckStateCount;

			// Start counting up from center
			var i = startI;
			while (i >= 0 && image[centerJ + i*qrcode.width])
			{
				stateCount[2]++;
				i--;
			}
			if (i < 0)
			{
				return NaN;
			}
			while (i >= 0 && !image[centerJ +i*qrcode.width] && stateCount[1] <= maxCount)
			{
				stateCount[1]++;
				i--;
			}
			// If already too many modules in this state or ran off the edge:
			if (i < 0 || stateCount[1] > maxCount)
			{
				return NaN;
			}
			while (i >= 0 && image[centerJ + i*qrcode.width] && stateCount[0] <= maxCount)
			{
				stateCount[0]++;
				i--;
			}
			if (stateCount[0] > maxCount)
			{
				return NaN;
			}

			// Now also count down from center
			i = startI + 1;
			while (i < maxI && image[centerJ +i*qrcode.width])
			{
				stateCount[2]++;
				i++;
			}
			if (i == maxI)
			{
				return NaN;
			}
			while (i < maxI && !image[centerJ + i*qrcode.width] && stateCount[3] < maxCount)
			{
				stateCount[3]++;
				i++;
			}
			if (i == maxI || stateCount[3] >= maxCount)
			{
				return NaN;
			}
			while (i < maxI && image[centerJ + i*qrcode.width] && stateCount[4] < maxCount)
			{
				stateCount[4]++;
				i++;
			}
			if (stateCount[4] >= maxCount)
			{
				return NaN;
			}

			// If we found a finder-pattern-like section, but its size is more than 40% different than
			// the original, assume it's a false positive
			var stateCountTotal = stateCount[0] + stateCount[1] + stateCount[2] + stateCount[3] + stateCount[4];
			if (5 * Math.abs(stateCountTotal - originalStateCountTotal) >= 2 * originalStateCountTotal)
			{
				return NaN;
			}

			return this.foundPatternCross(stateCount)?this.centerFromEnd(stateCount, i):NaN;
		}
	this.crossCheckHorizontal=function( startJ,  centerI,  maxCount, originalStateCountTotal)
		{
			var image = this.image;

			var maxJ = qrcode.width;
			var stateCount = this.CrossCheckStateCount;

			var j = startJ;
			while (j >= 0 && image[j+ centerI*qrcode.width])
			{
				stateCount[2]++;
				j--;
			}
			if (j < 0)
			{
				return NaN;
			}
			while (j >= 0 && !image[j+ centerI*qrcode.width] && stateCount[1] <= maxCount)
			{
				stateCount[1]++;
				j--;
			}
			if (j < 0 || stateCount[1] > maxCount)
			{
				return NaN;
			}
			while (j >= 0 && image[j+ centerI*qrcode.width] && stateCount[0] <= maxCount)
			{
				stateCount[0]++;
				j--;
			}
			if (stateCount[0] > maxCount)
			{
				return NaN;
			}

			j = startJ + 1;
			while (j < maxJ && image[j+ centerI*qrcode.width])
			{
				stateCount[2]++;
				j++;
			}
			if (j == maxJ)
			{
				return NaN;
			}
			while (j < maxJ && !image[j+ centerI*qrcode.width] && stateCount[3] < maxCount)
			{
				stateCount[3]++;
				j++;
			}
			if (j == maxJ || stateCount[3] >= maxCount)
			{
				return NaN;
			}
			while (j < maxJ && image[j+ centerI*qrcode.width] && stateCount[4] < maxCount)
			{
				stateCount[4]++;
				j++;
			}
			if (stateCount[4] >= maxCount)
			{
				return NaN;
			}

			// If we found a finder-pattern-like section, but its size is significantly different than
			// the original, assume it's a false positive
			var stateCountTotal = stateCount[0] + stateCount[1] + stateCount[2] + stateCount[3] + stateCount[4];
			if (5 * Math.abs(stateCountTotal - originalStateCountTotal) >= originalStateCountTotal)
			{
				return NaN;
			}

			return this.foundPatternCross(stateCount)?this.centerFromEnd(stateCount, j):NaN;
		}
	this.handlePossibleCenter=function( stateCount,  i,  j)
		{
			var stateCountTotal = stateCount[0] + stateCount[1] + stateCount[2] + stateCount[3] + stateCount[4];
			var centerJ = this.centerFromEnd(stateCount, j); //float
			var centerI = this.crossCheckVertical(i, Math.floor( centerJ), stateCount[2], stateCountTotal); //float
			if (!isNaN(centerI))
			{
				// Re-cross check
				centerJ = this.crossCheckHorizontal(Math.floor( centerJ), Math.floor( centerI), stateCount[2], stateCountTotal);
				if (!isNaN(centerJ))
				{
					var estimatedModuleSize =   stateCountTotal / 7.0;
					var found = false;
					var max = this.possibleCenters.length;
					for (var index = 0; index < max; index++)
					{
						var center = this.possibleCenters[index];
						// Look for about the same center and module size:
						if (center.aboutEquals(estimatedModuleSize, centerI, centerJ))
						{
							center.incrementCount();
							found = true;
							break;
						}
					}
					if (!found)
					{
						var point = new FinderPattern(centerJ, centerI, estimatedModuleSize);
						this.possibleCenters.push(point);
						if (this.resultPointCallback != null)
						{
							this.resultPointCallback.foundPossibleResultPoint(point);
						}
					}
					return true;
				}
			}
			return false;
		}

	this.selectBestPatterns=function()
		{

			var startSize = this.possibleCenters.length;
			if (startSize < 3)
			{
				// Couldn't find enough finder patterns
				throw "Couldn't find enough finder patterns";
			}

			// Filter outlier possibilities whose module size is too different
			if (startSize > 3)
			{
				// But we can only afford to do so if we have at least 4 possibilities to choose from
				var totalModuleSize = 0.0;
                var square = 0.0;
				for (var i = 0; i < startSize; i++)
				{
					//totalModuleSize +=  this.possibleCenters[i].EstimatedModuleSize;
                    var	centerValue=this.possibleCenters[i].EstimatedModuleSize;
					totalModuleSize += centerValue;
					square += (centerValue * centerValue);
				}
				var average = totalModuleSize /  startSize;
                this.possibleCenters.sort(function(center1,center2) {
				      var dA=Math.abs(center2.EstimatedModuleSize - average);
				      var dB=Math.abs(center1.EstimatedModuleSize - average);
				      if (dA < dB) {
				    	  return (-1);
				      } else if (dA == dB) {
				    	  return 0;
				      } else {
				    	  return 1;
				      }
					});

				var stdDev = Math.sqrt(square / startSize - average * average);
				var limit = Math.max(0.2 * average, stdDev);
				for (var i = 0; i < this.possibleCenters.length && this.possibleCenters.length > 3; i++)
				{
					var pattern =  this.possibleCenters[i];
					//if (Math.abs(pattern.EstimatedModuleSize - average) > 0.2 * average)
                    if (Math.abs(pattern.EstimatedModuleSize - average) > limit)
					{
						this.possibleCenters.remove(i);
						i--;
					}
				}
			}

			if (this.possibleCenters.length > 3)
			{
				// Throw away all but those first size candidate points we found.
				this.possibleCenters.sort(function(a, b){
					if (a.count > b.count){return -1;}
					if (a.count < b.count){return 1;}
					return 0;
				});
			}

			return new Array( this.possibleCenters[0],  this.possibleCenters[1],  this.possibleCenters[2]);
		}

	this.findRowSkip=function()
		{
			var max = this.possibleCenters.length;
			if (max <= 1)
			{
				return 0;
			}
			var firstConfirmedCenter = null;
			for (var i = 0; i < max; i++)
			{
				var center =  this.possibleCenters[i];
				if (center.Count >= CENTER_QUORUM)
				{
					if (firstConfirmedCenter == null)
					{
						firstConfirmedCenter = center;
					}
					else
					{
						// We have two confirmed centers
						// How far down can we skip before resuming looking for the next
						// pattern? In the worst case, only the difference between the
						// difference in the x / y coordinates of the two centers.
						// This is the case where you find top left last.
						this.hasSkipped = true;
						return Math.floor ((Math.abs(firstConfirmedCenter.X - center.X) - Math.abs(firstConfirmedCenter.Y - center.Y)) / 2);
					}
				}
			}
			return 0;
		}

	this.haveMultiplyConfirmedCenters=function()
		{
			var confirmedCount = 0;
			var totalModuleSize = 0.0;
			var max = this.possibleCenters.length;
			for (var i = 0; i < max; i++)
			{
				var pattern =  this.possibleCenters[i];
				if (pattern.Count >= CENTER_QUORUM)
				{
					confirmedCount++;
					totalModuleSize += pattern.EstimatedModuleSize;
				}
			}
			if (confirmedCount < 3)
			{
				return false;
			}
			// OK, we have at least 3 confirmed centers, but, it's possible that one is a "false positive"
			// and that we need to keep looking. We detect this by asking if the estimated module sizes
			// vary too much. We arbitrarily say that when the total deviation from average exceeds
			// 5% of the total module size estimates, it's too much.
			var average = totalModuleSize / max;
			var totalDeviation = 0.0;
			for (var i = 0; i < max; i++)
			{
				pattern = this.possibleCenters[i];
				totalDeviation += Math.abs(pattern.EstimatedModuleSize - average);
			}
			return totalDeviation <= 0.05 * totalModuleSize;
		}

	this.findFinderPattern = function(image){
		var tryHarder = false;
		this.image=image;
		var maxI = qrcode.height;
		var maxJ = qrcode.width;
		var iSkip = Math.floor((3 * maxI) / (4 * MAX_MODULES));
		if (iSkip < MIN_SKIP || tryHarder)
		{
				iSkip = MIN_SKIP;
		}

		var done = false;
		var stateCount = new Array(5);
		for (var i = iSkip - 1; i < maxI && !done; i += iSkip)
		{
			// Get a row of black/white values
			stateCount[0] = 0;
			stateCount[1] = 0;
			stateCount[2] = 0;
			stateCount[3] = 0;
			stateCount[4] = 0;
			var currentState = 0;
			for (var j = 0; j < maxJ; j++)
			{
				if (image[j+i*qrcode.width] )
				{
					// Black pixel
					if ((currentState & 1) == 1)
					{
						// Counting white pixels
						currentState++;
					}
					stateCount[currentState]++;
				}
				else
				{
					// White pixel
					if ((currentState & 1) == 0)
					{
						// Counting black pixels
						if (currentState == 4)
						{
							// A winner?
							if (this.foundPatternCross(stateCount))
							{
								// Yes
								var confirmed = this.handlePossibleCenter(stateCount, i, j);
								if (confirmed)
								{
									// Start examining every other line. Checking each line turned out to be too
									// expensive and didn't improve performance.
									iSkip = 2;
									if (this.hasSkipped)
									{
										done = this.haveMultiplyConfirmedCenters();
									}
									else
									{
										var rowSkip = this.findRowSkip();
										if (rowSkip > stateCount[2])
										{
											// Skip rows between row of lower confirmed center
											// and top of presumed third confirmed center
											// but back up a bit to get a full chance of detecting
											// it, entire width of center of finder pattern

											// Skip by rowSkip, but back off by stateCount[2] (size of last center
											// of pattern we saw) to be conservative, and also back off by iSkip which
											// is about to be re-added
											i += rowSkip - stateCount[2] - iSkip;
											j = maxJ - 1;
										}
									}
								}
								else
								{
									// Advance to next black pixel
									do
									{
										j++;
									}
									while (j < maxJ && !image[j + i*qrcode.width]);
									j--; // back up to that last white pixel
								}
								// Clear state to start looking again
								currentState = 0;
								stateCount[0] = 0;
								stateCount[1] = 0;
								stateCount[2] = 0;
								stateCount[3] = 0;
								stateCount[4] = 0;
							}
							else
							{
								// No, shift counts back by two
								stateCount[0] = stateCount[2];
								stateCount[1] = stateCount[3];
								stateCount[2] = stateCount[4];
								stateCount[3] = 1;
								stateCount[4] = 0;
								currentState = 3;
							}
						}
						else
						{
							stateCount[++currentState]++;
						}
					}
					else
					{
						// Counting white pixels
						stateCount[currentState]++;
					}
				}
			}
			if (this.foundPatternCross(stateCount))
			{
				var confirmed = this.handlePossibleCenter(stateCount, i, maxJ);
				if (confirmed)
				{
					iSkip = stateCount[0];
					if (this.hasSkipped)
					{
						// Found a third one
						done = haveMultiplyConfirmedCenters();
					}
				}
			}
		}

		var patternInfo = this.selectBestPatterns();
		qrcode.orderBestPatterns(patternInfo);

		return new FinderPatternInfo(patternInfo);
	};
}

function AlignmentPattern(posX, posY,  estimatedModuleSize)
{
	this.x=posX;
	this.y=posY;
	this.count = 1;
	this.estimatedModuleSize = estimatedModuleSize;

	this.__defineGetter__("EstimatedModuleSize", function()
	{
		return this.estimatedModuleSize;
	});
	this.__defineGetter__("Count", function()
	{
		return this.count;
	});
	this.__defineGetter__("X", function()
	{
		return Math.floor(this.x);
	});
	this.__defineGetter__("Y", function()
	{
		return Math.floor(this.y);
	});
	this.incrementCount = function()
	{
		this.count++;
	}
	this.aboutEquals=function( moduleSize,  i,  j)
		{
			if (Math.abs(i - this.y) <= moduleSize && Math.abs(j - this.x) <= moduleSize)
			{
				var moduleSizeDiff = Math.abs(moduleSize - this.estimatedModuleSize);
				return moduleSizeDiff <= 1.0 || moduleSizeDiff / this.estimatedModuleSize <= 1.0;
			}
			return false;
		}

}

function AlignmentPatternFinder( image,  startX,  startY,  width,  height,  moduleSize,  resultPointCallback)
{
	this.image = image;
	this.possibleCenters = new Array();
	this.startX = startX;
	this.startY = startY;
	this.width = width;
	this.height = height;
	this.moduleSize = moduleSize;
	this.crossCheckStateCount = new Array(0,0,0);
	this.resultPointCallback = resultPointCallback;

	this.centerFromEnd=function(stateCount,  end)
		{
			return  (end - stateCount[2]) - stateCount[1] / 2.0;
		}
	this.foundPatternCross = function(stateCount)
		{
			var moduleSize = this.moduleSize;
			var maxVariance = moduleSize / 2.0;
			for (var i = 0; i < 3; i++)
			{
				if (Math.abs(moduleSize - stateCount[i]) >= maxVariance)
				{
					return false;
				}
			}
			return true;
		}

	this.crossCheckVertical=function( startI,  centerJ,  maxCount,  originalStateCountTotal)
		{
			var image = this.image;

			var maxI = qrcode.height;
			var stateCount = this.crossCheckStateCount;
			stateCount[0] = 0;
			stateCount[1] = 0;
			stateCount[2] = 0;

			// Start counting up from center
			var i = startI;
			while (i >= 0 && image[centerJ + i*qrcode.width] && stateCount[1] <= maxCount)
			{
				stateCount[1]++;
				i--;
			}
			// If already too many modules in this state or ran off the edge:
			if (i < 0 || stateCount[1] > maxCount)
			{
				return NaN;
			}
			while (i >= 0 && !image[centerJ + i*qrcode.width] && stateCount[0] <= maxCount)
			{
				stateCount[0]++;
				i--;
			}
			if (stateCount[0] > maxCount)
			{
				return NaN;
			}

			// Now also count down from center
			i = startI + 1;
			while (i < maxI && image[centerJ + i*qrcode.width] && stateCount[1] <= maxCount)
			{
				stateCount[1]++;
				i++;
			}
			if (i == maxI || stateCount[1] > maxCount)
			{
				return NaN;
			}
			while (i < maxI && !image[centerJ + i*qrcode.width] && stateCount[2] <= maxCount)
			{
				stateCount[2]++;
				i++;
			}
			if (stateCount[2] > maxCount)
			{
				return NaN;
			}

			var stateCountTotal = stateCount[0] + stateCount[1] + stateCount[2];
			if (5 * Math.abs(stateCountTotal - originalStateCountTotal) >= 2 * originalStateCountTotal)
			{
				return NaN;
			}

			return this.foundPatternCross(stateCount)?this.centerFromEnd(stateCount, i):NaN;
		}

	this.handlePossibleCenter=function( stateCount,  i,  j)
		{
			var stateCountTotal = stateCount[0] + stateCount[1] + stateCount[2];
			var centerJ = this.centerFromEnd(stateCount, j);
			var centerI = this.crossCheckVertical(i, Math.floor (centerJ), 2 * stateCount[1], stateCountTotal);
			if (!isNaN(centerI))
			{
				var estimatedModuleSize = (stateCount[0] + stateCount[1] + stateCount[2]) / 3.0;
				var max = this.possibleCenters.length;
				for (var index = 0; index < max; index++)
				{
					var center =  this.possibleCenters[index];
					// Look for about the same center and module size:
					if (center.aboutEquals(estimatedModuleSize, centerI, centerJ))
					{
						return new AlignmentPattern(centerJ, centerI, estimatedModuleSize);
					}
				}
				// Hadn't found this before; save it
				var point = new AlignmentPattern(centerJ, centerI, estimatedModuleSize);
				this.possibleCenters.push(point);
				if (this.resultPointCallback != null)
				{
					this.resultPointCallback.foundPossibleResultPoint(point);
				}
			}
			return null;
		}

	this.find = function()
	{
			var startX = this.startX;
			var height = this.height;
			var maxJ = startX + width;
			var middleI = startY + (height >> 1);
			// We are looking for black/white/black modules in 1:1:1 ratio;
			// this tracks the number of black/white/black modules seen so far
			var stateCount = new Array(0,0,0);
			for (var iGen = 0; iGen < height; iGen++)
			{
				// Search from middle outwards
				var i = middleI + ((iGen & 0x01) == 0?((iGen + 1) >> 1):- ((iGen + 1) >> 1));
				stateCount[0] = 0;
				stateCount[1] = 0;
				stateCount[2] = 0;
				var j = startX;
				// Burn off leading white pixels before anything else; if we start in the middle of
				// a white run, it doesn't make sense to count its length, since we don't know if the
				// white run continued to the left of the start point
				while (j < maxJ && !image[j + qrcode.width* i])
				{
					j++;
				}
				var currentState = 0;
				while (j < maxJ)
				{
					if (image[j + i*qrcode.width])
					{
						// Black pixel
						if (currentState == 1)
						{
							// Counting black pixels
							stateCount[currentState]++;
						}
						else
						{
							// Counting white pixels
							if (currentState == 2)
							{
								// A winner?
								if (this.foundPatternCross(stateCount))
								{
									// Yes
									var confirmed = this.handlePossibleCenter(stateCount, i, j);
									if (confirmed != null)
									{
										return confirmed;
									}
								}
								stateCount[0] = stateCount[2];
								stateCount[1] = 1;
								stateCount[2] = 0;
								currentState = 1;
							}
							else
							{
								stateCount[++currentState]++;
							}
						}
					}
					else
					{
						// White pixel
						if (currentState == 1)
						{
							// Counting black pixels
							currentState++;
						}
						stateCount[currentState]++;
					}
					j++;
				}
				if (this.foundPatternCross(stateCount))
				{
					var confirmed = this.handlePossibleCenter(stateCount, i, maxJ);
					if (confirmed != null)
					{
						return confirmed;
					}
				}
			}

			// Hmm, nothing we saw was observed and confirmed twice. If we had
			// any guess at all, return it.
			if (!(this.possibleCenters.length == 0))
			{
				return  this.possibleCenters[0];
			}

			throw "Couldn't find enough alignment patterns";
		}

}

function QRCodeDataBlockReader(blocks,  version,  numErrorCorrectionCode)
{
	this.blockPointer = 0;
	this.bitPointer = 7;
	this.dataLength = 0;
	this.blocks = blocks;
	this.numErrorCorrectionCode = numErrorCorrectionCode;
	if (version <= 9)
		this.dataLengthMode = 0;
	else if (version >= 10 && version <= 26)
		this.dataLengthMode = 1;
	else if (version >= 27 && version <= 40)
		this.dataLengthMode = 2;

	this.getNextBits = function( numBits)
		{
			var bits = 0;
			if (numBits < this.bitPointer + 1)
			{
				// next word fits into current data block
				var mask = 0;
				for (var i = 0; i < numBits; i++)
				{
					mask += (1 << i);
				}
				mask <<= (this.bitPointer - numBits + 1);

				bits = (this.blocks[this.blockPointer] & mask) >> (this.bitPointer - numBits + 1);
				this.bitPointer -= numBits;
				return bits;
			}
			else if (numBits < this.bitPointer + 1 + 8)
			{
				// next word crosses 2 data blocks
				var mask1 = 0;
				for (var i = 0; i < this.bitPointer + 1; i++)
				{
					mask1 += (1 << i);
				}
				bits = (this.blocks[this.blockPointer] & mask1) << (numBits - (this.bitPointer + 1));
                this.blockPointer++;
				bits += ((this.blocks[this.blockPointer]) >> (8 - (numBits - (this.bitPointer + 1))));

				this.bitPointer = this.bitPointer - numBits % 8;
				if (this.bitPointer < 0)
				{
					this.bitPointer = 8 + this.bitPointer;
				}
				return bits;
			}
			else if (numBits < this.bitPointer + 1 + 16)
			{
				// next word crosses 3 data blocks
				var mask1 = 0; // mask of first block
				var mask3 = 0; // mask of 3rd block
				//bitPointer + 1 : number of bits of the 1st block
				//8 : number of the 2nd block (note that use already 8bits because next word uses 3 data blocks)
				//numBits - (bitPointer + 1 + 8) : number of bits of the 3rd block
				for (var i = 0; i < this.bitPointer + 1; i++)
				{
					mask1 += (1 << i);
				}
				var bitsFirstBlock = (this.blocks[this.blockPointer] & mask1) << (numBits - (this.bitPointer + 1));
				this.blockPointer++;

				var bitsSecondBlock = this.blocks[this.blockPointer] << (numBits - (this.bitPointer + 1 + 8));
				this.blockPointer++;

				for (var i = 0; i < numBits - (this.bitPointer + 1 + 8); i++)
				{
					mask3 += (1 << i);
				}
				mask3 <<= 8 - (numBits - (this.bitPointer + 1 + 8));
				var bitsThirdBlock = (this.blocks[this.blockPointer] & mask3) >> (8 - (numBits - (this.bitPointer + 1 + 8)));

				bits = bitsFirstBlock + bitsSecondBlock + bitsThirdBlock;
				this.bitPointer = this.bitPointer - (numBits - 8) % 8;
				if (this.bitPointer < 0)
				{
					this.bitPointer = 8 + this.bitPointer;
				}
				return bits;
			}
			else
			{
				return 0;
			}
		}
	this.NextMode=function()
	{
		if ((this.blockPointer > this.blocks.length - this.numErrorCorrectionCode - 2))
			return 0;
		else
			return this.getNextBits(4);
	}
	this.getDataLength=function( modeIndicator)
		{
			var index = 0;
			while (true)
			{
				if ((modeIndicator >> index) == 1)
					break;
				index++;
			}

			return this.getNextBits(qrcode.sizeOfDataLengthInfo[this.dataLengthMode][index]);
		}
	this.getRomanAndFigureString=function( dataLength)
		{
			var length = dataLength;
			var intData = 0;
			var strData = "";
			var tableRomanAndFigure = new Array('0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', ' ', unescape('%24'), '%', '*', '+', '-', '.', '/', ':');
			do
			{
				if (length > 1)
				{
					intData = this.getNextBits(11);
					var firstLetter = Math.floor(intData / 45);
					var secondLetter = intData % 45;
					strData += tableRomanAndFigure[firstLetter];
					strData += tableRomanAndFigure[secondLetter];
					length -= 2;
				}
				else if (length == 1)
				{
					intData = this.getNextBits(6);
					strData += tableRomanAndFigure[intData];
					length -= 1;
				}
			}
			while (length > 0);

			return strData;
		}
	this.getFigureString=function( dataLength)
		{
			var length = dataLength;
			var intData = 0;
			var strData = "";
			do
			{
				if (length >= 3)
				{
					intData = this.getNextBits(10);
					if (intData < 100)
						strData += "0";
					if (intData < 10)
						strData += "0";
					length -= 3;
				}
				else if (length == 2)
				{
					intData = this.getNextBits(7);
					if (intData < 10)
						strData += "0";
					length -= 2;
				}
				else if (length == 1)
				{
					intData = this.getNextBits(4);
					length -= 1;
				}
				strData += intData;
			}
			while (length > 0);

			return strData;
		}
	this.get8bitByteArray=function( dataLength)
		{
			var length = dataLength;
			var intData = 0;
			var output = new Array();

			do
			{
				intData = this.getNextBits(8);
				output.push( intData);
				length--;
			}
			while (length > 0);
			return output;
		}
    this.getKanjiString=function( dataLength)
		{
			var length = dataLength;
			var intData = 0;
			var unicodeString = "";
			do
			{
				intData = getNextBits(13);
				var lowerByte = intData % 0xC0;
				var higherByte = intData / 0xC0;

				var tempWord = (higherByte << 8) + lowerByte;
				var shiftjisWord = 0;
				if (tempWord + 0x8140 <= 0x9FFC)
				{
					// between 8140 - 9FFC on Shift_JIS character set
					shiftjisWord = tempWord + 0x8140;
				}
				else
				{
					// between E040 - EBBF on Shift_JIS character set
					shiftjisWord = tempWord + 0xC140;
				}

				//var tempByte = new Array(0,0);
				//tempByte[0] = (sbyte) (shiftjisWord >> 8);
				//tempByte[1] = (sbyte) (shiftjisWord & 0xFF);
				//unicodeString += new String(SystemUtils.ToCharArray(SystemUtils.ToByteArray(tempByte)));
                unicodeString += String.fromCharCode(shiftjisWord);
				length--;
			}
			while (length > 0);


			return unicodeString;
		}

	this.__defineGetter__("DataByte", function()
	{
		var output = new Array();
		var MODE_NUMBER = 1;
	    var MODE_ROMAN_AND_NUMBER = 2;
	    var MODE_8BIT_BYTE = 4;
	    var MODE_KANJI = 8;
		do
					{
						var mode = this.NextMode();
						//canvas.println("mode: " + mode);
						if (mode == 0)
						{
							if (output.length > 0)
								break;
							else
								throw "Empty data block";
						}
						//if (mode != 1 && mode != 2 && mode != 4 && mode != 8)
						//	break;
						//}
						if (mode != MODE_NUMBER && mode != MODE_ROMAN_AND_NUMBER && mode != MODE_8BIT_BYTE && mode != MODE_KANJI)
						{
							/*					canvas.println("Invalid mode: " + mode);
							mode = guessMode(mode);
							canvas.println("Guessed mode: " + mode); */
							throw "Invalid mode: " + mode + " in (block:" + this.blockPointer + " bit:" + this.bitPointer + ")";
						}
						dataLength = this.getDataLength(mode);
						if (dataLength < 1)
							throw "Invalid data length: " + dataLength;
						//canvas.println("length: " + dataLength);
						switch (mode)
						{

							case MODE_NUMBER:
								//canvas.println("Mode: Figure");
								var temp_str = this.getFigureString(dataLength);
								var ta = new Array(temp_str.length);
								for(var j=0;j<temp_str.length;j++)
									ta[j]=temp_str.charCodeAt(j);
								output.push(ta);
								break;

							case MODE_ROMAN_AND_NUMBER:
								//canvas.println("Mode: Roman&Figure");
								var temp_str = this.getRomanAndFigureString(dataLength);
								var ta = new Array(temp_str.length);
								for(var j=0;j<temp_str.length;j++)
									ta[j]=temp_str.charCodeAt(j);
								output.push(ta );
								//output.Write(SystemUtils.ToByteArray(temp_sbyteArray2), 0, temp_sbyteArray2.Length);
								break;

							case MODE_8BIT_BYTE:
								//canvas.println("Mode: 8bit Byte");
								//sbyte[] temp_sbyteArray3;
								var temp_sbyteArray3 = this.get8bitByteArray(dataLength);
								output.push(temp_sbyteArray3);
								//output.Write(SystemUtils.ToByteArray(temp_sbyteArray3), 0, temp_sbyteArray3.Length);
								break;

							case MODE_KANJI:
								//canvas.println("Mode: Kanji");
								//sbyte[] temp_sbyteArray4;
								//temp_sbyteArray4 = SystemUtils.ToSByteArray(SystemUtils.ToByteArray(getKanjiString(dataLength)));
								//output.Write(SystemUtils.ToByteArray(temp_sbyteArray4), 0, temp_sbyteArray4.Length);
                                var temp_str = this.getKanjiString(dataLength);
								output.push(temp_str);
								break;
							}
						//
						//canvas.println("DataLength: " + dataLength);
						//Console.out.println(dataString);
					}
					while (true);
		return output;
	});
}

function QRCodeScanner(width, height, container, success, error) {
	navigator.getUserMedia = navigator.getUserMedia ||
		navigator.webkitGetUserMedia ||
		navigator.mozGetUserMedia ||
		navigator.msGetUserMedia;
	window.URL = window.URL || window.webkitURL || window.mozURL || window.msURL;

	var _width = width;
	var _height = height;
	var _id_container = container;
	var _id_video = container + '_video';
	var _success = success;
	var _error = error;

	var _container = document.getElementById(container);
	var _video = null;
	var _stream = null;
	var _canvas = null;
	var _ctx = null;
	var _interval = null;

	function isCanvasSupported() {
		var elem = document.createElement('canvas');
		return !!(elem.getContext && elem.getContext('2d'));
	}

	function canvasInit() {
		_canvas = document.createElement('canvas');
		_canvas.width = _width;
		_canvas.height = _height;
		_ctx = _canvas.getContext('2d');
	}

	function captureToCanvas() {
		_ctx.drawImage(_video, 0, 0, _video.videoWidth, _video.videoHeight, 0, 0, _canvas.width, _canvas.height);
		qrcode.decode(_canvas.toDataURL());
	}

	this.isSupported = function() {
		if (!isCanvasSupported()) return false;
		if (!navigator.getUserMedia) return false;
		return true;
	}

	this.start = function() {
		if (_video) return;
		if (navigator.getUserMedia) {
			//Append the video element
			_container.innerHTML = '<video style="width:' + _width + 'px;height:' + _height + 'px" autoplay id="' + _id_video + '"></video>';
			_video = document.getElementById(_id_video);

			navigator.getUserMedia(
				{video: true},
				function(stream) {
					_stream = stream;
					_video.src = window.URL.createObjectURL(stream) || stream;

					setTimeout(function() {
						canvasInit();
						_interval = setInterval(captureToCanvas, 500);
					}, 250); // Needed to get videoWidth/videoHeight
				},
				function(error) {
					_container.innerHTML = '';
					_video = null;
					if (error && error.message)
						_error(error.message);
					else if (error && error.name)
						_error(error.name);
					else
						_error(error);
				});


			qrcode.callback = function(data) {
				if (data && data.indexOf('error') != 0) {
					stop();
					if (data.indexOf('bitcoin:') == 0)
						data = data.substring(8);
					_success(data);
				}
			};
		}else{
			_error('Sorry your browser is not supported. Please try Firefox, Chrome or safari.');
		}
	}

	function stop() {
		if (_interval) {
			clearInterval(_interval);
			_interval = null;
		}

		_container.innerHTML = '';
		_video = null;

		try {
			if (_stream) {
				_stream.stop();
				_stream = null;
			}
		} catch (e) {
			console.log(e);
			_error(e);
		}
	}
	this.stop = stop;
}

  </script>
  <style type="text/css">
.more { background: url("./images/plus.png") no-repeat left center; width: 17px; height: 17px; display: inline-block; float: right; }
.less { background: url("./images/minus.png") no-repeat left center; width: 17px; height: 17px; display: inline-block; float: right; }
a { position: relative; z-index: 20; text-decoration: none; color: #d58424; }
.right { text-align: right; }
.walletarea { display: none; border: 1px solid #BFBFBF; background-color: white; }
hr { margin: 20px 0; border-top: 1px dashed #008000; }
.keyarea { height: 110px; text-align: left; position: relative; padding: 25px 25px 10px; }
.keyarea .public { float: left; }
.keyarea .pubaddress { display: inline-block; height: 40px; padding: 0 0 0 10px; float: left; }
.keyarea .privwif { margin: 0;  float: right; text-align: right; padding: 0 20px 0 0; position: relative; }
.keyarea .label { font-weight: bold; }
.keyarea .output { display: block; font-family: monospace; font-size: 1.25em; }
.keyarea .qrcode_public { display: inline-block; float: left; }
.keyarea .qrcode_private { display: inline-block; position: relative; top: 28px; float: right; }
.pubkeyhex { word-wrap: break-word; }
html { height: 100%; }
body { font-family: Arial; background-image: url('images/diamonds.png'); height: 100%; }
.faqs ol { padding: 0 0 0 25px; }
.faqs li { padding: 3px 0; }
.question { padding: 10px 15px; text-align: left; cursor: pointer; }
.question:hover, .expandable:hover { color: #77777A; }
.answer { padding: 0 15px 10px 25px; text-align: left; display: none; font-size: 80%; }
.faq { border: 0; border-top: 1px solid #BFBFBF; }

#initBanner { position: relative; text-align: left; padding: 15px; background-color: white; border-bottom: 1px solid #bfbfbf; }
#walletCommands { display: none; }
#keyarea { display: none; }

#faqZone { text-align: left; padding: 10px 30px 30px 30px; }
.faqQuestion { margin-left: 15px; }
.faqAnswer { margin-left: 30px; display: none; }
.faqListBullet { padding: 0 5px 0px 15px; }
.faqLink { cursor: pointer; }

#btcaddress, #btcprivwif, #detailaddress, #detailaddresscomp, #detailprivwif, #detailprivwifcomp { font-family: monospace; font-size: 1.25em; }
#seedpoolarea { display: none; }
#seedpooldisplay {  font-family: monospace; font-size: 1em; width: 640px; padding: 15px 5px; word-wrap: break-word; min-height: 98px; }
.seedpoint { width: 6px; height: 6px; display: block; border-radius: 3px; background-color: #80CF80; position: absolute; z-index: 10; }

#seedSkipper { font-size: 11px; text-align: center; line-height: 14px;}
#skipMessage { margin-top: 8px; }

#generate #mousemovelimit { font-size: 16px; color: #FFF; }
#rightArea { position: absolute; right: 66px; top: 62px; width: 170px; }
#progress-bar { width: 170px; background:#80CF80; position: relative; margin-bottom: 20px;
  -webkit-border-radius: 5px; -moz-border-radius: 5px; border-radius: 5px;
 }
#progress-bar-percentage { background:#FFA247; padding: 3px 0px; text-align: center; height: 18px;
  -webkit-border-radius: 5px; -moz-border-radius: 5px; border-radius: 5px;
 }
#progress-bar-percentage span { display: inline-block; position: absolute; width: 100%; left: 0; }
.nicerButton { font-size: 14px; }

#generate { font-size: 13px; text-align: left; position: relative; padding: 20px; border: 1px solid #BFBFBF; background-color: white; -webkit-border-radius: 10px; -moz-border-radius: 10px; border-radius: 10px; }
#generate span { padding: 5px 5px 0 5px; }
#generatekeyinput { position: relative; z-index: 20; }

#generatelabelbitcoinaddress, #generatelabelmovemouse, #generatelabelkeypress { font-size: 14px; font-family: monospace; }
#mousemovelimit { font-size: 16px; font-family: monospace; }
.frontPageText { position: relative; }

h1 { margin: 0px; height: 91px; }

#keyarea { height: 250px; border-bottom: 1px solid #bfbfbf; }
#keyarea .pubaddress { float: none; display: block; padding: 0; height: auto; }
#keyarea .label { text-decoration: none; }
#keyarea .privwif { float: none; text-align: right; position: relative; padding: 0; }
#keyarea .qrcode_public { float: none; display: block; padding: 13px 11px 11px 11px; }
#keyarea .qrcode_private { float: none; display: block; top: 0; text-align: right; padding: 13px 11px 11px 11px; }
#keyarea .private { width: 30%; display: table-cell; }
#keyarea .public { width: 30%; display: table-cell; }
#singlearea { font-size: 90%; display: block; }
#singlesecret { position: relative; top: -130px; float: right; right: 200px; color: red; font-weight: bolder; font-size: 200%;  }
#singleshare { position: relative; top: -110px; float: left; left: 160px; color: green; font-weight: bolder; font-size: 200%;  }
#singlesafety { text-align: left; border-bottom: 1px solid #bfbfbf; position: relative; min-height: 500px; }

#singlesafety p { font-size: 13px; }

.firstHalfSingleSafety, .secondHalfSingleSafety {
  -webkit-box-sizing: border-box; /* Safari/Chrome, other WebKit */
  -moz-box-sizing: border-box;    /* Firefox, other Gecko */
  box-sizing: border-box;         /* Opera/IE 8+ */
  display: inline-block;
    vertical-align: top;
    position: relative;
}

.firstHalfSingleSafety { width: 50%; padding: 10px 30px 10px 30px; }

 .secondHalfSingleSafety {
    width: 50%;
    padding: 20px;
    text-align: center;
    float: right;
 }

.frontPageImage, .seedFrontPageImage {
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  border-radius: 10px;
}
.frontPageImage { width: 100%; }
.seedFrontPageImage { max-height: 340px; }

.currencyNameColumn { min-width: 120px; }

.securityChecklist {
  background-color: #FFE6C9;
  margin-top: 15px;
    padding: 20px;
    -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  border-radius: 10px;
  text-align: left;
}

.frontPageInstructions {
  padding: 5px;
}

.securityChecklist ul { padding: 5px 0 0 20px; }
.securityChecklist li { padding-left: 5px; margin-bottom: 10px; }

.redText { color: red; }
.greenText { color: green; }

#coinLogo { width: 55px; height: 55px; padding: 10px; position: absolute; top: 2px; left: 10px; }
#coinLogoImg { width: 100%; height: 100%; }

.coinIcoin { width: 64px; height: 64px; padding: 10px; position: absolute; top: 272px; left: 48px; }
#coinImg { width: 100%; height: 100%; }

#main { position: relative; text-align: center; margin: 0px auto; width: 1005px; }
#logo { width: 578px; height: 80px; }

.backLogo { float: right; width: 50px; height: 50px; }

#paperarea { min-height: 120px; display: none; }
#paperarea .keyarea { border: 1px solid #BFBFBF; border-top: 0; }
#paperarea .keyarea.art { display: block; height: auto; border: 0; font-family: Ubuntu, Arial; padding: 0; margin: 0; }
#paperarea .artwallet .papersvg { width: 1004px; height: 426px; border: 0; margin: 0; padding: 0; left: 0; }
#paperarea .artwallet .qrcode_public { top: 263px; left: 780px; z-index: 100; margin: 0; float: none; display: block; position: absolute; background-color: #FFFFFF;
                                    padding: 5px 5px 2px 5px; }
#paperarea .artwallet .qrcode_private { top: 37px; right: 446px; z-index: 100; margin: 0; float: none; display: block; position: absolute; background-color: #FFFFFF;
                                    padding: 5px 5px 2px 5px;
                                    -ms-transform: rotate(180deg); /* IE 9 */
                    -webkit-transform: rotate(180deg); /* Chrome, Safari, Opera */
                    transform: rotate(180deg);
                                     }
.supportWalletGenerator { margin: 30px; }
.errorMsg { color: red; text-align: center !important; width: 100% !important; padding-top: 15px !important; }
#paperarea .artwallet .btcaddress
{
  position: absolute; bottom: 88px; left: 874px; z-index: 100; font-size: 11px; background-color: transparent;
  font-weight: 100; color: #000000; margin: 0; width: 124px; height: 32px; text-align: center;
    word-wrap: break-word; font-family: Courier, monospace;
    -ms-transform: rotate(-90deg); /* IE 9 */
  -webkit-transform: rotate(-90deg); /* Chrome, Safari, Opera */
  transform: rotate(-90deg); font-family: Courier, monospace;
}
#paperarea .artwallet .btcprivwif
{
  position: absolute; top: 86px; right: 540px; z-index: 100; font-size: 11px; background-color: transparent;
  font-weight: 100; color: #000000; margin: 0; width: 124px; height: 32px; text-align: center;
    word-wrap: break-word;
  -ms-transform: rotate(90deg); /* IE 9 */
  -webkit-transform: rotate(90deg); /* Chrome, Safari, Opera */
  transform: rotate(90deg);
    font-family: Courier, monospace;
}
#paperarea .artwallet .btcencryptedkey
{
  position: absolute; top: 86px; right: 540px; z-index: 100; font-size: 11px; background-color: transparent;
  font-weight:100; color: #000000; margin: 0; width: 110px; height: 32px; text-align: center;
  word-wrap: break-word;
  -ms-transform: rotate(90deg); /* IE 9 */
  -webkit-transform: rotate(90deg); /* Chrome, Safari, Opera */
  transform: rotate(90deg);
  font-family: Courier, monospace;
}
#suppliedPrivateKey { width: 420px; }
#papergenerate { margin-left: 10px; margin-right: 0px;}

.displayNone { displa: none; }
.redColor { color: red; }
.1percentwidth { width: 1%; }
.100pxwidth { width: 100px; }

.paperWalletText { bottom: 8px; height: 156px; left: 339px; padding: 15px 15px 17px 37px; position: absolute; width: 265px; font-size: 10px; color: #383838; line-height: 15px; }
.paperWalletText ul { margin: 0px; padding: 0px; }
.paperWalletText li { line-height: 13px; margin-bottom: 5px; }

.qrzone { margin: 20px 0px 20px 20px; }
#detaillabelenterprivatekey { margin-right: 15px; }

.qrcodeinputwrapper { position: relative; margin-right: 15px; }
.qrcodeinputwrapper img { position: absolute; display: block; top: 3px; right: 20px; width: 16px; height: 16px;background: url("./images/qrcode.png"); cursor: pointer; padding: 0; }

#bulkarea .body { padding: 5px 0 0 0; }
#bulkarea .format { font-style: italic; font-size: 90%; }
#bulktextarea { font-size: 90%; width: 98%; margin: 4px 0 0 0; }
#brainarea .keyarea { visibility: hidden; min-height: 110px; }
#detailarea span.qrinput { position: relative; padding: 10px; }
#detailarea span.qrinput #detailprivkey { width: 420px; height: 20px; }
#detailkeyarea { padding: 10px; }
#detailarea { margin: 0; text-align: left; }
#detailarea .notes { text-align: left; font-size: 80%; padding: 0 0 20px 0; }
#detailarea .pubqr .item .label { text-decoration: none; }
#detailarea .pubqr .item { float: left; margin: 10px 0; position: relative; }
#detailarea .pubqr .item.right { float: right; position: relative; top: 0; }
#detailarea .privqr .item .label { text-decoration: none; }
#detailarea .privqr .item { float: left; margin: 0; position: relative; }
#detailarea .privqr .item.right { float: right; position: relative; }
#detailarea .item { margin: 10px 0; position: relative; font-size: 90%; padding: 1px 0; }
#detailarea .item.clear { clear: both; padding-top: 10px; }
#detailarea .label { display: block; font-weight: bold; }
#detailarea .output { display: block; font-family: monospace; font-size: 1.25em; }
#detailarea #detailqrcodepublic { position: relative; float: left; margin: 0 10px 0 0; padding: 13px 11px 11px 11px; }
#detailarea #detailqrcodepubliccomp { position: relative; float: right; margin: 0 0 0 10px; padding: 13px 11px 11px 11px; }
#detailarea #detailqrcodeprivate { position: relative; float: left; margin: 0 10px 0 0; padding: 13px 11px 11px 11px; }
#detailarea #detailqrcodeprivatecomp { position: relative; float: right; margin: 0 0 0 10px; padding: 13px 11px 11px 11px; }
#detailpubkey { width: 590px; }
#detailbip38commands { display: none; padding-top: 5px; }
#paperqrscanner { position: absolute; display: none; width: 100%; height: 100%; top: 0; left: 0; z-index: 5000; vertical-aligh: middle; }
#paperqrscanner.show { display: block; }

.englishjson { text-align: center; padding: 40px 0 20px 0; }
.unittests { text-align: center; }
.unittests div { width: 894px; font-family: monospace; text-align: left; margin: auto; padding: 5px; border: 1px solid black; }
#testnet { display: none; background-color: Orange; color: #000000; border-radius: 5px; font-weight: bold; padding: 10px 0; margin: 0 auto 20px auto; }
#busyblock { position: fixed; display: none; background: url("./images/busy.gif") #ccc no-repeat center; opacity: 0.4; width: 100%; height: 100%; top: 0; left: 0; z-index: 5000; }
#busyblock.busy { display: block; }
.hide { display: none; }
.show { display: block; }
.dialog { z-index: 6000; position: relative; background: white; border: 2px solid #f7931a; width: 600px; margin: 150px auto; padding: 1em; }
.dialog-narrow { width: 300px; }

#currencyddl { margin: 20px; position: absolute; right: 0; top: 60px; width: 320px; font-size: 14px; }
#currencyddl select { font-size: 14px; }

.banner { font-size: 46px; text-shadow: 1px 1px 3px #000; color: #FF9547; text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8); text-align: left; position: relative; }

/* IE8 */
.qrcodetable { border-width: 0px; border-style: none; border-color: #0000ff; border-collapse: collapse; }
.qrcodetddark { border-width: 0px; border-style: none; border-color: #0000ff; border-collapse: collapse; padding: 0; margin: 0; width: 2px; height: 2px; background-color: #000000; }
.qrcodetdlight { border-width: 0px; border-style: none; border-color: #0000ff; border-collapse: collapse; padding: 0; margin: 0; width: 2px; height: 2px; background-color: #ffffff; }

@media screen
{
  #tagline { margin: 0 0 15px 0; font-style: italic; text-align: left; }
  .menu { text-align: left; }
  .menu .tab { border-top-left-radius: 5px; border-top-right-radius: 5px; display: inline-block; background-color: #F5F5F5;
          border: 1px solid #BFBFBF; padding: 5px; margin: 0 2px 0 0; position: relative; top: 1px; z-index: 110; cursor: pointer; }
  .menu .tab:hover { color: #d58424; }
  .menu .tab.selected { background-color: #FFF; border-bottom: 1px solid #FFF; cursor: default; }
  .menu .tab.selected:hover { color: #000; }
  .pagebreak { height: 50px; }
  .commands { border-bottom: 1px solid #BFBFBF;  padding: 10px 2px; margin-bottom: 0; background-color: white; }
  .commands .row { padding: 0 0; text-align: left; }
  .commands .row.extra { padding-top: 6px; }
  .commands span { padding: 0 10px; }
  .commands span.print { float: right; }
  .commands span.right { float: right; }
  .expandable { padding: 10px 15px; text-align: left; cursor: pointer; }

    #menu { visibility: hidden; font-size: 90%; }
    #culturemenu { text-align: right; padding: 0 20px; font-size: 14px; margin-bottom: 35px; }
    #culturemenu span { padding: 3px; }
    #culturemenu .selected { text-decoration: none; color: #000000; }

  #braincommands .row .label { width: 200px; display: inline-block; }
  #braincommands .notes { font-size: 80%; display: block; padding: 5px 10px; }
  #brainpassphrase { width: 280px; }
  #brainpassphraseconfirm { width: 280px; }
  #brainwarning {  }
  #detailcommands { padding: 10px 0; }
  #detailcommands span { padding: 0 10px; }
  #detailprivkey { width: 300px; }
  #detailprivkeypassphrase { width: 250px; }
  .paper .commands { border: 1px solid #BFBFBF; }
  #bulkstartindex { width: 35px; }
  #bulklimit { width: 45px; }

  .footer { font-size: 90%; clear: both; width: 750px; padding: 10px 0 10px 0; margin: 50px auto auto auto; }
  .footer div span.item { margin: 10px; }
  .footer .authorbtc { float: left; width: 470px; }
  .footer .authorbtc span.item { text-align: left; display: block; padding: 0 20px; }
  .footer .authorbtc div { position: relative; z-index: 100; }
  .footer .authorpgp { position: relative; }
  .footer .authorpgp span.item { text-align: right; display: block; padding: 0 20px; }
  .footer .copyright { font-size: 80%; clear: both; padding: 5px 0; }
  .footer .copyright span { padding: 10px 2px; }
}
@media print
{
  body { -webkit-print-color-adjust: exact; width: 1000px; height: 450px;}
  #main { width: auto; }
  #singlearea { border: 0; }
  #singlesafety { border: 0; }
  #paperarea .keyarea:first-child { border-top: 1px solid #BFBFBF; }
  #paperarea .keyarea.art:first-child { border: 0; }
  .pagebreak { height: 1px; }
  .paper #logo { display: none; }
  .menu, .footer, .commands, #tagline, #faqs, #culturemenu { display: none; }
  #detailprivwif { width: 285px; word-wrap: break-word; }
  #detailprivwifcomp { width: 310px; word-wrap: break-word; text-align: right; }
  #detailarea .privqr .item.right { width: 310px; }
  #detailarea .privqr .item { width: 285px; }
  #detailarea .notes { display: none; }
  #seedpoolarea { display: none; }
  .faq { display: none; }
  .banner { display: none; }
  #currency { display: none; }
  #paperarea .artwallet .btcaddress, #paperarea .artwallet .btcprivwif { z-index: 999; }
  .paperWalletText { z-index: 999;}
    .dogeTag { display: none; }
}
#siteTitle {
	background: url("images/banner.png");
	float: right;
	background-position: -86px 0;
  width: 436px;
  height: 80px;
}
.banner {
	height: 100px;
}
#coinLogo {
	width: 305px;
}
#coinLogo img,
#coinLogo span {
	display: inline-block;
}
#coinLogo img {
	width: 55px;
}
#coinLogo span {
	position: relative;
	top: -12px;
}
#decryptseedarea .label {
	text-align: left;
	font-weight: bold;
	display: block;
	margin-bottom: 15px;
}
.decryptseedarea-inner {
	text-align: left;
	width: 80%;
	margin: 0 auto;
	padding: 35px 0;
}
#encryptedseed {
	margin-bottom: 20px;
}
#encryptedseedkey {
	width: 600px;
	padding: 5px;
}
#encryptedseeddecrypt {
	margin-left: 10px;
}
.decryptseedarea-inner-res {
	display: none;
	margin-top: 30px;
}
.decryptseedarea-inner-res-msg {
	padding-top: 5px;
}
.generateseedarea-inner {
	margin: 30px 20px 45px 20px;
}
#generateseedarea .label {
	font-weight: bold;
	display: block;
	margin-bottom: 15px;
}
.generateseedarea-seed-type {
	padding: 20px;
}
.generateseedarea-seed-type label {
	margin-right: 40px;
}
.generateseedarea-seed-type label,
.generateseedarea-seed-type input {
	cursor: pointer;
}
.generateseedarea-seed {
	width: 600px;
	text-align: justify;
	line-height: 24px;
	margin: 0 auto;
	border: dashed 1px;
	padding: 15px;
}
.generateseedarea-inner .label-padding {
	padding: 20px;
}
#getprivkeysarea .label,
#getbip39keysarea .label {
	text-align: left;
	font-weight: bold;
	display: block;
	margin-bottom: 15px;
}
.getprivkeysarea-inner,
.getbip39keysarea-inner {
	text-align: left;
	width: 80%;
	margin: 0 auto;
	padding: 35px 0;
}
.getprivkeysarea-inner-res,
.getbip39keysarea-inner-res {
	display: none;
	margin-top: 30px;
}
.getprivkeysarea-seed,
.getbip39keysarea-seed {
	display: block;
	width: 650px;
	padding: 5px;
	margin: 22px 0 24px 0;
}
.default-text {
	font-size: 14px;
}
.getprivkeysarea-select,
.getbip39keysarea-select {
	margin: 0 10px;
}
.getprivkeysarea-inner-res-msg-item-col,
.getbip39keysarea-inner-res-msg-item-col {
	margin: 10px 0;
}
.getprivkeysarea-inner-res-msg-item-col strong,
.getbip39keysarea-inner-res-msg-item-col strong {
	margin-right: 5px;
}
.getprivkeysarea-inner-res-msg-item-col.last,
.getbip39keysarea-inner-res-msg-item-col.last {
	margin-bottom: 35px;
}
.getprivkeysarea-selectors {
	width: 300px;
	margin-bottom: 20px;
}
.getprivkeysarea-selectors span {
	width: 70%;
  display: inline-block;
}
.label.small {
	font-size: 14px;
}
.getxpubarea-inner {
	text-align: left;
	width: 80%;
	margin: 0 auto;
	padding: 35px 0;
}
.getxpubarea-inner p {
	padding-top: 10px;
	padding-bottom: 15px;
}
.getxpubarea-inner p.first {
	padding-top: 10px;
	padding-bottom: 0;
}
.getxpubarea-seed {
	width: 510px;
}
#getxpubarea-res {
	word-break: break-all;
}
#getxpubarea .label {
	text-align: left;
	font-weight: bold;
	display: block;
	margin-bottom: 15px;
}
#getxpubarealabel-bip32,
.getxpubarea-account {
	margin-left: 40px;
}
.offline-sign-area-inner {
	text-align: left;
	width: 80%;
	margin: 0 auto;
	padding: 35px 0;
}
.offline-sign-area-inner .label {
	text-align: left;
	font-weight: bold;
	display: block;
	margin-bottom: 15px;
}
.offline-sign-area-seed {
	display: block;
	width: 650px;
	padding: 5px;
	margin: 22px 0 24px 0;
}
#offline-sign-area-verify-block p {
	padding-top: 10px;
}
#offline-sign-area-verify-block strong {
	font-size: 15px;
}
.offline-sign-area-verify-button-block {
	padding-top: 10px;
	padding-bottom: 10px;
}
#offline-sign-area-txin {
	padding: 5px;
}
#offline-sign-area-verify-block-error {
	font-weight: bold;
	color:brown;
	padding-top: 10px;
}
#offline-sign-area-rawtx-bytes {
	word-break: break-all;
	padding-top: 10px;
}
#offline-sign-area-clear-btn {
	position: absolute;
	right: 160px;
	margin-top: -38px;
}
  </style>
</head>
<body onclick="SecureRandom.seedTime();" onmousemove="ninja.seeder.seed(event);">
  <div id="busyblock"></div>
  <div id="main">
    <div id="culturemenu">
      <span><a href="?culture=en" id="cultureen" class="selected">English</a></span> |
      <span><a href="?culture=fr" id="culturefr">Français</a></span> |
      <span><a href="?culture=ru" id="cultureru">Русский</a></span> |
      <span><a href="?culture=es" id="culturees">Spanish</a></span> |
    </div>

    <div class="banner">
      <div id="coinLogo">
        <img id="coinLogoImg" src="logos/bitcoin.png" alt="Universal Open Source Client-Side Wallet Generator" />
        <span>KOMODO</span>
      </div>
      <div id="siteTitle" alt="Bitcoin Paper Wallet Generator"></div>
    </div>

    <div id="seedpoolarea"><textarea rows="16" cols="62" id="seedpool"></textarea></div>

    <div class="menu" id="menu">
      <div class="tab i18n selected" id="singlewallet" onclick="ninja.tabSwitch(this);">Single Wallet</div>
      <div class="tab i18n" id="paperwallet" onclick="ninja.tabSwitch(this);">Paper Wallet</div>
      <div class="tab i18n" id="bulkwallet" onclick="ninja.tabSwitch(this);">Bulk Wallet</div>
      <div class="tab i18n" id="brainwallet" onclick="ninja.tabSwitch(this);">Brain Wallet</div>
			<div class="tab i18n" id="detailwallet" onclick="ninja.tabSwitch(this);">Wallet Details</div>
			<div class="tab i18n" id="decryptseed" onclick="ninja.tabSwitch(this);">Decrypt seed</div>
			<div class="tab i18n" id="generateseed" onclick="ninja.tabSwitch(this);">Generate seed</div>
			<div class="tab i18n" id="getprivkeys" onclick="ninja.tabSwitch(this);">Seed priv keys</div>
			<div class="tab i18n" id="getbip39keys" onclick="ninja.tabSwitch(this);">BIP39 keys</div>
			<div class="tab i18n" id="getxpub" onclick="ninja.tabSwitch(this);">xpub</div>
			<div class="tab i18n" id="offlineSign" onclick="ninja.tabSwitch(this);">Sign</div>
    </div>

    <div id="wallets">
			<div id="offline-sign-area" class="walletarea">
				<div class="offline-sign-area-inner">
					<span class="label i18n">Provide required data below to sign a transaction</span>
					<input type="button" id="offline-sign-area-clear-btn" value="Clear" onclick="ninja.wallets.offlineSign.open();">
					<input type="password" id="offline-sign-area-seed" class="offline-sign-area-seed" value="lime lime2" placeholder="Enter your seed/priv key here" autocomplete="off" onfocus="this.select();">
					<textarea rows="10" cols="110" id="offline-sign-area-txin" placeholder="Provide unsigned transaction data generated from Agama">
					{"utxoSet":[{"txid":"7bf8e2e421fadd3f6555e8b4830a8aace079967a2e9abe7cde94bc99d3c03e8f","vout":1,"value":13884579076,"interestSats":8035610,"verified":false,"height":1163703,"currentHeight":1169830},{"txid":"e1a331676fc60d763b272916114ded6ca1bad9ff3ff8dd99418dc13e6c50e2a8","vout":0,"value":1141881981,"interestSats":4814748,"verified":false,"height":1060109,"currentHeight":1169830},{"txid":"3b026abffc51f2d78ea3aa57e5616dc05459254d6d2a182a8a68d0834ce58915","vout":1,"value":443084169,"interestSats":0,"verified":false,"height":1163659,"currentHeight":1169830}],"change":12830358,"changeAdjusted":12830358,"totalInterest":12850358,"fee":10000,"value":15469545226,"outputAddress":"RBtmFFVxL1r3xcvJHFXZmURuazCx75nprS","changeAddress":"RBtmFFVxL1r3xcvJHFXZmURuazCx75nprS","network":"kmd","utxoVerified":false,"coin":"kmd","from":"RBtmFFVxL1r3xcvJHFXZmURuazCx75nprS"}
					</textarea>
					<div class="offline-sign-area-verify-button-block">
						<input type="button" id="offline-sign-area-verify" value="Verify" onclick="ninja.wallets.offlineSign.verify();">
					</div>
					<div id="offline-sign-area-verify-block-error"></div>
					<div id="offline-sign-area-verify-block">
						<hr />
						<p>
							<strong>Coin:</strong> <span id="offline-sign-area-verify-coin" class="offline-sign-area-verify-coin"></span>
						</p>
						<p>
							<strong>From:</strong> <span id="offline-sign-area-verify-from" class="offline-sign-area-verify-from"></span>
						</p>
						<p>
							<strong>To:</strong> <span id="offline-sign-area-verify-to" class="offline-sign-area-verify-to"></span>
						</p>
						<p>
							<strong>Amount:</strong> <span id="offline-sign-area-verify-amount" class="offline-sign-area-verify-amount"></span>
						</p>
						<p>
							<strong>Fee:</strong> <span id="offline-sign-area-verify-fee" class="offline-sign-area-verify-fee"></span>
						</p>
						<p>
							<strong>Change:</strong> <span id="offline-sign-area-verify-change" class="offline-sign-area-verify-change"></span> <strong>to</strong> <span id="offline-sign-area-verify-change-address" class="offline-sign-area-verify-change-address"></span>
						</p>
						<div class="offline-sign-area-verify-button-block">
							<input type="button" id="offline-sign-area-verify" value="Sign" onclick="ninja.wallets.offlineSign.sign();">
						</div>
					</div>
					<div id="offline-sign-area-rawtx" class="offline-sign-area-rawtx">
						<hr />
						<strong>Signed transaction:</strong>
						<div id="offline-sign-area-rawtx-bytes"></div>
					</div>
				</div>
			</div>

			<div id="getbip39keysarea" class="walletarea">
				<div class="getbip39keysarea-inner">
					<span class="label i18n">Provide your Ledger or any other bip39 compatible seed to retrieve KMD private keys</span>
					<span class="label small i18n">Note: key search is a resource intensive operation. Please use small increments when choosing selector options. Otherwise it may result in browser hang for a long period of time.</span>
					<input type="password" id="getbip39keysarea-seed" class="getbip39keysarea-seed" value="" placeholder="Enter your seed here" autocomplete="off" onfocus="this.select();">
					<input type="text" id="getbip39keysarea-search" class="getbip39keysarea-seed" value="" placeholder="Enter a KMD address to search for" autocomplete="off" onfocus="this.select();">
					<div class="getprivkeysarea-selectors">
						<span class="default-text">Number of accounts</span>
						<select id="getprivkeysarea-select-accounts-count" class="getprivkeysarea-select" value="2">
							<option value="2">2</option>
							<option value="3">3</option>
							<option value="4">4</option>
							<option value="5">5</option>
							<option value="6">6</option>
							<option value="7">7</option>
							<option value="8">8</option>
							<option value="9">9</option>
							<option value="10">10</option>
						</select>
					</div>
					<div class="getprivkeysarea-selectors">
						<span class="default-text">Number of addresses</span>
						<select id="getprivkeysarea-select-addresses-count" class="getprivkeysarea-select" value="2">
							<option value="2">2</option>
							<option value="3">3</option>
							<option value="4">4</option>
							<option value="5">5</option>
							<option value="6">6</option>
							<option value="7">7</option>
							<option value="8">8</option>
							<option value="9">9</option>
							<option value="10">10</option>
						</select>
					</div>
					<div class="getprivkeysarea-selectors">
						<span class="default-text">Number of accounts offset</span>
						<select id="getprivkeysarea-select-accounts-count-offset" class="getprivkeysarea-select" value="0">
							<option value="0">0</option>
							<option value="1">1</option>
							<option value="2">2</option>
							<option value="3">3</option>
							<option value="4">4</option>
							<option value="5">5</option>
							<option value="6">6</option>
							<option value="7">7</option>
							<option value="8">8</option>
							<option value="9">9</option>
							<option value="10">10</option>
						</select>
					</div>
					<div class="getprivkeysarea-selectors">
						<span class="default-text">Number of addresses offset</span>
						<select id="getprivkeysarea-select-addresses-count-offset" class="getprivkeysarea-select" value="0">
							<option value="0">0</option>
							<option value="1">1</option>
							<option value="2">2</option>
							<option value="3">3</option>
							<option value="4">4</option>
							<option value="5">5</option>
							<option value="6">6</option>
							<option value="7">7</option>
							<option value="8">8</option>
							<option value="9">9</option>
							<option value="10">10</option>
						</select>
					</div>
					<input type="button" value="Get priv keys" onclick="ninja.wallets.getbip39keys.get();">
					<div id="getbip39keysarea-inner-res" class="getbip39keysarea-inner-res">
						<hr />
						<div id="getbip39keysarea-inner-res-msg" class="getbip39keysarea-inner-res-msg"></div>
					</div>
				</div>
			</div>

			<div id="getprivkeysarea" class="walletarea">
				<div class="getprivkeysarea-inner">
					<span class="label i18n">Provide your Agama/BarterDEX seed to retrieve private keys</span>
					<input type="password" id="getprivkeysarea-seed" class="getprivkeysarea-seed" value="" placeholder="Enter your seed here" autocomplete="off" onfocus="this.select();">
					<span class="default-text">Coin</span> <select id="getprivkeysarea-select" class="getprivkeysarea-select"></select>
					<input type="button" id="encryptedseeddecrypt" value="Get priv keys" onclick="ninja.wallets.getprivkeys.get();">
					<div id="getprivkeysarea-inner-res" class="getprivkeysarea-inner-res">
						<hr />
						<div id="getprivkeysarea-inner-res-msg" class="getprivkeysarea-inner-res-msg"></div>
					</div>
				</div>
			</div>

			<div id="decryptseedarea" class="walletarea">
				<div class="decryptseedarea-inner">
					<span class="label i18n">Insert contents of .pin file below</span>
					<textarea rows="20" cols="120" id="encryptedseed"></textarea>
					<input type="password" id="encryptedseedkey" value="" placeholder="Enter a decryption key here" autocomplete="off" onfocus="this.select();">
					<input type="button" id="encryptedseeddecrypt" value="Decrypt" onclick="ninja.wallets.decryptseed.decode();">
					<div id="decryptseedarea-inner-res" class="decryptseedarea-inner-res">
						<hr />
						<div id="decryptseedarea-inner-res-msg" class="decryptseedarea-inner-res-msg"></div>
					</div>
				</div>
			</div>

			<div id="generateseedarea" class="walletarea">
				<div class="generateseedarea-inner">
					<span class="label i18n">Select seed type</span>
					<div class="generateseedarea-seed-type">
							<input type="radio" id="seedtype1" name="seedtype" value="seedtype1" checked="checked" onClick="ninja.wallets.generateseed.generate(256)">
							<label for="seedtype1">Agama/BarterDEX (256-bit)</label>

							<input type="radio" id="seedtype2" name="seedtype" value="seedtype2" onClick="ninja.wallets.generateseed.generate(160)">
							<label for="seedtype2">Waves (160-bit)</label>

							<input type="radio" id="seedtype3" name="seedtype" value="seedtype3" onClick="ninja.wallets.generateseed.generate(128)">
							<label for="seedtype3">NXT (128-bit)</label>
					</div>
					<span class="label i18n label-padding">This is your new seed. Please keep it extra secure and don't use any smart editors to store it.</span>
					<div class="generateseedarea-seed" id="generateseedarea-seed"></div>
				</div>
			</div>

			<div id="getxpubarea" class="walletarea">
				<div class="getxpubarea-inner">
					<span class="label i18n">Provide your Agama/BarterDEX seed or a random seed to get xpub key</span>
					<p class="first">
						<span class="default-text">Coin</span> <select id="getxpubarea-select" class="getxpubarea-select"></select>
						<span onclick="ninja.wallets.getxpub.renderCoinSelector();"><label id="getxpubarealabel-bip32" for="getxpubarea-bip32">bip32</label> <input type="checkbox" id="getxpubarea-bip32" /></span>
						<span id="getxpubarea-account-block">
							<span class="default-text getxpubarea-account">Account</span> <select id="getxpubarea-account-select" class="getxpubarea-select"></select>
						</span>
					</p>
					<p>
						<input type="password" id="getxpubarea-seed" class="getxpubarea-seed" value="" placeholder="Enter your seed here" autocomplete="off" onfocus="this.select();">
						<input type="button" id="getxpubbutton" value="Get xpub key" onclick="ninja.wallets.getxpub.get();">
					</p>
					<div id="getxpubarea-res"></div>
				</div>
			</div>

			<div id="singlearea" class="walletarea">
        <div id="initBanner">
          <span id="generatelabelbitcoinaddress" class="i18n">Generating new Address...</span><br />
          <span id="generatelabelmovemouse" class="i18n">MOVE your mouse around to add some extra randomness... </span><span id="mousemovelimit"></span><br />
          <span id="generatelabelkeypress" class="i18n">OR type some random characters into this textbox</span> <input type="text" id="generatekeyinput" onkeypress="ninja.seeder.seedKeyPress(event);" /><br />
          <div id="seedpooldisplay"></div>

          <div id="rightArea">
            <div id="progress-bar" class="fullyRounded">
              <div id="progress-bar-percentage" class="fullyRounded 1percentwidth"></div>
            </div>

            <div id="seedSkipper">
              <a href="#" class="nicerButton 100pxwidth" onClick="ninja.seeder.seedCount = ninja.seeder.seedLimit; ninja.seeder.seed();">Skip &raquo;</a>
              <p id="skipMessage" class="i18n">You may skip this step if you do not plan to use the random key generator.</p>
            </div>
          </div>
        </div>

        <div id="walletCommands" class="commands">
          <div id="singlecommands" class="row">
            <span><input type="button" id="newaddress" value="Generate New Address" onclick="ninja.wallets.singlewallet.generateNewAddressAndKey();" /></span>
            <span class="print"><input type="button" name="print" value="Print" id="singleprint" onclick="window.print();" /></span>
          </div>
        </div>
        <div id="keyarea" class="keyarea">
          <div class="public">
            <div class="pubaddress">
              <span class="label i18n" id="singlelabelbitcoinaddress">Public Address</span>
            </div>
            <div id="qrcode_public" class="qrcode_public"></div>
            <div class="pubaddress">
              <span class="output" id="btcaddress"></span>
            </div>
            <div id="singleshare" class="i18n">SHARE</div>
          </div>
          <div class="private">
            <div class="privwif">
              <span class="label i18n" id="singlelabelprivatekey">Private Key (Wallet Import Format)</span>
            </div>
            <div id="qrcode_private" class="qrcode_private"></div>
            <div class="privwif">
              <span class="output" id="btcprivwif"></span>
            </div>
            <div id="singlesecret" class="i18n">SECRET</div>
          </div>
        </div>

        <div id="singlesafety">
          <div class="firstHalfSingleSafety">

            <h3 id="securitystep0title" class="i18n">Step 0. Follow the security checklist recommendation</h3>
            <p id="securitystep0" class="i18n">
              First step is to <strong>download</strong> this website from <a href="https://github.com/MichaelMure/PaperWallet/archive/master.zip">Github</a> and open the index.html file directly from your computer. It's just too easy to sneak some evil code in the 6000+ lines of javascript to leak your private key, and you don't want to see your fund stolen. Code version control make it much easier to cross-check what actually run. For extra security, <strong>unplug your Internet access</strong> while generating your wallet.
            </p>
            <h3 id="securitystep1title" class="i18n">Step 1. Generate new address</h3>
            <p id="securitystep1" class="i18n">
              Choose your currency and click on the "Generate new address" button.
            </p>
            <h3 id="securitystep2title" class="i18n">Step 2. Print the Paper Wallet</h3>
            <p id="securitystep2" class="i18n">
              Click the Paper Wallet tab and print the page on high quality setting. <strong>Never save the page as a PDF file to print it later since a file is more likely to be hacked than a piece of paper.</strong>
            </p>
            <h3 id="securitystep3title" class="i18n">Step 3. Fold the Paper Wallet</h3>
            <p id="securitystep3" class="i18n">
              Fold your new Paper wallet following the lines.
              <img src="images/foldinginstructions.png" alt="Fold in half lengthwise, and then in three widthwise." /><br />
              You can insert one side inside the other to lock the wallet.
            </p>

            <h3 id="securitystep4title" class="i18n">Step 4. Share your public address</h3>
            <p id="securitystep4" class="i18n">
              Use your public address to receive money from other crypto-currency users. You can share your public address as much as you want.
            </p>
            <h3 id="securitystep5title" class="i18n">Step 5. Keep your private key secret</h3>
            <p id="securitystep5" class="i18n">
              The private key is literally the keys to your coins, if someone was to obtain it, they could withdraw the funds currently in the wallet, and any funds that might be deposited in that wallet.
            </p>
            <p>
              <strong id="securitystep6" class="i18n">Please test spending a small amount before receiving any large payments.</strong><br /><br />
            </p>


          </div>
          <div class="secondHalfSingleSafety">
            <img class="frontPageImage" src="images/overview.png" alt="Overview image of 4 paper wallet" />

            <div class="securityChecklist">
              <b id="securitychecktitle" class="i18n">Security Checklist :</b>

              <ul>
              <li id="envSecurityCheck"></li>

              <li id="browserSecurityCheck"></li>

              <li id="securitychecklivecd" class="i18n">
                Are you using a secure operating system guaranteed to be free of spyware and viruses, for example, an Ubuntu LiveCD?
              </li>
              </ul>
            </div>
          </div>
        </div>

        <div id="faqZone">
          <h2>Frequently asked questions :</h2>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion1');ninja.toggleFaqQuestion('faqQuestion1.1')">&#8226; Is it safe ?</a></h3>
          <p class="faqAnswer" id="faqQuestion1">
            We try to make it that way ! The core of the tool, that generate the keys is 99% the same as the well reviewed bitaddress.org. We only changed it to be able to generate addresses for different crypto-currencies.
          </p>
          <p class="faqAnswer" id="faqQuestion1.1">
We think that having a unique generator for multiple currencies lead to a much better reviewed tool for all than having a myriad of half-backed generators.
Changes made to this generator are available on Github in small and divided commits and those are easy to review and reuse.
Walletgenerator.net use the same security measures as the original project. All-in-one html document, no ajax, no analytics, no external calls, no CDN that can inject anything they want. And trust us, we have seen some nasty things when reviewing some wallet generator.
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion2');">&#8226; Why should I use a paper wallet ?</a></h3>
          <p class="faqAnswer" id="faqQuestion2">
              Advantages of a paper wallet are multiple:<br/><br/>
              <span class="faqListBullet">&#8658;</span> They are not subject to malwares and keyloggers<br/>
              <span class="faqListBullet">&#8658;</span> You don’t rely on a third party’s honesty or capacity to protect your coins<br/>
              <span class="faqListBullet">&#8658;</span> You won't lose your coins when your device break
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion3');">&#8226; How to use a paper wallet ?</a></h3>
          <p class="faqAnswer" id="faqQuestion3">
            Once you have generated and printed a wallet, you can send coins to the public address, like for any wallet. Store your paper wallet securely. It contains everything that is needed to spend your funds. Consider using BIP38 to secure your paper wallet with a password.
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion4');">&#8226; How to spend the coins stored in a paper wallet ?</a></h3>
          <p class="faqAnswer" id="faqQuestion4">
            You will need to import your private key in a real client, that you can download from the currency website. The exact method to do that will depend on the client. If there is no integrated method, you can usually fall back to the debug console and use the command “importprivkey [yourprivatekey]“.
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion5');">&#8226; How walletgenerator.net is different than another wallet generator ?</a></h3>
          <p class="faqAnswer" id="faqQuestion5">
            It’s not that different. You will find another design for the paper wallet and some improvements here and there. The big difference is that this is a unique project for a lot of currencies, so more people can review it and check its safety.
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion7');">&#8226; Can you add support for cryptocurrency XYZ ?</a></h3>
          <p class="faqAnswer" id="faqQuestion7">
            Absolutely ! To help us do that, you can <a href="https://docs.google.com/forms/d/1nPIvukVWxlaveUPEUAEhGKwvEuwJiPSgs5zc5LhDVpk/viewform">fill this  form</a>. But keep in mind that there is some currency that we cannot support. If the developers made some change in the address format, we won’t hack the crypto core of the project and take the risk to tamper the security of the others currencies.
            You can also implement the support yourself by following this <a href="https://github.com/MichaelMure/WalletGenerator.net/wiki/How-to-add-a-new-currency">non-developer How-To</a>
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion8');">&#8226; Why should I make a donation ?</a></h3>
          <p class="faqAnswer" id="faqQuestion8">
            Donations money are used to pay our hosting service provider, but it’ll also be used to make walletgenerator.net more secure as we plan to organize a CrowdCurity campain as soon as we get enough money to pay for it.
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion9');">&#8226; I found a bug, what shall I do ?</a></h3>
          <p class="faqAnswer" id="faqQuestion9">
            You can report bugs using GitHub. You can also contact us using our Twitter account (<a href="http://twitter.com/WalletGenerator" target="_blank">@WalletGenerator</a>). Just try to explain clearly what is wrong and we will try to fix the bug as soon as possible.
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion10');">&#8226; Who are you ?</a></h3>
          <p class="faqAnswer" id="faqQuestion10">
            We are just two random guy having fun with a side project.
          </p>

          <h3 class="faqQuestion"><a class="faqLink" onclick="ninja.toggleFaqQuestion('faqQuestion11');">&#8226; How can I help ?</a></h3>
          <p class="faqAnswer" id="faqQuestion11">
            Donation are always welcome, but you can also help us translate the website. It's really easy. Just add "?i18nextract=LANGUAGECODE" in the end of the url (for instance http://walletgenerator.net/?i18nextract=es for Spanish). You will see at the end of the page a pre-filled javascript array ready to be translated. Translate or correct it, and send it to us the way you prefer. Even partial translation are helpful !
          </p>

        </div>
      </div>

      <div id="paperarea">
        <div class="commands">
          <div id="papercommands" class="row">
            <span><label id="paperlabelencrypt" for="paperencrypt" class="i18n">BIP38 Encrypt?</label> <input type="checkbox" id="paperencrypt" onchange="ninja.wallets.paperwallet.toggleEncrypt(this);" /></span>
            <span><label id="paperlabelBIPpassphrase" for="paperpassphrase" class="i18n">Passphrase:</label> <input type="text" id="paperpassphrase" /></span>

            <br/>
            <input type="button" id="papergenerate" value="Randomly generate" onclick="ninja.wallets.paperwallet.build(document.getElementById('paperpassphrase').value);" />
            <span>OR</span>
            <input placeholder="Enter your own WIF private key" id="suppliedPrivateKey" name="suppliedPrivateKey" spellcheck="false" />&nbsp;
            <input type="button" id="papergenerate" value="Apply &raquo;" onClick="ninja.wallets.paperwallet.testAndApplyVanityKey();" />

            <span class="print"><input type="button" name="print" value="Print" id="paperprint" onclick="window.print();" /></span>
          </div>

        </div>
        <div id="paperkeyarea"></div>
      </div>

      <div id="bulkarea" class="walletarea">
        <div class="commands">
          <div id="bulkcommands" class="row">
            <span><label id="bulklabelstartindex" for="bulkstartindex" class="i18n">Start index:</label> <input type="text" id="bulkstartindex" value="1" /></span>
            <span><label id="bulklabelrowstogenerate" for="bulklimit" class="i18n">Rows to generate:</label> <input type="text" id="bulklimit" value="3" /></span>
            <span><label id="bulklabelcompressed" for="bulkcompressed" class="i18n">Compressed addresses?</label> <input type="checkbox" id="bulkcompressed" /></span>
            <span><input type="button" id="bulkgenerate" value="Generate" onclick="ninja.wallets.bulkwallet.buildCSV(document.getElementById('bulklimit').value * 1, document.getElementById('bulkstartindex').value * 1, document.getElementById('bulkcompressed').checked);" /> </span>
            <span class="print"><input type="button" name="print" id="bulkprint" value="Print" onclick="window.print();" /></span>
          </div>
        </div>
        <div class="body">
          <span class="label i18n" id="bulklabelcsv">Comma Separated Values: Index,Address,Private Key (WIF)</span>
          <textarea rows="20" cols="88" id="bulktextarea"></textarea>
        </div>
      </div>

      <div id="brainarea" class="walletarea">
        <div id="braincommands" class="commands">
          <div class="row">
            <span id="brainlabelenterpassphrase" class="label"><label id="brainlabelenterpassphraselbl" class="i18n" for="brainpassphrase">Enter Passphrase: </label></span>
            <input tabindex="1" type="password" id="brainpassphrase" value="" onfocus="this.select();" onkeypress="if (event.keyCode == 13) ninja.wallets.brainwallet.view();" />
            <span><label id="brainlabelshow" for="brainpassphraseshow">Show?</label> <input type="checkbox" id="brainpassphraseshow" onchange="ninja.wallets.brainwallet.showToggle(this);" /></span>
            <span><label id="brainlabeliguana" for="brainpassphraseiguana">Iguana compatible?</label> <input type="checkbox" id="brainpassphraseiguana" /></span>
            <span class="print"><input type="button" name="print" id="brainprint" value="Print" onclick="window.print();" /></span>
          </div>
          <div class="row extra">
            <span class="label" id="brainlabelconfirm"><label id="brainlabelconfirmlbl" class="i18n" for="brainpassphraseconfirm">Confirm Passphrase: </label></span>
            <input tabindex="2" type="password" id="brainpassphraseconfirm" value="" onfocus="this.select();" onkeypress="if (event.keyCode == 13) ninja.wallets.brainwallet.view();" />
            <span><input tabindex="3" type="button" id="brainview" value="View" onclick="ninja.wallets.brainwallet.view();" /></span>
            <span id="brainalgorithm" class="notes right i18n">Algorithm: SHA256 (passphrase)</span>
          </div>
          <div class="row extra"><span id="brainwarning"></span></div>
          <div class="row extra errorMsg"><span id="brainerror"></span></div>
        </div>
        <div id="brainkeyarea" class="keyarea">
          <div class="public">
            <div id="brainqrcodepublic" class="qrcode_public"></div>
            <div class="pubaddress">
              <span class="label i18n" id="brainlabelbitcoinaddress">Public Address:</span>
              <span class="output" id="brainbtcaddress"></span>
            </div>
          </div>
          <div class="private">
            <div id="brainqrcodeprivate" class="qrcode_private"></div>
            <div class="privwif">
              <span class="label i18n" id="brainlabelprivatekey">Private Key (Wallet Import Format):</span>
              <span class="output" id="brainbtcprivwif"></span>
            </div>
          </div>
        </div>
      </div>

      <div id="detailarea" class="walletarea">
        <div id="detailcommands" class="commands">

          <div class="row extra qrzone">
            <span class="qrinput">
            <label id="detaillabelenterprivatekey" for="detailprivkey" class="i18n">Enter Private Key</label>

              <span class="qrcodeinputwrapper">
                <input type="text" id="detailprivkey" value="" placeholder="Enter a private key, or click the QR icon to scan" autocomplete="off" onFocus="this.select();" onKeyPress="if (event.keyCode == 13) ninja.wallets.detailwallet.viewDetails();" />
                <img onClick="ninja.wallets.detailwallet.qrscanner.start()" />
              </span>

            <input type="button" id="detailview" value="View Details" onclick="ninja.wallets.detailwallet.viewDetails();" />
          </span>
          <span class="print">
            <input type="button" name="print" id="detailprint" value="Print" onclick="window.print();" />
          </span>
          </div>

          <div id="paperqrscanner">
            <div class="background"></div>
            <div id="mainbody" class="dialog instructionsarea">
              <h2 id="qrcaminstructiontitle" class="i18n">Scan QR code using your camera</h2>
              <div id="paperqrnotsupported" class="hide redColor i18n">Sorry, but your web browser does not support the HTML5 camera controls. Try using a recent version of Firefox (recommended), Chrome or Opera.</div>
              <div id="paperqrpermissiondenied" class="hide redColor i18n">
                <p>Permission denied. Your browser should display a message requesting access to your camera. Please click the "Allow" button to enable the camera.</p>
              </div>
              <div id="paperqrerror" class="redColor"></div>
              <div id="paperqroutput"></div>
              <button onClick="ninja.wallets.detailwallet.qrscanner.stop()">Cancel</button>
            </div>
          </div>
          <div id="detailbip38commands">
            <span><label id="detaillabelpassphrase" class="i18n">Enter BIP38 Passphrase</label> <input type="text" id="detailprivkeypassphrase" value="" onfocus="this.select();" onkeypress="if (event.keyCode == 13) ninja.wallets.detailwallet.viewDetails();" /></span>
            <span><input type="button" id="detaildecrypt" value="Decrypt BIP38" onclick="ninja.wallets.detailwallet.viewDetails();" /></span>
          </div>
        </div>
        <div id="detailkeyarea">
          <div class="notes">
            <span id="detaillabelnote1" class="i18n">Your Private Key is a unique secret number that only you know. It can be encoded in a number of different formats. Below we show the Public Address and Public Key that corresponds to your Private Key as well as your Private Key in the most popular encoding formats (WIF, WIFC, HEX, B64).</span>
            <br /><br />
          </div>
          <div class="pubqr">
            <div class="item">
              <span class="label i18n" id="detaillabelbitcoinaddress">Public Address</span>
              <div id="detailqrcodepublic" class="qrcode_public"></div>
              <span class="output" id="detailaddress"></span>
            </div>
            <div class="item right">
              <span class="label i18n" id="detaillabelbitcoinaddresscomp">Public Address Compressed</span>
              <div id="detailqrcodepubliccomp" class="qrcode_public"></div>
              <span class="output" id="detailaddresscomp"></span>
            </div>
          </div>
          <br /><br />
          <div class="item clear">
            <span class="label i18n" id="detaillabelpublickey">Public Key (130 characters [0-9A-F]):</span>
            <span class="output pubkeyhex" id="detailpubkey"></span>
          </div>
          <div class="item">
            <span class="label i18n" id="detaillabelpublickeycomp">Public Key (compressed, 66 characters [0-9A-F]):</span>
            <span class="output" id="detailpubkeycomp"></span>
          </div>
          <hr />
          <div class="privqr">
            <div class="item">
              <span class="label"><span id="detaillabelprivwif" class="i18n">Private Key WIF<br />51 characters Base58</span></span>
              <div id="detailqrcodeprivate" class="qrcode_private"></div>
              <span class="output" id="detailprivwif"></span>
            </div>
            <div class="item right">
              <span class="label"><span id="detaillabelprivwifcomp" class="i18n">Private Key WIF Compressed<br />52 characters Base58</span></span>
              <div id="detailqrcodeprivatecomp" class="qrcode_private"></div>
              <span class="output" id="detailprivwifcomp"></span>
            </div>
          </div>
          <br /><br />
          <div class="item clear">
            <span class="label i18n" id="detaillabelprivhex">Private Key Hexadecimal Format (64 characters [0-9A-F]):</span>
            <span class="output" id="detailprivhex"></span>
          </div>
          <div class="item">
            <span class="label i18n" id="detaillabelprivb64">Private Key Base64 (44 characters):</span>
            <span class="output" id="detailprivb64"></span>
          </div>
          <div class="item displayNone" id="detailmini">
            <span class="label i18n" id="detaillabelprivmini">Private Key Mini Format (22, 26 or 30 characters):</span>
            <span class="output" id="detailprivmini"></span>
          </div>
          <div class="item displayNone" id="detailb6">
            <span class="label i18n" id="detaillabelprivb6">Private Key Base6 Format (99 characters [0-5]):</span>
            <span class="output" id="detailprivb6"></span>
          </div>
          <div class="item displayNone" id="detailbip38">
            <span class="label i18n" id="detaillabelprivbip38">Private Key BIP38 Format (58 characters Base58):</span>
            <span class="output" id="detailprivbip38"></span>
          </div>
        </div>
        <div class="faqs">
          <div id="detailfaq1" class="faq">
            <div id="detailq1" class="question" onclick="ninja.wallets.detailwallet.openCloseFaq(1);">
              <span id="detaillabelq1" class="i18n">How do I make a wallet using dice? What is B6?</span>
              <div id="detaile1" class="more"></div>
            </div>
            <div id="detaila1" class="answer i18n">An important part of creating a crypto-currency wallet is ensuring the random numbers used to create the wallet are truly random. Physical randomness is better than computer generated pseudo-randomness. The easiest way to generate physical randomness is with dice. To create a crypto-currency private key you only need one six sided die which you roll 99 times. Stopping each time to record the value of the die. When recording the values follow these rules: 1=1, 2=2, 3=3, 4=4, 5=5, 6=0. By doing this you are recording the big random number, your private key, in B6 or base 6 format. You can then enter the 99 character base 6 private key into the text field above and click View Details. You will then see the public address associated with your private key. You should also make note of your private key in WIF format since it is more widely used.</div>
          </div>
        </div>
      </div>

    <div id="footer" class="footer">
      <div>
        <span class="item"><a href="https://github.com/MichaelMure/WalletGenerator.net" target="_blank" id="footerlabelgithub" class="i18n">Download (GitHub Repository)</a></span>
        <span class="item"><a href="https://twitter.com/WalletGenerator">@WalletGenerator</a></span>
      </div>
      <div class="copyright">
        <span id="footerlabelcopyright1">Copyright WalletGenerator.net.</span>
        <span id="footerlabelcopyright2" class="i18n">JavaScript copyrights are included in the source.</span>
        <span id="footerlabelnowarranty" class="i18n">No warranty.</span>
      </div>
    </div>
  </div>

  <script type="text/javascript">
(function (window) {
	var muchIndex    = 0;
	var wowLength    = 0;
	var manyWords    = null;
	var suchInterval = null;
	var muchPlay     = false;
	var wowElement   = document.createElement('div');
	var suchColors   = [
		'#FF0000',
		'#00FF00',
		'#0000FF',
	];

	function veryRandom(val) {
		return Math.floor((Math.random() * val));
	}

	function placeWord(word) {

		var muchWidth  = window.innerWidth - 200; //Very random offset
		var manyHeight = window.innerHeight - 26; //Such fontsize based offset

		wowElement.textContent  = word;
		wowElement.style.left   = veryRandom(muchWidth) + 'px';
		wowElement.style.top    = veryRandom(manyHeight) + 'px';
		wowElement.style.color  = suchColors[veryRandom(suchColors.length)];
	}

	function muchWords() {
		muchPlay     = true;
		suchInterval = setInterval(function () {

			if(muchIndex === wowLength - 1) {
				muchIndex = 0;
			} else {
				muchIndex++;
			}

			placeWord(manyWords[muchIndex]);

		}, 6000);

	}

	var Doge = function (words) {
		if (typeof(words) !== 'object' || words.length === undefined) {
			return console.error('Wow. Words is not array. Much Error.');
		}

		if (words.length < 1) {
			return console.error('Much dumb. Very fail. No words in array. Wow');
		}

		wowLength = words.length;
		manyWords = words;

        wowElement.className = 'dogeTag';
		wowElement.style.position   = 'fixed';
		wowElement.style.fontSize   = '26px';
		wowElement.style.fontFamily = '"Comic Sans MS"';
		wowElement.style.zIndex     = 10000001;
		document.body.appendChild(wowElement);
		muchWords();
	};

	Doge.prototype.stop = function () {
		if (muchPlay) {
			muchPlay = false;
			clearInterval(suchInterval);
		}
        if(wowElement != null)
            wowElement.parentNode.removeChild(wowElement);
	};

	window.Doge = Doge;

}(window));
  </script>
  <script type="text/javascript">
var janin = {};

janin.currency = {
    createCurrency: function (name, networkVersion, privateKeyPrefix, WIF_Start, CWIF_Start, donate) {
        var currency = {};
        currency.name = name;
        currency.networkVersion = networkVersion;
        currency.privateKeyPrefix = privateKeyPrefix;
        currency.WIF_Start = WIF_Start;
        currency.CWIF_Start = CWIF_Start;
        currency.donate = donate;
        return currency;
    },

    name: function() {
        return janin.selectedCurrency.name;
    },

    networkVersion: function() {
        return janin.selectedCurrency.networkVersion;
    },

    privateKeyPrefix: function() {
        return janin.selectedCurrency.privateKeyPrefix;
    },

    WIF_RegEx: function() {
        return new RegExp("^" + janin.selectedCurrency.WIF_Start + "[123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz]{50}$");
    },

    CWIF_RegEx: function() {
        return new RegExp("^" + janin.selectedCurrency.CWIF_Start + "[123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz]{51}$");
    },

    // Switch currency
    useCurrency: function(index) {
        janin.selectedCurrency = janin.currencies[index];

        var coinImgUrl = "logos/" + janin.currency.name().toLowerCase() + ".png";
        document.getElementById("coinLogoImg").src = coinImgUrl;

        // Update title depending on currency
        document.title = janin.currency.name() + " " + ninja.translator.get("title");
        document.getElementById("siteTitle").alt = janin.currency.name() + " " + ninja.translator.get("title");

        // Update i18n link
        document.getElementById("cultureen").href = "?culture=en&currency=" + janin.currency.name().toLowerCase();
        document.getElementById("culturefr").href = "?culture=fr&currency=" + janin.currency.name().toLowerCase();
        document.getElementById("cultureru").href = "?culture=ru&currency=" + janin.currency.name().toLowerCase();
        document.getElementById("culturees").href = "?culture=es&currency=" + janin.currency.name().toLowerCase();

        if(ninja.seeder.isDone())
        {
            // Regenerate a new wallet when not expensive
            ninja.wallets.singlewallet.generateNewAddressAndKey();
            ninja.wallets.paperwallet.build(document.getElementById('paperpassphrase').value);
            ninja.wallets.brainwallet.view();
        }

        // Reset wallet tab when expensive or not applicable
        document.getElementById("bulktextarea").value = "";
        document.getElementById("suppliedPrivateKey").value = "";

        // easter egg doge ;)
        if(janin.currency.name() == "Dogecoin")
        {
            janin.doge = new Doge(['wow', 'so paper wallet', 'such random', 'very pretty', 'much design', 'awesome', 'much crypto', 'such coin', 'wow!!', 'to da moon']);
            return;
        }

        if(janin.doge != null)
        {
            janin.doge.stop();
            janin.doge = null;
        }
    },
};

janin.currencies = [
    //                    name, networkVersion, privateKeyPrefix, WIF_Start, CWIF_Start, donate
    janin.currency.createCurrency ("Komodo",         0x3c, 0xbc, "7",    "U"    , "")
                   ];

  </script>
  <script type="text/javascript">
var ninja = { wallets: {} };

ninja.privateKey = {
	isPrivateKey: function (key) {
		return (
					Bitcoin.ECKey.isWalletImportFormat(key) ||
					Bitcoin.ECKey.isCompressedWalletImportFormat(key) ||
					Bitcoin.ECKey.isHexFormat(key) ||
					Bitcoin.ECKey.isBase64Format(key) ||
					Bitcoin.ECKey.isMiniFormat(key)
				);
	},
	getECKeyFromAdding: function (privKey1, privKey2) {
		var n = EllipticCurve.getSECCurveByName("secp256k1").getN();
		var ecKey1 = new Bitcoin.ECKey(privKey1);
		var ecKey2 = new Bitcoin.ECKey(privKey2);
		// if both keys are the same return null
		if (ecKey1.getBitcoinHexFormat() == ecKey2.getBitcoinHexFormat()) return null;
		if (ecKey1 == null || ecKey2 == null) return null;
		var combinedPrivateKey = new Bitcoin.ECKey(ecKey1.priv.add(ecKey2.priv).mod(n));
		// compressed when both keys are compressed
		if (ecKey1.compressed && ecKey2.compressed) combinedPrivateKey.setCompressed(true);
		return combinedPrivateKey;
	},
	getECKeyFromMultiplying: function (privKey1, privKey2) {
		var n = EllipticCurve.getSECCurveByName("secp256k1").getN();
		var ecKey1 = new Bitcoin.ECKey(privKey1);
		var ecKey2 = new Bitcoin.ECKey(privKey2);
		// if both keys are the same return null
		if (ecKey1.getBitcoinHexFormat() == ecKey2.getBitcoinHexFormat()) return null;
		if (ecKey1 == null || ecKey2 == null) return null;
		var combinedPrivateKey = new Bitcoin.ECKey(ecKey1.priv.multiply(ecKey2.priv).mod(n));
		// compressed when both keys are compressed
		if (ecKey1.compressed && ecKey2.compressed) combinedPrivateKey.setCompressed(true);
		return combinedPrivateKey;
	},
	// 58 base58 characters starting with 6P
	isBIP38Format: function (key) {
		key = key.toString();
		return (/^6P[123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz]{56}$/.test(key));
	},
	BIP38EncryptedKeyToByteArrayAsync: function (base58Encrypted, passphrase, callback) {
		var hex;
		try {
			hex = Bitcoin.Base58.decode(base58Encrypted);
		} catch (e) {
			callback(new Error(ninja.translator.get("detailalertnotvalidprivatekey")));
			return;
		}

		// 43 bytes: 2 bytes prefix, 37 bytes payload, 4 bytes checksum
		if (hex.length != 43) {
			callback(new Error(ninja.translator.get("detailalertnotvalidprivatekey")));
			return;
		}
		// first byte is always 0x01
		else if (hex[0] != 0x01) {
			callback(new Error(ninja.translator.get("detailalertnotvalidprivatekey")));
			return;
		}

		var expChecksum = hex.slice(-4);
		hex = hex.slice(0, -4);
		var checksum = Bitcoin.Util.dsha256(hex);
		if (checksum[0] != expChecksum[0] || checksum[1] != expChecksum[1] || checksum[2] != expChecksum[2] || checksum[3] != expChecksum[3]) {
			callback(new Error(ninja.translator.get("detailalertnotvalidprivatekey")));
			return;
		}

		var isCompPoint = false;
		var isECMult = false;
		var hasLotSeq = false;
		// second byte for non-EC-multiplied key
		if (hex[1] == 0x42) {
			// key should use compression
			if (hex[2] == 0xe0) {
				isCompPoint = true;
			}
			// key should NOT use compression
			else if (hex[2] != 0xc0) {
				callback(new Error(ninja.translator.get("detailalertnotvalidprivatekey")));
				return;
			}
		}
		// second byte for EC-multiplied key
		else if (hex[1] == 0x43) {
			isECMult = true;
			isCompPoint = (hex[2] & 0x20) != 0;
			hasLotSeq = (hex[2] & 0x04) != 0;
			if ((hex[2] & 0x24) != hex[2]) {
				callback(new Error(ninja.translator.get("detailalertnotvalidprivatekey")));
				return;
			}
		}
		else {
			callback(new Error(ninja.translator.get("detailalertnotvalidprivatekey")));
			return;
		}

		var decrypted;
		var AES_opts = { mode: new Crypto.mode.ECB(Crypto.pad.NoPadding), asBytes: true };

		var verifyHashAndReturn = function () {
			var tmpkey = new Bitcoin.ECKey(decrypted); // decrypted using closure
			var base58AddrText = tmpkey.setCompressed(isCompPoint).getBitcoinAddress(); // isCompPoint using closure
			checksum = Bitcoin.Util.dsha256(base58AddrText); // checksum using closure

			if (checksum[0] != hex[3] || checksum[1] != hex[4] || checksum[2] != hex[5] || checksum[3] != hex[6]) {
				callback(new Error(ninja.translator.get("bip38alertincorrectpassphrase"))); // callback using closure
				return;
			}
			callback(tmpkey.getBitcoinPrivateKeyByteArray()); // callback using closure
		};

		if (!isECMult) {
			var addresshash = hex.slice(3, 7);
			Crypto_scrypt(passphrase, addresshash, 16384, 8, 8, 64, function (derivedBytes) {
				var k = derivedBytes.slice(32, 32 + 32);
				decrypted = Crypto.AES.decrypt(hex.slice(7, 7 + 32), k, AES_opts);
				for (var x = 0; x < 32; x++) decrypted[x] ^= derivedBytes[x];
				verifyHashAndReturn(); //TODO: pass in 'decrypted' as a param
			});
		}
		else {
			var ownerentropy = hex.slice(7, 7 + 8);
			var ownersalt = !hasLotSeq ? ownerentropy : ownerentropy.slice(0, 4);
			Crypto_scrypt(passphrase, ownersalt, 16384, 8, 8, 32, function (prefactorA) {
				var passfactor;
				if (!hasLotSeq) { // hasLotSeq using closure
					passfactor = prefactorA;
				} else {
					var prefactorB = prefactorA.concat(ownerentropy); // ownerentropy using closure
					passfactor = Bitcoin.Util.dsha256(prefactorB);
				}
				var kp = new Bitcoin.ECKey(passfactor);
				var passpoint = kp.setCompressed(true).getPub();

				var encryptedpart2 = hex.slice(23, 23 + 16);

				var addresshashplusownerentropy = hex.slice(3, 3 + 12);
				Crypto_scrypt(passpoint, addresshashplusownerentropy, 1024, 1, 1, 64, function (derived) {
					var k = derived.slice(32);

					var unencryptedpart2 = Crypto.AES.decrypt(encryptedpart2, k, AES_opts);
					for (var i = 0; i < 16; i++) { unencryptedpart2[i] ^= derived[i + 16]; }

					var encryptedpart1 = hex.slice(15, 15 + 8).concat(unencryptedpart2.slice(0, 0 + 8));
					var unencryptedpart1 = Crypto.AES.decrypt(encryptedpart1, k, AES_opts);
					for (var i = 0; i < 16; i++) { unencryptedpart1[i] ^= derived[i]; }

					var seedb = unencryptedpart1.slice(0, 0 + 16).concat(unencryptedpart2.slice(8, 8 + 8));

					var factorb = Bitcoin.Util.dsha256(seedb);

					var ps = EllipticCurve.getSECCurveByName("secp256k1");
					var privateKey = BigInteger.fromByteArrayUnsigned(passfactor).multiply(BigInteger.fromByteArrayUnsigned(factorb)).remainder(ps.getN());

					decrypted = privateKey.toByteArrayUnsigned();
					verifyHashAndReturn();
				});
			});
		}
	},
	BIP38PrivateKeyToEncryptedKeyAsync: function (base58Key, passphrase, compressed, callback) {
		var privKey = new Bitcoin.ECKey(base58Key);
		var privKeyBytes = privKey.getBitcoinPrivateKeyByteArray();
		var address = privKey.setCompressed(compressed).getBitcoinAddress();

		// compute sha256(sha256(address)) and take first 4 bytes
		var salt = Bitcoin.Util.dsha256(address).slice(0, 4);

		// derive key using scrypt
		var AES_opts = { mode: new Crypto.mode.ECB(Crypto.pad.NoPadding), asBytes: true };

		Crypto_scrypt(passphrase, salt, 16384, 8, 8, 64, function (derivedBytes) {
			for (var i = 0; i < 32; ++i) {
				privKeyBytes[i] ^= derivedBytes[i];
			}

			// 0x01 0x42 + flagbyte + salt + encryptedhalf1 + encryptedhalf2
			var flagByte = compressed ? 0xe0 : 0xc0;
			var encryptedKey = [0x01, 0x42, flagByte].concat(salt);
			encryptedKey = encryptedKey.concat(Crypto.AES.encrypt(privKeyBytes, derivedBytes.slice(32), AES_opts));
			encryptedKey = encryptedKey.concat(Bitcoin.Util.dsha256(encryptedKey).slice(0, 4));
			callback(Bitcoin.Base58.encode(encryptedKey));
		});
	},
	BIP38GenerateIntermediatePointAsync: function (passphrase, lotNum, sequenceNum, callback) {
		var noNumbers = lotNum === null || sequenceNum === null;
		var rng = new SecureRandom();
		var ownerEntropy, ownerSalt;

		if (noNumbers) {
			ownerSalt = ownerEntropy = new Array(8);
			rng.nextBytes(ownerEntropy);
		}
		else {
			// 1) generate 4 random bytes
			ownerSalt = new Array(4);

			rng.nextBytes(ownerSalt);

			// 2)  Encode the lot and sequence numbers as a 4 byte quantity (big-endian):
			// lotnumber * 4096 + sequencenumber. Call these four bytes lotsequence.
			var lotSequence = BigInteger(4096 * lotNum + sequenceNum).toByteArrayUnsigned();

			// 3) Concatenate ownersalt + lotsequence and call this ownerentropy.
			var ownerEntropy = ownerSalt.concat(lotSequence);
		}


		// 4) Derive a key from the passphrase using scrypt
		Crypto_scrypt(passphrase, ownerSalt, 16384, 8, 8, 32, function (prefactor) {
			// Take SHA256(SHA256(prefactor + ownerentropy)) and call this passfactor
			var passfactorBytes = noNumbers ? prefactor : Bitcoin.Util.dsha256(prefactor.concat(ownerEntropy));
			var passfactor = BigInteger.fromByteArrayUnsigned(passfactorBytes);

			// 5) Compute the elliptic curve point G * passfactor, and convert the result to compressed notation (33 bytes)
			var ellipticCurve = EllipticCurve.getSECCurveByName("secp256k1");
			var passpoint = ellipticCurve.getG().multiply(passfactor).getEncoded(1);

			// 6) Convey ownersalt and passpoint to the party generating the keys, along with a checksum to ensure integrity.
			// magic bytes "2C E9 B3 E1 FF 39 E2 51" followed by ownerentropy, and then passpoint
			var magicBytes = [0x2C, 0xE9, 0xB3, 0xE1, 0xFF, 0x39, 0xE2, 0x51];
			if (noNumbers) magicBytes[7] = 0x53;

			var intermediate = magicBytes.concat(ownerEntropy).concat(passpoint);

			// base58check encode
			intermediate = intermediate.concat(Bitcoin.Util.dsha256(intermediate).slice(0, 4));
			callback(Bitcoin.Base58.encode(intermediate));
		});
	},
	BIP38GenerateECAddressAsync: function (intermediate, compressed, callback) {
		// decode IPS
		var x = Bitcoin.Base58.decode(intermediate);
		//if(x.slice(49, 4) !== Bitcoin.Util.dsha256(x.slice(0,49)).slice(0,4)) {
		//	callback({error: 'Invalid intermediate passphrase string'});
		//}
		var noNumbers = (x[7] === 0x53);
		var ownerEntropy = x.slice(8, 8 + 8);
		var passpoint = x.slice(16, 16 + 33);

		// 1) Set flagbyte.
		// set bit 0x20 for compressed key
		// set bit 0x04 if ownerentropy contains a value for lotsequence
		var flagByte = (compressed ? 0x20 : 0x00) | (noNumbers ? 0x00 : 0x04);


		// 2) Generate 24 random bytes, call this seedb.
		var seedB = new Array(24);
		var rng = new SecureRandom();
		rng.nextBytes(seedB);

		// Take SHA256(SHA256(seedb)) to yield 32 bytes, call this factorb.
		var factorB = Bitcoin.Util.dsha256(seedB);

		// 3) ECMultiply passpoint by factorb. Use the resulting EC point as a public key and hash it into a Bitcoin
		// address using either compressed or uncompressed public key methodology (specify which methodology is used
		// inside flagbyte). This is the generated Bitcoin address, call it generatedaddress.
		var ec = EllipticCurve.getSECCurveByName("secp256k1").getCurve();
		var generatedPoint = ec.decodePointHex(ninja.publicKey.getHexFromByteArray(passpoint));
		var generatedBytes = generatedPoint.multiply(BigInteger.fromByteArrayUnsigned(factorB)).getEncoded(compressed);
		var generatedAddress = (new Bitcoin.Address(Bitcoin.Util.sha256ripe160(generatedBytes))).toString();

		// 4) Take the first four bytes of SHA256(SHA256(generatedaddress)) and call it addresshash.
		var addressHash = Bitcoin.Util.dsha256(generatedAddress).slice(0, 4);

		// 5) Now we will encrypt seedb. Derive a second key from passpoint using scrypt
		Crypto_scrypt(passpoint, addressHash.concat(ownerEntropy), 1024, 1, 1, 64, function (derivedBytes) {
			// 6) Do AES256Encrypt(seedb[0...15]] xor derivedhalf1[0...15], derivedhalf2), call the 16-byte result encryptedpart1
			for (var i = 0; i < 16; ++i) {
				seedB[i] ^= derivedBytes[i];
			}
			var AES_opts = { mode: new Crypto.mode.ECB(Crypto.pad.NoPadding), asBytes: true };
			var encryptedPart1 = Crypto.AES.encrypt(seedB.slice(0, 16), derivedBytes.slice(32), AES_opts);

			// 7) Do AES256Encrypt((encryptedpart1[8...15] + seedb[16...23]) xor derivedhalf1[16...31], derivedhalf2), call the 16-byte result encryptedseedb.
			var message2 = encryptedPart1.slice(8, 8 + 8).concat(seedB.slice(16, 16 + 8));
			for (var i = 0; i < 16; ++i) {
				message2[i] ^= derivedBytes[i + 16];
			}
			var encryptedSeedB = Crypto.AES.encrypt(message2, derivedBytes.slice(32), AES_opts);

			// 0x01 0x43 + flagbyte + addresshash + ownerentropy + encryptedpart1[0...7] + encryptedpart2
			var encryptedKey = [0x01, 0x43, flagByte].concat(addressHash).concat(ownerEntropy).concat(encryptedPart1.slice(0, 8)).concat(encryptedSeedB);

			// base58check encode
			encryptedKey = encryptedKey.concat(Bitcoin.Util.dsha256(encryptedKey).slice(0, 4));
			callback(generatedAddress, Bitcoin.Base58.encode(encryptedKey));
		});
	}
};

ninja.publicKey = {
	isPublicKeyHexFormat: function (key) {
		key = key.toString();
		return ninja.publicKey.isUncompressedPublicKeyHexFormat(key) || ninja.publicKey.isCompressedPublicKeyHexFormat(key);
	},
	// 130 characters [0-9A-F] starts with 04
	isUncompressedPublicKeyHexFormat: function (key) {
		key = key.toString();
		return /^04[A-Fa-f0-9]{128}$/.test(key);
	},
	// 66 characters [0-9A-F] starts with 02 or 03
	isCompressedPublicKeyHexFormat: function (key) {
		key = key.toString();
		return /^0[2-3][A-Fa-f0-9]{64}$/.test(key);
	},
	getBitcoinAddressFromByteArray: function (pubKeyByteArray) {
		var pubKeyHash = Bitcoin.Util.sha256ripe160(pubKeyByteArray);
		var addr = new Bitcoin.Address(pubKeyHash);
		return addr.toString();
	},
	getHexFromByteArray: function (pubKeyByteArray) {
		return Crypto.util.bytesToHex(pubKeyByteArray).toString().toUpperCase();
	},
	getByteArrayFromAdding: function (pubKeyHex1, pubKeyHex2) {
		var ecparams = EllipticCurve.getSECCurveByName("secp256k1");
		var curve = ecparams.getCurve();
		var ecPoint1 = curve.decodePointHex(pubKeyHex1);
		var ecPoint2 = curve.decodePointHex(pubKeyHex2);
		// if both points are the same return null
		if (ecPoint1.equals(ecPoint2)) return null;
		var compressed = (ecPoint1.compressed && ecPoint2.compressed);
		var pubKey = ecPoint1.add(ecPoint2).getEncoded(compressed);
		return pubKey;
	},
	getByteArrayFromMultiplying: function (pubKeyHex, ecKey) {
		var ecparams = EllipticCurve.getSECCurveByName("secp256k1");
		var ecPoint = ecparams.getCurve().decodePointHex(pubKeyHex);
		var compressed = (ecPoint.compressed && ecKey.compressed);
		// if both points are the same return null
		ecKey.setCompressed(false);
		if (ecPoint.equals(ecKey.getPubPoint())) {
			return null;
		}
		var bigInt = ecKey.priv;
		var pubKey = ecPoint.multiply(bigInt).getEncoded(compressed);
		return pubKey;
	},
	// used by unit test
	getDecompressedPubKeyHex: function (pubKeyHexComp) {
		var ecparams = EllipticCurve.getSECCurveByName("secp256k1");
		var ecPoint = ecparams.getCurve().decodePointHex(pubKeyHexComp);
		var pubByteArray = ecPoint.getEncoded(0);
		var pubHexUncompressed = ninja.publicKey.getHexFromByteArray(pubByteArray);
		return pubHexUncompressed;
	}
};
  </script>
  <script type="text/javascript">
	ninja.seeder = {
		init: (function () {
			document.getElementById("generatekeyinput").value = "";
		})(),

		// number of mouse movements to wait for
		seedLimit: (function () {
			var num = Crypto.util.randomBytes(12)[11];
			return 200 + Math.floor(num);
		})(),

		seedCount: 0, // counter
		lastInputTime: new Date().getTime(),
		seedPoints: [],

		isDone: function() {
			return ninja.seeder.seedCount >= ninja.seeder.seedLimit;
		},

		// seed function exists to wait for mouse movement to add more entropy before generating an address
		seed: function (evt) {
			if (!evt) var evt = window.event;
			var timeStamp = new Date().getTime();
			// seeding is over now we generate and display the address
			if (ninja.seeder.seedCount == ninja.seeder.seedLimit) {
				ninja.seeder.seedCount++;
				ninja.wallets.singlewallet.open();
				document.getElementById("menu").style.visibility = "visible";
				ninja.seeder.removePoints();
			}
			// seed mouse position X and Y when mouse movements are greater than 40ms apart.
			else if ((ninja.seeder.seedCount < ninja.seeder.seedLimit) && evt && (timeStamp - ninja.seeder.lastInputTime) > 40) {
				SecureRandom.seedTime();
				SecureRandom.seedInt16((evt.clientX * evt.clientY));
				ninja.seeder.showPoint(evt.clientX, evt.clientY);
				ninja.seeder.seedCount++;
				ninja.seeder.lastInputTime = new Date().getTime();
				ninja.seeder.showPool();
			}
		},

		// seed function exists to wait for mouse movement to add more entropy before generating an address
		seedKeyPress: function (evt) {
			if (!evt) var evt = window.event;
			// seeding is over now we generate and display the address
			if (ninja.seeder.seedCount == ninja.seeder.seedLimit) {
				ninja.seeder.seedCount++;
				ninja.wallets.singlewallet.open();
				document.getElementById("generate").style.display = "none";
				document.getElementById("menu").style.visibility = "visible";
				ninja.seeder.removePoints();
			}
			// seed key press character
			else if ((ninja.seeder.seedCount < ninja.seeder.seedLimit) && evt.which) {
				var timeStamp = new Date().getTime();
				// seed a bunch (minimum seedLimit) of times
				SecureRandom.seedTime();
				SecureRandom.seedInt8(evt.which);
				var keyPressTimeDiff = timeStamp - ninja.seeder.lastInputTime;
				SecureRandom.seedInt8(keyPressTimeDiff);
				ninja.seeder.seedCount++;
				ninja.seeder.lastInputTime = new Date().getTime();
				ninja.seeder.showPool();
			}
		},

		showPool: function () {
			var poolHex = Crypto.util.bytesToHex(SecureRandom.pool);
			document.getElementById("seedpool").innerHTML = poolHex;
			document.getElementById("seedpooldisplay").innerHTML = poolHex;
			document.getElementById("mousemovelimit").innerHTML = (ninja.seeder.seedLimit - ninja.seeder.seedCount);
		},

		showPoint: function (x, y) {
			var div = document.createElement("div");
			div.setAttribute("class", "seedpoint");
			div.style.top = y + "px";
			div.style.left = x + "px";

			// let's make the entropy 'points' grow and change color!
			percentageComplete = ninja.seeder.seedCount / ninja.seeder.seedLimit;
			document.getElementById("progress-bar-percentage").style.width=Math.ceil(percentageComplete*100)+"%";

			// for some reason, appending these divs to an IOS device breaks clicking altogether (?)
			if (navigator.platform != 'iPad' && navigator.platform != 'iPhone' && navigator.platform != 'iPod') {
				document.body.appendChild(div);
			}
			ninja.seeder.seedPoints.push(div);
		},

		removePoints: function () {
			for (var i = 0; i < ninja.seeder.seedPoints.length; i++) {
				document.body.removeChild(ninja.seeder.seedPoints[i]);
			}
			ninja.seeder.seedPoints = [];
		}
	};

ninja.qrCode = {
	// determine which type number is big enough for the input text length
	getTypeNumber: function (text) {
		var lengthCalculation = text.length * 8 + 12; // length as calculated by the QRCode
		if (lengthCalculation < 72) { return 1; }
		else if (lengthCalculation < 128) { return 2; }
		else if (lengthCalculation < 208) { return 3; }
		else if (lengthCalculation < 288) { return 4; }
		else if (lengthCalculation < 368) { return 5; }
		else if (lengthCalculation < 480) { return 6; }
		else if (lengthCalculation < 528) { return 7; }
		else if (lengthCalculation < 688) { return 8; }
		else if (lengthCalculation < 800) { return 9; }
		else if (lengthCalculation < 976) { return 10; }
		return null;
	},

	createCanvas: function (text, sizeMultiplier) {
		sizeMultiplier = (sizeMultiplier == undefined) ? 2 : sizeMultiplier; // default 2
		// create the qrcode itself
		var typeNumber = ninja.qrCode.getTypeNumber(text);
		var qrcode = new QRCode(typeNumber, QRCode.ErrorCorrectLevel.H);
		qrcode.addData(text);
		qrcode.make();
		var width = qrcode.getModuleCount() * sizeMultiplier;
		var height = qrcode.getModuleCount() * sizeMultiplier;
		// create canvas element
		var canvas = document.createElement('canvas');
		var scale = 10.0;
		canvas.width = width * scale;
		canvas.height = height * scale;
		canvas.style.width = width + 'px';
		canvas.style.height = height + 'px';
		var ctx = canvas.getContext('2d');
		ctx.scale(scale, scale);
		// compute tileW/tileH based on width/height
		var tileW = width / qrcode.getModuleCount();
		var tileH = height / qrcode.getModuleCount();
		// draw in the canvas
		for (var row = 0; row < qrcode.getModuleCount(); row++) {
			for (var col = 0; col < qrcode.getModuleCount(); col++) {
				ctx.fillStyle = qrcode.isDark(row, col) ? "#000000" : "#ffffff";
				ctx.fillRect(col * tileW, row * tileH, tileW, tileH);
			}
		}
		// return just built canvas
		return canvas;
	},

	// generate a QRCode and return it's representation as an Html table
	createTableHtml: function (text) {
		var typeNumber = ninja.qrCode.getTypeNumber(text);
		var qr = new QRCode(typeNumber, QRCode.ErrorCorrectLevel.H);
		qr.addData(text);
		qr.make();
		var tableHtml = "<table class='qrcodetable'>";
		for (var r = 0; r < qr.getModuleCount(); r++) {
			tableHtml += "<tr>";
			for (var c = 0; c < qr.getModuleCount(); c++) {
				if (qr.isDark(r, c)) {
					tableHtml += "<td class='qrcodetddark'/>";
				} else {
					tableHtml += "<td class='qrcodetdlight'/>";
				}
			}
			tableHtml += "</tr>";
		}
		tableHtml += "</table>";
		return tableHtml;
	},

	// show QRCodes with canvas OR table (IE8)
	// parameter: keyValuePair
	// example: { "id1": "string1", "id2": "string2"}
	//		"id1" is the id of a div element where you want a QRCode inserted.
	//		"string1" is the string you want encoded into the QRCode.
	showQrCode: function (keyValuePair, sizeMultiplier) {
		for (var key in keyValuePair) {
			var value = keyValuePair[key];
			try {
				if (document.getElementById(key)) {
					document.getElementById(key).innerHTML = "";
					document.getElementById(key).appendChild(ninja.qrCode.createCanvas(value, sizeMultiplier));
				}
			}
			catch (e) {
				// for browsers that do not support canvas (IE8)
				document.getElementById(key).innerHTML = ninja.qrCode.createTableHtml(value);
			}
		}
	}
};

ninja.tabSwitch = function (walletTab) {
	if (walletTab.className.indexOf("selected") == -1) {
		// unselect all tabs
		for (var wType in ninja.wallets) {
			if (document.getElementById(wType)) {
				document.getElementById(wType).className = "tab";
				ninja.wallets[wType].close();
			}
		}
		walletTab.className += " selected";
		ninja.wallets[walletTab.getAttribute("id")].open();
	}
};

ninja.envSecurityCheck = function() {
	var innerHTML = "";
	switch(window.location.protocol) {
		case 'http:':
		case 'https:':
			innerHTML = '<span style="color: #990000;">' + ninja.translator.get("securitychecklistofflineNOK") + '</span>';
			break;
		case 'file:':
			innerHTML = '<span style="color: #009900;">' + ninja.translator.get("securitychecklistofflineOK") + '</span>';
			break;
		default:
	}
	document.getElementById('envSecurityCheck').innerHTML = innerHTML;
};

ninja.browserSecurityCheck = function() {
	var innerHTML = "";
	if (window.crypto && window.crypto.getRandomValues) {
		innerHTML = '<span style="color: #009900;">' + ninja.translator.get("securitychecklistrandomOK") + '</span>';
	} else {
		innerHTML = '<span style="color: #990000;">' + ninja.translator.get("securitychecklistrandomNOK") + '</span>';
	}
	document.getElementById('browserSecurityCheck').innerHTML = innerHTML;
}

ninja.getQueryString = function () {
	var result = {}, queryString = location.search.substring(1), re = /([^&=]+)=([^&]*)/g, m;
	while (m = re.exec(queryString)) {
		result[decodeURIComponent(m[1])] = decodeURIComponent(m[2]);
	}
	return result;
};

// use when passing an Array of Functions
ninja.runSerialized = function (functions, onComplete) {
	onComplete = onComplete || function () { };

	if (functions.length === 0) onComplete();
	else {
		// run the first function, and make it call this
		// function when finished with the rest of the list
		var f = functions.shift();
		f(function () { ninja.runSerialized(functions, onComplete); });
	}
};

ninja.forSerialized = function (initial, max, whatToDo, onComplete) {
	onComplete = onComplete || function () { };

	if (initial === max) { onComplete(); }
	else {
		// same idea as runSerialized
		whatToDo(initial, function () { ninja.forSerialized(++initial, max, whatToDo, onComplete); });
	}
};

// use when passing an Object (dictionary) of Functions
ninja.foreachSerialized = function (collection, whatToDo, onComplete) {
	var keys = [];
	for (var name in collection) {
		keys.push(name);
	}
	ninja.forSerialized(0, keys.length, function (i, callback) {
		whatToDo(keys[i], callback);
	}, onComplete);
};

ninja.toggleFaqQuestion = function (elementId) {
	var answerDiv = document.getElementById(elementId);
	answerDiv.style.display = answerDiv.style.display == "block" ? "none" : "block";
};
  </script>
  <script type="text/javascript">
ninja.translator = {
	currentCulture: "en",

	autodetectTranslation: function() {
		// window.navigator.language for Firefox / Chrome / Opera Safari
		// window.navigator.userLanguage for IE
		var language = window.navigator.language || window.navigator.userLanguage;
		if (!ninja.translator.translate(language)) {
			// Try to remove part after dash, for example cs-CZ -> cs
			language = language.substr(0, language.indexOf('-'));
			ninja.translator.translate(language);
		}
	},

	translate: function (culture) {
		var dict = ninja.translator.translations[culture];
		if (dict) {
			// set current culture
			ninja.translator.currentCulture = culture;
			// update menu UI
			for (var cult in ninja.translator.translations) {
				document.getElementById("culture" + cult).setAttribute("class", "");
			}
			document.getElementById("culture" + culture).setAttribute("class", "selected");
			// apply translations for each know id
			for (var id in dict) {
				if (document.getElementById(id) && document.getElementById(id).value) {
					document.getElementById(id).value = dict[id];
				}
				else if (document.getElementById(id)) {
					document.getElementById(id).innerHTML = dict[id];
				}
			}
			return true;
		}
		return false;
	},

	get: function (id) {
		var translation = ninja.translator.translations[ninja.translator.currentCulture][id];
		return translation;
	},

    staticID: [
        "defaultTitle",
        "title",
        "brainalertpassphrasewarning",
        "brainalertpassphrasetooshort",
        "brainalertpassphrasedoesnotmatch",
        "bulkgeneratingaddresses",
        "bip38alertincorrectpassphrase",
        "bip38alertpassphraserequired",
        "detailconfirmsha256",
        "detailalertnotvalidprivatekey",
		"securitychecklistrandomOK",
		"securitychecklistrandomNOK",
		"securitychecklistofflineNOK",
		"securitychecklistofflineOK",
		"paperwalletback",
    ],

	translations: {
    ﻿"en": {
    "defaultTitle" : "WalletGenerator.net - Universal Paper wallet generator for Bitcoin and other cryptocurrencies",
    "title" : "Paper Wallet Generator",
    "bulkgeneratingaddresses": "Generating addresses... ",
    "brainalertpassphrasetooshort": "The passphrase you entered is too short.\n\n",
    "brainalertpassphrasewarning": "Warning: Choosing a strong passphrase is important to avoid brute force attempts to guess your passphrase and steal your coins.",
    "brainalertpassphrasedoesnotmatch": "The passphrase does not match the confirm passphrase.",
    "detailalertnotvalidprivatekey": "The text you entered is not a valid Private Key",
    "detailconfirmsha256": "The text you entered is not a valid Private Key!\n\nWould you like to use the entered text as a passphrase and create a Private Key using a SHA256 hash of the passphrase?\n\nWarning: Choosing a strong passphrase is important to avoid brute force attempts to guess your passphrase and steal your coins.",
    "bip38alertincorrectpassphrase": "Incorrect passphrase for this encrypted private key.",
    "bip38alertpassphraserequired": "Passphrase required for BIP38 key",
	"securitychecklistrandomOK": "Your browser is capable of generating cryptographically random keys using window.crypto.getRandomValues",
	"securitychecklistrandomNOK": "Your browser does NOT support window.crypto.getRandomValues(), which is important for generating the most secure random numbers possible. Please use a more modern browser.",
	"securitychecklistofflineNOK": "You appear to be running this generator off of a live website, which is not recommended for creating valuable wallets. Instead, use the download link at the bottom of this page to download the ZIP file from GitHub and run this generator offline as a \'local\' HTML file.",
	"securitychecklistofflineOK": "You are running this generator from your own download.",
	"paperwalletback": "<ul><li>To deposit funds to this paper wallet, send cryptocurrency to its public address, anytime.</li><li>Verify your balance by searching for the public address using a blockchain explorer such as blockchain.info.</li><li><b>DO NOT REVEAL THE PRIVATE KEY</b> until you are ready to import the balance on this wallet to a cryptocurrency client, exchange or online wallet.</li></ul><b>Amount :</b> ___________ &nbsp; &nbsp; &nbsp; <b>Date :</b> ________________<br /><b>Notes :</b> ______________________________________",
},

    ﻿"fr": {
	"choosecurrency": "Choisissez une monnaie",
	"singlewallet": "Porte-Monnaie Simple",
	"paperwallet": "Porte-Monnaie Papier",
	"bulkwallet": "Porte-Monnaie En Vrac",
	"brainwallet": "Porte-Monnaie Cerveau",
	"detailwallet": "Détails du Porte-Monnaie",
	"donate": "Soutien",
	"generatelabelbitcoinaddress": "Génération d'une nouvelle adresse...",
	"generatelabelmovemouse": "BOUGEZ votre souris pour ajouter de l'entropie...",
	"generatelabelkeypress": "OU tapez des lettres aléatoires dans le champ texte",
	"skipMessage": "Vous pouvez passer cette étape si vous ne voulez pas générer de porte-monnaie",
	"singlelabelbitcoinaddress": "Adresse publique",
	"singleshare": "PUBLIQUE",
	"singlelabelprivatekey": "Clé privée (format WIF)",
	"singlesecret": "SECRET",
	"securitystep0title": "Étape 0. Suivez les recommandations de la liste de sécurité",
	"securitystep0": "La première étape est de <strong>télécharger</strong> ce site à partir de <a href=\"https://github.com/MichaelMure/PaperWallet/archive/master.zip\">Github</a> et d'ouvrir le fichier index.html directement sur votre ordinateur. Il est beaucoup trop facile d'ajouter du code malicieux dans les 6000+ lignes de javascript pour transmettre votre clé privée, et vous ne voulez pas voir vos fonds volés, n'est-ce pas ? Le versionnage de code source rend bien plus facile la vérification par des personnes extérieures du code qui est exécuté. Pour une sécurité supplémentaire, <strong>débranchez votre accès Internet</strong> pendant la génération de votre porte-monnaie.",
	"securitystep1title": "Étape 1. Générez une nouvelle adresse",
	"securitystep1": "Choisissez votre monnaie et cliquez sur le bouton \"Générer une nouvelle adresse\".",
	"securitystep2title": "Étape 2. Imprimez votre porte-monnaie",
	"securitystep2": "Cliquez sur l'onglet \"Porte-Monnaie Papier\" et imprimez la page en haute qualité. <strong>Ne sauvegardez jamais la page au format PDF, car un fichier est plus susceptible d'être piraté qu'une feuille de papier.</strong>",
	"securitystep3title": "Étape 3. Pliez le porte-monnaie papier",
	"securitystep3": "Pliez votre nouveau porte-monnaie papier en suivant les lignes.\n<img src=\"images/foldinginstructions.png\" alt=\"Pliez en deux dans le sens de la longueur, puis en trois dans le sens de la largeur.\"><br>\nVous pouvez insérer un coté dans l'autre pour fermer le porte-monnaie.",
	"securitystep4title": "Étape 4. Partagez votre adresse publique",
	"securitystep4": "Transmettez votre adresse publique pour recevoir de l'argent d'autres utilisateurs de cette monnaie. Vous pouvez partager l'adresse publique autant que vous voulez.",
	"securitystep5title": "Étape 5. Gardez votre clé privée secrète",
	"securitystep5": "Votre clé privée est littéralement la clé pour accéder à votre argent. Si quelqu'un y accédait, il pourrait utiliser tous les fonds actuellement sur le porte-monnaie, ainsi que tous les fonds qui seront déposés dans le futur.",
	"securitystep6": "Faites un essai avec un montant faible avant de recevoir des paiements importants.",
	"securitychecktitle": "Liste de sécurité :",
	"securitychecklivecd": "Utilisez vous un système d'exploitation garanti sans malware ou virus, comme par exemple un live-CD Ubuntu ?",
	"supportedcurrencylbl": "monnaies supportées !",
	"paperlabelencrypt": "Chiffrer en BIP38 ?",
	"paperlabelBIPpassphrase": "Phrase de passe:",
	"bulklabelstartindex": "Index de départ:",
	"bulklabelrowstogenerate": "Quantité à générer:",
	"bulklabelcompressed": "Compresser les adresses ?",
	"bulklabelcsv": "Valeurs Séparées Par Des Virgules (CSV): Index, Adresse, Clé privée (WIF)",
	"brainlabelenterpassphraselbl": "Phrase de passe:",
	"brainlabelconfirmlbl": "Confirmer la phrase de passe:",
	"brainalgorithm": "Algorithme: SHA256(phrase de passe)",
	"brainlabelbitcoinaddress": "Adresse publique",
	"brainlabelprivatekey": "Clé privée (format WIF):",
	"detaillabelenterprivatekey": "Entrez votre clé privée",
	"qrcaminstructiontitle": "Scannez votre QR code avec votre webcam",
	"paperqrnotsupported": "Désolé, mais votre navigateur ne supporte pas les contrôles de webcam HTML5. Essayez avec une version récente de Firefox (recommandé), de Chrome ou d'Opera",
	"paperqrpermissiondenied": "<p>Permission refusée. Votre navigateur devrait afficher un message demandant l'autorisation d'accéder à votre webcam. Cliquez sur le bouton \"Autoriser\" pour activer la webcam.</p>",
	"detaillabelpassphrase": "Phrase de passe BIP38",
	"detaillabelnote1": "Votre clé privée est un nombre secret unique que seul vous connaissez. Elle peut être encodé selon différents formats. Ci-dessous s'affiche l'adresse publique et la clé publique qui correspond à votre clé privée, ainsi que votre clé privée dans les formats les plus populaires (WIF, WIFC, HEX, B64).",
	"detaillabelbitcoinaddress": "Adresse publique",
	"detaillabelbitcoinaddresscomp": "Adresse publique compressée",
	"detaillabelpublickey": "Clé publique (130 caractères [0-9A-F]):",
	"detaillabelpublickeycomp": "Clé publique compressée (66 caractères [0-9A-F]):",
	"detaillabelprivwif": "Clé privée WIF<br>51 caractère Base58",
	"detaillabelprivwifcomp": "Clé privée WIF compressée<br>52 caractères Base58",
	"detaillabelprivhex": "Clé privée en hexadécimal (64 caractères [0-9A-F]):",
	"detaillabelprivb64": "Clé privée en Base64 (44 caractères):",
	"detaillabelprivmini": "Clé privée au format MINI (22, 26 or 30 caractères):",
	"detaillabelprivb6": "Clé privée en Base6 (99 caractères [0-5]):",
	"detaillabelprivbip38": "Clé privée chiffrée au format BIP38 (58 caractères Base58):",
	"detaillabelq1": "Comment générer un porte-monnaie avec des dés ? Qu'est-ce que la Base6 (B6) ?",
	"detaila1": "Une partie importante de la création d'un porte-monnaie pour les monnaies cryptographiques est de s'assurer que les nombres aléatoires utilisés pour la génération sont réellement aléatoires. L'aléatoire d'origine physique est bien meilleur que le pseudo-aléatoire généré par un ordinateur. La façon la plus facile de générer de l'aléatoire physique est d'utiliser des dés. Pour générer une clé privée, vous avez uniquement besoin d'un dé à 6 faces que vous allez lancer 99 fois. Arrêtez-vous après chaque lancé pour noter la valeur. Pour noter la valeur, suivez les règles suivantes: 1=1, 2=2, 3=3, 4=4, 5=5, 6=0. En faisant ça, vous générez un grand nombre aléatoire, votre clé privée, en Base6 (B6). Vous pouvez ensuite entrer les 99 caractères B6 de votre clé privée dans le champs texte au dessus et cliquer sur \"View Details\". Vous verrez ensuite l'adresse publique associée à cette clé privée. Vous devrez également noter votre clé privée au format WIF, car il est plus courant d'usage que la clé privée brute.",
	"donatetextfooter": "Pour soutenir le développement de ce générateur de porte-monnaie, vous pouvez faire une donation grâce aux adresses suivante. Quand le support pour une monnaie a été ajouté par un contributeur externe au projet, les donations lui parviennent directement.",
	"footersupport": "Soutenir WalletGenerator.net",
	"footerlabelgithub": "Télécharger (dépôt GitHub)",
	"footerlabelcopyright2": "Les licences javascript sont incluses dans le code source.",
	"footerlabelnowarranty": "Aucune garantie.",
	"defaultTitle": "Générateur de porte-monnaie papier universel pour Bitcoin et autres monnaies cryptographiques",
	"title": "Générateur de porte-monnaie papier",
	"brainalertpassphrasewarning": "Attention: choisir une passe de phrase forte est important pour éviter les attaques par bruteforce, pour deviner votre phrase de passe et voler vos fonds.",
	"brainalertpassphrasetooshort": "La phrase de passe entrée est trop courte.",
	"brainalertpassphrasedoesnotmatch": "Les deux phrases de passe ne correspondent pas.",
	"bulkgeneratingaddresses": "Génération en cours des adresses...",
	"bip38alertincorrectpassphrase": "Phrase de passe incorrecte pour cette clé privée chiffrée.",
	"bip38alertpassphraserequired": "Phrase de passe requise pour une clé chiffrée BIP38.",
	"detailconfirmsha256": "Le texte que vous avez entré n'est pas une clé privée valide !\nVoulez vous utiliser le texte comme une phrase de passe et générer une clé privée en prenant un hash SHA256 de cette phrase ?\n\nAttention: Choisir un mot de passe solide est important pour vous protéger des attaques bruteforce visant à trouver votre mot de passe et voler vos fonds.",
	"detailalertnotvalidprivatekey": "Le texte que vous avez entré n'est pas une clé privée valide",
	"securitychecklistrandomOK": "Votre navigateur est capable de générer des clés cryptographiques sécurisés en utilisant window.crypto.getRandomValues",
	"securitychecklistrandomNOK": "Votre navigateur ne supporte PAS window.crypto.getRandomValues(), ce qui est important pour générer des portes-monnaies les plus sécurisé possible. Utilisez un navigateur plus moderne.",
	"securitychecklistofflineNOK": "Il semble que vous utilisez ce générateur directement depuis le site web, ce qui n'est pas recommandé pour générer des portes-monnaie. A la place, utilisez le lien de téléchargement en bas de cette page pour télécharger une archive ZIP depuis Github et lancez ce générateur hors-ligne comme un fichier HTML local.",
	"securitychecklistofflineOK": "Vous exécutez ce générateur depuis votre propre téléchargement.",
	"paperwalletback": "<ul><li>Pour transférer des fonds sur ce porte-monnaie, envoyez des fonds à l'adresse publique, à n'importe quel moment.</li><li>Vérifier votre solde en cherchant l'adresse publique dans un explorateur de Blockchain.</li><li><b>NE REVELEZ PAS VOTRE CLE PRIVEE</b> jusqu'au moment où vous voudrez importer votre solde dans un porte-monnaie logiciel.</li></ul><b>Montant :</b> ___________       <b>Date :</b> ________________<br /><b>Notes :</b> ______________________________________",
},
    "ru": {
	"choosecurrency": "Выберите валюту",
	"singlewallet": "Единичный кошелек",
	"paperwallet": "Бумажный кошелек",
	"bulkwallet": "Несколько кошельков",
	"brainwallet": "\"Умственный\" кошелек",
	"detailwallet": "Подробности о кошельке",
	"donate": "Поддержка",
	"generatelabelbitcoinaddress": "Создается новый адрес...",
	"generatelabelmovemouse": "ПОДВИГАЙТЕ мышкой, чтобы сделать генерацию немного более случайной...",
	"generatelabelkeypress": "ИЛИ введите случайные символы в это поле для текста",
	"skipMessage": "Этот шаг можно пропустить, если вы не планируете использовать генератор случайных ключей",
	"singlelabelbitcoinaddress": "Открытый адрес",
	"singleshare": "ОТКРЫТЫЙ",
	"singlelabelprivatekey": "Закрытый ключ (в формате импорта в кошелек - WIF)",
	"singlesecret": "СЕКРЕТНЫЙ",
	"securitystep0title": "Шаг 0. Следуйте рекомендациям в \"Перечне безопасности\"",
	"securitystep0": "Первым делом <strong>скачайте</strong> этот веб-сайт с <a href=\"https://github.com/MichaelMure/PaperWallet/archive/master.zip\">Github</a>, распакуйте и откройте файл index.html непосредственно с Вашего компьютера. Cпрятать вредоносный код в 6000+ строках javascript, чтобы украсть Ваш закрытый ключ, довольно просто, а Вам вряд ли этого хочется. Система контроля версий позволяет значительно упростить взаимную проверку исполняемого кода. Для большей безопасности <strong>отключитесь от Интернета</strong> на время создания кошелька.",
	"securitystep1title": "Шаг 1. Создайте новый адрес",
	"securitystep1": "Выберите Вашу валюту и нажмите на кнопку \"Создать новый адрес\"",
	"securitystep2title": "Шаг 2. Распечатайте бумажный кошелек",
	"securitystep2": "Нажмите на закладку \"Бумажный кошелек\" и распечатайте страницу в высоком качестве. <strong>Никогда не сохраняйте страницу как PDF файл для последующей печати, поскольку файл имеет гораздо более высокие шансы быть скомпрометированным, чем бумага.</strong>",
	"securitystep3title": "Шаг 3. Сверните бумажный кошелек",
	"securitystep3": " Сверните Ваш новый бумажный кошелек согласно линиям\n<img src=\"images/foldinginstructions.png\" alt=\"Согните пополам продольно, а потом каждую треть поперечно.\"><br>\nВы можете вставить одну часть внутрь другой, чтобы скрепить кошелек.",
	"securitystep4title": "Шаг 4. Делитесь Вашим открытым адресом",
	"securitystep4": "Используйте Ваш открытый адрес для получения денег от других пользователей крипто-валюты. Вы можете делиться открытым адресом сколько угодно раз.",
	"securitystep5title": "Шаг 5. Держите Ваш закрытый ключ в тайне",
	"securitystep5": "Закрытый ключ, по сути, и есть Ваши деньги - если кто-то получит к нему доступ, он сможет не только вывести деньги, которые будут там находиться на тот момент, но и выводить все средства, получаемые на этот кошелек в будущем.",
	"securitystep6": "Перед тем как получать значительные платежи, пожалуйста, попробуйте вывести с кошелька небольшие суммы.",
	"securitychecktitle": "Перечень безопасности :",
	"securitychecklivecd": "Используете ли Вы безопасную операционную систему, гарантированно чистую от различного рода шпионских программ и вирусов, например, такую как Ubuntu LiveCD?",
	"supportedcurrencylbl": "валют доступно !",
	"paperlabelencrypt": "Зашифровать с помощью BIP38?",
	"paperlabelBIPpassphrase": "Кодовая фраза:",
	"bulklabelstartindex": "Начальный индекс:",
	"bulklabelrowstogenerate": "Количество генерируемых строк:",
	"bulklabelcompressed": "Создавать сжатые адреса?",
	"bulklabelcsv": "Значения, разделенные запятой: Индекс,Адрес,Закрытый ключ (WIF)",
	"brainlabelenterpassphraselbl": "Введите кодовую фразу:",
	"brainlabelconfirmlbl": "Подтвердите кодовую фразу:",
	"brainalgorithm": "Алгоритм: SHA256(кодовая фраза)",
	"brainlabelbitcoinaddress": "Открытый адрес:",
	"brainlabelprivatekey": "Закрытый ключ (в формате импорта в кошелек - WIF):",
	"detaillabelenterprivatekey": "Введите закрытый ключ",
	"qrcaminstructiontitle": "Сосканируйте QR-код с помощью Вашей камеры",
	"paperqrnotsupported": "К сожалению, Ваш браузер не поддерживает возможности HTML5 по управлению камерой. Попробуйте использовать свежую версию Firefox (рекомендуется), Chrome или Opera.",
	"paperqrpermissiondenied": "<p>Доступ запрещен. Ваш браюзер должен отобразить сообщение с запросом доступа к Вашей камере. Пожалуйста, нажмите кнопку \"Разрешить\", чтобы предоставить доступ к Вашей камере.</p>",
	"detaillabelpassphrase": "Введите кодовую фразу BIP38",
	"detaillabelnote1": "Ваш закрытый ключ - это уникальный секретный номер, который знаете только Вы. Он может быть представлен в различных форматах. Ниже показаны открытый адрес и открытый ключ, соответствующие Вашему закрытому ключу, а также Ваш закрытый ключ в наиболее популярных форматах (WIF, WIFC, HEX, B64).",
	"detaillabelbitcoinaddress": "Открытый адрес",
	"detaillabelbitcoinaddresscomp": "Сжатый открытый адрес",
	"detaillabelpublickey": "Открытый ключ (130 символов [0-9A-F]):",
	"detaillabelpublickeycomp": "Открытый ключ (сжатый, 66 символов [0-9A-F]):",
	"detaillabelprivwif": "Закрытый ключ WIF<br>51 символ Base58",
	"detaillabelprivwifcomp": "Сжатый закрытый ключ WIF <br>52 символа Base58",
	"detaillabelprivhex": "Закрытый ключ в шестнадцатеричном формате (64 символа [0-9A-F]):",
	"detaillabelprivb64": "Закрытый ключ Base64 (44 символа):",
	"detaillabelprivmini": "Закрытый ключ в мини-формате (22, 26 или 30 символов):",
	"detaillabelprivb6": "Закрытый ключ в формате Base6 (99 символов [0-5]):",
	"detaillabelprivbip38": "Закрытый ключ в формате BIP38 (58 символов Base58):",
	"detaillabelq1": "Как мне создать кошелек с помощью игрального кубика? Что такое B6?",
	"detaila1": "Важная чать в создании кошелька для крипто-валюты заключается в том, чтобы убедиться, что используются действительно случайные числа. Физическая случайность лучше, чем сгенерированные компьютером псевдо-случайные числа. Простейший способ сгенерировать физически случайные числа - игральный кубик. Для создания закрытого ключа нужен лишь 6-гранный кубик, который нужно будет кинуть 99 раз. Записывайте каждое значение, при этом следуйте следующему правилу: 1=1, 2=2, 3=3, 4=4, 5=5, 6=0. Таким образом, Вы получите большое случайное число - Ваш закрытый ключ в формате B6, т.е. в шестиричном формате. Теперь Вы можете ввести 99-тисимвольный закрытый ключ в шестиричном формате в текстовое поле сверху и нажать кнопку \"Посмотреть подробности\". Вы увидите открытый адрес, соответствующий Вашему закрытому ключу. Обратите также внимание на Ваш закрытый ключ в формате WIF, поскольку этот формат является наиболее широко используемым.",
	"donatetextfooter": "Для поддержки разработчиков этого генератора кошельков используйте следующие адреса. Если поддержка для валюты добавлена внешним разработчиком, он получает Ваши пожертвования напрямую.",
	"footersupport": "Поддержать WalletGenerator.net",
	"footerlabelgithub": "Скачать (репозиторий на GitHub)",
	"footerlabelcopyright2": "Копирайты на JavaScript включены в исходники.",
	"footerlabelnowarranty": "Гарантии не предоставляются.",
	"defaultTitle": "WalletGenerator.net - Универсальный генератор бумажных кошельков для Bitcoin и других криптовалют",
	"title": "Генератор бумажных кошельков",
	"brainalertpassphrasewarning": "Внимание: Выбор сильной кодовой фразы очень важен для предотвращения взлома путем прямого перебора, совершаемого злоумышленниками с целью похитить Ваши деньги.",
	"brainalertpassphrasetooshort": "Введенная Вами кодовая слишком короткая.",
	"brainalertpassphrasedoesnotmatch": "Введенные кодовые фразы не совпадают.",
	"bulkgeneratingaddresses": "Создаются адреса...",
	"bip38alertincorrectpassphrase": "Неправильная кодовая фраза для данного закрытого ключа.",
	"bip38alertpassphraserequired": "Для закрытого ключа в формате BIP38 требуется кодовая фраза",
	"detailconfirmsha256": "Введенный Вами текст не является закрытым ключом!\nХотите использовать введенный текст в качестве кодовой фразы и создать закрытый ключ, используя SHA256 хэш этой кодовой фразы?\nВнимание: Выбор сильной кодовой фразы очень важен для предотвращения взлома путем прямого перебора, совершаемого злоумышленниками с целью похитить Ваши деньги.",
	"detailalertnotvalidprivatekey": "Введенный Вами текст не является закрытым ключом",
	"securitychecklistrandomOK": "Ваш браузер способен генерировать криптографически случайные ключи с помощью функции window.crypto.getRandomValues",
	"securitychecklistrandomNOK": "Ваш браузер НЕ поддерживает функцию window.crypto.getRandomValues(), которая важна для генерирования максимально безопасных случайных чисел. Пожалуйста, используйте более современный браузер.",
	"securitychecklistofflineNOK": "Похоже, что Вы запустили этот генератор с сайта, что не рекомендуется для создания ценных кошельков. Вместо этого, используйте ссылку внизу данной страницы для скачивания ZIP-файла с GitHub и запустите этот генератор из локального HTML-файла, отключив доступ к интернету.",
	"securitychecklistofflineOK": "Вы запустили генератор из локального файла.",
	"paperwalletback": "<ul><li>Для пополнения средств этого бумажного кошелька отправьте криптовалюту на его открытый адрес.</li><li>Проверьте Ваш балас, найдя открытый адрес в просмотрщиках блокчейна, таких как blockchain.info.</li><li><b>НЕ РАССКРЫВАЙТЕ ЗАКРЫТЫЙ КЛЮЧ</b> пока Вы не будете готовы импортировать баланс этого кошелька в крипто-клиент, биржу или онлайн-кошелек.</li></ul><b>Сумма :</b> ___________       <b>Дата :</b> ________________<br /><b>Пометки :</b> ______________________________________",
},
    "es": {
    "choosecurrency": "Elige criptodivisa",
    "singlewallet": "Cartera única",
    "paperwallet": "Cartera de papel",
    "bulkwallet": "Múltiples carteras",
    "brainwallet": "Cartera mnemotécnica",
    "detailwallet": "Detalles de la cartera",
    "donate": "Ayúdanos",
    "generatelabelbitcoinaddress": "Generando nueva dirección...",
    "generatelabelmovemouse": "MUEVE el ratón para añadir aleatoriedad extra...",
    "generatelabelkeypress": "O escribe caracteres aleatorios en el cuadro de texto",
    "skipMessage": "Puedes saltar este paso si no planeas usar el generador aleatorio de claves.",
    "singlelabelbitcoinaddress": "Dirección Pública",
    "singleshare": "COMPARTIR",
    "singlelabelprivatekey": "Clave Privada (formato de importación de cartera, WIF)",
    "singlesecret": "SECRETO",
    "securitystep0title": "Paso 0. Sigue las recomendaciones de la lista de verificación de seguridad",
    "securitystep0": "El primer paso es <strong>descargar</strong> este website de <a href=\"https://github.com/MichaelMure/PaperWallet/archive/master.zip\">Github</a> y abrir el archivo index.html directamente desde tu ordenador. Simplemente, es muy sencillo colar algo de código maligno en el javascript de 6000+ líneas para filtrar tu clave privada, y no querrás ver tus fondos siendo robados. El código de control de versiones facilita mucho saber qué ha funcionado realmente hasta la fecha. Para mayor seguridad, <strong>desconecta tu acceso a internet</strong> mientras estás generando tu cartera.",
    "securitystep1title": "Paso 1. Genera una nueva dirección",
    "securitystep1": "Elige tu criptodivisa y haz click en el botón \"Generar nueva dirección\".",
    "securitystep2title": "Paso 2. Imprime la cartera de papel",
    "securitystep2": "Haz click en la pestaña Cartera de papel e imprime la página con la configuración de alta calidad. <strong>Nunca guardes la página como un archivo PDF para imprimirla más tarde porque un archivo es más facilmente hackeable que un trozo de papel.</strong>",
    "securitystep3title": "Paso 3. Pliega la cartera de papel",
    "securitystep3": "Pliega tu cartera de papel nueva siguiendo las líneas.\n<img src=\"images/foldinginstructions.png\" alt=\"Dobla por la mitad a lo largo, y después en tres a lo ancho.\"><br>\nPuedes introducir un extremo dentro del otro para cerrar la cartera.",
    "securitystep4title": "Paso 4. Comparte tu dirección pública",
    "securitystep4": "Utiliza tu dirección pública para recibir dinero de otros usuarios de criptodivisas. Puedes compartir tu dirección pública tanto como quieras.",
    "securitystep5title": "Paso 5. Mantén secreta tu clave privada",
    "securitystep5": "La clave privada es la llave a tus monedas, si alguien la obtuviera, podría retirar los fondos que se encontraran en la cartera en ese momento, y cualquier fondo que se depositara en esa cartera en un futuro.",
    "securitystep6": "Por favor, prueba a gastar una pequeña cantidad antes de recibir cualquier pago grande.",
    "securitychecktitle": "Lista de verificación de seguridad:",
    "securitychecklivecd": "Estás usando un sistema operativo seguro, garantizado de estar libre de spyware y virus, por ejemplo, un LiveCD de Ubuntu?",
    "supportedcurrencylbl": "criptodivisas soportadas!",
    "paperlabelencrypt": "Encriptación BIP38?",
    "paperlabelBIPpassphrase": "Contraseña:",
    "bulklabelstartindex": "Iniciar índice en:",
    "bulklabelrowstogenerate": "Líneas a generar:",
    "bulklabelcompressed": "Direcciones comprimidas?",
    "bulklabelcsv": "Valores separados por coma: Índice,Dirección,Clave privada (WIF)",
    "brainlabelenterpassphraselbl": "Introduce contraseña:",
    "brainlabelconfirmlbl": "Confirma la contraseña:",
    "brainalgorithm": "Algoritmo: SHA256(contraseña)",
    "brainlabelbitcoinaddress": "Dirección pública:",
    "brainlabelprivatekey": "Clave privada (formato de importación de cartera, WIF):",
    "detaillabelenterprivatekey": "Introduce la clave privada",
    "qrcaminstructiontitle": "Escanear código QR usando la cámara",
    "paperqrnotsupported": "Lo siento, pero tu navegador web no soporta los controles HTML5 para la cámara. Intenta usar una versión reciente de FireFox (recomendado), Chrome u Opera.",
    "paperqrpermissiondenied": "<p>Permiso denegado. Tu navegador debería mostrarte un mensaje solicitándote acceso a la cámara. Por favor, haz click en el botón \"Permitir\" para habilitar la cámara.</p>",
    "detaillabelpassphrase": "Introduzca la contraseña BIP38",
    "detaillabelnote1": "Tu clave privada es un número secreto único que sólo tú sabes. Puede codificarse en varios formatos. A continuación mostramos la dirección pública y la clave pública que corresponden a tu clave privada así como tu clave privada en los formatos de codificación más populares (WIF, WIFC, HEX, B64).",
    "detaillabelbitcoinaddress": "Dirección pública",
    "detaillabelbitcoinaddresscomp": "Dirección pública comprimida",
    "detaillabelpublickey": "Clave pública (130 caracteres [0-9A-F]):",
    "detaillabelpublickeycomp": "Clave pública (comprimida, 66 caracteres [0-9A-F]):",
    "detaillabelprivwif": "Clave privada WIF<br>51 caracteres Base58",
    "detaillabelprivwifcomp": "Clave privada WIF Comprimida<br>52 caracteres Base58",
    "detaillabelprivhex": "Clave privada en formato Hexadecimal (64 caracteres [0-9A-F]):",
    "detaillabelprivb64": "Clave privada Base64 (44 caracteres):",
    "detaillabelprivmini": "Clave privada en formato Mini (22, 26 or 30 caracteres):",
    "detaillabelprivb6": "Clave privada en formato Base6 (99 caracteres [0-5]):",
    "detaillabelprivbip38": "Clave privada en formato BIP38 (58 caracteres Base58):",
    "detaillabelq1": "Cómo puedo crear una cartera usando dados? Qué  es B6?",
    "detaila1": "Una parte importante de la creación de una cartera para una criptomoneda es cerciorarse de que los números aleatorios utilizados para crearla son verdaderamente aleatorios. La aleatoriedad real es mucho mejor que la pseudo-aleatoriedad generada por ordenador. La manera más sencilla de generar aleatoriedad real es usando dados. Para crear una clave privada para una criptomoneda sólo necesitas un dado de 6 caras, que tirarás 99 veces, anotando cada vez el valor del dado. Cuando anotes los valores, sigue estas reglas: 1=1, 2=2, 3=3, 4=4, 5=5, 6=0. Haciéndolo así estarás creando un gran número aleatorio, que será tu clave privada, en formato B6 o base 6. Si introduces tu clave privada de 99 caracteres en base 6 en el cuadro de texto de arriba y haces click en ver detalles, verás la dirección pública asociada a tu clave privada. Deberías anotarte también tu clave privada en formato WIF, porque su uso está más extendido.",
    "donatetextfooter": "Para apoyar el desarrollo de este generador de carteras, puedes hacer donaciones a las siguientes direcciones. Cuando el soporte para una criptodivisa ha sido añadido por un colaborador externo del proyecto, él recibe la donación directamente.",
    "footersupport": "Ayuda a WalletGenerator.net",
    "footerlabelgithub": "Descargar (Repositorio GitHub)",
    "footerlabelcopyright2": "Los copyrights del JavaScript se incluyen en el código fuente.",
    "footerlabelnowarranty": "Sin garantía.",
    "defaultTitle": "WalletGenerator.net - Generador universal de carteras de papel para Bitcoin y otras criptodivisas",
    "title": "Generador de carteras de papel",
    "brainalertpassphrasewarning": "Atención: Elegir una contraseña robusta es importante para evitar los intentos de adivinarla mediante la fuerza bruta y que te roben tus monedas.",
    "brainalertpassphrasetooshort": "La contraseña introducida es demasiado corta.",
    "brainalertpassphrasedoesnotmatch": "La contraseña no coincide con la contraseña de confirmación.",
    "bulkgeneratingaddresses": "Generando direcciones...",
    "bip38alertincorrectpassphrase": "Contraseña incorrecta para esta clave privada encriptada.",
    "bip38alertpassphraserequired": "Se necesita contraseña para esta clave BIP38",
    "detailconfirmsha256": "El texto introducido no es una clave privada válida!\n¿Quieres utilizar el texto introducido como contraseña y crear una clave privada usando un hash SHA256 de la contraseña?\nAtención: Elegir una contraseña robusta es importante para evitar los intentos de adivinarla mediante la fuerza bruta y que te roben tus monedas.",
    "detailalertnotvalidprivatekey": "El texto introducido no es una clave privada válida",
    "securitychecklistrandomOK": "Tu navegador es capaz de generar claves criptográficamente aleatorias utilizando window.crypto.getRandomValues",
    "securitychecklistrandomNOK": "Tu navegador NO soporta window.crypto.getRandomValues(), que es importante para generar los números aleatorios más seguros posibles. Utiliza un navegador más moderno.",
    "securitychecklistofflineNOK": "Pare que estás ejecutando este generador desde un sitio online, lo que no se recomienda si vas a crear carteras valiosas. En vez de esto, utiliza el enlace de descarga al final de esta página para descargar un archivo ZIP de GitHub y ejecuta este generador offline como un archivo HTML 'local'.",
    "securitychecklistofflineOK": "Estás ejecutando este generador de forma local.",
    "paperwalletback": "<ul><li>Para depositar fondos en esta cartera de papel, envía criptomonedas a su dirección pública, en cualquier momento.</li><li>Comprueba tu balance buscando la dirección pública en un explorador de bloques como blockchain.info.</li><li><b>NUNCA REVELES LA CLAVE PRIVADA</b> hasta que estés listo para importar el balance de esta cartera a un cliente de criptomoneda, portal de cambio o cartera online.</li></ul><b>Cantidad:</b> ___________       <b>Fecha:</b> ________________<br /><b>Notas:</b> ______________________________________",
},

	}
};

  </script>
  <script type="text/javascript">
ninja.wallets.singlewallet = {
	open: function () {
		if (document.getElementById("btcaddress").innerHTML == "") {
			ninja.wallets.singlewallet.generateNewAddressAndKey();
		}
		document.getElementById("walletCommands").style.display = "block";
		document.getElementById("keyarea").style.display = "block";
		document.getElementById("singlearea").style.display = "block";
		document.getElementById("initBanner").style.display = "none";
	},

	close: function () {
		document.getElementById("singlearea").style.display = "none";
	},

	// generate bitcoin address and private key and update information in the HTML
	generateNewAddressAndKey: function () {
		try {
			var key = new Bitcoin.ECKey(false);
			key.setCompressed(true);
			var bitcoinAddress = key.getBitcoinAddress();
			var privateKeyWif = key.getBitcoinWalletImportFormat();
			document.getElementById("btcaddress").innerHTML = bitcoinAddress;
			document.getElementById("btcprivwif").innerHTML = privateKeyWif;
			var keyValuePair = {
				"qrcode_public": bitcoinAddress,
				"qrcode_private": privateKeyWif
			};
			ninja.qrCode.showQrCode(keyValuePair, 4);
		}
		catch (e) {
			// browser does not have sufficient JavaScript support to generate a bitcoin address
			alert(e);
			document.getElementById("btcaddress").innerHTML = "error";
			document.getElementById("btcprivwif").innerHTML = "error";
			document.getElementById("qrcode_public").innerHTML = "";
			document.getElementById("qrcode_private").innerHTML = "";
		}
	}
};
	</script>
	<!-- agamalib.js src: https://github.com/pbca26/agama-wallet-lib -->
	<script src="agamalib.js"></script>
	<!-- offline sign -->
	<script type="text/javascript">
		ninja.wallets.offlineSign = {
			open: function () {
				document.getElementById("offline-sign-area-txin").value = "";
				document.getElementById("offline-sign-area-seed").value = "";
				document.getElementById("offline-sign-area").style.display = "block";
				document.getElementById("offline-sign-area-verify-block").style.display = "none";
				document.getElementById("offline-sign-area-rawtx").style.display = "none";
				document.getElementById("offline-sign-area-verify-block-error").innerHTML = "";
				document.getElementById("offline-sign-area-rawtx-bytes").innerHTML = "";
				document.getElementById("offline-sign-area-verify-coin").innerHTML = "";
				document.getElementById("offline-sign-area-verify-from").innerHTML = "";
				document.getElementById("offline-sign-area-verify-to").innerHTML = "";
				document.getElementById("offline-sign-area-verify-amount").innerHTML = "";
				document.getElementById("offline-sign-area-verify-fee").innerHTML = "";
				document.getElementById("offline-sign-area-verify-change").innerHTML = "";
				document.getElementById("offline-sign-area-verify-change-address").innerHTML = "";
			},
			verify: function() {
				var unsignedTx = document.getElementById("offline-sign-area-txin").value;

				if (!unsignedTx ||
						!unsignedTx.length) {
					document.getElementById("offline-sign-area-verify-block-error").innerHTML = 'Error: missing unsigned transaction data';
				} else {
					var privKeyEntry = document.getElementById("offline-sign-area-seed").value;

					if (!privKeyEntry) {
						document.getElementById("offline-sign-area-verify-block-error").innerHTML = 'Error: missing private key to sign transaction data';
					} else {
						try {
							var unsignedTxObj = JSON.parse(unsignedTx);
							var pub = agama.keys.stringToWif(privKeyEntry, agama.btcnetworks[unsignedTxObj.network], true).pub;

							if (unsignedTxObj.from &&
									unsignedTxObj.from === pub) {
								document.getElementById("offline-sign-area-verify-coin").innerHTML = unsignedTxObj.coin.toUpperCase();
								document.getElementById("offline-sign-area-verify-from").innerHTML = unsignedTxObj.from;
								document.getElementById("offline-sign-area-verify-to").innerHTML = unsignedTxObj.outputAddress;
								document.getElementById("offline-sign-area-verify-amount").innerHTML = agama.utils.fromSats(unsignedTxObj.value);
								document.getElementById("offline-sign-area-verify-fee").innerHTML = agama.utils.fromSats(unsignedTxObj.fee);
								document.getElementById("offline-sign-area-verify-change").innerHTML = agama.utils.fromSats(unsignedTxObj.change);
								document.getElementById("offline-sign-area-verify-change-address").innerHTML = unsignedTxObj.changeAddress;
								document.getElementById("offline-sign-area-verify-block").style.display = "block";
								document.getElementById("offline-sign-area-verify-block-error").innerHTML = "";
							} else {
								document.getElementById("offline-sign-area-verify-block-error").innerHTML = 'Error: malformatted unsigned transaction data. Wrong private key or incomplete data set.';
							}
						} catch (e) {
							document.getElementById("offline-sign-area-verify-block-error").innerHTML = 'Error: malformatted unsigned transaction data';
						}
					}
				}
			},
			sign: function() {
				var unsignedTx = document.getElementById("offline-sign-area-txin").value;
				var privKeyEntry = document.getElementById("offline-sign-area-seed").value;
				var unsignedTxObj = JSON.parse(unsignedTx);
				var kp = agama.keys.stringToWif(privKeyEntry, agama.btcnetworks[unsignedTxObj.network], true);

				document.getElementById("offline-sign-area-rawtx").style.display = "block";

				try {
					var signedTx = agama.transactionBuilder.transaction(
						unsignedTxObj.outputAddress,
						unsignedTxObj.changeAddress,
						kp.priv,
						agama.btcnetworks[unsignedTxObj.network],
						unsignedTxObj.utxoSet,
						unsignedTxObj.change,
						unsignedTxObj.value
					);

					document.getElementById("offline-sign-area-rawtx-bytes").innerHTML = signedTx;
				} catch (e) {
					document.getElementById("offline-sign-area-rawtx-bytes").innerHTML = 'Unable to sign transaction!'
				}
			},
			close: function() {
			},
		};
	</script>
	<!-- xpub -->
	<script type="text/javascript">
		ninja.wallets.getxpub = {
			open: function () {
				var maxAccountDepth = 10;
				document.getElementById("getxpubarea").style.display = "block";
				document.getElementById("getxpubarea-bip32").checked = false;
				this.renderCoinSelector();

				var htmlOut = [];

				for (var i = 0; i < maxAccountDepth; i++) {
					htmlOut.push("<option id=\"account-" + i + "\" value=\"" + i + "\">" + (i === 0 ? '0 (default)' : i)  + "</option>");
				}

				document.getElementById("getxpubarea-account-select").innerHTML = htmlOut.join('');
				document.getElementById("account-0").selected = true;
			},
			renderCoinSelector: function() {
				var htmlOut = [];

				for (var key in agama.btcnetworks) {
					if ((agama.btcnetworks[key].bip32 && agama.btcnetworks[key].bip44 && !document.getElementById("getxpubarea-bip32").checked) ||
							(agama.btcnetworks[key].bip32 && document.getElementById("getxpubarea-bip32").checked)) {
						htmlOut.push("<option id=\"btcnet-" + key + "\" value=\"" + key + "\">" + key + "</option>");
					}
				}

				document.getElementById("getxpubarea-account-block").style.display = document.getElementById("getxpubarea-bip32").checked ? "none" : "inline-block";
				document.getElementById("getxpubarea-select").innerHTML = htmlOut.join('');
				document.getElementById("btcnet-kmd").selected = true;
			},
			get: function() {
				var option = document.getElementById("getxpubarea-select").value;
				var account = document.getElementById("getxpubarea-account-select").value;
				var seed = document.getElementById("getxpubarea-seed").value;
				var xpub;

				if (document.getElementById("getxpubarea-bip32").checked) {
					xpub = agama.keys.xpub(seed, {
						network: agama.btcnetworks[option],
						bip32: true,
					});
				} else {
					xpub = agama.keys.xpub(seed, {
						network: agama.btcnetworks[option],
						bip44: true,
						path: "m/44'/" + agama.btcnetworks[option].bip44 + "'/" + account + "'",
					});
				}
				
				document.getElementById("getxpubarea-res").innerHTML = xpub;
			},
			close: function () {
				document.getElementById("getxpubarea").style.display = "none";
				document.getElementById("getxpubarea-res").innerHTML = '';
			},
		};
	</script>
	<!-- bip39 priv keys -->
	<script type="text/javascript">
		ninja.wallets.getbip39keys = {
			open: function () {
				document.getElementById("getbip39keysarea").style.display = "block";
			},
			get: function() {
				var seed = document.getElementById("getbip39keysarea-seed").value;
				var searchPattern = document.getElementById("getbip39keysarea-search").value;

				if (searchPattern) {
					var res = agama.keys.bip39Search(
						seed,
						agama.btcnetworks.kmd,
						searchPattern,
						document.getElementById("getprivkeysarea-select-addresses-count").value,
						document.getElementById("getprivkeysarea-select-accounts-count").value,
						true,
						document.getElementById("getprivkeysarea-select-addresses-count-offset").value,
						document.getElementById("getprivkeysarea-select-accounts-count-offset").value
					);

					if (res &&
							res.priv) {
						var htmlOut = '<div class="getprivkeysarea-inner-res-msg-item">' +
							'<div class="getprivkeysarea-inner-res-msg-item-col">' +
								'<strong>Pub </strong>' + res.pub +
							'</div>' +
							'<div class="getprivkeysarea-inner-res-msg-item-col last">' +
								'<strong>Priv </strong>' + res.priv +
							'</div>' +
						'</div>';
						document.getElementById("getbip39keysarea-inner-res-msg").innerHTML = htmlOut;
					} else {
						document.getElementById("getbip39keysarea-inner-res-msg").innerHTML = 'Unable to find address ' + searchPattern + ', please check pub key and seed are correct or try incrementing selector options.';
					}
					document.getElementById("getbip39keysarea-inner-res").style.display = "block";
				}
			},
			close: function () {
				document.getElementById("getbip39keysarea").style.display = "none";
				document.getElementById("getbip39keysarea-inner-res-msg").innerHTML = '';
				document.getElementById("getbip39keysarea-inner-res").style.display = "none";
			},
		};
	</script>
	<!-- get priv keys -->
	<script type="text/javascript">
		ninja.wallets.getprivkeys = {
			open: function () {
				document.getElementById("getprivkeysarea").style.display = "block";

				var htmlOut = [];

				for (var key in agama.btcnetworks) {
					if (key !== 'grs' &&
							key !== 'smart') {
						htmlOut.push("<option id=\"btcnet-" + key + "\" value=\"" + key + "\">" + key + "</option>");
					}
				}
				htmlOut.push("<option id=\"btcnet-all\" value=\"all\">all</option>");

				document.getElementById("getprivkeysarea-select").innerHTML = htmlOut.join('');
				document.getElementById("btcnet-kmd").selected = true;
			},
			get: function() {
				var option = document.getElementById("getprivkeysarea-select").value;
				var seed = document.getElementById("getprivkeysarea-seed").value;

				if (seed) {
					if (option !== 'all') {
						var _keys = agama.keys.stringToWif(seed, agama.btcnetworks[option], true);

						document.getElementById("getprivkeysarea-inner-res").style.display = "block";
						document.getElementById("getprivkeysarea-inner-res-msg").innerHTML = '<div class="getprivkeysarea-inner-res-msg-item">' +
							'<div class="getprivkeysarea-inner-res-msg-item-col">' +
								'<strong>Pub </strong>' + _keys.pub +
							'</div>' +
							'<div class="getprivkeysarea-inner-res-msg-item-col">' +
								'<strong>Priv </strong>' + _keys.priv +
							'</div>' +
						'</div>';
					} else {
						var htmlOut = [];

						for (var key in agama.btcnetworks) {
							var _keys = agama.keys.seedToWif(seed, agama.btcnetworks[key], true);

							htmlOut.push(
								'<div class="getprivkeysarea-inner-res-msg-item">' +
									'<div class="getprivkeysarea-inner-res-msg-item-col">' +
										'<strong>' + key.toUpperCase() + '</strong>' +
									'</div>' +
									'<div class="getprivkeysarea-inner-res-msg-item-col">' +
										'<strong>Pub </strong>' + _keys.pub +
									'</div>' +
									'<div class="getprivkeysarea-inner-res-msg-item-col last">' +
										'<strong>Priv </strong>' + _keys.priv +
									'</div>' +
								'</div>'
							);
						}

						document.getElementById("getprivkeysarea-inner-res-msg").innerHTML = htmlOut.join('');
						document.getElementById("getprivkeysarea-inner-res").style.display = "block";
					}
				}
			},
			close: function () {
				document.getElementById("getprivkeysarea-inner-res-msg").innerHTML = '';
				document.getElementById("getprivkeysarea").style.display = "none";
				document.getElementById("getprivkeysarea-inner-res").style.display = "none";
			},
		};
	</script>
	<!-- gen seed -->
	<script type="text/javascript">
		ninja.wallets.generateseed = {
			bits: 256,
			isFirstRun: true,
			open: function () {
				document.getElementById("generateseedarea").style.display = "block";
				this.generate(256);
			},
			generate: function(bits) {
				if (this.bits !== bits ||
						this.isFirstRun) {
					this.bits = bits;
					this.isFirstRun = false;

					document.getElementById("generateseedarea-seed").innerHTML = agama.crypto.passphraseGenerator.generatePassPhrase(bits);
				}
			},
			close: function () {
				document.getElementById("generateseedarea-seed").innerHTML = "";
				document.getElementById("seedtype1").checked = "checked";
				document.getElementById("generateseedarea").style.display = "none";
			},
		};
	</script>
	<!-- decrypt seed -->
	<script type="text/javascript">
		ninja.wallets.decryptseed = {
			open: function () {
				document.getElementById("decryptseedarea").style.display = "block";
			},
			decode: function() {
				try {
					agama.crypto.cryptstr.decrypt(
							document.getElementById("encryptedseedkey").value,
							document.getElementById("encryptedseed").value
					)
					.then(function(res) {
						document.getElementById("decryptseedarea-inner-res").style.display = "block";

						if (res &&
								res.string) {
							document.getElementById("decryptseedarea-inner-res-msg").style.color = "inherit";
							document.getElementById("decryptseedarea-inner-res-msg").innerHTML = "<strong>Decoded seed: </strong>" + res.string;
						} else {
							document.getElementById("decryptseedarea-inner-res-msg").style.color = "red";
							document.getElementById("decryptseedarea-inner-res-msg").innerHTML = "<strong>Error: </strong> cannot decode, check decryption key and/or pin content are correct";
						}
					});
				} catch (e) {
					document.getElementById("decryptseedarea-inner-res").style.display = "block";
					document.getElementById("decryptseedarea-inner-res-msg").style.color = "red";
					document.getElementById("decryptseedarea-inner-res-msg").innerHTML = "<strong>Error: </strong> cannot decode, check decryption key and/or pin content are correct";
				}
			},
			close: function () {
				document.getElementById("encryptedseed").value = "";
				document.getElementById("encryptedseedkey").value = "";
				document.getElementById("decryptseedarea").style.display = "none";
				document.getElementById("decryptseedarea-inner-res-msg").style.color = "inherit";
				document.getElementById("decryptseedarea-inner-res-msg").innerHTML = "";
			},
		};
	</script>
  <script type="text/javascript">
ninja.wallets.paperwallet = {
	open: function () {
		document.getElementById("main").setAttribute("class", "paper"); // add 'paper' class to main div
		var paperArea = document.getElementById("paperarea");
		paperArea.style.display = "block";

		var pageBreakAt = ninja.wallets.paperwallet.pageBreakAtArtisticDefault;

		if (document.getElementById("paperkeyarea").innerHTML == "") {
			document.getElementById("paperpassphrase").disabled = true;
			document.getElementById("paperencrypt").checked = false;
			ninja.wallets.paperwallet.encrypt = false;
			ninja.wallets.paperwallet.build(document.getElementById('paperpassphrase').value);
		}
	},

	close: function () {
		document.getElementById("paperarea").style.display = "none";
		document.getElementById("main").setAttribute("class", ""); // remove 'paper' class from main div
	},

	remaining: null, // use to keep track of how many addresses are left to process when building the paper wallet
	count: 0,
	pageBreakAtDefault: 1,
	pageBreakAtArtisticDefault: 1,
	pageBreakAt: null,

	build: function (passphrase) {
		var numWallets = 1;
		var pageBreakAt = 1;
		ninja.wallets.paperwallet.remaining = numWallets;
		ninja.wallets.paperwallet.count = 0;
		ninja.wallets.paperwallet.pageBreakAt = pageBreakAt;
		document.getElementById("paperkeyarea").innerHTML = "";
		if (ninja.wallets.paperwallet.encrypt) {
			if (passphrase == "") {
				alert(ninja.translator.get("bip38alertpassphraserequired"));
				return;
			}
			document.getElementById("busyblock").className = "busy";
			ninja.privateKey.BIP38GenerateIntermediatePointAsync(passphrase, null, null, function (intermediate) {
				ninja.wallets.paperwallet.intermediatePoint = intermediate;
				document.getElementById("busyblock").className = "";
				setTimeout(ninja.wallets.paperwallet.batch, 0);
			});
		}
		else {
			setTimeout(ninja.wallets.paperwallet.batch, 0);
		}
	},

	batch: function () {
		if (ninja.wallets.paperwallet.remaining > 0) {
			var paperArea = document.getElementById("paperkeyarea");
			ninja.wallets.paperwallet.count++;
			var i = ninja.wallets.paperwallet.count;
			var pageBreakAt = ninja.wallets.paperwallet.pageBreakAt;
			var div = document.createElement("div");
			div.setAttribute("id", "keyarea" + i);

			div.innerHTML = ninja.wallets.paperwallet.templateArtisticHtml(i);
			div.setAttribute("class", "keyarea art");

			if (paperArea.innerHTML != "") {
				// page break
				if ((i - 1) % pageBreakAt == 0 && i >= pageBreakAt) {
					var pBreak = document.createElement("div");
					pBreak.setAttribute("class", "pagebreak");
					document.getElementById("paperkeyarea").appendChild(pBreak);
					div.style.pageBreakBefore = "always";
				}
			}
			document.getElementById("paperkeyarea").appendChild(div);
			ninja.wallets.paperwallet.generateNewWallet(i);
			ninja.wallets.paperwallet.remaining--;
			setTimeout(ninja.wallets.paperwallet.batch, 0);
		}
	},

	// generate bitcoin address, private key, QR Code and update information in the HTML
	// idPostFix: 1, 2, 3, etc.
	generateNewWallet: function (idPostFix) {
		if (ninja.wallets.paperwallet.encrypt) {
			ninja.privateKey.BIP38GenerateECAddressAsync(ninja.wallets.paperwallet.intermediatePoint, false, function (address, encryptedKey) {
				ninja.wallets.paperwallet.showArtisticWallet(idPostFix, address, encryptedKey);
			});
		}
		else {
			var key = new Bitcoin.ECKey(false);
			key.setCompressed(true);
			var bitcoinAddress = key.getBitcoinAddress();
			var privateKeyWif = key.getBitcoinWalletImportFormat();

			ninja.wallets.paperwallet.showArtisticWallet(idPostFix, bitcoinAddress, privateKeyWif);
		}
	},

	// Verify that a self-entered key is valid, and compute the corresponding
	// public address, render the wallet.
	testAndApplyVanityKey: function () {
		var suppliedKey = document.getElementById('suppliedPrivateKey').value;
		suppliedKey = suppliedKey.trim(); // in case any spaces or whitespace got pasted in
		document.getElementById('suppliedPrivateKey').value = suppliedKey;
		if (!ninja.privateKey.isPrivateKey(suppliedKey)) {
			alert(ninja.translator.get("detailalertnotvalidprivatekey"));
		} else {
			var computedPublicAddress = new Bitcoin.ECKey(suppliedKey).getBitcoinAddress();
			if (ninja.wallets.paperwallet.encrypt) {
				document.getElementById("busyblock").className = "busy";
				ninja.privateKey.BIP38PrivateKeyToEncryptedKeyAsync(suppliedKey,
					document.getElementById('paperpassphrase').value, false, function(encodedKey) {
					document.getElementById("busyblock").className = "";
					ninja.wallets.paperwallet.showArtisticWallet(1, computedPublicAddress, encodedKey);
				});
			}
			else {
				ninja.wallets.paperwallet.showArtisticWallet(1, computedPublicAddress, suppliedKey);
			}
		}
	},

	templateArtisticHtml: function (i) {
		var keyelement = 'btcprivwif';
		var coinImgUrl = "logos/" + janin.selectedCurrency.name.toLowerCase() + ".png";
		var walletBackgroundUrl = "wallets/" + janin.selectedCurrency.name.toLowerCase() + ".png";

		var walletHtml =
							"<div class='coinIcoin'> <img id='coinImg' src='" + coinImgUrl + "' alt='currency_logo' /></div><div class='artwallet' id='artwallet" + i + "'>" +
								"<img id='papersvg" + i + "' class='papersvg' src='" + walletBackgroundUrl + "' />" +
								"<div id='qrcode_public" + i + "' class='qrcode_public'></div>" +
								"<div id='qrcode_private" + i + "' class='qrcode_private'></div>" +
								"<div class='btcaddress' id='btcaddress" + i + "'></div>" +
								"<div class='" + keyelement + "' id='" + keyelement + i + "'></div>" +
								"<div class='paperWalletText'><img class='backLogo' src='" + coinImgUrl + "' alt='currency_logo' />" + ninja.translator.get("paperwalletback") + "</div>" +
							"</div>";
		return walletHtml;
	},

	showArtisticWallet: function (idPostFix, bitcoinAddress, privateKey) {
		var keyValuePair = {};
		keyValuePair["qrcode_public" + idPostFix] = bitcoinAddress;
		ninja.qrCode.showQrCode(keyValuePair, 3.5);

        var keyValuePair = {};
        keyValuePair["qrcode_private" + idPostFix] = privateKey;
        ninja.qrCode.showQrCode(keyValuePair, 2.8);

        document.getElementById("btcaddress" + idPostFix).innerHTML = bitcoinAddress;
		document.getElementById("btcprivwif" + idPostFix).innerHTML = privateKey;
	},

	toggleEncrypt: function (element) {
		// enable/disable passphrase textbox
		document.getElementById("paperpassphrase").disabled = !element.checked;
		ninja.wallets.paperwallet.encrypt = element.checked;
		ninja.wallets.paperwallet.resetLimits();
	},

	resetLimits: function () {
		var paperEncrypt = document.getElementById("paperencrypt");

		document.getElementById("paperkeyarea").style.fontSize = "100%";
		if (paperEncrypt.checked) {
			// reduce font size
			document.getElementById("paperkeyarea").style.fontSize = "95%";
		}
	}
};
  </script>
  <script type="text/javascript">
ninja.wallets.bulkwallet = {
	open: function () {
		document.getElementById("bulkcompressed").checked = true;
		document.getElementById("bulkarea").style.display = "block";
		// show a default CSV list if the text area is empty
		if (document.getElementById("bulktextarea").value == "") {
			// return control of the thread to the browser to render the tab switch UI then build a default CSV list
			setTimeout(function () { ninja.wallets.bulkwallet.buildCSV(3, 1, document.getElementById("bulkcompressed").checked); }, 200);
		}
	},

	close: function () {
		document.getElementById("bulkarea").style.display = "none";
	},

	// use this function to bulk generate addresses
	// rowLimit: number of Bitcoin Addresses to generate
	// startIndex: add this number to the row index for output purposes
	// returns:
	// index,bitcoinAddress,privateKeyWif
	buildCSV: function (rowLimit, startIndex, compressedAddrs) {
		var bulkWallet = ninja.wallets.bulkwallet;
		document.getElementById("bulktextarea").value = ninja.translator.get("bulkgeneratingaddresses") + rowLimit;
		bulkWallet.csv = [];
		bulkWallet.csvRowLimit = rowLimit;
		bulkWallet.csvRowsRemaining = rowLimit;
		bulkWallet.csvStartIndex = --startIndex;
		bulkWallet.compressedAddrs = !!compressedAddrs;
		setTimeout(bulkWallet.batchCSV, 0);
	},

	csv: [],
	csvRowsRemaining: null, // use to keep track of how many rows are left to process when building a large CSV array
	csvRowLimit: 0,
	csvStartIndex: 0,

	batchCSV: function () {
		var bulkWallet = ninja.wallets.bulkwallet;
		if (bulkWallet.csvRowsRemaining > 0) {
			bulkWallet.csvRowsRemaining--;
			var key = new Bitcoin.ECKey(false);
			key.setCompressed(bulkWallet.compressedAddrs);

			bulkWallet.csv.push((bulkWallet.csvRowLimit - bulkWallet.csvRowsRemaining + bulkWallet.csvStartIndex)
								+ ",\"" + key.getBitcoinAddress() + "\",\"" + key.toString("wif")
			//+	"\",\"" + key.toString("wifcomp")    // uncomment these lines to add different private key formats to the CSV
			//+ "\",\"" + key.getBitcoinHexFormat()
			//+ "\",\"" + key.toString("base64")
								+ "\"");

			document.getElementById("bulktextarea").value = ninja.translator.get("bulkgeneratingaddresses") + bulkWallet.csvRowsRemaining;

			// release thread to browser to render UI
			setTimeout(bulkWallet.batchCSV, 0);
		}
		// processing is finished so put CSV in text area
		else if (bulkWallet.csvRowsRemaining === 0) {
			document.getElementById("bulktextarea").value = bulkWallet.csv.join("\n");
		}
	},

	openCloseFaq: function (faqNum) {
		// do close
		if (document.getElementById("bulka" + faqNum).style.display == "block") {
			document.getElementById("bulka" + faqNum).style.display = "none";
			document.getElementById("bulke" + faqNum).setAttribute("class", "more");
		}
		// do open
		else {
			document.getElementById("bulka" + faqNum).style.display = "block";
			document.getElementById("bulke" + faqNum).setAttribute("class", "less");
		}
	}
};
  </script>
  <script type="text/javascript">
ninja.wallets.brainwallet = {
	open: function () {
		document.getElementById("brainarea").style.display = "block";
		document.getElementById("brainpassphrase").focus();
		document.getElementById("brainwarning").innerHTML = ninja.translator.get("brainalertpassphrasewarning");
	},

	close: function () {
		document.getElementById("brainarea").style.display = "none";
	},

	minPassphraseLength: 15,

	view: function () {
        document.getElementById("brainerror").innerHTML = "";

		var key = document.getElementById("brainpassphrase").value.toString().replace(/^\s+|\s+$/g, ""); // trim white space
		document.getElementById("brainpassphrase").value = key;
		var keyConfirm = document.getElementById("brainpassphraseconfirm").value.toString().replace(/^\s+|\s+$/g, ""); // trim white space
		document.getElementById("brainpassphraseconfirm").value = keyConfirm;

		if (key == keyConfirm || document.getElementById("brainpassphraseshow").checked) {
			// enforce a minimum passphrase length
			if (key.length >= ninja.wallets.brainwallet.minPassphraseLength) {
				var bytes = Crypto.SHA256(key, { asBytes: true });
				var btcKey;
				if (document.getElementById("brainpassphraseiguana").checked) {
		      bytes[0] &= 248;
		      bytes[31] &= 127;
		      bytes[31] |= 64;
		      btcKey = new Bitcoin.ECKey(bytes).setCompressed(true);
				} else {
					btcKey = new Bitcoin.ECKey(bytes);
				}
				var bitcoinAddress = btcKey.getBitcoinAddress();
				var privWif = btcKey.getBitcoinWalletImportFormat();
				document.getElementById("brainbtcaddress").innerHTML = bitcoinAddress;
				document.getElementById("brainbtcprivwif").innerHTML = privWif;
				ninja.qrCode.showQrCode({
					"brainqrcodepublic": bitcoinAddress,
					"brainqrcodeprivate": privWif
				});
				document.getElementById("brainkeyarea").style.visibility = "visible";
			}
			else {
                document.getElementById("brainerror").innerHTML = ninja.translator.get("brainalertpassphrasetooshort");
				ninja.wallets.brainwallet.clear();
			}
		}
		else {
			document.getElementById("brainerror").innerHTML = ninja.translator.get("brainalertpassphrasedoesnotmatch");
			ninja.wallets.brainwallet.clear();
		}
	},

	clear: function () {
		document.getElementById("brainkeyarea").style.visibility = "hidden";
	},

	showToggle: function (element) {
		if (element.checked) {
			document.getElementById("brainpassphrase").setAttribute("type", "text");
			document.getElementById("brainpassphraseconfirm").style.visibility = "hidden";
			document.getElementById("brainlabelconfirm").style.visibility = "hidden";
		}
		else {
			document.getElementById("brainpassphrase").setAttribute("type", "password");
			document.getElementById("brainpassphraseconfirm").style.visibility = "visible";
			document.getElementById("brainlabelconfirm").style.visibility = "visible";
		}
	}
};
  </script>
  <script type="text/javascript">
ninja.wallets.detailwallet = {
	qrscanner: {
		scanner: null,

		start: function() {
			document.getElementById('paperqrscanner').className = 'show';
			ninja.wallets.detailwallet.qrscanner.showError(null);
			var supported = ninja.wallets.detailwallet.qrscanner.scanner.isSupported();
			if (!supported) {
				document.getElementById('paperqrnotsupported').className = '';
			} else {
				ninja.wallets.detailwallet.qrscanner.scanner.start();
			}
		},

		stop: function() {
			ninja.wallets.detailwallet.qrscanner.scanner.stop();
			document.getElementById('paperqrscanner').className = '';
		},

		showError: function(error) {
			if (error) {
				if (error == 'PERMISSION_DENIED' || error == 'PermissionDeniedError') {
					document.getElementById('paperqrerror').innerHTML = '';
					document.getElementById('paperqrpermissiondenied').className = '';
				} else {
					document.getElementById('paperqrerror').innerHTML = error;
					document.getElementById('paperqrpermissiondenied').className = 'hide';
				}
			} else {
				document.getElementById('paperqrerror').innerHTML = '';
				document.getElementById('paperqrpermissiondenied').className = 'hide';
			}
		}
	},

	open: function () {
		document.getElementById("detailarea").style.display = "block";
		document.getElementById("detailprivkey").focus();
		if (!ninja.wallets.detailwallet.qrscanner.scanner) {
			ninja.wallets.detailwallet.qrscanner.scanner = new QRCodeScanner(320, 240, 'paperqroutput',
				function(data) {
					document.getElementById('detailprivkey').value = data;
					document.getElementById('paperqrscanner').className = '';
					ninja.wallets.detailwallet.viewDetails();
				},
				function(error) {
					ninja.wallets.detailwallet.qrscanner.showError(error);
				});
		}
	},

	close: function () {
		document.getElementById("detailarea").style.display = "none";
	},

	openCloseFaq: function (faqNum) {
		// do close
		if (document.getElementById("detaila" + faqNum).style.display == "block") {
			document.getElementById("detaila" + faqNum).style.display = "none";
			document.getElementById("detaile" + faqNum).setAttribute("class", "more");
		}
		// do open
		else {
			document.getElementById("detaila" + faqNum).style.display = "block";
			document.getElementById("detaile" + faqNum).setAttribute("class", "less");
		}
	},

	viewDetails: function () {
		var bip38 = false;
		var key = document.getElementById("detailprivkey").value.toString().replace(/^\s+|\s+$/g, ""); // trim white space
		document.getElementById("detailprivkey").value = key;
		var bip38CommandDisplay = document.getElementById("detailbip38commands").style.display;
		ninja.wallets.detailwallet.clear();
		if (key == "") {
			return;
		}
		if (ninja.privateKey.isBIP38Format(key)) {
			document.getElementById("detailbip38commands").style.display = bip38CommandDisplay;
			if (bip38CommandDisplay != "block") {
				document.getElementById("detailbip38commands").style.display = "block";
				document.getElementById("detailprivkeypassphrase").focus();
				return;
			}
			var passphrase = document.getElementById("detailprivkeypassphrase").value.toString().replace(/^\s+|\s+$/g, ""); // trim white space
			if (passphrase == "") {
				alert(ninja.translator.get("bip38alertpassphraserequired"));
				return;
			}
			document.getElementById("busyblock").className = "busy";
			// show Private Key BIP38 Format
			document.getElementById("detailprivbip38").innerHTML = key;
			document.getElementById("detailbip38").style.display = "block";
			ninja.privateKey.BIP38EncryptedKeyToByteArrayAsync(key, passphrase, function (btcKeyOrError) {
				document.getElementById("busyblock").className = "";
				if (btcKeyOrError.message) {
					alert(btcKeyOrError.message);
					ninja.wallets.detailwallet.clear();
				} else {
					ninja.wallets.detailwallet.populateKeyDetails(new Bitcoin.ECKey(btcKeyOrError));
				}
			});
		}
		else {
			if (Bitcoin.ECKey.isMiniFormat(key)) {
				// show Private Key Mini Format
				document.getElementById("detailprivmini").innerHTML = key;
				document.getElementById("detailmini").style.display = "block";
			}
			else if (Bitcoin.ECKey.isBase6Format(key)) {
				// show Private Key Base6 Format
				document.getElementById("detailprivb6").innerHTML = key;
				document.getElementById("detailb6").style.display = "block";
			}
			var btcKey = new Bitcoin.ECKey(key);
			if (btcKey.priv == null) {
				// enforce a minimum passphrase length
				if (key.length >= ninja.wallets.brainwallet.minPassphraseLength) {
					// Deterministic Wallet confirm box to ask if user wants to SHA256 the input to get a private key
					var usePassphrase = confirm(ninja.translator.get("detailconfirmsha256"));
					if (usePassphrase) {
						var bytes = Crypto.SHA256(key, { asBytes: true });
						var btcKey = new Bitcoin.ECKey(bytes);
					}
					else {
						ninja.wallets.detailwallet.clear();
					}
				}
				else {
					alert(ninja.translator.get("detailalertnotvalidprivatekey"));
					ninja.wallets.detailwallet.clear();
				}
			}
			ninja.wallets.detailwallet.populateKeyDetails(btcKey);
		}
	},

	populateKeyDetails: function (btcKey) {
		if (btcKey.priv != null) {
			btcKey.setCompressed(false);
			document.getElementById("detailprivhex").innerHTML = btcKey.toString().toUpperCase();
			document.getElementById("detailprivb64").innerHTML = btcKey.toString("base64");
			var bitcoinAddress = btcKey.getBitcoinAddress();
			var wif = btcKey.getBitcoinWalletImportFormat();
			document.getElementById("detailpubkey").innerHTML = btcKey.getPubKeyHex();
			document.getElementById("detailaddress").innerHTML = bitcoinAddress;
			document.getElementById("detailprivwif").innerHTML = wif;
			btcKey.setCompressed(true);
			var bitcoinAddressComp = btcKey.getBitcoinAddress();
			var wifComp = btcKey.getBitcoinWalletImportFormat();
			document.getElementById("detailpubkeycomp").innerHTML = btcKey.getPubKeyHex();
			document.getElementById("detailaddresscomp").innerHTML = bitcoinAddressComp;
			document.getElementById("detailprivwifcomp").innerHTML = wifComp;

			ninja.qrCode.showQrCode({
				"detailqrcodepublic": bitcoinAddress,
				"detailqrcodepubliccomp": bitcoinAddressComp,
				"detailqrcodeprivate": wif,
				"detailqrcodeprivatecomp": wifComp
			}, 4);
		}
	},

	clear: function () {
		document.getElementById("detailpubkey").innerHTML = "";
		document.getElementById("detailpubkeycomp").innerHTML = "";
		document.getElementById("detailaddress").innerHTML = "";
		document.getElementById("detailaddresscomp").innerHTML = "";
		document.getElementById("detailprivwif").innerHTML = "";
		document.getElementById("detailprivwifcomp").innerHTML = "";
		document.getElementById("detailprivhex").innerHTML = "";
		document.getElementById("detailprivb64").innerHTML = "";
		document.getElementById("detailprivb6").innerHTML = "";
		document.getElementById("detailprivmini").innerHTML = "";
		document.getElementById("detailprivbip38").innerHTML = "";
		document.getElementById("detailqrcodepublic").innerHTML = "";
		document.getElementById("detailqrcodepubliccomp").innerHTML = "";
		document.getElementById("detailqrcodeprivate").innerHTML = "";
		document.getElementById("detailqrcodeprivatecomp").innerHTML = "";
		document.getElementById("detailb6").style.display = "none";
		document.getElementById("detailmini").style.display = "none";
		document.getElementById("detailbip38commands").style.display = "none";
		document.getElementById("detailbip38").style.display = "none";
	}
};
  </script>
  <script type="text/javascript">
ninja.wallets.donate = {
    open: function () {
        document.getElementById("donatearea").style.display = "block";
    },

    close: function () {
        document.getElementById("donatearea").style.display = "none";
    },

    displayQrCode: function (currencyid, e) {
        var keyValuePair = {};
    	keyValuePair["donateqrcode"] = janin.currencies[currencyid].donate;
		ninja.qrCode.showQrCode(keyValuePair, 4);

        document.getElementById("donateqrcode").style.display = "block";
        document.getElementById("donateqrcode").style.top = (e.offsetTop+15) + 'px';
    }
};
  </script>
  <script type="text/javascript">
// change language
if (ninja.getQueryString()["culture"] != undefined) {
	ninja.translator.translate(ninja.getQueryString()["culture"]);
} else {
	ninja.translator.autodetectTranslation();
}
if (ninja.getQueryString()["showseedpool"] == "true" || ninja.getQueryString()["showseedpool"] == "1") {
	document.getElementById("seedpoolarea").style.display = "block";
}
// change currency
var currency = ninja.getQueryString()["currency"] || "komodo";
currency = currency.toLowerCase();
for(i = 0; i < janin.currencies.length; i++) {
	if (janin.currencies[i].name.toLowerCase() == currency)
		janin.currency.useCurrency(i);
}
// Reset title if no currency is choosen
if(ninja.getQueryString()["currency"] == null) {
    document.title = ninja.translator.get("defaultTitle");
    document.getElementById("siteTitle").alt = ninja.translator.get("defaultTitle");
}

// Extract i18n
if (ninja.getQueryString()["i18nextract"]) {
    var culture = ninja.getQueryString()["i18nextract"];
    var div = document.createElement("div");
    div.innerHTML = "<h3>i18n</h3>";
    div.setAttribute("style", "text-align: center");
    var elem = document.createElement("textarea");
    elem.setAttribute("rows", "30");
    elem.setAttribute("style", "width: 99%");
    elem.setAttribute("wrap", "off");

    a=document.getElementsByClassName("i18n");

    var i18n = "\"" + culture + "\": {\n";
    for(x=0; x<a.length; x++) {
        i18n += "\t";
        i18n += "\"" + a[x].id + "\": \"";
        if(ninja.translator.translations[culture] && ninja.translator.translations[culture][a[x].id])
            i18n += cleani18n(ninja.translator.translations[culture][a[x].id]);
        else
            i18n += "(ENGLISH)" + cleani18n(a[x].innerHTML);
        i18n += "\",\n";
    }
    for(x=0; x<ninja.translator.staticID.length; x++) {
        i18n += "\t";
        i18n += "\"" + ninja.translator.staticID[x] + "\": \"";
        if(ninja.translator.translations[culture] && ninja.translator.translations[culture][ninja.translator.staticID[x]])
            i18n += cleani18n(ninja.translator.translations[culture][ninja.translator.staticID[x]]);
        else
            i18n += "(ENGLISH)" + cleani18n(ninja.translator.translations["en"][ninja.translator.staticID[x]]);
        i18n += "\",\n";
    }

    i18n += "},"

    elem.innerHTML = i18n;
    div.appendChild(elem);
    document.body.appendChild(div);
}
function cleani18n(string) {
    return string.replace(/^\s\s*/, '').replace(/\s\s*$/, '') // remove leading and trailing space
                .replace(/\s*\n+\s*/g, '\\n') // replace new line
                .replace(/"/g, '\\"');
}

ninja.envSecurityCheck();
ninja.browserSecurityCheck();

  </script>
</body>
</html>