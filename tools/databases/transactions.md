# Transaction - unit of work performed within a database management system (or similar system) against a database,
and treated in a coherent and reliable way independent of other transactions.  
A database transaction, by definition, must be:  
* Atomic (it must either be complete in its entirety or have no effect whatsoever),  
* Consistent (it must conform to existing constraints in the database),  
* Isolated (it must not affect other transactions),  
* Durable (it must get written to persistent storage).  

# Transaction Scheduling  

## 2PL (two phase locking)
Lock all resources to all scope of transactions, then release them.  
By the 2PL protocol, locks are applied and removed in two phases:  
* Expanding phase: locks are acquired and no locks are released.  
* Shrinking phase: locks are released and no locks are acquired.  
  
Problem: deadlocks, slow  
Deadlock resolution: graph of deadlocks, kill locking  


## Multiversion Concurrency Control (MV2PL)
We take previous version of resource (no need to wait for the lock)


<img width="393" alt="image" src="https://user-images.githubusercontent.com/19955305/160246190-4194bab8-c74c-4c5e-b829-66c29dbb41fd.png">





