---
title: "How I made a basic menu bar with React and Storybook"
datePublished: Thu Dec 05 2024 06:03:42 GMT+0000 (Coordinated Universal Time)
cuid: cm4awvkn4000408l1g2y72rig
slug: how-i-made-a-basic-menu-bar
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733374769944/4467bd2d-b50f-4d96-a020-97a137fde6aa.png
tags: reactjs, coding, components, storybook

---

> In this article, I detail my experience transitioning my personal website's menu from HTML/CSS to a React component-based UI using Storybook. I describe my design goals, including maintaining a similar style, adding animations, and managing active states. I share the code and styling I used for the menu bar and explain the benefits of Storybook for developing components in isolation. I hope to provide insights and invite feedback as I continue to learn and grow in coding.

***Amateur Alert!***

I want to preface my first coding article with a warning: I am not a professional developer. I took courses in various languages throughout my life and have dabbled in front-end development with a few personal and volunteer projects. I’ve decided to start writing entries about things I’ve managed to pull off. I welcome any and all feedback about how to improve, and I hope my writing will help someone else who is also just starting out. Please forgive any mistakes in my explanation or actual code. I’m still learning. Alright, let’s get into this.

## The Idea

I am in the process of converting my personal website from HTML/CSS to React. I’m interested in getting into the mindset of designing a component based UI (plus, it might make my site look prettier).

*This is the old menu:*

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733375177067/a600b01d-412c-422b-a5f8-cacbd1f2d43a.png align="center")

I didn’t want to drastically modify my style, since I’m still learning this. I don’t want to make it too complicated and overwhelm myself. So my requirements were as follows:

1. Similar design, a black rounded bar with text items
    
2. Add a bit of animation on hover/click for some fun
    
3. Clicking on the menu item will link to a different page story, not an external page (I’ll get into stories next)
    
4. Implement active states for each menu item
    

## Storybook Stories

[Storybook](https://storybook.js.org/) employs a story architecture that enables developers to build, document, and test UI components in isolation. Each "story" represents a specific state or configuration of a UI component. What this does is allow development of components in isolation, which ensures that each component functions correctly on its own. Another purpose is to provide visual documentation of each story “state.” This helps collaborators document and explain how a component behaves with different inputs, such as different args, props, contexts, etc.

Essentially, this architecture creates a durable, reusable, and testable library of individually designed UI elements that streamlines productivity and collaboration.

If you want to skip ahead and view the finished story, [check out the live Storybook](https://storybook.stephenjlu.com/?path=/story/menu-bar--default).

## CSS Formatting

With anything I develop, I first develop the visual look. Transitioning into the component mindset took me a little adjustment, since I was so used to hardcoding everything with HTML and CSS. I now had to think in props and args. Here is the styling I employed for my black menu bar:

```css
/* menuBar.css */
/* The overall bar design */
.storybook-menu-bar {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px;
  color: white;
  font-family: 'Atlas Grotesk', 'Helvetica Neue', Helvetica, Arial, sans-serif;
  border-radius: 30px;
}

.storybook-menu-bar__list {
  list-style: none;
  display: flex;
  gap: 20px; /* add a gap between items in the list */
  padding: 0;
  margin: 0;
}

.storybook-menu-bar__item {
  display: inline-block; /* make sure the items don't run into each other */
}

.storybook-menu-bar__button {
  background: none;
  border: none;
  color: white;
  cursor: pointer;
  font-size: 16px;
  font-weight: 700;
  padding: 10px 20px;
  border-radius: 3em;
  transition: background-color 0.3s ease;
}

/* This class is for active menu items */
.storybook-menu-bar__button.storybook-menu-bar__active {
  background-color: rgba(255, 255, 255, 0.9);
  color: #000;
  font-weight: 700;
  /* Add subtle bottom border/indicator */
  border-bottom: 2px solid rgba(255, 255, 255, 0.9);
  /* Match existing transition */
  transition: all 0.3s ease;
}

  /* Ensure hover state doesn't override active state */
.storybook-menu-bar__button.storybook-menu-bar__active:hover {
  background-color: rgba(255, 255, 255, 1);
}

/* and here is the hover animation */
.storybook-menu-bar__button:hover {
  background-color: rgba(255, 255, 255, 0.75);
  color: #000;
  animation: bounce 0.5s; /* Apply the bounce animation on hover */
}

@keyframes bounce {
  0%, 20%, 50%, 80%, 100% { /* keyframe designations: this is the return to "ground" */
    transform: translateY(0);
  }
  40% {
    transform: translateY(-10px); /* This is the first higher bounce */
  }
  60% {
    transform: translateY(-5px); /* This is the second bounce, lower than the first */
  }
}
```

## Functions and Props

Now for the meat of the menu bar. Here, when the user clicked on a menu item, I wanted the function to return its value, make it active, and wrap it in the active CSS class. Check out the code below:

```typescript
/** MenuBar.tsx */
import React, { useState, useEffect } from 'react';
import './menuBar.css';

export interface MenuItem {
  /** The label of the menu item */
  label: string;
  /** Optional click handler */
  onClick?: () => void;
}

export interface MenuBarProps {
  /** Array of menu items */
  items: MenuItem[];
  /** Optional background color for the menu bar. I might want to change the background
color in different areas. I might not, but it's good to have the option. */
  backgroundColor?: string;
  /** Callback when a menu item is selected. Actual actions will occur in the higher
level component. */
  onSelect?: (item: MenuItem) => void;
  /** The currently active item */
  activeItem?: string;
}

/** Primary UI component for navigation */
export const MenuBar = ({ items, backgroundColor, onSelect, activeItem }: MenuBarProps) => {
  const [localactiveItem, setLocalActiveItem] = useState(activeItem || null);
/** The effect of clicking -> setting the active item */
  useEffect(() => {
    setLocalActiveItem(activeItem || null);
  }, [activeItem]);
/** The result of clicking -> updates which is now active */
  const handleClick = (item: MenuItem) => {
    setLocalActiveItem(item.label);
    if (item.onClick) {
      item.onClick();
    }
/** The result of clicking -> Parent component is notified of the selected item */
    if (onSelect) {
      onSelect(item);
    }
  };

/** Rendering the menu bar and all the items. Wraps active items in the 
CSS active class */
  return (
    <nav className="storybook-menu-bar" style={{ backgroundColor }}>
      <ul className="storybook-menu-bar__list">
        {items.map((item, index) => (
          <li key={index} className="storybook-menu-bar__item">
            <button
              type="button"
              onClick={() => handleClick(item)}
              className={`storybook-menu-bar__button ${
                localactiveItem === item.label ? 'storybook-menu-bar__active' : ''
              }`}
            >
              {item.label}
            </button>
          </li>
        ))}
      </ul>
    </nav>
  );
};
```

## It’s Story Time!

Now for Storybook’s unique contribution. There’s a lot of information here, and Storybook has good documentation on how all of this works. For now, I’ll comment in the code, like above.

```typescript
/** menuBar.stories.ts */
import type { Meta, StoryObj } from '@storybook/react';
import { MenuBar, MenuBarProps } from './MenuBar';
import { fn } from '@storybook/test';

// More on how to set up stories at: 
// https://storybook.js.org/docs/react/writing-stories/introduction
const meta = {
  title: 'Menu Bar',
  component: MenuBar,
  parameters: {
// Optional parameter to center the component in the Canvas.
// More info: https://storybook.js.org/docs/react/configure/story-layout
    layout: 'centered',
  },
// This component will have an automatically generated Autodocs entry: 
// https://storybook.js.org/docs/react/writing-docs/autodocs
  tags: ['autodocs'],
// More on argTypes: https://storybook.js.org/docs/react/api/argtypes
// Basically, these are the dynamic aspects of each component.
  argTypes: {
    backgroundColor: { control: 'color' },
    activeItem: { 
      control: 'select',
    options: ['Home', 'About', 'Services', 'Contact'],
  },
},

} satisfies Meta<typeof MenuBar>;

export default meta;
type Story = StoryObj<typeof meta>;

// More on writing stories with args:
// https://storybook.js.org/docs/react/writing-stories/args
// These define the different stories (or states) of each component
export const Default: Story = {
  args: {
    items: [
      { label: 'Home', onClick: fn() },
      { label: 'About', onClick: fn() },
      { label: 'Services', onClick: fn() },
      { label: 'Contact', onClick: fn() },
    ], 
  backgroundColor: '#000',    
},

};

export const Active: Story = {
  args: {
    items: [
      { label: 'Home', onClick: fn() },
      { label: 'About', onClick: fn() },
      { label: 'Services', onClick: fn() },
      { label: 'Contact', onClick: fn() },
    ],
    backgroundColor: "#000",
    activeItem: "Home",      
},
};
```

## Putting It All Together

I’ll admit, I was a little overwhelmed at first with this different kind of thinking. As I’ve used it more and more, however, I’m beginning to really enjoy coding this.

In the end, Storybook takes these three files (menuBar.css, MenuBar.tsx, menuBar.stories.ts) and paints a complete picture of a single component independent of any other element, allowing you to look at how it looks and behaves on its own.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733378753774/f68f1b32-9f6a-494e-885e-201873ef5872.png align="center")

To review:

* menuBar.css - Provides the styling and isolated CSS animations
    
* MenuBar.tsx - Provides the logic for the component
    
* menuBar.stories.ts - Provides the instructions for examination in Storybook
    

[**Check out the live Storybook**](https://storybook.stephenjlu.com/?path=/story/menu-bar--default)

### What's Next?

Make the menu bar responsive to screen size (desktop vs. mobile).

---

Whew! I hope this helps a bit, and you got a good look at how I created a basic menu bar for my website. Feel free to drop a comment below or [contact me](https://www.stephenjlu.com/contact#main). Happy coding!

## Changelog

Improvements and updates will appear here.