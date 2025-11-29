# prompt2manga: Autonomous AI Manga Studio 🎨📚

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![Google Gemini](https://img.shields.io/badge/AI-Gemini%202.5%20Flash--Lite-8E75B2?logo=google&logoColor=white)
![Stable Diffusion](https://img.shields.io/badge/Image%20Gen-Stable%20Diffusion%20(Anything--v5)-orange?logo=stability-ai&logoColor=white)
![Google ADK](https://img.shields.io/badge/Framework-Google%20ADK-4285F4?logo=google-cloud&logoColor=white)
![Kaggle](https://img.shields.io/badge/Platform-Kaggle%20Notebooks-20BEFF?logo=kaggle&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

> **prompt2manga** uses Google ADK & Gemini to autonomously write, draw, and compile a 10-panel manga PDF from a single text prompt.

---

## 📖 Table of Contents
- [Problem Statement](#-problem-statement)
- [The Solution](#-the-solution)
- [Architecture & Agents](#-architecture--agents)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Usage](#-usage)
- [Output Examples](#-output-examples)
- [Roadmap](#-roadmap)
- [License](#-license)

---

## 🚩 Problem Statement
Creating a manga is a massive multi-disciplinary challenge. It requires the combined skills of a creative writer, a storyboard artist, a character designer, and an illustrator. 

While Generative AI tools exist for writing (LLMs) and drawing (Image Generators), they are typically siloed. Stitching them together into a coherent, long-form visual narrative is incredibly difficult and manual. **prompt2manga** solves this by automating the entire pipeline from "Idea" to "PDF."

## 💡 The Solution: Agentic Workflow
This project creates a **Sequential Multi-Agent Pipeline** that mimics a real-world publishing studio. 

Instead of asking one AI to do everything (which causes hallucinations and memory crashes), we split the workload into specialized agents that pass data to each other via a **robust file-based handoff system**.

---

## 🤖 Architecture & Agents

The system uses the **Google Agent Development Kit (ADK)** to orchestrate a "Relay Race" between 5 specialized agents.

### The 5-Agent Team:
1.  **👤 Character Designer Agent** *Role:* Creative Director.  
    *Task:* Generates a JSON profile defining the protagonist's appearance, personality, and visual style.
    
2.  **📖 Story Writer Agent** *Role:* Plot Writer.  
    *Task:* Outlines a complete dramatic arc with exactly **10 scenes**, covering introduction, conflict, and resolution.

3.  **📝 Script Writer Agent** *Role:* Screenwriter.  
    *Task:* Adds dialogue and detailed visual descriptions to the scenes.  
    *Key Tech:* Saves the massive script to `manga_script.json` to prevent context window crashes.

4.  **🎨 Illustrator Agent** *Role:* Lead Artist.  
    *Task:* Reads the script file and triggers the **Image Generation Tool**.  
    *Model:* Uses **Stable Diffusion (Anything-v5)** to generate 10 high-quality anime-style panels.

5.  **📄 Publisher Agent** *Role:* Editor & Printer.  
    *Task:* Compiles the generated images and text into a formatted **5-Page PDF** (2 panels per page) using a custom layout engine.

---

## 🛠 Tech Stack

* **Orchestration:** [Google ADK (Agent Development Kit)](https://github.com/google/adk-python)
* **LLM Brain:** Google Gemini 2.5 Flash-Lite (via `google-genai` SDK)
* **Image Generation:** Stable Diffusion (`stablediffusionapi/anything-v5`)
* **Deep Learning:** PyTorch, Diffusers
* **Document Processing:** FPDF
* **Infrastructure:** Kaggle Notebooks (GPU T4 x2)

---

## 🚀 Getting Started

### Prerequisites
* A **Kaggle Account** (or a local machine with a powerful GPU).
* A **Google Gemini API Key** (Get it from [Google AI Studio](https://aistudio.google.com/)).

### Installation (Kaggle Setup)
1.  **Create a New Notebook** on Kaggle.
2.  **Set Accelerator:** Go to "Settings" -> "Accelerator" -> Select **GPU T4 x2**.
3.  **Add API Key:** * Go to "Add-ons" -> "Secrets".
    * Add a new secret label: `GEMINI_API_KEY`.
    * Paste your key as the value.
4.  **Install Dependencies:**
    Run the following in the first cell:
    ```python
    !pip install -U google-adk google-genai diffusers transformers accelerate torch fpdf safetensors
    ```

---

## 💻 Usage

### 1. Build the Pipeline
Initialize the agents and tools using the builder function. This ensures a fresh state for every run.

```python
# Initialize the pipeline
root_agent = build_manga_pipeline()
runner = InMemoryRunner(agent=root_agent)
