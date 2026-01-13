# Webserv

A custom HTTP/1.1 web server implementation written in C++98.

## Description

**Webserv** is a project that challenges you to build your own HTTP server from scratch, similar to Nginx. The server handles multiple client connections concurrently using non-blocking I/O, serves static and dynamic content, and is fully configurable through a configuration file.

## Features

- **HTTP/1.1 Protocol**: Compliant with RFC 7230-7235
- **HTTP Methods**: GET, POST, DELETE
- **Non-blocking I/O**: Uses `select()`, `poll()`, or `epoll()` for multiplexing
- **Multiple Servers**: Support for multiple server configurations
- **CGI Support**: Execute dynamic content (PHP, Python, etc.)
- **Configuration File**: Nginx-like configuration syntax
- **File Upload**: Handle file uploads via POST requests
- **Directory Listing**: Autoindex for directory browsing
- **Custom Error Pages**: Configurable error pages for different status codes
- **Multiple Ports**: Listen on multiple ports simultaneously

## How It Works

The server operates on a client-server architecture:
1. Server listens on specified ports for incoming connections
2. Client sends HTTP requests
3. Server parses the request and generates appropriate response
4. Response is sent back to the client
5. Connection is managed (keep-alive or close)

## Usage

```bash
# Compile
make

# Run with custom configuration file
./webserv config/server.conf
```

## Configuration File

The configuration file uses an Nginx-like syntax with server blocks and location blocks:

```nginx
server {
    listen 127.0.0.1:8080;                          # Port to listen on

    server_name myserver;                 # Server name
    
    keep_alive_timeout 500;
    
    error_page 404 /errors/404.html;      # Custom error pages
    error_page 500 502 /errors/50x.html;
    
    location / {
        allow_methods GET POST;           # Allowed methods
        autoindex off;                    # Directory listing
    }
    
    location /uploads {
        methods GET POST DELETE;
        upload_path /var/www/uploads;     # Upload directory
        autoindex on;
    }
    
    location /cgi-bin {
        methods GET POST;
        cgi_extension .php .py;           # CGI extensions
    }
    
    location /redirect {
        return 301 https://example.com;   # Redirections
    }
}

server {
    listen 127.0.0.1:8081;
    server_name another_server;
    root /var/www/other;
    # ... additional configuration
}
```

## Supported Directives

**Server Block:**
- `listen`: Port and/or host to listen on
- `server_name`: Virtual server name
- `host`: Host address
- `root`: Root directory for serving files
- `index`: Default index files
- `error_page`: Custom error pages
- `client_max_body_size`: Maximum request body size

**Location Block:**
- `allow_methods`: Allowed HTTP methods (GET, POST, DELETE)
- `root`: Location-specific root directory
- `autoindex`: Enable/disable directory listing
- `index`: Default files for location
- `cgi_extension`: Extensions to be processed by CGI
- `cgi_path`: Path to CGI interpreter
- `upload_path`: Directory for file uploads
- `return`: HTTP redirection
- `alias`: Alternative path for location

## HTTP Methods

**GET**: Retrieve resources from the server
```bash
curl http://localhost:8080/index.html
```

**POST**: Send data to the server (file uploads, form submissions)
```bash
curl -X POST -F "file=@image.jpg" http://localhost:8080/upload
```

**DELETE**: Remove resources from the server
```bash
curl -X DELETE http://localhost:8080/uploads/file.txt
```

## CGI (Common Gateway Interface)

The server supports CGI for dynamic content:
- PHP scripts
- Python scripts
- Any executable with proper shebang

Example PHP script:
```php
<?php
echo "Hello from CGI!";
?>
```

## Testing

```bash
# Test with curl
curl http://localhost:8080

# Test file upload
curl -X POST -F "file=@test.txt" http://localhost:8080/upload

# Test with browser
open http://localhost:8080

# Stress test with siege
siege -c 100 -t 30s http://localhost:8080

# Test configuration parsing
./webserv config/test.conf
```


## Key Concepts

- **Socket Programming**: Creating and managing TCP sockets
- **I/O Multiplexing**: Non-blocking I/O with select/poll/epoll
- **HTTP Protocol**: Request/response structure, headers, methods, status codes
- **Configuration Parsing**: Reading and validating configuration files
- **CGI Implementation**: Executing external programs and capturing output
- **Concurrent Connections**: Handling multiple clients simultaneously
- **File I/O**: Reading, writing, and managing files
- **Error Handling**: Graceful error handling and custom error pages

## Allowed Functions

- `socket`, `bind`, `listen`, `accept`, `connect`, `send`, `recv`
- `select`, `poll`, `epoll` (depending on system)
- `fcntl`, `setsockopt`
- `getaddrinfo`, `freeaddrinfo`
- `htons`, `htonl`, `ntohs`, `ntohl`
- `fork`, `execve`, `pipe`, `dup`, `dup2`
- Standard C++ library functions

## Common Status Codes

- **200 OK**: Request succeeded
- **201 Created**: Resource created successfully
- **204 No Content**: Successful request with no content
- **301 Moved Permanently**: Permanent redirection
- **400 Bad Request**: Invalid request syntax
- **403 Forbidden**: Access denied
- **404 Not Found**: Resource not found
- **405 Method Not Allowed**: HTTP method not supported
- **413 Payload Too Large**: Request body exceeds limit
- **500 Internal Server Error**: Server error
- **501 Not Implemented**: Method not supported

## Performance Considerations

- Non-blocking I/O prevents server from hanging on slow clients
- Connection pooling for efficient resource management
- Proper buffer management to avoid memory leaks
- Timeout handling for idle connections
- Efficient file serving with proper chunking

## Resources

- [RFC 7230 - HTTP/1.1 Message Syntax](https://tools.ietf.org/html/rfc7230)
- [RFC 7231 - HTTP/1.1 Semantics](https://tools.ietf.org/html/rfc7231)
- [Beej's Guide to Network Programming](https://beej.us/guide/bgnet/)
- [Nginx Configuration Documentation](https://nginx.org/en/docs/)

---

*A 42 School project - Building the backbone of the web.*
