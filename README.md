## Memoria (Simple)
Memoria is a key-value store. This is a fairly simple implementation of a key-value store, meant for beginners who do not have much experience in writing Go. You can contribute to this to get a feel for key value stores and cache systems. Once you read the codebase and solve some issues you are welcome to move onto the advanced version which introduces you to new concepts like **client-server** architectures, **pubsub** and more!.

It provides a simple API for storing arbitrary data on the filesystem by key. What is API you may ask? An API is nothing but a way to interact with the program. 
In the simple format we introduce you to some basic datastructures and basic golang primitives like writers,channels, Mutexes etc. 



## API

This is what the API should look like. If you implement new features please see the below API's to see what your program should do.


### Primitive APIs
Currently the idea is to implement simple primitive methods like `Read`,`Write`,`Keys`,`Erase`,`Put`.
We may also introduce efficient Wrapper methods like `GetSet`.




```golang
package main

import (
    "log"

    memoria "github.com/utkarshdagoat/memoria/simple"
)

func main() {
    // Opens the database in a given directory located in path_to_fs
    // The dir will be created if it doesn't exist
    db, err := memoria.Open(
        memoria.Options {
            CacheSizeMax: 1024*1024, // the cache has a size of 10 Megabytes
            CacheType: memoria.CacheType.FIFO, // type of cache ejection/retention scheme learn more about it in caches.
            PathTransform: func() *memoria.PathKey {} // this is some function to convert the path of data in the file systems to some meaningfull value like content addressable strings etc. m,
            DataStructure: nil
        }, // set para
        memoria.WithDir("path_to_db")
    )
    
    if err != nil {
        log.Fatal(err)
    }
    
    defer db.Close()



    // Write three bytes to key "img"
    key := "img"
    err := db.Write(key, []byte{"made","with","love"})

    // read three bytes from the key
    value, err := db.Read(key) // the value here should be []byte{"made" , "with","love"}

    keys, err := db.Keys() // returns all the keys maybe by some order accessible by the store. 

    db.Erase(key) // rase the given key
}
```

## DataStrucutures API 
Implemeting datastructure will be hard and a lot of points. So if you want a challenge and do something interesting. Initiative is key so if you want to do this and want a little help reach out to me on discord or slack!.

For DataStructures the idea is to introduced things like `SortedSetStore` (Read more about it [here](https://jothipn.github.io/2023/04/07/redis-sorted-set.html)) , implement `Serialization` and `Deserialization` methods for the data structure. 
Here is a outline of what will Happen.

```golang
package main

import (
    "log"

    memoria "github.com/utkarshdagoat/memoria/simple"
)

func main() {
    // Opens the database in a given directory located in path_to_fs
    // The dir will be created if it doesn't exist
    db, err := memoria.Open(
        memoria.SortedSetOptions(), // set para
        memoria.WithDir("path_to_db")
    )
    
    if err != nil {
        log.Fatal(err)
    }
    
    defer db.Close()



    // Write three bytes to key "img"
    key := []byte("img")
    value := []byte{"made","with","love"}
    err := db.Add(key,1,value);

    members , err := db.Members() // here members is a map[*SortedSetMember] 
    // see all the API's for DataStrucutures below
}
```
**Operations**
- `Add`: Adds the specified member with the specified score into the sorted set specified by key.
- `Cardinality`: Returns the Number of elements of the sorted set by key in the bucket
- `Count`: Returns the count with min and max scores
- `Score`: score of the members in a sorted set by a key
- `Members`: Returns all the members and scores of the set specified by key in a bucket 

More operations which will be added like `PeekMax`,`PopMax`,`PopMin` etc.