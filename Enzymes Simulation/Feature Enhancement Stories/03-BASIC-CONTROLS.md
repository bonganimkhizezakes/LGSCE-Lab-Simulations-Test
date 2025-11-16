# Phase 3: Basic Controls

## Overview
This phase adds essential user controls for managing the simulation: reset functionality, simulation speed control, and camera navigation. These quality-of-life features improve the user experience significantly.

**Status:** ✅ Done
**Priority:** High
**Estimated Time:** 3-4 hours
**Dependencies:** Phase 1 MUST be complete

---

## Prerequisites

- [x] Phase 1 complete and tested
- [ ] Multiple enzymes and substrates working
- [ ] Statistics tracking functional
- [ ] 2D membrane implemented
- [ ] All molecules moving with physics

**Note:** Phase 2 is NOT required for this phase - can be done in parallel

---

## Features in This Phase

1. ✅ Reset Button
2. ✅ Simulation Speed Control
3. ✅ Camera Controls (Orbit, Pan, Zoom)

---

## Feature 1: Reset Button

### User Story
As a user, I want to start a fresh experiment without reloading the page, while keeping my current parameter settings.

### Acceptance Criteria
- [ ] "Reset Simulation" button visible in control panel
- [ ] Clicking reset:
  - Clears all statistics to 0
  - Resets all molecular states to 'free'
  - Randomizes all molecule positions
  - Resets all velocities to random initial values
  - Clears any bound enzyme-substrate complexes
  - Resets enzyme denaturation (if Phase 2 implemented)
  - Maintains all slider settings (temp, pH, counts)
- [ ] Button shows visual feedback on click
- [ ] Simulation continues running after reset (if not paused)
- [ ] Reset happens instantly (<100ms)

### Implementation Steps

1. **Add HTML reset button**
   ```html
   <div class="control-box">
       <h3>Simulation Controls</h3>
       <button id="toggleBtn" class="btn-pause">Pause Simulation</button>
       <button id="resetBtn" class="btn-reset">Reset Simulation</button>
   </div>
   ```

2. **Add CSS for reset button**
   ```css
   .btn-reset {
       width: 100%;
       padding: 12px;
       margin-top: 10px;
       background: #f59e0b;
       color: white;
       border: none;
       border-radius: 8px;
       font-size: 16px;
       font-weight: bold;
       cursor: pointer;
       transition: all 0.2s;
   }
   
   .btn-reset:hover {
       background: #d97706;
       transform: translateY(-2px);
   }
   
   .btn-reset:active {
       transform: scale(0.98);
       background: #b45309;
   }
   ```

3. **Create reset function**
   ```javascript
   function resetSimulation() {
       // Reset statistics
       collisionCount = 0;
       successfulBindings = 0;
       productCount = 0;
       if (typeof failedBindings !== 'undefined') failedBindings = 0;
       if (typeof denaturedCount !== 'undefined') denaturedCount = 0;
       
       // Reset enzymes
       enzymes.forEach(enzyme => {
           // Reset state
           enzyme.isBound = false;
           enzyme.boundSubstrate = null;
           
           // Reset denaturation if Phase 2 implemented
           if (enzyme.denatured !== undefined) {
               enzyme.denatured = false;
               enzyme.extremeConditionTimer = 0;
           }
           
           // Randomize position
           enzyme.mesh.position.set(
               (Math.random() - 0.5) * 18,
               (Math.random() - 0.5) * 13,
               0
           );
           
           // Randomize velocity
           enzyme.velocity.set(
               (Math.random() - 0.5) * 0.5,
               (Math.random() - 0.5) * 0.5,
               0
           );
           
           // Reset appearance
           enzyme.mesh.material.color.setHex(0x3b82f6);
           enzyme.mesh.rotation.set(0, 0, 0);
           
           const activeSite = enzyme.mesh.children[0];
           if (activeSite) {
               activeSite.material.color.setHex(0xff6b9d);
               activeSite.material.emissiveIntensity = 0.3;
           }
       });
       
       // Reset substrates
       substrates.forEach(substrate => {
           substrate.state = 'free';
           
           // Original color
           if (substrate.isCorrect) {
               substrate.mesh.material.color.setHex(0x10b981); // Green
           } else {
               substrate.mesh.material.color.setHex(0xef4444); // Red
           }
           
           // Randomize position
           substrate.mesh.position.set(
               (Math.random() - 0.5) * 18,
               (Math.random() - 0.5) * 13,
               0
           );
           
           // Randomize velocity
           substrate.velocity.set(
               (Math.random() - 0.5) * 2,
               (Math.random() - 0.5) * 2,
               0
           );
           
           // Reset rotation
           substrate.mesh.rotation.set(0, 0, 0);
       });
       
       // Update UI immediately
       updateStatistics();
       
       console.log('Simulation reset');
   }
   ```

4. **Create updateStatistics utility function**
   ```javascript
   function updateStatistics() {
       collisionsEl.textContent = collisionCount;
       bindingsEl.textContent = successfulBindings;
       productsEl.textContent = productCount;
       
       // Update Phase 2 stats if they exist
       if (typeof failedBindingsEl !== 'undefined' && failedBindingsEl) {
           failedBindingsEl.textContent = failedBindings;
       }
       if (typeof denaturedEl !== 'undefined' && denaturedEl) {
           denaturedEl.textContent = denaturedCount;
       }
   }
   ```

5. **Add event listener**
   ```javascript
   const resetBtn = document.getElementById('resetBtn');
   resetBtn.addEventListener('click', () => {
       // Visual feedback
       resetBtn.style.transform = 'scale(0.95)';
       setTimeout(() => {
           resetBtn.style.transform = 'scale(1)';
       }, 100);
       
       resetSimulation();
   });
   ```

6. **Update animation loop statistics update**
   ```javascript
   // Replace individual updates with:
   if (frameCount % 30 === 0) {
       updateStatistics();
   }
   ```

### Testing Checklist
- [ ] Button is visible and styled correctly
- [ ] Clicking button resets all statistics to 0
- [ ] All molecules get new random positions
- [ ] All molecules get new velocities
- [ ] No bound complexes remain after reset
- [ ] Enzyme colors return to blue
- [ ] Product molecules become free substrates again
- [ ] Temperature slider maintains value
- [ ] pH slider maintains value (if implemented)
- [ ] Molecule count sliders maintain values
- [ ] Can reset multiple times in succession
- [ ] Performance is instant
- [ ] No console errors
- [ ] Simulation continues running after reset

### Integration Notes
- Reset will be used by Phase 4 scenario presets
- Should work whether Phase 2 is implemented or not
- Consider adding confirmation dialog for extended version

### Definition of Done
- [ ] All acceptance criteria met
- [ ] All tests pass
- [ ] Button provides clear visual feedback
- [ ] Code handles all edge cases
- [ ] Function is well-commented

---

## Feature 2: Simulation Speed Control

### User Story
As a user, I want to slow down or speed up the simulation for better observation, independent of temperature.

### Acceptance Criteria
- [ ] "Simulation Speed" slider in control panel
- [ ] Range: 0.1x to 5.0x (default: 1.0x)
- [ ] Current speed displayed (e.g., "2.5x")
- [ ] Affects animation frame rate, not molecular properties
- [ ] Temperature still controls thermal motion intensity
- [ ] Pausing still freezes simulation completely
- [ ] Speed change is immediate and smooth
- [ ] No negative performance impacts at high speeds

### Implementation Steps

1. **Add HTML speed slider**
   ```html
   <div class="control-box">
       <h3>Simulation Speed: <span id="speedValue">1.0x</span></h3>
       <input type="range" id="speedSlider" min="0.1" max="5" step="0.1" value="1.0">
       <div class="temp-value">Slow down to observe details or speed up reactions</div>
   </div>
   ```

2. **Add speed variable and event listener**
   ```javascript
   let simulationSpeed = 1.0;
   const speedSlider = document.getElementById('speedSlider');
   const speedValue = document.getElementById('speedValue');
   
   speedSlider.addEventListener('input', (e) => {
       simulationSpeed = parseFloat(e.target.value);
       speedValue.textContent = simulationSpeed.toFixed(1) + 'x';
   });
   ```

3. **Update physics calculations with delta time**
   ```javascript
   // Define delta time based on speed
   const baseDeltaTime = 0.016; // ~60 FPS
   
   // In animation loop:
   if (isRunning) {
       const deltaTime = baseDeltaTime * simulationSpeed;
       
       // Update substrates
       substrates.forEach(substrate => {
           if (substrate.state === 'free') {
               applyThermalMotion(substrate, temperature);
               substrate.velocity.add(world.gravity.clone().multiplyScalar(deltaTime));
               substrate.velocity.multiplyScalar(world.damping);
               substrate.mesh.position.add(substrate.velocity.clone().multiplyScalar(deltaTime));
               checkMembraneBoundary(substrate, membranePoints);
               
               substrate.mesh.rotation.x += 0.01 * simulationSpeed;
               substrate.mesh.rotation.y += 0.01 * simulationSpeed;
           }
           // ... other states ...
       });
       
       // Update enzymes
       enzymes.forEach(enzyme => {
           if (!enzyme.isBound) {
               applyThermalMotion(enzyme, temperature);
               enzyme.velocity.add(world.gravity.clone().multiplyScalar(deltaTime));
               enzyme.velocity.multiplyScalar(world.damping);
               enzyme.mesh.position.add(enzyme.velocity.clone().multiplyScalar(deltaTime));
               checkMembraneBoundary(enzyme, membranePoints);
           }
           // ... rest of enzyme logic ...
       });
   }
   ```

4. **Update binding timer (if using setTimeout)**
   ```javascript
   // In handleCollision or binding function:
   const bindingDuration = 2000 / simulationSpeed; // Scale binding time
   
   setTimeout(() => {
       if (substrate.state === 'bound') {
           convertToProduct(substrate, enzyme);
       }
   }, bindingDuration);
   ```

5. **Update rotation animations**
   ```javascript
   // Scale all rotation speeds
   substrate.mesh.rotation.x += 0.01 * simulationSpeed;
   substrate.mesh.rotation.y += 0.01 * simulationSpeed;
   
   if (!enzyme.denatured) {
       enzyme.mesh.rotation.y += 0.005 * simulationSpeed;
   } else {
       // Denatured wobble
       enzyme.mesh.rotation.x += (Math.random() - 0.5) * 0.1 * simulationSpeed;
       enzyme.mesh.rotation.y += (Math.random() - 0.5) * 0.1 * simulationSpeed;
       enzyme.mesh.rotation.z += (Math.random() - 0.5) * 0.1 * simulationSpeed;
   }
   ```

6. **Update denaturation timer (if Phase 2 implemented)**
   ```javascript
   function checkDenaturationConditions(enzyme, temp, ph) {
       if (enzyme.denatured) return;
       
       const isExtremeTemp = temp > 45;
       const isExtremePH = ph < 3.0 || ph > 11.0;
       
       if (isExtremeTemp || isExtremePH) {
           // Increment timer scaled by speed
           enzyme.extremeConditionTimer += simulationSpeed;
           
           if (enzyme.extremeConditionTimer >= enzyme.denaturationThreshold) {
               denatureEnzyme(enzyme);
           }
       } else {
           enzyme.extremeConditionTimer = 0;
       }
   }
   ```

### Testing Checklist
- [ ] Slider moves smoothly from 0.1x to 5.0x
- [ ] Speed value displays correctly
- [ ] At 0.1x: simulation moves very slowly
- [ ] At 1.0x: normal speed (baseline)
- [ ] At 5.0x: simulation moves 5x faster
- [ ] Temperature effects still work correctly
- [ ] Speed and temperature are independent
- [ ] Binding still takes ~2 seconds (scaled)
- [ ] Denaturation timer scales appropriately
- [ ] No performance issues at 5.0x speed
- [ ] Pause button overrides speed (full stop)
- [ ] Resume returns to selected speed

### Integration Notes
- Speed control makes observation easier for education
- Useful for Phase 4 quiz mode (speed up to see results faster)
- Should not affect scientific accuracy of simulation

### Definition of Done
- [ ] All acceptance criteria met
- [ ] All tests pass
- [ ] Speed changes are smooth
- [ ] No physics glitches at extreme speeds
- [ ] Code is well-commented

---

## Feature 3: Camera Controls

### User Story
As a user, I want to view the simulation from different angles using intuitive mouse/trackpad controls.

### Acceptance Criteria
- [ ] OrbitControls integrated from Three.js
- [ ] Mouse controls:
  - Left click + drag: Rotate view around center
  - Right click + drag (or two-finger drag): Pan camera
  - Scroll wheel: Zoom in/out
- [ ] Camera cannot go inside cell membrane
- [ ] Camera cannot zoom too far away
- [ ] Smooth camera movement (damping enabled)
- [ ] "Reset Camera" button returns to default view
- [ ] Controls work during simulation (running or paused)
- [ ] Touch support for tablets/mobile

### Implementation Steps

1. **Include OrbitControls library**
   ```html
   <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
   ```

2. **Initialize OrbitControls**
   ```javascript
   // After camera and renderer setup
   const controls = new THREE.OrbitControls(camera, renderer.domElement);
   
   // Configure controls
   controls.enableDamping = true;  // Smooth movement
   controls.dampingFactor = 0.05;
   controls.screenSpacePanning = true;  // Pan parallel to screen
   controls.minDistance = 10;  // Can't zoom too close
   controls.maxDistance = 50;  // Can't zoom too far
   controls.maxPolarAngle = Math.PI / 2 + 0.5;  // Limit rotation below horizon
   controls.target.set(0, 0, 0);  // Look at center
   
   // Optional: Enable auto-rotate for demo
   // controls.autoRotate = false;
   // controls.autoRotateSpeed = 0.5;
   ```

3. **Store default camera position**
   ```javascript
   const defaultCameraPosition = {
       x: 0,
       y: 0,
       z: 25
   };
   
   const defaultCameraTarget = {
       x: 0,
       y: 0,
       z: 0
   };
   
   // Set initial position
   camera.position.set(
       defaultCameraPosition.x,
       defaultCameraPosition.y,
       defaultCameraPosition.z
   );
   ```

4. **Add reset camera button**
   ```html
   <div class="control-box">
       <h3>View Controls</h3>
       <button id="resetCameraBtn" class="btn-reset">Reset Camera</button>
       <div class="temp-value">
           🖱️ Left drag: Rotate<br>
           🖱️ Right drag: Pan<br>
           🖱️ Scroll: Zoom
       </div>
   </div>
   ```

5. **Implement reset camera function**
   ```javascript
   function resetCamera() {
       camera.position.set(
           defaultCameraPosition.x,
           defaultCameraPosition.y,
           defaultCameraPosition.z
       );
       
       controls.target.set(
           defaultCameraTarget.x,
           defaultCameraTarget.y,
           defaultCameraTarget.z
       );
       
       controls.update();
   }
   
   const resetCameraBtn = document.getElementById('resetCameraBtn');
   resetCameraBtn.addEventListener('click', resetCamera);
   ```

6. **Update controls in animation loop**
   ```javascript
   function animate() {
       requestAnimationFrame(animate);
       frameCount++;
       
       // Update orbit controls (always, even when paused)
       controls.update();
       
       if (isRunning) {
           // ... rest of animation logic ...
       }
       
       renderer.render(scene, camera);
   }
   ```

7. **Add touch support**
   ```javascript
   // OrbitControls handles touch by default, but verify:
   controls.touches = {
       ONE: THREE.TOUCH.ROTATE,      // One finger: rotate
       TWO: THREE.TOUCH.DOLLY_PAN    // Two fingers: zoom and pan
   };
   ```

8. **Handle window resize with controls**
   ```javascript
   window.addEventListener('resize', () => {
       const container = document.getElementById('simulation-container');
       camera.aspect = container.clientWidth / container.clientHeight;
       camera.updateProjectionMatrix();
       renderer.setSize(container.clientWidth, container.clientHeight);
       controls.update();
   });
   ```

9. **Optional: Add keyboard controls**
   ```javascript
   // Optional keyboard shortcuts
   document.addEventListener('keydown', (e) => {
       switch(e.key) {
           case 'r':
           case 'R':
               resetCamera();
               break;
           case ' ':
               // Space to pause/resume
               toggleBtn.click();
               break;
       }
   });
   ```

### Testing Checklist
- [ ] Left click and drag rotates view smoothly
- [ ] Right click and drag pans view
- [ ] Scroll wheel zooms in and out
- [ ] Cannot zoom inside cell membrane
- [ ] Cannot zoom too far away
- [ ] Camera movement has smooth damping
- [ ] Reset button returns to default view
- [ ] Controls work while simulation is running
- [ ] Controls work while simulation is paused
- [ ] On tablet: one finger rotates, two fingers pan/zoom
- [ ] Window resize maintains proper aspect ratio
- [ ] No console errors
- [ ] Performance impact is minimal

### Integration Notes
- Camera controls improve user experience significantly
- Important for observing collision details
- Phase 4 scenarios could set specific camera angles
- Consider adding preset camera angles in future

### Alternative Implementation
If OrbitControls library doesn't load:
- Implement basic mouse drag rotation manually
- Less features but more control over behavior
- Fallback to fixed camera view

### Definition of Done
- [ ] All acceptance criteria met
- [ ] All tests pass
- [ ] Controls are intuitive
- [ ] Help text clearly explains controls
- [ ] Works on desktop and touch devices
- [ ] Performance is smooth

---

## Phase 3 Integration Testing

### Integration Test Checklist
- [ ] Reset button works with all molecule counts
- [ ] Speed control works at all temperature settings
- [ ] Camera controls don't interfere with UI interactions
- [ ] All three features work together seamlessly
- [ ] Reset + Speed change + Camera move: all work
- [ ] Phase 1 features still work (movement, membrane, etc.)
- [ ] Phase 2 features still work if implemented (pH, denaturation)
- [ ] Statistics update correctly after reset
- [ ] Performance is stable with all controls active

---

## Phase 3 Definition of Done

- [ ] All 3 features implemented
- [ ] All tests pass
- [ ] Integration testing complete
- [ ] User experience is smooth and intuitive
- [ ] Help text is clear
- [ ] Code is committed and documented
- [ ] Demo prepared showing:
  - Reset functionality
  - Speed control at various speeds
  - Camera navigation
- [ ] Ready for Phase 4

---

## User Experience Notes

This phase significantly improves UX:
- **Reset** - No page reload needed for new experiments
- **Speed** - Observe slow details or speed up results
- **Camera** - View from best angle for understanding

Consider adding:
- Keyboard shortcuts (R for reset, Space for pause, etc.)
- Camera angle presets (Top, Side, Angled)
- Speed presets (0.5x, 1x, 2x buttons)

---

## Time Tracking

| Feature | Estimated | Actual | Notes |
|---------|-----------|--------|-------|
| Reset Button | 1 hour | | |
| Speed Control | 1 hour | | |
| Camera Controls | 1.5 hours | | |
| Integration Testing | 30 min | | |
| **Total** | **4 hours** | | |

---

**Next Phase:** Proceed to Phase 4 (Educational Features) - requires Phase 1 & 2