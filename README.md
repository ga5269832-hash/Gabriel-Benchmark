# Gabriel Multi-Model Benchmark — Plataforma MVP

Backend Python + FastAPI, SQLite e painel web conectado.

## Requisitos
- Python 3.10+
- Credencial de pelo menos um provedor para executar avaliações reais.
- Para Llama local, Ollama com endpoint OpenAI-compatible ativo.

## Instalação local
1. Extraia o ZIP.
2. Abra um terminal na pasta do projeto.
3. Crie um ambiente virtual:
   - Windows: `py -m venv .venv` e `.venv\Scripts\activate`
   - Linux/macOS: `python3 -m venv .venv` e `source .venv/bin/activate`
4. Instale dependências: `pip install -r requirements.txt`
5. Copie `.env.example` para `.env`.
6. Edite `.env`:
   - Defina `APP_SECRET` com uma string aleatória de pelo menos 32 caracteres.
   - Troque `ADMIN_PASSWORD`.
   - Insira as chaves dos provedores que deseja testar.
7. Inicie: `python start.py`
8. Acesse `http://127.0.0.1:8000`.

## Uso
- O painel consulta o backend automaticamente a cada 30 segundos.
- Clique em **Nova rodada**, entre com o usuário administrativo e senha do `.env`.
- Selecione repetições (1 a 3) e execute.
- A primeira rodada executa 30 cenários para cada provedor configurado.
- O ranking e histórico são atualizados a partir do SQLite.
- A documentação interativa da API fica em `/docs`.

## Provedores
- GPT: OpenAI Responses API.
- Claude: Anthropic Messages API.
- Gemini: Google Gemini generateContent API.
- Llama: endpoint `/v1/chat/completions` compatível com OpenAI (Ollama local por padrão).

## Segurança
- As chaves ficam no servidor, no `.env`; o frontend nunca recebe as credenciais.
- Não faça commit do `.env`.
- Use HTTPS, senha forte, `APP_SECRET` aleatório, firewall e proxy reverso antes de publicar na internet.
- O login administrativo deste MVP usa token assinado com validade de 8 horas. Não substitui um sistema corporativo de identidade, proteção contra brute force ou rotação de sessões.
- Restrinja `CORS_ORIGINS` aos domínios reais quando publicar.
- Não exponha o servidor de desenvolvimento `--reload` na internet.

## Sobre as pontuações
A avaliação incluída é uma heurística inicial e transparente, baseada em verificações simples da resposta. Ela serve para validar o fluxo técnico e comparar regressões grosseiras, mas não equivale a uma avaliação científica ou a um juiz independente. Antes de usar os resultados como ranking público ou decisão de produto, revise a rubrica, adicione avaliadores independentes, testes adversariais revisados e amostras humanas. Falhas de API são registradas separadamente e não entram na média.

## Limitações desta versão
- Execução sequencial; rodadas longas podem levar vários minutos.
- Sem fila de tarefas ou cancelamento.
- Sem recuperação de senha nem gestão de múltiplos administradores.
- SQLite é adequado para desenvolvimento e uso pequeno; use PostgreSQL para escala.
- Alguns modelos/IDs podem mudar; ajuste os nomes no `.env` conforme o acesso da conta.

## Publicação no Render
1. Envie o conteúdo desta pasta para um repositório GitHub.
2. No Render, escolha **New + → Blueprint** e conecte o repositório.
3. O Render detectará `render.yaml` e preparará o serviço `gabriel-benchmark`.
4. Informe `ADMIN_PASSWORD` quando solicitado. Cadastre também as chaves dos provedores que deseja testar (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `GEMINI_API_KEY`).
5. Confirme a criação. Após o deploy, abra o endereço `https://gabriel-benchmark.onrender.com` (ou o domínio gerado pelo Render).

**Persistência:** o Blueprint monta um disco em `/var/data` e grava o SQLite em `/var/data/benchmark.db`. O disco persistente do Render exige um plano pago. Não remova esse disco se quiser preservar o histórico das avaliações.

**Observação:** o endpoint Llama precisa ser acessível pela internet a partir do Render; `localhost` ou um Ollama rodando apenas no seu computador não ficará acessível ao serviço hospedado.
