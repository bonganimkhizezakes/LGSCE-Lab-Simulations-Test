# Phase 4: Educational Features

## Overview
This phase adds educational tools that transform the simulation into an interactive learning platform: scenario presets for guided exploration and quiz mode for testing understanding.

**Status:** ✅ Done
**Priority:** Medium
**Estimated Time:** 6-8 hours
**Dependencies:** Phase 1 AND Phase 2 MUST be complete

---

## Prerequisites

- [x] Phase 1 complete (foundation features)
- [x] Phase 2 complete (pH and denaturation)
- [ ] Temperature range 1-50°C functional
- [ ] pH slider 0-14 functional
- [ ] Molecule count sliders functional
- [ ] Reset button functional (Phase 3 recommended)
- [ ] Statistics tracking all events

**Note:** Phase 3 is strongly recommended but not strictly required

---

## Features in This Phase

1. ✅ Scenario Presets
2. ✅ Quiz Mode

---

## Feature 1: Scenario Presets

### User Story
As a teacher/student, I want pre-configured experimental scenarios to demonstrate specific biochemical concepts.

### Acceptance Criteria
- [ ] "Load Scenario" dropdown menu in control panel
- [ ] At least 6 different scenarios available
- [ ] Loading a scenario:
  - Automatically sets all relevant sliders
  - Resets the simulation
  - Shows brief explanation of the scenario purpose
  - Locks parameters for 10 seconds (optional)
- [ ] Scenarios demonstrate key concepts:
  - Normal enzyme behavior
  - Temperature effects
  - pH effects
  - Concentration effects
  - Enzyme saturation
  - Competition
- [ ] Smooth transition when loading scenarios

### Scenario Definitions

1. **Normal Conditions** (Baseline)
   - Temperature: 25°C
   - pH: 7.0
   - Enzymes: 1
   - Correct substrates: 5
   - Wrong substrates: 8
   - Purpose: "Observe normal enzyme-substrate interactions under optimal conditions"

2. **High Temperature Denaturation**
   - Temperature: 48°C
   - pH: 7.0
   - Enzymes: 2
   - Correct substrates: 10
   - Wrong substrates: 5
   - Purpose: "Watch enzymes denature from excessive heat over time"

3. **Extreme pH**
   - Temperature: 25°C
   - pH: 3.0
   - Enzymes: 2
   - Correct substrates: 10
   - Wrong substrates: 3
   - Purpose: "Observe how acidic conditions reduce binding success and cause denaturation"

4. **Optimal Conditions**
   - Temperature: 30°C
   - pH: 7.0
   - Enzymes: 5
   - Correct substrates: 15
   - Wrong substrates: 2
   - Purpose: "See maximum reaction rate with ideal conditions and high enzyme-substrate ratios"

5. **Low Substrate Concentration**
   - Temperature: 25°C
   - pH: 7.0
   - Enzymes: 4
   - Correct substrates: 3
   - Wrong substrates: 0
   - Purpose: "Observe enzyme saturation - enzymes compete for limited substrates"

6. **Substrate Competition**
   - Temperature: 25°C
   - pH: 7.0
   - Enzymes: 1
   - Correct substrates: 6
   - Wrong substrates: 20
   - Purpose: "See how wrong substrates interfere with enzyme-substrate recognition"

### Implementation Steps

1. **Add HTML scenario selector**
   ```html
   <div class="control-box">
       <h3>Educational Scenarios</h3>
       <select id="scenarioSelect">
           <option value="">-- Select a Scenario --</option>
           <option value="normal">Normal Conditions</option>
           <option value="highTemp">High Temperature Denaturation</option>
           <option value="extremePH">Extreme pH</option>
           <option value="optimal">Optimal Conditions</option>
           <option value="lowSubstrate">Low Substrate Concentration</option>
           <option value="competition">Substrate Competition</option>
       </select>
       <div id="scenarioDescription" class="scenario-description"></div>
   </div>
   ```

2. **Add CSS for scenario UI**
   ```css
   #scenarioSelect {
       width: 100%;
       padding: 10px;
       background: #2d3748;
       color: white;
       border: 2px solid #4a5568;
       border-radius: 6px;
       font-size: 14px;
       cursor: pointer;
   }
   
   #scenarioSelect:hover {
       border-color: #60a5fa;
   }
   
   .scenario-description {
       margin-top: 10px;
       padding: 10px;
       background: #2d3748;
       border-radius: 4px;
       font-size: 13px;
       line-height: 1.5;
       min-height: 40px;
       color: #cbd5e0;
   }
   
   .scenario-locked {
       border: 2px solid #fbbf24;
       animation: lockPulse 2s infinite;
   }
   
   @keyframes lockPulse {
       0%, 100% { border-color: #fbbf24; }
       50% { border-color: #f59e0b; }
   }
   ```

3. **Define scenarios object**
   ```javascript
   const scenarios = {
       normal: {
           name: "Normal Conditions",
           temperature: 25,
           ph: 7.0,
           enzymeCount: 1,
           correctSubstrates: 5,
           wrongSubstrates: 8,
           description: "Observe normal enzyme-substrate interactions under optimal conditions. This represents typical cellular environment.",
           learningGoals: ["Basic enzyme mechanism", "Substrate specificity", "Product formation"]
       },
       highTemp: {
           name: "High Temperature Denaturation",
           temperature: 48,
           ph: 7.0,
           enzymeCount: 2,
           correctSubstrates: 10,
           wrongSubstrates: 5,
           description: "Watch enzymes denature from excessive heat over time. Notice color change and loss of function after ~5 seconds.",
           learningGoals: ["Temperature denaturation", "Irreversible damage", "Protein structure importance"]
       },
       extremePH: {
           name: "Extreme pH",
           temperature: 25,
           ph: 3.0,
           enzymeCount: 2,
           correctSubstrates: 10,
           wrongSubstrates: 3,
           description: "Observe how acidic conditions reduce binding success and eventually cause denaturation. Notice the failed binding attempts.",
           learningGoals: ["pH effects on enzymes", "Binding success rate", "Chemical denaturation"]
       },
       optimal: {
           name: "Optimal Conditions",
           temperature: 30,
           ph: 7.0,
           enzymeCount: 5,
           correctSubstrates: 15,
           wrongSubstrates: 2,
           description: "See maximum reaction rate with ideal conditions and high enzyme-substrate ratios. Products form rapidly.",
           learningGoals: ["Optimal conditions", "High reaction rates", "Enzyme saturation"]
       },
       lowSubstrate: {
           name: "Low Substrate Concentration",
           temperature: 25,
           ph: 7.0,
           enzymeCount: 4,
           correctSubstrates: 3,
           wrongSubstrates: 0,
           description: "Observe enzyme saturation - multiple enzymes compete for limited substrates. Most enzymes stay free.",
           learningGoals: ["Substrate limitation", "Enzyme excess", "Concentration effects"]
       },
       competition: {
           name: "Substrate Competition",
           temperature: 25,
           ph: 7.0,
           enzymeCount: 1,
           correctSubstrates: 6,
           wrongSubstrates: 20,
           description: "See how wrong substrates interfere with enzyme-substrate recognition. Notice frequent collisions but fewer successful bindings.",
           learningGoals: ["Competitive inhibition", "Specificity importance", "Collision theory"]
       }
   };
   ```

4. **Implement loadScenario function**
   ```javascript
   let scenarioLocked = false;
   let lockTimeout = null;
   
   function loadScenario(scenarioKey) {
       if (!scenarios[scenarioKey]) return;
       
       const scenario = scenarios[scenarioKey];
       
       // Update temperature slider
       tempSlider.value = scenario.temperature;
       temperature = scenario.temperature;
       tempValue.textContent = temperature;
       
       // Update pH slider
       phSlider.value = scenario.ph;
       currentPH = scenario.ph;
       phValue.textContent = currentPH.toFixed(1);
       
       // Update pH status
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
       
       // Update enzyme count
       enzymeSlider.value = scenario.enzymeCount;
       enzymeCountEl.textContent = scenario.enzymeCount;
       updateEnzymeCount(scenario.enzymeCount);
       
       // Update correct substrates
       correctSlider.value = scenario.correctSubstrates;
       correctCountEl.textContent = scenario.correctSubstrates;
       updateSubstrateCount(true, scenario.correctSubstrates);
       
       // Update wrong substrates
       wrongSlider.value = scenario.wrongSubstrates;
       wrongCountEl.textContent = scenario.wrongSubstrates;
       updateSubstrateCount(false, scenario.wrongSubstrates);
       
       // Reset simulation with new parameters
       resetSimulation();
       
       // Show description
       const descDiv = document.getElementById('scenarioDescription');
       descDiv.innerHTML = `
           <strong>${scenario.name}</strong><br>
           ${scenario.description}
       `;
       
       // Optional: Lock parameters for 10 seconds
       lockScenarioParameters(10);
       
       console.log(`Loaded scenario: ${scenario.name}`);
   }
   
   function lockScenarioParameters(seconds) {
       scenarioLocked = true;
       
       // Disable all sliders
       const sliders = document.querySelectorAll('input[type="range"]');
       sliders.forEach(slider => {
           slider.disabled = true;
           slider.classList.add('scenario-locked');
       });
       
       // Show countdown
       let remaining = seconds;
       const descDiv = document.getElementById('scenarioDescription');
       const originalHTML = descDiv.innerHTML;
       
       const countdownInterval = setInterval(() => {
           descDiv.innerHTML = originalHTML + `<br><em>🔒 Parameters locked: ${remaining}s remaining</em>`;
           remaining--;
           
           if (remaining < 0) {
               clearInterval(countdownInterval);
               unlockScenarioParameters();
               descDiv.innerHTML = originalHTML;
           }
       }, 1000);
   }
   
   function unlockScenarioParameters() {
       scenarioLocked = false;
       const sliders = document.querySelectorAll('input[type="range"]');
       sliders.forEach(slider => {
           slider.disabled = false;
           slider.classList.remove('scenario-locked');
       });
   }
   ```

5. **Add event listener**
   ```javascript
   const scenarioSelect = document.getElementById('scenarioSelect');
   scenarioSelect.addEventListener('change', (e) => {
       const scenarioKey = e.target.value;
       if (scenarioKey) {
           loadScenario(scenarioKey);
       }
   });
   ```

6. **Optional: Add learning goals display**
   ```javascript
   function displayLearningGoals(scenario) {
       const goals = scenario.learningGoals
           .map(goal => `• ${goal}`)
           .join('<br>');
       return `<div class="learning-goals" style="margin-top: 8px; padding: 8px; background: #1f2937; border-left: 3px solid #60a5fa;"><strong>Learning Goals:</strong><br>${goals}</div>`;
   }
   
   // In loadScenario, add to description:
   descDiv.innerHTML = `
       <strong>${scenario.name}</strong><br>
       ${scenario.description}
       ${displayLearningGoals(scenario)}
   `;
   ```

### Testing Checklist
- [ ] Scenario dropdown is visible and styled
- [ ] All 6 scenarios load correctly
- [ ] Each scenario sets correct parameter values
- [ ] Simulation resets when scenario loads
- [ ] Description displays clearly
- [ ] Parameters lock for 10 seconds
- [ ] Parameters unlock automatically
- [ ] Can switch between scenarios
- [ ] Reset button works during scenario
- [ ] Slider changes respected after unlock
- [ ] No errors when rapidly switching scenarios

### Integration Notes
- Scenarios provide structured learning experiences
- Teachers can assign specific scenarios to study
- Students can compare different conditions
- Quiz mode (Feature 2) can use scenarios as test cases

### Definition of Done
- [ ] All 6 scenarios implemented
- [ ] All tests pass
- [ ] Descriptions are educational and clear
- [ ] Parameter locking works smoothly
- [ ] Code is well-commented with educational intent

---

## Feature 2: Quiz Mode

### User Story
As a student, I want to test my understanding by predicting outcomes before running experiments.

### Acceptance Criteria
- [ ] "Quiz Mode" toggle button in control panel
- [ ] When enabled:
  - User adjusts parameters without simulation running
  - System asks prediction question
  - User selects answer from multiple choice
  - Simulation runs for 30 seconds automatically
  - System compares prediction to actual result
  - Feedback provided with explanation
- [ ] Multiple question types:
  - Reaction rate changes
  - Denaturation prediction
  - Binding success estimation
- [ ] Score tracking (X correct / Y total)
- [ ] Questions have educational explanations

### Implementation Steps

1. **Add HTML quiz mode UI**
   ```html
   <div class="control-box">
       <h3>Quiz Mode</h3>
       <button id="quizToggle" class="btn-quiz">Start Quiz Mode</button>
       <div id="quizScore" class="quiz-score" style="display: none;">
           Score: <span id="correctAnswers">0</span> / <span id="totalQuestions">0</span>
       </div>
   </div>
   
   <!-- Quiz Modal -->
   <div id="quizModal" class="modal" style="display: none;">
       <div class="modal-content">
           <h2 id="quizQuestion"></h2>
           <div id="quizOptions"></div>
           <button id="submitAnswer" class="btn-submit">Submit Answer</button>
           <div id="quizFeedback" style="display: none;"></div>
           <button id="nextQuestion" class="btn-next" style="display: none;">Continue</button>
       </div>
   </div>
   ```

2. **Add CSS for quiz mode**
   ```css
   .btn-quiz {
       width: 100%;
       padding: 12px;
       background: #8b5cf6;
       color: white;
       border: none;
       border-radius: 8px;
       font-size: 16px;
       font-weight: bold;
       cursor: pointer;
       transition: all 0.2s;
   }
   
   .btn-quiz:hover {
       background: #7c3aed;
   }
   
   .btn-quiz.active {
       background: #10b981;
   }
   
   .quiz-score {
       margin-top: 10px;
       font-size: 16px;
       font-weight: bold;
       text-align: center;
   }
   
   .modal {
       position: fixed;
       top: 0;
       left: 0;
       width: 100%;
       height: 100%;
       background: rgba(0, 0, 0, 0.8);
       display: flex;
       align-items: center;
       justify-content: center;
       z-index: 1000;
   }
   
   .modal-content {
       background: #2d3748;
       padding: 30px;
       border-radius: 12px;
       max-width: 600px;
       width: 90%;
       max-height: 80vh;
       overflow-y: auto;
   }
   
   .modal-content h2 {
       margin-bottom: 20px;
       color: #60a5fa;
       font-size: 20px;
   }
   
   #quizOptions {
       margin: 20px 0;
   }
   
   .quiz-option {
       display: block;
       padding: 15px;
       margin: 10px 0;
       background: #4a5568;
       border-radius: 8px;
       cursor: pointer;
       transition: background 0.2s;
       border: 2px solid transparent;
   }
   
   .quiz-option:hover {
       background: #5a6678;
   }
   
   .quiz-option.selected {
       background: #3b82f6;
       border-color: #60a5fa;
   }
   
   .quiz-option.correct {
       background: #10b981;
       border-color: #34d399;
   }
   
   .quiz-option.incorrect {
       background: #dc2626;
       border-color: #ef4444;
   }
   
   #quizFeedback {
       margin-top: 20px;
       padding: 15px;
       border-radius: 8px;
       line-height: 1.6;
   }
   
   #quizFeedback.correct {
       background: #064e3b;
       border: 2px solid #10b981;
       color: #d1fae5;
   }
   
   #quizFeedback.incorrect {
       background: #7f1d1d;
       border: 2px solid #dc2626;
       color: #fecaca;
   }
   
   .btn-submit, .btn-next {
       width: 100%;
       margin-top: 15px;
       padding: 12px;
       border: none;
       border-radius: 8px;
       font-size: 16px;
       font-weight: bold;
       cursor: pointer;
       transition: all 0.2s;
   }
   
   .btn-submit {
       background: #3b82f6;
       color: white;
   }
   
   .btn-submit:hover {
       background: #2563eb;
   }
   
   .btn-next {
       background: #10b981;
       color: white;
   }
   
   .btn-next:hover {
       background: #059669;
   }
   ```

3. **Define quiz question bank**
   ```javascript
   const quizQuestions = {
       highTemperature: {
           setup: { temperature: 48, ph: 7.0, enzymeCount: 2, correctSubstrates: 10, wrongSubstrates: 5 },
           question: "What will happen to the enzymes at 48°C over time?",
           options: [
               { text: "Nothing - enzymes are stable at all temperatures", correct: false },
               { text: "Enzymes will denature and stop functioning", correct: true },
               { text: "Enzymes will work faster indefinitely", correct: false },
               { text: "Only wrong substrates will be affected", correct: false }
           ],
           explanation: "At 48°C (above 45°C), enzymes denature after about 5 seconds. High temperature breaks hydrogen bonds in protein structure, causing irreversible loss of function."
       },
       
       lowPH: {
           setup: { temperature: 25, ph: 3.0, enzymeCount: 2, correctSubstrates: 10, wrongSubstrates: 3 },
           question: "How will pH 3.0 affect the reaction rate?",
           options: [
               { text: "Reaction rate will increase significantly", correct: false },
               { text: "Reaction rate will stay the same", correct: false },
               { text: "Reaction rate will decrease due to reduced binding success", correct: true },
               { text: "Only wrong substrates will be affected", correct: false }
           ],
           explanation: "pH 3.0 is highly acidic and far from the optimal pH of 7.0. This disrupts the enzyme's active site, reducing binding success rate. Eventually, enzymes may denature."
       },
       
       excessEnzymes: {
           setup: { temperature: 25, ph: 7.0, enzymeCount: 5, correctSubstrates: 2, wrongSubstrates: 0 },
           question: "With 5 enzymes and only 2 substrates, what will limit the reaction rate?",
           options: [
               { text: "Enzyme concentration", correct: false },
               { text: "Substrate concentration", correct: true },
               { text: "Temperature", correct: false },
               { text: "Nothing - reaction will be fast", correct: false }
           ],
           explanation: "Substrate concentration is the limiting factor. Enzymes compete for limited substrates. This demonstrates substrate-limited kinetics."
       },
       
       optimalConditions: {
           setup: { temperature: 30, ph: 7.0, enzymeCount: 3, correctSubstrates: 15, wrongSubstrates: 1 },
           question: "How will the reaction rate compare to normal conditions (25°C, pH 7, 1 enzyme, 5 substrates)?",
           options: [
               { text: "Much slower", correct: false },
               { text: "About the same", correct: false },
               { text: "Significantly faster", correct: true },
               { text: "Slightly slower", correct: false }
           ],
           explanation: "Optimal temperature (30°C), ideal pH (7.0), more enzymes (3 vs 1), and more substrates (15 vs 5) all contribute to a much higher reaction rate."
       },
       
       competition: {
           setup: { temperature: 25, ph: 7.0, enzymeCount: 1, correctSubstrates: 5, wrongSubstrates: 20 },
           question: "With many wrong substrates, what will happen to successful bindings?",
           options: [
               { text: "Increase - more collisions overall", correct: false },
               { text: "Decrease - wrong substrates interfere", correct: true },
               { text: "Stay the same - wrong substrates are ignored", correct: false },
               { text: "Stop completely", correct: false }
           ],
           explanation: "Wrong substrates cause frequent unsuccessful collisions, reducing the chance of correct substrate-enzyme encounters. This is similar to competitive inhibition."
       }
   };
   ```

4. **Implement quiz mode logic**
   ```javascript
   let quizMode = false;
   let quizActive = false;
   let currentQuestion = null;
   let selectedOption = null;
   let quizScore = { correct: 0, total: 0 };
   
   const quizToggle = document.getElementById('quizToggle');
   const quizModal = document.getElementById('quizModal');
   const quizScoreDiv = document.getElementById('quizScore');
   const correctAnswersEl = document.getElementById('correctAnswers');
   const totalQuestionsEl = document.getElementById('totalQuestions');
   
   quizToggle.addEventListener('click', () => {
       quizMode = !quizMode;
       
       if (quizMode) {
           quizToggle.textContent = 'Exit Quiz Mode';
           quizToggle.classList.add('active');
           quizScoreDiv.style.display = 'block';
           startQuizQuestion();
       } else {
           quizToggle.textContent = 'Start Quiz Mode';
           quizToggle.classList.remove('active');
           quizScoreDiv.style.display = 'none';
           quizModal.style.display = 'none';
           isRunning = true; // Resume normal operation
       }
   });
   
   function startQuizQuestion() {
       // Select random question
       const questionKeys = Object.keys(quizQuestions);
       const randomKey = questionKeys[Math.floor(Math.random() * questionKeys.length)];
       currentQuestion = quizQuestions[randomKey];
       
       // Apply setup parameters
       applyQuizSetup(currentQuestion.setup);
       
       // Pause simulation
       isRunning = false;
       
       // Show modal with question
       showQuizQuestion();
   }
   
   function applyQuizSetup(setup) {
       tempSlider.value = setup.temperature;
       temperature = setup.temperature;
       tempValue.textContent = temperature;
       
       phSlider.value = setup.ph;
       currentPH = setup.ph;
       phValue.textContent = currentPH.toFixed(1);
       
       updateEnzymeCount(setup.enzymeCount);
       enzymeSlider.value = setup.enzymeCount;
       enzymeCountEl.textContent = setup.enzymeCount;
       
       updateSubstrateCount(true, setup.correctSubstrates);
       correctSlider.value = setup.correctSubstrates;
       correctCountEl.textContent = setup.correctSubstrates;
       
       updateSubstrateCount(false, setup.wrongSubstrates);
       wrongSlider.value = setup.wrongSubstrates;
       wrongCountEl.textContent = setup.wrongSubstrates;
       
       resetSimulation();
   }
   
   function showQuizQuestion() {
       const questionEl = document.getElementById('quizQuestion');
       const optionsEl = document.getElementById('quizOptions');
       const feedbackEl = document.getElementById('quizFeedback');
       const submitBtn = document.getElementById('submitAnswer');
       const nextBtn = document.getElementById('nextQuestion');
       
       questionEl.textContent = currentQuestion.question;
       
       // Clear previous options
       optionsEl.innerHTML = '';
       selectedOption = null;
       
       // Create option buttons
       currentQuestion.options.forEach((option, index) => {
           const optionDiv = document.createElement('div');
           optionDiv.className = 'quiz-option';
           optionDiv.textContent = option.text;
           optionDiv.dataset.index = index;
           
           optionDiv.addEventListener('click', () => {
               // Deselect all
               document.querySelectorAll('.quiz-option').forEach(opt => {
                   opt.classList.remove('selected');
               });
               
               // Select clicked
               optionDiv.classList.add('selected');
               selectedOption = index;
           });
           
           optionsEl.appendChild(optionDiv);
       });
       
       // Reset feedback
       feedbackEl.style.display = 'none';
       submitBtn.style.display = 'block';
       nextBtn.style.display = 'none';
       
       quizModal.style.display = 'flex';
   }
   
   document.getElementById('submitAnswer').addEventListener('click', () => {
       if (selectedOption === null) {
           alert('Please select an answer');
           return;
       }
       
       // Record baseline statistics
       const initialProducts = productCount;
       const initialBindings = successfulBindings;
       
       // Run simulation for 30 seconds
       isRunning = true;
       quizModal.style.display = 'none';
       
       setTimeout(() => {
           isRunning = false;
           
           // Calculate results
           const finalProducts = productCount;
           const finalBindings = successfulBindings;
           
           // Evaluate answer
           evaluateQuizAnswer(selectedOption, initialProducts, finalProducts);
       }, 30000); // 30 seconds
   });
   
   function evaluateQuizAnswer(selectedIndex, initialProducts, finalProducts) {
       const selectedAnswer = currentQuestion.options[selectedIndex];
       const isCorrect = selectedAnswer.correct;
       
       quizScore.total++;
       if (isCorrect) quizScore.correct++;
       
       // Update score display
       correctAnswersEl.textContent = quizScore.correct;
       totalQuestionsEl.textContent = quizScore.total;
       
       // Show feedback
       const feedbackEl = document.getElementById('quizFeedback');
       const submitBtn = document.getElementById('submitAnswer');
       const nextBtn = document.getElementById('nextQuestion');
       
       submitBtn.style.display = 'none';
       nextBtn.style.display = 'block';
       feedbackEl.style.display = 'block';
       
       // Highlight correct/incorrect options
       document.querySelectorAll('.quiz-option').forEach((opt, index) => {
           if (currentQuestion.options[index].correct) {
               opt.classList.add('correct');
           } else if (index === selectedIndex && !isCorrect) {
               opt.classList.add('incorrect');
           }
       });
       
       if (isCorrect) {
           feedbackEl.className = 'correct';
           feedbackEl.innerHTML = `
               <strong>✓ Correct!</strong><br>
               ${currentQuestion.explanation}<br><br>
               <em>During the simulation: ${finalProducts - initialProducts} products were formed.</em>
           `;
       } else {
           feedbackEl.className = 'incorrect';
           feedbackEl.innerHTML = `
               <strong>✗ Incorrect</strong><br>
               The correct answer was: "${currentQuestion.options.find(o => o.correct).text}"<br><br>
               ${currentQuestion.explanation}<br><br>
               <em>During the simulation: ${finalProducts - initialProducts} products were formed.</em>
           `;
       }
       
       quizModal.style.display = 'flex';
   }
   
   document.getElementById('nextQuestion').addEventListener('click', () => {
       quizModal.style.display = 'none';
       startQuizQuestion();
   });
   ```

### Testing Checklist
- [ ] Quiz mode toggle works
- [ ] Random questions are selected
- [ ] Parameters apply correctly for each question
- [ ] Options are clickable and show selection
- [ ] Simulation runs for 30 seconds after submit
- [ ] Correct answers show green feedback
- [ ] Incorrect answers show red feedback
- [ ] Correct option is highlighted after submit
- [ ] Explanations are displayed
- [ ] Score updates correctly
- [ ] Can progress through multiple questions
- [ ] Exit quiz mode returns to normal operation
- [ ] No errors during quiz flow

### Integration Notes
- Quiz mode makes simulation educational interactive
- Can be expanded with more question types
- Could track performance for assessment
- Consider adding difficulty levels

### Definition of Done
- [ ] All acceptance criteria met
- [ ] At least 5 different questions
- [ ] All tests pass
- [ ] Questions are educationally sound
- [ ] Explanations are scientifically accurate
- [ ] UI is polished and user-friendly

---

## Phase 4 Integration Testing

### Integration Test Checklist
- [ ] Scenarios and quiz mode work independently
- [ ] Can load scenario then start quiz
- [ ] Quiz questions match their scenarios correctly
- [ ] Score persists across questions
- [ ] Reset button works in quiz mode
- [ ] Speed control works in quiz mode (optional)
- [ ] All Phase 1, 2, 3 features still work
- [ ] Statistics track correctly during quiz
- [ ] Performance is stable

---

## Phase 4 Definition of Done

- [ ] Both features implemented
- [ ] All tests pass
- [ ] Educational value is clear
- [ ] Questions are scientifically accurate
- [ ] Scenarios demonstrate key concepts
- [ ] Code is well-documented
- [ ] Demo prepared showing:
  - Loading different scenarios
  - Completing quiz questions
  - Score tracking
- [ ] Ready for Phase 5

---

## Educational Assessment

Verify the features teach effectively:
- [ ] Scenarios cover important biochemistry concepts
- [ ] Questions test understanding, not memorization
- [ ] Explanations reinforce learning
- [ ] Progression is logical
- [ ] Feedback is constructive
- [ ] Content is age-appropriate (high school/college)

---

## Time Tracking

| Feature | Estimated | Actual | Notes |
|---------|-----------|--------|-------|
| Scenario Presets | 2.5 hours | | |
| Quiz Mode | 4.5 hours | | |
| Integration Testing | 1 hour | | |
| **Total** | **8 hours** | | |

---

**Next Phase:** Proceed to Phase 5 (Visual Polish) - optional but enhances experience