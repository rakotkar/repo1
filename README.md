```
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.security.Keys;

import java.io.File;
import java.nio.file.Files;
import java.security.KeyFactory;
import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.util.Base64;
import java.util.Date;

public class JwtSignerVerifierRSA384 {

    public static void main(String[] args) throws Exception {
        // Path to your private and public PEM files
        File privatePemFile = new File("path/to/your/private_key.pem");
        File publicPemFile = new File("path/to/your/public_key.pem");

        // Load the private and public keys
        PrivateKey privateKey = loadPrivateKey(privatePemFile);
        PublicKey publicKey = loadPublicKey(publicPemFile);

        // Sign the JWT with the private key
        String jwt = Jwts.builder()
                .setSubject("yourSubject")
                .setExpiration(new Date(System.currentTimeMillis() + 3600000)) // 1 hour expiration
                .signWith(privateKey, SignatureAlgorithm.RS384)
                .compact();

        System.out.println("Generated JWT: " + jwt);

        // Verify the JWT with the public key
        Jwts.parserBuilder()
                .setSigningKey(publicKey)
                .build()
                .parseClaimsJws(jwt);

        System.out.println("JWT has been successfully verified.");
    }

    private static PrivateKey loadPrivateKey(File pemFile) throws Exception {
        String key = new String(Files.readAllBytes(pemFile.toPath()));
        key = key.replace("-----BEGIN PRIVATE KEY-----", "").replace("-----END PRIVATE KEY-----", "").replaceAll("\\s+", "");
        byte[] keyBytes = Base64.getDecoder().decode(key);
        PKCS8EncodedKeySpec spec = new PKCS8EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        return keyFactory.generatePrivate(spec);
    }

    private static PublicKey loadPublicKey(File pemFile) throws Exception {
        String key = new String(Files.readAllBytes(pemFile.toPath()));
        key = key.replace("-----BEGIN PUBLIC KEY-----", "").replace("-----END PUBLIC KEY-----", "").replaceAll("\\s+", "");
        byte[] keyBytes = Base64.getDecoder().decode(key);
        X509EncodedKeySpec spec = new X509EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        return keyFactory.generatePublic(spec);
    }
}
```
