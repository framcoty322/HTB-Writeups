[https://github.com/s4vitar/rpcenum](https://github.com/s4vitar/rpcenum)

#rpcenum

![](<attachments/Pasted image 20260101181315.png>) ![](<attachments/Pasted image 20260101181332.png>)

Let's dump all users into a file and clean it up:


```bash
# Extract only the columns with names (between the bars |) 
cat users | awk -F'|' '{print $2}' | grep -v "^[[:space:]]*$" | grep -v "Users" > temp.txt
```

![](<attachments/Pasted image 20260101181606.png>)