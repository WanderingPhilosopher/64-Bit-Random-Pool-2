# 64-Bit-Random-Pool-2
Second version of 64 Bit Random Pool

Ok, as said, we will be redoing the way the 64-bit random pool is done.
We started out with 2^19 subranges and that was too many or just not enough participants, which the latter is/was expected.
For this round and beyond, the range will again be divided up, this time into 2^16 ranges, so each subrange will be 2^47 in size. Subranges will be assigned randomly. 
GPU threads will spread out inside each subrange in 2^15 smaller subranges and check 2^32 keys. Once all ranges have been checked (should be a lot quicker this time around, to get through each range), I will add 8000 (in hex) to the beginning range and subtract 8000 (in hex) from the end of the last range, and we will run the next round. 
So, this way, each round we will check 2^48 keys but spread out over the entire range versus checking every key in every range like TD’s pool. 
Here is example of one of my tests, to ensure no overlap. this is the beginning range:
8000000000000000:87ffffffffffffff
8000000000008000:8800000000006fff
8000000000010000:880000000000dfff
See how it's incremented?  (Note, this was a test on larger subranges so I could get through them quicker, the pool subranges will be much smaller, but this should give you an idea on the increments between rounds)
So we are still checking “random” keys in each subrange, but each round will be sequential in nature. 
Example:
If you get assigned range 8000000000000000:87ffffffffffffff
Instead of running and checking every key in the range, your GPU threads will spread out every 2^15 subranges.
Thread 1 will start at key: 8000000000000000
And will sequentially check the first: 131,072 keys (8000000000000000 thru 8000000000020000)
Thread 2 will start at key: 80000FFFFFFFFFFF
And will sequentially check the first: 131,072 keys (80000fffffffffff thru 800010000001ffff)
The last thread will start at key: 87FFEFFFFFFF8001
And will sequentially check the first: 131,072 keys (87FFEFFFFFFF8001 thru 87fff00000018001)
After all ranges have been ran (if key hasn't been found), we will reset the starting point by adding 20000 to the original starting range of 8000000000000000, which our new starting range will now be 8000000000020000
By doing it this way, hopefully we catch the key in a smaller subrange and soon. If we do not, we will eventually because eventually we will have checked every key in the 64-bit range.
If you are connected and running a client, eventually you will see a message on your screen that says, [INFO UPDATE] Range Sent
[INFO UPDATE] Resetting Ranges
[MAIN] Resetting...
[INFO UPDATE] Resetting Ranges. 
That is by design so do not turn off your machine. That lets us know we are done with the current round, and we need to adjust for the next range. Once I adjust the ranges and restart the server, your GPU will pick back up where it left off and keep working through ranges. 
Excluding myself with multiple GPUs, we normally had 3-5 additional users connected and running in the pool. With 8-10 cards running, we should get through each round quickly, which was the original intent. I alone will have at least 4 cards running at a time, at all times.
We will be using a different client and cracking program this time around as well. The client has to be new/reconfigured because of the different cracking program. The program will work with all recent cards, GTX 10xx, GTX16xx, RTX 20xx and 30xx cards. The new client program also allows multiple cards on one rig to run without bottlenecking the CPU. With the old program, my rig started bottlenecking the CPU with 4-5 cards running.
