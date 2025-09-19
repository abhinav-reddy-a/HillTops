# HillTops

## Basics 
This repository contains code for a platform to run the [HillTops problem](https://cs.nyu.edu/courses/fall25/CSCI-GA.2965-001/hilltops.html) game. It consists of a server and a sample client.

## Server &harr; Client Interaction
The server implements a custom **plain-text, line-oriented protocol over TCP** for the Hilltops game. Clients can be written in any language (C/C++/Java/Python) or invoked directly via tools such as nc/ncat, including interactive use from a terminal.

### Protocol
1. Client connects to server's port, on connect, Server sends Client &rarr; `CONNECTED Hilltops Server`
2. Client can either choose to play or ask the server to end the game and send results
3. If client wants to play, Client sends server &rarr; `PLAY <player_name>`
	- Server replies to client by sending &rarr;
		- A line of rows and columns &rarr; `<rows> <cols>`
		- Sends the matrix &rarr; A row in each line with space-separated ints
	- Server then sends a request to client to send swaps &rarr; `SEND_SWAPS`
	- Client then replies to the server by sending &rarr;
		- Number of swaps &rarr; `<number_of_swaps>`
		- Next N lines with each swap of format &rarr; `<row_1> <column_1> <row_2> <column_2>`
	- The server then checks if the resulting matrix is hilltop perfect, and then 
		- If hilltop perfect, sends &rarr; `OK <number_of_swaps> <max_worst_distance>`
		- If not hilltop perfect &rarr; `NOT_OK`
	- **Server is now open for another client to play**
4. If the client wants to end the game and check results, the Client sends the server → `RESULTS`
	- Server replies with a `RESULTS_BEGIN` token
	- The server then sends the results leaderboard with
		- Player names sorted lexicographically by `<number_of_swaps>` and `<max_worst_distance>` if they won
		- Followed by player names that couldn't form a hilltop perfect matrix 
	- This leaderboard consists of all the players who connected to the server and played the game in that session
	- Server then sends an `END` token
5. The server then closes the connection with the client by sending a `DISCONNECTED` token





## Run Instructions
### Server 
1. To compile the server files, run this command
	```
	make
	```
	This will generate an executable for the server &rarr; `hilltops_server`
2. To initiate a server, run this command 
	```
	./hilltops_server <port_number> <matrix_file_path>
	```
3. The initiated server accepts one client at a time; it runs on the `localhost` IP

### Client
#### Manual Client
1. Connect to the server by running this command 
```
nc localhost <port_number>
```
2. Refer to the protocol in the previous section for the next steps 

#### Automated Client
1. To run an automated client, use the `client.py` file provided with the code. 
2. All the communication aspects of the client have already been set up, you just need to fill the `your_algorithm` function &rarr;
	- Takes the initial matrix as an argument 
	- Expected to return an array of swaps
3. To run this client to play, run 
```
python client.py --port <port_number> --name <player_name>
```
4, To run this client to the end game and see results, run &rarr;
```
python client.py --port <port_number> --results
```

### Logs
All the logs pertaining to a server session and a player client connection are stored in the file of format &rarr;
```
session_<pid>_<conn_id>_<player_name>.txt
```
These log files include &rarr;
- Player name
- Initial matrix
- Matrix after each swap
- Final matrix
- Reachability matrix &rarr; Matrix displaying all the cells that drain into the minimum cell as "1" and all the cells that don't drain into the minimum cell as "0"
- Worst Distances matrix &rarr; Worst distances to the minimum cell for each cell
- Verdict

### Matrix file format
The initial matrix file has to follow this format &rarr;
- First line &rarr; `<rows> <columns>`
- Subsequent line &rarr; A row in each line with space-separated ints






