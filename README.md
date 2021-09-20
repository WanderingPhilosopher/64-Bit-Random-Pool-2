# 64-Bit-Random-Pool-2
Second version of 64 Bit Random Pool

Ok, as said, we will be redoing the way the 64-bit random pool is done.

We started out with 2^19 subranges and that was too many or just not enough participants, which the latter is/was expected.

For this round and beyond, the range will again be divided up, this time into 2^16 ranges, so each subrange will be 2^47 in size. Subranges will be assigned randomly. 
GPU threads will spread out inside each subrange in 2^47/2^15 smaller subranges and check 2^32 keys. Once all ranges have been checked (should be a lot quicker this time around, to get through each range), I will add 20000 (in hex) to the beginning range and subtract 20000 (in hex) from the end of the last range, and we will run the next round. 
So, this way, each round we will check 2^48 keys but spread out over the entire range versus checking every key in every range like TD’s pool. 

Here is example of one of my tests, to ensure no overlap. this is the beginning range:

8000000000000000:87ffffffffffffff

8000000000008000:8800000000006fff

8000000000010000:880000000000dfff

See how it's incremented?  (Note, this was a test on larger subranges so I could get through them quicker, the pool subranges will be much smaller, but this should give you an idea on the increments between rounds)

So we are still checking “random” keys in each subrange, but each round will be sequential in nature. 
Example:
If you get assigned range 8000000000000000:87ffffffffffffff
Instead of running and checking every key in the range, your GPU threads will spread out every 2^47/2^15 subranges.

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

How do you join and get started?

Download the 2 files from here: https://github.com/WanderingPhilosopher/64-Bit-Random-Pool-2/releases/tag/v1.0 or discord channel: https://discord.com/channels/871109371805962240/889292084190777394 . 

SHA256 Checksum for KHClient.exe = ```78cd069446995cced9582fc78827077197e291d7af10b7b6af0e49751bfb9bd3```

SHA256 Checksum for KH32.exe     = ```b155652aaf9cf50720ae8cff76a3b04495321f4face0b2927849c805231cad00```

(Verify by opening cmd, change directory to where files are located, and run CertUtil -hashfile KHClient.exe SHA256 and CertUtil -hashfile KH32.exe SHA256

Place them in the same folder. Create a batch file EXACTLY like this:

```KHClient.exe -d 0 -name username```

If you have multiple GPUs on a single PC/rig, use the -d flag to signify which GPU to use. -d 0 for GPU 0, -d 1 for GPU 1, etc.

Replace the -name username with your actual username, such as for me, -name wanderph ; I wanted to be able to use a BTC address but the database is case insensitive and at this time, that won't work But please only use one username whether you have 1 GPU or 1,000 GPUs. Keep the username the same. Example if using two GPUs: GPU 0 = Client.exe -d 0 -name wanderph GPU 1 = Client.exe -d 1 -name wanderph

If you have multiple GPUs, it is better to run each one on a separate/different instance versus running them together. If you have 2 GPUs, let them run the program seperately, it helps the pool check each range quicker and gives you credit for 2 ranges versus 1 range.

The username is how the program tracks how many ranges you have been assigned and completed. This is IMPORTANT because this is how we know who to pay and how much to pay them. Users will be paid based on their work. Your total ranges checked divided by total ranges checked equals your payout. Example: You checked 125,000 ranges and it took us 1,000,000 ranges checked to find the key then your amount would be 125,000 divided by 1,000,000 = .125 percent of ranges checked. Now multiply that by the amount of BTC we will find, .60 so your BTC payout would be .125 multiplied by .60 = .075 BTC.

Wait, what? .60 ????? I thought it was for .64 BTC???? I am glad you are paying attention! .04 will go to the user who finds the private key. Yes, the program allows me to see who actually found the key. So overall, the pool will split, based on amount of work each user does, .60 BTC and the private key finder will get .04, in addition to their equal share. So if user is owed .055 BTC based on their work, and they find the key, they will get .055 + .04 = .095 BTC. So make sure you run your GPUs as often as you can.

While we are searching for the key, you can message me your BTC address so I will have a record of it when we do find the key! Once we find the key, I will message everyone who participated and sent me their BTC address to verify the address is correct.

Stats look like this:

![image](https://user-images.githubusercontent.com/65258474/133953862-94be3b64-c98c-48db-bcb6-b2ecaef9ed62.png)

