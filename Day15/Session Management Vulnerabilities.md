### **\#Day 15: Session Management Vulnerabilities \- Hacker Sidekick Certified Vibe Hacker CTF Walkthrough**

**\#\# Challenge:** The application generates session IDs using predictable random number generation, making sessions vulnerable to hijacking.What is the exact method name in AuthService.java that generates session IDs using Math.random() instead of SecureRandom?

Today’s challenge belongs to the static code analysis category and it’s a funny surprise. It’s the same challenge as [Day 13: Weak Random Number Generation](https://github.com/bscsaki/33-Days-of-Certified-Vibe-Hacker-by-Hacker-Sidekick/blob/main/Day13/Weak%20Random%20Number.md) so go there to see the full walkthrough on how to solve today’s challenge and understand why Math.Random() is a non cryptographic algorithm. 

