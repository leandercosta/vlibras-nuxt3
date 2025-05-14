VLibras (gov.br) to Nuxt 3
==========================

Mais informações sobre o [VLibras](https://vlibras.gov.br/)

### To install

```
npm i vlibras-nuxt3
```

### To Use

```
<template>
  <VLibras/>
  <div>
    <p>Olá, bom dia.</p>
  </div>
</template>

<script>
import VLibras from 'vlibras-nuxt3'
export default {
  components:{VLibras}
}
</script>
```

Requisitos SSL
--------------

-   A app Nuxt precisa estar usando SSL para que não haja instabilidade já que o script é carregado diretamente do site do Governo Brasileiro (gov.br) com protocolo https. A comunicação entre sua aplicação e o serviço VLibras precisa ser segura para garantir o funcionamento correto.

Configurando devServer com domínio personalizado e SSL
------------------------------------------------------

Para criar um servidor de desenvolvimento Nuxt com um domínio personalizado e SSL, siga estas etapas:

### 1\. Configurando um domínio local

Adicione uma entrada no arquivo hosts do seu sistema operacional:

**Linux/Mac:**

```
sudo nano /etc/hosts
```

**Windows:** Abra o Bloco de Notas como administrador e edite:

```
C:\Windows\System32\drivers\etc\hosts
```

Adicione a linha:

```
127.0.0.1  meu-app-local.com
```

### 2\. Criando um certificado autoassinado

Você pode criar certificados autoassinados utilizando uma das seguintes ferramentas:

#### Opção 1: Usando mkcert (Recomendado)

O [mkcert](https://github.com/FiloSottile/mkcert) é uma ferramenta simples que facilita a criação de certificados de desenvolvimento localmente confiáveis. Está disponível para:

-   Windows
-   macOS
-   Linux

**Instalação:**

**macOS:**

bash

```
brew install mkcert
brew install nss # se você usa Firefox
```

**Windows:**

bash

```
choco install mkcert
# ou
scoop bucket add extras
scoop install mkcert
```

**Linux (Debian/Ubuntu):**

bash

```
sudo apt install libnss3-tools
sudo apt install mkcert
# ou faça download binário da release mais recente do GitHub
```

**Criando certificados:**

bash

```
# Crie uma pasta para os certificados
mkdir -p ssl

# Instale a CA local (autoridade certificadora)
mkcert -install

# Crie certificados para seu domínio
cd ssl
mkcert meu-app-local.com localhost 127.0.0.1 ::1

# Isso gera dois arquivos:
# - meu-app-local.com+3.pem (certificado)
# - meu-app-local.com+3-key.pem (chave privada)
```

A vantagem do mkcert é que ele adiciona a CA (autoridade certificadora) automaticamente ao seu sistema, eliminando os avisos de segurança no navegador.

#### Opção 2: Usando OpenSSL:

OpenSSL está disponível em praticamente todos os sistemas operacionais (Windows, macOS e Linux).

bash

```
# Crie uma pasta para armazenar os certificados
mkdir -p ssl

# Gere uma chave privada
openssl genrsa -out ssl/key.pem 2048

# Crie um arquivo de configuração para o certificado
cat > ssl/openssl.conf << EOF
[req]
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no

[req_distinguished_name]
CN = meu-app-local.com

[v3_req]
subjectAltName = @alt_names

[alt_names]
DNS.1 = meu-app-local.com
DNS.2 = localhost
EOF

# Gere o certificado
openssl req -new -x509 -key ssl/key.pem -out ssl/cert.pem -days 365 -config ssl/openssl.conf
```

### 3\. Configurando o Nuxt 3

No arquivo `nuxt.config.ts`, adicione:

typescript

```
export default defineNuxtConfig({
  // ... outras configurações

  devServer: {
    port: 3000,
    host: 'meu-app-local.com',
    https: {
      // Se você usou mkcert:
      key: './ssl/meu-app-local.com+3-key.pem',
      cert: './ssl/meu-app-local.com+3.pem'

      // Se você usou OpenSSL:
      // key: './ssl/key.pem',
      // cert: './ssl/cert.pem'
    }
  },

  // Se estiver usando Vite com Nuxt 3:
  vite: {
    server: {
      https: true
    }
  }
})
```

### 4\. Iniciando o servidor de desenvolvimento

bash

```
npm run dev
```

Agora você pode acessar sua aplicação em `https://meu-app-local.com:3000`

**Nota:** Ao acessar pela primeira vez, o navegador irá mostrar um aviso de segurança porque o certificado é autoassinado. Você precisará adicionar uma exceção de segurança ou clicar em "Avançado" e depois "Continuar para o site" (o texto exato varia conforme o navegador).

Ambiente de produção
--------------------

Em produção, é recomendado utilizar certificados SSL válidos emitidos por uma autoridade certificadora confiável, como Let's Encrypt.