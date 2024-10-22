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





pseudocode
```python
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



