# Process and safety solo task
In this file is all information about the process and safety solo task from Anjo Bouwmeester (556058).
The task that needs doing:
Individually you will write a small PLC program of a self-chosen process (you may "translate" a personal routine into code) in Structured Text with supported documentation in GIT version control. 
The self-chosen process is about a student who is studying. The student wants coffee after a while.

## The PLC program must contain:

- **2 tasks running independently:**
    - Student learning `(PRG_Student)`
    - Coffee machine working `(PRG_Machine)`
- **1 Function called by at least one task:**
    - Calculating coffee brew time `(Calctime)`
- **A Global Variable List** from which the 2 tasks read and write variables.

## Your documentation must contain
- One State diagram for each task
- One Sequence diagram depicting the communication between the tasks
- One Sequence diagram showing the calling of the function by the task.


 
## The idea
- To get to all the requirements the following idea has been made.
    A student is studying and wants coffee. 
 -   2 tasks is made
 -    One student 
 - one the machine that makes coffee.
  -  The called function is the time that it takes to make the coffee. (tijd = sterkte * 1500) How stronger the cofee how longer the time.
