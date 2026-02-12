# PHASE 2: REALISTIC MOCK EXAM IMPLEMENTATION

## Copy-Paste Code Snippets for Next Upgrade

---

## STEP 1: Update Mock Exam HTML Structure

**Replace the existing Mock Exam section with this:**

```html
<!-- Mock Exam Section -->
<div id="mockExam" class="content-section">
  <h2 style="color: var(--primary); margin-bottom: 20px;">
    ⏱️ Mock Examinations
  </h2>

  <div
    class="tip-box"
    style="background: linear-gradient(135deg, #fef3c7 0%, #fde68a 100%);"
  >
    <strong>⚠️ IMPORTANT - MOCK EXAM RULES:</strong><br />
    • Once started, you CANNOT pause the exam<br />
    • You CANNOT return to previous sections<br />
    • All questions must be answered (blanks count as wrong)<br />
    • Time limits are strictly enforced<br />
    • Exam will auto-submit when time expires<br /><br />
    <strong>This simulates the actual AFPSAT exam conditions.</strong>
  </div>

  <div class="card-grid">
    <div class="card" onclick="startMockExam('afpsat')">
      <h3>AFPSAT Full Mock Exam</h3>
      <p>150 items - Strict timed simulation</p>
      <div
        style="margin-top: 15px; padding: 15px; background: var(--bg-light); border-radius: 8px;"
      >
        <strong>Format:</strong><br />
        • Abstract: 50 questions (12 min)<br />
        • Verbal: 65 questions (30 min)<br />
        • Numerical: 35 questions (35 min)<br />
        <strong style="color: var(--accent);">Total: 77 minutes</strong>
      </div>
      <div id="afpsatMockLock" style="margin-top: 15px;"></div>
    </div>
    <div class="card" onclick="startMockExam('neuroPsych')">
      <h3>Neuro-Psych Simulation</h3>
      <p>Comprehensive psychological assessment</p>
      <div
        style="margin-top: 15px; padding: 15px; background: var(--bg-light); border-radius: 8px;"
      >
        <strong>Includes:</strong><br />
        • Personality assessment<br />
        • Situational judgment<br />
        • Consistency checking<br />
        <strong style="color: var(--accent);">Time: 60 minutes</strong>
      </div>
    </div>
  </div>

  <div id="mockExamContent"></div>
</div>
```

---

## STEP 2: Add Mock Exam State Management

**Add this after the timerState object:**

```javascript
// Mock Exam State
let mockExamState = {
  active: false,
  examType: null,
  currentSection: 0,
  sections: [],
  responses: [],
  sectionStartTime: null,
  totalStartTime: null,
  canProgress: false,
  completedSections: [],
};
```

---

## STEP 3: Replace startMockExam Function

**Replace the existing startMockExam function with:**

```javascript
// Start mock exam with readiness check
function startMockExam(examType) {
  if (examType === "afpsat") {
    // Check readiness
    const prediction = afpsatPredictor.calculatePassingProbability(userData);
    const isReady = prediction.breakdown.totalScore >= 55; // Borderline minimum

    const lockMessage = !isReady
      ? `
            <div style="background: #fee2e2; padding: 15px; border-radius: 8px; border-left: 4px solid var(--danger); margin-top: 15px;">
                <strong>🔒 MOCK EXAM LOCKED</strong><br><br>
                You need more practice before taking a full mock exam.<br><br>
                <strong>Requirements:</strong><br>
                • Complete 30+ practice questions per section<br>
                • Average 55%+ across all sections<br>
                • Practice time management<br><br>
                <strong>Your current readiness:</strong> ${prediction.breakdown.totalScore}/100<br>
                Status: ${prediction.band}
            </div>
        `
      : "";

    document.getElementById("afpsatMockLock").innerHTML = lockMessage;

    if (!isReady) {
      showNotification(
        "❌ Complete more practice sessions to unlock mock exams",
      );
      return;
    }

    const html = `
            <div style="text-align: center; padding: 40px; background: white; border-radius: 16px; margin-top: 20px;">
                <h2 style="color: var(--primary); margin-bottom: 20px;">⚠️ AFPSAT FULL MOCK EXAM</h2>
                
                <div style="background: #fef3c7; padding: 25px; border-radius: 12px; margin: 20px auto; max-width: 600px; text-align: left; border-left: 4px solid var(--accent);">
                    <h3 style="color: var(--accent); margin-bottom: 15px;">EXAM CONDITIONS - READ CAREFULLY</h3>
                    
                    <p><strong>This mock exam simulates actual AFPSAT conditions:</strong></p>
                    <ul style="margin: 15px 0; line-height: 2;">
                        <li>✅ You will complete 3 sections in order</li>
                        <li>⏱️ Each section has a strict time limit</li>
                        <li>🚫 You CANNOT pause during sections</li>
                        <li>🚫 You CANNOT return to previous sections</li>
                        <li>🚫 You CANNOT skip questions</li>
                        <li>⚠️ Auto-submit when time expires</li>
                        <li>📝 2-minute breaks between sections only</li>
                    </ul>
                    
                    <p style="margin-top: 20px;"><strong>Total Time:</strong> 77 minutes + breaks</p>
                    <p><strong>Total Questions:</strong> 150</p>
                </div>
                
                <div style="background: #fee2e2; padding: 20px; border-radius: 12px; margin: 20px auto; max-width: 600px; border-left: 4px solid var(--danger);">
                    <strong>⚠️ WARNING:</strong><br>
                    • Ensure you have 90 uninterrupted minutes<br>
                    • Closing the window will forfeit your exam<br>
                    • You will receive comprehensive results at the end<br>
                    • Treat this as if it were the real exam
                </div>
                
                <div style="margin: 30px 0;">
                    <p style="font-size: 1.1rem; margin-bottom: 20px;">
                        <strong>Are you ready to begin?</strong>
                    </p>
                    <button class="btn btn-success" onclick="launchRealisticMock('afpsat')" style="padding: 15px 50px; font-size: 1.2rem;">
                        ✅ I UNDERSTAND - START EXAM
                    </button>
                    <br><br>
                    <button class="btn" style="background: var(--text-light); color: white; padding: 12px 40px;" onclick="document.getElementById('mockExamContent').innerHTML = ''">
                        Cancel
                    </button>
                </div>
            </div>
        `;
    document.getElementById("mockExamContent").innerHTML = html;
  } else {
    // Neuro-Psych mock exam
    const html = `
            <div style="text-align: center; padding: 40px; background: white; border-radius: 16px; margin-top: 20px;">
                <h2 style="color: var(--primary); margin-bottom: 20px;">🧠 NEURO-PSYCHOLOGICAL MOCK EXAM</h2>
                
                <p style="margin: 20px 0; font-size: 1.1rem;">This simulation includes:</p>
                <ul style="text-align: left; max-width: 500px; margin: 20px auto; line-height: 2;">
                    <li>Personality assessment questions</li>
                    <li>Situational judgment scenarios</li>
                    <li>Logical thinking problems</li>
                    <li>Stress tolerance evaluation</li>
                    <li>Consistency checking across questions</li>
                </ul>
                
                <div class="tip-box" style="max-width: 600px; margin: 30px auto;">
                    <strong>📋 Remember:</strong><br>
                    • Answer honestly and consistently<br>
                    • There are no "right" or "wrong" answers<br>
                    • The test checks for truthfulness<br>
                    • Contradictory answers will be flagged<br>
                    • Be yourself - don't try to "game" the system
                </div>
                
                <button class="btn btn-success" onclick="launchRealisticMock('neuroPsych')" style="padding: 15px 40px; font-size: 1.2rem; margin-top: 20px;">
                    START ASSESSMENT
                </button>
            </div>
        `;
    document.getElementById("mockExamContent").innerHTML = html;
  }
}
```

---

## STEP 4: Add Mock Exam Launcher

**Add this new function:**

```javascript
// Launch realistic mock exam
window.launchRealisticMock = function (examType) {
  if (examType === "afpsat") {
    // Initialize mock exam state
    mockExamState = {
      active: true,
      examType: "afpsat",
      currentSection: 0,
      sections: [
        {
          name: "Abstract Reasoning",
          questions: [
            ...practiceQuestions.afpsatAbstract.easy,
            ...practiceQuestions.afpsatAbstract.medium,
            ...practiceQuestions.afpsatAbstract.hard,
          ].slice(0, 15), // Use 15 for demo
          timeLimit: 720, // 12 minutes
          responses: [],
        },
        {
          name: "Verbal Ability",
          questions: [
            ...practiceQuestions.afpsatVerbal.easy,
            ...practiceQuestions.afpsatVerbal.medium,
            ...practiceQuestions.afpsatVerbal.hard,
          ].slice(0, 15), // Use 15 for demo
          timeLimit: 1800, // 30 minutes
          responses: [],
        },
        {
          name: "Numerical Ability",
          questions: [
            ...practiceQuestions.afpsatNumerical.easy,
            ...practiceQuestions.afpsatNumerical.medium,
            ...practiceQuestions.afpsatNumerical.hard,
          ].slice(0, 15), // Use 15 for demo
          timeLimit: 2100, // 35 minutes
          responses: [],
        },
      ],
      currentQuestion: 0,
      totalStartTime: Date.now(),
      sectionStartTime: Date.now(),
    };

    // Start first section
    startMockSection(0);
  }
};

// Start a mock exam section
function startMockSection(sectionIndex) {
  const section = mockExamState.sections[sectionIndex];
  mockExamState.currentSection = sectionIndex;
  mockExamState.currentQuestion = 0;
  mockExamState.sectionStartTime = Date.now();

  // Show section instructions
  const html = `
        <div style="background: white; padding: 40px; border-radius: 16px; text-align: center;">
            <h2 style="color: var(--primary); margin-bottom: 20px;">
                SECTION ${sectionIndex + 1} OF 3
            </h2>
            <h3 style="color: var(--secondary); margin-bottom: 30px;">
                ${section.name}
            </h3>
            
            <div style="background: var(--bg-light); padding: 30px; border-radius: 12px; max-width: 600px; margin: 0 auto; text-align: left;">
                <p style="font-size: 1.1rem; margin-bottom: 20px;"><strong>INSTRUCTIONS:</strong></p>
                <ul style="line-height: 2;">
                    <li>You have <strong>${Math.floor(section.timeLimit / 60)} minutes</strong> for this section</li>
                    <li>There are <strong>${section.questions.length} questions</strong></li>
                    <li>You must answer every question</li>
                    <li>You cannot return to this section once completed</li>
                    <li>The timer will start when you click "BEGIN"</li>
                </ul>
                
                <div style="background: #fee2e2; padding: 15px; border-radius: 8px; margin-top: 20px; border-left: 4px solid var(--danger);">
                    <strong>⚠️ NO PAUSE BUTTON</strong><br>
                    Once you begin, you must complete the entire section.
                </div>
            </div>
            
            <button class="btn btn-success" onclick="beginMockSection()" style="margin-top: 30px; padding: 15px 50px; font-size: 1.2rem;">
                BEGIN SECTION
            </button>
        </div>
    `;

  document.getElementById("mockExamContent").innerHTML = html;
}

// Begin mock exam section (start timer and questions)
window.beginMockSection = function () {
  const section = mockExamState.sections[mockExamState.currentSection];

  // Start timer
  startTimer(section.timeLimit, section.name, "exam");

  // Show first question
  showMockQuestion();
};

// Show current mock question
function showMockQuestion() {
  const section = mockExamState.sections[mockExamState.currentSection];
  const question = section.questions[mockExamState.currentQuestion];

  const html = `
        <div style="background: white; padding: 30px; border-radius: 16px;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; padding-bottom: 15px; border-bottom: 2px solid var(--border);">
                <div>
                    <h3 style="color: var(--primary); margin: 0;">
                        ${section.name}
                    </h3>
                    <p style="color: var(--text-light); margin: 5px 0 0 0;">
                        Question ${mockExamState.currentQuestion + 1} of ${section.questions.length}
                    </p>
                </div>
                <div id="sectionTimer" class="timer"></div>
            </div>
            
            <div class="question-card">
                <p style="font-size: 1.1rem; line-height: 1.6; margin: 20px 0;">
                    ${question.question}
                </p>
                
                <div class="options" style="margin-top: 30px;">
                    ${question.options
                      .map(
                        (opt, i) => `
                        <div class="option" onclick="selectMockAnswer(${i})">
                            ${String.fromCharCode(65 + i)}. ${opt}
                        </div>
                    `,
                      )
                      .join("")}
                </div>
            </div>
            
            <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 30px; padding-top: 20px; border-top: 2px solid var(--border);">
                <div style="color: var(--text-light);">
                    Progress: ${mockExamState.currentQuestion + 1} / ${section.questions.length}
                </div>
                <button class="btn btn-primary" onclick="submitMockAnswer()" style="padding: 12px 40px;">
                    ${mockExamState.currentQuestion < section.questions.length - 1 ? "NEXT QUESTION →" : "FINISH SECTION"}
                </button>
            </div>
            
            <div class="tip-box" style="margin-top: 20px; background: #fee2e2; border-left-color: var(--danger);">
                <strong>⚠️ EXAM MODE:</strong> You cannot pause or return to previous questions. 
                Make sure to answer before proceeding.
            </div>
        </div>
    `;

  document.getElementById("mockExamContent").innerHTML = html;
  updateTimerDisplay();
}

// Select answer in mock exam
window.selectMockAnswer = function (optionIndex) {
  document
    .querySelectorAll(".option")
    .forEach((opt) => opt.classList.remove("selected"));
  document.querySelectorAll(".option")[optionIndex].classList.add("selected");
  window.selectedMockAnswer = optionIndex;
};

// Submit answer in mock exam
window.submitMockAnswer = function () {
  if (window.selectedMockAnswer === undefined) {
    showNotification(
      "⚠️ You must select an answer. Blanks count as incorrect in the real exam.",
    );
    return;
  }

  const section = mockExamState.sections[mockExamState.currentSection];
  const question = section.questions[mockExamState.currentQuestion];

  // Record answer
  section.responses.push({
    questionIndex: mockExamState.currentQuestion,
    selected: window.selectedMockAnswer,
    correct: question.correct,
    isCorrect: window.selectedMockAnswer === question.correct,
  });

  // Move to next question or finish section
  if (mockExamState.currentQuestion < section.questions.length - 1) {
    mockExamState.currentQuestion++;
    window.selectedMockAnswer = undefined;
    showMockQuestion();
  } else {
    finishMockSection();
  }
};

// Finish current mock section
function finishMockSection() {
  stopTimer();
  const sectionTime = Math.floor(
    (Date.now() - mockExamState.sectionStartTime) / 1000,
  );
  mockExamState.sections[mockExamState.currentSection].timeSpent = sectionTime;

  // Check if more sections
  if (mockExamState.currentSection < mockExamState.sections.length - 1) {
    showMockBreak();
  } else {
    finishMockExam();
  }
}

// Show break between sections
function showMockBreak() {
  const nextSection = mockExamState.sections[mockExamState.currentSection + 1];

  const html = `
        <div style="background: white; padding: 40px; border-radius: 16px; text-align: center;">
            <h2 style="color: var(--success); margin-bottom: 20px;">
                ✅ Section ${mockExamState.currentSection + 1} Complete
            </h2>
            
            <div style="background: var(--bg-light); padding: 30px; border-radius: 12px; max-width: 500px; margin: 30px auto;">
                <p style="font-size: 1.2rem; margin-bottom: 15px;">
                    <strong>2-Minute Break</strong>
                </p>
                <p>Use this time to clear your mind.</p>
                <p style="margin-top: 15px; color: var(--text-light);">
                    Next: ${nextSection.name}
                </p>
            </div>
            
            <div id="breakTimer" class="timer" style="display: inline-block; font-size: 2rem; margin: 20px 0;"></div>
            
            <p style="margin-top: 20px;">The next section will begin automatically...</p>
        </div>
    `;

  document.getElementById("mockExamContent").innerHTML = html;

  // 2-minute countdown
  let breakTime = 120;
  const breakInterval = setInterval(() => {
    const mins = Math.floor(breakTime / 60);
    const secs = breakTime % 60;
    document.getElementById("breakTimer").textContent =
      `${mins}:${secs.toString().padStart(2, "0")}`;
    breakTime--;

    if (breakTime < 0) {
      clearInterval(breakInterval);
      startMockSection(mockExamState.currentSection + 1);
    }
  }, 1000);
}

// Finish entire mock exam
function finishMockExam() {
  const totalTime = Math.floor(
    (Date.now() - mockExamState.totalStartTime) / 1000,
  );

  // Calculate results
  let totalQuestions = 0;
  let totalCorrect = 0;
  const sectionResults = [];

  mockExamState.sections.forEach((section, index) => {
    const correct = section.responses.filter((r) => r.isCorrect).length;
    const total = section.responses.length;
    const percentage = Math.round((correct / total) * 100);
    const timeUsed = Math.floor(section.timeSpent / 60);
    const timeLimit = Math.floor(section.timeLimit / 60);
    const onTime = section.timeSpent <= section.timeLimit;

    totalQuestions += total;
    totalCorrect += correct;

    sectionResults.push({
      name: section.name,
      correct,
      total,
      percentage,
      timeUsed,
      timeLimit,
      onTime,
    });
  });

  const finalPercentage = Math.round((totalCorrect / totalQuestions) * 100);
  const passed = finalPercentage >= 47; // 71/150 = 47.3%

  // Save to userData
  userData.mockExams.push({
    date: new Date().toISOString(),
    score: finalPercentage,
    correct: totalCorrect,
    total: totalQuestions,
    sections: sectionResults,
    totalTime: totalTime,
  });
  saveUserData();

  // Show results
  showMockExamResults(
    finalPercentage,
    totalCorrect,
    totalQuestions,
    sectionResults,
    passed,
  );
}

// Show mock exam results
function showMockExamResults(percentage, correct, total, sections, passed) {
  const html = `
        <div style="background: white; padding: 40px; border-radius: 16px;">
            <h2 style="color: var(--primary); text-align: center; margin-bottom: 30px;">
                MOCK EXAM RESULTS
            </h2>
            
            <div style="text-align: center; margin: 40px 0;">
                <div class="score-circle" style="margin: 0 auto;">
                    <div class="score">${percentage}%</div>
                    <div class="total">${correct}/${total}</div>
                </div>
                
                <h3 style="color: ${passed ? "var(--success)" : "var(--danger)"}; margin-top: 30px; font-size: 2rem;">
                    ${passed ? "✅ YOU WOULD HAVE PASSED" : "❌ YOU WOULD HAVE FAILED"}
                </h3>
                
                <p style="margin-top: 15px; font-size: 1.1rem; color: var(--text-light);">
                    Passing threshold: 47% (71/150 questions)
                </p>
            </div>
            
            <div style="background: var(--bg-light); padding: 30px; border-radius: 12px; margin: 30px 0;">
                <h4 style="color: var(--primary); margin-bottom: 20px;">📊 SECTION BREAKDOWN</h4>
                
                ${sections
                  .map(
                    (section) => `
                    <div style="background: white; padding: 20px; border-radius: 8px; margin: 15px 0; border-left: 4px solid ${section.onTime ? "var(--success)" : "var(--danger)"};">
                        <h5 style="color: var(--primary); margin-bottom: 10px;">${section.name}</h5>
                        <div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px;">
                            <div>
                                <strong>Score:</strong> ${section.correct}/${section.total} (${section.percentage}%)
                                <div class="progress-bar" style="height: 20px; margin-top: 5px;">
                                    <div class="progress-fill" style="width: ${section.percentage}%;"></div>
                                </div>
                            </div>
                            <div>
                                <strong>Time:</strong> ${section.timeUsed}/${section.timeLimit} min
                                ${
                                  section.onTime
                                    ? '<div style="color: var(--success); margin-top: 5px;">✅ Completed on time</div>'
                                    : '<div style="color: var(--danger); margin-top: 5px;">⚠️ OVERTIME - would be auto-submitted</div>'
                                }
                            </div>
                        </div>
                    </div>
                `,
                  )
                  .join("")}
            </div>
            
            <div class="tip-box" style="${passed ? "background: #dcfce7; border-left-color: var(--success);" : "background: #fee2e2; border-left-color: var(--danger);"}">
                <strong>${passed ? "🎯 CONGRATULATIONS!" : "📚 KEEP PRACTICING"}</strong><br>
                ${
                  passed
                    ? "You demonstrated exam-passing performance! Continue practicing to maintain this level before your actual exam."
                    : "You need more preparation. Focus on your weakest section and practice time management. Aim for 75%+ before taking the real exam."
                }
            </div>
            
            <div style="text-align: center; margin-top: 30px;">
                <button class="btn btn-primary" onclick="showSection('stats')" style="padding: 12px 40px; margin: 0 10px;">
                    View Full Statistics
                </button>
                <button class="btn btn-success" onclick="showSection('dashboard')" style="padding: 12px 40px; margin: 0 10px;">
                    Back to Dashboard
                </button>
            </div>
        </div>
    `;

  document.getElementById("mockExamContent").innerHTML = html;
  mockExamState.active = false;
  updateDashboard();
}
```

---

## TESTING INSTRUCTIONS

1. **Copy all code snippets above**
2. **Insert them in the correct locations in your HTML file**
3. **Test the mock exam flow:**
   - Click "Take Mock Exam" → AFPSAT
   - Read instructions carefully
   - Click "I UNDERSTAND - START EXAM"
   - Complete each section
   - Observe 2-minute breaks
   - Review comprehensive results

4. **Verify all features work:**
   - ✅ Cannot pause during sections
   - ✅ Timer counts down
   - ✅ Auto-progresses between sections
   - ✅ Shows detailed results
   - ✅ Saves to performance history

---

## NEXT STEPS AFTER PHASE 2

Once Phase 2 is implemented and tested, proceed to:

- **Phase 3:** Adaptive Learning System
- **Phase 4:** Neuro-Psych Consistency Checking

Refer to IMPLEMENTATION_GUIDE.md for full details.

---

_Phase 2 Code Snippets v1.0_
_Ready for immediate implementation_
