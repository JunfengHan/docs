## 全局

#### 1. 查看帮助信息

**db.help( ):**

```shell
# 部分 DB methods:
	db.getCollectionNames() # 查看有哪些 connection
	db.getCollection(cname) # same as db['cname'] or db.cname
	db.createCollection(name, {size: ..., capped: ..., max: ...}) # 创建集合

	db.aggregate([pipeline], {options}) # 聚合
	db.auth(username, password)	#	用户认证
	db.cloneDatabase(fromhost) # will only function with MongoDB 4.0 and below
	db.commandHelp(name) # 返回命令行帮助信息
	db.createUser(userDocument) # 创建用户
	db.getCollectionInfos([filter]) # returns a list that contains the names and options of the db's collections
	db.getMongo() # get the server connection object
	db.getName() # 当前数据库名称
	db.printCollectionStats() # 查看集合状态
	db.runCommand(cmdObj) # run a database command.  if cmdObj is a string, turns it into {cmdObj: 1}
	db.stats() # 当前数据库状态
	db.version() # current version of the server
```

查看 user 集合的帮助信息，

**db.user.help( ):**

```shell
# 部分DBCollection help
	db.user.find().help() # show DBCursor help
	db.user.bulkWrite( operations, <optional params> ) - bulk execute write operations, optional parameters are: w, wtimeout, j
	db.user.count( query = {}, <optional params> ) - count the number of documents that matches the query, optional parameters are: limit, skip, hint, maxTimeMS
	db.user.countDocuments( query = {}, <optional params> ) - count the number of documents that matches the query, optional parameters are: limit, skip, hint, maxTimeMS
	db.user.estimatedDocumentCount( <optional params> ) - estimate the document count using collection metadata, optional parameters are: maxTimeMS
	db.user.convertToCapped(maxBytes) - calls {convertToCapped:'user', size:maxBytes}} command
	db.user.createIndex(keypattern[,options])
	db.user.createIndexes([keypatterns], <options>)
	db.user.dataSize()
	db.user.deleteOne( filter, <optional params> ) - delete first matching document, optional parameters are: w, wtimeout, j
	db.user.deleteMany( filter, <optional params> ) - delete all matching documents, optional parameters are: w, wtimeout, j
	db.user.distinct( key, query, <optional params> ) - e.g. db.user.distinct( 'x' ), optional parameters are: maxTimeMS
	db.user.drop() drop the collection
	db.user.dropIndex(index) - e.g. db.user.dropIndex( "indexName" ) or db.user.dropIndex( { "indexKey" : 1 } )
	db.user.dropIndexes()
	db.user.ensureIndex(keypattern[,options]) - DEPRECATED, use createIndex() instead
	db.user.explain().help() - show explain help
	db.user.reIndex()
	db.user.find([query],[fields]) # query is an optional query filter
	                                              e.g. db.user.find( {x:77} , {name:1, x:1} )
	db.user.find(...).count()
	db.user.find(...).limit(n)
	db.user.find(...).skip(n)
	db.user.find(...).sort(...)
	db.user.findOne([query], [fields], [options], [readConcern])
	db.user.findOneAndDelete( filter, <optional params> ) - delete first matching document, optional parameters are: projection, sort, maxTimeMS
	db.user.findOneAndReplace( filter, replacement, <optional params> ) - replace first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
	db.user.findOneAndUpdate( filter, <update object or pipeline>, <optional params> ) - update first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
	db.user.getDB() get DB object associated with collection
	db.user.getPlanCache() get query plan cache associated with collection
	db.user.getIndexes()
	db.user.insert(obj)
	db.user.insertOne( obj, <optional params> ) - insert a document, optional parameters are: w, wtimeout, j
	db.user.insertMany( [objects], <optional params> ) - insert multiple documents, optional parameters are: w, wtimeout, j
	db.user.mapReduce( mapFunction , reduceFunction , <optional params> )
	db.user.aggregate( [pipeline], <optional params> ) - performs an aggregation on a collection; returns a cursor
	db.user.remove(query)
	db.user.replaceOne( filter, replacement, <optional params> ) - replace the first matching document, optional parameters are: upsert, w, wtimeout, j
	db.user.renameCollection( newName , <dropTarget> ) renames the collection.
	db.user.runCommand( name , <options> ) runs a db command with the given name where the first param is the collection name
	db.user.save(obj)
	db.user.stats({scale: N, indexDetails: true/false, indexDetailsKey: <index key>, indexDetailsName: <index name>})
	db.user.storageSize() - includes free space allocated to this collection
	db.user.totalIndexSize() - size in bytes of all the indexes
	db.user.totalSize() - storage allocated for all data and indexes
	db.user.update( query, <update object or pipeline>[, upsert_bool, multi_bool] ) - instead of two flags, you can pass an object with fields: upsert, multi, hint
	db.user.updateOne( filter, <update object or pipeline>, <optional params> ) - update the first matching document, optional parameters are: upsert, w, wtimeout, j, hint
	db.user.updateMany( filter, <update object or pipeline>, <optional params> ) - update all matching documents, optional parameters are: upsert, w, wtimeout, j, hint
	db.user.validate( <full> ) - SLOW
	db.user.getShardVersion() - only for use with sharding
	db.user.getShardDistribution() - prints statistics about data distribution in the cluster
	db.user.getSplitKeysForChunks( <maxChunkSize> ) - calculates split points over all chunks and returns splitter function
	db.user.getWriteConcern() - returns the write concern used for any operations on this collection, inherited from server/db if set
	db.user.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the collection
	db.user.unsetWriteConcern( <write concern doc> ) - unsets the write concern for writes to the collection
	db.user.latencyStats() - display operation latency histograms for this collection
```

查看 find 的帮助信息：

#### db.user.find( ).help( ):

```shell
find(<predicate>, <projection>) modifiers
	.sort({...})
	.limit(<n>)
	.skip(<n>)
	.batchSize(<n>) # sets the number of docs to return per getMore
	.collation({...})
	.hint({...})
	.readConcern(<level>)
	.readPref(<mode>, <tagset>)
	.count(<applySkipLimit>) # total of objects matching query
	.size() # total of objects cursor would return, honors skip,limit
	.explain(<verbosity>) # accepted verbosities are {'queryPlanner', 'executionStats', 'allPlansExecution'}
	.min({...})
	.max({...})
	.maxTimeMS(<n>)
	.comment(<comment>)
	.tailable(<isAwaitData>)
	.noCursorTimeout()
	.allowPartialResults()
	.returnKey()
	.showRecordId() # adds a $recordId field to each returned object

Cursor methods
	.toArray() # iterates through docs and returns an array of the results
	.forEach(<func>)
	.map(<func>)
	.hasNext()
	.next()
	.close()
	.objsLeftInBatch() # returns count of docs left in current batch (when exhausted, a new getMore will be issued)
	.itcount() # iterates through documents and counts them
	.pretty() # pretty print each document, possibly over multiple lines
```

#### 3. 全局命令

注意：查询结果和认证的用户有关哦

```shell
# 查看当前数据库
db
# 查看数据库列表
show dbs
# 切换当前数据库,不存在的话就新建一个
use dbName
```
