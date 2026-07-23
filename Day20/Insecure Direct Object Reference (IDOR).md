### **\#Day 20: Insecure Direct Object Reference (IDOR) \- Hacker Sidekick Certified Vibe Hacker CTF Walkthrough**

**\#\# Challenge:** Users can access resources by directly referencing object IDs without authorization checks to verify ownership. What endpoint path allows downloading files by file ID without verifying the requesting user owns the file? Examine FileController.java and look for GET mapping annotations that accept a file ID path variable. 

Today’s CTF challenge belongs to the Static Code Analysis category and after having seen what it is like to exploit such a vulnerability on [Day 6](https://bscsaki.medium.com/day-6-insecure-direct-object-reference-idor-24aff88c1dd8) let’s explore the back end.

**\#\# Methodology:**  
I followed the guidance of the challenge description, opened   
The file has 6 @GetMapping methods in total, a mix of view rendered pages, downloads, and JSON API endpoints,  As you can see in the following image this method meets both criteria, of the download endpoint and no fileId check. 

![getflag](imgages/getflag.png)

Before breaking this down its worth mentioning that the **FileController.java** file is annotated with @RequestMapping(“/files”), meaning that all endpoints from the 6 @GetMappings would be appended to /files. @request mapping is a class and all its methods inherit its path.   
The method gets the current user id from the cookie session and returns 401 if it comes back null, so an unauthenticated request is correctly rejected before anything else happens.   
Then is where the Insecure Direct Object Reference vulnerability lies as the file requested gets pulled with no verification on the user, the file their relationship and access to resources. 

So in action that would look like me logging into this app  and requesting files/download/1 and then requesting files/download/2 in the same session, and get a successful response.  
Another example was **/api/user?id=1** from day 6 when we were able to see different user information json files by changing the id number.

**\#\# The why:**  
IDOR happens because the code uses a direct reference to an object. fileId is a direct reference and points to the internal database primary key value. An indirect reference would be a random value token or a UUID, in contrast to direct the server maps this value to the internal id which then points to the database primary key value . Direct references like fileId are very predictable since they come from the database that follows a certain structure.  
The second part of IDOR is broken access control where the code does not check the user’s privileges and relationship with the objected requested.   
This vulnerability is classified under CWE: Authorization Bypass Through User Controlled Key. In simple terms the user is authenticated but not authorised, the app will know who the user is, but does not check or know if the userId matches the ownerId of the resource. This weakness  is subcategory of CWE 284: Improper Access Control.

The **FileController.java** file has another controller with the same vulnerability so 

@PostMapping("/delete/{fileId}")  
    public String deleteFile(@PathVariable Long fileId,  
                           HttpServletRequest request,  
                           RedirectAttributes redirectAttributes) {  
        Long userId \= getCurrentUserId(request);  
        if (userId \== null) {  
            return "redirect:/auth/login";  
        }  
          
        …  
          
        return "redirect:/files";  
    }

**\#\# Prevention:**  
Prevention:

- Object level authorization, making sure every request is authenticated and authorized to access this specific resource/object  
- Indirect Object Reference Maps, instead of files/download/9 use files/download/9f2a7b3e1c4d5f60. So instead of using real internal identifiers the path uses a placeholder value. The map part pertains to the mapping of 7f3a9c21 to 1 for a specific session. The externally mapped value is a randomly generated token that has no tie to the actual location of the object in the Database.

I also asked Hacker Sidekick for help. I instructed Sidekick to harden this method and it suggested these changes:

1\.  \*\*Repository-Level Scoping:\*\* The most robust fix is to modify the data access layer. Instead of \`fileRepository.findById(fileId)\`, use a scoped query like \`fileRepository.findByIdAndOwnerId(fileId, userId)\`. This ensures unauthorized rows are never even loaded into memory.  
2\.  \*\*Service-Level Authorization:\*\* If fetching the object first is necessary, explicitly validate ownership immediately after retrieval. Check if \`fileEntity.getOwnerId().equals(userId)\` before returning the data.  
3\.  \*\*Indirect Object References:\*\* Replace direct database IDs with indirect, randomized, and non-predictable references (e.g., UUIDs mapped to the actual ID) to limit the attack surface of simple enumeration.

**\#\# Summary:**  
In this challenge of [Certified Vibe Hacker Workshop](https://certifiedvibehacker.com/) by [Hacker Sidekick](https://hackersidekick.com/) we saw a file download endpoint that allows authenticated users to access unauthorised objects by changing the number id in the web request.

**\#\# Bibliography:**  
[CWE \- CWE-639: Authorization Bypass Through User-Controlled Key (4.20)](https://cwe.mitre.org/data/definitions/639.html)   
[CWE \- CWE-284: Improper Access Control (4.20)](https://cwe.mitre.org/data/definitions/284.html)   
[Insecure Direct Object Reference Prevention \- OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)   
[WSTG \- Latest | OWASP Foundation](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References)   
[Insecure Direct Object Reference (IDOR) | OWASP Foundation](https://owasp.org/www-community/attacks/insecure_direct_object_reference)   
[Insecure direct object references (IDOR) | Web Security Academy](https://portswigger.net/web-security/access-control/idor)   
[Mapping Requests :: Spring Framework](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html) 

