# -EXP-5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
## Name: LATHIKA .K
## Reg.no:212224230140
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
```
import socket
import os

HOST = '127.0.0.1'  # Localhost
PORT = 8000         # Port number

def handle_get_request(client_socket, requested_file):
    if os.path.isfile(requested_file):  # Check if the requested file exists
        with open(requested_file, 'rb') as f:
            response_body = f.read()
        response_header = 'HTTP/1.1 200 OK\r\n'
        response_header += 'Content-Type: text/html\r\n'
        response_header += f'Content-Length: {len(response_body)}\r\n\r\n'
    else:
        response_body = b"<h1>404 Not Found</h1>"
        response_header = 'HTTP/1.1 404 Not Found\r\n'
        response_header += 'Content-Type: text/html\r\n'
        response_header += f'Content-Length: {len(response_body)}\r\n\r\n'

    client_socket.sendall(response_header.encode() + response_body)

def handle_post_request(client_socket, request):
    try:
        headers, body = request.split(b"\r\n\r\n", 1)
        filename = "uploaded_file.html"  # Save as a default file
        
        # Save uploaded content to file
        with open(filename, 'wb') as f:
            f.write(body)
        
        response_body = f"<h1>File uploaded successfully: {filename}</h1>".encode()
        response_header = 'HTTP/1.1 200 OK\r\n'
        response_header += 'Content-Type: text/html\r\n'
        response_header += f'Content-Length: {len(response_body)}\r\n\r\n'
        
        client_socket.sendall(response_header.encode() + response_body)
    except Exception as e:
        print(f"Error handling POST request: {e}")
        client_socket.sendall(b"HTTP/1.1 500 Internal Server Error\r\n\r\n")

def start_server():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server_socket:
        server_socket.bind((HOST, PORT))
        server_socket.listen(5)
        print(f"Server started at http://{HOST}:{PORT}")

        while True:
            client_socket, client_address = server_socket.accept()
            with client_socket:
                print(f"Connection from {client_address}")

                # Receive the request data from the client
                request = client_socket.recv(1024)

                if not request:
                    continue

                request_lines = request.split(b"\r\n")
                request_type = request_lines[0].split(b" ")[0]  # e.g. "GET" or "POST"
                requested_file = request_lines[0].split(b" ")[1].decode()[1:]  # Extract file name

                if request_type == b"GET":
                    if requested_file == '':
                        requested_file = 'index.html'  # Default to index.html if no file specified
                    handle_get_request(client_socket, requested_file)

                elif request_type == b"POST":
                    handle_post_request(client_socket, request)
if __name__ == "__main__":
    start_server()

```

## OUTPUT

![Screenshot 2025-05-01 184547](https://github.com/user-attachments/assets/cf834a6d-5fa8-47a6-854f-12b9a0861c43)


## Result
Thus the socket for HTTP for web page upload and download created and Executed
