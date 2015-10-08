##Foundations of Software Engineering - Homework 3

Submitted by - Praveen Kumar Devaraj (prde1873)

* Using the material that we have covered in Lectures 8, 9, and 10, explain 1 why the broken program doesn't work. What concurrency-related problems are occurring in this program? If you see the program end in livelock, then describe what is happening with the threads. Why can't they make progress? If you see the program end in another way, such as getting to the point where it prints out the product ids but doesn't include all of them, explain why you think that happened. Note: If you start to add println statements to the Producers and Consumers, you may actually alter the behavior of the program! If you observe this, then also include a discussion on why that happens as well. In your answer, if you want to include snippets of code and/or output to explain what you are seeing, then do so. Use all of Markdown's capabilities to display what you need to explain the concurrency-related problems that you are observing.

  **Solution:**

In this program the consumer tries to de-queue the product which is inserted by the producer onto the queue. The output of this program is as below and was terminated manually.
Queue empty!

Producer 2 Produced: Product<2> on iteration 0

Producer 3 Produced: Product<0> on iteration 0

Producer 3 Produced: Product<9> on iteration 1

Producer 1 Produced: Product<4> on iteration 0

Producer 3 Produced: Product<10> on iteration 2

Producer 1 Produced: Product<11> on iteration 1

Producer 9 Produced: Product<5> on iteration 0

Producer 3 Produced: Product<12> on iteration 3

Producer 9 Produced: Product<14> on iteration 1

Producer 4 Produced: Product<0> on iteration 0

Producer 9 Produced: Product<16> on iteration 2

Producer 5 Produced: Product<6> on iteration 0

Producer 3 Produced: Product<15> on iteration 4

Producer 1 Produced: Product<13> on iteration 2

Producer 7 Produced: Product<7> on iteration 0

Producer 8 Produced: Product<8> on iteration 0

Producer 0 Produced: Product<0> on iteration 0

Producer 2 Produced: Product<9> on iteration 1

Producer 6 Produced: Product<3> on iteration 0

Too many items in the queue: 19!

Too many items in the queue: 19!

Consumer 0 Consumed: Product<2>

Consumer 0 Consumed: Product<0>

Consumer 0 Consumed: Product<9>

Consumer 0 Consumed: Product<4>

Consumer 0 Consumed: Product<10>

Consumer 0 Consumed: Product<11>

Consumer 0 Consumed: Product<5>

Consumer 0 Consumed: Product<12>

Consumer 0 Consumed: Product<14>

Consumer 0 Consumed: Product<0>

Consumer 0 Consumed: Product<16>

Consumer 0 Consumed: Product<6>

Consumer 0 Consumed: Product<15>

Consumer 0 Consumed: Product<13>

Consumer 0 Consumed: Product<7>

Consumer 0 Consumed: Product<8>

Consumer 0 Consumed: Product<0>

Consumer 0 Consumed: Product<9>

Consumer 0 Consumed: Product<3>

Queue empty!

Queue empty!

Queue empty!

Queue empty!

Queue empty!

Queue empty!

Queue empty!

Here in this case the consumer is trying to remove an item from an empty queue. Since the queue is empty Consumer X has the control over the queue waiting for the product to be inserted but the other Producer Threads are waiting for control to insert a product onto the queue. As a result a deadlock occurs where a process acquiring the control is waiting for the data to be inserted on the queue and other processes are waiting to acquire the control to insert data on to the queue. To resolve this issue one of the process is randomly chosen which takes the control and proceeds with the execution. But it ends up with a consumer process trying to retrieve from an empty queue.
I tried adding a few print statements to debug the behavior. In this case the livelock occurred little later compared to the previous case. This could be because the processor spends some time in executing the print instruction during which other threads could access the queue and update the products on the queue (either addition or removal).


*  Now switch your attention to the broken2 program. The only difference between the two programs are the synchronized keywords on the methods contained in ProductionLine.java. For this question, explain why this approach to fixing the program failed. Why is it that synchronizing these methods is not enough? What interactions between the threads are still occurring that cause the program to not be able to produce the correct output? Again, you may use snippets of code and/or output to illustrate your points. The only requirement for this question is that you focus exclusively on issues related to why this particular approach fails to solve the problem. In other words, your answer to this question should be different than your answer to the question above where you are discussing the program and its concurrency problems in general.

**Solution:**

The operation on the queue is synchronized meaning no two threads can operate on the products on the queue simultaneously, i.e. it causes a thread to obtain a lock when entering the method, so that only one thread can execute the method at the same time. Thus either the producer thread or the consumer thread can access the queue at any point in time. This synchronization helps us avoid deadlocks only when the lock on a thread is released. If the lock is not released and the thread is waiting for the resource indefinitely then there is a deadlock. Here the part of code adding or retrieving the data on the queue is not synchronized but the methods are synchronized, as a result if more threads are accessing the queue there is no guarantee of the order of execution. This results in lot of data being missed from the queue.  

The output snippet of the same is shown below.

Producer 5 Produced: Product<67> on iteration 7

Producer 0 Produced: Product<68> on iteration 7

Consumer 2 Consumed: Product<44>

Consumer 2 Consumed: Product<62>

Consumer 2 Consumed: Product<64>

Consumer 2 Consumed: Product<61>

Consumer 2 Consumed: Product<58>

Consumer 2 Consumed: Product<54>

Consumer 2 Consumed: Product<63>

Consumer 2 Consumed: Product<57>

Consumer 2 Consumed: Product<56>

In this case (above example) Consumer Thread 2 has acquired the lock and keeps retrieving the products from the queue. Once the iteration is completed the control is transferred, but till that point in time the same consumer keeps retrieving the products from the queue. In such a scenario one Consumer would have retrieved more products and shuts down only after retrieving one of the special products from the queue.
Consider the following output snippet below.

148

149

151

152

Too many items in the queue: 17!

Too many items in the queue: 17!

Too many items in the queue: 17!

Too many items in the queue: 17!

Too many items in the queue: 17!

Too many items in the queue: 17!

Too many items in the queue: 17!

Too many items in the queue: 17!

In this case there are producer threads trying to add products on the queue but no consumer thread is getting control to retrieve the product from the queue. This result in a deadlock even if the methods to access the queue are synchronized as this depends on the sequence of execution of the threads as well.


