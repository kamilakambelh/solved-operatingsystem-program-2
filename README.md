Download Link: https://assignmentchef.com/product/solved-operatingsystem-program-2
<br>
## Program 2 – Adventure

This assignment asks you to write a simple game akin to old text adventure games like Adventure:

[http://en.wikipedia.org/wiki/Colossal_Cave_Adventure](http://en.wikipedia.org/wiki/Colossal_Cave_Adventure)

You’ll write two programs that will introduce you to programming in C on UNIX based systems, and will get you familiar with reading and writing files.

## Overview

This assignment is split up into two programs. The first program (hereafter called the “rooms program”) will be contained in a file named “&lt;STUDENT ONID USERNAME&gt;.buildrooms.c”, which when compiled with the same name (minus the extension) and run creates a series of files that hold descriptions of the in-game rooms and how the rooms are connected.

The second program (hereafter called the “game”) will be called “&lt;STUDENT ONID USERNAME&gt;.adventure.c” and when compiled with the same name (minus the extension) and run provides an interface for playing the game using the most recently generated rooms.

In the game, the player will begin in the “starting room” and will win the game automatically upon entering the “ending room”, which causes the game to exit, displaying the path taken by the player.

During the game, the player can also enter a command that returns the current time – this functionality utilizes mutexes and multithreading.

## Specifications

### Rooms Program

The first thing your rooms program must do is generate 7 different room files, one room per file, in a directory called “&lt;STUDENT ONID USERNAME&gt;.rooms.&lt;PROCESS ID&gt;”. You get to pick the names for those files, which should be hard-coded into your program. For example, the directory, if John Smith was writing the program, should be hard-coded (except for the process id number) as:

smithj.rooms.19903

Each room has a Room Name, at least 3 outgoing connections (and at most 6 outgoing connections, where the number of outgoing connections is random) from this room to other rooms, and a room type. The connections from one room to the others should be randomly assigned – i.e. which rooms connect to each other one is random – but note that if room A connects to room B, then room B must have a connection back to room A. Because of these specs, there will always be at least one path through. Note that a room cannot connect to itself.

Each file that stores a room must have exactly this form, where the … is additional room connections, as randomly generated:

“`ROOM NAME: &lt;room name&gt;CONNECTION 1: &lt;room name&gt;…ROOM TYPE: &lt;room type&gt;“`

Choose a list of ten different Room Names, hard coded into your rooms program, and have your rooms program randomly assign a room name to each room generated. For a given run of your program, 7 of the 10 room names will be used. Note that a room name cannot be used to in more than one room,

The possible room type entries are: START_ROOM, END_ROOM, and MID_ROOM. The assignment of which room gets which type should be random. Naturally, only one room should be assigned as the start room, and only one room should be assigned as the end room.

Here are the contents of files representing three sample rooms from a full set of room files. My list of room names includes the following, among others: XYZZY, PLUGH, PLOVER, twisty, Zork, Crowther, and Dungeon.

“`ROOM NAME: XYZZYCONNECTION 1: PLOVERCONNECTION 2: DungeonCONNECTION 3: twistyROOM TYPE: START_ROOM

ROOM NAME: twistyCONNECTION 1: PLOVERCONNECTION 2: XYZZYCONNECTION 3: DungeonCONNECTION 4: PLUGHROOM TYPE: MID_ROOM

… (Other rooms) …

ROOM NAME: DungeonCONNECTION 1: twistyCONNECTION 2: PLOVERCONNECTION 3: XYZZYCONNECTION 4: PLUGHCONNECTION 5: CrowtherCONNECTION 6: ZorkROOM TYPE: END_ROOM“`

The ordering of the connections from a room to the other rooms, in the file, does not matter. Note that the randomization you do here to define the layout is not all that important: just make sure the connections between rooms, the room names themselves and which room is which type, is somewhat different each time, however you want to do that. We’re not evaluating your randomization procedure, though it’s not acceptable to just randomize the room names but use the same room structure every time.

Here is an example of the rooms program being compiled and then run. Note that it returns NO OUTPUT, unless there is an error:

“`$ gcc -o smithj.buildrooms smithj.buildrooms.c$ smithj.buildrooms$“`

### The Game

Now let’s describe what should be presented to the player in the game. Upon being executed, after the rooms program has run and the rooms are generated, the game should present an interface to the player. Note that the room data must be read back into the program from the previously-generated room files, for use by the game. Since the rooms program may have been run multiple times before executing the game, your game should use the most recently created files: perform a [`stat()` function call](https://linux.die.net/man/2/stat) on rooms directories in the same directory as the game, and open the one with most recent `st_mtime` component of the returned `stat` struct.

This player interface should list where the player current is, and list the possible connections that can be followed. It should also then have a prompt. Here is the form that must be used:

“`CURRENT LOCATION: XYZZYPOSSIBLE CONNECTIONS: PLOVER, Dungeon, twisty.WHERE TO? &gt;“`

The cursor should be placed just after the &gt; sign. Note the punctuation used: colons on the first two lines, commas on the second line, and the period on the second line. All are required.

When the user types in the exact name of a connection to another room (Dungeon, for example), and then hits return, your program should write a new line, and then continue running as before. For example, if I typed twisty above, here is what the output should look like:

“`CURRENT LOCATION: XYZZYPOSSIBLE CONNECTIONS: PLOVER, Dungeon, twisty.WHERE TO? &gt;twisty

CURRENT LOCATION: twistyPOSSIBLE CONNECTIONS: PLOVER, XYZZY, Dungeon, PLUGH.WHERE TO? &gt;“`

If the user types anything but a valid room name from this location (case matters!), the game should return an error line that says “HUH? I DON’T UNDERSTAND THAT ROOM. TRY AGAIN.”, and repeat the current location and prompt, as follows:

“`CURRENT LOCATION: XYZZYPOSSIBLE CONNECTIONS: PLOVER, Dungeon, twisty.WHERE TO? &gt;Twisty

HUH? I DON’T UNDERSTAND THAT ROOM. TRY AGAIN.

CURRENT LOCATION: XYZZYPOSSIBLE CONNECTIONS: PLOVER, Dungeon, twisty.WHERE TO? &gt;“`

Trying to go to an incorrect location does not increment the path history or the step count. Once the user has reached the End Room, the game should indicate that it has been reached. It should also print out the path the user has taken to get there, the number of steps taken (not the number of rooms visited, which would be one higher because of the start room), a congratulatory message, and then exit.

Note the punctuation used in the complete example below: we’re looking for the same punctuation in your program.

When your program exits, set the exit status code to 0, and leave the rooms directory in place, so that it can be examined.

If you need to use temporary files (you probably won’t), place them in the directory you create, above. Do not leave any behind once your program is finished. We will not test for early termination of your program, so you don’t need to watch for those signals.

Do not use the -C99 standard or flag when compiling – this should be done using raw C.

### Time Keeping

Your game program must also be able to return the current time of day by utilizing a second thread and mutex(es). I recommend you complete all other portions of this assignment first, then add this mutex-based timekeeping component last of all. Use the classic pthread library for this simple multithreading, which will require you to use the “-lpthread” compile option switch with gcc (see below for compilation example).

When the player types in the command “time” at the prompt, and hits enter, a second thread must write the current time (in the format shown below) to a file called “currentTime.txt”, which should be located in the same directory as the game. The main thread will then read this time value from the file and print it out to the user, with the next prompt on the next line. I recommend you keep the second thread running during the execution of the main program, and merely wake it up as needed via this “time” command. In any event, at least one mutex must be used to control execution between these two threads.

To help you know if you’re using it right, remember these key points about mutexes:

* They’re only useful if …lock() is being called in multiple threads (i.e. having the only …lock() and …unlock() calls occur in one thread isn’t using the mutex for control).* Mutexes allow threads to jockey for contention by having multiple locks (attempt to be) established. So, perhaps the main thread locks a mutex, then spawns the second thread (whose first action is to attempt to gain control of the mutex by calling …lock(), which blocks the second thread). How is the second thread started up again? By the first thread calling …unlock().* A thread can be told to wait for another to complete with the …join() function, resuming execution when the other thread finally terminates.

Using the time command does not increment the path history or the step count. Do not delete the currentTime.txt file after your program completes – it will be examined by the grading TA.

Here is an example of the “time” command being run in-game:

“`CURRENT LOCATION: XYZZYPOSSIBLE CONNECTIONS: PLOVER, Dungeon, twisty.WHERE TO? &gt;time

1:03pm, Tuesday, September 13, 2016

WHERE TO? &gt;“`

### Complete Example

Here is a complete example of the game being compiled and run, including the building of the rooms. Note the time command, incorrect spelling of a room name, the winning messages and formatting, and the return to the prompt with some examination commands following:

“`$ gcc –o smithj.buildrooms smithj.buildrooms.c$ smithj.buildrooms$ gcc –o smithj.adventure smithj.adventure.c -lpthread$ smithj.adventureCURRENT LOCATION: XYZZYPOSSIBLE CONNECTIONS: PLOVER, Dungeon, twisty.WHERE TO? &gt;Twisty

HUH? I DON’T UNDERSTAND THAT ROOM. TRY AGAIN.

CURRENT LOCATION: XYZZYPOSSIBLE CONNECTIONS: PLOVER, Dungeon, twisty.WHERE TO? &gt;time

1:03pm, Tuesday, September 13, 2016

WHERE TO? &gt;twisty

CURRENT LOCATION: twistyPOSSIBLE CONNECTIONS: PLOVER, XYZZY, Dungeon, PLUGH.WHERE TO? &gt;Dungeon

YOU HAVE FOUND THE END ROOM. CONGRATULATIONS!YOU TOOK 2 STEPS. YOUR PATH TO VICTORY WAS:twistyDungeon$ echo $?0$ lscurrentTime.txt smithj.adventure smithj.adventure.c smithj.buildroomssmithj.buildrooms.c smithj.rooms.19903$ ls smithj.rooms.19903Crowther_room Dungeon_room PLUGH_room PLOVER_roomtwisty_room XYZZY_room Zork_room$ cat smithj.rooms.19903/XYZZY_roomROOM NAME: XYZZYCONNECTION 1: PLOVERCONNECTION 2: DungeonCONNECTION 3: twistyROOM TYPE: START_ROOM“`

## What to Hand In

What you’ll submit is a .zip file that contains both of your correctly named programs, which compile according to the instructions given above.

## Hints

You’ll need to figure out how to get C to read input from the keyboard, and pause until input is received. I recommend you use the getline() function. You’ll also get the chance to become proficient reading and writing files. You may use either the older open, close, lseek method of manipulating files, or the STDIO standard input library methods that use fopen, fclose, and fseek.

I HIGHY recommend that you develop this program directly on our class server (see our home page). Doing so will prevent you from having problems transferring the program back and forth, which can cause compatibility issues.

If you do see ^M characters all over your files, try this command:

`$ dos2unix bustedFile`

## Grading

You should be warned that if your program doesn’t compile or doesn’t generate room files on execution, you may receive a zero for the grade.

144 points are available for meeting all of the listed specifications, while the final 16 points will be based on your style, readability, and commenting. Comment well, often, and verbosely, approximately every four or five lines or so, as appropriate: we want to see that you are telling us WHY you are doing things, in addition to telling us WHAT you are doing.