# A Better XNA Skinned Sample

Originally from http://metlab.cse.msu.edu/betterskinned.html 

Now offline - pulled from Archive.org's wayback machine

Original website text:
--

Like most people, when I started out with XNA, I learned from the Skinned Model sample provided by Microsoft. In subsequent years. As I learned more, I was always puzzled by some of the choices they made in this sample, and I noticed as I read online groups how some of these choices have led to beliefs about XNA that are not at all true. I also noticed that no simple examples seem to exist for animation of content that isn't skinned. For these reasons, I had always wanted to produce a better skinned model and animation sample program that would help people understand how these things should be done. I also wanted to provide examples of other useful ideas.

You can find the XNA 4.0 Project here:  BetterSkinned.zip.  The project has been created for Windows, but should be portable to other platforms.

Some unique features of this sample relative to the Skinned Model sample are:

    Animation of the bones, not the skeleton
    Works for both rigid and skinned content
    Supports multiple animations from separate FBX files
    Tweening
    Support for both procedural and keyframe animation
    Linear keyframe reduction
    Useless keyframe reduction
    Removal of unused and extraneous bones
    Fix for the feature that removes constant keyframes

Bad Skinned Model Choices

I may make someone mad in Microsoft, but I was always surprised by some of the choices made in the Skinned Model sample. The single biggest mistake was basing animation on the skeleton rather than the bones. In XNA the position of meshes is governed by bones. These are a hierarchy of transformations using conventional forward kinematics. A skinned model also has a skeleton. A skeleton is a simply a subset of those bones that is used by the smooth skin algorithm. For some reason, they chose to only use the skeleton bones for their animation demos. Had they use all of the bones and simply extracted the bones for the skeleton when creating the skinning matrix, the code would have worked equally well for both skinned and non-skinned content. My solution use the bones correctly.

Another mistake was basing animation on matrices. Yes, the original animation is matrices, but real animation should be based on positions and quaternion orientations. Then it is easy to support tweening and blending. And, the decomposition should take place on the content processing side.
Additional Features

Aside from demonstrating the right way to treat animations, the program has examples of several other ideas that I think are useful.
Support for Rigid and Skinned Content

It is easy to make a program that detects rigid and skinned content and treats them differently. In the sample animation, the hat and crop are rigid models and the character is skinned. In one game project I was given a character with earrings that were rigid. That's when I realized the error of their ways in the original sample. Content with associated skinning data is converted to use the SkinnedEffect. The rest use BasicEffect. It will be easy to swap those for custom effects.
Supports multiple animations from separate FBX files

I have been using MotionBuilder to put multiple animations in a single FBX file for years. But, XNA 4.0 decided that was not a feature anyone needed and removed it. So, I had to deal with how to load multiple animations for a single character. Some of the ideas I read for dealing with this problem, like concatenating all clips into one big one, were just plain dumb. At first, I used the idea from Merging Animation Files. It was not unreasonable, but you end up with a tiny blank with dozens of items like this: "walk/Victoria-walk.fbx;jump/Victoria-jump.fbx;...". And, any change to any animation means it must rebuild all of these files.

The problem is completely solved if you just add a bone name to each animation channel. Then you can load each separately as content items then assign the bones at runtime. The trick is to create .FBX files with only the animation and little or no geometry. I sometimes call these "naked" files. For a skinned mesh, just delete the mesh before you export. For rigid items, I cull the mesh down to one triangle and be sure any materials with textures are removed.

Notice: be sure your mesh has a sibling other than the skeleton when you delete it. I commonly just create a dummy node in 3DS Max. Otherwise, the bones will move up a level and the transforms will be wrong.
 Tweening

The sample does proper tweening between keyframes. The tweening is linear. So, you can play animations very slowly if you like or use sparse keyframes.
Support for both procedural and keyframe animation

The class that represents bones in the example can be used for both keyframe animation and procedural animation. The rotation and translation values are applied in the local coordinate system (after any scaling, however, in this example).
Linear keyframe reduction

Keyframes that can be approximated accurately with linear interpolation (LERP or translation and SLERP for rotation) are deleted. This can reduce the number of keyframes drastically, sometimes by up to 90% if many bones don't have any movement. Note that this won't work if you don't do tweening, which, of course, I do.
Useless keyframe reduction

What you get from programs like 3DS Max will include lots of useless animation. You'll find keyframes for dummy nodes, footsteps, other controllers, etc. It's easy to just check that an animation is used and delete it otherwise.
Removal of unused and extraneous bones

The number of bones you can use for skinning is limited and 3DS Max tosses in a bunch that are just not needed. These "nub" bones are there to support inverse kinematics. The program detects them and removes them from the skeleton. The character in this demo would not fit, otherwise.
Fix for the feature that removes constant keyframes

XNA 4.0 removes all keyframes for a channel if they are the same as the bind pose transform. I'm sure someone thought this was a good idea. But, it means that if a bone has no animation on it, the keyframes are gone. But, when you are use skinning, the character has to be provided in the bind pose (t-pose) when loaded. So, you usually have a bind pose file for your model with no animation and then load the animation from other files. Suppose the bone LHand is moved in the animation from the bind pose version in an animation, usually by a keyframe as the first frame. The exported FBX file with that animation will also have the bind pose set to that first keyframe. If it does not move otherwise, XNA (or maybe the FBX exporter, though I don't think so) deletes the keyframe(s). This is evidenced by animation you play that does not set all of the bones right, so the character looks only partially animated.

My program fixes this problem by simple creating a keyframe 0 if one does not exist. A side benefit of this is that you can export a pose with no animation and it will create a 1 keyframe animation of the pose.
Summary

I hope others find this useful and I hope it will dispel some of the myths I am reading such as "you can't mixed skinned and non-skinned content in a model" or "you can only play one animation on a model". If there are questions, I will try to answer them as I have time. I can be reached at cbowen@cse.msu.edu.

Sorry about the awful animation. It's a modified version of a 3D Studio Max sample. I added the hat bump and whip to show that we can mix rigid and skinned animation.

All content and source code downloaded from this page are bound by the Microsoft Public License (Ms-PL).
