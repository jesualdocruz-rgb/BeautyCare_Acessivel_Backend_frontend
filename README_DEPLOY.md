# BeautyCare Acessível — pacote para publicação (Vercel + Render)

Este pacote prepara uma UI React/Vite e uma API FastAPI para publicação. **A publicação real exige que você entre nas suas contas GitHub, Render e Vercel e faça os passos abaixo**; este pacote não cria contas, repositórios nem publica por você.

## 1. Estrutura
- `api/`: FastAPI + SQLAlchemy + SQLite, endpoints de usuários, profissionais, serviços e agendamentos.
- `ui/`: React + Vite, pesquisa e listagem de profissionais com suporte a fonte maior.
- `documentacao/Plano_Sprints.md`: tarefas e status propostos para organizar no Trello.

## 2. Testar localmente
Requer Python 3.10+ e Node.js LTS. Abra dois terminais no VS Code.

### Terminal 1 — API
```bash
cd api
python -m venv .venv
# Windows PowerShell:
.venv\Scripts\Activate.ps1
# Se o PowerShell bloquear a ativação, use CMD e execute: .venv\Scripts\activate.bat
pip install -r requirements.txt
python seed.py
uvicorn app.main:app --reload
```
Swagger: `http://127.0.0.1:8000/docs`; saúde: `http://127.0.0.1:8000/health`.

### Terminal 2 — UI
```bash
cd ui
npm install
# crie .env com a linha abaixo (ou copie .env.example e ajuste):
# VITE_API_URL=http://127.0.0.1:8000
npm run dev
```
Abra a URL local mostrada pelo Vite.

## 3. Subir para GitHub
Crie um repositório vazio, por exemplo `beautycare-acessivel`. Na raiz desta pasta: `git init`, `git add .`, `git commit -m "feat: prepara BeautyCare para deploy"`, `git branch -M main`, `git remote add origin URL_DO_REPOSITORIO` e `git push -u origin main`. Substitua a URL pelo endereço real do repositório. Não envie `.venv`, `node_modules`, senhas ou tokens.

## 4. Publicar API no Render
1. Entre em `https://render.com` e conecte o GitHub.
2. Crie **New + → Web Service** e selecione o repositório.
3. Em **Root Directory**, informe `api`.
4. Runtime: Python. Build Command: `pip install -r requirements.txt`.
5. Start Command: `python seed.py && uvicorn app.main:app --host 0.0.0.0 --port $PORT`.
6. Health Check Path: `/health`, se o campo estiver disponível.
7. Clique em Deploy e aguarde a URL pública, por exemplo `https://beautycare-acessivel-api.onrender.com`. O exemplo é ilustrativo: use a URL que o Render realmente gerar.
8. Teste `URL_GERADA/health` e `URL_GERADA/docs`.

## 5. Publicar UI na Vercel
1. Entre em `https://vercel.com` e importe o mesmo repositório GitHub.
2. Configure **Root Directory** como `ui`. Framework: Vite. Build Command: `npm run build`; Output Directory: `dist`; Install Command: `npm install`.
3. Em **Environment Variables**, crie `VITE_API_URL` com a URL real do serviço Render, sem barra no final e sem `/docs`. Ex.: `https://SUA-API-REAL.onrender.com`.
4. Faça o deploy. A cada mudança, envie commits para GitHub e a Vercel poderá reconstruir o projeto.
5. Teste a URL pública da Vercel e verifique se os profissionais aparecem. Se não aparecerem, confira a variável `VITE_API_URL`, os logs do Render e a aba Console/Network do navegador.

## 6. Organização do Trello e evidências
Use as listas `PRODUCT BACKLOG → SPRINT 1 – A FAZER → EM DESENVOLVIMENTO → EM TESTE → CONCLUÍDO`. Mova cada cartão somente após realizar e testar a tarefa.

**Back-end — cartões sugeridos (10):**
1. API-01 Definir tecnologias back-end.
2. API-02 Criar projeto FastAPI.
3. API-03 Configurar ambiente e dependências.
4. API-04 Configurar SQLite/SQLAlchemy.
5. API-05 Criar modelos de domínio.
6. API-06 Implementar validação dos dados.
7. API-07 Implementar usuários e profissionais.
8. API-08 Implementar serviços e agendamentos.
9. API-09 Implementar busca/filtro e CORS.
10. API-10 Documentar e testar Swagger/deploy.

**UI — cartões sugeridos (12):**
1. UI-01 Definir tecnologias front-end.
2. UI-02 Criar projeto React/Vite.
3. UI-03 Configurar Git e repositório remoto.
4. UI-04 Criar identidade visual acessível.
5. UI-05 Estruturar login/cadastro.
6. UI-06 Criar tela inicial.
7. UI-07 Implementar busca de profissionais.
8. UI-08 Exibir perfil profissional.
9. UI-09 Estruturar fluxo de agendamento.
10. UI-10 Estruturar agenda.
11. UI-11 Testar responsividade e fonte ampliada.
12. UI-12 Integrar UI com API e publicar na Vercel.

**Evidências para entregar:** URL do GitHub; URL pública da Vercel; URL pública do Render; captura do Swagger `/docs`; captura da interface publicada; captura do Trello com cartões e status; commits reais no histórico.

## Limitações importantes do MVP
- A base SQLite no disco padrão do Render pode ser apagada em reinicializações/redeploys e não é adequada para persistência garantida. Para uso real, configure PostgreSQL persistente.
- O cadastro atualmente armazena o campo de senha como texto simples: não use dados pessoais reais nem publique como serviço de produção sem implementar hash de senha, autenticação/autorização e política de privacidade.
- A UI é um MVP acadêmico; não equivale a um sistema completo de autenticação ou pagamentos.
