These Python scripts require Python 3.

Unpack all files from an archive file, or git clone from
   git@github.com:lowcostsuperhero/trailBid.git



INTRODUCTION
------------
This implements a trail bidding system for an interhash weekend where
the multiple trails are offered per time slot, and where typically no
single trail can accomodate all the hashers. The trailBid scripts provide
a possible solution for hashers to indicate their preference of trails by
bidding at a time of their convenience on trails of interest to them. The
trailBid scripts at a later time then batch processes the bids to award
trails to hashers based on their bid values.



QUICKSTART
----------
cd into the trailBid directory, then execute:
   make generate_pool
   make trailBid.py
Results will be dumped to the screen, as well as in html files in
iahLunar/html



DESCRIPTION
-----------
All the Python scripts are located in the same directory, which will also
need to have a subdirectory, called an event directory, for all the data
files necessary to define all the trails for a hashing weekend. A
demo/example event directory is provided as iahLunar. The required files in
the event directory are:
   timeSlots.txt The timeslots during which trails will be run
   trails.txt    The trails for the weekend
   calendar.txt  Defines which trails occur during which timeslot
   settings.txt  Global settings
   hashers.txt   Hashers registered for the weekend
   bids.txt      Bids submitted by hashers for trail(s) they want to run
In the iahLunar demo/example directory, hashers.txt and bids.txt are
missing. For an actual event, this information should be supplied by the
event mismanagement. For iahLunar, virtual hashers and virtual bids can be
generated for these two files by executing:
      python generate.py iahLunar pool
This is also accessible via make:
      make generate_pool

NOTE: All the Makefile targets pass iahLunar as the event directory.

By default, generate.py will create 2000 hashers, this number can be
changed by passing -n and the number of hashers to generate, eg:
   python generate.py -n1234 iahLunar
The distribution of bids by each hasher is done by a method internally
named "pool"; other distributions are "dribble", or "random". The bid
distribution can also be passed:
   python generate.py iahLunar random
Both the "-n" and distribution arguments are optional, and either, both,
or neither can be passed. Generating a random distribution of hasher bids
in the iahLunar event directory is also available as:
   make generate_random

To process the bids, run trailBid.py and pass the event directory name:
   python trailBid.py iahLunar
This is also accessible via make:
   make trailBid.py
Executing trailBid.py will result in a 00-orderOfHashers.txt file and an
html subdirectory to be created in the event directory. The
00-orderOfHashers.txt file contains a slightly randomized sort of all the
hashers with a slight advantage for registering early, based on an
assumption of a monotonically increasing rego/registration ID. The intent
of this file is to preserve the order of hashers on repeated executions
since the order incorporates an amount of randomness. The html subdirectory
contains all the html files reflecting the results of having processed the
bids for trails, and these files are suitable for publication to the event
website, and for hardcopy printouts as rosters for bus loading on the day
of the event.

At its core, bids for trails are processed in order of:
   - Higher bid value. If there are multiple bids from hashers with the
     same bid value, then tie-breaking the order of processing of bids
     will be done by ordering according to:
     - Advantage to hashers with fewer successful bids
     - Advantage to hashers who have submitted fewer bids
     - Hasher's slightly randomized ranking order.
       When a trail becomes oversubscribed, we do additional tie-breaking
       by fulfilling bids in the following order:
       - Favor bids for trails with fewer bids submitted (this delays the
         trail with more bids from filling up, hopefully allowing us to
         satisfy more hashers)
       - Trail ID as a last resort tie-breaker since the trail ID should
         be unique
