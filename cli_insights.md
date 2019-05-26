# Nerva CLI Insights

**INDEX:**

1. A Basic Walkthrough
2. Common Commands
3. Common Errors and How to Solve Them
4. Some Things to Consider


# 1 - A Basic Walkthrough

Mining through a command line interface (the black screen terminal with all the scrolling text) can be daunting for a new user. After a while you will find this way of operating comfortable, fast, flexible. You'll prefer a CLI to feel at home.
But until then, you will have many questions and see a lot of text scrolling by that you don't understand.
Therefore, here is a small guide that could answer some questions. Everything in this article was performed on Windows 8 in a CLI tool called ``Cmder`` which simply replaces the traditional Windows Command Prompt. If you are interested in improving your Windows Command Prompt experience, Cmder can be found at https://cmder.net/. The mini version is what you want.  

## Let's get started 

![picture 1 - Nerva CLI Startup](https://i.imgur.com/mXNhf5W.png)

We start our Nerva Daemon, in this case located in the folder ``H:\Nerva``, by executing ``nervad.exe``.
Right from the start you see a lot of text scrolling down while the daemon starts up all the necessary processes.
The first few lines initialize the CryptoNote core module, which is the base protocol of all CryptoNote coins.
The lines after that are all about booting up the servers required for accepting network requests and Remote Procedure Call (RPC) requests, which basically means the peer-to-peer stuff on the network or in simpler terms: this lets your computer talk to other computers in the Nerva network. 

The text you see is formatted as follows: <timestamp> <source id> <level> <category> <source line> <message>
* Every announcement has a `time stamp`: YYYY-MM-DD followed by the hour HH:MM:SS.sss (yes, miliseconds)
* The `source ID` is a number that identifies a thread during startup or shutdown, while SRV_MAIN, P2P1, PSP4 etc are basically log tags indicating a source for the message.
* INFO is a `log level indicator`. Other possibilities are WARNINGs and ERRORs. 
  Only errors require you to take action. But these are supposedly rare so don't worry about them.
* global is a `category` in the log file
* The `source line` points to a file and a specific line of code in it 
* The `message` explains what is happening in the startup processes

## Syncing the chain 

![picture 2 - Nerva CLI Synchronization](https://i.imgur.com/w0Qzprh.png)

With all the necessary processes booted up, the Nerva daemon is now ready to check how far along your copy of the blockchain is. If it finds that your copy of the database has not caught up to the current block, it will tell you how far behind you are. After that, it will start synchronizing your chain with those of the peers you connect to. This means that your daemon will send requests to other people's daemon to ask for info. If you do not wish to synchronize the blockchain database from scratch, you can use the ``quicksync`` function which allows you to download a partial copy of the blockchain and import this to your computer.

## The Joy of a Fully Synced Chain 

![picture 3 - Nerva CLI Start Stop Exit](https://i.imgur.com/KukpuzY.png)

Once your chain has finished its synchronization, it will let you know by showing SYNCHRONIZED OK.
This is the point where you can start mining. To mine you type:
``start_mining <address> #``  
Where you replace ``<address>`` with your Nerva address and ``#`` with the amount of threads you wish to use.
The daemon will let you know that mining has started succesfully. 


If you are unsure about something, you can type 'status' at any time and get a lot of information:

* The current block height of the network
* How far you are synced with the blockchain
* Your mining status (mining at # H/s OR not mining)
* The net hash: the hashrate of all miners combined
* The current version number and whether you are up to date
* Your outgoing and incoming connections:
    outgoing connections are P2P connections that were initiated by your daemon, asking other daemons for info
    incoming connections are P2P connections that were initiated by other daemons, asking your daemon for info
  A healthy network requires people that have both types of connections. 
  Ironically, in the screenshot you see 0 incoming connections which is due to a poorly configured VPN.
  !!!When mining, having no incoming connections may reduce your block finding chances because your info propagates more slowly. 
``To receive incoming connections, TCP Port 17565 has to be open on your system.``
* How long your daemon has been running uninterrupted a.k.a. your uptime

Never simply shut down your daemon. This may corrupt your copy of the blockchain and then you'll have to pop some blocks or resync the entire thing.
To stop mining type:
``stop_mining``
Then, to exit the program, simply type:
``exit``
You will then see several lines of info that shows you everything is shutting down neatly.

# 2 - Common Commands



# 3 - Common Errors and How to Solve Them

* Problem: Your blockchain doesn't fully sync. It stays behind a fixed number of blocks.
  * Solution: Check your system's clock. If you clock's time is off from the network time by more than the future time limit,the local daemon will reject the block.

* Problem: Your daemon complains about a hash not being in the database. 
  * Solution: The algorithm is trying to look up a block that you have not synchronized with yet; it is trying to go too fast. The way around this is to restart your nervad daemon as follows: ``nervad.exe --block-sync-size 1``

* Problem: In Windows, you use some of the Nerva tools with admin privileges and some others without admin privileges. This can lead to a whole bunch of problems.
  * Solution: Try to stay consistent in your use of admin privileges.




# 4 - Some Things to Consider

## Concerning Nethash

As mentioned earlier, when you type ``status`` in the daemon one of the things you see is the network's total hashrate.
It is a very common misconception that this nethash actually represents the network hashrate. It doesn't and it's not even close. The nethash is derived from the current difficulty, which is in turn derived from the block time. Add to that the way the Difficulty Adjusting Algorithm (DAA)responds to the block time, which it does at every block, and you have spiking nethash. 

```An example: blocks get found quickly, so it looks like the total nethash has increased, the DAA adjusts to compensate, blocks slow down and it looks like nethash has decreased. Up and down.```

The reason this is mentioned here is to make it abundantly clear that nethash is not an accurate representation of actual hash on the network. This problem is intensified because Nerva cannot be mined in pools, only solo.  A pool can better
reflect hashrate because it can collect info on the shares submitted to the pool. Solo miners don't have that option.

## Concerning Addresses

The standard transfer command goes as follows:
``transfer <address> <amount> -p <payment_id>``

A ``normal address`` for Nerva starts with ``NV``. If you send coins to an exchange or a merchant you have to add a payment ID to this kind of address. Due to the unlikability and untraceability, they will need this to know the payment comes from you. Alternatively, you can use an ``integrated address`` which starts with ``Niz``. Don't ask how the Z got there. This is likely a wrongly configured prefix; when the chain launched with it, it stayed forever. Integrated addresses don't require a separate payment ID because it is...integrated into it. A third kind of address is a ``Subaddress``. This is what you get if you create more than one address for a wallet. Subaddresses start with ``NS``. Subaddresses are the next generation of privacy enhancing address.

To summarize:
* ``normal address``: starts with ``NV``, can be combined with payment ID
* ``integrated address``: starts with ``Niz``, has an integrated payment ID
* ``subaddress``: starts with ``NS``

## Concerning Chain Reorganizations:

Nodes always broadcast what they think is the right block height but this is not necessarily the correct one.
* It used to happen a lot after hardforks that outdated nodes kept broadcasting their bad chain top block as the right one. This is no longer an issue (since the CN-A v3 algorithm) because outdated nodes now get blocked as soon as they send a bad block or announce their invalid version height.
* The other possibility is that two miners find a block simultaneously (a so called uncle block situation) and both get half the network behind 'their' chain. This creates a temporary fork and happens every so often (daily). It goes like this:

```Miner A and Miner B find a block almost simultaneously and start broadcasting their solution. The nodes that think block A is the right block mine as if it was and the nodes that think block B is the right one also mine as if Miner B's chain is the right one. Both chains diverge for a time until the chain is able to determine which one has the most nodes working on it (calculated from cumulative difficulty). That becomes the right chain and everyone on the other chain gets their node "reorganised" onto the right one```
 
## Concerning Hard Forks

Nerva is a project in full development. Every now and again the developers will make changes to the algorithm or other parts of the code. Changes like these often require a hard fork, just like you have with other big projects like Monero (XMR). Hard forks are network updates where the newer version of the client software is not compatible with the older versions. To avoid problems, Nerva uses version blocking: every new hard fork version will always block earlier versions.

* Question: Does a hard fork imply that you will get extra coins, as people have come to expect from all the crappy Bitcoin forks? Will you get Nerva Classic or Nerva ABC?
  * Answer: No, there is still only one main chain. Hard forks in Nerva do not create chain splits, there is just Nerva.

* Question: Will you lose your coins after a hard fork?
  * Answer: No, there is no need for concern. A hard fork simply upgrades a part of the network software. As soon as you upgrade you will be able to transact again with your account.

* Question: After a hard fork you see a lot of "node blocked" messages in your daemon. Is this a problem?
  * Answer: This is not a problem. It is just the version blocking in action: everyone must upgrade to the newest client version to be able to participate in the network.
  
* Question: Will the exchange support this hard fork?
  * Answer: Yes. The Nerva developers are in close communication with every exchange that lists Nerva. Scheduled forks are communicated well in advance to exchanges to make everything go smooth.





  

