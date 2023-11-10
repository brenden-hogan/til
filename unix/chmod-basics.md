# CHMOD Basics

Allows you to change the ACL to files on the file system.

Unix files have an ACL that allows Read/Write/Execute permissions to file owners/group members/others

You can see this each time you do a `ls -l` and look at the letter proceeding each file/dir

```
Owner | Group | Others
 rwx  |  rwx  | rwx 
```

r = read, w = write, x = execute

Each can be specified in binary or octal
```
000 = 0 = ---
001 = 1 = --x
010 = 2 = -w-
011 = 3 = -wx
100 = 4 = r--
101 = 5 = r-x
110 = 6 = rw-
111 = 7 = rwx
```

Using the octal value in chmod lets us specify the access level for owner/group/others quickly
```
chmod 751 <fileName>
# results in
rwxr-x--x <fileName>
```

Generally using modes is recommended though. The following should do the same and is easier to read if you aren't as comfortable with binary
```
chmod u=rwx,g=rx,o=x <fileName>
```


