# mongo-leader

MongoDB based leader election for go applications.

Motivation: I have an application where I already have the complexity of MongoDB and wanted simple 
leader election without introducing another complex component like Zookeeper or Consul.

Your MongoDB cluster must be a replica set.  This is a requirement of MongoDB's change stream feature
which is used by this library.

Simply pass enough information for this library to connect to your MongoDB replica set and implement two callback 
interfaces: LeaderWorker and FollowerWorker.  LeaderWorker.Start() is called when this instance wins an election.
LeaderWorker.Stop() is called when this instance loses an election.  FollowerWorker.Start() is called when this
instance loses an election.  FollowerWorker.Stop() is called when this instance wins an election.

## Usage

Example
```
// Creates a new instance of Elector for the given boundary.
// ctx - your context
// boundary - a unique case-sensitive string (conventionally a path). Only one election can take place in a boundary at a time.
// database - a mongodb database name (default: "Elector").  Must be shared by all Electors in a boundary. I suggest be shared by all Electors across all boundaries.
// leaderWorker - leaderWorker.Start() is called when this instance wins an election.  leaderWorker.Stop() is called when this instance loses an election
// followerWorker - followerWorker.Start() is called when this instance loses an election.  followerWorker.Stop() is called when this instance wins an election
// thisInstanceLeaderHostname - a hostname that will be passed to followers they can use to connect to a service on the leader, can be empty
// thisInstanceLeaderPort - a port that will be passed to followers they can use to connect to a service on the leader, can be empty
// type LeaderWorker interface {
// 	// Start the worker. May be called multiple times in a row.
// 	Start(ctx context.Context)
// 
// 	// Stop the worker. May be called multiple times in a row.
// 	Stop()
// }
// 
// type FollowerWorker interface {
// 	// Start the worker. May be called multiple times in a row.
// 	Start(ctx context.Context, thisLeaderUUID CandidateId)
// 
// 	// Stop the worker. May be called multiple times in a row.
// 	Stop()
// }
elector, err := mongoelector.NewElector(
    ctx,  
    mongostore.Instance(),
    boundary,
    leaderWorker,
    followerWorker,
    "host.docker.internal",
    uint64(8081),
    mongoelector.NewElectorOptions(),
)
```

## Contributing

Happy to accept PRs.

# Author

**davidwartell**

* <http://github.com/davidwartell>
* <http://linkedin.com/in/wartell>
