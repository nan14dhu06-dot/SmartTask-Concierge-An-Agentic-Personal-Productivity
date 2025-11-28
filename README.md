# SmartTask-Concierge-An-Agentic-Personal-Productivity
SmartTask Concierge is an LLM-powered productivity agent that turns natural-language requests into organised tasks, deadlines and priorities. It generates helpful email/message drafts, maintains persistent task state, and produces daily summaries to simplify planning for students and professionals.
### Problem Statement -- the problem you're trying to solve, and why you think it's an important or interesting problem to solve

Most students and knowledge workers manage their day across many tools: a to-do list app, calendar, email drafts, chat, and notes. Switching between these tools and manually entering the same information is repetitive and time-consuming. 

Typical pain points are:
- Remembering all tasks that appear in chats, emails, and thoughts during the day.
- Manually planning when to do each task and which ones matter most.
- Writing routine content like status emails or update messages from scratch.
- At the end of the day, having no clear picture of what actually got done.

These problems are interesting because they are not only about information storage but also about reasoning: deciding priorities, understanding vague natural-language instructions (“sometime tomorrow afternoon”), and generating appropriate communication. This is exactly where LLM-based agents can add value, by combining language understanding with tool use and stateful memory.

My goal in this project is to build a single conversational agent that can:
1. Turn messy natural-language requests into structured tasks with due dates and priorities.
2. Generate draft emails or messages related to those tasks.
3. Produce an end-of-day summary of completed work.

---

### Why agents? -- Why are agents the right solution to this problem?

Traditional rule-based productivity tools usually expect the user to adapt to the tool: clicking specific buttons, filling in forms, and learning a rigid interface. They are not good at understanding vague or combined requests such as:

> “Tomorrow afternoon finish the research memo, remind me to review the contract by Friday, and draft a polite update email to the client.”

An agentic system powered by an LLM is better suited because:

- *Natural-language interface:* The user can simply describe what they want in plain language, without thinking about fields or forms.
- *Tool calling:* The agent can decide when to call specific tools – for example, a task manager tool to create tasks, a date parser to interpret “tomorrow afternoon”, and a writer tool to create an email draft.
- *State and memory:* The agent can maintain a list of tasks across turns and reuse that context when generating summaries or follow-up content.
- *Reasoning and clarification:* When the request is ambiguous (“finish soon”), the agent can ask targeted clarification questions instead of silently guessing.

Because the problem mixes understanding, planning, and communication, an LLM agent that orchestrates specialised tools is a natural fit.

---

### What you created -- What's the overall architecture?

I created *SmartTask Concierge*, a conversational productivity assistant that acts as a personal task manager and content writer.

High-level architecture:

1. *LLM Agent Core*
   - Runs with a system prompt that defines its role: “You are a productivity assistant that manages tasks, deadlines, and communication for a single user.”
   - Decides which tools to call and in what order, based on the user’s message.

2. *Tools*
   - *Task Manager Tool:* Create, list, update, and delete tasks. Each task has id, title, description, created_at, due, priority, and status.
   - *Date & Time Tool:* Uses natural-language date parsing to convert phrases like “tomorrow afternoon” or “next Monday morning” into concrete datetimes.
   - *Content Writer Tool:* Generates email or message drafts (status updates, reminders, confirmations) using tasks as context.
   - *Daily Summary Tool:* Generates a concise summary of completed tasks for a given day.

3. *Memory / Storage*
   - Tasks are stored in a simple JSON/CSV file so the state persists across interactions.
   - The agent loads this state at the start of a session and writes back updates after each tool call.

4. *Interaction Flow*
   - User sends a natural-language request.
   - The agent interprets it, calls the task and date tools as needed, then optionally the content writer.
   - The final answer combines a human-readable explanation, a structured task list, and any generated drafts.

---

### Demo -- Show your solution

A simplified example interaction:

*User:*  
“Tomorrow afternoon finish the research memo, email the client with an update, and remind me to review the contract by Friday.”

*Agent reasoning (summarised):*
- Parse two tasks and one related email.
- Use the date tool for “tomorrow afternoon” and “Friday”.
- Create tasks via the task manager.
- Use the content writer to create an email draft.

*Agent Output (shortened):*

1. ✅ Created task: *Finish research memo*  
   - Due: tomorrow, 3:00 PM (local time assumed)  
   - Priority: High  
   - Status: Not started

2. ✅ Created task: *Review contract*  
   - Due: Friday, 5:00 PM  
   - Priority: Medium  
   - Status: Not started

3. ✉ Draft email to client:

> Subject: Update on research memo  
>  
> Dear Ragul ,  
> I’m writing to share an update on the research memo we discussed. I am finalising the analysis and expect to have a complete version ready by tomorrow afternoon. Once it’s ready, I will send it for your review and incorporate any feedback you may have.  
>  
> Best regards,  
>   Nandhitha V N 

At the end of the day, the user can ask:

*User:*  
“Give me a summary of what I completed today.”

*Agent:*  
- Looks up all tasks with status “done” for today.
- Returns a bullet list of completed items plus a short paragraph summary that can be pasted into a log or an email.

---

### The Build -- How you created it, what tools or technologies you used.

*Environment*

- Implemented in a Kaggle Notebook using Python.
- Used an LLM with tool-calling / function-calling capability as the central reasoning engine.

*Libraries & Components*

- openai (or compatible SDK) to talk to the LLM.
- pandas to manage the task table and handle simple analytics.
- dateparser / datetime for interpreting natural-language dates.
- json or CSV for persistent file-based storage.

*Steps*

1. *Designing the schema and tools*
   - Defined the task schema and wrote Python functions for create_task, list_tasks, update_task, delete_task.
   - Defined tool schemas compatible with the LLM’s function-calling interface.

2. *Prompting the agent*
   - Wrote a system prompt describing the agent’s behaviour.
   - Added developer instructions emphasising:
     - Always ask for clarification when dates are ambiguous.
     - Avoid creating duplicate tasks; instead suggest merging or updating.
     - Use concise but clear language in responses.

3. *Connecting tools to the LLM*
   - Registered the Python functions as tools.
   - Implemented the loop: send user message → receive tool calls from the model → execute tools → send tool results back → receive final response.

4. *Evaluation and iteration*
   - Created a small set of realistic test prompts for students and junior professionals.
   - Logged where the agent behaved unexpectedly (e.g., misinterpreting “this weekend” or creating near-duplicate tasks).
   - Improved prompts and simple rules (like default times for “morning/afternoon/evening”) based on those tests.

---

### If I had more time, this is what I'd do

This project focuses on the core loop of an agentic productivity assistant. With more time, I would extend it in several directions:

1. *Real integrations*
   - Connect to Google Calendar or other calendar APIs so that due dates become real events.
   - Integrate with email APIs so that approved drafts can be sent directly from the agent.

2. *Better personalisation and prioritisation*
   - Learn from the user’s behaviour: which tasks they usually complete first, preferred working hours, and typical deadlines.
   - Use this information to generate a smarter schedule or to automatically assign more accurate priorities.

3. *Richer evaluation*
   - Collect structured logs of agent decisions and user corrections.
   - Build simple metrics such as “task parsing accuracy”, “deadline correctness”, and user-rated quality of email drafts.
   - Use these metrics to compare different prompts or tool designs.

4. *User interface*
   - Build a lightweight web UI or chat-style dashboard where the user can see their tasks, edit them manually, and talk to the agent in the same place.
   - Add visual summaries (charts of completed vs pending tasks) to complement the textual summaries.

5. *Multi-agent variations*
   - Experiment with a planner–executor setup: one agent that plans and breaks down work, and another focused on writing content or managing long-running tasks.
  


AUTHOR 



NANDHITHA V N 

Even in its current form, SmartTask Concierge demonstrates how an LLM-driven agent can turn unstructured daily instructions into organised, actionable work, but there is plenty of room to grow into a fully featured personal productivity companion.
