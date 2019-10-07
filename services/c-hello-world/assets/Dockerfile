FROM alpine:3.10 AS builder
RUN apk add build-base
WORKDIR /home
COPY hello.c .
RUN gcc "-DARCH=\"`uname -a`\"" hello.c -o hello
 
FROM alpine:3.10
WORKDIR /home
COPY --from=builder /home/hello .
CMD ["./hello"]