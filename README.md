🎤 Lost at the Concert: How Friends (and Servers) Agree

Live demo: https://ZanyeNair.github.io/lost-at-the-concert/

An interactive simulator of the Raft consensus algorithm, the algorithm that keeps systems like Kubernetes (via etcd) consistent, told through a story everyone has lived: a group of friends at a packed concert, with bad signal, trying to agree on where to meet.

Built for my ECE 1100 Discovery Project at Georgia Tech (Fall 2026).

Screenshot of the simulator

The idea

Five friends are scattered in a crowd. One of them is the captain who decides the meetup spot. But phones die, texts fail, and crowd surges cut people off. How does the group make sure nobody ends up at the wrong spot?

Distributed computer systems face the exact same problem, and Raft is how they solve it.

At the concert	In Raft
Captain who calls the meetup spot	Leader
"Still here, plan is the same" check-in texts	Heartbeats (AppendEntries)
Friend loses patience and asks "Can I call the spot?"	Election timeout → RequestVote
Plan is locked in once most friends have it	Entry is committed by a majority
Phone dies	Node crash
Texts that don't go through	Packet loss
Crowd surge splits the group	Network partition
Cut-off captain's plans get thrown out after the crowd clears	Uncommitted entries are overwritten

A toggle in the top-right switches every label between the concert story and the real Raft terminology.

What you can do
Watch leader election live: an animated "patience ring" around each friend shows their election timer.
Break things: kill the captain's phone, split the crowd (network partition), isolate one friend, or make texts fail with a packet-loss slider.
Send plan updates and watch them replicate and lock in (commit) across the group.
Run experiments: the simulator runs hundreds of trials headlessly and graphs how long it takes to recover a leader as the network gets worse.
📱 Live with friends (real devices, real network)

The Live with friends tab turns every phone or laptop into one friend in the crowd. Each device runs its own copy of Raft and sends real Raft messages (vote requests, heartbeats, plan updates) to the others over the internet, so the delays are real, not simulated.

Join with a show code, so several groups can each run their own show.
Anyone can start the show, and bots can fill in empty spots.
Kill your own phone, kill the captain's, change how many texts fail, or split the crowd.
The page measures real round-trip times between devices and how long each regroup took, and can copy the results as CSV.

Live mode uses Claude's live rooms, so it works when the page is opened from claude.ai by signed-in people the page is shared with (Contributor access to take part). On GitHub Pages the simulator works, but Live mode can't connect.

Key finding

Leader failover time grows sharply as messages are lost (5 friends, 300 ms ± 120 ms text delay, 1.5–3 s election timeout):

Texts that fail	Median time to a new captain
0%	~2.6 s
20%	~3.4 s
40%	~9 s
60%	~20 s, and some groups never recover within 60 s

Takeaway: timeout settings that work on a good network break down on a bad one. That's why real systems (and robot swarms on flaky radio links) have to tune Raft for the network they actually run on.

How it works
A single self-contained index.html with no dependencies or build step.
The Raft engine (class Cluster) is separate from the drawing code. It implements leader election, log replication, the commit rule, and the "new leader appends a no-op" rule from the Raft paper (§5, §8).
A seeded random number generator makes experiment runs reproducible.
The engine was tested with randomized chaos runs (crashes, restarts, partitions, 15% packet loss). No term ever had two leaders, and no two nodes ever disagreed on a committed entry.
Run it locally

Download index.html and open it in any browser. That's it.

References
Diego Ongaro and John Ousterhout, In Search of an Understandable Consensus Algorithm (Raft), USENIX ATC 2014.
raft.github.io
