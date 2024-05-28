openssl genrsa -des3 -out ca.key 2048
openssl rsa -in ca.key -out ca.key.nopass
openssl req -new -key ca.key.nopass -out server.csr
openssl x509 -req -days 365 -in server.csr -signkey ca.key.nopass -out server.crt
