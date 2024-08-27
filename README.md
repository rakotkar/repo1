import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.security.Keys;

import java.io.File;
import java.security.KeyFactory;
import java.security.PrivateKey;
import java.security.spec.PKCS8EncodedKeySpec;
import java.util.Base64;
import java.util.Date;

public class JwtSigner {

    public static void main(String[] args) throws Exception {
        // Path to your PEM file
        File pemFile = new File("path/to/your/private_key.pem");
        
        // Load the private key
        PrivateKey privateKey = loadPrivateKey(pemFile);
        
        // Create the JWT and sign it with RS384
        String jwt = Jwts.builder()
                .setSubject("yourSubject")
                .setExpiration(new Date(System.currentTimeMillis() + 3600000)) // 1 hour expiration
                .signWith(privateKey, SignatureAlgorithm.RS384) // RS384 algorithm for signing
                .compact();
        
        System.out.println("Generated JWT: " + jwt);
    }

    // Method to load the RSA private key from a PEM file
    private static PrivateKey loadPrivateKey(File pemFile) throws Exception {
        // Read the PEM file
        String key = new String(java.nio.file.Files.readAllBytes(pemFile.toPath()));
        
        // Remove the header and footer
        key = key.replace("-----BEGIN PRIVATE KEY-----", "").replace("-----END PRIVATE KEY-----", "").replaceAll("\\s+", "");
        
        // Decode the Base64 encoded key
        byte[] keyBytes = Base64.getDecoder().decode(key);

        // Create the private key from the decoded bytes
        PKCS8EncodedKeySpec spec = new PKCS8EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        return keyFactory.generatePrivate(spec);
    }
}
