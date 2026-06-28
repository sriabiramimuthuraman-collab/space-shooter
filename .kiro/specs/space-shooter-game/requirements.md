# Requirements Document

## Introduction

Space Shooter is a complete browser-based arcade game delivered as a single self-contained `index.html` file. It features a cartoon rocket player ship, three tiers of asteroid enemies, power-ups, a full account system with per-user highscores and leaderboards, ship skin unlocks, a cinematic story intro, onboarding tutorial, synthesized background music and sound effects, and a polished neon-themed UI — all implemented with inline CSS, the Canvas 2D API, the Web Audio API, and the Web Speech API. No external libraries, images, or audio files are used.

---

## Glossary

- **Game**: The overall browser-based Space Shooter application.
- **Canvas**: The 800×600 HTML5 canvas element where gameplay is rendered.
- **Player**: The user-controlled rocket ship.
- **Asteroid**: The basic (tier-1) enemy that falls straight down.
- **Big Asteroid**: The mid-tier (tier-2) comet-styled enemy that takes two hits.
- **Boss Asteroid**: The large (tier-3) boss enemy that takes three hits.
- **Bullet**: A projectile fired upward by the Player.
- **Power-up**: A collectible pickup that grants a temporary or one-time benefit.
- **Skin**: A recolored variant of the Player ship shape, unlocked by highscore milestones.
- **User**: An account identified by a username and password stored in localStorage.
- **Highscore**: The highest score ever achieved by a User, persisted in localStorage.
- **Leaderboard**: A sorted list of all Users and their Highscores.
- **HUD**: The in-game heads-up display showing score, highscore, lives, and pause button.
- **AudioEngine**: The Web Audio API subsystem responsible for all sound synthesis.
- **SpeechEngine**: The Web Speech API subsystem responsible for spoken voice lines.
- **Level**: A progression milestone reached when the score crosses a threshold, triggering a level-up overlay and increased difficulty.
- **GameLoop**: The `requestAnimationFrame`-driven update-and-render cycle.
- **Particle**: A short-lived visual element spawned on enemy destruction or life loss.
- **Command Deck**: A settings/navigation panel accessible from the game-over screen.
- **Tutorial**: A brief training mini-game shown during onboarding before the main game.

---

## Requirements

### Requirement 1: Page Layout and Canvas

**User Story:** As a player, I want a clean dark page with a clearly visible game canvas, so that the game is easy to focus on.

#### Acceptance Criteria

1. THE Game SHALL render the page with a dark background color.
2. THE Game SHALL display a centered "Space Shooter" heading above the Canvas.
3. THE Canvas SHALL be 800 pixels wide and 600 pixels tall, centered on the page, with a black fill.

---

### Requirement 2: Player Ship Visuals

**User Story:** As a player, I want a detailed cartoon rocket ship, so that the game feels polished and fun.

#### Acceptance Criteria

1. THE Player SHALL be drawn as a cartoon rocket with a pointed nose cone, a rounded silver-gray mid-body, a large circular porthole window near the front with a lighter inset glass-reflection highlight, a pair of swept-back fin shapes flanking the rear engine, and small rivet detail dots on the body.
2. THE Player SHALL display a soft yellow-orange-to-transparent gradient engine flame trailing beneath the ship body.
3. THE Player's default color scheme SHALL apply cyan/blue accents to the fins and window trim.
4. WHEN a Skin is selected, THE Player SHALL render using that Skin's accent colors on the fins and window trim while keeping the same rocket shape.

---

### Requirement 3: Player Movement

**User Story:** As a player, I want responsive horizontal movement, so that I can dodge asteroids skillfully.

#### Acceptance Criteria

1. WHEN the left arrow key is held, THE Player SHALL move left at a constant speed.
2. WHEN the right arrow key is held, THE Player SHALL move right at a constant speed.
3. THE Player's horizontal movement speed SHALL remain constant and SHALL NOT be affected by score, difficulty level, or any power-up.
4. THE Player SHALL remain within the horizontal bounds of the Canvas at all times.
5. THE GameLoop SHALL drive all Player position updates via `requestAnimationFrame`.

---

### Requirement 4: Shooting

**User Story:** As a player, I want to fire bullets at asteroids, so that I can destroy them and score points.

#### Acceptance Criteria

1. WHEN the spacebar is pressed and the shot cooldown has elapsed, THE Player SHALL fire one Bullet upward from the tip of the ship.
2. THE Bullet SHALL travel continuously upward and be removed when it exits the top edge of the Canvas.
3. THE Game SHALL enforce a minimum 250 ms cooldown between consecutive Bullet firings.
4. THE AudioEngine SHALL NOT play any sound when a Bullet is fired.
5. WHILE the game is paused, THE Player SHALL NOT fire Bullets.

---

### Requirement 5: Asteroid Enemy System

**User Story:** As a player, I want three distinct tiers of asteroid enemies, so that the game has varied challenge and visual interest.

#### Acceptance Criteria

1. THE Asteroid SHALL be drawn as a roughly circular lumpy gray rock with darker gray irregular crater patches for texture, and SHALL display no flame or trail.
2. WHEN an Asteroid reaches the bottom of the Canvas, THE Game SHALL remove the Asteroid and deduct one life from the Player.
3. WHEN a Bullet hits an Asteroid, THE Game SHALL remove the Bullet, destroy the Asteroid, award 10 points, and trigger an orange/red Particle burst.
4. THE Big Asteroid SHALL be drawn as a larger comet with a bright yellow-white glowing core, orange-red layered rings, and a flame-like tail trailing upward made of overlapping translucent teardrop/triangle gradient shapes.
5. THE Big Asteroid SHALL require exactly 2 Bullet hits to be destroyed and SHALL award 20 points on destruction, triggering an orange/red Particle burst.
6. WHEN a Big Asteroid reaches the bottom of the Canvas, THE Game SHALL remove the Big Asteroid and deduct one life from the Player.
7. THE Boss Asteroid SHALL be drawn as a large dark brown/red cratered spherical core with an intense jagged orange-red flame corona radiating outward using layered translucent shapes.
8. THE Boss Asteroid SHALL require exactly 3 Bullet hits to be destroyed and SHALL award 50 points on destruction, triggering a purple Particle burst.
9. WHEN the Boss Asteroid is destroyed, THE Game SHALL grant the Player 2 additional lives, capping total lives at 5.
10. WHEN a Boss Asteroid reaches the bottom of the Canvas, THE Game SHALL remove it and deduct one life from the Player.
11. THE Game SHALL spawn Big Asteroids mixed in with regular Asteroids beginning at Level 2.
12. WHEN the game is at Level 3 or higher, THE Game SHALL reduce the Big Asteroid's movement speed independently of the general difficulty curve so it remains common but slower.
13. THE Boss Asteroid SHALL appear after every 6th Bullet-hit kill, but NOT before 15 seconds have elapsed since the start of the run.
14. WHEN a Bullet hits any Asteroid tier, THE Game SHALL remove the Bullet and decrement that Asteroid's remaining hit points by 1; IF hit points reach 0, THE Game SHALL destroy the Asteroid and apply score and Particle effects.

---

### Requirement 6: Particle Effects

**User Story:** As a player, I want visual feedback on destruction and life loss, so that the game feels impactful.

#### Acceptance Criteria

1. WHEN a regular Asteroid or Big Asteroid is destroyed, THE Game SHALL spawn small orange/red Particle dots that explode outward and fade.
2. WHEN the Boss Asteroid is destroyed, THE Game SHALL spawn small purple Particle dots that explode outward and fade.
3. WHEN the Player loses a life due to an Asteroid reaching the bottom, THE Game SHALL visually break the corresponding heart icon into two halves and burst a small red Particle effect from the heart's position.

---

### Requirement 7: Score and Highscore

**User Story:** As a player, I want to see my score and personal highscore, so that I have a goal to beat.

#### Acceptance Criteria

1. THE Game SHALL display the current score in white text at the top-left of the Canvas.
2. THE Game SHALL display the current User's highscore just below the score, labeled with the username, in white text at the top-left of the Canvas.
3. THE Highscore SHALL be persisted per User in localStorage and SHALL survive page reloads.
4. WHEN the game ends and the final score exceeds the saved Highscore, THE Game SHALL display "NEW HIGH SCORE!" text, play a short triumphant sound effect, and save the new Highscore to localStorage.
5. WHEN a Score Multiplier power-up is active, THE Game SHALL double all points awarded to the Player for its duration.

---

### Requirement 8: Lives System

**User Story:** As a player, I want to see my remaining lives as illustrated hearts, so that I understand my health at a glance.

#### Acceptance Criteria

1. THE Player SHALL start each game with exactly 3 lives.
2. THE Game SHALL display the Player's current lives as illustrated red heart icons at the top-right of the Canvas.
3. WHEN the Player loses a life, THE Game SHALL remove one heart icon and trigger the heart-break Particle effect.
4. WHEN the Boss Asteroid is destroyed, THE Game SHALL add 2 lives to the Player's total, capping the total at 5 lives.
5. WHEN the Player collects an Extra Life power-up, THE Game SHALL add 1 life, capping the total at 5 lives.

---

### Requirement 9: Game Over and Restart

**User Story:** As a player, I want to clearly see when the game ends and be able to restart quickly, so that replaying is frictionless.

#### Acceptance Criteria

1. WHEN the Player's lives reach 0, THE Game SHALL stop the GameLoop and display "GAME OVER" in large red text centered on the Canvas, the final score, and "Press R to Restart".
2. WHEN the R key is pressed on the game-over screen, THE Game SHALL reset all game state (score, lives, asteroids, bullets, power-ups, level) to Level 1 defaults and resume the GameLoop.
3. THE Game SHALL display two buttons on the game-over screen: "Instructions for Pilot" and "Command Deck".

---

### Requirement 10: Background Visuals

**User Story:** As a player, I want a dynamic animated starfield and per-level backgrounds, so that the game world feels alive and progressing.

#### Acceptance Criteria

1. THE Game SHALL render white star dots across the Canvas, each drifting slowly in its own random direction and speed, wrapping around when exiting any edge.
2. EACH star SHALL pulse its opacity independently via its own random cycle, creating an unsynchronized twinkling effect.
3. THE Game SHALL apply a per-level gradient background: Level 1 deep navy/indigo, Level 2 deep teal with soft nebula glow, Level 3 purple with denser nebula and larger twinkling stars, Level 4 amber/orange with sparse faint drifting rock silhouettes, Level 5+ deep red; the palette SHALL cycle every 5 levels.
4. WHEN a level-up transition occurs, THE Game SHALL crossfade the background gradient over a short duration.
5. ALL background gradients SHALL remain dark and low-contrast enough that the Player, Asteroids, Bullets, and HUD text remain clearly readable at all times.

---

### Requirement 11: Difficulty Pacing and Leveling

**User Story:** As a player, I want difficulty to increase gradually and level-ups to feel rewarding, so that the game stays engaging without becoming suddenly impossible.

#### Acceptance Criteria

1. THE Game SHALL increase enemy movement speed and spawn rate as a smooth continuous function of score and elapsed time, with no sudden jumps.
2. THE Game SHALL cap the maximum enemy speed and spawn rate at a sensible limit so late-game remains playable.
3. THE Game SHALL store the points-per-level threshold as a single named constant set to 150 points.
4. WHEN the score crosses a level threshold, THE Game SHALL display a level-up overlay, freeze all on-screen objects (Asteroids, Big Asteroids, Boss Asteroid, Bullets, active Power-ups) to zero velocity, and speak the new level number aloud via the SpeechEngine.
5. THE level-up overlay freeze SHALL persist for the full duration of the voice line, plus the overlay fade-out animation, plus a 1-second wait after the fade-out.
6. WHEN the level-up overlay fully completes (voice done, fade done, 1-second wait done), THE Game SHALL resume movement and apply the new level's updated speed to previously frozen objects, and resume spawning at the new level's rate.
7. THE Player's movement speed SHALL NOT be affected by the level or difficulty system.

---

### Requirement 12: Power-ups

**User Story:** As a player, I want collectible power-ups that grant temporary abilities, so that gameplay has exciting surprises and strategic depth.

#### Acceptance Criteria

1. WHEN a regular Asteroid or Big Asteroid is destroyed, THE Game SHALL apply a small random chance to spawn a Power-up at that position, falling slowly downward; Boss Asteroids SHALL NOT drop Power-ups.
2. WHEN the Player's ship overlaps a Power-up, THE Game SHALL collect it and apply its effect immediately.
3. THE Shield power-up (electric blue #3399FF, bubble icon) SHALL grant approximately 6 seconds of invincibility with a pulsing blue glow around the Player ship; WHILE the Shield is active, THE Player SHALL NOT lose a life from Asteroid collisions.
4. THE Rapid Fire power-up (orange #FF9900, lightning icon) SHALL reduce the Bullet firing cooldown sharply for 5 to 8 seconds.
5. THE Spread Shot power-up (green #33FF66, fan icon) SHALL cause the Player to fire 3 Bullets in a spread fan for its duration.
6. THE Extra Life power-up (pink #FF66CC, heart icon) SHALL be a rare drop that adds 1 life to the Player, capped at 5.
7. THE Time Slow power-up (indigo #6666FF, hourglass icon) SHALL cause all enemies to move at half speed for its duration.
8. THE Score Multiplier power-up (gold #FFDD33, star icon) SHALL double all points awarded to the Player for approximately 8 to 10 seconds.
9. THE Screen Clear power-up (red core #FF3333 with white glow, bomb icon) SHALL instantly destroy all on-screen enemies and trigger their respective explosion Particle effects; this power-up is one-time use and has no duration.
10. WHEN a Power-up is collected, THE Game SHALL display a glowing text popup near the top-center of the Canvas in the power-up's accent color (e.g. "SHIELD ACTIVATED!") that fades after approximately 1.5 to 2 seconds.
11. WHEN a Power-up is collected, THE SpeechEngine SHALL speak the power-up's activation line, queued after any currently-speaking line.

---

### Requirement 13: Pause System

**User Story:** As a player, I want to pause and resume the game at any time, so that I can take breaks without losing progress.

#### Acceptance Criteria

1. THE Game SHALL display a visible Pause button at the top-center of the Canvas during active gameplay only.
2. WHEN the P key or Escape key is pressed during gameplay, THE Game SHALL toggle the pause state.
3. WHEN the Pause button is clicked during gameplay, THE Game SHALL enter the paused state.
4. WHILE the game is paused, THE Game SHALL freeze all enemy movement, spawning, Bullet movement, Power-up timers, and the level/difficulty clock.
5. WHILE the game is paused, THE Game SHALL display a "PAUSED" overlay and a Resume button.
6. WHILE the game is paused, THE Player SHALL NOT be able to move or fire.
7. WHEN the game is resumed (via Resume button, P key, or Escape key), THE Game SHALL continue from the exact state it was in when paused.

---

### Requirement 14: Sound and Music

**User Story:** As a player, I want atmospheric background music and satisfying sound effects, so that the game feels immersive.

#### Acceptance Criteria

1. WHEN a Bullet hits an Asteroid, THE AudioEngine SHALL play a short impact sound effect.
2. WHEN the game ends, THE AudioEngine SHALL play a game-over sound effect.
3. THE AudioEngine SHALL NOT play any sound when a Bullet is fired.
4. THE Game SHALL play a synthesized retro synthwave background music loop with a steady two-note bass arpeggio in a minor key, a slow atmospheric pad chord progression, and a light delayed-echo layer using slightly-delayed quieter oscillator copies.
5. THE background music SHALL begin playing from the story intro screen and SHALL continue without restarting through all subsequent screens and game restarts.
6. THE background music volume SHALL be mixed quieter than sound effects so it remains atmospheric.
7. WHEN the game is paused, THE AudioEngine SHALL pause background music playback.
8. WHEN the game is resumed, THE AudioEngine SHALL resume background music from where it was paused.
9. THE background music SHALL stop and reset ONLY when the User logs out or switches User.
10. THE Game SHALL provide a Music Volume slider (0–100%) and an SFX Volume slider (0–100%) in the Command Deck, both live-updating and persisted per User in localStorage, defaulting to 70% for new Users.

---

### Requirement 15: Account System

**User Story:** As a player, I want a personal account with login and registration, so that my scores and settings are saved to my profile.

#### Acceptance Criteria

1. THE Game SHALL display a polished login screen with a neon glow theme before gameplay.
2. THE Game SHALL display a create-account screen requiring username, password, and confirm-password fields.
3. WHEN the username entered during account creation already exists in localStorage, THE Game SHALL display "Username already exists, try a different one" and suggest a few auto-generated alternative usernames (e.g. by appending numbers).
4. WHEN passwords do not match during account creation, THE Game SHALL display an appropriate validation error.
5. WHEN a User successfully logs in or creates an account, THE Game SHALL display a brief "Hello, {username}!" message before proceeding to the next screen.
6. THE Game SHALL persist all account data (username, hashed or plain password, highscore, settings) in localStorage.
7. THE Command Deck SHALL include a Switch User option that returns the User to the login screen and stops/resets the background music.
8. THE Command Deck SHALL include a Delete Account option with a confirmation step; WHEN confirmed, THE Game SHALL remove the User's data from localStorage and return to the login screen.

---

### Requirement 16: Story Intro

**User Story:** As a player, I want a cinematic story intro, so that the game has an atmospheric narrative hook.

#### Acceptance Criteria

1. WHEN the Game first loads, THE Game SHALL display a one-time cinematic intro on an animated starfield background with scrolling/fading story text (e.g. "Year 2157. A fractured asteroid belt is raining rock and fire across the outer colonies. Pilots are running out. You're the next one up.").
2. THE SpeechEngine SHALL speak the story text aloud during the intro.
3. THE Game SHALL display a visible "Skip Intro" button throughout the story intro.
4. WHEN the "Skip Intro" button is clicked, THE Game SHALL immediately cancel the story text and transition to the login screen.
5. WHEN the story text finishes, THE Game SHALL fade into the login screen.

---

### Requirement 17: Onboarding Flow

**User Story:** As a new player, I want guided onboarding before my first game, so that I understand the controls and mechanics before being thrown into combat.

#### Acceptance Criteria

1. THE Game SHALL follow this onboarding order: Story Intro → Login/Create Account → "Hello, {username}!" message → Rules/Instructions page → Tutorial → Ship Skin Selection → "Play" button → Level 1 intro overlay → gameplay.
2. THE Rules/Instructions page SHALL explain controls, asteroid tiers, lives, and power-ups, and SHALL include a "Continue" button to proceed.
3. THE Tutorial SHALL be a mini-game with slow-speed basic Asteroids only, no life loss, a visible counter "Training: {n}/5 destroyed", a visible "Skip Tutorial" button, and SHALL end when 5 Asteroids are destroyed or 25 seconds have elapsed.
4. WHEN the Tutorial ends, THE Game SHALL display "Training Complete!" before proceeding to the Ship Skin Selection screen.
5. THE Ship Skin Selection screen SHALL display all 8 skins; locked skins SHALL appear grayed out with their unlock point requirement shown; WHEN the Player selects a skin, THE Game SHALL apply it to the Player ship.
6. THE Ship Skin Selection screen SHALL include a "Play" button that starts the game with the selected skin.

---

### Requirement 18: Game-Over Screen Actions

**User Story:** As a player, I want navigation options after dying, so that I can quickly replay, review instructions, or access my profile.

#### Acceptance Criteria

1. THE game-over screen SHALL display an "Instructions for Pilot" button that reopens the Rules/Instructions page content in a read-only view.
2. THE game-over screen SHALL display a "Command Deck" button that opens the Command Deck panel.
3. THE Command Deck SHALL contain: Ship Skin Selection, a Leaderboard button, Switch User, Delete Account, Music Volume slider, and SFX Volume slider.

---

### Requirement 19: Skin Unlocks

**User Story:** As a player, I want to unlock new ship appearances by achieving higher scores, so that progression feels rewarding beyond just the score number.

#### Acceptance Criteria

1. THE Game SHALL provide 8 ship skins: Cyan (default, always unlocked), Red (500 pts), Green (1000 pts), Gold (1500 pts), Pink (2000 pts), Teal (2500 pts), Orange (3000 pts), Violet (3500 pts).
2. THE Game SHALL unlock a Skin permanently for a User when their Highscore reaches or exceeds that Skin's point threshold.
3. WHEN a locked Skin is displayed on the Skin Selection screen, THE Game SHALL show a lock indicator and the required Highscore threshold.
4. THE Game SHALL persist Skin unlock state per User in localStorage.

---

### Requirement 20: Leaderboard

**User Story:** As a player, I want to see how I rank against other users, so that I have competitive motivation.

#### Acceptance Criteria

1. THE Game SHALL display a Leaderboard accessible from the Command Deck that lists all registered Users and their Highscores, sorted in descending order by Highscore.
2. THE Leaderboard SHALL read all User Highscore data from localStorage at the time it is opened.

---

### Requirement 21: UI Polish and Button Interactions

**User Story:** As a player, I want smooth, responsive button interactions throughout the UI, so that the game feels professional and satisfying to navigate.

#### Acceptance Criteria

1. ALL buttons across every screen SHALL lift slightly (translateY upward) and glow (box-shadow in their accent color) on mouse hover, with smooth CSS transitions.
2. ALL button hover effects SHALL revert smoothly on mouse-out.
3. THE login, create-account, and story-intro screens SHALL use a neon glow visual theme.
4. Ship skins SHALL NOT be shown on the login or create-account screens.
