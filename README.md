# Help Desk

Sistema de chamados com perfis de acesso (`usuario`, `agente`, `admin`), relatório em PDF por período e login com Supabase Auth.
Backend em **FastAPI** com **Arquitetura Limpa**, frontend estático separado, deploy em **dois projetos na Vercel** + **Supabase**.

## Estrutura
```
backend/
  api/index.py            entrypoint da Vercel
  helpdesk/
    domain/               entidades, regras de negócio e portas (sem dependências externas)
    application/          casos de uso
    infrastructure/       Supabase, PDF (fpdf2), relógio, configurações
    presentation/         rotas FastAPI, schemas, tratamento de erros, composição
  tests/                  testes com dependências falsas (sem banco)
frontend/                 HTML + CSS + JS puro (config.js com as chaves públicas)
supabase/schema.sql       tabelas, trigger de perfil e RLS
```
Regra de dependência: `presentation → application → domain` e `infrastructure → domain`. O domínio não importa FastAPI nem Supabase.

## Rodar localmente
1. Supabase: rode `supabase/schema.sql` no SQL Editor.
2. Backend: `cd backend && cp .env.example .env` (preencha), depois
   `pip install -r requirements-dev.txt && export $(grep -v '^#' .env | xargs) && uvicorn api.index:app --reload`
3. Frontend: edite `frontend/config.js` e rode `python -m http.server 5500 -d frontend` (http://localhost:5500).
4. Testes: `cd backend && pytest`
5. Promover alguém: `update public.profiles set role = 'admin' where id = '<uuid>';`

## Deploy (dois projetos na Vercel, mesmo repositório)
- **Backend:** Root Directory `backend`. Variáveis: `SUPABASE_URL`, `SUPABASE_SERVICE_KEY`, `CORS_ORIGINS` (URL do frontend).
- **Frontend:** Root Directory `frontend`, preset "Other". Antes do deploy, preencha `config.js` com a URL do backend e as chaves públicas do Supabase.
- Supabase, em Authentication > URL Configuration: Site URL e Redirect URLs apontando para o frontend.
