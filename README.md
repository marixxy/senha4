
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CryptoCode - Criptografia</title>

<style>
* {
  box-sizing: border-box;
  font-family: Arial, sans-serif;
}

body {
  margin: 0;
  background: #0f172a;
  color: white;
  padding: 25px 15px;
}

.container {
  max-width: 800px;
  margin: auto;
}

header {
  text-align: center;
  padding: 25px;
}

header h1 {
  color: #38bdf8;
  font-size: 32px;
}

header p {
  color: #cbd5e1;
}

.card {
  background: #1e293b;
  padding: 22px;
  border-radius: 14px;
  margin-bottom: 20px;
  border: 1px solid #334155;
}

h2 {
  color: #38bdf8;
  font-size: 20px;
}

label {
  display: block;
  margin: 15px 0 7px;
  font-weight: bold;
}

textarea, input {
  width: 100%;
  padding: 13px;
  border-radius: 8px;
  border: 1px solid #475569;
  background: #0f172a;
  color: white;
  font-size: 15px;
}

textarea {
  resize: vertical;
  min-height: 90px;
}

button {
  padding: 13px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 15px;
  font-weight: bold;
  margin-top: 15px;
  width: 100%;
}

.encrypt {
  background: #0284c7;
  color: white;
}

.decrypt {
  background: #16a34a;
  color: white;
}

button:hover {
  opacity: .85;
}

.result {
  background: #020617;
  border: 1px solid #334155;
  padding: 15px;
  border-radius: 8px;
  overflow-wrap: anywhere;
  color: #4ade80;
  min-height: 45px;
  font-size: 13px;
}

.step {
  border-left: 4px solid #38bdf8;
  padding: 12px;
  margin: 15px 0;
  background: #0f172a;
  border-radius: 5px;
}

.step strong {
  color: #38bdf8;
}

.small {
  color: #94a3b8;
  font-size: 13px;
}

footer {
  text-align: center;
  color: #64748b;
  padding: 20px;
}
</style>
</head>

<body>
<div class="container">

<header>
  <h1>🔐 CryptoCode</h1>
  <p>Sistema de Criptografia e Descriptografia</p>
  <p>Transforme mensagens em códigos protegidos.</p>
</header>

<div class="card">
  <h2>1. Criptografar mensagem</h2>

  <label>Digite sua mensagem, senha ou código:</label>
  <textarea id="mensagem"
    placeholder="Exemplo: Senha@123#"></textarea>

  <label>Chave secreta:</label>
  <input id="chave" type="password"
    placeholder="Digite uma chave secreta">

  <button class="encrypt" onclick="criptografar()">
    🔒 CRIPTOGRAFAR
  </button>
</div>

<div class="card">
  <h2>2. Resultado criptografado</h2>

  <p class="small">
    Este é o conteúdo transformado pelo algoritmo AES-256-GCM:
  </p>

  <div class="result" id="resultado">
    O resultado aparecerá aqui...
  </div>

  <button onclick="copiar()">
    Copiar código criptografado
  </button>
</div>

<div class="card">
  <h2>3. Descriptografar mensagem</h2>

  <label>Código criptografado:</label>
  <textarea id="codigo"
    placeholder="Cole aqui o código criptografado"></textarea>

  <label>Chave secreta:</label>
  <input id="chaveDec" type="password"
    placeholder="Digite a mesma chave utilizada">

  <button class="decrypt" onclick="descriptografar()">
    🔓 DESCRIPTOGRAFAR
  </button>

  <label>Mensagem recuperada:</label>
  <div class="result" id="recuperada">
    A mensagem original aparecerá aqui...
  </div>
</div>

<div class="card">
  <h2>4. Como aconteceu o processo?</h2>

  <div class="step">
    <strong>ETAPA 1 - Mensagem original</strong>
    <p>O usuário informa um texto contendo letras,
    números e símbolos.</p>
  </div>

  <div class="step">
    <strong>ETAPA 2 - Derivação da chave</strong>
    <p>A senha secreta passa pelo algoritmo PBKDF2,
    utilizando um salt aleatório e SHA-256,
    para produzir uma chave de 256 bits.</p>
  </div>

  <div class="step">
    <strong>ETAPA 3 - Criptografia AES-256-GCM</strong>
    <p>A mensagem é transformada em dados
    criptografados. São utilizados uma chave
    secreta e um IV aleatório.</p>
  </div>

  <div class="step">
    <strong>ETAPA 4 - Resultado</strong>
    <p>O conteúdo criptografado é convertido
    para Base64 para facilitar sua visualização
    e armazenamento.</p>
  </div>

  <div class="step">
    <strong>ETAPA 5 - Descriptografia</strong>
    <p>O sistema utiliza a mesma senha, o salt
    e o IV armazenados para recuperar a chave
    e decifrar a mensagem original.</p>
  </div>

  <div class="step">
    <strong>ETAPA 6 - Verificação</strong>
    <p>O AES-GCM verifica a autenticidade dos dados.
    Se a chave estiver errada ou os dados forem
    alterados, a descriptografia falhará.</p>
  </div>
</div>

<footer>
  Projeto educacional - Criptografia e Segurança Digital
</footer>

</div>

<script>
const encoder = new TextEncoder();
const decoder = new TextDecoder();

function toBase64(bytes) {
  let binary = "";
  for (const byte of bytes) {
    binary += String.fromCharCode(byte);
  }
  return btoa(binary);
}

function fromBase64(value) {
  const binary = atob(value);
  return Uint8Array.from(binary,
    char => char.charCodeAt(0));
}

async function gerarChave(senha, salt) {
  const material = await crypto.subtle.importKey(
    "raw",
    encoder.encode(senha),
    "PBKDF2",
    false,
    ["deriveKey"]
  );

  return crypto.subtle.deriveKey(
    {
      name: "PBKDF2",
      salt: salt,
      iterations: 600000,
      hash: "SHA-256"
    },
    material,
    {
      name: "AES-GCM",
      length: 256
    },
    false,
    ["encrypt", "decrypt"]
  );
}

async function criptografar() {
  try {
    const mensagem =
      document.getElementById("mensagem").value;

    const senha =
      document.getElementById("chave").value;

    if (!mensagem || !senha) {
      alert("Preencha a mensagem e a chave secreta!");
      return;
    }

    // 1. Criar salt aleatório
    const salt = crypto.getRandomValues(
      new Uint8Array(16)
    );

    // 2. Criar IV aleatório
    const iv = crypto.getRandomValues(
      new Uint8Array(12)
    );

    // 3. Derivar a chave
    const chave = await gerarChave(senha, salt);

    // 4. Criptografar
    const conteudo = await crypto.subtle.encrypt(
      { name: "AES-GCM", iv: iv },
      chave,
      encoder.encode(mensagem)
    );

    // 5. Montar pacote: salt + IV + conteúdo
    const pacote = new Uint8Array(
      salt.length + iv.length + conteudo.byteLength
    );

    pacote.set(salt, 0);
    pacote.set(iv, salt.length);
    pacote.set(new Uint8Array(conteudo),
      salt.length + iv.length);

    const codigo = toBase64(pacote);

    document.getElementById("resultado").textContent =
      codigo;

    document.getElementById("codigo").value =
      codigo;

    alert("Mensagem criptografada com sucesso!");

  } catch (erro) {
    alert("Erro ao criptografar: " + erro.message);
  }
}

async function descriptografar() {
  try {
    const codigo =
      document.getElementById("codigo").value.trim();

    const senha =
      document.getElementById("chaveDec").value;

    if (!codigo || !senha) {
      alert("Informe o código e a chave secreta!");
      return;
    }

    // 1. Decodificar Base64
    const pacote = fromBase64(codigo);

    if (pacote.length < 45) {
      throw new Error("Código inválido ou incompleto.");
    }

    // 2. Recuperar salt, IV e conteúdo
    const salt = pacote.slice(0, 16);
    const iv = pacote.slice(16, 28);
    const conteudo = pacote.slice(28);

    // 3. Derivar novamente a chave
    const chave = await gerarChave(senha, salt);

    // 4. Descriptografar
    const original = await crypto.subtle.decrypt(
      { name: "AES-GCM", iv: iv },
      chave,
      conteudo
    );

    // 5. Mostrar mensagem original
    document.getElementById("recuperada").textContent =
      decoder.decode(original);

  } catch (erro) {
    document.getElementById("recuperada").textContent =
      "Falha! Chave incorreta ou código inválido.";

  }
}

function copiar() {
  const codigo =
    document.getElementById("resultado").textContent;

  if (!codigo || codigo.includes("aparecerá")) {
    alert("Primeiro criptografe uma mensagem.");
    return;
  }

  navigator.clipboard.writeText(codigo)
    .then(() => alert("Código copiado!"))
    .catch(() => alert("Não foi possível copiar."));
}
</script>

</body>
</html>
