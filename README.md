# Dynamic P2P File Transfer Simulation with Real-Time Visualization

Expectations for Code Behavior and Real-Time Visualization


1.System starts and initializes
          Random file size generated
          File divided into 10 pieces by default
          Each peer gets a random number of pieces from 0 to 9
          				a random upload speed 
           （Peer 0 is designated as the seeder and holds all 10 file pieces
          the seed can be removed by choice）
          request pieces when a new player is added to simpy environment
          
2.Dynamic Download Process
          System updates download progress every 0.1 seconds
          Real-time download speed depends on the congestion at each peer you are requesting from

3.animation function
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





pseudocode
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


