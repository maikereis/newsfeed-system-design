# Estimativas de Capacidade

Este documento calcula os recursos necessários para suportar o sistema de NewsFeed baseado nos requisitos definidos.

---

## Premissas Base

### Usuários

- DAU (_Daily Active Users_): 500 milhões
- MAU (_Montly Active Users_): 2 bilhões
- Razão DAU/MAU: 25%

### Comportamento do Usuário

- Posts criados por usuário: 0.16 posts/dia
- Posts visualizados por usuário: 200 posts/dia
- Usuários seguidos (média): 250 pessoas
- Curtidas por post: 50 curtidas
- Comentários por post: 10 comentários

### Distribuição de Conteúdo

- Texto apenas: 2%
- Vídeo apenas: 3%
- Imagem apenas: 10%

- Texto + Imagem: 30%
- Texto + Vídeo: 18%
- Imagem + Vídeo: 23%
- Texto + Imagem + Vídeo: 14%

- Posts com pelo menos uma imagem: 10% + 30% + 23% + 14% = 77%  
- Posts com pelo menos um vídeo: 3% + 18% + 23% + 14% = 58%

---

## Tráfego e Throughput

### Criação de Posts

**Posts por dia:**
```
500M DAU × 0.16 posts/usuário = 80M posts/dia
```

**Posts por segundo (média):**
```
80M posts/dia ÷ 86.400s ≈ 925,93 posts/segundo (926 posts/s)
```

**Posts por segundo (pico - 3x média):**
```
926 × 3 ≈ 2.778 posts/s
```

### Leituras de Feed

**Visualizações de feed por dia:**
```
500M DAU × 200 posts visualizados = 100B visualizações/dia
```

**Leituras por segundo (média):**
```
100B ÷ 86.400s ≈ 1.157.407,41 leituras/segundo (1.16M reads/s)
```

**Leituras por segundo (pico - 3x média):**
```
1.157.407 × 3 ≈ 3.472.000 leituras/s (3.47M reads/s)
```

**Razão Leitura:Escrita:**
```
1.157.407,41 ÷ 925,93 ≈ 1.250:1
```

### Interações

**Curtidas por dia:**
```
80M posts × 50 curtidas = 4B curtidas/dia
```

**Curtidas por segundo (média):**
```
4B ÷ 86.400s ≈ 46.296,29 curtidas/segundo (47.000 curtidas/s)
```

**Curtidas por segundo (pico - 3x média):**
```
47.000 × 3 ≈ 141.000 curtidas/s
```

**Comentários por dia:**
```
80M posts × 10 comentários = 800M comentários/dia
```

**Comentários por segundo (média):**
```
800M ÷ 86.400s ≈ 9.259,25 comentários/segundo (9.260 comentários/s)
```

**Comentários por segundo (pico - 3x média):**
```
9.260 × 3 ≈ 27.780 comentários/s
```

---

## Estimativas de Armazenamento

### Dados de Posts (Texto)

**Tamanho Médio de texto por post:**
```
Assumindo ~500 caracteres em média = 500 bytes (utf-8)
```

**Armazenamento Diário para Texto:**
```
80M posts × 500 bytes = 40 × 10^9 bytes = 40GB/dia
```

**Armazenamento de texto por ano:**
```
40GB/dia × 365 dias = 14.600GB/ano ≈ 14,6TB/ano
```

### Dados de Mídia (Imagens)

**Tamanho Médio de Imagem:**
```
Assumindo ~2MB por imagem
```

**Média de Imagens por post:**
```
Assumindo 4 imagens/post
```

**Posts com imagens por dia:**
```
80M posts × 77% = 61,6M posts com imagens/dia
```

**Armazenamento de imagens por dia:**
```
61,6M posts × 4 imagens/post × 2MB/imagem = 
61,6 × 10^6 × 4 × 2 × 10^6 bytes = 
492,8 × 10^12 bytes = 492,8TB/dia
```

**Armazenamento de imagens por ano:**
```
492,8TB/dia × 365 dias = 179.872TB/ano ≈ 179,9PB/ano
```

### Dados de Mídia (Vídeos)

**Tamanho Médio de Vídeo (após compressão, ~60s):**
```
Assumindo ~20MB por vídeo
```

**Média de Vídeos por post:**
```
Assumindo 1 vídeo/post
```

**Posts com vídeos por dia:**
```
80M posts × 58% = 46,4M posts com vídeos/dia
```

**Armazenamento de vídeos por dia:**
```
46,4M posts × 1 vídeo/post × 20MB/vídeo = 
46,4 × 10^6 × 20 × 10^6 bytes = 
928 × 10^12 bytes = 928TB/dia
```

**Armazenamento de vídeos por ano:**
```
928TB/dia × 365 dias = 338.720TB/ano ≈ 338,7PB/ano
```

### Metadados

**Metadados por post:**
```
Post ID: 8 bytes
User ID: 8 bytes
Timestamp: 8 bytes
Text length: 2 bytes
Media refs: ~100 bytes (IDs de mídias)
Stats (likes, comments): 16 bytes
Outros: ~100 bytes
---
Total: ~242 bytes por post
```

**Metadados por dia:**
```
80M posts × 242 bytes = 80 × 10^6 × 242 bytes = 19.360 × 10^6 bytes ≈ 19,36GB/dia
```

**Metadados por ano:**
```
19,36GB/dia × 365 dias = 7.066,4GB/ano ≈ 7,07TB/ano
```

### Dados de usuários

**Dados por usuário:**
```
User ID: 8 bytes
Username: 50 bytes
Email: 100 bytes
Password hash: 64 bytes
Profile info: ~500 bytes
Bio: ~500 bytes
Profile picture ref: 50 bytes
Stats: 32 bytes
Timestamps: 16 bytes
---
Total: ~1.320 bytes por usuário
```

**Usuários totais:** 2B MAU

**Armazenamento total de usuários:**
```
2B usuários × 1.320 bytes = 2 × 10^9 × 1.320 bytes = 2.640 × 10^9 bytes ≈ 2,64TB
```

### Relacionamentos (Follow/Followers)

**Relacionamentos totais:**
```
2B usuários × 250 follows médios = 500B relacionamentos
```

**Tamanho Médio de Dados por relacionamento:**
```
FollowerID: 8 bytes
FollowedID: 8 bytes
Timestamp: 8 bytes
---
Total: 24 bytes
```

**Armazenamento de relacionamentos:**
```
500B relacionamentos × 24 bytes = 500 × 10^9 × 24 bytes = 12 × 10^12 bytes ≈ 12TB
```

### Comentários

**Tamanho Médio de Dados por comentário:**
```
CommentID: 8 bytes
PostID: 8 bytes
UserID: 8 bytes
Text: ~200 bytes (média)
Timestamp: 8 bytes
---
Total: ~232 bytes
```

**Comentários por dia:**
```
800M comentários/dia
```

**Armazenamento de comentários por dia:**
```
800M comentários × 232 bytes = 800 × 10^6 × 232 bytes = 185.600 × 10^6 bytes ≈ 185,6GB/dia
```

**Armazenamento de comentários por ano:**
```
185,6GB/dia × 365 dias = 67.744GB/ano ≈ 67,74TB/ano
```

### Curtidas

**Curtidas por dia:**
```
4B curtidas/dia
```

**Dados por curtida:**
```
Post ID: 8 bytes
User ID: 8 bytes
Timestamp: 8 bytes
---
Total: 24 bytes
```

**Armazenamento de curtidas por dia:**
```
4B curtidas × 24 bytes = 4 × 10^9 × 24 bytes = 96 × 10^9 bytes ≈ 96GB/dia
```

**Armazenamento de curtidas por ano:**
```
96GB/dia × 365 dias = 35.040GB/ano ≈ 35TB/ano
```

---

## Resumo de Armazenamento (Por Ano)

| Tipo de Dado | Armazenamento/Ano |
|--------------|-------------------|
| Texto de Posts | ~14,6TB |
| Imagens | ~179,9PB |
| Vídeos | ~338,7PB |
| Metadados de Posts | ~7,07TB |
| Usuários | ~2,64TB |
| Relacionamentos | ~12TB |
| Comentários | ~67,74TB |
| Curtidas | ~35TB |
| **TOTAL** | **~518,7PB/ano** |

---

## Largura de Banda

### Upload (Escrita)

**Largura de banda de upload de Texto:**
```
500 bytes/post × 80M posts/dia = 
40 × 10^9 bytes/dia = 
40 × 10^9 bytes × 8 bits/byte = 
320 × 10^9 bits/dia = 
320 × 10^9 bits/dia ÷ 86.400s/dia = 
3.703.703,70 bits/s = 
3.703.703,70 bits/s ÷ 10^6 bits/Mbit ≈ 3,70Mbits/s
```

**Largura de banda de upload de Imagens:**
```
61,6M posts × 4 imagens/post × 2MB/imagem = 
492,8TB/dia = 
492,8 × 10^12 bytes/dia = 
492,8 × 10^12 bytes × 8 bits/byte = 
3.942,4 × 10^12 bits/dia = 
3.942,4 × 10^12 bits/dia ÷ 86.400s/dia = 
45.638.888.888,89 bits/s = 
45.638.888.888,89 bits/s ÷ 10^9 bits/Gbit ≈ 45,64Gbits/s
```

**Largura de banda de upload de Vídeos:**
```
46,4M posts × 1 vídeo/post × 20MB/vídeo = 
928TB/dia = 
928 × 10^12 bytes/dia = 
928 × 10^12 bytes × 8 bits/byte = 
7.424 × 10^12 bits/dia = 
7.424 × 10^12 bits/dia ÷ 86.400s/dia = 
85.925.925,93 bits/s = 
85.925.925,93 bits/s ÷ 10^9 bits/Gbit ≈ 85,93Gbits/s
```

**Total Upload (Média):**
```
3,70Mbits/s + 45,64Gbits/s + 85,93Gbits/s ≈ 131,57Gbits/s
```

**Upload Pico (3x Média):**
```
131,57Gbits/s × 3 ≈ 394,71Gbits/s
```

### Download (Leitura)

Assumindo que cada visualização de post carrega:
- Metadados: ~1KB
- Thumbnail de imagem: ~100KB (se houver, 77% dos posts)
- Sem vídeo inicialmente (lazy loading)

**Dados por visualização:**
```
1KB + (77% × 100KB) = 1KB + 77KB = 78KB por visualização
```

**Download por segundo (Média):**
```
1.157.407,41 leituras/s × 78KB/leitura = 
1.157.407,41 × 78 × 10^3 bytes/s = 
90.277.778.000 bytes/s = 
90.277.778.000 bytes/s ÷ 10^9 bytes/GB = 
~90,28GB/s = 
90,28GB/s × 8 bits/byte ≈ 722,24Gbits/s
```

**Download Pico (3x Média):**
```
90,28GB/s × 3 ≈ 270,84GB/s ≈ 2.166,72Gbits/s
```

---

## Memória (Cache)

### Cache de Feed

Cachear os últimos 100 posts para os usuários mais ativos (top 20% DAU):

**Usuários ativos no cache:**
```
500M DAU × 20% = 100M usuários
```

**Dados por usuário em cache:**
```
100 posts × (500 bytes texto + 1KB metadados) = 
100 × 1.500 bytes = 
150.000 bytes = 
~150KB por usuário
```

**Memória total para cache de feed:**
```
100M usuários × 150KB/usuário = 
100 × 10^6 × 150 × 10^3 bytes = 
15 × 10^12 bytes = 
~15TB
```

### Cache de Hot Data

**Posts recentes (últimas 24h):**
```
Metadados: ~19,36GB
```

**Thumbnails de imagens:**
```
Assumindo thumbnails de ~50KB para posts das últimas 24h:
61,6M posts × 50KB ≈ 3,08TB (mas apenas thumbnails mais acessados) ≈ 50GB
```

**Previews de vídeos:**
```
Assumindo previews de ~2MB para vídeos das últimas 24h:
46,4M posts × 2MB ≈ 92,8TB (mas apenas previews mais acessados) ≈ 100GB
```

**Total Hot Data Cache:**
```
19,36GB + 50GB + 100GB ≈ 169,36GB ≈ 200GB (com overhead)
```

### Cache de Contadores (Curtidas/Comentários)

**Posts das últimas 24h:**
```
80M posts × (50 curtidas + 10 comentários) × 8 bytes/contador = 
80 × 10^6 × 60 × 8 bytes = 
38.400 × 10^6 bytes = 
38,4GB
```

---

## Servidores de Aplicação

### Requisições por Segundo

**Total RPS (leitura + escrita + interações) - Média:**
```
1.157.407,41 reads/s + 925,93 writes/s + 47.000 likes/s + 9.260 comments/s ≈ 1.214.593 RPS
```

**RPS Pico:**
```
1.214.593 RPS × 3 ≈ 3.643.779 RPS
```

### Servidores Necessários

Assumindo cada servidor suportando 10.000 RPS:

**Servidores para tráfego médio:**
```
1.214.593 RPS ÷ 10.000 RPS/servidor ≈ 122 servidores
```

**Servidores para pico (com redundância 20%):**
```
3.643.779 RPS ÷ 10.000 RPS/servidor = 364 servidores
364 servidores × 1,20 (redundância) ≈ 437 servidores
```

---

## Resumo de Recursos

| Recurso | Média | Pico (3x) |
|---------|-------|-----------|
| **Armazenamento Anual** | ~518,7PB/ano | - |
| **Largura de Banda Upload** | ~131,6Gbits/s | ~394,7Gbits/s |
| **Largura de Banda Download** | ~722,2Gbits/s | ~2.166,7Gbits/s |
| **Memória para Cache (Feed)** | ~15TB | - |
| **Memória para Cache (Hot Data)** | ~200GB | - |
| **Memória para Cache (Contadores)** | ~38,4GB | - |
| **Servidores de Aplicação** | ~122 servidores | ~437 servidores |
| **RPS - Posts** | ~926/s | ~2.778/s |
| **RPS - Leituras** | ~1,16M/s | ~3,47M/s |
| **RPS - Curtidas** | ~47K/s | ~141K/s |
| **RPS - Comentários** | ~9,26K/s | ~27,78K/s |
| **RPS Total** | ~1,21M/s | ~3,64M/s |

---

## Considerações Adicionais

1. **CDN:** Essencial para distribuição de mídia (imagens/vídeos) - pode reduzir bandwidth em ~80%
2. **Compressão:** Vídeos e imagens devem ser comprimidos e em múltiplas resoluções
3. **Sharding:** Database deve ser particionado por User ID para escalar horizontalmente
4. **Replicação:** 3 réplicas para durabilidade e alta disponibilidade
5. **Cold Storage:** Mover posts com >6 meses para armazenamento mais barato