---
layout: post
title: Is JSON the best choice for high-throughput systems?
description: Discover the CPU impact of JSON serialization and deserialization
date: 2024-08-25 21:08 +0800
categories: [json, performance]
tags: [json, performance, serialization, deserialization]
---

## Introduction

### What is JSON?

JavaScript Object Notation (JSON) is probably something every developer has come across at some point in their career. It's a lightweight data interchange format. JSON is widely used in web applications, APIs, and microservices to transmit data between systems.

The reason why it's so popular is that it's simple, flexible, and language-independent. JSON is supported by most programming languages, and there are libraries available for serializing and deserializing JSON data in almost every language.

Furthermore, JSON format is syntactically similar to JavaScript objects, making it easy to work with in web applications when converting between JSON and JavaScript objects. Sometimes, it can be even used between microservices when it is beneficial to have a dynamic schema-less data representation format.

### What is JSON serialization and deserialization?

Serialization is the process of converting an object into a format that can be easily stored or transmitted. In the context of JSON, serialization refers to converting an object into a JSON string. This is useful when you want to store an object in a file or send it over the network.

Deserialization is the opposite process of serialization. It involves converting a JSON string back into an object. This is useful when you receive a JSON string from a file or over the network and want to convert it back into an object that you can work with in your application.

### Performance considerations

While JSON is a versatile and widely-used data format, it's not without its drawbacks. One of the main concerns with JSON is its performance impact, especially when serializing and deserializing large amounts of data where the JSON data is big or when there are many requests coming in.

In high-throughput systems, the CPU overhead of JSON serialization and deserialization can become a bottleneck, affecting the overall performance of the application. For example, since JSON is a data exchange format, a web server that receives and sends JSON data would have to perform serialization and deserialization operations on every request, which can be computationally expensive based on the number of requests and the size of the data.

## Why is JSON serialization and deserialization slow?

JSON serialization and deserialization can be slow for several reasons:

1. **Parsing overhead**: JSON parsing involves reading the JSON string, parsing it, and constructing the corresponding object. This process can be computationally expensive, especially for large JSON payloads with deep nesting and complex structures.

2. **String manipulation**: JSON serialization and deserialization often involve string manipulation operations, such as concatenation, splitting, and parsing. These operations can be slow, especially when working with large strings. String handling in most programming languages is not as efficient as working with binary data.

3. **Verbosity**: JSON is designed to be human-readable and easy to work with. However, this comes at the cost of verbosity. JSON strings can be large and contain a lot of redundant information. For example, for the JSON example below, most of the information is used for making it human-readable and easy to understand, but it's not necessary for the data itself.

```json
{
  "students": [
    {
      "name": "Alice",
      "age": 20
    },
    {
      "name": "Bob",
      "age": 21
    }
  ]
}
```

## Optimisation strategies

1. **Use binary formats**: Binary formats like Protocol Buffers, MessagePack, and BSON are more efficient than JSON for serialization and deserialization. These formats are designed to be compact and fast, making them ideal for high-throughput systems where performance is critical.

   - **Protocol Buffers**: Protocol Buffers is a binary serialization format developed by Google. It's designed to be fast, compact, and efficient. Protocol Buffers schemas are defined using a schema definition language, which is compiled into code that can be used to serialize and deserialize objects. Protocol Buffers is widely used in Google's internal systems and is available as an open-source project. Check out this interesting [medium article](https://medium.com/@david_turner/protocol-buffers-just-how-fast-are-they-9ec19ea580db) where the author compares Java serialization and deserialization with Protocol Buffers. The author has also shared a YouTube video which is worth watching and will be included down below.

     - Pros:
       - Compact binary format with efficient serialization and deserialization
       - Cross-language support and code generation
       - Basis for gRPC, a high-performance RPC framework
       - Schema evolution support
     - Cons:
       - Requires schema definition
       - Need to regenerate code when schema changes which requires a new deployment

   - **MessagePack**: MessagePack is a binary serialization format that is similar to JSON but more compact and faster. MessagePack is designed to be simple and efficient, with a focus on performance.

     - Pros:
       - Compact binary format with efficient serialization and deserialization
       - Supports rich data types
       - Cross-language support
       - Schema-less (dynamic schema)
     - Cons:
       - Less human-readable than JSON
       - Limited schema support

   - **BSON**: BSON (Binary JSON) is a binary serialization format used by MongoDB. BSON is designed to be efficient and fast, with support for rich data types like dates, regular expressions, and binary data. BSON is optimised for performance and is used in MongoDB to store and retrieve data.

     - Pros:
       - Compact binary format with efficient serialization and deserialization
       - Rich data types and support for MongoDB features
       - Efficient for database storage and retrieval with MongoDB
     - Cons:
       - MongoDB-specific format
       - Limited cross-language support

2. **Optimise JSON parsing**: If you have to use JSON, consider using a faster JSON parser library. There are several JSON parser libraries available that are faster than the standard JSON parser provided by most programming languages.

   - Some popular JSON parser libraries include:
     - **FastJSON**: FastJSON is a JSON parser library for Java that is designed to be fast and efficient. FastJSON is one of the fastest JSON parser libraries available for Java and is widely used in high-performance systems.
     - **simdjson**: simdjson is a JSON parser library that is designed to take advantage of modern CPU features like SIMD instructions to parse JSON data faster. simdjson is written in C++ and is one of the fastest JSON parser libraries available.
     - **ujson**: ujson is a JSON parser library for Python that is designed to be fast and efficient. ujson is written in C and is one of the fastest JSON parser libraries available for Python.

3. **Reduce JSON size**: Minimising the size of the JSON payload can improve performance. You can do this by removing unnecessary whitespace, using shorter field names, and avoiding redundant information. For example, instead of using verbose field names like `firstName` and `lastName`, you can use shorter names like `fn` and `ln`. Although this may make the JSON less human-readable, it can significantly reduce the size of the payload.

## Conclusion

JSON is a versatile and widely-used data format, but it's not without its performance drawbacks. JSON serialization and deserialization can be slow, especially for large payloads and high-throughput systems. To improve performance, consider using binary formats like Protocol Buffers, MessagePack, or BSON, or optimise your JSON parsing and reduce the size of the JSON payload.

## References

- [https://www.w3schools.com/js/js_json_intro.asp](https://www.w3schools.com/js/js_json_intro.asp)
- [https://dev.to/nikl/json-is-slower-here-are-its-4-faster-alternatives-2g30](https://dev.to/nikl/json-is-slower-here-are-its-4-faster-alternatives-2g30)
- [https://www.mongodb.com/resources/languages/bson#:~:text=BSON%20can%20be%20compared%20to,slight%20disadvantage%20of%20space%20efficiency.](https://www.mongodb.com/resources/languages/bson#:~:text=BSON%20can%20be%20compared%20to,slight%20disadvantage%20of%20space%20efficiency.)
- [https://medium.com/@david_turner/protocol-buffers-just-how-fast-are-they-9ec19ea580db](https://medium.com/@david_turner/protocol-buffers-just-how-fast-are-they-9ec19ea580db)

### Video

Here is the YouTube video shared by the author of the medium article mentioned above:

{% include embed/youtube.html id='sav7X7_XgKc' %}
