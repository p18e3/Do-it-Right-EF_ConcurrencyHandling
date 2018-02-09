# Do-it-Right / Entity Framework Concurrency Handling

## Scenario

Once an application allows multiple users to work on one dataset at the same time, concurrency conflicts may come to pass.
In this context, it is to mention that such a conflict can exclusively happen when executing an Update or Delete command on the database.

* **Update conflict:** Two or more users are about to perform changes on the same dataset, which can lead to that the changes in the first update will be lost.

* **Delete conflict:** One or more users try to update a dataset, which meanwhile has been deleted by another user.

It stands to reason, that concurrency conflicts cannot occur when executing an Insert Command or when querying the data.
When inserting a new dataset, there cannot arise concurrency conflicts because it is not possible for other users to access the respective dataset until it is committed to the database. On the other hand, querying on the data cannot lead to concurrency conflicts by the fact that query operations are read-only.

In general, when an Update or Delete conflict occurs, this is not noticed by the surrounding application and leads unnoticed by the user to data loss or corrupt data. Under this circumstance, it is important to provide and implement mechanisms that prevent the users from data loss or corruption.

## Approach
