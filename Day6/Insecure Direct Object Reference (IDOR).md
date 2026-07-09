### **Day 6: Insecure Direct Object Reference (IDOR)**

### 

**Challenge:** IDOR vulnerability allows accessing any user's data by manipulating the id parameter without proper authorization checks. Access the API endpoint with user ID 1: /api/user?id=1 The flag is included in the JSON response for user ID 1\.

**Methodology:**

1. Fairly simple, plug in the **/api/user?id=1** after the website name and you will get back a json response of the flag and the user info. 

![idoor](images/idoor.png)
![idoor2](images/idoor2.png)


Just like in [yesterday’s](https://medium.com/@bscsaki/day-5-server-side-request-forgery-ssrf-affe57809052?sharedUserId=bscsaki) example the breakdown of the URL looks like this,  
**/api/user**, is the path and the endpoint of the object we are looking for. However it is an API request and the user is the resource.  
**?**, this marks the start of the string query   
**id=1**, is the parameter of the query, id is the key and 1 is the value

**The why:**  
The Insecure Direct Object Reference CWE-639 is an access control vulnerability that happens when user input is used to access/modify objects directly by manipulating the URL. According to OWASP this attack consists of 3 parts:

1. The **Object** \= an account, document, user profile ie. **user**  
2. The **reference** \= the ID, UUID, token… version of that object ie. **id=2**   
3. **No object level authorization check** \= the user being able to read/modify an object they shouldn’t ie. **the flag**

There is also other types of IDOR attacks such as,

- **Body Manipulation**, when the object reference is included inside the HTTP request body  
- **Cookie or Json ID manipulation**, when either one is manipulated for authentication purposes  
- **Path Traversal**, when file paths are manipulated to gain access to unauthorised locations and can also read files.


**Prevention:**

- Object level authorization, making sure every request is authenticated and authorised to access this specific resource/object   
- Indirect Object Reference Maps, instead of **/api/user?id=1** use **/api/user?id=7f3a9c21**. So instead of using real internal identifiers the path uses a placeholder value. The map part pertains to the mapping of **7f3a9c21** to **1** for a specific session  
- Parameter Verification, making sure the user’s input is sanitised and validated. That the format and contents are valid. This measure however only checks the format of the answer and not if it is authorised and therefore is not really a solution.


  
**Summary:**  
In this challenge of [Certified Vibe Hacker Workshop](https://certifiedvibehacker.com/) by [Hacker Sidekick](https://hackersidekick.com/) we performed a simple IDOR attack and got back a json response of the user email, id, role, username, and flag.

**Bibliography:**  
[**Insecure direct object references (IDOR) | Web Security Academy**](https://portswigger.net/web-security/access-control/idor)   
[**Insecure Direct Object Reference Prevention \- OWASP Cheat Sheet Series**](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)   
[**MITRE. CWE 639: Authorization Bypass Through User Controlled Key**](http://cwe.mitre.org/data/definitions/639.html)  
[**Insecure Direct Object Reference (IDOR) | Best Practices | Imperva**](https://www.imperva.com/learn/application-security/insecure-direct-object-reference-idor/)    
[**Insecure Direct Object Reference IDOR Vulnerability Prevention | EC-Council**](https://www.eccouncil.org/cybersecurity-exchange/web-application-hacking/idor-vulnerability-detection-prevention/) 
