---
title: "How I made a starry night animation with Adobe and ImageMagick"
datePublished: Sat Dec 07 2024 05:27:09 GMT+0000 (Coordinated Universal Time)
cuid: cm4dqgac800040al91p4dacme
slug: how-i-made-a-starry-night-animation-with-adobe-and-imagemagick
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733541927920/f0049b09-ad0a-4e64-8ba5-4cdab219bf6c.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1733549107413/04e197c5-845d-4a53-891a-71690ae201b2.png
tags: adobe, photoshop, animation, illustrator, imagemagick, after-effects, stars

---

> In this article, I detail a creative process for enhancing my photograph of San Diego's skyline by adding a sparkling starry night effect. This involves using Adobe Illustrator for vectorization, Adobe Photoshop for separating the sky and adding stars, and Adobe After Effects to create and animate the stars. I explain how to prepare the image for animation, configure star effects, and ultimately produce a GIF that brings the skyline to life.

## The Idea

In the course of converting my website, I’ve been thinking up of fun things to accomplish to make my website more interesting and attractive. One idea came to me earlier this week revolving around my photograph of San Diego’s skyline.

[I wanted to make it sparkle](https://storybook.stephenjlu.com/iframe?globals=&args=&id=page--home-page&viewMode=story). Here’s how I did it.

***Artificial Intelligence Note:*** *While I produced a majority of the work and art, I used AI (*[*Adobe Firefly*](https://www.adobe.com/products/firefly.html)*) to generate the static image of a starry night to complement the animation. That’s the limit of AI use in this article.*

## How I Pulled It Off

### The Original Image

Here is the original image that I took on November 20, 2022 while taking the ferry to San Diego from Coronado Island.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733542481936/8342741d-fdcf-4346-98fe-ed4c903d1133.png align="center")

As you can see, it’s a perfect template for making some stars! I particularly enjoy the blue accent provided by The Shell. It’s an awesome venue if you haven’t been, and I highly recommend it.

### Step One: Vectorize the Image

In order to maintain sharpness and quality of the image, it must be vectorized. The image will likely be resized and manipulated, so it’s best to do this. I used **Adobe Illustrator** to accomplish this. You can do it pretty efficiently with **Image Trace**.

Here is a portion of the source image:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733543896961/79dd3fde-782c-466a-b98d-5220c193519f.png align="center")

This is what image trace does:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733543923462/ae82870d-f5a3-4687-a2ef-1c9159197c4b.png align="center")

And here is the vectorized image:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733543954373/835253ca-1df4-456e-a401-f37822d8f359.png align="center")

If you look closely, the source image has some fuzziness around the edges of the buildings. It’s pretty subtle until you really zoom in; but when you’re looking at the whole picture, it maintains its clarity no matter how much you resize it.

### Step Two: Separate the Sky and Make Some Stars!

The next part can be kind of tricky. I used **Adobe Photoshop** to help me with this next step. What you’ll need to do is to use **Polygon Select/Lasso** and trace along the skyline, leaving about a centimeter buffer of sky. Doing so will make the next step much easier. When you’ve gone through the entire skyline, complete the lasso by going up and around the edges of the sky and back to your original starting point.

Then hit Delete.

You’ll end up with a transparent sky. If not, change your background color to be transparent. Next, use the **Magic Wand** tool to select that centimeter of buffer you left in the previous step. Adjust the tolerances so that it doesn’t run into the buildings. If you need to do multiple runs of the wand, that’s okay. Delete that sky.

You should end up with something like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733544688662/07fd3528-ae3b-4209-9257-fac60a396219.png align="center")

Chicken Little has never been more right.

Now you have some options here. If you have your own static image of stars, create a new layer called **Sky** or **Stars** below the skyline layer and add your image. *Having two layers is important, because you want to be able to adjust the opacity of the sky layer.*

Or you can have some fun with AI. Adobe now has this nifty feature called **Generative Fill**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733546204240/4554bef3-d97f-49b9-8686-d0231dbba611.png align="center")

As you can see, I had some fun with this.

Okay, back to business. In the end, I ended up with a static image like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733546396971/f807d783-4339-406f-a7b5-b41217a7a485.png align="center")

It’s a nice backdrop to tack on some starry animations. Next, depending on how strongly you want your star animations to come through, reduce the opacity of the Stars layer to around 30% to 50%. The higher your opacity, the more subtle the animation will be.

Now export your image, ideally as a PNG, because you need to retain your transparency data. Also, take note of the resolution of your image. In my case, it was 1920×1080.

### Step Three: Make It Shine Like a Diamond

For the star animation, I used **Adobe After Effects**. This program is one of the most powerful and versatile animation tools out there, and I’ve had a hard time finding anything that provides the same utility (at least anything free). If you find something of equal caliber, please let me know.

Create a new project in AE, create a new composition, and create a new black, solid layer called **Stars** in the same resolution as your new PNG. Go to **Effect &gt; Simulation &gt; CC Particle World**. Then, adjust your simulation settings similar to what I used below.

***Orientation***

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733547253232/b9667e46-8aa6-46a8-9be4-b8e564e9a43f.png align="center")

You can play around with this after you set your settings, but I like to do this first since it can be finnicky. Increase the size of the world to cover your layer. Then adjust the perspective so that you’re looking down on it from above **(-Y)**.

For the following settings, play around with them and see how they change the particle simulator and adjust to your liking.

***Birth Rate***

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733547463023/3faf0637-e402-4002-856a-6556404a0c8e.png align="center")

How many baby stars are you making in a certain amount of time? I started low, then ended up with a birth rate of **1.2** for my animation.

***Longevity***

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733547551486/39988825-490d-4563-9b7c-53b2bf1a851d.png align="center")

I started at 3 seconds, but lowered this down to about **1.5 seconds**. The shorter the longevity, the more “sparkle” in your animation.

***Physics***

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733547635880/b7336165-2944-466f-993a-18242b54e20d.png align="center")

For all of these, you’ll want to make everything **0**. Unless you want it to look like you’re flying through space, your settings should make it so that the stars don’t move.

***Particle Types***

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733547730603/b722b0f5-ea2f-4abb-ad34-d9e994c3fed0.png align="center")

For this animation, **Star** is the obvious choice. I wanted my stars to appear and grow a little before fading away, so **Birth Size &lt; Death Size**. You can also change the colors. I wanted a blue tint to my stars, so that’s what you see here.

**Congratulations!** You’re a star daddy or mommy! ✨

### Step Four: Import Your Homegrown Skyline PNG

When you import your image, make sure it is above the **Stars** layer you created for your animation. This way, the animation will only show through the sky you created earlier. If you want to change your opacity, you’ll have to go back and make the modification, then import it again. I did this many times to get the effect I wanted.

### Step Five: Make that GIF

Ideally, you want the GIF to be as small as possible. So, making an animation that is about 3 - 6 seconds will result in a file size of about 10 MB. You’ll likely have to adjust your quality settings quite a bit so that your visitors aren’t downloading a GB of data just to view your animation. It’s not very complicated, so you don’t have to use 30fps. Don’t kill a mosquito with an axe.

In my case:

* **Resolution**: 1280×720px
    
* **Duration**: 3 seconds (I have an original animation of 1.5 seconds long, then reversed it and tacked it onto the back end so there’s no noticeable jump between loops of the animation)
    
* **Frame Rate**: 8 fps
    
* **Resulting File Size**: ~10 MB
    

To create my GIF, I chose to create a PNG sequence first, then used [**ImageMagick**](https://imagemagick.org/) to create the animated GIF. Doing it this way retained the image quality throughout the animation without being too large.

## The Final Result

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733548784932/dd7df7e0-aeb2-4bd9-84eb-97ca92039183.png align="center")

And there you have it! If you want to see the animation in action, [check out the Storybook](https://storybook.stephenjlu.com/iframe?globals=&args=&id=page--home-page&viewMode=story).

Hopefully, this gave you some ideas and ways to create your own. And never forget, ***you’re a star!***

## Changelog

December 7, 2024

* I made the header image a static image that I added the stars in. The contrast between the star field and skyline was too sharp, and I didn't really like that.
    
* Instead, I made the page background an animated star field and am now working on making it a darkly themed website.