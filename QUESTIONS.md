#Foundations of Software Engineering - Homework 3

Submitted by - Praveen Kumar Devaraj (prde1873) and Sharath Vontari (shvo9450)

1. Using the material that we have covered in Lectures 8, 9, and 10, explain 1 why the broken program doesn't work. What concurrency-related problems are occurring in this program? If you see the program end in livelock, then describe what is happening with the threads. Why can't they make progress? If you see the program end in another way, such as getting to the point where it prints out the product ids but doesn't include all of them, explain why you think that happened. Note: If you start to add println statements to the Producers and Consumers, you may actually alter the behavior of the program! If you observe this, then also include a discussion on why that happens as well. In your answer, if you want to include snippets of code and/or output to explain what you are seeing, then do so. Use all of Markdown's capabilities to display what you need to explain the concurrency-related problems that you are observing.

####Solution:	

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
