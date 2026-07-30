### **\#Day 28: Command Injection \- Hacker Sidekick Certified Vibe Hacker CTF Walkthrough**

**\#\# Challenge:** Command injection vulnerability allows arbitrary command execution through unsanitized user input passed to shell command. The flag is stored in the environment variable CTF\_FLAG Execute commands via the /command endpoint to read the environment variable

Today’s challenge belongs to the Web App Pentesting category. Given that Hacker Sidekick is launching their new environment, I wanted to try it out and use Hacker Sidekick as my agentic workbench to help me execute this attack.

In the following days I will also be solving the Command Injection challenge that is part of the Static Code Analysis track and that will help us better understand what the vulnerable backend looks like for such an attack to be executed.

**\#\# Methodology:**  
I started solving today's challenge by navigating to the Integrated Hacking Environment IHE and giving instructions to Hacker Sidekick. One of the biggest differences in this new launch is the IHE which has received a makeover.  
Hacker Sidekick used to be built on VS Code. The new environment has an incredible user flow and is great for a visual learner like me.

So here is the methodology that Hacker Sidekick followed to solve the challenge and locate the today’s flag:

1. Navigate to **[https://certifiedvibehacker.com/command](https://certifiedvibehacker.com/command)**  
2. Send **POST** requests with the **command** parameter to execute shell commands  
3. Execute **pwd** \- confirm your current location in the system /app  
4. Execute **ls \-la** \- list all files, hidden files and permission and ownership details of current directory  
5. Execute **whoami** \- confirm the application's process was running as root inside the container  
6. Execute cat **FLAGS.md** \- check other flags living inside the web app  
7. Execute echo **$CTF\_FLAG** \- retrieve the flag

Watching the agent work through the conversation panel let me see its thinking process in real time, not just the final flag it landed on. Hacker Sidekick shared its reasoning as it went, which meant I could evaluate each step for myself while watching it hack the live site, and step in with my own judgment if I wanted to steer it in a different direction rather than just letting it run. That visibility is what made this insightful.

Between steps 1 and 2, this is what the agent was thinking:   
*Let me read the HTML more carefully to see if there's any hidden text:Let me try to execute a command through the URL and see what happens:Let me check the response after refreshing:*

*Let me see the response from the server directly:Interesting \- the /command endpoint responds differently when directly navigated vs when accessed via fetch. The direct navigation gives a minimal page, but fetch showed it gives 200 with empty body on POST.*

And so seeing this versus simply waiting for an agent makes a difference and also gives you the opportunity to understand what the agent is doing if you want to learn\!

Here is a screenshot of the workbench, where you can see this in action. On the center screen the browser panels host the raw terminal output of each command as Hacker Sidekick sent the requests to the live app. And you can see the commands that took place after with the final echo $CTF\_FLAG call that located the flag to today’s challenge. On the right side there is the agent chat panel where the agent narrates the results of the raw terminal and explains each step taken. Seeing both side by side, the raw output and the agent’s explanation helped turn this into an actual learning exercise.

**\#\# The why:**  
Command injection happens when an application builds a shell command using untrusted input and plugs that string into the OS without properly separating the data from the executable syntax. The shell doesn’t know the difference between a value the developer intended and one supplied by an attacker and will interpret everything that is thrown at it. This weakness is classified by CWE 78: Improper Neutralization of Special Elements used in an OS Command, and OWASP groups it under A03:2021 Injection in the Top 10\. This is the same broad category as SQL injection, since both stem from the same root problem of untrusted input being treated as executable syntax instead of plain data. Unlike an SQLi which is limited to the contents of the database, a command injection is bounded by the OS-level permissions of the process running it, but even within those bounds it reaches the filesystem, environment variables, and running processes, far more surface area than a database exposes.

**\#\# Prevention:**  
OWASP's OS Command Injection Defense Cheat Sheet lists out 3 primary defences,

1. **Avoid calling OS commands directly**. This is the strongest solution since it removes the problem entirely. Built in library functions can be used as an alternative and cannot be manipulated into executing commands they are not meant to do. i.e. instead of running system(“mkdir /name”) call mkdir() function.  
2. **Add escape values to OS commands specific to each OS**. If OS commands have to be used, then escaping is the solution as it surrounds the user’s input and the shell will treat it as a literal value versus executable syntax.  
3. **Parameterization and Input Validation**. The former means using structured mechanisms that automatically separate data and commands. This prevents any string concatenation. The latter means checking both the command and its arguments. The command can be compared against an allow-list of permitted commands and each argument needs to be validated against regular expression rules.  
   . 

One last recommendation is to run the application with the lowest privileges required for a task. In today’s example the whoami showed that the agent had root access inside the container. As a user account, the process had far more power than the task required. Least privilege wouldn’t have stopped the attack, but it could have limited what the agent was able to do once inside the app.  
Tomorrow, Day 29, we'll explore these prevention methods in more depth as we look at tthe backend that's vulnerable to command injection.

**\#\# Summary:**  
In this challenge of [Certified Vibe Hacker Workshop](https://certifiedvibehacker.com/) by [Hacker Sidekick](https://hackersidekick.com/) we explored, the new Integrated Hacking Environment where the agent walked through a command injection attack against the /command endpoint and retrieved today's flag by reading it out of an environment variable. The new launch has a smooth workflow and getting to watch the agent’s reasoning live is a highlight for me.

**\#\# Bibliography:**  
[OS Command Injection Defense \- OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html)   
[What is OS command injection, and how to prevent it? | Web Security Academy](https://portswigger.net/web-security/os-command-injection)   
[4 essentials to prevent OS command injection attacks | Red Hat Developer](https://developers.redhat.com/articles/2023/03/29/4-essentials-prevent-os-command-injection-attacks#how_os_command_injection_works)   
[CWE \- CWE-78: Improper Neutralization of Special Elements used in an OS Command ('OS Command Injection') (4.20)](https://cwe.mitre.org/data/definitions/78.html) 

