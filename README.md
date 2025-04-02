# tHe-PrograMMer
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>RSA Cryptography Web Tool</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 20px;
      background: linear-gradient(to right, #1e3c72, #2a5298);
      color: #fff;
    }
    h1 {
      text-align: center;
      color: #fff;
      text-shadow: 1px 1px 3px rgba(0,0,0,0.8);
    }
    .container {
      max-width: 900px;
      margin: auto;
      background: rgba(0, 0, 50, 0.85);
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 10px 20px rgba(0,0,0,0.6);
    }
    textarea, input {
      width: 100%;
      padding: 12px;
      margin: 10px 0;
      border-radius: 6px;
      border: 1px solid #2e6bcf;
      background: #1b2a47;
      color: #fff;
      resize: vertical;
    }
    button {
      padding: 10px 20px;
      margin: 10px 5px 20px 0;
      background: #3498db;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover {
      background: #2980b9;
    }
    p, label { font-weight: 600; color: #e0eaff; }
    .key-display {
      font-family: monospace;
      word-break: break-word;
      background: #254b87;
      padding: 10px;
      border-radius: 5px;
      color: #cfe3ff;
    }
    .test-case {
      margin-top: 40px;
      background: rgba(255,255,255,0.05);
      padding: 20px;
      border: 1px solid #467ddb;
      border-radius: 10px;
    }
    .footer {
      margin-top: 50px;
      font-size: 14px;
      text-align: center;
      color: #bbd4f3;
    }
    a { color: #5dade2; }
  </style>
</head>
<body>
  

  <div class="container">
    <h1>🔐 RSA Cryptography Tool</h1>

    <button onclick="generateKeys()">🔁 Generate RSA Keys</button>
    <p>Public Key:</p>
    <div id="publicKey" class="key-display"></div>
    <p>Private Key:</p>
    <div id="privateKey" class="key-display"></div>

    <label for="message">Plaintext Message:</label>
    <textarea id="message" placeholder="Type your message..."></textarea>

    <button onclick="encryptMessage()">🔒 Encrypt</button>
    <label for="encryptedMessage">Encrypted Output:</label>
    <textarea id="encryptedMessage" readonly></textarea>

    <button onclick="decryptMessage()">🔓 Decrypt</button>
    <label for="decryptedMessage">Decrypted Output:</label>
    <textarea id="decryptedMessage" readonly></textarea>

    <button onclick="signMessage()">🖋️ Sign Message</button>
    <label for="signature">Digital Signature:</label>
    <textarea id="signature" readonly></textarea>

    <button onclick="verifySignature()">✅ Verify Signature</button>
    <p id="verificationResult"></p>

    <div class="test-case">
      <h3>🧪 Try This Test Case:</h3>
      <p>Step-by-step:</p>
      <ol>
        <li>Click <strong>Generate RSA Keys</strong>.</li>
        <li>Enter this message: <code>Hello RSA</code></li>
        <li>Click <strong>Encrypt</strong> to encrypt the message.</li>
        <li>Click <strong>Decrypt</strong> to ensure the message returns to original.</li>
        <li>Click <strong>Sign Message</strong> to generate a digital signature.</li>
        <li>Click <strong>Verify Signature</strong> to confirm the message authenticity.</li>
      </ol>
      <p>If everything works, you should see <strong>"✅ Signature Verified"</strong> below the verification button.</p>
    </div>

    <div class="footer">
      <p>If this page doesn't work properly, please contact me via email: <a href="mailto:20230107@student.act.edu">20230107@student.act.edu</a><br>
      or on my personal number: +30 6999 759876</p>
    </div>
  </div>

  <script>
    let publicKey, privateKey, n;
    let signature = [];

    function gcd(a, b) {
      return b === 0n ? a : gcd(b, a % b);
    }

    function modInv(a, m) {
      let m0 = m, t, q;
      let x0 = 0n, x1 = 1n;
      while (a > 1n) {
        q = a / m;
        t = m;
        m = a % m;
        a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;
      }
      return x1 < 0n ? x1 + m0 : x1;
    }

    function generateKeys() {
      const p = 61n, q = 53n;
      n = p * q;
      const phi = (p - 1n) * (q - 1n);
      const e = 17n;
      const d = modInv(e, phi);

      publicKey = { e, n };
      privateKey = { d, n };

      document.getElementById("publicKey").textContent = `e: ${e}, n: ${n}`;
      document.getElementById("privateKey").textContent = `d: ${d}, n: ${n}`;
    }

    function encryptMessage() {
      if (!publicKey || !n) return alert("Please generate keys first");
      const message = document.getElementById("message").value;
      const encrypted = Array.from(message).map(ch => (BigInt(ch.charCodeAt(0)) ** publicKey.e) % publicKey.n);
      document.getElementById("encryptedMessage").value = encrypted.join(",");
    }

    function decryptMessage() {
      if (!privateKey || !n) return alert("Please generate keys first");
      const encrypted = document.getElementById("encryptedMessage").value.split(",").map(e => BigInt(e));
      const decrypted = encrypted.map(e => String.fromCharCode(Number((e ** privateKey.d) % privateKey.n))).join("");
      document.getElementById("decryptedMessage").value = decrypted;
    }

    function signMessage() {
      if (!privateKey || !n) return alert("Please generate keys first");
      const message = document.getElementById("message").value;
      signature = Array.from(message).map(ch => (BigInt(ch.charCodeAt(0)) ** privateKey.d) % privateKey.n);
      document.getElementById("signature").value = signature.join(",");
    }

    function verifySignature() {
      if (!publicKey || !n || signature.length === 0) return alert("Missing signature or keys");
      const verified = signature.map(s => String.fromCharCode(Number((s ** publicKey.e) % publicKey.n))).join("");
      const original = document.getElementById("message").value;
      const result = (verified === original) ? "✅ Signature Verified" : "❌ Signature Mismatch";
      document.getElementById("verificationResult").textContent = result;
    }
  </script>
</body>
</html>

