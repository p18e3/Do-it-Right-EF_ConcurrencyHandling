# Do-it-Right / Entity Framework Concurrency Handling

## Scenario

Once an application allows multiple users to work on one dataset at the same time, concurrency conflicts may come to pass.
In this context, it is to mention that such a conflict can exclusively happen when executing an Update or Delete command on the database.

* **Update conflict:** Two or more users are about to perform changes on the same dataset, which can lead to that the changes in the first update will be lost.

* **Delete conflict:** One or more users try to update a dataset, which meanwhile has been deleted by another user.
