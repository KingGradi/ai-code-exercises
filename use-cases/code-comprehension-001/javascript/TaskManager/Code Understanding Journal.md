Part 1: Understanding a Specific Feature

When I started exploring the code, I focused on task creation and how status updates work. I looked for files like models.js for the Task definition, cli.js for command handling, and storage.js for saving and loading tasks. I used the AI strategy of first knowing where to start, so I didn't try to understand everything. I just checked the file names and quickly read the README to get an idea of what each part does.​

To understand task creation, I searched for keywords like create, add, and status in both cli.js and models.js. Then, I asked the AI to explain how these parts work together when I run the create command. From this, I learned that the CLI command creates a new Task (with a title, description, etc.), updates its status in the Task model, and saves it using Storage. The data is stored in a file, and each update is written back.​

Part 2: Deepen Understanding Through Guided Questions

Next, I wanted to know how task prioritization works. I told the AI my first impression (having read some code): priorities like low, medium, high, and urgent are set when you create a Task, and are just stored as fields. But I was confused if the priority affects sorting, overdue logic, or anything special.

The AI asked me questions such as:

Does changing a task's priority affect its order in task listings?

Are there places in the code where priority triggers something else?

What happens when a high priority task is overdue?

After answering and double checking the code, I realized priorities don't trigger extra logic by default but might be used for future features or reporting. The guided questions helped me see where I had assumptions that weren't backed up by code.​

Part 3: Mapping Data Flow

To map data flow when a task is marked complete, I started from the CLI command (complete or update status), which leads into the models where the Task's status is changed and then to storage.js for saving. I drew myself a little flow diagram: 
User CLI → cli.js → models.js (updates object) → storage.js (writes to file).

The AI helped me break this down further with prompts like where is the input handled?, Who changes the task's status?, and How is it persisted? I noted possible failure points like if file saving fails, or if the status update is invalid. I also learned that state changes mainly live in the model and storage layers.​

Part 4: Reflection and Presentation

Stepping back, I feel I now understand the application's structure much better. Task creation starts in the CLI and runs through the Task model, then to file storage. Prioritization is a simple field for now, but could be used for sorting or alerting later. Completing a task means updating the status and saving that change.​

One design approach I liked is how the code keeps model logic (what a Task is and how it updates) separate from storage which how tasks are saved, making changes easier. The toughest part was at first just figuring out where things happened looking at file names, using smart keyword searches, and asking pointed questions made it much easier. Using AI prompts for guided questions and clear explanations made me less worried about missing something small or making wrong assumptions.​

If I did this with a team, I'd present my journey of exploring first with file names, then searching keywords, and using guided questions to dig deeper. I'd explain how AI helped me clarify uncertainties and nudged me to check my reasoning whenever I was unsure.

That’s how I would complete and reflect on the exercise, relating it to using AI for code comprehension.