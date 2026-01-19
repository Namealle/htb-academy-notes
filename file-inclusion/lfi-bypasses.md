# LFI bypasses
### What I learned
- Path traversal filters can be bypassed with ....// as is server is configure to remove `../` by adding addition dots and slash line after removing `../` from `....//` we are left with `../` which is exactly what we want to go directory back.
-  URL encoding can bypass blacklists
- Server can use several types of filtering to prevent LFI
- Code disclosure is possible using base64 filters.
```url
php://filter/read=convert.base64-encode/resource=phpfilename
```
and the decode the base64 to view potentially useful information.
- ffuf can be used not only for directory or subdomains fuzzing.
### What confused me
- Thought that non-recursive removal removes only first specified character for example `../` so it would be possible to by pass it with just `....//../../../etc/passwd` but found out that it delete each `../` in a text and not just once so `....//....//....//....//etc/passwd` is required to bypass non-recursive removal.
-  could not figure out why `curl -s 'http://94.237.61.249:53726/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=cat flag.txt` does not work latter found found out that all space in url should be replaced with `%20`. 

### What I'd do differently
- Test one bypass technique at a time instead of combining them randomly 
- Check PHP version/config before trying RCE methods
