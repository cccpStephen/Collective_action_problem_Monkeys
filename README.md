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
1: procedure RUN_SIMULATION(num_peers)
2:    env ← Create simpy environment
3:    peers ← Empty list
4:    animation ← Initialize Animation object 
5:    Peer.file_size ← Random value between 50 and 200 # assume file size is 50-200 MB.
6:    Peer.piece_size ← Peer.file_size / TOTAL_PIECES #split the file into pieces
7:    animation.generate_peers() # run the animation method
8:    for i = 0 to num_peers - 1 do # num_peer is the input of RUN_SIMULATION, which refer to the number of peers at the beginning of the game.
9:        upload_speed ← Random value between 1 and 5
10:       download_speed ← Random value between 1 and 5 #initialize the peers abandwith
11:       peer ← Create Peer with id i, upload_speed, download_speed, animation_object
12:       add (peer) to a list of peer, peers
13:       if i = 0 then
14:           seeder/peer(0).file_pieces ← All file pieces/whole file # the seed has all file
15:       else
17:           other peer.file_pieces ← Random selection of file pieces
18:    end for
19:    for peer in peers do #connect all the peers
20:       for other_peer in peers do
21:           if peer ≠ other_peer then
22:               peer.add (other_peer) to its neighbor
23:           end if
24:       end for
25:    end for
26:    set env/environment end time as SIMULATION_TIME
27:    generate animation with name as animation.mp4, total 100 frame, 0.1 sec between each frame
28: end procedure


29: class PEER(env, id, upload_speed, download_speed, animation)
30:    self.file_pieces ← Empty set # File pieces self own
31:    self.peers ← Empty list #the neigbor it connect to
31:    self.current_requests ← 0 # Current number of requests from ur peer    
33:    
34:    
35:    method ADD_PEER(peer)
36:       self.peers add (peer) to its neighbors
37:    end method
38:
39:    method REQUEST_PIECE(piece, target)
40:       if this piece in target.file_pieces then # Check if the target peer still has the requested
41:          
43:               while downloaded < piece_size do #check whether the piece is  finished
44:                   available_bandwidth for each peers ← Calculate per request
45:                   downloaded += bandwidth_per_time_step 
46:                   yield env.timeout(0.1) # time step is 0.1 sec, which means the bandwith is dynamic for each 0.1 sec
47:                   animation.update_progress((target.id, self.id), progress_percentage)
48:               end while
49:               self.file_pieces.add(piece) #Add the completed file piece to the owned set
50:       end if
51:    end method


52:    method RUN()
53:       while len(self.file_pieces) < TOTAL_PIECES do
54:           missing_pieces ← Set of missing file pieces
55:           request_tasks ← Empty list
56:           for piece in missing_pieces do
57:               Shuffle self.peers
58:               for peer in shuffled peers do
59:                   if piece in peer.file_pieces then
60:                       animation.connect_peers(peer.id, self.id)
61:                       request_tasks.append(env.process(self.request_piece(piece, peer)))
62:                   end if
63:               end for
64:           end for
65:           yield env.all_of(request_tasks)
66:       end while
67:    end method

