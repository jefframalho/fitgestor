# FitGestor — sistema para várias academias

Um único sistema (um link só) com três tipos de acesso:

| Quem | O que faz |
|---|---|
| 👑 **Você (dono do sistema)** | Cadastra academias, define a mensalidade de cada uma, registra pagamentos, cobra pelo WhatsApp e bloqueia/desbloqueia. Academia com mais de 5 dias de atraso é suspensa automaticamente. |
| 🏋️ **Academia** | Cadastro de alunos, mensalidades (gera o mês com 1 clique, cobra e manda recibo pelo WhatsApp), avaliações físicas com metas e gráfico, montagem de treinos (A/B/C…) e envio da ficha pelo WhatsApp, criação do login do aluno. |
| 🙋 **Aluno** | Painel no celular: vê os treinos e marca os exercícios feitos, acompanha a evolução das medidas e metas, vê as mensalidades e copia o Pix da academia. |

---

## 1. Testar agora (modo demonstração)

Abra o `index.html` no navegador. Na tela de login há botões de acesso rápido
(senha de todos: `123456`). Os dados ficam só no seu navegador.

## 2. Colocar para funcionar de verdade (Firebase — plano grátis)

1. Acesse **console.firebase.google.com** → **Adicionar projeto** (ex.: `fitgestor`).
2. **Authentication** → Começar → ative **E-mail/senha**.
3. **Firestore Database** → Criar banco → modo produção → região `southamerica-east1 (São Paulo)`.
4. Firestore → aba **Regras** → apague tudo, cole o conteúdo de `firestore.rules` → **Publicar**.
5. Engrenagem ⚙️ → **Configurações do projeto** → **Seus apps** → ícone `</>` (Web) → registre o app.
   Copie o objeto `firebaseConfig`.
6. No `index.html`, troque `const FIREBASE_CONFIG = null;` pelo objeto copiado, ex.:
   ```js
   const FIREBASE_CONFIG = { apiKey:"...", authDomain:"...", projectId:"...", storageBucket:"...", messagingSenderId:"...", appId:"..." };
   ```

### Criar o seu login de dono do sistema (só uma vez)
1. **Authentication** → **Usuários** → **Adicionar usuário** → seu e-mail e senha. Copie o **UID**.
2. **Firestore** → **Iniciar coleção** → nome `usuarios` → ID do documento = **o UID copiado** → campos:
   - `papel` (string) = `superadmin`
   - `nome` (string) = seu nome
   - `email` (string) = seu e-mail
3. Entre no sistema → **Configurações** → coloque sua chave Pix e seu WhatsApp (aparecem para as academias na hora de pagar).

Pronto: a partir daí você cria as academias pelo próprio sistema, e cada academia cria o acesso dos alunos dela.

## 3. Publicar na internet

**Opção fácil — GitHub Pages:** crie um repositório (ex.: `fitgestor`), envie o `index.html`,
vá em *Settings → Pages → Deploy from branch → main*. O link fica
`https://SEU-USUARIO.github.io/fitgestor/`.

**Opção Firebase Hosting:** `npm i -g firebase-tools` → `firebase login` → `firebase init hosting`
→ coloque o `index.html` na pasta `public` → `firebase deploy`.

Depois de publicar, em **Authentication → Configurações → Domínios autorizados**, adicione o domínio
do site (ex.: `seu-usuario.github.io`).

## Ficha do aluno e Inteligência Artificial

Na ficha de cada aluno, a academia tem:
- **Anamnese:** nível, disponibilidade, condições de saúde, medicamentos, dores/lesões e questionário **PAR-Q**. Se o PAR-Q der "sim", o sistema marca a liberação médica como pendente.
- **Avaliação física:** peso, altura, % de gordura, pressão, frequência cardíaca e 12 perímetros, com lado direito e esquerdo. O sistema calcula IMC, massa gorda e magra e a relação cintura/quadril com o risco, e mostra a evolução desde a 1ª avaliação.
- **📄 Ficha completa:** abre a ficha pronta para imprimir ou salvar em PDF, com espaço para assinaturas.
- **✨ Gerar com IA** (aba Treinos): a IA lê a ficha e monta a divisão de treino, respeitando lesões e saúde. O instrutor revisa antes de salvar.
- **✨ Análise da IA** (aba Avaliação física): resumo da evolução, pontos de atenção e uma mensagem pronta para mandar ao aluno.

### Ativar a IA (Google Gemini — tem plano grátis)
1. Acesse **aistudio.google.com** com sua conta Google → **Get API key** → **Create API key**.
2. No FitGestor, entre como dono → **Configurações** → cole a chave → **Salvar chave** → **Testar IA**.
3. **Proteja a chave** (importante): em **console.cloud.google.com → APIs e serviços → Credenciais**, abra a chave → *Restrições de aplicativos* → **Referenciadores HTTP** → adicione `https://jefframalho.github.io/*` → Salvar. Assim, ela só funciona no seu site.
4. Ao criar ou editar uma academia, você escolhe se a **IA está incluída no plano**, o que dá para vender como diferencial. Na lista de academias aparece quantas vezes cada uma usou a IA.

A IA recebe só os dados de saúde e treino. Nome, telefone e e-mail do aluno **não** são enviados.

## Como funciona a cobrança das academias

- Cada academia tem **valor** e **vencimento**. Ao clicar em **Receber**, o vencimento avança 1, 2, 3, 6 ou 12 meses.
- 5 dias antes do vencimento a academia vê um aviso; depois de vencida, aviso vermelho;
  passados **5 dias de atraso**, o acesso é suspenso e aparece a tela com sua chave Pix
  (mude em `TOLERANCIA_DIAS` no início do código).
- O botão **Bloquear** corta o acesso na hora, inclusive nas regras do banco de dados.

## Observações

- O link é o mesmo para todos: o sistema identifica pelo login se é você, uma academia ou um aluno.
- "Remover acesso" do aluno apaga o perfil (ele não entra mais). Para apagar o e-mail de vez,
  remova também em Authentication → Usuários.
- O plano grátis do Firebase (Spark) aguenta bem dezenas de academias pequenas.
