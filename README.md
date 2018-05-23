# Transaction Manager

In Zgt_tc.C, implement readtx(), writetx(), aborttx(), committx(), do_commit_abort(), perform_readwrite(), set_lock(). 

First get the transaction id, object number and count for that transaction. In the beginning of the transaction set the obno=0 and SGNO=1. 

The following are the states used in a transaction: 

•	TR_ACTIVE or processing (represented as “P”) 

•	TR_WAIT (represented as “W”) 

•	TR_ABORT (represented as “A”) and 

•	TR_END (represented as “E”) This is the state while commit is going on. 

readtx():

For read operation retrieve the existing transaction and check if the transaction is in processing state ‘P’. If it is in P state set the lock in Shared mode . After finishing the operation exit that thread that is currently running using pthread_exit(NULL). The writetx() method is same as  readtx() method. If the transaction is in “Processing“ state set the lock in "Exclusive" mode and carry the operation. In the perform_readwrite() based on the lockmode perform the read and write operation. If the lockmode is in Exclusive mode then perform write operation the object value here is incremented by 1 and in case of read operation in shared mode the object value is decremented by 1. The results of each operation is printed and the logfile is flushed after the operation is completed. 

writetx():

In the Zgt_tm.C the TxWrite operation is same as the TxRead method. 

aborttx() and committx() :

In aborttx function(zgt_tx.c) and the committx change the status of the transaction to 'A' and 'E' respectively  and then call the do_commit_abort() method. 

do_commit_abort():

In do_commit_abort() method, release all the locks held by the transaction by using free_locks() method and then remove the transaction using remove_tx() method. Release all the transactions waiting  on the commit/abort transactions. This is done by retrieving the list of transactions that are waiting on this transaction using zgt_nwait() method and then release the semaphores using zgt_v(0). 

setlock():

In setlock() method try to find if the requested object is already present in the hash table using the transaction id of the current transaction. This is done using the findt() method. If the method is successful then call perform_readwrite function to perform the read/write operation and find if the object is locked by some other transaction. For this use the find() method. If the object is not present in the hash table then add the transaction using the add() method and then call perform_readwrite(). If the object is present in the hash table and if its locked by some other transaction then then need to check the lockmode of the transaction .

perform_readwrite():

If both transaction has shared lock then call perform_readwrite() operation since there can be more than one transaction can perform reading operation in the critical section. If the transaction holding the lock on the requested object is an 'exclusive lock', then put our current transaction in wait status. The current transaction resumes operation after the transaction aborts/commits and releases the lock on the requested object and the semaphores are release form all the waiting transaction
