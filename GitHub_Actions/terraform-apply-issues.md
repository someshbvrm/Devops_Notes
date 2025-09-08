Here is the transcribed content from your image in a clean, plain text format suitable for a notepad:

***

### Common Errors/Issues

**1. Setup Terraform Error**  
**Error:**  
`Error: Unable to locate executable file: unzip. Please verify either the file path exists or the file can be found within a directory specified by the PATH environment variable. Also check the file mode to verify the file is executable.`

**Solution:**  
```bash
sudo apt update  
sudo apt install unzip
```

---

**2. Terraform Init Error**  
**Error:**  
`0s Run terraform init /usr/bin/env: ‘node’: No such file or directory Error: Process completed with exit code 127.`

**Solution:**  
```bash
sudo apt update  
sudo apt install nodejs  
sudo ln -s /usr/bin/nodejs /usr/local/bin/node
```