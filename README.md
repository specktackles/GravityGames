# Gravity Games!

This is a game focusing on a cool visual effect achieved with simulated physics and dynamic linear drag based on distance.

### Controls
`WASD` to move, `Spacebar` to jump, `G` to spawn cubes in front of you.

### Optimization

#### Profiler insights before optimization
<img width="1000" src="https://github.com/user-attachments/assets/e756056c-3302-42ce-93fe-dce82dc38e4c" />

As is clear from the frame graph, the game was was lagging after a lot of cubes were spawning, perhaps due to the added strain of physics, or the expensive distance check code running on `Event Tick`.

Regardless, as you'll see in the screenshots for `BP_Cubey` below, I decided to implement the **Dirty flag** design pattern, to check whether the linear drag adjustment code should even run in the first place. The dirty flag is triggered when the object maintains more than 50k units of distance from the player, at which point I decided it's safe to assume that cube is out of range for the player to conceivably ever touch again (i.e. in the sky).
#### Profiler insights after optimization
<img width="1000" src="https://github.com/user-attachments/assets/f4c7da72-870b-462f-8d20-716aa34fa00c" />

### Blueprints

#### Appended part of the EventGraph for `BP_FirstPersonCharacter`
<img width="1000" src="https://github.com/user-attachments/assets/c979ffe7-fcb7-4f6f-a36c-0b34f384c9d4" />

#### EventGraph for `BP_Cubey`
<img width="1000" src="https://github.com/user-attachments/assets/57da88b7-80ad-4a53-bc46-65d4e67b97a7" />

### Q/A

**Q:** What did you notice was impacting the performance of your project?<br>
**A:** I noticed that the performance was impacted in correlation with how many cubes were spawned. After further investigation, it was the linear drag adjustment logic, which continued executing every tick, even when the cubes were stood still.

**Q:** Which pattern(s) did you implement, and what behavior did they replace?<br>
**A:** I implemented the Dirty Flag pattern, which replaced (or more accurately, encapsulated) the unconditional `Event Tick` logic that was overwhelming the game loop.

**Q:** Do your patterns / technique implementations improve performance? Why or why not?<br>
**A:** Yes, the technique absolutely helps with performance, due to the fact that the cubes that are out of range are no longer being considered in the heavy physics calculations, especially the ones where they interact with each other.

**Q:** Can you demonstrate what the profiling process revealed?<br>
**A:** It revealed exactly what I felt during the gameplay: lag spikes or lag waves, which is to say mounting lag which was cleared and then built up again, over and over again. 

