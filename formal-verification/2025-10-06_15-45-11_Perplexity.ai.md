# Installing Research Software from Scratch: The StepProof Journey

**Source:** StepProof Installation and Configuration  
**Date:** September 2025  
**Duration:** 20+ troubleshooting cycles over several hours  
**Framework:** Systematic Debugging with AI-Guided Problem Solving

---

## The Mission: From Paper to Working Proof Verifier

This conversation documents a complete journey from reading an academic paper to running functional mathematical proof verification software on consumer hardware. The goal was installing StepProof—a system combining LLMs with the Isabelle theorem prover for step-by-step natural language proof verification.

**Starting Point:** Research paper, GitHub repository link, MacBook Air M2 with 8GB RAM  
**Ending Point:** Functional proof verification system successfully formalizing and checking mathematical statements  
**Key Challenge:** Research software typically assumes expert setup knowledge; this journey succeeded through persistent AI-assisted debugging

**Why This Matters:** Demonstrates that AI assistance can bridge the gap between "this software requires expert knowledge to install" and "anyone with persistence can get it working," democratizing access to research tools.

---

## Critical Moments: The Five Major Breakthroughs

### Breakthrough 1: Understanding the Missing Connect Button

**The Problem:** Documentation mentioned a "Connect" button to initialize the LLM backend, but it wasn't visible in the macOS menu bar. Clicking "Upload Problem" immediately triggered AttributeError about missing self.model.

**The Investigation:** AI analysis of ui.py source code revealed that connect() method existed and created self.model, but the menu bar wasn't showing the Connect option on macOS.

**The Solution:** Modified ui.py to call self.connect() automatically in the init method, bypassing the missing UI element. This pattern—when GUI doesn't match documentation, inspect code and add automatic initialization—became crucial.

**Key Learning:** When UI elements don't appear as documented, code inspection reveals initialization logic. Automatic initialization can bypass platform-specific GUI rendering issues.

---

### Breakthrough 2: The Isabelle Path Mystery

**The Problem:** Repeated "FileNotFoundError: No such file or directory: 'isabelle'" despite providing isabelle_path argument. Multiple attempts with different path variations all failed.

**The Journey:**
- First attempt: /Applications/Isabelle2024.app (wrong, this is just the bundle)
- Second attempt: /Applications/Isabelle2025.app/bin/ (wrong, bin folder doesn't exist)
- Final solution: /Applications/Isabelle2025.app/Contents/MacOS (correct location of executable)

**The Discovery Method:** Using find command to locate the actual isabelle executable inside the macOS app bundle structure:

    find /Applications/Isabelle2025.app -name isabelle

**Key Learning:** macOS .app bundles have internal structure with executables typically in Contents/MacOS, not at bundle root. Never assume path structure—use find to locate files definitively.

---

### Breakthrough 3: Access Token Confusion Resolved

**The Problem:** Initial assumption that ChatGPT subscription token would work for StepProof's LLM access. Clarification required about which service provided the necessary authentication.

**The Clarification:** StepProof uses Hugging Face to download and run Meta Llama 3 models locally—completely different from OpenAI's ChatGPT API. Required:
1. Hugging Face account creation
2. Access token generation at huggingface.co/settings/tokens
3. Requesting permission for Meta-Llama-3-8B-Instruct model
4. Waiting for approval (granted within hours)

**Key Learning:** Different AI services have completely separate authentication systems. Understanding which service a tool uses (local models via HF vs. API access to hosted models) is crucial for obtaining correct credentials.

---

### Breakthrough 4: The Tkinter Module Missing

**The Problem:** ModuleNotFoundError for tkinter despite Python being installed. This blocked UI launch entirely.

**The Root Cause:** Homebrew-installed Python on macOS often lacks GUI support because system Tk libraries aren't linked by default.

**The Solution:** Install Tcl/Tk system libraries and Python's Tkinter binding:

    brew install tcl-tk python-tk
    brew link --overwrite tcl-tk --force

Then verify:

    python3 -m tkinter

Should open a test GUI window.

**Key Learning:** Package managers sometimes install minimal distributions. GUI frameworks like Tkinter require explicit system library installation on macOS, not just Python packages.

---

### Breakthrough 5: Strategic Reset After Configuration Tangling

**The Moment of Decision:** After multiple virtual environment attempts, conflicting package installations, and nested venv confusion, continuing to debug became counterproductive.

**The Clean Slate Approach:**
1. Remove all virtual environments: rm -rf venv
2. Create fresh environment: python3 -m venv venv
3. Activate cleanly: source venv/bin/activate
4. Install all dependencies fresh: pip install -r requirements.txt
5. Verify each critical package: python -c "import accelerate; print(accelerate.version)"

**Why This Worked:** Starting fresh eliminated accumulated errors, conflicting installations, and path confusion that iterative fixes couldn't untangle.

**Key Learning:** Recognizing when to reset rather than continue debugging is a critical skill. Clean reinstallation is often faster than debugging tangled state, especially with AI to regenerate correct commands.

---

## The Error Encyclopedia: Pattern Recognition

### Pattern 1: Virtual Environment Not Activated

**Symptom:** error: externally-managed-environment when trying pip install

**Diagnosis:** Attempting to install system-wide on Homebrew Python, which is protected

**Solution:** Always create, activate, verify virtual environment before any pip operation

**Frequency:** Appeared 5+ times across the conversation

---

### Pattern 2: Missing Python Dependencies

**Symptom:** ModuleNotFoundError for transformers, accelerate, isabelle-client

**Diagnosis:** Virtual environment missing required packages

**Solution:** pip install -r requirements.txt after activating venv

**Frequency:** 3-4 occurrences with different missing modules

---

### Pattern 3: Wrong Executable Path

**Symptom:** FileNotFoundError or NotADirectoryError for isabelle

**Diagnosis:** Incorrect --isabelle_path pointing to wrong location in .app bundle

**Solution:** Use find to locate executable, provide directory containing it

**Frequency:** 4-5 attempts with different path variations

---

### Pattern 4: Model Backend Not Initialized

**Symptom:** AttributeError: '_tkinter.tkapp' object has no attribute 'model'

**Diagnosis:** connect() method not called before UI tries to use self.model

**Solution:** Call self.connect() in __init__ or ensure Connect menu works

**Frequency:** Recurring issue until code modification applied

---

## The Download Marathon: Managing Large Models

### The Challenge: 8GB of Model Files

**Context:** Meta Llama 3 8B model consists of multiple multi-gigabyte safetensors files totaling ~8GB, downloaded to USB-A external drive.

**The Wait:** Initial model download took significant time due to:
- Large file sizes (4-5GB per file)
- USB-A bandwidth limitations vs. internal SSD
- Network speed constraints
- Multiple file parts downloaded in parallel

**Storage Planning:** User proactively decided to store on external drive before download started, avoiding later reorganization. All subsequent commands referenced /Volumes/WD/STEP-PROOF path.

**Key Learning:** Planning storage location for large files early prevents painful file migration later. External drives require explicit path specification in all commands and impact I/O performance.

---

## The Moment of Success: First Proof Formalized

### The Final Achievement

After approximately 20 debugging cycles and multiple hours of persistent troubleshooting, the system finally worked:

**Test Problem Submitted:**
"Prove that if an integer n is even, then n squared (n^2) is even."

**First Proof Step:**
"Assume n is even, so there exists an integer k such that n = 2k."

**System Response:**

    theorem even_squared:
      fixes n :: int
      assumes "even n"
      shows "even (n^2)"


**Status Message:**
"Status: New proof step has been appended. Please check your proof."

**Terminal Output:**
Formalization completed, generating Isabelle theorem syntax

**What This Means:** StepProof successfully:
1. Connected to LLM backend (Llama 3 8B running on CPU)
2. Connected to Isabelle theorem prover
3. Accepted natural language problem statement
4. Formalized it into Isabelle syntax
5. Accepted natural language proof step
6. Ready for subsequent step-by-step verification

---

## Performance Reality: CPU-Only Inference

### Managing Expectations

**Hardware Limitations:**
- MacBook Air M2: No CUDA support (Apple Silicon doesn't support NVIDIA CUDA)
- 8GB RAM: Minimal for 8B parameter model
- External USB-A drive: Slower I/O than internal SSD
- CPU-only inference: 10x+ slower than GPU

**Measured Performance:**
- Initial model loading: Several minutes
- Problem formalization: 5-15 minutes estimated
- Each proof step: 1-5 minutes estimated
- Wait times noticeable but functional

**The Trade-off:** Accessibility (runs on standard laptop without specialized hardware) vs. Speed (significantly slower than GPU-equipped workstation)

**Key Insight:** Research software can run on consumer hardware with patience. AI assistance makes this accessible by helping navigate installation, while realistic expectations about performance prevent frustration.

---

## The Collaboration Dynamic: What Made This Work

### Human Contributions
- **Persistence:** Continued through 20+ error cycles without giving up
- **Clear Error Reporting:** Shared complete terminal outputs enabling precise diagnosis
- **Contextual Information:** Provided system specs, storage configuration, installation choices
- **Strategic Decisions:** Chose when to reset vs. continue debugging
- **Code Modification Willingness:** Edited ui.py when necessary to work around issues

### AI Contributions
- **Error Pattern Recognition:** Quickly identified common failure modes from error messages
- **Platform-Specific Knowledge:** Provided macOS-specific solutions for Isabelle paths, Tkinter, Homebrew
- **Step-by-Step Guidance:** Broke complex installation into manageable incremental steps with verification
- **Code Inspection:** Analyzed ui.py to identify missing initialization logic
- **Troubleshooting Strategies:** Suggested when to reset vs. debug, which logs to check, how to verify installations

### The Synergy
Neither alone could have succeeded efficiently. The human provided persistence, context, and decision-making; the AI provided diagnostic expertise, platform knowledge, and procedural guidance. Each error became a learning moment rather than a roadblock.

---

## Lessons for Future Complex Installations

### Technical Principles

**1. Environment Isolation Is Sacred:** Always use virtual environments; never install packages system-wide; activate before every operation

**2. Path Discovery Over Assumption:** Use find command to locate executables; never guess paths, especially in macOS .app bundles

**3. Dependency Order Matters:** Install system libraries (Tk) before Python bindings (Tkinter); install base packages before application-specific ones

**4. Error Messages Are Information:** Complete terminal output contains crucial diagnostic information; share it fully with AI assistants

**5. Strategic Resets Beat Infinite Debugging:** When configuration becomes tangled, clean reinstallation is faster than continued troubleshooting

### Collaboration Principles

**1. Complete Context Upfront:** System specs, platform, storage configuration enable targeted advice immediately

**2. Full Error Outputs:** Partial error messages force guessing; complete traces enable precise diagnosis

**3. Document State Changes:** Explicitly state actions taken (moved files, reinstalled packages) to prevent confusion

**4. Accept Workarounds:** When official documentation incomplete, code modifications and alternative approaches maintain momentum

**5. Patience Compounds:** Each solved error builds understanding; later problems resolve faster as patterns become familiar

---

## The Democratization Thesis: AI as Access Enabler

### Traditional Barrier
Research tools typically require graduate-level domain knowledge plus expert systems administration skills. Installation manuals assume familiarity with command-line tools, Python environments, system libraries, and debugging techniques. Many potential users abandon installation attempts when confronted with cryptic error messages.

### AI-Assisted Reality
Non-expert users can now navigate complex installations through conversational debugging. Each error becomes a teaching moment with immediate, contextualized explanation and fix. Success requires time investment (hours) and persistence but not prior expert knowledge.

### The Accessibility Shift
This isn't eliminating complexity—it's transforming the barrier from "expert knowledge required" to "persistence and systematic problem-solving required." That's a fundamentally more democratizing threshold, opening research tools to self-learners, interdisciplinary researchers, and students.

### The Broader Impact
As AI-assisted troubleshooting becomes standard, the friction of adopting new tools decreases. This could accelerate:
- Cross-disciplinary research (easier to use tools from other fields)
- Student engagement with advanced methods (installation doesn't block learning)
- Reproducibility (more researchers can actually run shared code)
- Tool adoption rates (developers can target broader audiences)

---

## Summary: From Paper to Production

**Starting State:** Academic paper about StepProof, no prior installation knowledge, standard consumer laptop

**Final State:** Functional proof verification system successfully formalizing mathematical statements and ready for interactive use

**What Was Accomplished:**
- Complete installation of StepProof with all dependencies
- Resolution of 20+ distinct error types across multiple categories
- Configuration of Python environment, Isabelle integration, LLM backend
- Download and setup of 8GB language model on external storage
- Modification of source code to work around platform-specific issues
- Successful formalization of first mathematical proof

**Time Investment:** Several hours across multiple sessions with extensive debugging

**Key Enabler:** AI-guided troubleshooting transformed each error from potential abandonment point into solvable puzzle with clear next steps

**The Broader Lesson:** Complex technical installations are achievable for non-experts through persistence, systematic debugging, and AI collaboration. The future of software adoption may involve AI assistants serving as patient, knowledgeable guides through installation processes that would otherwise require expert intervention.

---

This journey demonstrates that the combination of human persistence and AI diagnostic expertise can overcome barriers that traditionally limited access to research tools, potentially democratizing advanced computational methods across disciplines.

