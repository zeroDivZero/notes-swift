# INTRO

Used to access data associated with files, sockets, pipes, and devices. For file, can read, write, and seek. For others, can monitor and process data asynchronously.

Most creation methods cause file handle object to take ownership of associated file descriptor - creates file descriptor and responsible for closing it later, usually when handle itself deallocated. To use own file descriptor, use `init(fileDescriptor:)` or `init(fileDescriptor:closeOnDealloc:)`.

## Run Loop Considerations

When communicating asynchronously with socket, must initiate corresponding operations from thread with active run loop. Read, accept, and wait operations are performed asynchronously on background threads, but file handle uses run loop source to monitor operations and notify app code appropriately.
