# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
## AIM :
To write a PYTHON program for socket for HTTP for web page upload and download
## Algorithm

1.Start the program.
<BR>
2.Get the frame size from the user
<BR>
3.To create the frame based on the user request.
<BR>
4.To send frames to server from the client side.
<BR>
5.If your frames reach the server it will send ACK signal to client otherwise it will send NACK signal to client.
<BR>
6.Stop the program
<BR>
## Program 
Client
```
import socket

def send_request(host, port, request):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        s.sendall(request.encode())
        response = s.recv(4096).decode()
    return response

def upload_file(host, port, filename):
    with open(filename, 'rb') as file:
        data = file.read()
        content_length = len(data)

        request = (
            f"POST /upload HTTP/1.1\r\n"
            f"Host: {host}\r\n"
            f"Content-Length: {content_length}\r\n"
            f"Content-Type: text/plain\r\n\r\n"
        )
        request += data.decode(errors="ignore")

    return send_request(host, port, request)

def download_file(host, port, filename):
    request = f"GET /{filename} HTTP/1.1\r\nHost: {host}\r\n\r\n"
    response = send_request(host, port, request)

    parts = response.split("\r\n\r\n", 1)
    if len(parts) > 1:
        content = parts[1]
        with open("downloaded_" + filename, "wb") as f:
            f.write(content.encode())
        print(f"{filename} downloaded successfully.")

if __name__ == "__main__":
    host = "example.com"
    port = 80

    upload_response = upload_file(host, port, "example.txt")
    print("Upload response:", upload_response)
    download_file(host, port, "example.txt")

```

Server
```
from http.server import BaseHTTPRequestHandler, HTTPServer
import urllib

class SimpleUploadServer(BaseHTTPRequestHandler):
    def do_POST(self):
        if self.path == "/upload":
            content_length = int(self.headers.get('Content-Length', 0))
            post_data = self.rfile.read(content_length)

            with open("uploaded_file.txt", "wb") as f:
                f.write(post_data)

            self.send_response(200)
            self.send_header("Content-Type", "text/plain")
            self.end_headers()
            self.wfile.write(b"File uploaded successfully")
        else:
            self.send_response(404)
            self.end_headers()

    def do_GET(self):
        path = urllib.parse.unquote(self.path.lstrip("/"))
        try:
            with open(path, "rb") as f:
                data = f.read()
            self.send_response(200)
            self.send_header("Content-Type", "application/octet-stream")
            self.send_header("Content-Length", str(len(data)))
            self.end_headers()
            self.wfile.write(data)
        except FileNotFoundError:
            self.send_response(404)
            self.end_headers()

server = HTTPServer(('localhost', 8000), SimpleUploadServer)
print("Server running at http://localhost:8000")
server.serve_forever()

```

## OUTPUT
<img width="1039" height="367" alt="image" src="https://github.com/user-attachments/assets/0049ddc5-2765-4799-9a51-9b47a188d98e" />

## Result
Thus the socket for HTTP for web page upload and download created and Executed
