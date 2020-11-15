# Space Villains

The clue is:

> Don't even bother. You need admin access for this.

Well, that sounds like a challenge...

The link takes you to the Budget Bank website. If you are going in order of point value, this is your first exposure to Budget Bank, an intentionally insecure website that is used to acquire a few flags in this challenge.

The major clue is "admin acess". How many sites have an "admin" page? Just all of them right?  So the thinking here is that you will try navigating to /admin even though there are no hard links to it. This is a forced browsing attack and scanners like OWASP Zap can uncover these obfuscated resources so never rely on such primitive security techniques.

[try it out](https://budgetbank.azurewebsites.net/admin/)