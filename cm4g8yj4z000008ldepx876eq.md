---
title: "How I Messed Up My Menu Bar"
datePublished: Sun Dec 08 2024 23:40:46 GMT+0000 (Coordinated Universal Time)
cuid: cm4g8yj4z000008ldepx876eq
slug: how-i-messed-up-my-menu-bar
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733700923467/740c6d1c-9a21-47e8-be14-2e09ec3f4238.png
tags: reactjs, coding, mistakesweremade

---

> I initially struggled with organizing my React project's components, particularly the menu bar, leading to a messy code base. After reorganizing the structure into distinct components with dedicated files for the menu bar and buttons, I found debugging much easier. I learned the importance of maintaining component-based architecture, which improves both code readability and functionality. Silly me.

Well, I didn’t do what I said I would do. I suppose it was inevitable since I’m still getting used to it. Anyway, here’s what I did wrong.

## What Went Wrong

Even though the previous iteration worked, it was messy. I didn’t break the menu bar up into its own components and built up from that. Even after going on about how React is component-based and everything, I went ahead and did the exact opposite. Whoops.

So, to fix it, I went back and reorganized a significant amount of code, and restructured the hierarchy. I now have `Components` folder which holds background animation, the menu bar, text animations, and soon-to-be-developed text components and transitions.

Higher up in the hierarchy is my `Stories` folder, which houses the footer, header, and page-level structure. In addition, I streamlined and consolidated all the exports and imports into index files. Whew!

True to form, it was so much easier to debug, too.

## New Menu Bar Structure

Now, I have **four** files to construct the menu bar: `menuBar.tsx`, `menuBar.css`, `MenuButton.tsx`, and `MenuButton.css`. Those of you who are annoyed at my capitalization, I feel you. I wasn’t really paying attention. Here’s what they look like now (roughly).

```typescript
/* menuBar.tsx */
/* The logic here is CONSIDERABLY optimized from the previous iteration */
import React, { useRef } from "react";
import "./menuBar.css";
import MenuButton, { MenuItem } from "./MenuButton";
export interface MenuBarProps {
  items: MenuItem[];
  backgroundColor?: string;
  onSelect?: (item: MenuItem) => void;
  activeItem?: string;
}
export const MenuBar: React.FC<MenuBarProps> = ({
  items,
  backgroundColor,
  onSelect,
  activeItem,
}) => {
  const menuBarRef = useRef<HTMLDivElement>(null);
  const handleClick = (item: MenuItem) => {
    if (onSelect) {
      onSelect(item);
    }
  };
```

And now for the rendering:

```typescript
/* menuBar.tsx */
/* Rendering, too */
return (
    <div
      className="menu-bar-container"
      ref={menuBarRef}
      style={{ backgroundColor }}
    >
      <nav className="menu-bar">
        <ul className="menu-bar-list">
          {items.map((item, index) => (
            <MenuButton
              key={index}
              item={item}
              isActive={activeItem === item.label}
              onClick={handleClick}
            />
          ))}
        </ul>
      </nav>
    </div>
  );
};
```

And here’s the awesomeness that is React: `MenuButton.tsx`:

```typescript
import React from "react";
import "./MenuButton.css";
export interface MenuItem {
  label: string;
  onClick?: () => void;
  action?: string;
  targetID?: string;
  url?: string;
}
interface MenuButtonProps {
  item: MenuItem;
  isActive?: boolean;
  onClick: (item: MenuItem) => void;
}
const MenuButton: React.FC<MenuButtonProps> = ({ item, isActive }) => {
  const handleClick = () => {
    if (item.onClick) {
      item.onClick();
    }
    if (item.action === "scroll" && item.targetID) {
      console.log(item.label + " clicked!");
    } else if (item.action === "open" && item.url) {
      window.open(item.url, "_blank", "noopener,noreferrer");
    }
  };
  return (
    <li>
      <button
        type="button"
        onClick={handleClick}
        className={`menu-button${isActive ? " active" : ""}`}
      >
        {item.label}
      </button>
    </li>
  );
};
export default MenuButton;
```

There’s a little bit more involved for my actual site, since this is just for CodeSandbox, but the gist is the same.

## Lesson Learned

I certainly learned my lesson. Getting adjusted to a new system isn’t quick or easy, and I guess I got ahead of myself.

### CodeSandbox Preview

<iframe src="https://codesandbox.io/embed/qxczfv?view=preview&module=%2Fsrc%2FPage.tsx&hidenavigation=1&expanddevtools=1" style="width:100%;height:75vh;border:0;border-radius:4px;overflow:hidden" sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"></iframe>