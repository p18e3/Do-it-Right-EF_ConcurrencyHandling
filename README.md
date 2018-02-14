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

### (2) Delete conflict
> One or more users try to update a dataset, which meanwhile has been deleted by another user.

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
