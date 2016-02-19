# EasyCoreData

`EasyCoreData` it's a clean and simple way to start using CoreData in your iOS project.

# Integration

Drag the EasyCoreData folder into your project.
Optionally you can use it as cocoa touch framework as it appears in the demo project.
To launch app which uses Cocoa Touch framework on the device you might need to add EasyCoreData.framework to 'embedded binaries' on Your_Project->General page

# Setup

`EasyCoreData` don't required any setup and you can start to work as far as you have added it into your project.
In some cases you may need to setup `sqliteStoreURL`, `modelName`, `modelURL`, `modelBundle` or `persistentStoreCoordinatorOptions` before you start

Simply set values into EasyCoreData singleton class like this:

`EasyCoreData.sharedInstance.modelName = "MyCustomModelName"`

If you need to force setup all CoreData related properties you can call `EasyCoreData.sharedInstance.setup()` method, otherwise EasyCoreData will load all properties in lazy-load mode

# Usage

- access main thread context: `NSManagedObjectContext.mainThreadContext`
- create tempolorary main thread context: `NSManagedObjectContext.temporaryMainThreadContext`

#### Save data in background:
```Swift
NSManagedObjectContext.saveDataInBackground({ localContext in
      for (index, dict) in enumerate(objects) {
			let album = localContext.createEntity(Album)
			album.mapFromJSONDict(dict, context: localContext)
		  }
	    }) {
			print("All done")
	}
```

#### Create entity

```Swift
let user = NSManagedObjectContext.mainThreadContext.createEntity(User)
```
or `NSManagedObject` class function, type cast is required this way
```Swift
let user = User.createEntity() as! User
```
You can add additional param `inContext` if needed here
```Swift
let user = User.createEntity(inContent: myContext) as! User
```

#### Fetching objects

```Swift
let album = context.fetchFirstOne(Album)
let songs = context.fetchObjects(Track.self, predicate: NSPreicate(format: "album == %@", album), sortDesriptors: [NSSortDescriptor(key: "sortingOrder", ascending: true)])
```
or
```Swift
let album = Album.findFirstOne() as? Album
let tracks = Track.findAll() as? [Track]
```
`predicate` and `sortDescriptors` params here are optional you can simply skip it

#### Asynchronous objects fetching (iOS 8+)

```Swift
context.fetchObjectsAsynchronously(Movie.self, predicate: NSPredicate("artist == 'Christopher Nolan'", sortDescriptors: [NSSortDescriptor(key: "releaseDate", ascending: false)]) { movies in }
```

#### Obtaining information about number of entities

```Swift
User.countOfEntities()
Track.hasAtLeastOneEntity(predicate: NSPredicate(format: "album == %@", album))
```
You can add additional params `predicate`, `inContext` if needed

#### Get entity from other context

```Swift
let localObject = localContext.entityFromOtherContext(object)
```
or 
```Swift
let localUser = user.inContext(localContext) as? User
```

#### Aggregation operation

```Swift
let value = Album.aggregateOperation("max:", onAttribute: "trackCount")?.intValue {
    println("Record track count in album is \(value)")
}
```
you can add `predicate` and `inContext` parameters if needed

#### Use within existing architecture
You even can setup your own main NSManagedObjectContext and use all the fetching, creating, removing and aggregation stuff with your own solution. To achieve that you simply call
```Swift
NSManagedObjectContext.setupMainThreadManagedObjectContext(myContext)
```
