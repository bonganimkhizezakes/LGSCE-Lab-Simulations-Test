# Phase 2: Biochemistry Realism

## Overview
This phase adds realistic biochemical behavior including pH effects and enzyme denaturation. These features demonstrate how environmental conditions affect enzyme function.

**Status:** ✅ Done
**Priority:** High
**Estimated Time:** 4-5 hours
**Dependencies:** Phase 1 MUST be complete

---

## Prerequisites

- [x] Phase 1 complete and tested
- [ ] Temperature range extends to 50°C
- [ ] Multiple enzymes supported in `enzymes[]` array
- [ ] 2D membrane boundary working correctly
- [ ] All molecules moving with physics

---

## Features in This Phase

1. ✅ pH Slider (0-14) with activity effects
2. ✅ Enzyme denaturation (irreversible damage)

---

## Feature 1: pH Slider with Activity Effects

### User Story
As a student, I want to understand how pH affects enzyme activity and binding success rates.

### Acceptance Criteria
- [ ] pH slider with range 0-14 (default: 7.0, neutral)
- [ ] Current pH value displayed with one decimal place
- [ ] pH status label: "Acidic" (0-6.5), "Neutral" (6.5-7.5), "Basic" (7.5-14)
- [ ] Each enzyme has optimal pH range (default: 6.0-8.0)
- [ ] Binding success rate decreases outside optimal pH
- [ ] pH effects are gradual and realistic
- [ ] Enzyme color shifts based on pH (visual feedback)
- [ ] Statistics show failed binding attempts
- [ ] pH effects are reversible (until denaturation occurs)

### Scientific Background
- Most enzymes have optimal pH around 7 (neutral)
- pH affects charged amino acids in active site
- Extreme pH disrupts enzyme shape and function
- Effects are reversible if denaturation hasn't occurred

### Implementation Steps

1. **Add HTML pH slider**
   ```html
   <div class="control-box">
       <h3>pH Level: <span id="phValue">7.0</span> (<span id="phStatus">Neutral</span>)</h3>
       <input type="range" id="phSlider" min="0" max="14" step="0.5" value="7.0">
       <div class="temp-value">Optimal pH: 6.0 - 8.0</div>
   </div>
   ```

2. **Add pH variable and event listener**
   ```javascript
   let currentPH = 7.0;
   const phSlider = document.getElementById('phSlider');
   const phValue = document.getElementById('phValue');
   const phStatus = document.getElementById('phStatus');
   
   phSlider.addEventListener('input', (e) => {
       currentPH = parseFloat(e.target.value);
       phValue.textContent = currentPH.toFixed(1);
       
       // Update status
       if (currentPH < 6.5) {
           phStatus.textContent = 'Acidic';
           phStatus.style.color = '#ff6b6b';
       } else if (currentPH > 7.5) {
           phStatus.textContent = 'Basic';
           phStatus.style.color = '#a78bfa';
       } else {
           phStatus.textContent = 'Neutral';
           phStatus.style.color = '#10b981';
       }
   });
   ```

3. **Add pH properties to enzyme objects**
   ```javascript
   function createEnzyme(position) {
       // ... existing code ...
       const enzyme = {
           // ... existing properties ...
           optimalPH: 7.0,
           phTolerance: 1.5,  // ±1.5 from optimal
           denatured: false
       };
       return enzyme;
   }
   ```

4. **Calculate pH activity modifier**
   ```javascript
   function getPHActivityModifier(enzyme, currentPH) {
       if (enzyme.denatured) return 0; // Denatured enzymes don't work
       
       const deviation = Math.abs(currentPH - enzyme.optimalPH);
       
       if (deviation <= enzyme.phTolerance) {
           // Within optimal range: 100% activity
           return 1.0;
       } else {
           // Outside optimal: activity drops
           const excess = deviation - enzyme.phTolerance;
           const modifier = Math.max(0, 1.0 - (excess * 0.3));
           return modifier;
       }
   }
   ```

5. **Update collision handling to include pH check**
   ```javascript
   function handleCollision(substrate, enzyme) {
       collisionCount++;
       
       if (substrate.isCorrect && !enzyme.isBound && !enzyme.denatured) {
           const phModifier = getPHActivityModifier(enzyme, currentPH);
           
           // Random check: binding succeeds based on pH modifier
           if (Math.random() < phModifier) {
               // Successful binding
               successfulBindings++;
               substrate.state = 'bound';
               enzyme.isBound = true;
               enzyme.boundSubstrate = substrate;
               
               // Change color to yellow (bound state)
               substrate.mesh.material.color.setHex(0xfbbf24);
               
               // Stop substrate movement
               substrate.velocity.set(0, 0, 0);
               
               // Position at active site
               const bindPosition = enzyme.mesh.position.clone().add(new THREE.Vector3(1.8, 0, 0));
               substrate.mesh.position.copy(bindPosition);
               
               // Brighten active site
               if (enzyme.mesh.children[0]) {
                   enzyme.mesh.children[0].material.emissiveIntensity = 0.8;
               }
               
               // Convert to product after delay
               setTimeout(() => {
                   if (substrate.state === 'bound') {
                       convertToProduct(substrate, enzyme);
                   }
               }, 2000);
           } else {
               // Failed binding attempt
               failedBindings++;
               // Bounce substrate away
               const direction = new THREE.Vector3()
                   .subVectors(substrate.mesh.position, enzyme.mesh.position)
                   .normalize();
               substrate.velocity.copy(direction.multiplyScalar(3));
           }
       } else {
           // Wrong substrate or enzyme busy/denatured
           const direction = new THREE.Vector3()
               .subVectors(substrate.mesh.position, enzyme.mesh.position)
               .normalize();
           substrate.velocity.copy(direction.multiplyScalar(3));
       }
   }
   
   function convertToProduct(substrate, enzyme) {
       substrate.state = 'product';
       substrate.mesh.material.color.setHex(0xa78bfa); // Purple
       
       // Release from enzyme
       enzyme.isBound = false;
       enzyme.boundSubstrate = null;
       
       // Reset active site
       if (enzyme.mesh.children[0]) {
           enzyme.mesh.children[0].material.emissiveIntensity = 0.3;
       }
       
       // Give product velocity
       substrate.velocity.set(
           (Math.random() - 0.5) * 2,
           (Math.random() - 0.5) * 2,
           0
       );
       
       productCount++;
   }
   ```

6. **Add failed bindings statistic**
   ```html
   <!-- In HTML control panel -->
   <div class="stat">Failed Bindings: <span class="stat-value" id="failedBindings" style="color: #ef4444;">0</span></div>
   ```
   
   ```javascript
   let failedBindings = 0;
   const failedBindingsEl = document.getElementById('failedBindings');
   
   // Update in animation loop
   if (frameCount % 30 === 0) {
       // ... existing updates ...
       failedBindingsEl.textContent = failedBindings;
   }
   ```

7. **Visual pH feedback on enzymes**
   ```javascript
   function updateEnzymeAppearance(enzyme, currentPH) {
       if (enzyme.denatured) return; // Don't change denatured appearance
       
       const baseColor = new THREE.Color(0x3b82f6); // Blue
       const acidColor = new THREE.Color(0xff6b9d);  // Pink/red
       const basicColor = new THREE.Color(0xa78bfa); // Purple
       
       let targetColor;
       if (currentPH < 6.5) {
           // Acidic: blend toward red
           const factor = (6.5 - currentPH) / 6.5;
           targetColor = baseColor.clone().lerp(acidColor, factor * 0.5);
       } else if (currentPH > 7.5) {
           // Basic: blend toward purple
           const factor = (currentPH - 7.5) / 6.5;
           targetColor = baseColor.clone().lerp(basicColor, factor * 0.5);
       } else {
           // Neutral: normal blue
           targetColor = baseColor;
       }
       
       enzyme.mesh.material.color.copy(targetColor);
   }
   
   // Call in animation loop for each enzyme
   enzymes.forEach(enzyme => {
       updateEnzymeAppearance(enzyme, currentPH);
   });
   ```

### Testing Checklist
- [ ] pH slider moves smoothly from 0-14
- [ ] pH value displays correctly
- [ ] Status changes at correct thresholds (6.5 and 7.5)
- [ ] At pH 7.0: binding success rate is ~100%
- [ ] At pH 5.0: binding success rate drops noticeably
- [ ] At pH 3.0: binding success rate is very low (<20%)
- [ ] At pH 10.0: binding success rate drops
- [ ] Failed bindings are counted and displayed
- [ ] Enzyme color shifts with pH (subtle but visible)
- [ ] Returning pH to 7.0 restores full activity
- [ ] Multiple enzymes all affected by pH
- [ ] Denatured enzymes stay inactive regardless of pH

### Integration Notes
- pH will be used in Phase 4 for scenario presets
- pH extreme ranges will trigger denaturation in Feature 2
- Failed bindings add educational value (not all collisions succeed)

### Definition of Done
- [ ] All acceptance criteria met
- [ ] All tests pass
- [ ] pH effects are scientifically reasonable
- [ ] Visual feedback is clear
- [ ] Code is commented with scientific explanation

---

## Feature 2: Enzyme Denaturation

### User Story
As a student, I want to observe irreversible enzyme damage from extreme environmental conditions.

### Acceptance Criteria
- [ ] Enzymes denature under extreme conditions:
  - Temperature > 45°C for >5 seconds
  - pH < 3.0 for >5 seconds
  - pH > 11.0 for >5 seconds
- [ ] Denaturation is permanent and irreversible
- [ ] Denatured enzymes:
  - Turn gray/dark color
  - Active site becomes darker/inactive
  - Cannot bind substrates
  - Still collide and move but functionless
  - Wobble erratically (broken structure)
- [ ] Warning indicators when approaching denaturation:
  - Temperature slider background turns orange/red >42°C
  - pH slider background turns orange/red at extremes
- [ ] Statistic shows "Denatured Enzymes: X"
- [ ] Bound substrates are released if enzyme denatures

### Scientific Background
- High temperature breaks hydrogen bonds in protein structure
- Extreme pH disrupts ionic bonds and protein folding
- Denaturation is irreversible (protein can't refold)
- Structure determines function - denatured enzymes are non-functional

### Implementation Steps

1. **Add denaturation properties to enzymes**
   ```javascript
   function createEnzyme(position) {
       const enzyme = {
           // ... existing properties ...
           denatured: false,
           extremeConditionTimer: 0,
           denaturationThreshold: 300  // 5 seconds at 60fps
       };
       return enzyme;
   }
   ```

2. **Check extreme conditions each frame**
   ```javascript
   function checkDenaturationConditions(enzyme, temp, ph) {
       if (enzyme.denatured) return; // Already denatured
       
       const isExtremeTemp = temp > 45;
       const isExtremePH = ph < 3.0 || ph > 11.0;
       
       if (isExtremeTemp || isExtremePH) {
           enzyme.extremeConditionTimer++;
           
           if (enzyme.extremeConditionTimer >= enzyme.denaturationThreshold) {
               denatureEnzyme(enzyme);
           }
       } else {
           // Reset timer if conditions return to normal
           enzyme.extremeConditionTimer = 0;
       }
   }
   ```

3. **Denature enzyme function**
   ```javascript
   function denatureEnzyme(enzyme) {
       enzyme.denatured = true;
       denaturedCount++;
       
       // Visual changes
       enzyme.mesh.material.color.setHex(0x6b7280); // Gray
       enzyme.mesh.material.emissive = new THREE.Color(0x000000);
       
       // Active site becomes dark
       const activeSite = enzyme.mesh.children[0];
       if (activeSite) {
           activeSite.material.color.setHex(0x374151); // Dark gray
           activeSite.material.emissiveIntensity = 0;
       }
       
       // Release bound substrate if any
       if (enzyme.isBound && enzyme.boundSubstrate) {
           const substrate = enzyme.boundSubstrate;
           substrate.state = 'free';
           substrate.mesh.material.color.setHex(0x10b981); // Back to green
           substrate.velocity.set(
               (Math.random() - 0.5) * 3,
               (Math.random() - 0.5) * 3,
               0
           );
           enzyme.isBound = false;
           enzyme.boundSubstrate = null;
       }
       
       console.log('Enzyme denatured!');
   }
   ```

4. **Add denatured enzyme wobble animation**
   ```javascript
   function updateEnzymeAnimation(enzyme) {
       if (enzyme.denatured) {
           // Erratic wobble instead of smooth rotation
           enzyme.mesh.rotation.x += (Math.random() - 0.5) * 0.1;
           enzyme.mesh.rotation.y += (Math.random() - 0.5) * 0.1;
           enzyme.mesh.rotation.z += (Math.random() - 0.5) * 0.1;
       } else {
           // Normal smooth rotation
           enzyme.mesh.rotation.y += 0.005;
       }
   }
   ```

5. **Add warning indicators to UI**
   ```javascript
   // Add CSS for warning states
   const style = document.createElement('style');
   style.textContent = `
       .warning-extreme {
           background: #dc2626 !important;
           animation: pulse 1s infinite;
       }
       .warning-caution {
           background: #f59e0b !important;
       }
       @keyframes pulse {
           0%, 100% { opacity: 1; }
           50% { opacity: 0.7; }
       }
   `;
   document.head.appendChild(style);
   
   // Update slider backgrounds based on conditions
   function updateWarningIndicators(temp, ph) {
       const tempBox = tempSlider.closest('.control-box');
       const phBox = phSlider.closest('.control-box');
       
       // Temperature warnings
       if (temp > 45) {
           tempBox.classList.add('warning-extreme');
           tempBox.classList.remove('warning-caution');
       } else if (temp > 42) {
           tempBox.classList.add('warning-caution');
           tempBox.classList.remove('warning-extreme');
       } else {
           tempBox.classList.remove('warning-extreme', 'warning-caution');
       }
       
       // pH warnings
       if (ph < 3 || ph > 11) {
           phBox.classList.add('warning-extreme');
           phBox.classList.remove('warning-caution');
       } else if (ph < 4 || ph > 10) {
           phBox.classList.add('warning-caution');
           phBox.classList.remove('warning-extreme');
       } else {
           phBox.classList.remove('warning-extreme', 'warning-caution');
       }
   }
   ```

6. **Add denatured count statistic**
   ```html
   <div class="stat">Denatured Enzymes: <span class="stat-value" id="denatured" style="color: #6b7280;">0</span></div>
   ```
   
   ```javascript
   let denaturedCount = 0;
   const denaturedEl = document.getElementById('denatured');
   
   // Update in animation loop
   if (frameCount % 30 === 0) {
       denaturedEl.textContent = denaturedCount;
   }
   ```

7. **Update animation loop**
   ```javascript
   // In animate() function
   enzymes.forEach(enzyme => {
       checkDenaturationConditions(enzyme, temperature, currentPH);
       updateEnzymeAnimation(enzyme);
       
       // Update appearance if not denatured
       if (!enzyme.denatured) {
           updateEnzymeAppearance(enzyme, currentPH);
       }
       
       // Movement code...
   });
   
   // Update warnings
   updateWarningIndicators(temperature, currentPH);
   ```

8. **Prevent denatured enzymes from binding**
   - Already handled in `handleCollision()` with `!enzyme.denatured` check

### Testing Checklist
- [ ] Set temp to 48°C: enzymes denature after ~5 seconds
- [ ] Set pH to 2.0: enzymes denature after ~5 seconds
- [ ] Set pH to 12.0: enzymes denature after ~5 seconds
- [ ] Denatured enzymes turn gray
- [ ] Denatured enzymes wobble erratically
- [ ] Denatured enzymes cannot bind substrates
- [ ] Bound substrates release when enzyme denatures
- [ ] Warning indicators appear at 42°C and pH 4/10
- [ ] Extreme warnings appear at 45°C and pH 3/11
- [ ] Denatured count updates correctly
- [ ] Returning to normal conditions doesn't reverse denaturation
- [ ] Multiple enzymes can denature independently
- [ ] Timer resets if conditions return to normal before 5 seconds

### Integration Notes
- Denaturation is permanent - reset button (Phase 3) needed to recover
- Phase 4 scenarios will use denaturation as teaching tool
- Performance: denatured enzymes still move but don't check collisions for binding

### Definition of Done
- [ ] All acceptance criteria met
- [ ] All tests pass
- [ ] Denaturation is scientifically realistic
- [ ] Visual feedback is dramatic and clear
- [ ] Warning system helps users understand conditions
- [ ] Code is well-commented

---

## Phase 2 Integration Testing

### Integration Test Checklist
- [ ] pH and temperature work independently
- [ ] pH + temperature combined can denature faster
- [ ] Denatured enzymes show correct color regardless of pH
- [ ] Statistics all update correctly
- [ ] Failed bindings tracked separately from successful ones
- [ ] Multiple enzymes affected by global pH/temp
- [ ] Performance stable with 5+ enzymes
- [ ] Warning indicators don't interfere with controls
- [ ] All Phase 1 features still work correctly

---

## Phase 2 Definition of Done

- [ ] Both features implemented
- [ ] All tests pass
- [ ] Integration testing complete
- [ ] Scientific accuracy validated
- [ ] Code committed and documented
- [ ] Demo prepared showing:
  - pH effects on binding
  - Temperature denaturation
  - pH denaturation
  - Warning system
- [ ] Ready for Phase 3 or Phase 4

---

## Scientific Validation

Verify the simulation teaches correct concepts:
- [ ] pH affects enzyme activity (correct)
- [ ] Extreme conditions cause denaturation (correct)
- [ ] Denaturation is irreversible (correct)
- [ ] Not all collisions result in binding (correct - activation energy concept)
- [ ] Enzymes are specific (correct - only green substrates bind)

---

## Time Tracking

| Feature | Estimated | Actual | Notes |
|---------|-----------|--------|-------|
| pH Slider | 2 hours | | |
| Denaturation | 2.5 hours | | |
| Integration Testing | 30 min | | |
| **Total** | **5 hours** | | |

---

**Next Phase:** Proceed to Phase 3 (Basic Controls) or Phase 4 (Educational Features)