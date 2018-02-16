# Do-it-Right / Entity Framework Concurrency Handling

## Scenario

Once an application allows multiple users to work on one dataset at the same time, concurrency conflicts may come to pass.
In this context, it is to mention that such a conflict can exclusively happen when executing an Update or Delete command on the dataset, which will be explained in the following.

### (1) Update conflict
> Two or more users are about to perform changes on the same dataset, which can lead to that the changes in the first update will be lost.
> In Database Terminology, this is called Lost Update.

The phenomenon is illustrated in the graphic below, where we can se the memory-dimension on the y-axis, which is divided into the Database dimension and the In-Memory representation, which is titled RAM. The database entities, which are loaded in the application are thereby represented in the RAM, whereas all the committed data are represented in the database.
On the x-axis, the Time dimension is represented, which shows the time course of the concurrent transactions.

<p align="center">
  <img src="https://github.com/p18e3/Do-it-Right-EF_ConcurrencyHandling/blob/master/UpdateConflict.png" />
</p>

Beginning on the lower left-hand side, we can see that there's an User Entity persisted in the database, which is then being loaded by one user, and shortly thereafter by another user. This process is illustrated with the green arrows.
In the next step, the user which has first loaded the entity out of the database changes the Credit from 100$ to 80$ and commits the changes to the database. Thus, the new Credit of the User Entity with the Id 42 is 80$. The user, which has secondly loaded the database entity does not know about this and performs its changes on the old version of the database object. This means in effect, that the user performs a positive Credit booking with the value 10$ and changes the Credit from 100$ to 110$ in his loaded in-memory representation of the User with the Id 42. After the operation, the entity should be committed to the database, which leads to that the first update from 100$ to 80$ is lost, and the new Credit of the User with the Id 42 is now at 110$ instead of 90$, which would be correct.

### (2) Delete conflict
> One or more users try to update a dataset, which meanwhile has been deleted by another user.

A delete conflict happens by the same constellation as seen in the Update Conflict above. The difference is that a Delete Conflict happens by the fact that the changes on the loaded entity should be committed back to the database, where the entity does not exist anymore. In this context, the illustration below points out that the first user deletes the entity and applies the changes to the database. The second user (which has loaded the database entity a little bit later) modifies some properties and tries also to save the changes back into the database. In the course of this, the Database Management System executes a delete statement and determines that there are no rows to delete, because the target row has been deleted meanwhile.

<p align="center">
  <img src="https://github.com/p18e3/Do-it-Right-EF_ConcurrencyHandling/blob/master/DeleteConflict.png" />
</p>

It stands to reason, that concurrency conflicts cannot occur when executing an Insert Command or when querying the data.
When inserting a new dataset, there cannot arise concurrency conflicts because it is not possible for other users to access the respective dataset until it is committed to the database. On the other hand, querying on the data cannot lead to concurrency conflicts by the fact that query operations are read-only.

In general, when an Update or Delete conflict occurs, this is not noticed by the surrounding application and leads unnoticed by the user to data loss or corrupt data. Under this circumstance, it is important to provide and implement mechanisms that prevent the users from data loss or corruption.

## Approach

For this reason, we need a mechanism which enables us to perform the following steps:

1. Detect concurrency conflicts and inform the application / user about it
2. Provide the last consistent dataset and the dirty dataset(s) in order to be able to merge / synchronize them
3. Realize strategies to write the data back in a synchronized state

In order to implementing a such mechanism, Entity Framework helps us to achieve the expected behavior and prevents us from unsynchronized operations on data. Therefore, it defines a so-called `ConcurrencyToken`, which can be applied on a specified property of an Entity. When applying, the `ConcurrencyToken` uses the specified property to auto-increment its value on each update or delete command of an Entity instance. Thereby, it is possible to check for other users changes on a dataset, before writing it back.
