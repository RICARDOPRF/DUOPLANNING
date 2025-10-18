# Duoplanning PWA

## Como instalar no Android (PWA)
1. Hospede os arquivos em um servidor HTTPS (pode usar GitHub Pages, Vercel, Netlify).
2. Abra a URL no Chrome do Android.
3. Clique no menu (⋮) > "Adicionar à tela inicial".

## Como gerar APK (Trusted Web Activity)
1. Instale Node.js, JDK 11+ e Android Studio.
2. Instale Bubblewrap:
   ```bash
   npm install -g @bubblewrap/cli
   ```
3. Inicialize o projeto:
   ```bash
   bubblewrap init --manifest=https://SEU_SITE/manifest.json
   ```
4. Configure nome, pacote e ícones.
5. Gere a chave de assinatura:
   ```bash
   keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
   ```
6. Compile o APK:
   ```bash
   bubblewrap build
   ```
7. Instale no Android ou publique na Play Store.
