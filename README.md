# Py-Stun

Py-Stun is a Python implementation of the STUN protocol (Session Traversal Utilities for NAT).

It allows you to discover your public IP address and the type of NAT you are behind (Full Cone, Restricted Cone, Port Restricted Cone, Symmetric).

## stun_server.py

The `stun_server.py` file implements a simple STUN server that handles both UDP STUN requests and TCP health check requests on the same port (default: 3560).

### Key Components

- **STUN Message Types and Attributes**: Constants for STUN message types and attributes.
- **Functions**:
  - `parse_stun_message(data)`: Parses a STUN message.
  - `create_stun_binding_response(transaction_id, ip, port)`: Creates a STUN Binding Response message.
  - `handle_stun_request(server_socket)`: Handles incoming STUN requests.
  - `handle_tcp_health_check(client_socket, client_address)`: Handles TCP health check requests.
  - `start_combined_server(host="0.0.0.0", port=3560)`: Starts the combined server.

### Running the Server

To run the server, execute the `stun_server.py` script:

```sh
python stun_server.py --port 3560
````

## turn/ (the relay)

STUN tells a browser its public address; it cannot carry a call between two networks that allow no direct path (one of them maps ports per destination, a common router setting). For those calls Skapi runs a TURN relay, coturn, on the same instance as `stun_server.py`, reached directly at the instance's permanent address as `turn.skapi.com:3478` (UDP and TCP, relay ports 49152 to 65535).

- `turn/turnserver.conf`: the configuration. Credentials are time-limited (coturn's `use-auth-secret`): the backend's WebSocket handler signs `<expiry>:<user id>` with the same key and hands it to the SDK, so no password is ever written into a page.
- `turn/install.sh`: `sudo bash install.sh <key>` on the instance installs coturn, writes the configuration with the instance's addresses and starts the service. Run it again to change the key.

The key is also the record stack's `TurnKey` parameter in every region. A region without it offers STUN alone.

