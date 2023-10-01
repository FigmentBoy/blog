---
title: On Video Game Modding and Game Updates
date: 2023-09-30 12:00:00 -0400
categories: [Programming, Game Modding]
tags: [geometry dash, modding, reverse engineering]
pin: true
math: true
mermaid: true
---
Video game modding is a lot of work. You may not realize it when you're downloading optimizations for Minecraft, multiplayer mods for Skyrim, or mod menus for Geometry Dash, but it takes a lot of expertise and knowledge to successfully mod a video game. As a somewhat prominent Geometry Dash modder, I want to take the opportunity to discuss how game updates affect modding in the wake of update 2.2, and what game developers and modders can do to limit the effects of such updates.

## The problem

First, I'd like to discuss what makes modding so difficult in the first place. Games like Geometry Dash are written in many programming languages, but the most common language for most modern games is C++. Game engines like Unreal and Cocos natively use C++ under the hood, and Unity games, originally written in C#, get transpiled to C++ before getting built. This leads to some problems for game modders, as C++ is a compiled language. Once the code is built, it's converted to machine code, and only readable through assembly, making it much harder to understand. A simple function like the one below turns into something much more complicated and harder to read without the proper expertise.

```c++
int add(int a, int b){
    return a + b;
}
```

```nasm
func01:
    push    rbp
    mov     rbp, rsp
    mov     dword ptr [rbp - 4], edi
    mov     dword ptr [rbp - 8], esi
    mov     eax, dword ptr [rbp - 4]
    add     eax, dword ptr [rbp - 8]
    pop     rbp
    ret
```

Astute readers may notice the lack of any variable information, function names, or other items that can help us in the modding process. Now imagine a whole game worth of these nameless, informationless functions! Plus, more complicated games like Geometry Dash may have multiple large structures within their C++ code that get completely obscured in the compilation process. Game modders essentially become cybersecurity experts, using industry-standard reverse engineering tools and picking apart the game, one function and structure at a time.

The worst part of this process is what happens when a game updates. Once a new version of the game is recompiled, all of the existing compiled functions can change in size and location, structures can be almost completely changed, and new functions are almost certainly inserted throughout the compiled assembly. This means that most mods will have to take days, if not weeks to update, as most of the work done to find functions and define structures in an older version of the game largely won't apply to the new version.

## What game developers can do

Our problem is caused largely by a lack of shared information between updates of a game. If a developer could provide information about the new compilation that could help port mods more efficiently, all the downsides of updating a game for modders would disappear.

By far the easiest way to provide information to modders is by allowing the compiler to add debug information within the assembly code. By compiling the final build of an update using `RelWithDebInfo` instead of `Release`, the compiler generates a Program Database (`.pdb`) file that contains almost all of the information on function locations and structures that us modders need, and by including the generated `.pdb` file in the update files, the time needed to update mods for a new update of the game drastically decreases. This compiler change doesn't affect performance at all, as all optimizations for `Release` compilations are still present in `RelWithDebInfo`.

For a game like Geometry Dash, where more than 5 years of mod development on one update caused a rich modding scene, the inclusion of a `.pdb` would be great for the community. Most players use mods like Mega Hack and Better Edit daily, so a smoother transition for such mods would be incredibly beneficial. Plus, allowing modding tools to be developed using the actual information from the original game code allows for faster mod development times and prevents some bugs within mods.

Overall, there are few downsides to including a Program Database file with updates of a game. Omitting one only serves to delay mod development, as most of the information provided in a `.pdb` can be deduced through Reverse Engineering, and since most IDEs and Build Environments allow you to switch to `RelWithDebInfo` with the click of a button, it's extremely easy for game developers to include.

If you're a game developer looking to update your game soon (\*cough\* RobTopGames \*cough\*), please consider including a `.pdb`. It's a win-win: being easy for you while making our lives easier too. 