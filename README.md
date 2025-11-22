CRC :
import java.util.*;

public class CRC {

    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);

        System.out.print("Enter the size of the data: ");
        int n = scan.nextInt();
        int[] data = new int[n];

        System.out.println("Enter the data bits (space separated):");
        for (int i = 0; i < n; i++) {
            data[i] = scan.nextInt();
            if (data[i] != 0 && data[i] != 1) {
                System.out.println("Invalid bit. Only 0 or 1 allowed. Replacing with 0.");
                data[i] = 0;
            }
        }

        System.out.print("Enter the size of the divisor: ");
        int m = scan.nextInt();
        if (m < 2) {
            System.out.println("Divisor must be at least 2 bits long.");
            scan.close();
            return;
        }

        int[] divisor = new int[m];
        System.out.println("Enter the divisor bits (space separated):");
        for (int i = 0; i < m; i++) {
            divisor[i] = scan.nextInt();
        }

        
        int[] dataPadded = new int[data.length + m - 1];
        System.arraycopy(data, 0, dataPadded, 0, data.length);

        
        int[] remainder = mod2Division(dataPadded, divisor);

        System.out.print("Remainder: ");
        for (int b : remainder) {
            System.out.print(b);
        }
        System.out.println();

        
        int[] codeword = new int[data.length + remainder.length];
        System.arraycopy(data, 0, codeword, 0, data.length);
        System.arraycopy(remainder, 0, codeword, data.length, remainder.length);

        System.out.print("The CRC code generated is: ");
        for (int b : codeword) {
            System.out.print(b);
        }
        System.out.println();

        
        System.out.println("Enter the received codeword bits (space separated) OR press Enter to simulate correct transmission:");
        scan.nextLine(); 
        String line = scan.nextLine().trim();

        int[] received;
        if (line.isEmpty()) {
            received = Arrays.copyOf(codeword, codeword.length); 
            System.out.print("Simulating correct transmission: ");
            for (int b : received) {
                System.out.print(b);
            }
            System.out.println();
        } else {
            String[] toks = line.split("\\s+");
            if (toks.length != codeword.length) {
                System.out.println("Received codeword length mismatch. Expected length: " + codeword.length);
                scan.close();
                return;
            }
            received = new int[toks.length];
            for (int i = 0; i < toks.length; i++) {
                received[i] = Integer.parseInt(toks[i]);
                if (received[i] != 0 && received[i] != 1) {
                    System.out.println("Invalid bit at position " + i + ". Setting to 0.");
                    received[i] = 0;
                }
            }
        }

        int[] recvRemainder = mod2Division(received, divisor);

        boolean error = false;
        for (int b : recvRemainder) {
            if (b != 0) {
                error = true;
                break;
            }
        }

        if (error) {
            System.out.println("There is an error in received data.");
        } else {
            System.out.println("Data was received without any error.");
        }

        scan.close();
    }

    static int[] mod2Division(int[] data, int[] divisor) {
        int[] temp = Arrays.copyOf(data, data.length);
        int n = data.length;
        int m = divisor.length;

        
        for (int i = 0; i <= n - m; i++) {
            if (temp[i] == 1) {
                for (int j = 0; j < m; j++) {
                    temp[i + j] = exor(temp[i + j], divisor[j]);
                }
            }
        }

        int remLen = m - 1;
        int[] remainder = new int[remLen];
        int start = n - remLen;
        for (int i = 0; i < remLen; i++) {
            remainder[i] = temp[start + i];
        }
        return remainder;
    }

    static int exor(int a, int b) {
        return (a == b) ? 0 : 1;
    }
}
//OUTPUT:
// Enter the size of the data: 7
// Enter the data bits (space separated):
// 1 0 1 1 0 0 1
// Enter the size of the divisor: 3
// Enter the divisor bits (space separated):
// 1 0 1
// Remainder: 11
// The CRC code generated is: 101100111
// Enter the received codeword bits (space separated) OR press Enter to simulate correct transmission:
// 1 0 1 1 0 0 1 1 1
// Data was received without any error.
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Bellman Ford: 
import java.util.Scanner;

public class BellmanFord {
    private int[] D;
    private int num_ver;
    public static final int MAX_VALUE = 999;

    public BellmanFord(int num_ver) {
        this.num_ver = num_ver;
        D = new int[num_ver + 1];
    }

    public void BellmanFordEvaluation(int source, int[][] A) {

        for (int node = 1; node <= num_ver; node++) {
            D[node] = MAX_VALUE;
        }
        D[source] = 0;

        for (int k = 1; k <= num_ver - 1; k++) {
            for (int sn = 1; sn <= num_ver; sn++) {
                for (int dn = 1; dn <= num_ver; dn++) {
                    if (A[sn][dn] != MAX_VALUE) {
                        if (D[sn] != MAX_VALUE && D[dn] > D[sn] + A[sn][dn]) {
                            D[dn] = D[sn] + A[sn][dn];
                        }
                    }
                }
            }
        }

        for (int sn = 1; sn <= num_ver; sn++) {
            for (int dn = 1; dn <= num_ver; dn++) {
                if (A[sn][dn] != MAX_VALUE) {
                    if (D[sn] != MAX_VALUE && D[dn] > D[sn] + A[sn][dn]) {
                        System.out.println("The Graph contains a negative edge cycle!");
                        return;
                    }
                }
            }
        }

        for (int vertex = 1; vertex <= num_ver; vertex++) {
            System.out.println("Distance of source " + source + " to " + vertex + " is " + D[vertex]);
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.println("Enter the number of vertices:");
        int num_ver = scanner.nextInt();

        int[][] A = new int[num_ver + 1][num_ver + 1];

        System.out.println("Enter the adjacency matrix:");
        for (int sn = 1; sn <= num_ver; sn++) {
            for (int dn = 1; dn <= num_ver; dn++) {
                A[sn][dn] = scanner.nextInt();

                if (sn == dn) {
                    A[sn][dn] = 0;
                } else if (A[sn][dn] == 0) {
                    A[sn][dn] = MAX_VALUE;
                }
            }
        }

        System.out.println("Enter the source vertex:");
        int source = scanner.nextInt();

        BellmanFord b = new BellmanFord(num_ver);
        b.BellmanFordEvaluation(source, A);

        scanner.close();
    }
}

/*
OUTPUT:

Case 1:
Enter the number of vertices:
4
Enter the adjacency matrix:
0 5 0 0
5 0 3 4
0 3 0 2
0 4 2 0
Enter the source vertex:
2

Distance of source 2 to 1 is 5
Distance of source 2 to 2 is 0
Distance of source 2 to 3 is 3
Distance of source 2 to 4 is 4

--------------------------------------------------

Case 2:
Enter the number of vertices:
6
Enter the adjacency matrix:
0 6 4 5 0 0
0 0 0 0 -1 0
0 -2 0 0 3 0
0 0 -2 0 0 -1
0 0 0 0 0 3
0 0 0 0 0 0
Enter the source vertex:
1

Distance of source 1 to 1 is 0
Distance of source 1 to 2 is 1
Distance of source 1 to 3 is 3
Distance of source 1 to 4 is 5
Distance of source 1 to 5 is 0
Distance of source 1 to 6 is 3
*/
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
RSA ALGORITHM: 
import java.math.BigInteger;
import java.nio.charset.StandardCharsets;
import java.util.Random;
import java.util.Scanner;

public class RSABytes {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Random rnd = new Random(System.currentTimeMillis());

        System.out.println("Enter two prime numbers (or enter 0 0 to auto-generate suitable primes):");
        BigInteger p = sc.nextBigInteger();
        BigInteger q = sc.nextBigInteger();

        
        if (p.equals(BigInteger.ZERO) && q.equals(BigInteger.ZERO)) {
            int bitLength = 16;
            do {
                p = BigInteger.probablePrime(bitLength, rnd);
                q = BigInteger.probablePrime(bitLength, rnd);
                bitLength++;
            } while (p.multiply(q).compareTo(BigInteger.valueOf(256)) <= 0);

            System.out.println("Auto-generated primes:");
            System.out.println("p = " + p);
            System.out.println("q = " + q);
        }

        BigInteger n = p.multiply(q);
        BigInteger phi = (p.subtract(BigInteger.ONE)).multiply(q.subtract(BigInteger.ONE));

        
        if (n.compareTo(BigInteger.valueOf(256)) <= 0) {
            System.out.println("Error: modulus n = p*q is too small for byte-wise encryption. Use larger primes.");
            sc.close();
            return;
        }

        
        BigInteger e = BigInteger.valueOf(65537);
        if (!phi.gcd(e).equals(BigInteger.ONE)) {
            e = BigInteger.valueOf(3);
            while (!phi.gcd(e).equals(BigInteger.ONE)) {
                e = e.add(BigInteger.TWO);
            }
        }

        
        BigInteger d;
        try {
            d = e.modInverse(phi);
        } catch (ArithmeticException ex) {
            e = BigInteger.valueOf(3);
            while (!phi.gcd(e).equals(BigInteger.ONE)) {
                e = e.add(BigInteger.TWO);
            }
            d = e.modInverse(phi);
        }

        System.out.println("\nPublic key (e): " + e);
        System.out.println("Private key (d): " + d);
        System.out.println("Modulus (n): " + n);

        sc.nextLine(); 
        System.out.println("\nEnter the message (whole line allowed):");
        String plaintext = sc.nextLine();

        
        byte[] plainBytes = plaintext.getBytes(StandardCharsets.UTF_8);

        
        BigInteger[] cipher = new BigInteger[plainBytes.length];
        for (int i = 0; i < plainBytes.length; i++) {
            int unsigned = plainBytes[i] & 0xFF;
            BigInteger m = BigInteger.valueOf(unsigned);
            cipher[i] = m.modPow(e, n);
        }

        System.out.println("\nCiphertext (space-separated integers):");
        StringBuilder cb = new StringBuilder();
        for (BigInteger c : cipher) {
            cb.append(c.toString()).append(" ");
        }
        System.out.println(cb.toString().trim());


        byte[] decryptedBytes = new byte[cipher.length];
        for (int i = 0; i < cipher.length; i++) {
            BigInteger m = cipher[i].modPow(d, n);
            int val = m.intValue();
            decryptedBytes[i] = (byte) (val & 0xFF);
        }

        String decrypted = new String(decryptedBytes, StandardCharsets.UTF_8);
        System.out.println("\nDecrypted message: " + decrypted);

        sc.close();
    }
}
// Enter two prime numbers (or enter 0 0 to auto-generate suitable primes):
// 61 53

// Public key (e): 65537
// Private key (d): 2753
// Modulus (n): 3233

// Enter the message (whole line allowed):
// HELLO WORLD

// Ciphertext (space-separated integers):
// 3000 28 2726 2726 1307 1992 604 1307 1859 2726 1759

// Decrypted message: HELLO WORLD
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
TCP SERVER




import java.io.*;
import java.net.*;
import java.nio.charset.StandardCharsets;

public class FileTransferServer {

    
    private static final int SERVER_PORT = 1238;


    private static final int BUFFER_SIZE = 2000;

    public static void main(String[] args) {
        System.out.println("Starting TCP File Transfer Server...");

        try (ServerSocket serverSocket = new ServerSocket(SERVER_PORT)) {
            System.out.println("Server started and listening on port " + SERVER_PORT + "...");
            
            
            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("\nClient connected from: " + clientSocket.getInetAddress().getHostAddress());

                
                new Thread(() -> handleClient(clientSocket)).start();
            }

        } catch (IOException e) {
            System.err.println("Could not start server: " + e.getMessage());
        }
    }

    private static void handleClient(Socket clientSocket) {
        try (
            
            OutputStream socketOut = clientSocket.getOutputStream();
            
            BufferedReader socketIn = new BufferedReader(
                    new InputStreamReader(clientSocket.getInputStream(), StandardCharsets.UTF_8))
        ) {
            
            String fileName = socketIn.readLine();
            if (fileName == null || fileName.trim().isEmpty()) {
                System.out.println("Client disconnected before sending filename.");
                return;
            }

            fileName = fileName.trim();
            System.out.println("Requested file: " + fileName);

            File file = new File(fileName);

            
            if (!file.exists() || file.isDirectory()) {
                String errorMessage = "ERROR: File does not exist\n";
                System.out.println(errorMessage.trim());
                socketOut.write(errorMessage.getBytes(StandardCharsets.UTF_8));
                socketOut.flush();
                return;
            }

            
            System.out.println("File found. Sending contents...");

            try (FileInputStream fileIn = new FileInputStream(file)) {
                byte[] buffer = new byte[BUFFER_SIZE];
                int bytesRead;

                while ((bytesRead = fileIn.read(buffer)) != -1) {
                    socketOut.write(buffer, 0, bytesRead);
                }

                socketOut.flush();
                System.out.println("File sent successfully.");
            }

        } catch (IOException e) {
            System.err.println("Error processing client request: " + e.getMessage());
        } finally {
            try {
                clientSocket.close();
            } catch (IOException ignore) {}
            System.out.println("Client connection closed.");
        }
    }
}
// Starting TCP File Transfer Server...
// Server started and listening on port 1238...

// Client connected from: 127.0.0.1
// Requested file: test_file.txt
// File found. Sending contents...
// File sent successfully.
// Client connection closed.


TCP CLIENT



import java.io.*;
import java.net.Socket;
import java.net.UnknownHostException;
import java.nio.charset.StandardCharsets;

public class FileTransferClient {

    
    private static final int SERVER_PORT = 1238;


    private static final int BUFFER_SIZE = 2000;

    public static void main(String[] args) {

    
        if (args.length != 2) {
            System.out.println("Usage: java FileTransferClient <server_name> <file_name>");
            return;
        }

        String serverName = args[0]; 
        String fileName = args[1];   

        
        try (
            
            Socket socket = new Socket(serverName, SERVER_PORT);

            
            PrintWriter out = new PrintWriter(
                    new OutputStreamWriter(socket.getOutputStream(), StandardCharsets.UTF_8),
                    true);

        
            InputStream in = socket.getInputStream();

            
            DataOutputStream stdOut = new DataOutputStream(
                    new BufferedOutputStream(System.out))
        ) {
            
            out.println(fileName);

            byte[] buffer = new byte[BUFFER_SIZE];
            int bytesRead;

            
            while ((bytesRead = in.read(buffer)) != -1) {
                stdOut.write(buffer, 0, bytesRead);
            }

            stdOut.flush();

        } catch (UnknownHostException e) {
            System.err.println("Error: Unknown host " + serverName);
        } catch (IOException e) {
            System.err.println("Error connecting to server or reading/writing data: " + e.getMessage());
        }
    }
}

// java FileTransferClient localhost
// java FileTransferClient localhost test_file.txt
// HELLO DEV
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
UDPS: 
import java.io.*;
import java.net.*;

public class UDPS {
    public static void main(String[] args) {
        DatagramSocket socket = null;

        try {
            
            socket = new DatagramSocket(6788);
            System.out.println("UDP Server started on port 6788. Waiting for client messages...");

            byte[] buffer = new byte[1024];

            while (true) {
                
                DatagramPacket request = new DatagramPacket(buffer, buffer.length);
                socket.receive(request);

                
                String message = new String(request.getData(), 0, request.getLength());
                System.out.println("Received from client: " + message);

                
                String replyMessage = message + " (server processed)";
                byte[] sendMsg = replyMessage.getBytes();

                
                DatagramPacket reply = new DatagramPacket(
                        sendMsg,
                        sendMsg.length,
                        request.getAddress(),
                        request.getPort());

                socket.send(reply);
                System.out.println("Reply sent to client.\n");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (socket != null && !socket.isClosed())
                socket.close();
        }
    }
}
// Output:
//Server started. Waiting for client messages...
// Received from client: Hello Server
// Reply sent to client.

UDPC:
import java.io.*;
import java.net.*;

public class UDPC {
    public static void main(String[] args) {
        DatagramSocket socket = null;

        try {
            
            socket = new DatagramSocket();
            InetAddress host = InetAddress.getByName("127.0.0.1"); // Server IP
            int serverPort = 6788;

            
            String msg = "Hello Server";
            byte[] sendData = msg.getBytes();

    
            DatagramPacket request = new DatagramPacket(sendData, sendData.length, host, serverPort);
            socket.send(request);
            System.out.println("Message sent to server: " + msg);

    
            byte[] buffer = new byte[1024];
            DatagramPacket reply = new DatagramPacket(buffer, buffer.length);
            socket.receive(reply);


            String received = new String(reply.getData(), 0, reply.getLength());
            System.out.println("Client received: " + received);

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (socket != null && !socket.isClosed())
                sock
