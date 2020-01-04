#advancedProggraming milestone 1.

#summary
*****************
this proggram can read flight instructions
through a file named "fly.txt" and interpret it
to actually work inside "flightgear" simulator
*****************

#open data server and connect control client servers
*****************
in the program we use two servers that help us communicate with
the flightgear application
 

--open data server--
is a server that out program creats,
flightgear connects to our server as a client and sends us
crucial information about the simulation (such as current altitude of the airplane or the rpm value of the engine)
this server helps us to keep in touch with the simulation values.
--------------------

--connect control client server--
CCC server is built by the simulator itself
out program connects to it as a client
CCC can read commands and apply them to out airplane
the commands use certain format
|set objectSimulation value /r/n
exemple:
if we want to change the throttle value to 0.5 we can send
set /controls/engines/current-engine/throttle 0.5 /r/n
CCC helps us to actively control the simulation through our program
---------------------------------

these servers will work if we type in the flightgear program certain settings
--generic=socket,out,10,127.0.0.1,5400,tcp,generic_small (for the ODS)
--telnet=socket,in,10,127.0.0.1,5402,tcp (for the CCC)
ODS and CCC are our way to transform data from flightgear to our program and vice versa.
*****************

#how we actually transform data from our program to the servers
*****************
all the veriables that we create can be splitted to 2 groups 

group 1:(->)
these type of varibles are variables that we can control such as throttle or rudder
these varibles are stored in a map built that way:
variablesToServer<string varName, (here we store an object that stored the sim adress and the value of the variable)>
when we update a variable we first update the map, then we create a set command according to the known format
after we create the set command we push it to a queue in the CCC server the server will handle that command and apply every command we send to that queue

group 2:(<-)
these type of variables are variables that we update via the ODS server such as rpm or altitude 
every variable that is in this group will be stored in a map built that way
variablesFromServer<string varName, (here we store an object that stored the sim adress and the value of the variable)>
from now on every time the ODS server updates us it will update the variablesFromServer map accordingly
every variable we declared now will always have the relevent value from the simulator

note* every variable that is not (<-) or (->) will get inside variablesToServer map with sim value 0
we do that in order to get away with building another map.
*****************

#reading the fly.txt file
**********************
the 2 first lines will open the ODS and CCC servers
var declertaions will update our two maps 
every (<-) var will now alwyas be updated by ODS and the information can help us with condition checks and more
each time we update a (->)variable
we update the ToServer with the current change and send to CCC server the new command through a queue.
if and while conditions can be in the file 
the condition itself will be checked by the fromServer map
(for exemple if we want to check wheter altitude < 1000 we can do that easily because the current altitude is stored in the fromServer map)
Sleep and Print commands will be supported- our program knows to read it and apply it to itself
-done- command will finish our run disconnect our servers and finish the program.
**********************

note* this program uses calculator interpreter we did in another project it helps us checking conditions 
every math expression will be converted into value by this interpreter 
the interpreter also supports variables for exemple:
our program will be able to know how much is altitude-rpm 
it really helps with condition checking and varibles updating
