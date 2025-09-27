Realtime Price Client (C++)

A lightweight C++ socket client that connects to a TCP price server, ingests tick messages (price_id,price), keeps the last 3 prices in a std::deque<float>, detects strict momentum (up or down), and sends an order (the current price_id) back to the server with a small, configurable reaction delay.

Features

✅ POSIX TCP client (Linux/macOS)

✅ Parses tick messages: "<id>,<price>"

✅ Rolling last-3 price window using std::deque<float>

✅ Momentum signal: strict increasing (a<b && b<c) or strict decreasing (a>b && b>c)

✅ Sends the current price_id on momentum

✅ Optional “generic” path to always send an order (for testing)

✅ Friendly console logs

How It Works

Connects to the server at SERVER_IP:SERVER_PORT (defaults: 127.0.0.1:12345).

Sends the client name once upon connect.

Receives price ticks as text lines:

33,126.7
34,181.0
35,163.3


For each tick:

Parse price_id and price.

Push price into priceHistory (pop the oldest if size would exceed 3).

If we have exactly 3 values: check momentum (strict up/down).

On momentum: short sleep (10–59 ms) then send() the price_id back to server.

Build

Requires a POSIX environment (Linux/macOS) with a C++17 compiler.

g++ -std=c++17 client.cpp -pthread -o price_client


If you’re on macOS and get socket warnings, add -Wno-deprecated-declarations.

Run
./price_client
# Enter your client name when prompted


To change server settings, edit the macros at the top of the file:

#define SERVER_IP "127.0.0.1"
#define SERVER_PORT 12345
#define BUFFER_SIZE 1024

Expected Server Protocol

Inbound to client: text lines "price_id,price" (comma-separated).
Examples:

101,199.42
102,200.10
103,201.05


Example Output
✅ Connected to server at 127.0.0.1:12345
📥 Received price ID: 101, Value: 199.4
📥 Received price ID: 102, Value: 200.1
📥 Received price ID: 103, Value: 201.1
🚀 Momentum UP | window=[199.4, 200.1, 201.1] | sent order id=103
📥 Received price ID: 104, Value: 198.9
📥 Received price ID: 105, Value: 197.4
📥 Received price ID: 106, Value: 196.8
🚀 Momentum DOWN | window=[198.9, 197.4, 196.8] | sent order id=106
