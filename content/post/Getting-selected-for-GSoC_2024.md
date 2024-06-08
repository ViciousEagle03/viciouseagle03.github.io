+++
title = "Getting Selected for Google Summer of Code 2024"
date = 2024-06-07T16:11:56+05:30

+++
Hello everyone, this marks my first blog post where I pen down my experience and learnings about getting selected for Google Summer of Code.

## What is this series about?

In this series of blog posts, I mainly plan on walking you through what it was like working for GSoC, what challenges I faced, and how I overcame those.

![img](/images/GSoC_OA.png)

> Google Summer of Code is a global, online   program focused on bringing new contributors into open source software development. GSoC Contributors work with an open source organization on a 12+ week programming project under the guidance of mentors.

## Discovering GSoC: From Curiosity to Decision

I first came across Google Summer of Code (GSoC) during my early years at IIT Roorkee.

The idea of working on real-world open source projects with guidance from experienced mentors worldwide piqued my interest.
For me it provided a unique chance to collaborate with an international community of developers, enhancing my skills in coding, problem-solving, and project management. I was amazed by the idea of jumping into a library that so many people were part of really got me excited. Getting to help out and knowing it would make a difference for loads of folks - it resonated with me :) . So, I went on a hunt for an organisation that matched my skills and also kept me engaged enough to persistently work on the project idea throughout the summers ☀️.

## Proposal: A Daunting Task

Once I had chosen the organization and tackled some issues, opened a few pull requests, and familiarized myself with the codebase, the next step was to explore the idea list. Based on my interest, I chose NDCube (open-source SunPy affiliated package). I went through the tests and documentation to understand the NDCube package better and chose the **Serialization of NDCube Classes to ASDF** project. Crafting the proposal was a challenge, I had to keep it concise, sticking to the recommended 5-page limit.  With the support of the SunPy mentors, who provided valuable feedback, I was able to refine my proposal to meet the standards expected by GSoC.


## The NDCube package 

![](/images/ndcube.png)

Let us understand what really is the ndcube package, it is a Python package used for managing multi-dimensional data in astronomy. In astronomy, data often comes in arrays with multiple dimensions, such as images or spectroscopic data cubes. Each element of the array represents a measurement taken at a specific point in space and time. 
The challenge lies in managing the relationship between the array elements data and their corresponding physical locations in the observed sky. This is where the World Coordinate System (WCS) framework comes in. WCS provides a standardized way to relate array axes to physical coordinates(connects points in the image to their real locations in space).

Now, imagine you have lots of pictures, each showing a different part of the sky or taken at a different time. It would be great if you could combine all these pictures into one big map, right? That's where ndcube comes in. It's like a tool that takes all these pictures and their coordinate information and puts them together in a way that makes them easy to work with.

With ndcube, you can slice these pictures, zoom in on specific regions, or compare different pictures side by side. You can also transform the coordinates, which is like moving your map from one location to another or rotating it to see things from a different angle. And the best part is, ndcube does all this while keeping track of the complex coordinate transformations of data points. As ndcube puts it:
> The fundamental reason to opt for ndcube is to harness the astronomy-specific World Coordinate System (WCS).

## The Waiting Game: From Submission to Selection

After a month of anticipation, the long-awaited email arrived from the GSoC team: my proposal had been accepted by OpenAstronomy(The umbrella organization) 😁.

![](/images/Acceptance.png)

Getting into GSoC is super exciting and it marks the start of an amazing journey. I know it’s going to take a lot of hard work and dedication, but I’m really looking forward to the challenges ahead and most importantly, the incredible learning experience that GSoC brings.

The mentors overseeing my project are [Daniel Ryan](https://github.com/DanRyanIrish), [Stuart Mumford](https://github.com/cadair), and [Brett Graham](https://github.com/braingram).

I plan on writing the next post within a week or two, documenting the progress I make in the project.