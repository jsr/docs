====================
JavaScript Interface
====================

.. default-domain:: mongodb

Data Manipulation
-----------------

.. _js-query-and-update-methods:

Query and Update Methods
~~~~~~~~~~~~~~~~~~~~~~~~

.. function:: find(query,projection)

   :param JSON query: A :term:`JSON document` that specifies the
                     :term:`query` using valid :term:`JSON` and
                     :doc:`query operators </reference/operators>`.

   :param JSON projection: A JSON document that controls the
                           :term:`projection`, or the contents of the
                           data returned.

   :returns: All of the documents that match the ``query`` document.

   Provides access to querying functionality. The argument to
   :func:`find()` takes the form of a :term:`JSON` document. See
   the ":doc:`/reference/operators`" for an overview of the available
   operators for specifying and narrowing the query.

.. function:: findOne(query)

   :param JSON query: A JSON document that specifies the :term:`query`
                     using valid JSON and :doc:`query operators
                     </reference/operators>`.

   :returns: One document that satisfies the query specified as the
             argument to this method.

   Returns only one document that satisfies the specified query. If
   multiple documents satisfy the query, this method returns the first
   document according to the :term:`natural order` which reflects the
   order of documents on the disc. In :term:`capped collections
   <capped collection>`, natural order is the same as insertion order.

.. function:: findAndModify()

   The :func:`findAndModify()` method atomically modifies and
   returns a single document. Always call :func:`findAndModify()`
   on a collection object, using the following form:

   .. code-block:: javascript

      db.collection.findAndModify();

   Replace, "``collection``" with the name of the collection
   containing the document that you want to modify, and specify
   options, as a sub-document that specifies the following:

   :field query: A query object. This statement might resemble the
                  :term:`JSON document` passed to :func:`find()`,
                  and should return *one* document from the database.

   :field optional sort: If the query selects multiple documents, the
                         first document given by this sort clause will
                         be the one modified.

   :field remove: When ``true``, :dbcommand:`findAndModify` removes
                  the selected document.

   :field update: an :ref:`update operator <update-operators>` to
                  modify the selected document.

   :field new: when ``true``, returns the modified document rather
               than the original. :dbcommand:`findAndModify` ignores
               the ``new`` option for ``remove`` operations.

   :field fields: a subset of fields to return. See ":ref:`projection
                  operators <projection-operators>`" for more
                  information.

   :field upsert: when ``true``, creates a new document if the
                  specified ``query`` returns no documents. The
                  default is "``false``.

   For example:

   .. code-block:: javascript

      db.people.findAndModify( {
          query: { name: "Tom", state: "active", rating: { $gt: 10 } },
          sort: { rating: 1 },
          update: { $inc: { score: 1 } }
          } );

   This operation finds a document in the "``people``" collection
   where the "``name``" field has the value "``Tom``", the
   "``active``" value in the "``state``" field and a value in the
   "``rating``" field :operator:`greater than <$gt>` 10. If there is
   more than one result for this query, MongoDB sorts the results of
   the query in descending order, and :operator:`increments <$inc>`
   the value of the "``score``" field by 1.

   .. warning::

      When using :dbcommand:`findAndModify` in a :term:`sharded
      <sharding>` environment, the ``query`` must contain the
      :term:`shard key` for all operations against the shard
      cluster. :dbcommand:`findAndModify` operations issued against
      :program:`mongos` instances for non-sharded collections function
      normally.

.. function:: save()

   Provides the ability to create a new document in the current
   database and collection. The argument to :func:`save()` takes
   the form of a :term:`JSON document`. See ":ref:`update-operators`"
   for a reference of all operators that affect updates.

.. function:: update(query, update, [upsert,] [multi])

   The :func:`update()` takes the following for arguments.

   :param query: A query object that selects the record to update. Use
                 the :ref:`query selectors <query-selectors>` as you
                 would in a :func:`find()` operation.

   :param update: A :term:`JSON document` that will either replace the
                  matching document, or use :ref:`update operators
                  <update-operators>` to describe the update. By
                  default, this operation *only* updates one document.

   :param boolean upsert: Defaults to ``false``. When ``true``, this
                          operation will update a document if one
                          matches the query portion and inserts a new
                          document if *no* documents match the query
                          portion. Upsets only affect *one* document,
                          and cannot update more than one document.

   :param boolean multi: Defaults to ``false``. When ``true``, all
                         the operation updates all documents that
                         match the query.

   Provides the ability to update an existing document in the current
   database and collection. The argument to :func:`update()` takes
   the form of a :term:`JSON document`. See ":ref:`update-operators`"
   for a reference of all operators that affect updates.

Query Modifiers
~~~~~~~~~~~~~~~

.. function:: next()

   :returns: The next document in the cursor returned by the
             :func:`find()` method. See :func:`hasNext()` for
             related functionality.

.. function:: size()

   :returns: A count of the number of documents that match the
             :func:`find()` query.

.. function:: explain()

   :returns: A document that describes the process used to return the
             query.

   This method may provide useful insight when attempting to optimize
   a query.

   .. seealso:: :operator:`$explain` for related functionality and
      ":doc:`/applications/optimization`" regarding optimization
      strategies.

.. function:: showDiskLoc()

   :returns: A document that describes the on-disk location of the
             objects returned by the query.

   .. seealso:: :operator:`$showDiskLoc` for related
      functionality.

.. function:: forEach()

   Provides the ability to loop or iterate over the cursor returned by
   a :func:`find()` query and returns each result on the
   shell. Specify a JavaScript function as the argument for the
   :func:`forEach()` function. Consider the following example:

   .. code-block:: javascript

      db.users.find().forEach( function(u) { print("user: " + u.name); } );

   .. seealso:: :func:`map()` for similar functionality.

.. function:: map()

   Provides the ability to loop or iterate over the cursor returned by
   a :func:`find()` query and returns each result as the member of
   an array. Specify a JavaScript function as the argument for the
   :func:`map()` function. Consider the following example:

   .. code-block:: javascript

      db.users.find().map( function(u) { print("user: " + u.name); } );

   .. seealso:: :func:`forEach()` for similar functionality.

.. function:: hasNext()

   :returns: boolean.

   :func:`hasNext()` returns ``true`` if the cursor returned by the
   :func:`find()` query contains documents can iterate further to
   return results.

.. _js-query-cursor-methods:

Query Cursor Methods
~~~~~~~~~~~~~~~~~~~~

.. function:: count()

   :param boolean override: Override the effects of the
                            :func:`skip()` and :func:`limit()`
                            methods on the

   Append the :func:`count()`` method on a ":func:`.find()`" query to
   return the number of matching objects for any query.

   In normal operation, :func:`count()` ignores the effects of the
   :func:`skip()` and :func:`limit()`. To consider these
   effects specify "``count(true)``".

.. function:: limit()

   Use the :func:`limit()` method on a ":func:`find()`" query
   to specifies the maximum number of documents a query will
   return. :func:`limit()` is analogous to the ``LIMIT`` statement
   in a SQL database.

   Use :func:`limit()` to maximize performance and prevent MongoDB
   from returning more results than required for processing.

   A :func:`limit()` value of 0 (e.g. "``.limit(0)``") is equivalent to
   setting no limit.

.. function:: skip()

   Call the :func:`skip()` method on a ":func:`.find()`" query
   to control where MongoDB begins returning results. This approach
   may be useful in implementing "paged" results. Consider the
   following JavaScript function as an example of the sort function:

   .. code-block:: javascript

        function printStudents(pageNumber, nPerPage) {
           print("Page: " + pageNumber);
           db.students.find().skip((pageNumber-1)*nPerPage).limit(nPerPage).forEach( function(student) { print(student.name + "<p>"); } );
        }

   The :func:`skip()` method is often expensive because it requires
   the server to walk from the beginning of the collection or index to
   get the offset or skip position before beginning to return
   result. As offset (e.g. ``pageNumber`` above) increases,
   :func:`skip()` will become slower and more CPU intensive. With
   larger collections, :func:`skip()` may become IO bound.

   Consider using range-based pagination for these kinds of
   tasks. That is, query for a range of objects, using logic within
   the application to determine the pagination rather than the
   database itself. This approach features better index utilization,
   if you do not need to easily jump to a specific page.

.. function:: snapshot()

   Append the :func:`snapshot()` method to the :func:`find()`
   query to toggle the "snapshot" mode. This ensures that the query
   will not miss any documents and return no duplicates, when other
   operations modify objects while the query runs. Snapshot mode only
   affects documents modified documents, not inserted or removed
   documents.

   Queries with results of less than 1 megabyte are effectively
   snapshotted.

.. function:: sort()

   Append the :func:`sort()` method to the :func:`find()`"
   queries to control the order that the query returns matching
   documents. Consider the following example:

   .. code-block:: javascript

      db.collection.find().sort( { age: -1 } );

   Here, the query returns all documents in ``collection`` ordered by
   the ``age`` field in descending order. Specify a value of negative
   one (e.g. "``-1``", as above) to sort in descending order or a
   positive value (e.g. "``1``") to sort in ascending order.

   Unless you have a index for the specified key pattern, use
   :func:`sort()` in conjunction with :func:`limit()` to avoid
   requiring MongoDB to perform a large in-memory
   sort. :func:`limit()` increases the speed and reduce the amount
   of memory required to return this query by way of an optimized
   algorithm.

   .. warning::

      The sort function requires that the entire sort be able to
      complete within 32 megabytes. When the sort option consumes more
      than 32 megabytes, MongoDB will return an error. Use
      :func:`limit()`, or create an index on the field that you're
      sorting to avoid this error.

.. function:: hint()

   :argument index: The name of the index to "hint" or force MongoDB
                    to use when performing the query.

   Call this method on a query to override MongoDB's default index
   selection and query optimization process. Specify, as an argument,
   the name which index the query should use to fulfill the query. Use
   :func:`getIndexes()` to return a list of indexes on the current
   collection.

   .. seealso:: ":operator:`$hint`

Data Aggregation
~~~~~~~~~~~~~~~~

.. function:: aggregate(pipeline)

   .. versionadded:: 2.1.0

   Always call the :func:`aggregate()` method on a collection
   object.

   :argument pipeline: Specifies a sequence of data aggregation
                       processes. See the :doc:`aggregation reference
                       </reference/aggregation>` for documentation of
                       these operators.

   Consider the following example from the :doc:`aggregation
   documentation </applications/aggregation>`.

   .. code-block:: javascript

      db.article.aggregate(
        { $project : {
           author : 1,
           tags : 1,
        } },
        { $unwind : “$tags” },
        { $group : {
           _id : { tags : 1 },
           authors : { $addToSet : “$author” }
        } }
      );

    .. seealso:: ":dbcommand:`aggregate`,"
       ":doc:`/applications/aggregation`," and
       ":doc:`/reference/aggregation`."

.. function:: group({key, reduce, initial, [keyf,] [cond,] finalize})

   The :func:`group()` accepts a single :term:`JSON document` that
   contains the following:

   :field key: Specify one or more fields to group by. Use the
               form of a :term:`JSON document`.

   :field reduce: Specify a reduce function that operates over all the
                  iterated objects. Typically these aggregator
                  functions perform some sort of summing or
                  counting. The reduce function takes two arguments:
                  the current document and an aggregation counter
                  object.

   :field inital: The starting value of the aggregation counter
                  object.

   :field optional keyf: An optional function that returns a "key
                         object" for use as the grouping key. Use
                         ``keyf`` instead of ``key`` to specify a key
                         that is not a single/multiple existing
                         fields. For example, use ``keyf`` to group by
                         day or week in place of a fixed ``key``.

   :field optional cond: A statement that must evaluate to true for
                         the :func:`group()` to process this
                         document. Essentially this argument specifies
                         a query document (as for
                         :func:`find()`). Unless specified,
                         :func:`group()` runs the "reduce" function
                         against all documents in the collection.

   :field optional finalize: An optional function that runs each item
                             in the result set before
                             :func:`group()` returns the final
                             value. This function can either modify
                             the document by computing and adding an
                             average field, or return compute and
                             return a new document.

   .. warning::

      :func:`group()` does not work in :term:`shard environments
      <shard cluster>`. Use the :term:`aggregation framework` or
      :term:`map/reduce` in :term:`sharded environments <sharding>`.

   .. note::

      The result set of the :func:`group()` must fit within the
      maximum :term:`BSON` object.

      Furthermore, you must ensure that there are fewer then 10,000
      unique keys. If you have more than this, use
      :dbcommand:`mapReduce`.

   :func:`group()` provides a simple aggregation capability similar
   to the function of "``GROUP BY``" in SQL statements. Use
   :func:`group()` to return counts and averages from collections
   of MongoDB documents. Consider the following example
   :func:`group()` command:

   .. code-block:: javascript

      db.collection.group(
                    {key: { a:true, b:true },
                     cond: { active: 1 },
                     reduce: function(obj,prev) { prev.csum += obj.c; },
                     initial: { csum: 0 }
                    });

   This command in for the :program:`mongo` shell groups the documents
   in the collection named "``collection``" by the ``a`` and ``b``
   fields, when the "``active``" field has a value of ``1``. Then, the
   reduce function, adds the current value of fields "``a``" "``b``"
   to the previous value of those fields. This is equivalent to the
   following SQL statement.

   .. code-block:: sql

      SELECT a,b,sum(c) csum FROM collection WHERE active=1 GROUP BY a,b

   .. seealso:: The ":doc:`/applications/simple-aggregation`" and
      ":doc:`/applications/aggregation`."

.. function:: mapReduce(map,reduce,out,[query],[sort],[limit],[finalize],[scope],[jsMode],[verbose])

   The :func:`mapReduce()` provides a wrapper around the
   :dbcommand:`mapReduce` :term:`database command`. Always call the
   :func:`mapReduce()` method on a collection. The following
   argument list specifies a :term:`JSON document` with 3 required and
   8 optional fields:

   :param map: A JavaScript function that performs the "map" step of
               the MapReduce operation. This function references the
               current input document and calls the
               "``emit(key,value)``" method that supplies values to
               the reduce function. Map functions may call ``emit()``,
               once, more than once, or not at all depending on the
               type of aggregation.

   :param reduce: A JavaScript function that performs the "reduce"
                  step of the MapReduce operation. The reduce function
                  receives an array of emitted values from the map
                  function, and returns a single value. Because it's
                  possible to invoke the reduce function more than
                  once for the same key, the structure of the object
                  returned by function must be identical to the
                  structure of the emitted function.

   :param out: Specifies the location of the out of the reduce stage
               of the operation. Specify a string to write the output
               of the Map/Reduce job to a collection with that
               name. See below for additional output options.

   :param optional query: A query object, like the query used by the
                          :func:`find()` method. Use this to filter
                          to limit the number of documents enter the
                          map phase of the aggregation.

   :param optional sort: Sorts the input objects using this key. This
                         option is useful for optimizing the
                         job. Common uses include sorting by the emit
                         key so that there are fewer reduces.

   :param optional limit: Species a maximum number of objects to
                          return from the collection.

   :param optional finalize: Specifies an optional "finalize" function
                             to run on a result, following the reduce
                             stage, to modify or control the output of
                             the :func:`mapReduce()` operation.

   :param optional scope: Place a :term:`JSON` document as the contents
                          of this field, to place fields into the
                          global javascript scope.

   :param optional jsMode: Boolean. The ``jsMode`` option defaults to
                           true.

   :param optional verbose: Boolean. The ``verbose`` option provides
                            statistics on job execution times.

   The "``out``" field of the :func:`mapReduce()`, provides a
   number of additional configuration options that you may use to
   control how MongoDB returns data from the map/reduce job. Consider
   the following 4 output possibilities.

   .. versionadded: 1.8

   :param optional replace: Specify a collection name (e.g. ``{ out: {
                            replace: collectionName } }``) where the
                            output of the map/reduce overwrites the
                            contents of the collection specified
                            (i.e. "``collectionName``") if there is
                            any data in that collection.

   :param optional merge: Specify a collection name (e.g. ``{ out: {
                          merge: collectionName } }``) where the
                          map/reduce operation writes output to an
                          existing collection
                          (i.e. "``collectionName``",) and only
                          overwrites existing documents when a new
                          document has the same key as an "old"
                          document in this collection.

   :param optional reduce: This operation behaves as the "``merge``"
                           option above, except that when an existing
                           document has the same key as a new
                           document, "``reduce``" function from the
                           map reduce job will run on both values and
                           MongoDB writes the result of this function
                           to the new collection. The specification
                           takes the form of "``{ out: { reduce:
                           collectionName } }``", where
                           "``collectionName``" is the name of the
                           results collection.

   :param optional inline: Indicate the inline option (i.e. "``{ out:
                           { inline: 1 } }``") to perform the map
                           reduce job in ram and return the results at
                           the end of the function. This option is
                           only possible when the entire result set
                           will fit within the :ref:`maximum size of a
                           BSON document
                           <limit-maximum-bson-document-size>`. When
                           performing map/reduce jobs on secondary
                           members of replica sets, this is the only
                           available option.

   .. seealso:: ":doc:`/core/map-reduce`, provides a greater overview
      of MognoDB's map/reduce functionality. Consider
      ":doc:`/applications/simple-aggregation` for simple aggregation
      operations and ":doc:`/applications/aggregation`" for a more flexible
      approach to data aggregation in MongoDB.

Administrative Functions
------------------------

Database
~~~~~~~~

.. function:: db.addUser("username", "password"[, readOnly])

   :param string username: Specifies a new username.

   :param string password: Specifies the corresponding password.

   :param boolean readOnly: Optionally restricts a user to read-privileges
                            only. Defaults to false.

   Use this function to create new database users, by specifying a
   username, password as arguments to the command. If you want to
   restrict this user to only have read-only privileges; however, this
   defaults to false.

.. function:: db.auth("username", "password")

   :param string username: Specifies an existing username with access
                           privileges for this database.

   :param string password: Specifies the corresponding password.

   Allows a user to authenticate to the database from within the
   shell. Alternatively use :option:`mongo --username` and
   :option:`--password <mongo --password>` to specify authentication
   credentials.

.. function:: db.cloneDatabase("hostname")

   :param string hostname: Specifies the hostname to copy the current
                           instance.

   Use this function to copy a database from a remote to the current
   database. The command assumes that the remote database has the same
   name as the current database. Use the following command to change
   to the database "``importdb``":

   .. code-block:: javascript

      use importdb

   New databases are implicitly created, so the current host does not
   need to have a database named ``importdb`` for this command to
   succeed.

   This function provides a wrapper around the MongoDB :term:`database
   command` ":dbcommand:`clone`." The :dbcommand:`copydb` database command
   provide related functionality.

.. function:: db.commandHelp(command)

   :param command: Specifies a :doc:`database command name
                   </reference/commands>`.

   :returns: Help text for the specified :term:`database command`. See
             the :doc:`database command reference
             </reference/commands>` for full documentation of these
             commands.

.. function:: db.copyDatabase(origin, destination, hostname)

   :param database origin: Specifies the name of the database on the
                           origin system.

   :param database destination: Specifies the name of the database
                                that you wish to copy the origin
                                database into.

   :param origin hostname: Indicate the hostname of the origin database
                           host.

   Use this function to copy a specific database, named "``origin``"
   running on the system accessible via "``hostname``" into the local
   database named "``destination``". The command creates destination
   databases implicitly when they do not exit.

   This function provides a wrapper around the MongoDB :term:`database
   command` ":dbcommand:`copydb`." The :dbcommand:`clone` database
   command provides related functionality.

.. function:: db.createCollection(name [{size: <value>, capped: <boolean> , max <bytes>}] )

   :param string name: Specifies the name of a collection to create.

   :param JSON capped: Optional. If specified this document creates a
                       capped collection. The capped argument is a
                       JSON document that contains the following three
                       fields:

   :param bytes size: If ``capped`` is ``true``, ``size`` Specifies a
                      maximum size in bytes, for the as a ":term:`cap
                      <capped collection>` for the collection. When
                      ``capped`` is false, you may use ``size``

   :param boolean capped: Enables a :term:`collection cap <capped
                          collection>`. False by default. If enabled,
                          you must specify a ``size`` parameter.

   :param int max: Optional. Specifies a maximum "cap," in number of
                   documents for capped collections. You must also
                   specify ``size`` when specifying ``max``.

   Explicitly creates a new collation. Because MongoDB creates
   collections implicitly when referenced, this command is primarily
   used for creating new capped collections. In some circumstances,
   you may use this command to pre-allocate space for a uncapped
   collection.

   Capped collections have maximum size or document counts that limit
   their ability to grow beyond maximum thresholds. All capped
   collections must specify a maximum size, but may also specify a
   maximum document count. The collection will remove older documents
   if a collection reaches the maximum size limit before it reaches
   the maximum document count. Consider the following example:

   .. code-block:: javascript

      db.createCollection(log, { size : 5120, capped : true, max : 5000 } )

   This command creates a collection named log with a maximum size of
   5 megabytes (5120 bytes,) or a maximum of 5000 documents.

   The following command simply pre-allocates a 2 gigabyte, uncapped,
   collection named "``people``":

   .. code-block:: javascript

      db.createCollection(people, { size: 2147483648 })

   This command provides a wrapper around the database command
   ":dbcommand:`create`. See the ":doc:`/core/capped-collections`"
   document for more information about capped collections.

.. function:: db.currentOp()

   :returns: A :term:`JSON` document that contains an array named
             "``inprog``".

   The ``inprog`` array reports the current operation in progress for
   the database instance.

.. function:: db.dropDatabase()

   Removes (and deletes) the current database. Does not change the
   current database, so the creation of any documents in this database
   will create.

.. function:: db.eval(function, arguments)

   :param JavaScript function: A JavaScript function.

   :param arguments: A list of arguments to pass to the JavaScript
                     function.

   Provides the ability to run JavaScript code using the JavaScript
   engine embeded in the MongoDB instance. In this environment the
   value of the "``db``" variable on the server is the name of the
   current database.

   Unless you use :func:`db.eval()`, the :program:`mongo` shell
   itself will evaluate all JavaScript entered into :program:`mongo`
   shell itself.

   .. warning::

      Do not use :func:`db.eval()` for long running operations, as
      :func:`db.eval()` blocks all other operations. Consider using
      :doc:`map reduce </core/map-reduce>` for similar functionality in
      these situations.

      The :func:`db.eval() method cannot operate on sharded
      data. However, you may use :func:`db.eval()` with non-sharded
      collections and databases stored in :term:`shard cluster`.

.. function:: db.getCollection(name)

   :param name: The name of a collection.

   :returns: The name of a collection.

   Use this command to describe collections that may interact with the
   shell itself, including collections with names that begin with
   "``_``" or mirror the :doc:`database commands
   </reference/commands>`.

.. function:: db.getCollectionNames()

   :returns: An array containing all collections in the existing
             database.

.. function:: db.getLastError()

   :returns: The last error message as a string.

   In many situation MongoDB drivers and users will, attach this
   command to a write operation to ensure that writes succeed. Using
   This "safe mode" is ideal for many--but not all--write operations.

   .. seealso:: ":ref:`Replica Set Write Concern <replica-set-write-concern>`"
      and ":dbcommand:`getLastError`."

.. function:: db.getLastErrorObj()

   :returns: A full :term:`JSON document` with status information.

.. function:: db.getMongo()

   :returns: The current connection status.

   :func:`db.getMongo()` returns when the shell initiates. Use this
   command to test that the :program:`mongo` shell has a connection to
   the proper database instance.

.. function:: db.setSlaveOk()

   For the current session, this command permits read operations from
   non-master (i.e. :term:`slave` or :term:`secondary`)
   instances. Practically, use this method in the following form:

   .. code-block:: javascript

      db.getMongo().setSlaveOK()

   Indicates that ":term:`eventually consistent <eventual
   consistency>`" read operations are acceptable for the current
   connection. This function provides the same functionality as
   :func:`rs.slaveOk()`.

.. function:: db.getName()

   :returns: the current database name.

.. function:: db.getPrevError()

   :returns: A status document, containing the errors.

   This output reports all errors since the last time the database
   received a :dbcommand:`resetError` (also
   :func:`db.resetError()`) command.

   This method provides a wrapper around the
   :dbcommand:`getPrevError` command.

.. function:: db.getProfilingLevel()

   This method provides a wrapper around the database command
   ":dbcommand:`profile`" and returns the current profiling level.

   .. deprecated:: 1.8.4
      Use :func:`db.getProfilingStatus()` for related functionality.

.. function:: db.getProfilingStatus()

   :returns: The current :dbcommand:`profile` level and
             :setting:`slowms` setting.

.. function:: db.getReplicationInfo()

   :returns: A status document.

   This output reports statistics related to replication.

   .. seealso:: ":doc:`/reference/replication-info`" for full
      documentation of this output.

.. function:: db.getSiblingDB()

   Used to return another database without modifying the current
   "``db``" setting in the shell environment.

.. function:: db.killOP(opid)

   :param oppid: Specify an operation ID.

   Terminates the specified operation. Use :func:`db.currentOp()`
   to determine the current operation.

.. function:: db.listCommands()

   Provides a list of all database commands. See the
   ":doc:`/reference/commands`" document for a more extensive index of
   these options.

.. function:: db.logout()

   Forces the current session to end the current authentication
   session. This function has no effect if the current session is not
   authenticated.

   This function provides a wrapper around the database command
   ":dbcommand:`logout`".

.. function:: db.printCollectionStats()

   Provides a wrapper around the :func:`stats()` method. Returns
   statistics from every collection separated by three hyphen
   characters.

   .. seealso:: ":doc:`/reference/collection-statistics`"

.. function:: db.printReplicationInfo()

   Provides a formatted report of the status of a :term:`replica set`
   from the perspective of the :term:`primary` set member. See the
   ":doc:`/reference/replica-status`" for more information regarding
   the contents of this output.

   This function will return :func:`db.printSlaveReplicationInfo()`
   if issued against a :term:`secondary` set member.

.. function:: db.printSlaveReplicationInfo()

   Provides a formatted report of the status of a :term:`replica set`
   from the perspective of the :term:`secondary` set member. See the
   ":doc:`/reference/replica-status`" for more information regarding
   the contents of this output.

.. function:: db.printShardingStatus()

   Provides a formatted report of the status of the shards and the
   information regarding the chunks of the database for the current
   :term:`shard cluster`.

   .. seealso:: :func:`sh.status()`

.. function:: db.removeUser(username)

   :param username: Specify a database username.

   Removes the specified username from the database.

.. function:: db.repairDatabase()

   Checks and repairs errors and inconsistencies with the data
   storage. This function is analogous to a ``fsck`` operation for
   file systems. Additionally, the function compacts the database to
   optimize the current database's storage utilization, as with the
   :dbcommand:`compact` command.

   This function has the same effect as using the runtier option
   ":option:`mongod --repair`," but only operates on the current
   database.

   This command provides a wrapper around the database command
   ":dbcommand:`repairDatabase`".

.. function:: db.resetError()

   Resets the error message returned by :func:`db.getPrevError` or
   :dbcommand:`getPrevError`. Provides a wrapper around the
   :dbcommand:`resetError` command.

.. function:: db.runCommand(command)

   :param JSON command: Specifies a :term:`database command` in the
                        form of a JSON document.

   :param string command: When specifying a :doc:`command
                          </reference/commands>` as a string,
                          :func:`db.runCommand()` transforms the
                          command into the form "``{ command: 1 }``".

   Provides a helper to run specified :doc:`database commands
   </reference/commands>`. This is the preferred method to issue
   database commands, as it provides a consistent interface between
   the shell and drivers.

.. function:: db.serverStatus()

   Returns a :term:`JSON document` that provides an over view of the
   database process' state.

   This command provides a wrapper around the database command
   :dbcommand:`serverStatus`.

   .. seealso:: ":doc:`/reference/server-status`" for complete
      documentation of the output of this function.

.. function:: db.setProfilingLevel(level, [slowms])

   :param level: Specify a profiling level, see list of possible
                 values below.

   :param slowms: Optionally modify the threshold for the profile to
                  consider a query or operation "slow."

   Modifies the current :term:`database profiler` level. This allows
   administrators to capture data regarding performance. The database
   profiling system can impact performance and can allow the server to
   write the contents of queries to the log, which might information
   security implications for your deployment.

   The following profiling levels are available:

   =========  ==================================
   **Level**  **Setting**
   ---------  ----------------------------------
      0       Off. No profiling.
      1       On. Only includes slow operations.
      2       On. Includes all operations.
   =========  ==================================

   Also configure the :setting:`slowms` option to set the threshold
   for the profiler to consider a query "slow." Specify this value in
   milliseconds to override the default.

   This command provides a wrapper around the :term:`database command`
   :dbcommand:`profile`.

   :program:`mongod` writes the output of the database profiler to the
   ``system.profile`` collection.

   :program:`mongod` records a record of queries that take longer than
   the :setting:`slowms` to the log even when the database profiler is
   not active.

.. function:: db.shutdownServer()

   Shuts down the current :program:`mongod` or :program:`mongos`
   process cleanly and safely.

   This operation fails when the current database *is not* the
   admin database.

   This command provides a wrapper around the :dbcommand:`shutdown`.

.. function:: db.stats(scale)

   :param optional scale: Specifies the scale to deliver
                          results. Unless specified, this command
                          returns all data in bytes.

   :returns: A :term:`JSON document` that contains statistics
             reflecting the database system's state.

   This function provides a wrapper around the database command
   ":dbcommand:`dbStats`". The "``scale``" option allows you to
   configure how the :program:`mongo` shell scales the output
   values. For example, specify a "``scale``" value of "``1024``" to
   display kilobytes rather than bytes.

   See the ":doc:`/reference/database-statistics`" document for an
   overview of this output.

   .. note::

      The scale factor rounds values to whole numbers. This can
      produce unpredictable and unexpected results in some situations.

.. function:: db.version()

   :returns: The version of the :program:`mongod` instance.

.. function:: db.fsyncLock()

   Forces the database to flush all write operations to the disk and
   locks the database to prevent additional writes until the user
   releases the lock with the :func:`db.fsyncUnlock()` command.

   This command provides a simple wrapper around a
   :dbcommand:`fsync` database command with the following
   syntax:

   .. code-block:: javascript

        { fsync: 1, lock: true }

   This function locks the database and create a window for
   :doc:`backup operations </administration/backups>`.

.. function:: db.fsyncUnlock()

   Unlocks a database server to allow writes to reverse the operation
   of a :func:`db.fsyncLock()` operation. Typically used to allow
   writes following a database :doc:`backup operation
   </administration/backups>`.

Collection
~~~~~~~~~~

These methods operate on collection objects. Also consider the
":ref:`js-query-and-update-methods`" and
":ref:`js-query-cursor-methods`" documentation for additional methods
that you may use with collection objects.

.. note::

   Call these methods on a :term:`collection` object in the shell
   (i.e. "``db.collection.[method]()``", where "``collection``" is the
   name of the collection) to produce the documented behavior.

.. function:: dataSize()

   Returns the size of the collection. This method provides a wrapper
   around the :stats:`size` output of the :dbcommand:`collStats`
   (i.e. :func:`stats()`) command.

.. function:: storageSize()

   Returns the amount of storage space, calculated using the number of
   extents, used by the collection. This method provides a wrapper
   around the :stats:`storageSize` output of the
   :dbcommand:`collStats` (i.e. :func:`stats()`) command.

.. function:: totalIndexSize()

   Returns the total size of all indexes for the collection. This
   method provides a wrapper around the :stats:`totalIndexSize` output
   of the :dbcommand:`collStats` (i.e. :func:`stats()`) command.

.. function:: distinct(field)

   :param field string: A field that exists in a document or documents
                        within the :term:`collection`.

   Returns an array that contains a list of the distinct values for
   the specified field.

   .. note::

      The :func:`distinct()` method provides a wrapper around the
      :dbcommand:`distinct`. Results larger than the maximum
      :ref:`BSON size <limit-maximum-bson-document-size>` (e.g. 16 MB)

.. function:: drop()

   Call the :func:`drop()` method on a collection to drop it from
   the database.

   :func:`drop()` takes no arguments and will produce an error if
   called with any arguments.

.. function:: dropIndex(name)

   :param index name: The name of the index to drop.

   Drops or removes the specified index. This method provides a
   wrapper around the :dbcommand:`dropIndexes`.

   Use :func:`getIndexes()` to get a list of the indexes on the
   current collection, and only call :func:`dropIndex()` as a
   method on a collection object.

.. function:: dropIndexes()

   Drops all indexes other than the required index on the "``_id``"
   field. Only call :func:`dropIndexes()` as a method on a
   collection object.

.. function:: ensureIndex(keys, options)

   :param JSON keys: A :term:`JSON Document` that contains key/value
                     pair or pairs with the name of the field or
                     fields to index and order of the index. A ``1``
                     specifies ascending and a ``-1`` specifies
                     descending.

   :param JSON options: An JSON document that controls the creation of
                        the database. This argument is optional.

   .. warning:: Index names, including their full namespace
      (i.e. "``database.collection``") can be no longer than 128
      characters. See the :func:`getIndexes` field
      ":data:`name`" for the names of existing indexes.

   Creates an index on the field specified, if that index does not
   already exist. If the ``keys`` document specifies more than one
   field, than :func:`ensureIndex` creates a :term:`compound
   index`. For example:

   .. code-block:: javascript

      db.ensureIndex({ [key]: 1})

   This command creates an index, in ascending order, on the field
   "``[key]``". To specify a compound index use the following form:

   .. code-block:: javascript

      db.ensureIndex({ [key]: 1, [key1]: -1 })

   This command creates a compound index on the "``key``" field
   (in ascending order) and "``key1``" field (in descending order.)

   .. note::

      Typically the order of an index is only important when doing
      :func:`sort()` operations on the indexed fields.

   The available options, possible values, and the default settings
   are as follows:

   ===========  =================  =======
   Option       Value              Default
   ===========  =================  =======
   background   true or false      false
   unique       true or false      false
   dropDups     true or false      false
   sparse       true or false      false
   v            index version.     1 [#]_
   ===========  =================  =======

   - Specify "``{ background: true }``" to build the index in the
     background so that building an index will *not* block other
     database activities.

   - Specify "``{ unique: true }``" to create a unique index so that
     the collection will not accept insertion of documents where the
     index key or keys matches an existing value in the index.

   - Specify "``{ dropDups: true }``" when creating a unique index, on
     a field that *may* have duplicate to index only the first occurrence of
     a key, and ignore subsequent occurrences of that key.

   - Specify "``{ sparse: true }``" only references documents with the
     specified field. These indexes use less space, but behave
     differently in some situations (particularly sorts.)

   - Only specify a different index version in unusual situations. The
     latest index version provides a smaller and faster index format.

   .. seealso:: ":doc:`/core/indexing`."

   .. [#] The default index version depends on the version of
      :program:`mongod` running when creating the index. Before version
      2.0, the this value was 0; versions 2.0 and later use version 1.

.. function:: reIndex()

   This method drops all indexes and recreates them. This operation
   may be expensive for collections that have a large amount of data
   and/or a large number of indexes.

   Call this method, which takes no arguments, on a collection
   object. For example:

   .. code-block:: javascript

      db.collection.reIndex()

   Change "``collection``" to the name of the collection that you want
   to rebuild the index.

.. function:: getDB()

   Returns the name of the current database as a string.

.. function:: getIndexes()

   Returns an array that holds a list of documents that identify and
   describe the existing indexes on the collection. You must call the
   :func:`getIndexes()` on a collection. For example:

   .. code-block:: javascript

      db.collection.getIndexes()

   Change "``collection``" to the name of the collection whose indexes
   you want to learn.

   The :func:`getIndexes()` items consist of the following fields:

   .. data:: getIndexes.v

      Holds the version of the index.

      The index version depends on the version of :program:`mongod`
      that created the index. Before version 2.0 of MongoDB, the this
      value was 0; versions 2.0 and later use version 1.

   .. data:: getIndexes.key

      Contains a document holding the keys held in the index, and the
      order of the index. Indexes may be either descending or
      ascending order. A value of negative one (e.g. "``-1``")
      indicates an index sorted in descending order while a positive
      value (e.g. "``1``") indicates an index sorted in an ascending
      order.

   .. data:: getIndexes.ns

      The namespace context for the index.

   .. data:: getIndexes.name

      A unique name for the index comprised of the field names and
      orders of all keys.

.. function:: remove(query,justOne)

   Call the :func:`remove()` method on a collection object, to
   remove documents from a collection. Use the following form:

   .. code-block:: javascript

      db.collection.remove()

   Where "``collection``" is the name of the collection that you want
   to remove. Without arguments, this method removes all documents in
   the collection. To control the output of :func:`remove()`:

   :param optional query: Specify a query object to limit or filter
                          the documents to remove. See
                          :func:`find()` and the :doc:`operator
                          reference </reference/operators>` for more
                          information

   :param optional justOne: Boolean. Specify "``true``" to only delete
                            the first result. Equivalent to the
                            operation of :func:`findOne()`.

   Consider the following example:

   .. code-block:: javascript

      db.records.remove({expired: 1, archived: 1}, false)

   This is functionally equivalent to:

   .. code-block:: javascript

      db.records.remove({expired: 1, archived: 1})

   These operations remove documents with "``expired``" *and*
   "``archived``" fields holding a value of "``1``" from the
   collection named "``records``".

.. function:: renameCollection()

   :param string name: Specifies the new name of the
                       collection. Enclose the string in quotes.

   Call the :func:`renameCollection()` method on a collection
   object, to rename a collection. Specify the new name of the
   collection as an argument. For example:

   .. code-block:: javascript

      db.rrecord.renameCollection("record")

   This method renames a collection named "``rrecord``" to
   "``record``". If the target name (i.e. "``record``") is the name of
   an existing collection, then the operation will fail.

   :func:`renameCollection()` provides a wrapper around the
   :term:`database command` ":dbcommand:`renameCollection`".

.. function:: validate()

   :param optional full: Boolean. Specify "``true``" to enable a full
                         validation. MongoDB disables full validation
                         by default because it is a potentially
                         resource intensive operation.

   Provides a wrapper around the :dbcommand:`validate` :term:`database
   command`. Call the :func:`renameCollection()` method on a
   collection object, to validate the collection itself. Specify the
   full option to return full statistics.

   The :dbcommand:`validation <validate>` operation scans all of the
   data structures for correctness and returns a single :term:`JSON
   Document` that describes the relationship between the logical
   collection and the physical representation of that data.

   The output can provide a more in depth view of how the collection
   uses storage. Be aware that this command is potentially resource
   intensive, and may impact the performance of your MongoDB
   instance.

   .. seealso:: ":doc:`/reference/collection-validation`"

.. function:: getShardVersion()

   This method returns information regarding the state of data in a
   sharded cluster that is useful when diagnosing underlying issues
   with a :term:`shard cluster`.

   For internal and diagnostic use only.

.. function:: getShardDistribution()

   .. depends on SERVER-4902

.. function:: stats(scale)

   :param optional scale: Specifies the scale to deliver
                          results. Unless specified, this command
                          returns all data in bytes.

   :returns: A :term:`JSON document` containing statistics that
             reflecting the state of the specified collection.

   This function provides a wrapper around the database command
   :dbcommand:`collStats`. The "``scale``" option allows you to
   configure how the :program:`mongo` shell scales the output
   values. For example, specify a "``scale``" value of "``1024``" to
   display kilobytes rather than bytes.

   Call the :func:`stats()` method on a collection object, to
   return statistics regarding that collection. For example, the
   following operation returns stats on the ``people`` collection:

   .. code-block:: javascript

      db.people.stats()

   .. seealso:: ":doc:`/reference/collection-statistics`" for an
      overview of the output of this command.

Sharding
~~~~~~~~

.. seealso:: ":doc:`/core/sharding`" for more information on the
   sharding technology and methods for creating :term:`shard clusters
   <shard cluster>`.

.. function:: sh.addShard(host)

   :param hostname host: Specify the hostname of a new shard server.

   Use this to add shard instances to the present :term:`shard
   cluster`. The ``host`` parameter can be in any of the following
   forms: ::

        [hostname]
        [hostname]:[port]
        [set]/[hosname]
        [set]/[hosname],[hostname]:port

   You can specify shards using the hostname, or a hostname and port
   combination if the shard is ruining on a non-standard port. A
   :term:`replica set` can also function as a shard member. In these
   cases supply ``addShard`` with the set name, followed by at least
   one existing member of the set as a seed in a comma separated list,
   as in the final two examples.

   This function provides a wrapper around the administrative command
   :dbcommand:`addShard`.

.. function:: sh.enableSharding(database)

   :param name database: Specify a database name to shard.

   Enables sharding on the specified database. This does not
   automatically shard the database, but makes it possible to begin
   sharding collections using :func:`sh.shardCollection()`.

.. function:: sh.shardCollection(collection,key,unique)

   :param name collection: The name of the collection to shard.

   :param JSON key: A JSON document containing :term:`shard key` that
                    the sharding system uses to :term:`partition` and
                    distribute objects among the shards.

   :param boolean unique: Set true.

   Shards the named collection, according to the specified
   :term:`shard key`. Specify shard keys in the form of a :term:`JSON
   document`. Shard keys may refer to a single document field, or more
   typically several document fields to form a "compound shard key."

.. function:: sh.splitFind(collection, query)

   :param string collection: Specify the sharded collection containing
                             the chunk to migrate.

   :param JSON query: Specify a query to identify a document in a
                      specific chunk. Typically specify the
                      :term:`shard key` for a document as the query.

   Splits the chunk containing the document specified by the ``query``
   at its median point, creating two roughly equal chunks. Use
   :func:`sh.splitAt()` to split a collection in a specific point.

   In most circumstances, chunk splitting should be left to the
   automated processes. However, when initially deploying a
   :term:`shard cluster` it is necessary to perform some measure of
   :term:`pre-splitting` using manual methods including
   :func:`sh.splitFind()`.

.. function:: sh.splitAt(collection, query)

   :param string collection: Specify the sharded collection containing
                             the chunk to migrate.

   :param JSON query: Specify a query to identify a document in a
                      specific chunk. Typically specify the
                      :term:`shard key` for a document as the query.

   Splits the chunk containing the document specified by the ``query``
   as if that document is at the "middle" of the collection, even if
   the specified document is not the actual median of the
   collection. Use this command to manually split chunks unevenly. Use
   the ":func:`sh.splitFind()`" function to split a chunk at the
   actual median.

   In most circumstances, you should leave chunk splitting to the
   automated processes within MongoDB. However, when initially
   deploying a :term:`shard cluster` it is necessary to perform some
   measure of :term:`pre-splitting` using manual methods including
   :func:`sh.splitAt()`.

.. function:: sh.moveChunk(collection, query, destination)

   :param string collection: Specify the sharded collection containing
                             the chunk to migrate.

   :param JSON query: Specify a query to identify a document in a
                      specific chunk. Typically specify the
                      :term:`shard key` for a document as the query.

   :param string destination: Specify the name of the shard that you
                              wish to move the designated chunk to.

   Moves the chunk containing the document specified by the ``query``
   to the shard described by ``destination``.

   This function provides a wrapper around the
   :dbcommand:`moveChunk`. In most circumstances, allow the
   :term:`balancer` to automatically migrate :term:`chunks <chunk>`,
   and avoid calling :func:`sh.moveChunk()` directly.

   .. seealso:: ":dbcommand:`moveChunk`" and ":doc:`/sharding`."

.. function:: sh.setBalancerState(state)

   :param boolean state: ``true`` enables the balancer if disabled,
                         and ``false`` disables the balancer.

   Enables or disables the :term:`balancer`. Use
   :func:`sh.getBalancerState()` to determine if the balancer is
   currently enabled or disabled and :func:`sh.isBalancerRunning()`
   to check its current state.

.. function:: sh.getBalancerState()

   :returns: boolean.

   :func:`sh.getBalancerState()` returns ``true`` when the :term:`balancer` is
   enabled and false if the balancer is disabled. This does not
   reflect the current state of balancing operations: use
   :func:`sh.isBalancerRunning()` to check the balancer's current
   state.

.. function:: sh.isBalancerRunning()

   :returns: boolean.

   Returns true if the :term:`balancer` process is currently running
   and migrating chunks and false if the balancer process is not
   running. Use :func:`sh.getBalancerState()` to determine if the
   balancer is enabled or disabled.

.. function:: sh.status()

   :returns: a formatted report of the status of the :term:`shard
             cluster`, including data regarding the distribution of
             chunks.

.. function:: sh.help()

   :returns: a basic help text for all sharding related shell
             functions.

.. _replica-set-functions:

Replica Sets
~~~~~~~~~~~~

.. seealso:: ":doc:`/core/replication`" for more information regarding
   replication.

.. function:: rs.status()

   :returns: A :term:`JSON document` with status information.

   This output reflects the current status of the replica set, using
   data derived from the heartbeat packets sent by the other members
   of the replica set.

   This method provides a wrapper around the
   :dbcommand:`replSetGetStatus` :term:`database command`.

   .. seealso:: ":doc:`/reference/replica-status`" for documentation
                of this output.

.. function:: rs.initiate(configuration)

   :param JSON,optional configuration: A :term:`JSON document` that
                                       specifies the configuration of
                                       a replica set. If not
                                       specified, a default
                                       configuration is used.

   Initiates a replica set. Optionally takes a configuration argument
   in the form of a :term:`JSON document` that holds the configuration
   of a replica set. Consider the following model of the most basic
   configuration for a 3-member replica set: ::

          {
              _id : <setname>,
               members : [
                   {_id : 0, host : <host0>},
                   {_id : 1, host : <host1>},
                   {_id : 2, host : <host2>},
               ]
          }

   This function provides a wrapper around the
   ":dbcommand:`replSetInitiate`" :term:`database command`.

.. function:: rs.conf(configuration)

   :returns: a :term:`JSON document` that contains the current
             :term:`replica set` configuration object.

.. function:: rs.reconfig(configuration)

   :param JSON configuration: A :term:`JSON document` that specifies
                              the configuration of a replica set.

   Initializes a new :term:`replica set` configuration. This function
   will disconnect the shell briefly and forces a reconnection as the
   replica set renegotiates negotiates which node will be
   :term:`primary`. As a result, the shell will display an error even
   if this command succeeds.

   This function will overwrite the existing replica set
   configuration. Use :func:`rs.status()` to retrieve the current
   status, and consider the following procedure for modifying a

   This function provides a wrapper around the
   ":dbcommand:`replSetReconfig`" :term:`database command`.

.. function:: rs.add(host,configuration)

   Specify one of the following forms:

   :param string host: Specifies a host (and optionally port-number)
                       for a new host member for the replica set. This
                       host will be added with the default
                       configuration.

   :param JSON configuration: A :term:`JSON document` that specifies a
                              new replica set member, with a custom
                              configuration.

   Provides a simple method to add a member to an existing
   :term:`replica set`. You can specify new hosts in one of two ways:
   as a "hostname" with an optional port number to use the default
   configuration, or a as a JSON configuration object.

   This function will disconnect the shell briefly and forces a
   reconnection as the replica set renegotiates negotiates which node
   will be :term:`primary`. As a result, the shell will display an
   error even if this command succeeds.

   :func:`rs.add()` provides a wrapper around some of the functionality of
   the ":dbcommand:`replSetReconfig`" :term:`database command`.

.. function:: rs.addArb(hostname)

   :param string host: Specifies a host (and optionally port-number)
                       for a arbiter member for the replica set.

   Adds a new :term:`arbiter` to an existing replica set.

   This function will disconnect the shell briefly and forces a
   reconnection as the replica set renegotiates negotiates which node
   will be :term:`primary`. As a result, the shell will display an
   error even if this command succeeds.

.. function:: rs.stepDown(seconds)

   :param init seconds: Specify the duration of this operation. If not
                        specified the command uses the default value
                        of 60 seconds.

   :returns: disconnects shell.

   Forces the current replica set member to step down as
   :term:`primary` and then attempt to avoid election as primary for
   the designated number of seconds. Produces an error if the current
   node is not primary.

   This function will disconnect the shell briefly and forces a
   reconnection as the :term:`replica set` renegotiates which node
   will be :term:`primary`. As a result, the shell will display an
   error even if this command succeeds.

   :func:`rs.stepDown()` provides a wrapper around the
   :term:`database command` :dbcommand:`replSetStepDown`.

.. function:: rs.freeze(seconds)

   :param init seconds: Specify the duration of this operation.

   Forces the current node to become ineligible to become primary for
   the period specified.

   :func:`rs.freeze()` provides a wrapper around the :term:`database
   command` :dbcommand:`replSetFreeze`.

.. function:: rs.remove(hostname)

   :param hostname: Specify one of the existing hosts to remove from
                    the current replica set.

   Removes the node described by the "``hostname`` parameter from the
   current :term:`replica set`. This function will disconnect the
   shell briefly and forces a reconnection as the :term:`replica set`
   renegotiates negotiates which node will be :term:`primary`. As a
   result, the shell will display an error even if this command
   succeeds.

.. function:: rs.slaveOk()

   Provides a shorthand for the following operation:

   .. code-block:: javascript

      db.getMongo().setSlaveOK()

   This allows the current connection to allow read operations to run
   on :term:`secondary` nodes.

.. function:: db.isMaster()

   Returns a status document with fields that includes the
   "``ismaster`` field that reports if the current node is the
   :term:`primary` node, as well as a report of the current
   replication configuration.

   This function provides a wrapper around the :term:`database command`
   :dbcommand:`isMaster`

.. function:: rs.help()

   Returns a basic help text for all of the :doc:`replication
   </core/replication>` related shell functions.

Native Shell Functions
----------------------

These functions provide a number of low level and internal functions
that may be useful in the context of some advanced operations in the
shell. The JavaScript standard library is accessible in the
:program:`mongo` shell.

User Functions
--------------

.. function:: Date()

   :returns: Current date.

.. function:: load("file")

   :para string file: Specify a path and file name containing
                      JavaScript.

   This native function loads and runs a JavaScript file into the
   current shell environment. To run JavaScript with the mongo shell,
   you can either:

   - use the ":option:`--eval <mongo --eval>`" option when invoking
     the shell to evaluate a small amount of JavaScript code, or

   - specify a file name with ":ref:`mongo <mongo-shell-file>`".
     :program:`mongo` will execute the script and then exit. Add the
     :option:`--shell <mongo --shell>` option to return to the shell after
     running the command.

   Specify files loaded with the :func:`load()` function in relative terms
   to the current directory of the :program:`mongo` shell
   session. Check the current directory using the ":func:`pwd()`"
   function.

.. function:: quit()

   Exits the current shell session.

.. function:: getMemInfo()

   Returns a document with two fields that report the amount of memory
   used by the JavaScript shell process. The fields returned are
   :term:`resident <resident memory>` and :term:`virtual <virtual
   memory>`.

.. function:: _srand()

   For internal use.

.. function:: _rand()

   :returns: A random number between ``0`` and ``1``.

   This function provides functionality similar to the
   "``Math.rand()``" function from the standard library.

.. function:: _isWindows()

   :returns: boolean.

   Returns "true" if the server is running on a system that is
   Windows, or "false"  if the server is running on a Unix or Linux
   systems.

.. function:: ls()

   Returns a list of the files in the current directory.

   This function returns with output relative to the current shell
   session, and does not impact the server.

.. function:: pwd()

   Returns the current directory.

   This function returns with output relative to the current shell
   session, and does not impact the server.

.. function:: cd("path")

   :param string file: Specify a path on the local file system.

   Changes the current context to the specified path.

   This function returns with output relative to the current shell
   session, and does not impact the server.

   .. note:: This feature is not yet implemented.

.. function:: cat("filename")

   :param string filename: Specify a path and file name on the local file
                          system.

   Returns the contents of the specified file.

   This function returns with output relative to the current shell
   session, and does not impact the server.

.. function:: md5sumFile("filename")

   :param string filename: a file name.

   :returns: The :term:`md5` hash of the specified file.

   .. note:: The specified filename must refer to a file located on
             the system running the :program:`mongo` shell.

.. function:: mkdir("path")

   :param string path: A path on the local filesystem.

   Creates a directory at the specified path. This command will create
   the entire path specified, if the enclosing directory or
   directories do not already exit.

   Equivalent to :command:`mkdir -p` with BSD or GNU utilities.

.. function:: hostname()

   :returns: The hostname of the system running the :program:`mongo`
              shell process.

.. function:: getHostName()

   :returns: The hostname of the system running the :program:`mongo`
             shell process.

.. function:: removeFile("filename")

   :param string filename: Specify a filename or path to a local
                           file.

   :returns: boolean.

   Removes the specified file from the local file system.

.. function:: fuzzFile("filename")

   :param string filename: Specify a filename or path to a local
                           file.

   :returns: null

   For internal use.

.. function:: listFiles()

   Returns an array, containing one document per object in the
   directory. This function operates in the context of the
   :program:`mongo` process. The included fields are:

   .. describe:: name

      Returns a string which contains the name of the object.

   .. describe:: isDirectory

      Returns true or false if the object is a directory.

   .. describe:: size

      Returns the size of the object in bytes. This field is only
      present for files.

Internal Functions
~~~~~~~~~~~~~~~~~~

These functions are accessible in the shell but exist to support other
functionality in the environment. Do not call these functions
directly.

.. function:: _startMognoProgram()

   For internal use.

.. function:: runProgram()

   For internal use.

.. function:: run()

   For internal use.

.. function:: runMongoProgram()

   For internal use.

.. function:: stopMongod()

   For internal use.

.. function:: stopMongoProgram()

   For internal use.

.. function:: stopMongoProgramByPid()

   For internal use.

.. function:: rawMongoProgramOutput()

   For internal use.

.. function:: clearRawMongoProgramOutput()

   For internal use.

.. function:: waitProgram()

   For internal use.

.. function:: waitMongoProgramOnPort()

   For internal use.

.. function:: resetDbpath()

   For internal use.

.. function:: copyDbpath()

   For internal use.
