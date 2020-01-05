#advancedProggraming milestone 1.
made by: omer abarzel, or tovy

#summary
*****************
this proggram can read flight instructions
through a file named "fly.txt" and interpret it
to actually work inside "flightgear" simulator
*****************

#how it works
*****************
first, we call the object lexer that takes the fly.txt file and split it to an array of strings.
then, we call the parser to iterate over our lexer array and takes all (almost all)
the strings and convert them to Command type objects, and execute eact one of the objects:
*OpenDataServerCmd- opens a thread that creats a server(tcp socket).
	flightgear connects to our server as a client and sends us crucial information about the simulation (such 	  as current altitude of the airplane or the rpm value of the engine)
	this server helps us to keep in touch with the simulation values.
*ConnectControlClientCmd-opens a thread that creats client and connect to the simulator as a client
	CCC can read commands and apply them to out airplane the commands use certain format
	|set objectSimulation value /r/n
	exemple:
	if we want to change the throttle value to 0.5 we can send
	set /controls/engines/current-engine/throttle 0.5 /r/n
	CCC helps us to actively control the simulation through our program
*VarDeclarationCmd-all the veriables that we create can be splitted to 2 maps 
	updateVarToServer:(->)
	these type of varibles are variables that we can control such as throttle or rudder
	the map built that way:
	updateVarToServer<string varName, (here we store an object that stored the sim adress and the value of the 		variable)>
	updateVarFromServer:(<-)
	these type of variables are variables that we update via the ODS server such as rpm or altitude 
	the map built that way:
	variablesFromServer<string varName, (here we store an object that stored the sim adress and the value of 	 the variable)>
	note- every variable that is not (<-) or (->) will get inside variablesToServer map with sim value "0"
	we do that in order to get away with building another map.note* every variable that is not (<-) or (->) will get inside variablesToServer map with sim value 0
we do that in order to get away with building another map.
*UpdateVarCmd-when we update a variable we first update the map, then we create a set command according to the 		known format, after we create the set command we push it to a queue. in the CCC the client will use the
	queue and send the commands inside the queue to the simulator 
*ConditionVarCmd-if and while conditions can be in the file 
	the condition itself will usually be checked by the fromServer map 
	(for exemple if we want to check wheter altitude < 1000 we can do that easily because the current altitude 		is stored in the fromServer map)
*Sleep and Print commands will be supported- our program knows to read it and apply it to itself
-done- command will finish our run disconnect our servers and finish the program.


#note-
in order to open the server and client correctly we need to type in the flightgear program settings:
--generic=socket,out,10,127.0.0.1,5400,tcp,generic_small (for the ODS)
--telnet=socket,in,10,127.0.0.1,5402,tcp (for the CCC)
ODS and CCC are our way to transform data from flightgear to our program and vice versa.

#reading the fly.txt file
**********************
the 2 first lines will open the ODS and CCC servers
var declertaions will update our two maps 
every (<-) var will now alwyas be updated by ODS and the information can help us with condition checks and more
each time we update a (->)variable
we update the ToServer with the current change and send to CCC server the new command through a queue.
**********************

note* this program uses calculator interpreter we did in another project it helps us interpret expression to simple values. every math expression will be converted into value by this interpreter 
the interpreter also supports variables for exemple:
our program will be able to know how much is (altitude-rpm)


#how to run the project
*******************************
open terminal from the project folder and compile with this command:
g++ -std=c++14 *.cpp -Wall -Wextra -Wshadow -Wnon-virtual-dtor -pedantic -o a.out -pthread
after compile type ./a.out fly.txt
(the program is running and waits to the simulator connection)
then open the flightGear simulator or run fake_server.py

#gitHub link
*******************************
https://github.com/ortovy/AdvProg-milestone1
