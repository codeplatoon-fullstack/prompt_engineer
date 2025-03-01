# CHAT GPT-PROMPT ENGINEER

## Two types of LLM (Large Language Models) API’s

- Base LLM (GitHub_pilot)
  - Predicts input (next word) based on training data from internet or other sources
  - Does not return results but instead returns follow on text matching your prompts
- Instruction Tuned LLM (ChatGPT)
  - Follows specific instructions and attempts to follow those instructions (may need to be trained through RLHF[Reinforcement Learning with Human Feedback]
  - Attempts to return results to a prompt depending on it’s training and data
  - If it doesn’t work it’s because it wasn’t clear enough not because the LLM is broken.
  - Imagine you are speaking with someone who has no idea what you are talking about (boot, JD, toddler)

## SET UP

- Install `openai` and `python-dotenv` to set secret keys onto openai

```python
  pip install openai
  pip install python-dotenv
```

- Grab api key from the chatgpt site and save it as a `.env` file
- Set up a form of payment method `gpt-3.5-turbo` cost is $0.002 each ping
- Give openai your api key

```python
import openai
from dotenv import load_dotenv
import os

load_dotenv()
openai.api_key = os.getenv("API_KEY")
```

## Principles and Tactics on building prompts
  
### 1. Clear and specific instructions

- Clear is not a short
- Tactic 1: Use delimeters to ensure the LLM can clearly identify a separation in the prompt
  - Tripple quotes
  - Tripple backticks
  - Tripple dashes
  - Angle brackets
  - XML tags

```python
# here we specify where the text begins and ends with ```
prompt = f"""
Summarize the text delimited by triple backticks \ 
into a single sentence.
```{text}```
"""
```

- Tactic 2: Ask for a structured output like HTML or JSON

```python
prompt = f"""
Generate a list of three made-up book titles along \ 
with their authors and genres. 
Provide them in JSON format with the following keys: 
book_id, title, author, genre.
"""
```

- Tactic 3: Check whether conditions are satisfied check assumptions required to do the task

```python
# Here we specifically break down what to do if a condition 
# is met and what ot do otherwise along with a specific format
# with an example in which we would like the text to be returned
prompt = f"""
You will be provided with text delimited by triple quotes. 
If it contains a sequence of instructions, \ 
re-write those instructions in the following format:

Step 1 - ...
Step 2 - …
…
Step N - …

If the text does not contain a sequence of instructions, \ 
then simply write \"No steps provided.\"

\"\"\"{text_1}\"\"\"
"""
```

- Tactic 4: Few-shot prompting
  - Give examples of successful conditions of  a task and then ask the model to replicate the behavior provided

```python
# Provide a quick example of the behavior you want the LLM to replicate for you 
prompt = f"""
Your task is to answer in a consistent style.

<child>: Teach me about patience.

<grandparent>: The river that carves the deepest \ 
valley flows from a modest spring; the \ 
grandest symphony originates from a single note; \ 
the most intricate tapestry begins with a solitary thread.

<child>: Teach me about resilience.
"""
```

### 2. Give the model time to think

- Tactic 1: Specify the steps required to complete a task.

```python
text = f"""
In a charming village, siblings Jack and Jill set out on \ 
a quest to fetch water from a hilltop \ 
well. As they climbed, singing joyfully, misfortune \ 
struck—Jack tripped on a stone and tumbled \ 
down the hill, with Jill following suit. \ 
Though slightly battered, the pair returned home to \ 
comforting embraces. Despite the mishap, \ 
their adventurous spirits remained undimmed, and they \ 
continued exploring with delight.
"""
bad_prompt = """
Translate this paragraph into French, give me a list of all the names within the paragraph, and give me a dict with the summary and the names.

{text}
"""
good_prompt = f"""
Perform the following actions: 
1 - Summarize the following text delimited by triple \
backticks with 1 sentence.
2 - Translate the summary into French.
3 - List each name in the French summary.
4 - Output a json object that contains the following \
keys: french_summary, num_names.

Separate your answers with line breaks.

Use the following format:
Text: <text to summarize>
Summary: <summary>
Translation: <summary translation>
Names: <list of names in Italian summary>
Output JSON: <json with summary and num_names>

Text:
```{text}```
"""
```

- Tactic 2: Instruct the model to work out its own solujtion before rushing to a conclusion

  - By instructing the LLM to work out its own solution to a problem you are assuring that it wont just make an assumption that something is correct. This is valuable when running word problems and/or mathematical reasoning along with an attempteed solution. You can as the LLM to create it's own solution to the problem and compare it to the solution provided, identify errors, and provide constructive feedback.

## LLM Limitations

- Hallucinations = Makes statements that sound plausible but are not actually true. (Probability upon data provided to the LLM)

```python
prompt = f"""
Tell me about AeroGlide UltraSlim Smart Toothbrush by Boie
"""
```

Boie is a real company but the pruduct is not yet the LLM will still provide a `Hallucinated` answer that it thinks COULD be factual.

- Reducing hallucinations = provide the LLM a specific set of resources it should utilize to populate it's answer and you could even ask its response to come attached with resources.

## Iterative Prompt Development

1. IDEA
2. IMPLEMENTATION (CODE/DATA/PROMPT)
3. EXPERIMENT WITH RESULT
4. ERROR ANALYSIS
5. REPEAT

Prompt guidelines to follow:

- Be clear and specific
- Analyze why result does not give desired output
- Clarigy instructions, give more time to think
- Refine prompts with a batch of examples
