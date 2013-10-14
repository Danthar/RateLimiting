Simple rate limiting component in c#
======================================================================

## Overview
This is not my code. This small component is written by Jack Leitch, which he published on his blog at:
http://www.pennedobjects.com/2010/10/better-rate-limiting-with-dot-net/

I simply put this here for my own ease of use.

The component is easy enough to use. So if you want examples: Go check out the blog.

##PLinq

If you are looking at rate limiting an IEnumerable which you are processing with PLinq. This component wont help you. This component is not suitable
for usage across threads.
So for example this wont work:

	using (var rateGate = new RateGate(5, TimeSpan.FromSeconds(1)))
	{
		Enumerable.Range(1, 1000)
			.LimitRate(rateGate)
			.AsParallel()
			.ForAll(x => PerformAction(x));
	}
	
This is due to the way the default algorithms that the TPL uses to chunk up an collection and process them in parallel. Based on environment 
variables the TPL makes a decision on how many threads are going to process your collection. In order to do that it cuts your collection
to pieces, creating chunks, which will then be processed in parallel. However it can also decide to process the entire collection on 1 thread.
In the first situation the RateGate wont work, in the former it does.