## Motivation and Operating Systems fundamentals

### Why do we need multiple threads?
1. Responsiveness
   - No feedback from an app after clicking button

Example:
- Suppose we have an online store app that services thousands of users
- App saves info of users purchases in a DB
- if One user makes a large purchase, which results in a long operation in the DB. At the same time, another user makes a purchase, the seocond user will not get any response until app is done responding to the first request. With multiple threading we can serve multiple users simultaneously by  serving each request in a difference thread.

![](assets/markdown-img-paste-20200305215127418.png)

Example: Movie Player app
- responsiveness particularly critical in apps with a UI
- app is showing images, playing audio, and if we click we should see a response.
![](assets/markdown-img-paste-20200305215413799.png)
- by multitasking quickly b/w different threads, it creates an illusion that all those tasks are happening at the same time
- **Note:** we do not even need multiple cores to achieve concurrency. we can create responsive apps by using multiple threads.

<br>

2. Performance

![](assets/markdown-img-paste-20200305215816708.png)

![](assets/markdown-img-paste-2020030521585710.png)

![](assets/markdown-img-paste-2020030522021984.png)
- A lot of the intuition we have from the traditional,single threaded sequential programming, will break when we introduce multiple threads.

<br>

### What are threads and where they live and OS fundamentals
- when we turn pc on, OS is loaded from the disk/HD into the memory

![](assets/markdown-img-paste-2020030522075741.png)
- os takes over allowing to interact with hardware and CPU

<br>

![](assets/markdown-img-paste-20200305220907649.png)
- apps reside in HD
- when user runs app the oS takes program from disk and creates an instance of the app in the memory

![](assets/markdown-img-paste-20200305221001330.png)
- that instance is called a **process/context of an app/context**
- each process is completely isolated from other processes that runs on the system


![](assets/markdown-img-paste-20200305221139764.png)

**Contained within process**
- process contains PID and other metadata
- the heap (contains data our app needs)
- files app opens
- code (program instructions) that will be executed
- Main thread
  - stack
  - instruction pointer

<br>

![](assets/markdown-img-paste-2020030522155146.png)
- for multi threaded apps each thread has its stack and instruction pointer
- all other components in process are shared


![](assets/markdown-img-paste-20200305221801246.png)
- it is easy to understand why each thread contains its own stack and pointer if we remember each thread is executing a different instruction in a different function, at any given moment

<br>

## Context Switch

![](assets/markdown-img-paste-20200305222509837.png)

![](assets/markdown-img-paste-20200305222616917.png)
