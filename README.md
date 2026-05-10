# SDP — Sistema de Delegações e Prazos

## Como subir no Vercel (5 minutos)

### 1. Criar conta no Vercel
- Acesse vercel.com e crie uma conta gratuita
- Pode entrar com Google ou GitHub

### 2. Subir os arquivos
- No painel do Vercel, clique em "Add New Project"
- Escolha "Deploy without Git" (ou faça upload direto)
- Arraste a pasta `sdp/` inteira
- Clique em Deploy

### 3. Criar conta no Supabase (para salvar dados)
- Acesse supabase.com e crie conta gratuita
- Crie um novo projeto
- Vá em Settings → API e copie:
  - Project URL
  - anon/public key

### 4. Conectar o banco
No arquivo `index.html`, substitua:
```
const SUPA_URL = 'SUPABASE_URL_AQUI';
const SUPA_KEY = 'SUPABASE_ANON_KEY_AQUI';
```

Cole suas credenciais do Supabase.

### 5. Criar as tabelas no Supabase
No painel do Supabase, vá em SQL Editor e execute:

```sql
-- Tabela de perfis dos usuários
create table perfis (
  id uuid default gen_random_uuid() primary key,
  user_id uuid references auth.users not null unique,
  nome text not null,
  cargo text default 'membro', -- admin, intermediario, membro
  cor text default '#185FA5',
  iniciais text,
  created_at timestamp default now()
);

-- Tabela de tarefas
create table tarefas (
  id uuid default gen_random_uuid() primary key,
  titulo text not null,
  orientacao text,
  prazo date,
  status text default 'afazer', -- afazer, fazendo, concluido
  prioridade text default 'media', -- baixa, media, alta
  tipo text default 'interno', -- processo, interno
  responsavel_id uuid,
  responsavel text,
  processo text,
  criado_por uuid references auth.users,
  created_at timestamp default now()
);

-- Tabela de processos
create table processos (
  id uuid default gen_random_uuid() primary key,
  titulo text not null,
  cnj text,
  cliente text,
  acao text,
  vara text,
  responsavel text,
  status text default 'ativo',
  atualizado text,
  criado_por uuid references auth.users,
  created_at timestamp default now()
);

-- Tabela de comentários
create table comentarios (
  id uuid default gen_random_uuid() primary key,
  tarefa_id uuid references tarefas,
  autor_id uuid references auth.users,
  autor_nome text,
  texto text not null,
  created_at timestamp default now()
);

-- Habilitar RLS (segurança por usuário)
alter table perfis enable row level security;
alter table tarefas enable row level security;
alter table processos enable row level security;
alter table comentarios enable row level security;

-- Políticas de acesso (todos os usuários autenticados veem tudo)
create policy "usuarios autenticados" on perfis for all using (auth.role() = 'authenticated');
create policy "usuarios autenticados" on tarefas for all using (auth.role() = 'authenticated');
create policy "usuarios autenticados" on processos for all using (auth.role() = 'authenticated');
create policy "usuarios autenticados" on comentarios for all using (auth.role() = 'authenticated');
```

### 6. Ativar e-mail de convite no Supabase
- Vá em Authentication → Settings
- Confirme que "Enable email confirmations" está ativo
- Personalize o template de convite se quiser

### 7. Instalar como app (PWA)
- Android: abra no Chrome → menu → "Adicionar à tela inicial"
- iOS: abra no Safari → compartilhar → "Adicionar à tela de início"
- Windows: abra no Edge/Chrome → ícone de instalar na barra de endereço

## Arquivos
```
sdp/
├── index.html    ← aplicação completa
├── manifest.json ← configuração PWA
└── README.md     ← este arquivo
```

## Modo demonstração
Sem as credenciais do Supabase, o sistema roda em modo demonstração
com dados de exemplo. Nenhum dado é salvo — ideal para testar.
