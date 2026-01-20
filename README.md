# Hugo's Dungeon

A cinematic 2D platformer inspired by the classic 1989 Prince of Persia, featuring weighty physics and the iconic ledge grab mechanic.

![Game Type](https://img.shields.io/badge/Type-Platformer-blue)
![Tech](https://img.shields.io/badge/Tech-HTML5%20Canvas-orange)
![Status](https://img.shields.io/badge/Status-MVP-green)

## Quick Start

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd hugo_dungeon
   ```

2. **Open and play**
   ```bash
   # Open index.html in your browser
   open index.html    # macOS
   xdg-open index.html  # Linux
   start index.html   # Windows
   ```

   Or simply drag `index.html` into your browser.

3. **No build process required!** Everything runs directly in the browser.

## Story

You are **Hugo**, a young adventurer trapped in a dangerous dungeon. Navigate treacherous platforms, avoid deadly spikes, and master the art of ledge grabbing to reach the golden door and escape!

## Controls

| Key | Action |
|-----|--------|
| **← →** | Move left/right |
| **SPACE** | Jump |
| **↑** (while hanging) | Climb up onto ledge |
| **SPACE** (while hanging) | Drop down from ledge |

## Core Mechanics

### Weighty Movement
- **Momentum-based physics**: Hugo accelerates and decelerates smoothly
- **No instant movement**: Feel the weight of each action
- **Air control**: Maintain good control while airborne for precise platforming

### Ledge Grab System
The signature Prince of Persia mechanic:

1. **Jump toward a platform** while moving horizontally
2. **Hugo automatically grabs the ledge** when his hands reach the platform edge
3. **Press UP** to climb onto the platform
4. **Press SPACE** to drop down

**Pro tip**: You can grab ledges whether jumping up OR falling down!

### Hazards
- **Red Spikes**: Instant death - avoid at all costs!
- **Gaps**: Fall too far and you'll restart the level

### Victory
Reach the glowing **Golden Door** at the top of the dungeon to complete the level!

## Technical Details

### Physics Constants

Located in `index.html` lines 59-65:

```javascript
const GRAVITY = 0.6;           // Downward acceleration
const ACCELERATION = 0.8;      // How fast Hugo speeds up
const MAX_SPEED = 6;           // Maximum horizontal speed
const FRICTION = 0.92;         // Momentum retention (higher = more momentum)
const JUMP_FORCE = -13;        // Initial jump velocity
const AIR_CONTROL = 0.8;       // Movement control while airborne
const CLIMB_SPEED = 3;         // Speed of climbing animation
```

**Tuning Guide**:
- Increase `FRICTION` (0.90-0.95) for more momentum
- Increase `AIR_CONTROL` (0.7-1.0) for easier platforming
- Increase `JUMP_FORCE` (negative values) for higher jumps
- Increase `MAX_SPEED` for faster movement

### Game States

Hugo can be in one of six states:

1. **IDLE** - Standing still on solid ground
2. **RUNNING** - Moving left or right on ground
3. **JUMPING** - Ascending after jump
4. **FALLING** - Descending through air
5. **HANGING** - Gripping a ledge
6. **CLIMBING** - Pulling up onto a platform

### Architecture

The game uses a simple game loop pattern:

```
gameLoop()
  ├── updatePlayer()
  │   ├── Handle input
  │   ├── Apply physics
  │   ├── Check collisions
  │   └── Update state
  └── render()
      ├── drawBackground()
      ├── drawPlatforms()
      ├── drawSpikes()
      ├── drawDoor()
      └── drawPlayer()
```

## File Structure

```
hugo_dungeon/
├── index.html          # Complete game (HTML + CSS + JS)
├── README.md           # This file
├── DEVELOPMENT.md      # Development guide
└── .gitignore         # Git ignore rules
```

## Browser Compatibility

Works in all modern browsers:
- Chrome/Edge (Chromium) 90+
- Firefox 88+
- Safari 14+

## Current Features (MVP)

- ✅ Momentum-based movement system
- ✅ Ledge grab and climb mechanic
- ✅ Spike hazards with level reset
- ✅ Victory condition (golden door)
- ✅ One complete level
- ✅ Debug info display
- ✅ Placeholder graphics (colored rectangles)

## Planned Features

See [DEVELOPMENT.md](DEVELOPMENT.md) for the roadmap and how to contribute.

## Development

To modify the game:

1. Open `index.html` in your favorite code editor
2. All game code is in the `<script>` section
3. Save and refresh your browser to see changes
4. Check browser console (F12) for any errors

For detailed development instructions, see [DEVELOPMENT.md](DEVELOPMENT.md).

## Credits

**Concept**: Inspired by Prince of Persia (1989) by Jordan Mechner
**Developer**: Built as an HTML5 Canvas demonstration
**Character**: Hugo, the adventurer

## License

This is a demonstration project. Feel free to use and modify as needed.

## Troubleshooting

**Hugo won't grab ledges**
- Make sure you're moving horizontally when you jump
- Try jumping a bit earlier before the gap
- The detection range is ±15 pixels from the platform edge

**Jumps are too hard**
- Adjust `FRICTION` to 0.94 for more momentum
- Increase `MAX_SPEED` to 7 or 8
- Increase `JUMP_FORCE` to -14 or -15

**Game runs too fast/slow**
- The game uses `requestAnimationFrame` (~60 FPS)
- Performance depends on your browser and hardware
- Try closing other browser tabs if it's laggy

## Contact

Found a bug? Have suggestions? Open an issue on GitHub!

---

**Made with ❤️ using vanilla HTML5 Canvas and JavaScript**
