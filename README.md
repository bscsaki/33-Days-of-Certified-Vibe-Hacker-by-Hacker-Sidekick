  
**Hello there\!**  
My name is Sofia and in this series of posts I will provide step by step guidance on how to solve the [Certified Vibe Hacker Workshop](https://certifiedvibehacker.com/) by [Hacker SideKick](https://hackersidekick.com/). It is a Capture The Flag style activity. Over the next 33 days I will go through each challenge using Hacker SideKick Integrated Hacking Environment IHE as support, and share the research and findings along the way.

**Why am I doing this?**  
The purpose of this series is to learn and provide material to the community that other beginners may find useful. I also want to showcase how to use Hacker SideKick AI as a tool while still doing the actual learning yourself.

Another reason I have wanted to do this is because I have been and keep hearing online, and at live events, that my generation of 2026 BS in Computer Science graduates doesn’t know how to code or do anything really without AI.  
Claude was down last week and I am sure a lot of people started panicking and blankly staring at the status page of Anthropic.  
By a lot of people I am talking about myself, and I wish I was joking. Once we begin to over rely on AI for basic functions, what happens when it is unavailable or less widely accessible? That same day Scott Hanselman was speaking at the Chicago Cloud Computing meetup and was calling out this same thing. One quote stayed with me and that was "Do you know you can use airplane mode when you are not flying? Turn it on and code." My generation is able to 'skip' and press 'play forward x10' on a lot of crucial learning moments and pitfalls that come with experience. A good resource to check out is this [video](https://www.youtube.com/watch?v=HTUh0OO6Kmo) by JetBrains named "What AI does to the minds of novice coders," and there is also real research from credible institutions on the term coined as "[Cognitive Debt](https://arxiv.org/abs/2506.08872)." 

TO BE CLEAR I am not against AI. I support AI technology and want to explore better, more mindful ways of using AI, less brainrot (:

**My CTF Journey**  
I have participated in a few CTFs and AI has taken some of the joy of trying or having the mere illusion or possibility that you might win on your own merit. Knowing that the majority of the contestants are using AI, and so am I, only adds more pressure and makes the clock go by faster. As someone who knows little about it I have been using AI against my own learning.   
You see, when you are competing against time and the powerful OpenClaw agents that some genius with real skills and grit deployed… is solving all the challenges in parallel you are already defeated and inevitably turn into a slower copycat of scrappier quality. Unfortunately, just pasting logs or files into an AI and getting a solution back does not mean you actually know how to solve that kind of problem. The test is simple; could you solve it again, alone, without that help? In a time crunch the honest answer is no because during the competition finding the flag matters more than learning or understanding how you got there. 

**The Rules**  
So these 33 days are my effort to enjoy the journey and not the destination of CTFs. I want to slow down and dive into each challenge to understand what is going on behind the scenes. Without a clock on top running or an automated script outpacing me in the background.   
The rules I am setting for myself are simple:

\- Use Hacker SideKick when I am genuinely stuck and my own effort reaches 0%.  
\- Use it for questions and explanations, not full solutions.  
\- I know that Hacker SideKick can solve every challenge instantly. That is not the point. The point is to see how far I can get with Hacker SideKick beside me and my own brain in charge.

**Rights & Disclosure**

All rights to the Certified Vibe Hacker Workshop belong to Hacker SideKick. What I am sharing here is my own framework and process for approaching each challenge. I will not reveal the flag itself or its exact location.

Everything written in these posts is written by me, in my own words, without AI generating or drafting the text.

Any comments or concerns let me know at sofia@sofiabatala.com and check out my [Portfolio...](https://www.sofiabatala.com)

**What’s Next?**

Follow along for a new step by step writeup each day. Below are the 33 days along with the categories and sections I will be working through.  
I will keep this list updated as the weeks go on.

| CATEGORIES |  |
| :---- | :---- |
| **LOG ANALYSIS** | The collection, parsing, filtering and examination of log data from systems, applications, and networks. Logs are machine generated event records created for, **Access logs** are the answer to who, what, when, and how accessed a resource. **Error logs** record failures, exceptions or atypical conditions. Ie an application crash. **Event logs** cover changes and occurrences across the OS or system. For example: OS logs: Windows Event Logs or Linux syslog Network logs: Firewalls, VPNs Identity logs: Active Directory Services logs: Web Servers Cloud logs: Azure Activity |
| Used for | Security monitoring & detection  Incident response & Forensics Compliance & Auditing Operations Threat Hunting  |
|  |  |
| **WEB APPLICATION PENTEST** | The identification, exploitation, and report of security vulnerabilities in a web application. The goal is to assess the security posture of the application and follows this process, **Reconnaissance** involves gathering information about a target before launching an attack. e.g. running subfinder to enumerate subdomains or checking out robots.txt. **Vulnerability Identification** is finding flaws and security gaps i.e. discovering an sql injection vulnerability by plugging \<' OR 1=1-- \> into the login form. **Threat Modeling** assesses the potential vulnerabilities found and their severity and risk they pose before moving on. Ie how far can this vulnerability go? **Exploitation** validates the vulnerabilities by demonstrating that they are exploitable and unsafe. e.g. using a confirmed SQLi to dump a table of usernames or password hashes via sqlmap. **Post- Exploitation**… |
| Used for | Finding exploitable vulnerabilities before attackers do Validating security controls Compliance requirements Responsible Disclosure & Bug Bounties  |
|  |  |
| **STATIC CODE ANALYSIS** | The method of examining source code without executing or running a program. The goal is to catch bugs, problems, inconsistencies earlier and setting a strong foundation for your code. This step is applied at the implementation phase of a Security Development Lifecycle (SDL). The output is usually reports and warnings of the code insecurities or problems. There are various types of static code analysis, **Lexical Analysis**, breaks code into tokens, identifiers, keywords… and looks for invalid or incorrect use **Syntax Analysis** aka **Parsing**, builds a parse tree or abstract syntax tree a structured representation of code based on the grammar to ensure it adheres to syntax rules of language. **Semantic Analysis** interprets the meaning of syntactically correct code by enforcing rules on types and symbols. **Control Flow Analysis**, generates a control flow graph to map the different execution paths of the program. This helps identify code that is unreachable, never executed, infinite loops. **Data Flow Analysis** tracks how the data is used. If it is defined, initialised, used, propagated. If there are unused variables, or null pointer references… **Pattern-Based Analysis** operates on matching code blocks to regular expressions or abstract syntax patterns and looks for mismatches such as improper error handling. There is also dynamic analysis, which is analysis when the code is being executed. This approach can find memory leaks, logic flaws and the output is the runtime behaviour, logs (like error logs mentioned before) and other metrics.  |
| Used for | The two approaches have different use cases, with static analysis developers save on system resources and catch mistakes in the development process. Versus with dynamic analysis developers catch problems that need real input and environment conditions to be identified or triggered.   Some more use cases for static code analysis: Security vulnerability detection Coding standards enforcement Code quality metrics Dead code Identification |



|  | Certified Vibe Hacker \- Challenges | SOLUTION |
| :---- | :---- | :---- |
| **A** | **LOG ANALYSIS** |  |
| 1 | Log Analysis 1 |  |
| 2 | Log Analysis 2 |  |
| 3 | Log Analysis 3 | [Day 1]([https://certifiedvibehacker.com/](https://github.com/bscsaki/33-Days-of-Certified-Vibe-Hacker-by-Hacker-Sidekick/blob/main/Day1/Log%20Analysis%203.md) |
| 4 | Log Analysis 4 |  |
| 5 | Log Analysis 5 |  |
| **B** | **WEB APPLICATION PENTEST** |  |
| 6 | Hardcoded Secret |  |
| 7 | SQL Injection \- Login Bypass |  |
| 8 | Path Traversal |  |
| 9 | MML External Entity (XXE) Injection |  |
| 10 | Server-Side Request Forgery (SSRF) |  |
| 11 | Broken Access Control \- Admin Panel |  |
| 12 | Cross-Site Scripting (XSS) \- Stored |  |
| 13 | Insecure Direct Object Reference (IDOR) |  |
| 14 | Weak Password Reset |  |
| 15 | Command Injection |  |
| **Γ**  | **STATIC CODE ANALYSIS** |  |
| 16 | Weak Password Storage |  |
| 17 | SQL Injection |  |
| 18 | Open Redirect |  |
| 19 | Cross-Site Scripting (XSS) |  |
| 20 | Path Traversal |  |
| 21 | Weak Random Number Generation |  |
| 22 | Command Injection |  |
| 23 | Session Management Vulnerabilities |  |
| 24 | Mass Assignment |  |
| 25 | Missing Input Validation |  |
| 26 | Insecure Cookie Configuration |  |
| 27 | Missing Authorization |  |
| 28 | XML External Entity (XXE) |  |
| 29 | Insecure Deserialization |  |
| 30 | Sensitive Data Exposure |  |
| 31 | Server-Side Request Forgery (SSRF) |  |
| 32 | Cross-Site Request Forgery (CSRF) |  |
| 33 | Insecure Direct Object Reference (IDOR) |  |

Bibliography:  
Log Analysis:  
[https://www.ibm.com/think/topics/log-analysis](https://www.ibm.com/think/topics/log-analysis)  
[What Is Log Analysis Benefits and Uses in Cybersecurity | Huntress](https://www.huntress.com/cybersecurity-101/topic/what-is-log-analysis)   
[nistspecialpublication800-92.pdf](https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-92.pdf)   
Web App Testing  
[https://owasp.org/www-project-web-security-testing-guide/stable/4-Web\_Application\_Security\_Testing/00-Introduction\_and\_Objectives/README](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/00-Introduction_and_Objectives/README)

[Web Application Pentests & The Basics | by Mike Smith | Medium](https://medium.com/@mikesmith12137/web-application-pentests-the-basics-1c576720558b)   
[What is Cyber Reconnaissance?](https://www.sentinelone.com/cybersecurity-101/threat-intelligence/what-is-cyber-reconnaissance/)   
Static Code Analysis  
[What is Static Code Analysis? | JetBrains Qodana](https://www.jetbrains.com/pages/static-code-analysis-guide/)   
[Static Code Analysis | OWASP Foundation](https://owasp.org/www-community/controls/Static_Code_Analysis)   
[Static Code Analysis: Top 7 Methods, Pros/Cons and Best Practices](https://www.oligo.security/academy/static-code-analysis)   
Jet Brains and MIT study  
[\[2506.08872\] Your Brain on ChatGPT: Accumulation of Cognitive Debt when Using an AI Assistant for Essay Writing Task](https://arxiv.org/abs/2506.08872)   
https://www.youtube.com/watch?v=HTUh0OO6Kmo   
