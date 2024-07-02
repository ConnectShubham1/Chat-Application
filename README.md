# Chat-Application
A simple client-server chat application using sockets
# Chat Application

## Description

A simple client-server chat application using sockets in Java.

## How to Run

1. **Compile the Server**:
    ```
    javac src/Server.java
    ```

2. **Run the Server**:
    ```
    java -cp src Server
    ```

3. **Compile the Client**:
    ```
    javac src/Client.java
    ```

4. **Run the Client**:
    ```
    java -cp src Client
    ```

5. **Follow the prompts**:
    - Start the server first.
    - Start one or more clients.
    - Type messages in the client's console and press Enter to send them to the server.
    - The server will echo back the received messages to each client.

## Example Code

### Server.java

```java
import java.io.*;
import java.net.*;

public class Server {
    private static final int PORT = 12345;

    public static void main(String[] args) {
        try (ServerSocket serverSocket = new ServerSocket(PORT)) {
            System.out.println("Server started on port " + PORT);

            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("Client connected: " + clientSocket);

                new Thread(new ClientHandler(clientSocket)).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static class ClientHandler implements Runnable {
        private final Socket clientSocket;
        private PrintWriter out;
        private BufferedReader in;

        public ClientHandler(Socket socket) {
            this.clientSocket = socket;
            try {
                out = new PrintWriter(clientSocket.getOutputStream(), true);
                in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void run() {
            String inputLine;
            try {
                while ((inputLine = in.readLine()) != null) {
                    System.out.println("Client: " + inputLine);
                    out.println("Server received: " + inputLine);
                }
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    in.close();
                    out.close();
                    clientSocket.close();
                    System.out.println("Client disconnected: " + clientSocket);
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
