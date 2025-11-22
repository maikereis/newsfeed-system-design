# Requisitos

Este documento define of requisitos funcionais e não-funcionais do sistema de NewsFeed.

---

## Requisitos Funcionais

### Recursos Principais

#### 1. Gerenciamento de Usuário

- Usuários podem criar e gerencias contas
- Usuários podem seguir/deixar de seguir outros usuários
- Usuários podem visualizer perfis (próprio e de outros usuários)

#### 2. Gerenciamento de Post

- Usuários podem criar posts com texto até 2200 caracteres;
- Usuários podem anexar até 20 mídias aos posts:
    - Imagens (com tamanho até 10mb cada)
    - Vídeos (com 60 segundos de duração e somando 4gb de memória total).
- Usuários podem editar seus próprios posts
- Usuários podem deletar seus próprios posts
- Usuários podem comentar e curtir posts de outros usuários

#### 3. Feed

- Usuários podem visualizar posts de quem seguem em ordem cronológica reversa (ao contrário da data de postagem).
- Feed carrega incrementalmente (paginação)
- Feed atualiza com novos posts.

#### 4. Interações

- Usuários podem curtir posts
- Usuários podem comentar em posts
- Usuários podem compartilhar posts no próprio feed
- Usuários podem visualizar contagem de curtidas, comentários e compartilhamentos

#### 5. Notificações

- Notificações quando outro usuário:
    - Curte seu post
    - Comenta no seu post
    - Começa a seguir você
- Notificações em tempo real

#### #. Fora do Escopo

- Mensagens diretas entre usuários
- Stories temporários
- Grupos ou páginas
- Publicidade ou posts patrocinados
- Recursos avançados de moderação de conteúdo
- Recomendações algorítmicas de conteúdo

## Requisitos não funcionais

#### 1. Disponibilidade

- 99.99% de uptime (aproximadamente 52 minutos de downtime por ano)
- Sistema deve ser resiliente a falhas de componentes individuais

#### 2. Performance

- Latência para carregar os 20 primeiros posts < 2s
- Latência de resposta para curtir/comentar < 200ms
- Latência de upload imagens < 5s
- Latência de upload de vídeos < 15s
- Latência de notificações < 1s

#### 3. Scalabilidade

- Suportar 500 milhões de usuários ativos diários (DAU)
- Suportar 2 bilhões de usuários ativos mensais (MAU)
- Suportar 80 milhões de posts criados por dia.
- Suportar 100 bilhões de visualizacões de feed por dia.

#### 4. Consistência
    
- Consistência eventual para feed e contadores:
    - Curtidas e comentários podem demorar até alguns segundos para refletir no feed
- Consistencia forte para:
    - Criação, edição e deleção de posts
    - Operações de seguir/deixar de seguir
    - Notificações devem ser entregues em ordem correta
    - Autenticação e autorização

#### 5. Durabilidade

- Posts e mídias nunca devem ser perdidos após confirmação
- Backups regulares dos dados do usuário e posts com RPO (Recovery Point Objective) < 1h

#### 6. Segurança

- Autenticação e autorização obrigatórias
- Proteção contra ataques comuns (XSS, CSRF, SQL Injection)
- Criptografia de dados sensíveis em trânsito e em repouso
- Conformidade com regulamentos de privacidade (GDPR, LGPD)
- Rate limiting para prevenir abuso

#### 7. Usabilidade

- Interface responsiva (mobile + desktop)
- Acessibilidade para usuários com deficiências
- Feedback claro para ações do usuário (curtir, comentar, seguir)
- Suporte a múltiplos idiomas

#### Assunções

- Um usuário segue em média ~250 pessoas
- Um usuário cria em média ~0.2 posts por dia
- Um usuário visualiza em média ~200 posts por dia
- Um post recebe em média 50 curtidas e 10 comentários

- 2% dos posts contém apenas texto
- 3% dos posts contém apenas vídeos
- 10% dos posts contém apenas imagens

- 30% dos posts contém texto e imagens
- 18% dos posts contém texto e videos
- 23% dos posts contém imagens e videos
- 14% → texto + imagens + vídeos

#### Restrições

- Limite de 2200 caracteres por post
- Limite de 20 mídias por post
- Tamanho máximo de 10mb por imagem
- Duração máxima de 60 segundos por vídeo
- Tamanho máximo de mídias de vídeo de 4gb
- Limite de taxa para criação de posts: 100 posts por dia por usuário
- Limite de taxa para comentários: 14 comentários por hora por usuário
- Limite de taxa para curtidas: 20 curtidas por hora por usuário
- Limite de taxa para seguir: 20 ações por hora por usuário
- Limite de taxa para deixar de seguir: 60 ações por hora por usuário
- Limite de taxa para upload de mídia: 20 uploads por hora por usuário
