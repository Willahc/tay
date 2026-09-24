# TAY — Talking About You
### PRD Técnico (Product Requirements Document)
**Versão:** 1.0 (rascunho inicial) · **Baseado em:** TAY.pdf + Aplicativo_TAY.pdf (wireframes)

---

## 1. Visão Geral

TAY é um app de perguntas e respostas no estilo "caderno de amizade" da época de escola, levado para o digital: a pessoa responde a um conjunto de perguntas pré-definidas, escolhe a fonte e a cor da sua "letra", e ao terminar convida um amigo — que entra, vê as respostas anteriores (anonimizadas) e sugere a próxima pergunta da fila.

**Proposta de valor:** nostalgia + curiosidade social + personalização visual, com crescimento por convite em cadeia (loop viral nativo).

**Modelo de negócio:** freemium — app geral gratuito, monetizado por moedas (revelar quem respondeu) e por uma versão paga de "grupo fechado" (círculo de amigos com limite de usuários).

---

## 2. Personas

| Persona | Motivação | Comportamento |
|---|---|---|
| **Curioso(a) social** | Quer saber o que os amigos pensam/sentem | Responde rápido, convida, às vezes compra moedas pra revelar identidade |
| **Nostálgico(a)** | Lembra do caderninho de perguntas da escola | Valoriza customização visual (fonte/cor), guarda como "diário" |
| **Dono(a) de grupo fechado** | Quer um caderno privado só com a turma dele(a) | Paga pela versão de grupo fechado, modera quem entra |

---

## 3. Fluxo do Usuário (a partir dos wireframes)

```
[Login] → [Personalização: foto, fonte, cor]
   ↓
[Tela de Pergunta Nº000X]
   - exibe respostas anteriores anonimizadas, cada uma na fonte/cor de quem respondeu
   - campo de resposta da pessoa atual
   - opção "🔒 Ver quem respondeu" → Loja de Moedas
   ↓ (repete até a última pergunta disponível)
[Tela de Conclusão]
   - "Parabéns, você respondeu tudo!"
   - Enviar convite (Send Invite)
   - Sugerir nova pergunta (entra em fila de moderação)
   ↓
[Loja de Moedas] (Pix / Boleto / Cartão Crédito / Cartão Débito)
```

**Regra de customização:** a pessoa não pode repetir a fonte+cor da sua própria última resposta (força variação visual ao longo do tempo).

---

## 4. Requisitos Funcionais

**RF1 — Conta e perfil**
- Cadastro/login por e-mail + senha, recuperação de senha
- Foto de perfil (opcional), nome
- Fonte e cor padrão da "letra", com histórico da última combinação usada (para bloquear repetição)

**RF2 — Motor de perguntas**
- Perguntas numeradas sequencialmente (Nº0000, Nº0001...) e exibidas em ordem fixa
- Ao responder a todas (ou X% configurável) das perguntas disponíveis, a pessoa libera: (a) convite para amigo, (b) campo de sugestão de nova pergunta
- Pergunta sugerida entra em **fila de moderação** (status: pendente → aprovada/rejeitada) antes de virar a próxima pergunta da sequência
- **Decisão confirmada:** toda pergunta aprovada (vinda de sugestão de qualquer usuário, de qualquer grupo) entra na **sequência global** do app — não fica restrita a quem sugeriu nem ao grupo de origem
- Durante a moderação, cada pergunta recebe um **rótulo de classificação indicativa** (L, 10, 12, 14, 16, 18 — mesmas faixas do Ministério da Justiça), que define quem pode vê-la (ver RF6)

**RF3 — Respostas e anonimato (revisado)**
- Resposta é exibida para outros usuários como "Anônimo" + fonte/cor escolhida — **nunca** o nome real, por padrão
- **Mudança de modelo proposta:** revelar identidade exige consentimento mútuo (opt-in) — não é simplesmente "pagar para ver quem é". Quem quiser ser revelado marca a resposta como revelável; só então ela pode ser desbloqueada com moedas por quem também tiver resposta revelável equivalente, ou pelo próprio autor permitir individualmente.
- Sistema de moedas: compra via Pix/Boleto/Cartão (crédito/débito); gasto unitário por revelação de identidade

**RF4 — Convites e crescimento**
- Convite por link único (e-mail/WhatsApp/compartilhamento nativo)
- Rastreamento de quem convidou quem (para métricas de crescimento)
- **Decisão confirmada:** convite recompensado em moedas. Sugestão de regra anti-fraude: o convidador só recebe a recompensa quando o convidado **aceita o convite E responde à primeira pergunta** (evita farm de contas fantasma). Valor sugerido inicial: 15 moedas por convite efetivo — ajustável conforme o preço do pacote de moedas
- A partir da 2ª pergunta concluída, permitir múltiplos convites simultâneos (não só 1 por vez) para acelerar crescimento

**RF5 — Grupos fechados (paid)**
- Criação de um "caderno" privado vinculado a um grupo de amigos, com limite de usuários definido pelo criador (que paga)
- Perguntas e respostas do grupo fechado são isoladas do feed geral (exceto perguntas sugeridas aprovadas, que viram globais — ver RF2)
- **Decisão confirmada:** a moeda é **única para o app inteiro** por padrão. Quando um grupo fechado é vendido/licenciado para um conjunto específico de pessoas (ex: uma empresa, uma turma, uma família grande), **esse grupo decide** se usa a moeda global ou uma economia de moedas isolada só para o próprio grupo (`groups.coin_economy`)

**RF6 — Moderação**
- Fila de revisão humana para perguntas sugeridas
- Pré-filtro automático antes da fila humana: lista de termos bloqueados, checagem de duplicidade, limite de caracteres
- **Decisão confirmada:** não existe uma faixa etária mínima fixa para o app inteiro. Cada pergunta recebe um rótulo de classificação indicativa no momento da moderação (L, 10, 12, 14, 16, 18). Um usuário só vê (e só pode responder) perguntas compatíveis com a idade calculada a partir do `birth_year` informado no cadastro. A faixa etária do app nas lojas (Play Store/App Store) passa a ser a **classificação mais alta entre as perguntas ativas no feed geral** — então o rótulo da loja sobe automaticamente se uma pergunta mais "pesada" for aprovada

---

## 5. Requisitos Não-Funcionais

- **Privacidade:** dados de identidade (nome, foto, e-mail) nunca expostos sem consentimento explícito por resposta — não só por usuário
- **Performance:** feed de respostas deve paginar (não carregar histórico completo de uma vez) à medida que a base de perguntas cresce
- **Auditoria:** toda revelação de identidade e transação de moeda deve ser logada (rastreabilidade para suporte/disputas)
- **LGPD:** base legal para tratamento de dados de menores de idade precisa ser definida explicitamente, dado o público observado nos exemplos (nascidos em 2008/2011)

---

## 6. Modelo de Dados (PostgreSQL)

```sql
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

CREATE TABLE groups (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  owner_id UUID, -- FK adicionada após criação de users
  is_paid BOOLEAN DEFAULT false,
  member_limit INT,
  coin_economy TEXT DEFAULT 'global' CHECK (coin_economy IN ('global','isolated')), -- decidido pelo grupo licenciado
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  birth_year INT,
  avatar_url TEXT,
  answer_font TEXT DEFAULT 'Kalam',
  answer_color TEXT DEFAULT '#1A1A2E',
  last_font TEXT,         -- para regra "não repetir última combinação"
  last_color TEXT,
  coin_balance INT DEFAULT 0,
  invited_by UUID REFERENCES users(id),
  group_id UUID REFERENCES groups(id),
  created_at TIMESTAMPTZ DEFAULT now()
);

ALTER TABLE groups ADD CONSTRAINT fk_owner FOREIGN KEY (owner_id) REFERENCES users(id);

CREATE TABLE questions (
  id SERIAL PRIMARY KEY,
  sequence_number INT UNIQUE NOT NULL, -- ordem de exibição (Nº0000, Nº0001...)
  text TEXT NOT NULL,
  status TEXT DEFAULT 'approved' CHECK (status IN ('pending','approved','rejected')),
  content_rating TEXT DEFAULT 'L' CHECK (content_rating IN ('L','10','12','14','16','18')),
  suggested_by UUID REFERENCES users(id),
  group_id UUID REFERENCES groups(id), -- NULL = pergunta global (toda pergunta aprovada vira global)
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE answers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  question_id INT REFERENCES questions(id),
  answer_text TEXT NOT NULL,
  font TEXT NOT NULL,
  color TEXT NOT NULL,
  revealable BOOLEAN DEFAULT false, -- opt-in: autor permite ser identificado
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE (user_id, question_id)
);

CREATE TABLE invites (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  inviter_id UUID REFERENCES users(id),
  invitee_email TEXT,
  status TEXT DEFAULT 'pending' CHECK (status IN ('pending','accepted','expired')),
  reward_coins INT DEFAULT 15,
  reward_claimed BOOLEAN DEFAULT false, -- true quando o convidado responde a 1ª pergunta
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE coin_transactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  amount INT NOT NULL,            -- positivo = compra, negativo = gasto
  reason TEXT NOT NULL CHECK (reason IN ('purchase','reveal_identity','group_unlock','invite_reward')),
  payment_method TEXT CHECK (payment_method IN ('pix','boleto','credit_card','debit_card')),
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE identity_reveals (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  viewer_id UUID REFERENCES users(id),
  answer_id UUID REFERENCES answers(id),
  coins_spent INT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE (viewer_id, answer_id)
);
```

---

## 7. Modelo de Monetização

| Camada | Mecanismo | Observação |
|---|---|---|
| **App geral** | Gratuito para responder/convidar | Adoção e viralização |
| **Moedas (global)** | Compra via Pix/Boleto/Cartão → gasta para revelar identidade de respostas marcadas como `revelable` | Moeda única para o app inteiro por padrão |
| **Recompensa de convite** | +15 moedas (sugestão inicial) ao convidador quando o convidado responde à 1ª pergunta | Custo de aquisição pago em moeda própria, não em dinheiro — sustentável enquanto a conversão de compra de moedas cobrir o "subsídio" de recompensas |
| **Grupo fechado pago** | Pagamento único ou recorrente por criar um caderno privado com limite de N usuários | O grupo licenciado escolhe entre usar a moeda global do app ou uma economia de moedas isolada só para o grupo |
| **Pergunta patrocinada (futuro)** | Marcas podem sugerir uma pergunta destacada na fila | Receita de mídia, só depois de escala |

**Sugestão de precificação inicial** (a validar): pacotes de moedas (ex: 50/150/500 moedas), grupo fechado com mensalidade baixa fixa + valor extra por slot de usuário acima do limite base.

---

## 8. Roadmap

**MVP (V1)**
- Cadastro, 10 perguntas fixas, resposta com fonte/cor, regra de não-repetição
- Convite simples (1 link por vez), sugestão de pergunta com fila de moderação manual
- Sem moedas ainda — feature de revelação de identidade fica para V2 (validar primeiro se as pessoas realmente querem responder e voltar)

**V2**
- Sistema de moedas + revelação de identidade com opt-in
- Pré-filtro automático de moderação
- Múltiplos convites simultâneos

**V3**
- Grupos fechados pagos
- Métricas de coeficiente viral por usuário
- Possível pergunta patrocinada

---

## 9. Métricas de Sucesso

- **Taxa de conclusão**: % de usuários que respondem todas as perguntas disponíveis
- **Coeficiente viral**: nº médio de convites aceitos por usuário que completou o caderno
- **Retenção D7/D30**: usuários que voltam quando uma nova pergunta é aprovada
- **Conversão de moedas**: % de usuários ativos que compram moedas pelo menos uma vez
- **Taxa de aprovação de perguntas sugeridas**: sinaliza qualidade da fila de moderação

---

## 10. Decisões Confirmadas

1. **Faixa etária:** não há classificação fixa do app — cada pergunta recebe seu próprio rótulo (L/10/12/14/16/18) na moderação, e a classificação da loja acompanha a pergunta mais "pesada" ativa no feed geral.
2. **Moedas:** únicas para o app inteiro por padrão. Grupos fechados licenciados podem optar por uma economia de moedas isolada.
3. **Convites:** recompensados em moedas (15 moedas sugeridas por convite efetivo, com gatilho na 1ª resposta do convidado para evitar fraude).
4. **Pergunta sugerida:** ao ser aprovada, vira global — todos os usuários do feed geral passam a vê-la, independente de quem sugeriu.

### Parâmetros a calibrar (não bloqueiam o MVP, mas precisam de um número antes do lançamento)
- Valor exato da recompensa por convite (15 moedas é um chute inicial — depende do preço final do pacote de moedas)
- Quem define o `content_rating` de cada pergunta: moderador humano manual, ou um pré-classificador automático por palavras-chave que o moderador só confirma?
- Se uma pergunta global aprovada por um grupo licenciado com `coin_economy = isolated` ainda assim aparece pra todo mundo (provavelmente sim, já que "pergunta vira global" é regra do produto, não do grupo) — vale confirmar com você
