# CWS - Lightweight WebSocket Client Library

## Overview

CWS (Compact WebSocket) is a lightweight C library for handling WebSocket client connections. It provides essential functions for establishing WebSocket handshakes, sending and receiving messages, and managing WebSocket frames efficiently.

# Features

## Supports WebSocket client handshake.

- Implements WebSocket frame parsing (continuation, text, binary, close, ping, and pong).

- Handles fragmented messages with linked chunks.

- Provides error handling for various WebSocket operations.

- Uses function pointers for flexible socket operations (read, write, allocate, free).

- Works with masked and unmasked WebSocket frames.


# Installation

To use CWS, include cws.h in your project and define CWS_IMPLEMENTATION in one source file before including it:

```code
#define CWS_IMPLEMENTATION
#include "cws.h"
```

Compile your project as usual, ensuring that your compiler supports C99 or later.

# API Usage

__Initialization__

Before using CWS, you need to initialize a Cws instance with appropriate function pointers:

Cws cws = {
    .socket = my_socket,  // Your socket object
    .read = my_socket_read,
    .write = my_socket_write,
    .ator = my_allocator,
    .alloc = my_alloc,
    .free = my_free,
};

WebSocket Handshake

To establish a WebSocket connection with a server:

if (cws_client_handshake(&cws, "example.com") != 0) {
    printf("Handshake failed: %s\n", cws_get_error_string(&cws));
}

Sending Messages

To send a text or binary WebSocket message:

const char *message = "Hello, WebSocket!";
cws_send_message(&cws, CWS_MESSAGE_TEXT, (const uint8_t *)message, strlen(message), 1024);

Receiving Messages

To receive a WebSocket message:

Cws_Message message;
if (cws_read_message(&cws, &message) == 0) {
    printf("Received message of length %llu\n", message.chunks->payload_len);
    cws_free_message(&cws, &message);
}

Sending and Receiving Frames

To send a WebSocket frame:

cws_send_frame(&cws, true, CWS_OPCODE_TEXT, (const uint8_t *)"Hello", 5);

To receive a WebSocket frame:

Cws_Frame frame = {0};
if (cws_read_frame(&cws, &frame) == 0) {
    printf("Received frame of opcode %d\n", frame.opcode);
    cws_free_frame(&cws, &frame);
}

Error Handling

CWS provides error codes for debugging:

if (cws.error != CWS_NO_ERROR) {
    printf("Error: %s\n", cws_get_error_string(&cws));
}

Error Codes

CWS_NO_ERROR – No error.

CWS_CLIENT_HANDSHAKE_ERROR – WebSocket handshake failed.

CWS_SOCKET_ERROR – Socket read/write failure.

CWS_ALLOCATOR_ERROR – Memory allocation failure.

CWS_SERVER_CLOSE_ERROR – Server sent a CLOSE frame.


Notes & TODOs

The server handshake response is currently ignored.

The implementation assumes little-endian byte order.

The client frames are always masked per WebSocket specifications.


License

This project is licensed under the MIT License.


