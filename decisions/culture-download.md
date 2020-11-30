# Culture Downloading

This document will cover a compressive plan on how it will enable downloadable content.

**Goals**:

- Accessible Offline
- Persistent information
- Up to date information
- Small storage usage
- Cross platform
- Fast

Those are the goals that downloading would like to achieve and the rest of this document will list operations and their pros and cons versus other choices.

## Where to compress the data?

There are two choices here either the frontend or the backend both have their own pros and cons.

### Backend

- ❌ Complicates API
  adding a route similar to `/v1/culture/<name>/download` which would exclusively be used for download information.
- ✅ Easy implementation
  Easy as a result of being done solely on one endpoint, not many places for changes and complex logic.
- ❌ Slow
  The server would have to compress all information being served from the `download` route.
  This is feasible for small user bases, but at scale would result in the Api being overloaded.
- ✅ Faster for user's phones
  User's phones don't have to do any of the compression of content

### Frontend

- ✅ Fast
  This is a result of shifting cost of compressing content onto the user.
- ✅ Simpler API
  the API no longer needs to perform any task other than database queries and serving up the information the database gives it.
- ❌ Complicated implementation
  information fetched from the backend would be compressed by the user's device.
- ❓ Slower for user's phones
  User's phones will have to compress the data, and that could result in higher CPU usage, but will most likely be unnoticeable.

## How to compress the data?

The algorithm to compress data is difficult to change later. It's essential to find the proper balance between performance and storage.

**Sources**:

- [Linkedin's switch from Gzip to Brotli](https://engineering.linkedin.com/blog/2017/05/boosting-site-speed-using-brotli-compression).
- [Brotli RFC](https://tools.ietf.org/html/rfc7932)
- [Zlib RFC](http://www.zlib.org/rfc-deflate.html)
- [Akamai Gzip vs Brotli](https://blogs.akamai.com/2016/02/understanding-brotlis-potential.html)
- [OpenCPU Benchmarks](https://www.opencpu.org/posts/brotli-benchmarks/)
- [Mozilla Brotli Comparison to Gzip](https://hacks.mozilla.org/2015/11/better-than-gzip-compression-with-brotli/)

### Brotli

- ✅ Smaller size
- ✅ JavaScript library [brotli](https://github.com/foliojs/brotli.js)
  Pure JavaScript
- ✅ Targeted towards text compression
- ❌ Slower compression
- ❓ On par decompression

### Gzip

- ✅ JavaScript library [pako](https://github.com/nodeca/pako)
  Pure JavaScript
  Popular
- ✅ Faster compression
- ❌ Larger size
- ❓ On par decompression

This comparison is a toss up, when it comes to libraries `pako` is more supported and has a better backing. Brotli has better compression sizes. Brotli seems to be the winner, but it could go either way.

## How to store and access the data?

Unlike previous choices this question only has one obvious answer.
In [React Native](https://reactnative.dev/) accessing user storage is commonly done through [Async Storage](https://react-native-async-storage.github.io/async-storage/).

[Async Storage](https://react-native-async-storage.github.io/async-storage/)

- ✅ Asynchronous
- ✅ Easy to use
- ✅ Cross platform
- ✅ Persistent
- ❌ 6MB of storage by default

## Proposed Implementation

The backend API will remain mostly the same and all compression will be done on the user's phone or machine. This yields better performance for the infrastructure as a whole and user's will only be impacted when they click the download button.

There are a few procedures that will occur when the user uses the app.
These are listed below and how they will be done for downloading and managing downloaded content.

### The Ledger

The ledger is a `JSON` list located in `Async Storage` called "Ledger", it will look like:

```json
{
    "African American": 1603984950,
    "Latin American": 1603984950,
    ...
}
```

Where they key is the Culture's name and the value is a [EPOCH Timestamp](https://en.wikipedia.org/wiki/Unix_time) signifying when they were last updated.

#### Responsibilities

- Keep track of downloaded cultures
  Cultures appear in this list **ONLY** if they are downloaded
- Culture names signify where in `Async Storage` they exist
- Keep track of the time cultures were last updated stored as EPOCH Timestamps

### Click Download Button Procedure (ON)

1. Connect to the Api and fetch culture's information
2. Compress the data using `Brotli` for reduced size
3. Store the data locally on their phone/computer via `Async Storage`.
   Where they key will be the culture name and the value will be the compressed payload.
4. Update or create a ledger

### Open App Procedure

1. Whenever the user opens the application we load the ledger into memory
2. Iterate over all downloaded cultures and query the database if they've been updated
   In order to improve performance, in the `GET /v1/culture` route we should return the culture name **AND** the last time it was updated.
3. If they've been updated we re-download the data and compress it overwriting the previous data

### Click Download Button Procedure (OFF)

1. Remove the culture from the ledger
2. Delete the culture's information from the device

### User Offline Procedure

1. Instead of using `GET /v1/culture` to list cultures, parse ledger and show all cultures on the ledger
2. When the user clicks on a culture use `Async Storage` to lookup the compressed data and decompress it
   For performance, the data should be stored in memory if possible instead of having to decompress every time.
3. Display content to user

## Future

This depends greatly on the size of out Culture data, but it may be worthwhile to have the API do the compressing. This would possibly hurt performance on the server, but would improve performance on phones. It would also be beneficial for users outside of the mobile/web application.
