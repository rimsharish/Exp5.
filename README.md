----server.py
import socket, ast

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.bind(("127.0.0.1",8080))
s.listen(1)
print("Server is waiting for connection...")
c,a=s.accept()
print("Client connected:",a)

while True:
    data=c.recv(4096)
    if not data: break
    msg=data.decode()
    if msg.lower()=="exit":
        print("Client terminated the connection.")
        break
    m=ast.literal_eval(msg)
    print("\nReceived Matrix:")
    for r in m: print(r)
    n=len(m)
    u=l=d=True
    for i in range(n):
        for j in range(n):
            if i>j and m[i][j]!=0: u=False
            if i<j and m[i][j]!=0: l=False
            if i!=j and m[i][j]!=0: d=False
    if d: result="Diagonal Matrix"
    elif u: result="Upper Triangular Matrix"
    elif l: result="Lower Triangular Matrix"
    else: result="Neither Upper nor Lower Triangular Matrix"
    c.send(result.encode())

c.close()
s.close()
print("Server terminated.")

--client.py-

import socket, ast

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("127.0.0.1",8080))
print("Connected to server.")
print("Enter 'exit' to terminate.\n")

while True:
    m=input("Enter N x N matrix: ")
    s.send(str(m).encode())
    if m.lower()=="exit": break
    try:
        a=ast.literal_eval(m)
        n=len(a)
        if n==0 or any(len(r)!=n for r in a):
            print("Invalid matrix. Enter a square matrix.")
            continue
    except (ValueError,SyntaxError,TypeError):
        print("Invalid matrix format.")
        continue
    print("Matrix Type:",s.recv(1024).decode())
    print()

s.close()
print("Client terminated.")
