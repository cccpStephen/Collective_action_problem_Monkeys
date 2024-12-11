# Dynamic P2P File Transfer Simulation with Real-Time Visualization

Expectations for Code Behavior and Real-Time Visualization


1. System starts and initializes
          Random file size generated
          File divided into 10 pieces by default
          Each peer gets a random number of pieces from 0 to 9
          				a random upload speed 
           （Peer 0 is designated as the seeder and holds all 10 file pieces
          the seed can be removed by choice）
          request pieces when a new player is added to simpy environment
          
2. Dynamic Download Process
          System updates download progress every 0.1 seconds
          Real-time download speed depends on the congestion at each peer you are requesting from

3. animation function
          Visualization of file transfer and congestion effects
          Each frame shows connections and transfer progress
          Updated every  0.1 seconds
          
4. plentity of Strategy implementation can be insert to the simulation

During peer initialization in run_simulation() , each peer is assigned a random strategy from the available strategies implemntation in strategy class.

Each peer uses the assigned strategy to allocate its upload bandwidth or respond the request from other peers. 
The strategies include:
1.equal_distribution_strategy: Evenly distributes bandwidth across all current requests.

2.tit_for_tat: Allocates bandwidth based on the cumulative proportion of the file pieces uploaded to that peer in the pass.

3.random_bandwidth: Randomly generates bandwidth allocation for each requesting peer at the begining of game, and the allocation won't be changed

# Execution
SET num_simulations TO 300
SET num_peers TO 10
CALL calculate_combined_averages(num_simulations, num_peers)
DISPLAY the combined averages in a clear format

# Simulation Configuration
SET total_pieces TO 10 # Total number of file pieces
SET simulation_time TO 100 # Maximum simulation duration
SET request_timeout TO 10 # Timeout threshold for a single request
  
pseudocode for code version 3.0
```python
# Request Strategies
DEFINE CLASS RequestStrategy:
  METHOD random_peer_selection(missing_pieces, peers, requesting_pieces):
    INITIALIZE request_tasks AS an empty list
    FOR piece IN missing_pieces:
     IF piece IS IN requesting_pieces:
       CONTINUE # Skip already requested pieces
     SHUFFLE the list of peers
     FOR target_peer IN shuffled peers:
      IF target_peer owns the piece:
        ADD request to request_tasks
        MARK piece AS requested in requesting_pieces
        BREAK the loop (only one peer per piece)
    RETURN request_tasks

METHOD avoid_excessive_requests(missing_pieces, peers, requesting_pieces):
 INITIALIZE request_tasks AS an empty list
 INITIALIZE peer_request_count AS a dictionary with peer ids as keys and 0 as default
 FOR piece IN missing_pieces:
    FOR peer IN peers:
       IF peer owns the piece:
            INCREMENT peer_request_count[peer.id] BY 1
    CALCULATE scarcity_level FOR each piece IN missing_pieces
    SORT missing_pieces BY scarcity_level (ascending)
    FOR piece IN sorted missing_pieces:
       IF piece IS IN requesting_pieces:
           CONTINUE
       FIND all peers who own the piece
       SORT those peers BY peer_request_count (fewer pieces = higher priority)
       SELECT the best peer
       ADD request to request_tasks
       MARK piece AS requested in requesting_pieces
    RETURN request_tasks

# Bandwidth Allocation Strategies
DEFINE CLASS Strategy:
  METHOD random_bandwidth_distribution(peer):
   - INITIALIZE total_bandwidth AS peer.upload_speed
   - INITIALIZE allocation_map AS an empty dictionary
   FOR target_peer IN peer.peers:
    - ALLOCATE random bandwidth (up to total_bandwidth / number_of_peers)
    - UPDATE allocation_map WITH target_peer and allocated bandwidth
    - DECREASE total_bandwidth BY allocated bandwidth
   RETURN allocation_map
 METHOD allocate_bandwidth(peer, target_peer_id):
  IF allocation_map IS empty:
   - CALL random_bandwidth_distribution(peer)
  RETURN allocation_map[target_peer_id]

# Peer Behavior
DEFINE CLASS Peer:
  METHOD __init__(env, id, upload_speed, download_speed, strategy, request_strategy):
     STORE peer details (id, speed, etc.)
     INITIALIZE upload and download tracking variables
     START peer simulation loop
 METHOD request_piece(piece, target_peer):
     CHECK IF target_peer owns the piece
     IF NOT:
          RETURN failure
     ELSE:
          START download
          WHILE download progress < piece_size:
             CALCULATE bandwidth using the peer’s strategy
             INCREASE download progress based on bandwidth
             CHECK timeout condition
             IF timed out:
                RETURN failure
             MARK piece AS downloaded
             UPDATE records for download completion
  
METHOD run():
WHILE total_pieces NOT downloaded:
  - FIND all missing pieces
  - CREATE request tasks for missing pieces
  - WAIT for all request tasks to finish
  - CHECK for unfinished pieces
  - RETRY requests for unfinished pieces IF any
RETURN completed download information

# Animation System
DEFINE CLASS Animation:
 METHOD generate_peers(num_peers):
    - ADD nodes FOR all peers
    - ARRANGE them in a circular layout
 METHOD connect_peers(from_peer, to_peer):
    - ADD an edge BETWEEN the two peers IF not already connected
 METHOD update_progress(edge, progress):
    - UPDATE the progress label ON the specified edge
 METHOD save_animation(filename, duration):
    - SAVE the animation AS a video file
    - RETURN confirmation

# Simulation Logic
DEFINE FUNCTION run_simulation(num_peers):
  INITIALIZE the simulation environment
  CREATE peers WITH random speeds and strategies
  CONNECT all peers to each other
  START the simulation and RUN until the time limit
  DISPLAY results including:
   - Total download times per peer
   - Average download times per strategy
  RETURN results

DEFINE FUNCTION calculate_combined_averages(num_simulations, num_peers):
  INITIALIZE result containers FOR each combination of strategies
  FOR simulation_index IN range(num_simulations):
          RUN a single simulation
          RECORD the results FOR each peer’s strategy combination
  CALCULATE averages FOR all strategy combinations
  RETURN combined averages

version 2.0
```python
SET TOTAL_PIECES to 10
SET SIMULATION_TIME to 100
DEFINE Strategy class:
   DEFINE equal_distribution_strategy(peer, target_peer_id):
       - Calculate number of requests
       - Divide upload speed by the number of requests (make sure to handle division by zero)
       - RETURN available bandwidth per request
   DEFINE tit_for_tat(peer, target_peer_id):
       - If peer hasn't downloaded anything yet, fall back to equal_distribution_strategy
       - Otherwise, check how much target peer has downloaded from this peer
       - Calculate bandwidth ratio based on upload history
       - RETURN the bandwidth ratio multiplied by the upload speed
   DEFINE random_bandwidth_distribution(peer):
       - Randomly distribute upload bandwidth across peers
       - Store the allocation in peer's bandwidth_allocation
   DEFINE allocate_bandwidth(peer, target_peer_id):
       - If no allocation exists, generate a random distribution
       - RETURN bandwidth allocation for the target peer

DEFINE Peer class:
   INIT peer with environment, id, upload/download speed, animation, and strategy
       - Initialize file pieces and a list of other peers
       - Create a resource to manage upload bandwidth
       - SET upload_history and total_downloaded to zero
       - START running the peer process
   DEFINE add_peer(peer):
       - ADD another peer to the list of known peers
   DEFINE request_piece(piece, target):
       - IF target has the piece:
           - Log the request
           - Request access to target's upload resource
           - Calculate the available bandwidth using the chosen strategy (random, tit-for-tat, or equal)
           - Download piece step-by-step, updating progress
           - Call the animation to update the visual progress between peers
           - Log completion and update peer's file pieces
           - Update upload history and total downloaded size
       - ELSE log that the piece is unavailable
   DEFINE run():
       - While peer doesn't have all pieces:
           - Identify missing pieces
           - Shuffle the list of peers and find one that has the piece
           - Request the missing piece
           - Wait for all requests to complete

DEFINE Animation class:
   INIT animation with number of peers:
       - Create a directed graph for the peers
       - Prepare for visual updates (progress bars)
   DEFINE generate_peers():
       - Add each peer to the graph
       - Position them in a circular layout
   DEFINE connect_peers(from_peer, to_peer):
       - If no edge exists between peers, create one and set progress to 0%
   DEFINE update_progress(edge, progress):
       - Update the visual progress for the edge (file transfer)
   DEFINE animate(i):
       - Draw the graph, update peer connections and progress
   DEFINE save_animation(filename, frames, interval):
       - Animate the simulation and save it as an MP4 file

DEFINE run_simulation(num_peers):
   Create environment and initialize peers with random upload/download speeds
   Assign strategies to peers randomly
   For peer 0, give all pieces (seeder), others get random pieces
   Connect all peers to each other
   Run the simulation until SIMULATION_TIME
   Save the animation

CALL run_simulation(5)




version 1.0

procedure RUN_SIMULATION(num_peers)
    env ← Create simpy environment
    peers ← Empty list
    animation ← Initialize Animation object 
    Peer.file_size ← Random value between 50 and 200 # assume file size is 50-200 MB.
    Peer.piece_size ← Peer.file_size / TOTAL_PIECES #split the file into pieces
    animation.generate_peers() # run the animation method
    for i = 0 to num_peers - 1 do # num_peer is the input of RUN_SIMULATION, which refer to the number of peers at the beginning of the game.
        upload_speed ← Random value between 1 and 5
       download_speed ← Random value between 1 and 5 #initialize the peers abandwith
       peer ← Create Peer with id i, upload_speed, download_speed, animation_object
       add (peer) to a list of peer, peers
       if i = 0 then
           seeder/peer(0).file_pieces ← All file pieces/whole file # the seed has all file
       else
           other peer.file_pieces ← Random selection of file pieces
    end for
    for peer in peers do #connect all the peers
       for other_peer in peers do
           if peer ≠ other_peer then
               peer.add (other_peer) to its neighbor
           end if
       end for
    end for
    set env/environment end time as SIMULATION_TIME
    generate animation with name as animation.mp4, total 100 frame, 0.1 sec between each frame
 end procedure

 class PEER(env, id, upload_speed, download_speed, animation)
    self.file_pieces ← Empty set # File pieces self own
    self.peers ← Empty list #the neigbor it connect to
    self.current_requests ← 0 # Current number of requests from ur peer    
    method ADD_PEER(peer)
       self.peers add (peer) to its neighbors
    end method

    method REQUEST_PIECE(piece, target)
       if this piece in target.file_pieces then # Check if the target peer still has the requested
          
               while downloaded < piece_size do #check whether the piece is  finished
                   available_bandwidth for each peers ← Calculate per request
                   downloaded += bandwidth_per_time_step 
                   yield env.timeout(0.1) # time step is 0.1 sec, which means the bandwith is dynamic for each 0.1 sec
                   animation.update_progress((target.id, self.id), progress_percentage)
               end while
               self.file_pieces.add(piece) #Add the completed file piece to the owned set
       end if
    end method


    method RUN()
       while len(self.file_pieces) < TOTAL_PIECES do
           missing_pieces ← Set of missing file pieces
           request_tasks ← Empty list
           for piece in missing_pieces do
               Shuffle self.peers
               for peer in shuffled peers do
                   if piece in peer.file_pieces then
                       animation.connect_peers(peer.id, self.id)
                       request_tasks.append(env.process(self.request_piece(piece, peer)))
                   end if
               end for
           end for
           yield env.all_of(request_tasks)
       end while
    end method
