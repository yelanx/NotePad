> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.chipstrat.com](https://www.chipstrat.com/p/gpu-networking-basics-part-1)

> Communication during Training, Scale Out, Scale Up, Network Architectures, Switches, etc.

We’re going to _very gently_ discuss networking and GPUs. It’s an important topic, but it can feel boring or esoteric. Hang with me!

Training an LLM requires a lot of floating point operations (FLOPs):

How long to train these models?

If a single GPU can produce about 2 PetaFLOP/s (2 * 10^15 floating point operations per second) and there are 86,400 seconds in a day, that equates to roughly 1.7 x 10^20 FLOPS. In the most ideal scenario, using a single GPU, you would need to train for approximately 16 years to reach 10^24 FLOPs.

**16 years! Ain’t nobody got time for that!**

How can we train LLMs in months or weeks? We need _a lot_ of GPUs working simultaneously.

They’ll also need to talk to each other to share their progress and results as they work in concert. How does this communication happen? **Networking!**

[![](https://substackcdn.com/image/fetch/$s_!ifw1!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe57e5977-fcd0-4725-92fe-05729ef07e92_833x500.png)](https://substackcdn.com/image/fetch/$s_!ifw1!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe57e5977-fcd0-4725-92fe-05729ef07e92_833x500.png)

No, no, not that kind of networking.

[![](https://substackcdn.com/image/fetch/$s_!5uVv!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6a796af3-d2d8-4358-b609-ce7bcf74abd9_480x720.png)](https://substackcdn.com/image/fetch/$s_!5uVv!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6a796af3-d2d8-4358-b609-ce7bcf74abd9_480x720.png)

Yeah, that kind of networking! 😅

Connecting GPUs is actually a pretty interesting problem. Think about xAI needing to coordinate communication between 200K GPUs!

I mean, just listen to this entire 4 minute video with Elon. What an epic systems engineering problem to work on!

Let’s take xAI’s 200K GPU cluster as an example. How to connect them?

In an ideal world, every GPU could talk to every other GPU as fast as possible.

The first idea that comes to mind then: well, what if we directly connect every GPU?

No intermediate hops through a switch or another device would be needed, so it should be really fast!

**This is a “full mesh” network.**

But there are many practical reasons why a full mesh network doesn’t work at scale.

For example, to connect pairs of GPUs directly, each GPU would need 199,999 ports, and we would need ~20 billion total cables! Lol.

What if we introduce network switches? A network switch is a specialized piece of hardware that helps route data efficiently between multiple devices—in this case, GPUs.

Instead of connecting every GPU to every other GPU directly, GPUs are connected to a switch, and the switch manages communication between them.

[![](https://substackcdn.com/image/fetch/$s_!q6nj!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3504ccfd-7153-4ee0-8f53-0afe9115549f_786x396.png)](https://substackcdn.com/image/fetch/$s_!q6nj!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3504ccfd-7153-4ee0-8f53-0afe9115549f_786x396.png)_The network switch connects these GPUs, enabling any of them to talk to each other_

A single network switch for 200,000 GPUs would simplify cabling to one per GPU, so down from 20 billion cables to only 200K!

But the switch would still require 200,000 ports, which isn’t feasible.

[![](https://substackcdn.com/image/fetch/$s_!s4dL!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F03b53518-2a8f-453b-95c7-c88a7dce5831_779x230.png)](https://substackcdn.com/image/fetch/$s_!s4dL!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F03b53518-2a8f-453b-95c7-c88a7dce5831_779x230.png)It’d be like this switch, except 8000x bigger 😂

Obviously one massive switch isn’t going to cut it, which means we’ll need hierarchical switching.

Instead of using one massive switch to connect all GPUs, we could organize the network into _layers_ of switches:

[![](https://substackcdn.com/image/fetch/$s_!uF2P!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fd4faa600-35d5-4805-ad20-70df6cc64735_1600x590.png)](https://substackcdn.com/image/fetch/$s_!uF2P!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fd4faa600-35d5-4805-ad20-70df6cc64735_1600x590.png)

Each switch in this hierarchy could be smaller and connect only a subset of GPUs, making it more manageable in size and cost.

With this solution, GPUs don’t need to have thousands of direct connections anymore, and neither do the switches!

However, the trade-off is that data must pass through multiple switches when GPUs on different branches need to communicate, introducing additional latency.

To illustrate, consider two GPUs that are not connected to the same switch. Instead of a direct link, their communication must travel up to a higher-level switch and then down to the target GPU’s switch.

[![](https://substackcdn.com/image/fetch/$s_!nuHy!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F24eef740-991f-4e57-b9bd-3459891227d0_1600x662.png)](https://substackcdn.com/image/fetch/$s_!nuHy!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F24eef740-991f-4e57-b9bd-3459891227d0_1600x662.png)Network hops increase the time it takes for data to reach its destination.

A two-tier architecture like this is often called a **leaf-spine architecture** or a **two-tier Clos network**.

Leaf switches connect directly to the compute, and spine switches connect the leaf switches:

[![](https://substackcdn.com/image/fetch/$s_!gTbB!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9c82e7ae-9663-4a0e-a3fb-1438207d918d_1600x412.png)](https://substackcdn.com/image/fetch/$s_!gTbB!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9c82e7ae-9663-4a0e-a3fb-1438207d918d_1600x412.png)

How to get to thousands of GPUs?

**Scaling out**, or **horizontal scaling**, is how we expand the cluster by adding more GPUs and network switches. This helps distribute the training workload across more hardware, reducing the time needed to train an LLM.

[![](https://substackcdn.com/image/fetch/$s_!9r5e!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F265b0175-4ed9-4f93-a941-5b46e991da10_1600x402.png)](https://substackcdn.com/image/fetch/$s_!9r5e!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F265b0175-4ed9-4f93-a941-5b46e991da10_1600x402.png)Just copy and paste the network over and over … scale out!

How do these GPUs and switches communicate with each other? Scaling out uses **Ethernet** or **InfiniBand**, both of which provide the high-speed networking required for GPU-to-GPU communication.

**InfiniBand** is a proprietary Nvidia offering (acquired via the Mellanox acquisition) and was historically preferred in large-scale AI clusters due to its lower latency and higher bandwidth compared to high-performance Ethernet variants like RoCE ([RDMA over Converged Ethernet](https://techdocs.broadcom.com/us/en/storage-and-ethernet-connectivity/ethernet-nic-controllers/bcm957xxx/adapters/RDMA-over-Converged-Ethernet.html)).

[![](https://substackcdn.com/image/fetch/$s_!WajD!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F930058ba-98a5-49d0-9962-1abc4ca9e3c5_800x396.png)](https://substackcdn.com/image/fetch/$s_!WajD!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F930058ba-98a5-49d0-9962-1abc4ca9e3c5_800x396.png)If you said that’s not an Ethernet cable, you’re right! [Source.](https://www.fs.com/products/204997.html?now_cid=3908) _1.5m (5ft) NVIDIA/Mellanox MCP4Y10-N01A Compatible 800G OSFP Finned Top InfiniBand NDR Passive Direct Attach Copper Twinax Cable for Quantum-2 Switches_

Ethernet is increasingly preferred for new training clusters. As Jensen shared in this week’s Nvidia GTC keynote, Elon’s xAI built the biggest training cluster ([Colossus](https://x.ai/colossus)) with Nvidia’s Spectrum X Ethernet.

Scaling out works for a while, but eventually, physics and economics push back. More devices and switches mean additional latency from extra hops, higher power consumption, and rising costs. At some point, scaling out alone is no longer the best solution.

This leads us to another approach: **scaling up** or **vertical scaling.**

Scaling up means increasing the computational power per node rather than adding more nodes.

Instead of each leaf switch connecting directly to individual GPUs, it could connect to servers that each contain multiple GPUs—say, eight per server. This reduces the number of direct network switches and cables needed:

[![](https://substackcdn.com/image/fetch/$s_!VsZy!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F36ec7709-933b-4304-b57b-7fcd1962637d_1600x1007.png)](https://substackcdn.com/image/fetch/$s_!VsZy!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F36ec7709-933b-4304-b57b-7fcd1962637d_1600x1007.png)Each server node contains 8 GPUs; a single switch can address many more GPUs

As an analogy, in the early days of web scaling, fast-growing companies might first upgrade a server by adding more CPU cores and memory. That’s vertical scaling. When a single machine wasn’t enough, they would add more servers and a load balancer to distribute traffic. That’s horizontal scaling.

The astute observer might wonder how these scale up GPUs talk to each other. _Don’t they need to be connected via a network switch still? And if so, how is this different from scaling out?_

Very good observation!

Communication within a server node is called **intra-node** communication.

[![](https://substackcdn.com/image/fetch/$s_!ITIG!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F79d6dc18-85df-4d66-9c87-4ab246048464_2264x1290.png)](https://substackcdn.com/image/fetch/$s_!ITIG!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F79d6dc18-85df-4d66-9c87-4ab246048464_2264x1290.png)

Communication between GPUs on different servers is called **inter-node.**

[![](https://substackcdn.com/image/fetch/$s_!9dMC!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa5ea7fed-3ee1-47e0-b59c-bba369c86a78_1846x1750.png)](https://substackcdn.com/image/fetch/$s_!9dMC!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa5ea7fed-3ee1-47e0-b59c-bba369c86a78_1846x1750.png)

It turns out neighboring intra-node GPUs can communicate more quickly and at a higher bandwidth than using inter-node Infiniband or Ethernet technologies.

Why?

This is primarily due to the physical proximity of the GPUs and the specialized interconnect technologies employed. These technologies utilize direct, short, and optimized signal traces that are often integrated directly onto the same circuit board or within a shared package. This reduces signal travel distance and minimizes latency.

For example, here’s the routing for AMD’s Infinity Fabric as shared in the [2018 IEEE International Solid-State Circuits Conference - (ISSCC)](https://ieeexplore.ieee.org/xpl/conhome/8304413/proceeding) proceedings

[![](https://substackcdn.com/image/fetch/$s_!Ax4m!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Feff05bd8-b3c9-48c1-b00f-054f6e16a7b0_1336x1496.png)](https://substackcdn.com/image/fetch/$s_!Ax4m!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Feff05bd8-b3c9-48c1-b00f-054f6e16a7b0_1336x1496.png)The bright connecting lines (traces) represent metal connections between compute units. Think of it as “wires” in the package substrate.

Since GPUs within a server are directly connected, they can avoid much of the overhead associated with inter-node communication between GPU servers. On-package routing improves efficiency by keeping trace lengths short, reducing propagation delay, and minimizing signal degradation.

External connections like Infiniband and Ethernet often require additional signal integrity components—such as repeaters, retimers, and error correction mechanisms—to maintain reliable transmission over longer distances. These can introduce incremental latency and increase power consumption,

**I like to think of intra-node communication like NVLink and InfinityFabric as the [Autobahn](https://www.german-way.com/travel-and-tourism/driving-in-europe/driving/autobahn/):** designed to go fast without interruptions.

**Inter-node communication is like a two-lane highway:** it’s slower, you can’t fit as much traffic, and you might have to slow down to go around a tractor during spring planting or fall harvest (i.e. deal with congestion)

[![](https://substackcdn.com/image/fetch/$s_!SE5R!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F5a3b38ac-9513-4c4a-92c0-e8c8f26d5865_660x440.png)](https://substackcdn.com/image/fetch/$s_!SE5R!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F5a3b38ac-9513-4c4a-92c0-e8c8f26d5865_660x440.png)Watch out, I’m pretty sure there’s a cop up ahead!

It’s helpful to remember of how neural networks are trained to understand communication challenges.

During each training cycle, the network first performs a **forward pass**, where input data flows through layers of the network to produce a prediction. This prediction is then compared to the correct answer using a loss function, which quantifies how far off the prediction was.

The heart of learning happens during the **backward pass**, where an algorithm called backpropagation calculates how much each weight in the network contributed to the error. Using this information, gradient descent adjusts all weights in the direction that reduces the error—essentially turning billion of “knobs” to gradually improve the network's accuracy. With each iteration, these incremental adjustments push the neural network closer to making reliable predictions on new data.

Each GPU computes gradients for weight updates based on the error from the forward pass, but since the GPUs each work on different data subsets, these gradients are only partial. To ensure that all GPUs apply the same updates and remain in sync, **gradients must be aggregated and averaged across GPUs**.

This process, known as **all-reduce communication**, allows GPUs to exchange and distribute the final computed values before updating their local models. By maintaining global consistency, this prevents model drift and ensures effective distributed training.

The latency of this all-reduce communication directly impacts training efficiency.

There are other collective operations too, for example these supported by [Nvidia’s NCCL software library:](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/usage/collectives.html) AllReduce, Broadcast, Reduce, AllGather, ReduceScatter.

So training clusters ideally use the highest bandwidth and lowest latency communication possible.

And, [as we saw with DeepSeek V3](https://www.chipstrat.com/i/158842573/dualpipe-algorithm-and-computation-communication-overlap), there are software approaches to overlap communication and computation to reduce the amount of GPUs idling and reduce the impact of communication constraints.

That’s it for this part 1 article. I told you it would be gentle!

There’s a lot more to talk about. Actual massive clusters aren’t full mesh; it’s more complicated.

We will also eventually cover topics like inference communication needs and how they differ from training, front-end vs back-end networks, optical communication, and so on.

But I hope you got far enough to start to have a bit of confidence when you see a diagram and some docs, for example this Nvidia SuperPOD Compute Fabric [diagram](https://docs.nvidia.com/dgx-superpod/reference-architecture-scalable-infrastructure-b200/latest/network-fabrics.html), you can get a high-level understanding and ask questions to fill in the gaps:

[![](https://substackcdn.com/image/fetch/$s_!H_OC!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6841c1fa-0603-4714-b4f7-b674960270b3_1386x1600.png)](https://substackcdn.com/image/fetch/$s_!H_OC!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6841c1fa-0603-4714-b4f7-b674960270b3_1386x1600.png)

In the diagram above we see the spine and leaf switches helping with scale out, and the B200 servers scaling up.

From the table we can figure out that each Scalable Unit (SU) has 32 nodes with 8 GPUs per node. So this is scale out (32 nodes) and scale up (8 GPUs per node). Forget the details about “removing a DGX to accommodate for UFM connectivity”; the point is you can generally understand some of this now!

Good work!