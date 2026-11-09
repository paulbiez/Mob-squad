Act as a Senior Game Developer. Build a complete, playable 2D "Mob Control"-style game in a single HTML file using only HTML, CSS, and Vanilla JavaScript on an HTML5 <canvas>. No libraries or external assets; draw everything with canvas primitives and gradients.

## Layout & Visuals (portrait, 9:16)
- Canvas scales to fit the screen, keeping a 9:16 logical resolution (e.g. 540x960) with devicePixelRatio support.
- Top-down view with a subtle 2.5D tilt: soft drop shadows under units, gradient lane surface, slight perspective feel on structures.
- Three vertical zones:
  1. Left side structure: a column of blue "+1" pads (decorative collectors).
  2. Center lane: the playable battlefield, bordered by stone/metal rails.
  3. Right side structure: tall clear tubes that fill with gold coins.
- Colors: player units blue, regular enemies red, bosses large and yellow with a red HP bar and bold white numeric HP overhead.
- Cartoony style: rounded bodies, small eyes, flame-shaped red enemies, bouncy walk animation.

## Controls & Shooting
- A cluster of blue cannons sits at the bottom of the lane.
- mousemove, touchstart and touchmove (with preventDefault) slide the cannons horizontally with smooth lerp easing, clamped to the lane edges.
- Auto-fire: every ~120ms, launch 1-2 small blue units upward with slight random spread. Add a muzzle flash and recoil.

## Moving Multiplier Gates
- Gates (e.g. "x3", "x2") span part of the lane width and oscillate with x = centerX + Math.sin(time * speed) * amplitude.
- When a blue unit overlaps a gate, spawn (multiplier - 1) clones at the unit's position with a small random offset.
- Each unit has a `passedGates` Set or flag so it can only be multiplied once per gate, and clones inherit the flag so they can't re-trigger the same gate.
- Gates glow briefly on trigger and may sit at different Y positions.

## Enemies
- Red units march downward toward the cannons in dense formation, with random wave spawns that scale in size over time.
- Bosses are large yellow brutes with HP (e.g. 150, scaling per wave) that walk slowly downward, surrounded by red units.

## Swarm Physics & Combat
- Soft separation force between same-color units, so they spread out like a fluid crowd:
  - Use a spatial hash grid (cell size ≈ 2x unit radius) so neighbor checks avoid O(n²).
  - Cap the push force and apply damping to avoid jitter.
- Blue vs red: on contact, both are destroyed (with a small pop particle).
- Blue vs boss: the blue unit is consumed and boss HP drops by 1. The boss is removed only at 0 HP, with a larger burst and a coin reward.
- Blue units that leave the top of the lane are removed.

## Progression, Win & Lose
- Each kill adds coins to the right-hand tubes (animated fill level and a counter, e.g. "70"). Bosses give a big bonus.
- Lose: any red unit or boss reaches the cannon line. Show "Game Over" with score and a restart button.
- Win a wave by clearing all enemies, then start a harder one (show wave number).

## Performance & Code Structure
- requestAnimationFrame loop with delta-time (clamped to ~33ms) so speed is frame-rate independent.
- Entity arrays for blueUnits, redUnits, bosses, gates, particles. Mark `dead = true` during updates and filter dead entries once at the end of each frame.
- Use object pooling for units and particles to reduce garbage collection.
- Draw units in batches and avoid per-frame allocations (no new objects in hot loops).
- Target a steady 60 FPS with 500+ entities.
- Organize the code into clear sections: config constants, input, entities, physics, collision, rendering, game state. Comment key mechanics.

Deliver the complete code in one HTML file, ready to open in a browser.
