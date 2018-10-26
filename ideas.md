Mind you that this simulator is based on google's tpu while Eyeriss is from MIT/NVIDIA
#The processing element

##memory

Each PE can access/store data in:

*local register (scratchpad): 
*spatial flow (accessing data from a neighboring PE)
*Global memory

each has a corresponding latency

##operations
each PE is able to:

*load data from the its own register, neighbor PEs, global memory
*muliply data
*add data
*store data in its register, pass it to neighbor PEs, broadcast to global memory

##data
data can be either:

*input feature map (typically the largest)
*weights/filters (multiply element wise filter by input, add them up, save them as output, then moves in strides)
*output feature map (smaller than input, can get smaller with large stride or large filter)


#dataflow
Accelerator is composed of

*PEs arranged in a big array
*Global memory that is specified for output, input and filters(aka weights)

dataflow can be:
##Weight stationary: (reuse weights, load input)

*_weights_ are loaded once and stored in the _register_ different PEs
*_inputs_ are loaded from _global memory_ everytime
*_after parallely multiplying, _psums_ are _spacially_ shared to neighbors for addition
inputs are discarded and finall output is shared back to memory. New input is fetched from memory and local register keeps weight.
This is also typically the least efficient as per yet another eyeriss [paper](https://arxiv.org/pdf/1612.07625.pdf). But this one claims that OS is more efficient than WS 


##Output Stationary: (reuse input, load weitghts)

*_weights_ are loaded from _global memory_ everytime
*_inputs_ are loaded once, then after used, they are _spatially_ shared to other
*_after multiplying, _psums_ are accumulated and stored in _register_ 

