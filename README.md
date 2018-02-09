# Do-it-Right / Entity Framework Concurrency Handling

## Scenario

Once an application allows multiple users to work on one dataset at the same time, concurrency conflicts may come to pass.
In this context, it is to mention that such a conflict can exclusively happen when executing an Update or Delete command on the database.

* **Update conflict:** Two or more users are about to perform changes on the same dataset, which can lead to that the changes in the first update will be lost.

* **Delete conflict:** One or more users try to update a dataset, which meanwhile has been deleted by another user.

It stands to reason, that concurrency conflicts cannot occur when executing an Insert Command or when querying the data.
When inserting a new dataset, there cannot arise concurrency conflicts because it is not possible for other users to access the respective dataset until it is inserted into the database.
