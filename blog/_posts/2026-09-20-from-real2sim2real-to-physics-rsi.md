---
layout: post
title: "From Real2Sim2Real to Physics RSI"
date: 2026-09-20
category: Essay
summary: "Frontier models are making Real2Sim cheap. Once the simulator builder becomes agentic, the loop closes on reality — and calibration starts to look like the first rung of something deeper."
math: true
---

> Disclaimer: This article is not a propaganda for fully automated science. I believe in the human ability to drive innovation and unlock science in the future as long as we keep our reasoning skills.

Real2Sim2Real is the process of converting a real-world scene into a simulation so that we can create an environment where we generate data and train policies at scale, without having to deploy anything on a real robot. NVIDIA uses the term "digital twins" to describe the process of creating a simulated replica of a real environment, such as a warehouse, a factory, or any other physical space. This first step is what we call Real2Sim.

Real2Sim2Real goes one step further: we use this simulated environment to train a policy, and then deploy that policy back into the real world, with the goal of achieving zero-shot transfer [10] [11].

If we want to simplify it, imagine taking a picture of your living room and turning it into a simulated replica that captures the dynamics of the real world. You can then interact with this simulated environment to learn policies and collect data, and finally deploy those policies on your real robot so it can clean your living room.

Currently, the Real2Sim2Real loop is still a heavy engineering process. It requires a lot of work to perform Real2Sim, and then additional work to go back from Sim2Real. It also requires different skills to tune things like friction coefficients, actuator dynamics, collision models, object geometry, and other properties of the simulator [9].

That's why, over the last few years, researchers, including myself, have been working on what we call "world models": representations of the dynamics of our world learned directly from data, mostly observations and actions.

And observations are available in abundance. Just think about YouTube: there is a huge amount of human-generated video data that we can use to learn about the dynamics of the world, without having to manually build a simulated environment for every single use case.

But, now with new frontier models like Astra 6, it seems that creating simulated environment from real representation (Real2Sim) is becoming faster and cheaper, opening new capabilities and faster iterations.

In this blog post, I'll first give a quick introduction to world models and physics models, and then introduce a concept that I call Physics Recurrent Self-Improvement (RSI), along with its potential impact on the future of robotics research.

## World Models

### What's a World Model?

At its core, a world model is basically trying to predict how the world evolves: given the current state and usually an action or a prompt, it predicts what will happen next [1].

For robotics, though, this means more than just predicting the next image. A useful world model also needs to understand some of the physics of the world: things like forces, friction, contacts, or whether an object still exists when it is no longer visible. It also needs to understand geometry: where objects are, how they are oriented, their shape, the free space around them, and how they relate to each other.

If the model works in a latent space, this latent representation also needs to preserve the information we need for planning. In simple terms, a change in the latent state should correspond to a meaningful change in the real world. Ideally, we also want to separate the general dynamics of the environment from the specific way a robot interacts with it.

The big question is how much of this can be learned from observations alone. We have a huge amount of images, videos, and demonstrations that can teach models a lot about how the physical world behaves. But watching the world is not always enough to understand what will happen when a robot takes a specific action. So for robotics, we still need some way to ground the model in robot actions.

Before going further, I want to introduce my own taxonomy of world models to give you an overview of the different approaches to world modeling, from observation space to latent space.

## Taxonomy / Categories

Inspired by recent discussions around world models and embodied intelligence, including Fei-Fei Li's commentary, I would simplify the learning-based landscape into two broad categories [2]:

### 1. Observation-space World Models

These models predict directly in observation space, typically pixels, video, depth, or other sensor outputs. They generate plausible future observations conditioned on past observations, language, actions, or other controls.

Subcategories include:

- **Video Generation Models** predict future observations, for example $$p_\theta(o_{t+H} \mid o_{t-k}, c)$$, where $$c$$ may include text, actions, or camera motion. Some also learn an inverse dynamics model to infer actions from observed transitions.
- **World Action Models** jointly predict future actions and observations rather than treating actions only as conditioning inputs: $$p_\theta(a_{t+H}, o_{t+H} \mid a_{t-k}, o_{t-k}, \ell)$$. → DreamZero [7], Cosmos 3 [8].
- **Interactive World Simulators** generate controllable rollouts under user or agent interventions while maintaining consistency across time, viewpoints, actions, and object interactions.

### 2. Latent World Models

Latent world models perform prediction and rollout in a learned representation rather than directly in pixel space. Their representations are typically optimized for prediction, planning, or control rather than visual fidelity.

Subcategories include:

- **Reconstruction-based latent dynamics models** learn latent dynamics while retaining reconstruction or generative decoding objectives. → Dreamer [3]
- **Value-aware latent dynamics models** optimize the representation for control, rewards, values, and planning while discarding visually irrelevant information. → TD-MPC [4]
- **Feature-predictive latent world models** predict future states in a pretrained or self-supervised feature space, such as DINO or I-JEPA representations, rather than reconstructing RGB pixels. → V-JEPA 2-AC [5], DINO-WM [6]

## Physics Models

Now that we have an overview of the different categories of world models, lets discuss about physics models. I will use the term **physics model** here as shorthand for an executable physics simulation of an environment, particularly in the context of robotics and physical AI.

The distinction from world models is conceptually simple. A world model tries to **learn physical dynamics and relationships from data**, mostly from observations. On the other hand, a simulator tries to **encode those dynamics explicitly and generate trajectories from them**.

Using my own simple words, world models learn the dynamics from data, while in a simulator, the dynamics are defined manually by engineers: from creating the scene and defining contacts to specifying friction, collisions, and other physical properties. Neither approach is universally superior. They solve different parts of the problem.

Historically, however, high-quality simulation had a major disadvantage: creating realistic simulated environments required a lot of human engineering. Building a useful simulation can require expertise in physics engines, system identification, 3D reconstruction, geometry, scene design, collision modeling, textures, lighting, contact dynamics, and many other areas. That made simulation powerful but expensive.

Personally, I was never a huge fan of spending weeks building and refining a simulation environment, only to realize that the Sim2Real gap was still too large to make it useful for my research.

World models offered a very different proposition: maybe enough of the structure of the world could simply be learned from large amounts of multimodal data. That made them especially appealing to people who, like me, hate spending hours building simulation environments.

But this tradeoff may now be changing.

## Inverse Physics

Recent frontier models such as Astra 6 point toward a different workflow [13]. Starting from observations of the real world, a sufficiently capable model can help reconstruct an executable simulated environment [12] [13]. We call this process **inverse physics**:

> **Inverse physics is the process of reconstructing an executable physical simulation from observations of the real world.**

This is closely related to Real2Sim and digital-twin workflows that have existed for years [10] [11].

The important point is not that the concept itself is new. What is changing is how much of the construction and calibration of these environments can potentially be automated. Using tools like Blender together with frontier models, a system could reconstruct geometry, textures, lighting, collision meshes, and other properties of a physical scene, then progressively refine the simulation and integrate it into a simulator like MuJoCo [12] [13].

If that workflow becomes sufficiently reliable, the economics of simulation change substantially. Based on my first experiments, it already seems promising, and I have no doubt that the next generation of frontier models will continue to improve in this area.

Instead of asking teams of engineers to manually create every environment, an agentic system can help convert real-world environments into executable simulations.

Here is a simple example I tested with Astra 6, using the following YouTube video to create a MuJoCo environment:

<figure class="post-video">
  <video controls playsinline preload="metadata" poster="{{ '/images/blog/mujoco-bedroom-poster.jpg' | relative_url }}">
    <source src="{{ '/videos/mujoco-bedroom-real2sim.mp4' | relative_url }}" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption>A bedroom scene reconstructed into MuJoCo in a single shot from a YouTube video.</figcaption>
</figure>

As you can see, even if the simulation is not perfect yet, the environment is already pretty close to the real one shown in the YouTube video. And this was generated in a single shot with a very simple prompt.

Once the environment exists inside a physics engine, it becomes possible to generate large amounts of interaction data [11]. Policies can then be trained using reinforcement learning, planning, imitation learning, synthetic trajectories, sparse rewards, or more traditional engineered approaches [11]. For bounded environments like homes, offices, shops, factories, warehouses, etc., this could make large-scale simulation much more accessible. Nevertheless, this approach is less straightforward in highly open-ended environments such as autonomous driving, where the world is effectively unbounded and constantly changing.

Despite the limitations, the conceptual shift presented by this approach is still important:

> **Instead of learning physics entirely from observations, we can reconstruct the environment, simulate its physics, and generate experience at scale.**

And once the process of building that simulator becomes agentic, a much more interesting loop becomes possible.

## Real2Sim2Real

Real2Sim by itself is not enough. What we are really interested in is Real2Sim2Real: the ability to collect data and train policies in a simulated environment, then deploy them in the real world in zero shot, or with as little calibration as possible. That's the holy grail.

At the same time, we have to be realistic: true zero-shot deployment is unlikely to happen in most cases. There will almost always be discrepancies between the simulated environment and the real world, coming from sensor noise, imperfect dynamics, modeling approximations, and many other factors [9].

With agents, however, these errors are no longer just failures. They can become part of a closed loop, where the system uses them to improve both the simulation and the policy over time. This is what I call agentic Real2Sim2Real.

<figure class="post-figure">
  <img src="{{ '/images/blog/real2sim2real-loop.png' | relative_url }}" alt="Diagram of the agentic Real2Sim2Real loop: real world capture feeds a Real2Sim rebuild of the simulator, a policy is trained in sim, deployed to real, and the sim-vs-real gap is used to correct the simulator and repeat.">
  <figcaption>Coral = physical world · teal = digital · gray = measurement</figcaption>
</figure>

The process has five high-level stages:

1. **Inverse Physics → Real2Sim**<br>
   Observe the real environment and construct a corresponding simulation.
2. **Learn in Simulation → Sim**<br>
   Generate trajectories, train policies, evaluate behaviors, and optimize the agent inside the physics engine.
3. **Deploy → Sim2Real**<br>
   Execute the learned behavior in the real world and measure the difference between the simulated and real outcomes.
4. **Recalibrate**<br>
   Use the deployment failures and discrepancies to update the simulation environment.
5. **Repeat Steps 2 → 4**<br>
   Train again in the updated simulation, redeploy, measure the new gap, and continue improving.

At this point, the simulator is no longer static. It becomes part of a closed-loop improvement process. Every deployment produces new evidence about where the simulated world differs from the real one. That evidence can be used to improve the next simulation, which can produce a better policy, which generates new real-world observations, which can then improve the simulator again.

Reality becomes the ultimate loss function.

## From Real2Sim2Real to Physics RSI

At first, this feedback loop might simply perform system identification. The agent notices that an object slides farther in the real world than in simulation, so it adjusts the friction coefficient. It sees that a robot joint responds differently from the simulated actuator, so it updates the motor parameters. It notices that a collision behaves incorrectly, so it changes the contact model or the object geometry.

These are already powerful capabilities, but fundamentally, this is still calibration. Physics RSI, on the other hand, begins when the system becomes capable of changing something deeper: the simulator engine itself. The new loop might look like this:

<figure class="post-figure">
  <img src="{{ '/images/blog/physics-rsi-loop.png' | relative_url }}" alt="Diagram of the Physics RSI loop: a simulator prediction error leads the agent to propose a candidate simulator edit, test it on hardware, compare the fix against the baseline, update the simulator with what held, and repeat on the new residual.">
  <figcaption>Teal = simulator · coral = physical experiment · purple = agent reasoning</figcaption>
</figure>

At first, the proposed modification could be something simple:

- change a parameter;
- improve an object's geometry;
- update a collision mesh;
- modify a material property;
- adjust a contact model.

But as the system becomes more capable, it could start operating at higher levels of abstraction. Instead of asking:

> What value should this parameter have?

it might start asking:

> Is the simulator using the right model in the first place? Is the simulator missing some capabilities?

One way I find useful to think about Physics RSI is as a hierarchy of increasingly deeper and more ambitious changes to the simulator:

### Level 1: Parameter Estimation

The structure of the simulator is fixed. The agent simply learns better values for parameters such as friction, mass, damping, stiffness, or actuator response.

### Level 2: Model Selection

The agent chooses between existing models. For example, one contact formulation might predict reality better than another.

### Level 3: Physics Engine Modification

The agent discovers that the existing simulator systematically fails under certain conditions and proposes an additional term, correction, or mechanism.

### Level 4: New Effective Laws

The system identifies a compact mathematical relationship that explains a recurring class of observations better than the models currently available to it.

### Level 5: Fundamental Physical Discovery

At the extreme and for now highly speculative end of this hierarchy, we can ask whether the same loop could eventually contribute to discovering genuinely new physical principles.

This is not a claim that better simulators will automatically lead to new physics. Moving from parameter fitting to actual scientific discovery would require the system to propose new classes of models, design experiments that can distinguish between different hypotheses, reject alternative explanations, and discover representations that generalize beyond the observations used to build them.

I know this idea might be controversial, but I believe that unlocking this Physics RSI closed loop could teach us a lot about our world. I'm also well aware that some experiments require highly specialized equipment and infrastructure. You cannot simply simulate an experiment that requires accelerating particles at facilities like CERN and expect the simulation alone to replace reality. Those kinds of experiments are outside the scope of what I'm describing here. Still, who knows what a sufficiently complex and reality-aligned simulator could eventually teach us about the underlying mechanisms of our world?

The early versions of Physics RSI would almost certainly operate near the beginning of this hierarchy. Most improvements would probably come from better system identification, calibration, model selection, and more accurate approximations. The higher levels are much more speculative, especially Levels 4 and 5. Whether the same architecture could eventually progress from improving existing simulators to discovering genuinely new physical laws remains an open question.

For Physics RSI to work beyond simple calibration, it will need enough observability to understand why simulation and reality diverge, ways to distinguish between competing explanations, sufficient simulator fidelity, strong validation beyond previously seen data, and eventually the ability to move from simply correcting prediction errors to discovering models that actually explain the physical world.

But even if this broader vision does not become reality, I'm highly convinced that Levels 1 to 3 are already underway [12] [13] and will improve significantly over the next few months. I was one of those people who did not believe much in LLMs in the early years, even though I was already working in the field at the time. But the progress of these systems, especially with the rise of agentic systems, is proving me wrong. So from now on, I'm much more open to believing that almost anything is possible.

## References

[1] **Ha, D., & Schmidhuber, J.** "Recurrent World Models Facilitate Policy Evolution." *NeurIPS*, 2018. [World Models](https://worldmodels.github.io/)

[2] **Li, F.-F.** "A Functional Taxonomy of World Models." 2026.

[3] **Hafner, D., Lillicrap, T., Ba, J., & Norouzi, M.** "Dream to Control: Learning Behaviors by Latent Imagination." *ICLR*, 2020. [arXiv](https://arxiv.org/abs/1912.01603)

[4] **Hansen, N., Wang, X., & Su, H.** "Temporal Difference Learning for Model Predictive Control." *ICML*, 2022. [GitHub](https://github.com/nicklashansen/tdmpc)

[5] **Assran, M., et al.** "V-JEPA 2: Self-Supervised Video Models Enable Understanding, Prediction and Planning." 2025. [arXiv](https://arxiv.org/abs/2506.09985)

[6] **Zhou, G., Pan, H., LeCun, Y., & Pinto, L.** "DINO-WM: World Models on Pre-trained Visual Features Enable Zero-shot Planning." *ICML*, 2025. [arXiv](https://arxiv.org/abs/2411.04983)

[7] **Ye, S., et al.** "World Action Models are Zero-shot Policies." 2026. arXiv

[8] **NVIDIA.** "Cosmos 3: Omnimodal World Models for Physical AI." 2026. NVIDIA

[9] **Hofer, S., et al.** "Sim2Real in Robotics and Automation: Applications and Challenges." *IEEE T-ASE*, 2021.

[10] **Lim, V., et al.** "Real2Sim2Real: Self-Supervised Learning of Physical Single-Step Dynamic Actions for Planar Robot Casting." *ICRA*, 2022. [arXiv](https://arxiv.org/abs/2111.04814)

[11] **Escontrela Dieguez, A.** "Scaling Robot Learning with World Models and Real2Sim." UC Berkeley, 2026. EECS at UC Berkeley

[12] **Ding, K., You, L., & Zhao, H.** "GPT6_ASTRA is A Zero-Shot Engine for Real-to-Simulation Generation." 2026. GitHub

[13] **OpenAI.** "GPT-6 Astra: A new generation of intelligence." 2026.
