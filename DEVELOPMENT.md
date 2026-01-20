# Development Guide - Hugo's Dungeon

This guide covers everything you need to know to continue developing Hugo's Dungeon.

## Table of Contents

1. [Code Structure](#code-structure)
2. [Key Systems](#key-systems)
3. [Adding New Features](#adding-new-features)
4. [Level Design](#level-design)
5. [Physics Tuning](#physics-tuning)
6. [Roadmap](#roadmap)
7. [Best Practices](#best-practices)

## Code Structure

The entire game is in `index.html` with three main sections:

### 1. HTML Structure (lines 1-51)
```html
<div id="gameContainer">
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <div id="message"></div>
    <div id="info"></div>
</div>
```

### 2. CSS Styling (lines 8-40)
- Dark theme styling
- Centered layout
- Info text styling

### 3. JavaScript Game Code (lines 53-600+)

**Section Breakdown**:

| Lines | Section | Purpose |
|-------|---------|---------|
| 54-65 | Constants | Physics values, game settings |
| 67-76 | Player State | State enum definition |
| 78-87 | Player Object | Player properties and state |
| 89-110 | Level Data | Platforms, spikes, door |
| 112-151 | Input Handling | Keyboard event listeners |
| 153-198 | Platform Collision | Ground detection & collision |
| 200-244 | Ledge Grab | Core ledge grab mechanic |
| 245-265 | Hazard Collision | Spikes and door detection |
| 267-400+ | Game Logic | Update loop and player movement |
| 400-550+ | Rendering | Drawing all game objects |
| 550+ | Game Loop | Main loop and initialization |

## Key Systems

### Player State Machine

The player transitions between states based on conditions:

```
IDLE ──────────────┐
  ↓                │
RUNNING ───────────┤
  ↓                │
JUMPING           GROUNDED
  ↓                ↑
FALLING ──────────┘
  ↓
HANGING
  ↓
CLIMBING ─────────→ IDLE
```

**State Transitions**:
- `IDLE → RUNNING`: Left/Right key pressed while grounded
- `RUNNING → JUMPING`: Space pressed while grounded
- `JUMPING → FALLING`: Vertical velocity becomes positive
- `FALLING → HANGING`: Ledge grab triggered
- `HANGING → CLIMBING`: Up key pressed
- `CLIMBING → IDLE`: Climb animation complete

### Collision Detection System

**checkPlatformCollision()** (lines 161-198):
- Calculates overlap on all four sides
- Resolves based on minimum overlap
- Sets `player.grounded` flag
- Stops movement into solid objects

**checkLedgeGrab()** (lines 200-244):
- Only checks when airborne (`!grounded`)
- Detects when player center is within ±15px of platform edge
- Checks horizontal velocity direction
- Snaps player to hanging position

**checkSpikeCollision()** (lines 245-251):
- Simple AABB collision
- Calls `resetLevel()` on contact

### Physics System

Applied in `updatePlayer()` every frame:

```javascript
// 1. Input → Acceleration
if (keys.right) {
    player.vx += ACCELERATION * control;
}

// 2. Friction
player.vx *= FRICTION;

// 3. Speed Limiting
player.vx = Math.max(-MAX_SPEED, Math.min(MAX_SPEED, player.vx));

// 4. Gravity
player.vy += GRAVITY;

// 5. Position Update
player.x += player.vx;
player.y += player.vy;

// 6. Collision Resolution
checkPlatformCollision();
checkLedgeGrab();
```

## Adding New Features

### 1. Adding a New Enemy

```javascript
// In level data section (around line 110)
const enemies = [
    {
        x: 400,
        y: 520,
        width: 30,
        height: 30,
        vx: 2,  // Movement speed
        direction: 1  // 1 = right, -1 = left
    }
];

// Add update function
function updateEnemies() {
    enemies.forEach(enemy => {
        enemy.x += enemy.vx * enemy.direction;

        // Reverse at platform edges
        if (enemy.x < 0 || enemy.x > 800) {
            enemy.direction *= -1;
        }

        // Check collision with player
        if (checkCollision(player, enemy)) {
            resetLevel();
        }
    });
}

// Add to game loop
function updatePlayer() {
    // ... existing code ...
    updateEnemies();  // Add this line
}

// Add rendering
function drawEnemies() {
    enemies.forEach(enemy => {
        ctx.fillStyle = '#8B0000';
        ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
    });
}

// Call in render section
function gameLoop() {
    // ...
    drawEnemies();  // Add this line
    // ...
}
```

### 2. Adding Power-ups

```javascript
// Define power-up
const powerups = [
    {
        x: 300,
        y: 400,
        width: 20,
        height: 20,
        type: 'doubleJump',
        collected: false
    }
];

// Add to player object
const player = {
    // ... existing properties ...
    hasDoubleJump: false,
    jumpCount: 0
};

// Check collection
function checkPowerupCollision() {
    powerups.forEach(powerup => {
        if (!powerup.collected && checkCollision(player, powerup)) {
            powerup.collected = true;

            if (powerup.type === 'doubleJump') {
                player.hasDoubleJump = true;
                messageDiv.textContent = 'Double Jump Unlocked!';
            }
        }
    });
}

// Modify jump logic
if (keys.space && (player.grounded || (player.hasDoubleJump && player.jumpCount < 2))) {
    player.vy = JUMP_FORCE;
    player.jumpCount++;
    player.state = PlayerState.JUMPING;
}

// Reset jump count when grounded
if (player.grounded) {
    player.jumpCount = 0;
}
```

### 3. Adding a New Level

```javascript
// Create level configurations
const levels = [
    {
        name: "Level 1",
        platforms: [ /* level 1 platforms */ ],
        spikes: [ /* level 1 spikes */ ],
        door: { x: 720, y: 160, width: 30, height: 40 }
    },
    {
        name: "Level 2",
        platforms: [ /* level 2 platforms */ ],
        spikes: [ /* level 2 spikes */ ],
        door: { x: 650, y: 100, width: 30, height: 40 }
    }
];

let currentLevel = 0;

// Load level function
function loadLevel(levelIndex) {
    const level = levels[levelIndex];
    platforms.length = 0;
    spikes.length = 0;

    platforms.push(...level.platforms);
    spikes.push(...level.spikes);
    door.x = level.door.x;
    door.y = level.door.y;
    door.reached = false;

    // Reset player
    player.x = 100;
    player.y = 450;
    player.vx = 0;
    player.vy = 0;
    player.state = PlayerState.IDLE;

    messageDiv.textContent = `${level.name} Start!`;
}

// Modify door collision
function checkDoorCollision() {
    if (!door.reached && checkCollision(player, door)) {
        door.reached = true;
        currentLevel++;

        if (currentLevel < levels.length) {
            messageDiv.textContent = 'Level Complete!';
            setTimeout(() => loadLevel(currentLevel), 2000);
        } else {
            messageDiv.textContent = 'Game Complete! You escaped!';
        }
    }
}
```

## Level Design

### Platform Design Guidelines

1. **Jump Distance**: With current physics, Hugo can jump approximately:
   - Horizontal: ~120-150 pixels
   - Vertical: ~80-100 pixels

2. **Safe Gaps**:
   - Easy: 60-80 pixels
   - Medium: 90-110 pixels
   - Hard: 120-140 pixels

3. **Ledge Grab Opportunities**:
   - Place platforms at jump height for ledge grabs
   - Ensure horizontal velocity can reach the edge
   - Detection range is ±15 pixels

4. **Creating a Platform**:
```javascript
{
    x: 400,        // Left edge position
    y: 450,        // Top edge position
    width: 100,    // Platform width
    height: 30     // Platform thickness (usually 30)
}
```

### Example Level Layout

```javascript
// Beginner friendly level
const beginnerLevel = {
    platforms: [
        // Starting area
        { x: 0, y: 550, width: 200, height: 50 },

        // Easy gap (80px)
        { x: 280, y: 550, width: 150, height: 50 },

        // Ascending platforms
        { x: 380, y: 480, width: 120, height: 30 },
        { x: 250, y: 410, width: 120, height: 30 },
        { x: 420, y: 340, width: 120, height: 30 },

        // Final platform with door
        { x: 600, y: 250, width: 200, height: 30 }
    ],
    spikes: [
        // Under first gap
        { x: 200, y: 540, width: 80, height: 10 }
    ]
};
```

## Physics Tuning

### Testing Your Changes

1. Open browser console (F12)
2. Add temporary logs:
```javascript
console.log('Jump force:', JUMP_FORCE, 'Max height:', Math.abs(JUMP_FORCE * JUMP_FORCE / (2 * GRAVITY)));
```

3. Test specific scenarios:
   - Minimum jump distance
   - Maximum jump distance
   - Ledge grab timing
   - Momentum retention

### Common Adjustments

**Make jumps easier**:
```javascript
const MAX_SPEED = 7;           // +1
const FRICTION = 0.94;         // +0.02
const AIR_CONTROL = 0.9;       // +0.1
```

**Make movement more responsive**:
```javascript
const ACCELERATION = 1.2;      // +0.4
const FRICTION = 0.88;         // -0.04
```

**Make it feel more like Prince of Persia**:
```javascript
const ACCELERATION = 0.6;      // Slower acceleration
const MAX_SPEED = 5;           // Moderate speed
const FRICTION = 0.90;         // Good momentum
const AIR_CONTROL = 0.4;       // Limited air control
const JUMP_FORCE = -13;        // Precise jumps
```

## Roadmap

### Phase 1: MVP ✅ (Current)
- [x] Basic movement and physics
- [x] Ledge grab mechanic
- [x] One level
- [x] Spike hazards
- [x] Victory condition
- [x] Placeholder graphics

### Phase 2: Enhanced Gameplay
- [ ] Multiple levels (3-5 levels)
- [ ] Checkpoints
- [ ] Moving platforms
- [ ] Basic enemies
- [ ] Collectibles
- [ ] Level timer
- [ ] Death counter
- [ ] Level select screen

### Phase 3: Polish
- [ ] Sprite graphics for Hugo
- [ ] Sprite graphics for environment
- [ ] Animation frames (run, jump, hang, climb)
- [ ] Particle effects
- [ ] Sound effects
- [ ] Background music
- [ ] Smoother camera (follow player)

### Phase 4: Advanced Features
- [ ] Sword combat system
- [ ] Boss fights
- [ ] Story cutscenes
- [ ] Save system (localStorage)
- [ ] Achievements
- [ ] Speedrun mode
- [ ] Level editor

## Best Practices

### 1. Commenting Code

```javascript
// BAD: No context
function update() {
    x += vx;
}

// GOOD: Explains why
// Apply horizontal velocity to position
// This happens after friction and before collision detection
function updatePosition() {
    player.x += player.vx;
}
```

### 2. Magic Numbers

```javascript
// BAD: Magic numbers
if (player.y > 600) resetLevel();

// GOOD: Named constants
const DEATH_ZONE_Y = canvas.height;
if (player.y > DEATH_ZONE_Y) resetLevel();
```

### 3. Testing Collision Changes

Always test:
1. Landing on platform from above
2. Hitting platform from below (head bump)
3. Walking into platform from left
4. Walking into platform from right
5. Ledge grab from both directions
6. Climbing up
7. Dropping down from hang

### 4. Performance

Current game runs at ~60 FPS. To maintain performance:
- Avoid creating objects in game loop
- Use object pooling for particles/enemies
- Limit particle counts
- Use sprite sheets instead of individual images
- Cache calculated values

### 5. Git Workflow

```bash
# Create feature branch
git checkout -b feature/add-enemies

# Make changes and test
# ... edit index.html ...

# Commit with descriptive message
git add index.html
git commit -m "Add basic enemy AI with patrol behavior"

# Push to remote
git push origin feature/add-enemies
```

## Debugging Tips

### Visual Debugging

Add to render section:

```javascript
function drawDebugInfo() {
    ctx.strokeStyle = '#00ff00';
    ctx.lineWidth = 2;

    // Player hitbox
    ctx.strokeRect(player.x, player.y, player.width, player.height);

    // Ledge grab detection zones
    platforms.forEach(platform => {
        // Left edge grab zone
        ctx.fillStyle = 'rgba(255, 0, 0, 0.2)';
        ctx.fillRect(platform.x - 15, platform.y - 15, 30, 30);

        // Right edge grab zone
        ctx.fillRect(platform.x + platform.width - 15, platform.y - 15, 30, 30);
    });
}

// Call in game loop
drawDebugInfo();
```

### Console Logging

```javascript
// Log state changes
const oldState = player.state;
// ... state update logic ...
if (oldState !== player.state) {
    console.log(`State: ${oldState} → ${player.state}`);
}
```

## Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| Player falls through platforms | Check collision detection order; ensure it runs after position update |
| Ledge grab not working | Verify platform edge coordinates; check if `grounded` is false; increase detection range |
| Jumps inconsistent | Make sure `grounded` flag is set correctly; check for key polling vs events |
| Physics feel wrong | Adjust constants incrementally; test each change; use console logs to verify values |
| Game runs slow | Check for infinite loops; reduce drawing operations; profile with browser dev tools |

## Resources

- **HTML5 Canvas API**: https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API
- **Game Loop Pattern**: https://gameprogrammingpatterns.com/game-loop.html
- **Platformer Physics**: https://www.youtube.com/watch?v=hG9SzQxaCm8
- **Prince of Persia Postmortem**: Classic Game Postmortem by Jordan Mechner

---

Happy developing! If you have questions, check the code comments or experiment in the browser console.
