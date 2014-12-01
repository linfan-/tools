FROM: http://hoytech.com/vmtouch/

vmtouch is a tool for learning about and controlling the file system cache of unix and unix-like systems. It is BSD licensed so you can basically do whatever you want with it.

Compilation:

$ gcc -Wall -O3 -o vmtouch vmtouch.c
Installation:

$ su
Password:
# cp vmtouch /usr/local/bin/
Example 1

How much of the /bin/ directory is currently in cache?

$ vmtouch /bin/
           Files: 92
     Directories: 1
  Resident Pages: 348/1307  1M/5M  26.6%
         Elapsed: 0.003426 seconds
Example 2

How much of big-dataset.txt is currently in memory?

$ vmtouch -v big-dataset.txt
big-dataset.txt
[                                                            ] 0/42116

           Files: 1
     Directories: 0
  Resident Pages: 0/42116  0/164M  0%
         Elapsed: 0.005182 seconds
None of it. Now let's bring part of it into memory with tail:
$ tail -n 10000 big-dataset.txt > /dev/null
Now how much?
$ vmtouch -v big-dataset.txt
big-dataset.txt
[                                                    oOOOOOOO] 4950/42116

           Files: 1
     Directories: 0
  Resident Pages: 4950/42116  19M/164M  11.8%
         Elapsed: 0.006706 seconds
vmtouch tells us that 4950 pages at the end of the file are now resident in memory.
Example 3

Let's bring the rest of big-dataset.txt into memory (pressing enter a few times to illustrate the animated progress bar you will see on your terminal):

$ vmtouch -vt big-dataset.txt
big-dataset.txt
[OOo                                                 oOOOOOOO] 6887/42116
[OOOOOOOOo                                           oOOOOOOO] 10631/42116
[OOOOOOOOOOOOOOo                                     oOOOOOOO] 15351/42116
[OOOOOOOOOOOOOOOOOOOOOo                              oOOOOOOO] 19719/42116
[OOOOOOOOOOOOOOOOOOOOOOOOOOOo                        oOOOOOOO] 24183/42116
[OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOo                  oOOOOOOO] 28615/42116
[OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOo              oOOOOOOO] 31415/42116
[OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOo      oOOOOOOO] 36775/42116
[OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOo  oOOOOOOO] 39431/42116
[OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO] 42116/42116

           Files: 1
     Directories: 0
   Touched Pages: 42116 (164M)
         Elapsed: 12.107 seconds
Example 4

We have 3 big datasets, a.txt, b.txt, and c.txt but only 2 of them will fit in memory at once. If we have a.txt and b.txt in memory but would now like to work with b.txt and c.txt, we could just start loading up c.txt but then our system would evict pages from both a.txt (which we want) and b.txt (which we don't want).

So let's give the system a hint and evict a.txt from memory, making room for c.txt:

$ vmtouch -ve a.txt
Evicting a.txt

           Files: 1
     Directories: 0
   Evicted Pages: 42116 (164M)
         Elapsed: 0.076824 seconds
Example 5

Daemonise and lock all files in a directory into physical memory:

vmtouch -dl /var/www/htdocs/critical/
