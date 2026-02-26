# 1. Start with an image that already has the compiler
FROM gcc:latest

# 2. Set the folder where our code will live inside the container
WORKDIR /app

# 3. Copy everything from your server folder into the container
COPY . .

# 4. Use your existing Makefile to build the project
# We run 'make clean' first just to be safe
RUN make clean && make

# 5. Document that the server uses port 6379
EXPOSE 6379

# 6. Start the server and pass the port as an argument
# This runs your compiled binary 'my_redis_server'
CMD ["./my_redis_server", "6379"]