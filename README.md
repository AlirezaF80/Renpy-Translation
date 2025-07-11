# Ren'Py AI Auto-Translator 🤖

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AlirezaF80/Renpy-AutoTranslator/blob/main/Renpy_AutoTranslator.ipynb)
[![Python](https://img.shields.io/badge/Python-3.x-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This project provides a Google Colab notebook that automates the translation of Ren'Py (`.rpy`) script translation files using Large Language Models (LLMs). It is designed to intelligently parse game's script, send only the translatable text to an AI for translation, and then reassemble the translated text into a new, perfectly formatted `.rpy` file.

This tool speeds up the localization process, handling the tedious work of separating code from dialogue while preserving all necessary formatting.

## Key Features ✨

*   **AI-Powered Translation**: Leverages powerful LLMs (via Google Gemini or any OpenAI-compatible API like OpenRouter) to provide high-quality, context-aware translations.
*   **Structure Preservation**: Keeps all original Ren'Py code, labels, comments, indentation, and formatting tags (`{b}`, `\n`, etc.) intact.
*   **Intelligent Chunking**: Automatically splits large script files into smaller, manageable chunks to fit within the AI model's context window.
*   **Validation & Retries**: Includes a validation step to check the integrity of the translated output. If a chunk fails or an API error occurs, it automatically retries the translation.
*   **Configurable Prompts**: Use a detailed system prompt and a game-specific context guide to give the AI the best possible instructions, improving translation consistency and accuracy.
*   **Flexible API Support**: Works with both the official Google `genai` client and any API service that offers an OpenAI-compatible endpoint.

## How It Works ⚙️

The notebook follows a structured, multi-step process to ensure a safe and accurate translation:

1.  **Setup and Configuration**: You provide API keys for your chosen service (e.g., Google AI Studio, OpenRouter). You then configure the source and target languages, and most importantly, the prompts that will guide the AI.
2.  **Preprocessing and Chunking**: The notebook reads your input `.rpy` file, identifies all dialogue and UI strings that need translation, and groups them into chunks. Each chunk is sized to avoid exceeding the API's token limits.
3.  **The Translation Loop**: The script iterates through each chunk one by one:
    *   It sends the chunk to the LLM with your detailed instructions.
    *   The AI translates *only* the text portions, leaving code and formatting untouched.
    *   The notebook validates the returned translation to ensure block counts and structure are correct.
    *   If validation or the API call fails, it automatically retries a few times.
4.  **Final Assembly**: Once all chunks are translated, the notebook stitches them back together in the correct order and saves the complete translation to a new `.rpy` file.

---

## 🚀 Getting Started: Step-by-Step Guide

### 1. Open the Notebook in Google Colab

Click the badge to open the notebook directly in Google Colab:

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AlirezaF80/Renpy-AutoTranslator/blob/main/Renpy_AutoTranslator.ipynb)

### 2. Set Up API Keys and Backend

You need an API key from a supported LLM provider.

*   **Google Gemini**: Get a key from [Google AI Studio](https://aistudio.google.com/app/apikey).
*   **OpenAI-Compatible APIs**: Services like [OpenRouter.ai](https://openrouter.ai/keys) provide access to a variety of models through an OpenAI-compatible endpoint.

In the notebook, find the **"Set API Keys here"** cell and paste your key(s):

```python
GEMINI_API_KEY = "your_google_api_key_here"
OPENAI_API_KEY = "your_openai_or_openrouter_key_here"
```

Next, **run only one** of the client definition cells:
*   Run the **"Define Google Client"** cell if you are using a Gemini key.
*   Run the **"Define OpenAI Client"** cell if you are using an OpenAI-compatible key. Configure the `API_URL` and `MODEL_NAME` as needed.

### 3. Configure the Translation

This is the most important part for getting high-quality results.

#### a. Set Languages
In the **"Set Languages and SYSTEM PROMPT"** cell, define your source and target languages. These should match the language names used in your Ren'Py project.

```python
SOURCE_LANGUAGE = "English"
TARGET_LANGUAGE = "Persian"
```

#### b. Customize the Prompts (Highly Recommended)
The notebook uses two main prompts to guide the AI:

1.  **`SYSTEM_PROMPT`**: This is a general set of rules for the AI. The default prompt is robust and usually sufficient.
2.  **`GAME_SPECIFIC_PROMPT`**: This is where you provide context about **your game**. A good game-specific prompt is the key to accurate and consistent translation.

Follow the instructions in the notebook section **"How to Create a Game-Specific System Prompt"** to generate a guide for your game. It should include:
*   The game's overall tone.
*   A guide to character personalities and speaking styles.
*   A glossary of key terms, character names, and locations that should be translated consistently (or not at all).

Paste your finalized guide into the `GAME_SPECIFIC_PROMPT` variable.

### 4. Run the Translation

1.  **Upload Your File**: In the Colab file browser (on the left), upload the `.rpy` file you want to translate.
2.  **Set Input File**: In the **"Input file and Preprocess"** cell, update the `INPUT_FILE` variable with the name of your uploaded file.
    ```python
    INPUT_FILE = "my_game_script.rpy"
    ```
3.  **Execute the Cells**: Run the cells in order:
    *   Run the **"Input file and Preprocess"** cell.
    *   Run the **"Calculate max input length and Turn into chunks"** cell to see how the script will be split.
    *   Run the **"Reset translated chunks list"** cell.
    *   Finally, run the **"Translate loop"** cell. A progress bar will appear as it translates each chunk.

### 5. Download the Result

Once the loop finishes, the notebook will save the final translation in a new file named `your_file_<TARGET_LANGUAGE>.rpy` (e.g., `my_game_script_Persian.rpy`).

Right-click the new file in the Colab file browser and select **"Download"**. You can now add this file to your Ren'Py project's `game/tl/<language>` directory.

## Troubleshooting and Tips

*   **Resuming a Failed Run**: If the script stops due to an error, you don't have to start over! Note the last successfully translated chunk index (e.g., if it failed on chunk 15, the last success was 14). In the **"Translate loop"** cell, set `from_index` to the index of the chunk you want to resume from (e.g., `from_index = 15`). Then, re-run the loop cell.
*   **Low-Quality Translations**: The #1 cause of poor translation is a weak `GAME_SPECIFIC_PROMPT`. Spend time improving it with more character details, a better glossary, and clearer tone descriptions.
*   **API Errors**: Make sure your API key is correct and that your account has sufficient funds/credits. Some services also have rate limits, which the notebook's retry mechanism can help with.
*   **Validation Failures**: The script prints an error if a translated chunk is structurally invalid (e.g., mismatched number of dialogue blocks). The retry logic often fixes this on the next attempt by using a slightly different "temperature" (randomness) setting. If it consistently fails on a specific chunk, that chunk may contain unusual syntax that confuses the AI.

## Disclaimer

This is an automated tool. While it is designed to be highly accurate, LLMs can make mistakes.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
