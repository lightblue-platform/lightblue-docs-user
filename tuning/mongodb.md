# MongoDB
You can monitor your MongoDB using a set of tools suggested by MongDB.com described [here](http://docs.mongodb.org/manual/administration/monitoring/) but you may also find interresting using other tools such as iostat to verify the I/O opeartion in your machine which maybe you have to tune your OS or even to upgrade your EBS service, for example. For more detail about optimization on MongoDB you can go to [their documentation](http://docs.mongodb.org/manual/administration/optimization/).

Also, you can `index` your data in MongoDB to  aster queries, but that will also increase the disk utilization. Also, there is an important note from [MongoDB's documentation](http://docs.mongodb.org/manual/applications/indexes/):

"MongoDB can only use one index to support any given operation. However, each clause of an $or query may use a different index."

