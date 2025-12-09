Part 1: Understanding Project Structure

When I first looked at the codebase, I saw it was a Task Manager built with Node.js. The main files I noticed are cli.js which seems to run all the commands, app.js, models.js, and storage.js. There’s also a README.md that explains how to use the app and its setup steps.​

The main technologies used here are Node.js and npm. It uses some packages like commander for the command line interface and uuid to make unique IDs for tasks.​

I think the main components are:

The CLI (for interacting with tasks)

Models (to describe what a task is)

Storage (where tasks are saved or loaded)

Main application logic (how everything connects)

At first, I was unsure about how task data is actually saved is it using a database or just a file? After checking, it looks like it saves to a file through code in storage.js.​

Part 2: Finding Feature Implementation

If I need to add a new Export tasks to CSV feature, my first step is searching for any file operations or export code. I checked for words like export,save, file, and transform and found that task data handling mostly happens in storage.js.​

So, my guess is, if I want to export to CSV, I should add code in storage.js since it already loads and saves tasks. I would probably need to write a new function to turn the tasks into CSV format, and maybe update cli.js so users can run this as a command.​

Part 3: Understanding Domain Model

The main item in this app is a “Task.” Each task can have a title, description, status (like “todo,” “in progress,” “review,” “done”), priority (low, medium, high, urgent), a due date, and some tags. The business rules (how tasks move between different statuses) are defined mostly in the models and main logic.​

My questions here: Is there any restriction on tags? Can priorities be changed once a task is created? How is overdue tracked in the system?

Part 4: Practical Application
For the scenario: Tasks overdue for more than 7 days should be marked as abandoned unless they are high priority, I would:

Update the logic in models.js and storage.js to check for overdue tasks each time the app loads or tasks are listed.

Mark any task overdue for more than 7 days as abandoned, except if its priority is high or urgent.

I think I would need my team’s help on where the best place to trigger this check is, should it run automatically, or only when tasks are listed or updated?

Reflection
The prompts really helped me break down the process. Before starting, I felt lost, but using clearly structured steps like looking at main files first and searching for features instead of just reading all the code made it much easier. I still want to dig deeper into how tasks are actually updated and validated, but I now know where to look and how to ask for help if I need it.​

Next time, I’ll start with the README and file names, then search for  logic keywords to get a rough idea before going into details. Using AI prompts or searching with smart keywords was faster than skimming every single file.