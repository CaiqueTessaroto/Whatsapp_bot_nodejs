# 📲 Whatsapp Bot Node.js

Bot de automação para WhatsApp desenvolvido em **Node.js** utilizando **Puppeteer**, capaz de enviar mensagens de boas-vindas automaticamente e responder mensagens, com controle via API REST.

---

## 🚀 Funcionalidades

- Envio automático de mensagens de boas-vindas
- Envio manual de mensagens via API
- Autenticação via QR Code
- Controle de status do bot
- Reset diário para reenvio de boas-vindas
- Integração simples com qualquer backend

---

## 🗄️ Estrutura do Banco de Dados

Antes de iniciar, crie uma tabela no banco de dados com os seguintes campos:

```sql
id INT AUTO_INCREMENT PRIMARY KEY,
nome VARCHAR(255),
whatsapp VARCHAR(20),
id_bot VARCHAR(255),
boas_vindas TEXT
```

---

## 🔄 Fluxo de Funcionamento

1. Cadastrar o usuário/prestador  
2. Obter o token (id do bot)  
3. Inicializar o bot  
4. Gerar e escanear o QR Code  
5. Acompanhar status  
6. Enviar mensagens  
7. Resetar o dia para reenviar boas-vindas  

---

## 📌 Endpoints da API

### 🔹 POST `/Cadastro`

Cadastra um novo prestador/bot.

```json
{
  "nome": "",
  "whatsapp": "",
  "boas_vindas": ""
}
```

**Retorno:** Cadastro criado com sucesso.

---

### 🔹 POST `/Update/{id}`

Atualiza os dados de um prestador.

```json
{
  "nome": "",
  "whatsapp": "",
  "boas_vindas": ""
}
```

**Retorno:** Prestador atualizado.

---

### 🔹 GET `/Todos`

Retorna todos os prestadores cadastrados.

---

### 🔹 GET `/Status/{id}`

Retorna o status atual do bot.

---

### 🔹 POST `/Inicializar/{id}`

Inicializa o bot utilizando o ID informado.

**Retorno:** `Inicializado`

---

### 🔹 GET `/RequestQR/{id}`

Gera o QR Code para autenticação no WhatsApp.

**Retorno:** QR Code em Base64.

---

### 🔹 GET `/Token/{id}`

Retorna o identificador do bot (`id_bot`).

---

### 🔹 POST `/Enviar`

Envia uma mensagem manualmente.

```json
{
  "id": "",
  "whatsapp": "",
  "mensagem": ""
}
```

**Retorno:** Mensagem enviada.

---

### 🔹 POST `/Resetar_dia`

Obrigatório executar diariamente para permitir o reenvio das mensagens de boas-vindas.

---

## 🧪 Exemplo de Uso em PHP

```php
public static function criarPrestador($nome, $whatsapp, $boasVindas){
    $client = self::getClient();
    $url = self::getUrl('/Cadastro');

    $headers = [
        'Content-Type' => 'application/json',
    ];

    $body = json_encode([
        "nome" => $nome,
        "whatsapp" => preg_replace('/(\(|\)|\-|\s)/', '', $whatsapp),
        "boas_vindas" => $boasVindas
    ]);

    try {
        $response = $client->request('POST', $url, [
            'body' => $body,
            'headers' => $headers
        ]);

        $resposta = $response->getBody();
        $codigo = $response->getStatusCode();

    } catch (GuzzleException $e) {
        return false;
    }

    if ($codigo >= 200 && $codigo < 300) {
        return json_decode($resposta);
    }

    return false;
}
```

---

## ⚠️ Observações Importantes

- O QR Code deve ser escaneado pelo WhatsApp do prestador
- Caso algo não funcione, utilize o endpoint `/Status`
- O reset diário é essencial para o funcionamento correto das boas-vindas
- O projeto utiliza automação de navegador e pode sofrer alterações conforme mudanças no WhatsApp Web
