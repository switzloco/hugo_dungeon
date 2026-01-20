# Contributing to Hugo's Dungeon

Thank you for your interest in contributing to Hugo's Dungeon! This document provides guidelines and best practices for contributing to the project.

## Getting Started

1. **Fork the repository** on GitHub
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/hugo_dungeon.git
   cd hugo_dungeon
   ```
3. **Create a feature branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```

## Development Workflow

### Making Changes

1. **Open `index.html`** in your preferred code editor
2. **Make your changes** to the code
3. **Test thoroughly** by opening the file in multiple browsers
4. **Document your changes** with clear comments

### Testing Checklist

Before submitting, test these scenarios:

- [ ] Movement (left, right, acceleration, deceleration)
- [ ] Jumping (height, distance, air control)
- [ ] Ledge grabbing (from left, from right, while ascending, while falling)
- [ ] Climbing (animation completes, correct positioning)
- [ ] Spike collision (resets level)
- [ ] Door collision (shows victory message)
- [ ] Falling out of bounds (resets level)
- [ ] No console errors in browser DevTools (F12)

### Browser Testing

Test in at least 2 of these browsers:
- Chrome/Chromium
- Firefox
- Safari
- Edge

## Code Style

### JavaScript Conventions

```javascript
// 1. Use const for constants
const GRAVITY = 0.6;
const MAX_SPEED = 6;

// 2. Use descriptive variable names
// BAD
let x = 10;

// GOOD
let platformX = 10;

// 3. Use camelCase for variables and functions
function checkPlatformCollision() { }
const playerState = PlayerState.IDLE;

// 4. Use UPPER_SNAKE_CASE for true constants
const JUMP_FORCE = -13;
const AIR_CONTROL = 0.8;

// 5. Add comments for complex logic
// Calculate minimum overlap to determine collision side
const minOverlap = Math.min(overlapLeft, overlapRight, overlapTop, overlapBottom);
```

### Code Organization

Keep the existing structure:

```javascript
// 1. Constants (physics, game settings)
// 2. Enums (PlayerState, etc.)
// 3. Game objects (player, platforms, etc.)
// 4. Input handling
// 5. Collision detection functions
// 6. Game logic functions
// 7. Rendering functions
// 8. Game loop
```

### Comments

```javascript
// BAD: Obvious comments
// increment x
x++;

// GOOD: Explain why or provide context
// Apply friction to simulate ground resistance and air drag
player.vx *= FRICTION;

// GOOD: Document complex algorithms
/**
 * Checks if player can grab a ledge
 * Detection happens when:
 * - Player is airborne (not grounded)
 * - Player's hands (top of sprite) are within ±15px of platform top
 * - Player is moving toward the platform edge horizontally
 */
function checkLedgeGrab() { }
```

## Submitting Changes

### Commit Messages

Use clear, descriptive commit messages:

```bash
# Good commit messages
git commit -m "Add double jump power-up mechanic"
git commit -m "Fix ledge grab not triggering when jumping upward"
git commit -m "Increase air control from 0.3 to 0.8 for easier platforming"

# Bad commit messages (too vague)
git commit -m "fixes"
git commit -m "update"
git commit -m "changes"
```

**Format**:
```
<type>: <subject>

<body (optional)>
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `refactor`: Code refactoring
- `style`: Visual/styling changes
- `docs`: Documentation updates
- `perf`: Performance improvements
- `test`: Adding tests

**Example**:
```
feat: Add enemy patrol AI

Implemented basic enemy that patrols between two points.
Enemies reverse direction at platform edges and damage
the player on contact, resetting the level.
```

### Pull Request Process

1. **Update documentation** if you've changed functionality
2. **Test your changes** thoroughly
3. **Push your branch** to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```
4. **Open a Pull Request** on GitHub
5. **Fill out the PR template** with:
   - Description of changes
   - Why the change is needed
   - How to test the changes
   - Screenshots/GIFs if visual changes

### PR Template

```markdown
## Description
Brief description of what this PR does

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Refactoring
- [ ] Documentation update

## Changes Made
- Added X feature
- Fixed Y bug
- Improved Z performance

## Testing
How to test these changes:
1. Open index.html
2. Jump to platform X
3. Verify behavior Y

## Screenshots
(If applicable)

## Checklist
- [ ] Code follows project style guidelines
- [ ] Tested in multiple browsers
- [ ] No console errors
- [ ] Documentation updated (if needed)
- [ ] All movement mechanics still work
```

## What to Contribute

### High Priority
- Bug fixes
- Performance improvements
- Browser compatibility fixes
- Physics tuning improvements
- Documentation improvements

### Feature Ideas
See [DEVELOPMENT.md](DEVELOPMENT.md) roadmap for planned features:

**Good first contributions**:
- Add more levels
- Create new hazards (moving spikes, fire, etc.)
- Add collectible items
- Implement a simple checkpoint system
- Add particle effects for jumps/landings

**Intermediate**:
- Enemy AI
- Moving platforms
- Level transition animations
- Sound effects integration
- Sprite-based graphics

**Advanced**:
- Combat system
- Boss fights
- Level editor
- Save system
- Speedrun timer with splits

## Code Review Process

1. **Automated checks**: None currently (but test manually!)
2. **Peer review**: At least one maintainer will review
3. **Testing**: Reviewer will test the changes
4. **Feedback**: You may be asked to make changes
5. **Merge**: Once approved, changes will be merged

## Questions or Problems?

- Check [DEVELOPMENT.md](DEVELOPMENT.md) for technical details
- Check [README.md](README.md) for general information
- Open an issue on GitHub for bugs or feature requests
- Tag maintainers in your PR if you need help

## Recognition

Contributors will be recognized in:
- GitHub contributors page
- Future CREDITS.md file (once we have multiple contributors)

## License

By contributing, you agree that your contributions will be licensed under the same license as the project.

---

Thank you for contributing to Hugo's Dungeon! Your efforts help make this game better for everyone.
