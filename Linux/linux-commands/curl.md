> **curl** --> A tool for transferring data from or to a server using URLs. It supports 28 protocols.

1. **Websites** --> `curl <website_url>`: Returns the body/source code of the URL.
2. **APIs** --> `curl <api_url>`: Will return the JSON returned from the API.
3. **Local Files** --> `curl <file_url>`: Returns the content of the file.

| Flag | What it does                                       | Example                                                                           |
| ---- | -------------------------------------------------- | --------------------------------------------------------------------------------- |
| `-I` | Fetch only HTTP headers                            | `curl -I https://example.com`                                                     |
| `-i` | Fetch headers and body                             | `curl -i https://example.com`                                                     |
| `-O` | Save the file using the original server filename   | `curl -O https://example.com/index.html`                                          |
| `-o` | Save output to the specified filename              | `curl -o mypage.html https://example.com`                                         |
| `-k` | Ignore SSL certificate errors                      | `curl -k https://self-signed.badssl.com/`                                         |
| `-v` | Verbose (detailed request/response)                | `curl -v https://example.com`                                                     |
| `-L` | Follow redirects like HTTP → HTTPS                 | `curl -L http://example.com`                                                      |
| `-d` | Attach data to be sent and implies a POST request. | `curl -d "param1=value1&param2=value2" https://api.example.com/submit`            |
| `-X` | Specify the HTTP method (PUT, DELETE etc.)         | `curl -X DELETE https://api.example.com/123`                                      |
| `-H` | Headers: Pass tokens, content type, cookies, etc.  | `curl -X POST -H "Content-Type: application/json" https://api.example.com/submit` |
| `-u` | Use basic HTTP auth.                               | `curl -u username:password https://example.com`                                   |

| Flag                | What it does                                            | Example                                                 |
| ------------------- | ------------------------------------------------------- | ------------------------------------------------------- |
| `-F`                | Multipart form upload (like file uploads in HTML forms) | `curl -F "file=@myfile.txt" https://example.com/upload` |
| `-s`                | Silent mode: don’t show progress or errors              | `curl -s https://example.com`                           |
| `-S`                | When used with `-s`, show errors if they happen         | `curl -S https://example.com`                           |
| `-w`                | Format output: show HTTP status code, timing, etc.      | `curl -w "%{http_code}\n" https://example.com`          |
| `-D`                | Save headers to a file                                  | `curl -D headers.txt https://example.com`               |
| `--cert`            | Use a client certificate                                | `curl --cert mycert.pem https://secure.example.com`     |
| `--cacert`          | Use a specific CA bundle                                | `curl --cacert ca.pem https://example.com`              |
| `--max-time`        | Limit the total time allowed for the request            | `curl --max-time 10 https://example.com`                |
| `--connect-timeout` | Limit connection time                                   | `curl --connect-timeout 5 https://example.com`          |
| `--retry`           | Retry up to `<num>` times on transient errors           | `curl --retry 3 https://example.com`                    |
```bash
# Follow redirects, silent mode and format output.
curl -Ls -w "%{http_code}\n" https://example.com

# Silent, download to a file.
curl -s -o output.html https://example.com

# Upload JSON securely with auth.
curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer <token>" -d '{"key":"value"}' https://api.example.com/submit
```
