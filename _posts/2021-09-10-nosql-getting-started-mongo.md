---
title: "Getting started MongoDB"
categories:
  - NoSql
tags:
  - NoSql
  - mongo
---

A database stores one or more collections of documents.
MongoDB stores documents in collections.
A record in MongoDB is a document.
MongoDB documents are similar to JSON objects.
The values of fields may include other documents, arrays,
and arrays of documents.
If a collection does not exist, MongoDB creates the
collection when you first store data for that collection

MongoDB server runs on the default port, which is 27017.

```sh
# refers to your current database
>db

# show dbs
>tcdoddshow dbs
```

```sh
# switch databases
>use db
```

```sh
# create collection
>db.createCollection()
```

```sh
# insert one creates both the database myNewDB and the collection myNewCollection1 if they do not already exist.
> use myNewDB
>db.myNewCollection1.insertOne( { x: 1 } )
# insert new documents into the movies collection.
# The operation returns a document that contains the acknowledgement
# indicator and an array that contains
# the \_id of each successfully inserted documents.

> db.movies.insertMany([
>  {

      title: 'The Dark Knight',

},
{
title: 'Casablanca',
genres: [ 'Drama', 'Romance', 'War' ],
 cast: [ 'Humphrey Bogart', 'Ingrid Bergman', 'Paul Henreid', 'Claude Rains' ],
awards: {
wins: 9,
nominations: 6,
text: 'Won 3 Oscars. Another 6 wins & 6 nominations.'
},
lastupdated: '2015-09-04 00:22:54.600000000',

}
])

> {
> acknowledged: true,
> insertedIds: {

    '0': ObjectId("6162aa0c9a8661f913868109"),
    '1': ObjectId("6162aa0c9a8661f91386810a")

}
}
```

```sh
# select the documents from a collection

> db.movies.find( { } )
```

```sh
# Filter Data $gt,$lt, $in
>db.movies.find( { "directors": "Christopher Nolan" } );
>db.movies.find( { "released": { $lt: ISODate("2000-01-01") } } );
>db.movies.find( { "languages": { $in: [ "Japanese", "Mandarin" ] } } )

#Specify Fields to Return
>db.movies.find( { }, { "_id": 0, "title": 1, "genres": 1 } );

```

```sh
# Aggregation
>db.movies.aggregate( [
   { $unwind: "$genres" }, #output a document of every genres field
   {
     $group: { #  accumulator to count the number of occurrences of each genre
       _id: "$genres",
       genreCount: { $count: { } } # The count is stored in the genreCount field
     }
   },
   { $sort: { "genreCount": -1 } } # sort desc
] )
```

## Links

<https://docs.mongodb.com/>
