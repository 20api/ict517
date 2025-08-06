import java.util.*;

public class PlayfairCipher {
    private static final char[][] keyMatrix = new char[5][5];

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.println("=== Playfair Cipher ===");
        System.out.print("Enter keyword: ");
        String keyword = sc.nextLine();

        System.out.print("Enter plaintext: ");
        String plaintext = sc.nextLine();

        generateKeyMatrix(keyword);
        displayKeyMatrix();

        String prepared = prepareText(plaintext);
        System.out.println("\nPrepared Text: " + prepared);

        String encrypted = encrypt(prepared);
        System.out.println("Encrypted Text: " + encrypted);

        String decrypted = cleanDecryptedText(decrypt(encrypted));
        System.out.println("Decrypted Text: " + decrypted);
    }

    static void generateKeyMatrix(String keyword) {
        keyword = keyword.toUpperCase().replace("J", "I");
        Set<Character> set = new LinkedHashSet<>();

        for (char c : keyword.toCharArray()) if (Character.isLetter(c)) set.add(c);
        for (char c = 'A'; c <= 'Z'; c++) if (c != 'J') set.add(c);

        Iterator<Character> it = set.iterator();
        for (int i = 0; i < 5; i++)
            for (int j = 0; j < 5; j++)
                keyMatrix[i][j] = it.next();
    }

    static void displayKeyMatrix() {
        System.out.println("\nKey Matrix:");
        for (char[] row : keyMatrix) {
            for (char c : row) System.out.print(c + " ");
            System.out.println();
        }
    }

    static String prepareText(String text) {
        text = text.toUpperCase().replace("J", "I").replaceAll("[^A-Z]", "");
        StringBuilder sb = new StringBuilder();

        for (int i = 0; i < text.length(); i++) {
            char a = text.charAt(i);
            char b = (i + 1 < text.length()) ? text.charAt(i + 1) : 'X';
            sb.append(a);
            if (a == b) sb.append('X');
            else {
                sb.append(b);
                i++;
            }
        }

        if (sb.length() % 2 != 0) sb.append('X');
        return sb.toString();
    }

    static int[] findPos(char c) {
        for (int i = 0; i < 5; i++)
            for (int j = 0; j < 5; j++)
                if (keyMatrix[i][j] == c) return new int[]{i, j};
        return null;
    }

    static String process(String text, boolean encrypt) {
        StringBuilder sb = new StringBuilder();
        int shift = encrypt ? 1 : 4;

        for (int i = 0; i < text.length(); i += 2) {
            char a = text.charAt(i), b = text.charAt(i + 1);
            int[] p1 = findPos(a), p2 = findPos(b);

            if (p1[0] == p2[0]) { // Same row
                sb.append(keyMatrix[p1[0]][(p1[1] + shift) % 5]);
                sb.append(keyMatrix[p2[0]][(p2[1] + shift) % 5]);
            } else if (p1[1] == p2[1]) { // Same column
                sb.append(keyMatrix[(p1[0] + shift) % 5][p1[1]]);
                sb.append(keyMatrix[(p2[0] + shift) % 5][p2[1]]);
            } else { // Rectangle
                sb.append(keyMatrix[p1[0]][p2[1]]);
                sb.append(keyMatrix[p2[0]][p1[1]]);
            }
        }

        return sb.toString();
    }

    static String encrypt(String text) {
        return process(text, true);
    }

    static String decrypt(String text) {
        return process(text, false);
    }

    static String cleanDecryptedText(String text) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < text.length(); ) {
            sb.append(text.charAt(i));
            if (i + 2 < text.length() && text.charAt(i + 1) == 'X' && text.charAt(i) == text.charAt(i + 2))
                i += 2;
            else i++;
        }
        return sb.toString();
    }
}
//
import java.util.Scanner;

public class RailFenceCipher {

    
    public static String encrypt(String text, int rails) {
        text = text.toUpperCase();
        int len = text.length();

        char[][] rail = new char[rails][len];

        for (int i = 0; i < rails; i++)
            for (int j = 0; j < len; j++)
                rail[i][j] = '\n';

        boolean down = false;
        int row = 0, col = 0;

        for (char c : text.toCharArray()) {
            rail[row][col++] = c;

            if (row == 0 || row == rails - 1)
                down = !down;

            row += down ? 1 : -1;
        }

        System.out.println();
        printRailMatrix(rail, rails, len);

        StringBuilder result = new StringBuilder();
        for (int i = 0; i < rails; i++)
            for (int j = 0; j < len; j++)
                if (rail[i][j] != '\n')
                    result.append(rail[i][j]);

        return result.toString();
    }

    
    public static String decrypt(String cipher, int rails) {
        int len = cipher.length();

        char[][] rail = new char[rails][len];

        boolean down = false;
        int row = 0, col = 0;

        for (int i = 0; i < len; i++) {
            rail[row][col++] = '*';

            if (row == 0 || row == rails - 1)
                down = !down;

            row += down ? 1 : -1;
        }

        int index = 0;
        for (int i = 0; i < rails; i++) {
            for (int j = 0; j < len; j++) {
                if (rail[i][j] == '*' && index < len) {
                    rail[i][j] = cipher.charAt(index++);
                }
            }
        }

        System.out.println();
        printRailMatrix(rail, rails, len);

        StringBuilder result = new StringBuilder();
        down = false;
        row = 0;
        col = 0;

        for (int i = 0; i < len; i++) {
            result.append(rail[row][col++]);

            if (row == 0 || row == rails - 1)
                down = !down;

            row += down ? 1 : -1;
        }

        return result.toString();
    }

    // Main method
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter Text: ");
        String message = sc.nextLine();

        int rails;
        while (true) {
            System.out.print("Enter Rails: ");
            rails = sc.nextInt();
            if (rails >= 2) break;
            System.out.println("Minimum 2 rails needed!");
        }

        String encryptedText = encrypt(message, rails);
        System.out.println("\nEncode: " + encryptedText);

        String decryptedText = decrypt(encryptedText, rails);
        System.out.println("\nDecode: " + decryptedText);
    }

    public static void printRailMatrix(char[][] rail, int rows, int cols) {
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++)
                System.out.print(rail[i][j] == '\n' ? ' ' : rail[i][j]);
            System.out.println();
        }
    }
}
//

import java.security.*;


public class RSAKeyPairGenerator {
    public static void main(String[] args) {
        try {
            // Initialize KeyPairGenerator for RSA with 2048-bit key size
            KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("RSA");
            keyPairGenerator.initialize(2048);
            
            // Generate the key pair
            KeyPair keyPair = keyPairGenerator.generateKeyPair();
            
            // Get the public and private keys
            PublicKey publicKey = keyPair.getPublic();
            PrivateKey privateKey = keyPair.getPrivate();
            
            // Print key information
            System.out.println("Public Key Format: " + publicKey.getFormat());
            System.out.println("Public Key (X.509 Encoded):\n" + bytesToHex(publicKey.getEncoded()));
            
            System.out.println("\nPrivate Key Format: " + privateKey.getFormat());
            System.out.println("Private Key (PKCS#8 Encoded):\n" + bytesToHex(privateKey.getEncoded()));
            
        } catch (NoSuchAlgorithmException e) {
            System.err.println("RSA algorithm not available: " + e.getMessage());
        }
    }
    
    // Helper method to convert byte array to hex string
    private static String bytesToHex(byte[] bytes) {
        StringBuilder sb = new StringBuilder();
        for (byte b : bytes) {
            sb.append(String.format("%02x", b));
        }
        return sb.toString();
    }
}

