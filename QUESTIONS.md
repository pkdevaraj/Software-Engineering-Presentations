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


*  Now turn your attention to creating an implementation of the program that functions correctly in the fixed directory. In your answer to this question, you should discuss the approach you took to fix the problem and get your version of the program to generate output that is similar to the example_output.txt file that is included with the repo. While your output will not match theexample_output.txt file exactly, it should have a similar structure:
  *	The beginning of the output will show the producers starting up, followed by the consumers, and then their outputs interleaving.
  *	At some point, Producers will finish their work and announce that they are shutting down.
  *	Consumers will then start to shut down as well as they encounter the special Products that indicate that production is done.
  *	All of the producers will eventually shutdown followed by the last couple of consumers shutting down as well.
  *	The last part of the file is then a list of product ids from 0 to 199 inclusive.
  *	The monitor may generate output at any time, so it might have a message that appears in the final list of ids. That is fine, if that happens to you.
A fixed program will then cleanly shutdown and return to the command line prompt.

**Solution:**

For the fix in the program, In the Consumer, we make the thread to sleep for few seconds before retrieving the data from the queue. This is done to ensure that there is one or the other producer who have pushed their product on to the queue ad thereby avoiding accessing an empty queue. The whole block of code of retrieval and addition of the products on to the map are inside a synchronized block to make sure every thread gets to access the queue sequentially.
In the producer, we add a synchronized block for that part of code that pushes the data on to the queue. In this case the queue will eventually end up giving the control to one or the other consumer threads which would remove a product from the queue and make room for entering new products on the queue by the producers as they are synchronized.  	
In some cases the producer thread might have added the special product on to the queue and could have shut down after which another producer adds a normal product. In such cases the consumer thread whichever retrieves the special product shuts down. This need not be the same always. 
A sample output of the fixed program is given in output.txt file in the Fixed Directory.
