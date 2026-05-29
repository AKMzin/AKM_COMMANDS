<img width="1024" height="434" alt="image" src="https://github.com/user-attachments/assets/371b2ba4-7a3d-49da-bd3f-1cc8c6f9f283" />

# 🖥️ AKM Commands — Launcher de Diagnóstico de Rede

> **Projeto pessoal de estudo para NOC/Redes**
> Ferramenta CLI em Batch (.bat) para execução rápida de comandos de diagnóstico de rede no Windows.

---

## 📌 Visão Geral

O **AKM Commands** é um launcher interativo desenvolvido em **Batch Script (.bat)** para o Windows.
Ele centraliza os principais comandos de diagnóstico de rede em um menu simples e direto,
ideal para uso em ambientes de **NOC (Network Operations Center)** ou para estudos práticos de infraestrutura.

Em vez de abrir o terminal e digitar cada comando manualmente, o operador escolhe uma opção no menu e o script já executa — com suporte a parâmetros extras.

**Comandos disponíveis no AKM:**

| Opção | Comando      | Finalidade                            |
|-------|--------------|---------------------------------------|
| 1     | `ping`       | Testa conectividade com um host       |
| 2     | `netstat`    | Exibe conexões de rede ativas         |
| 3     | `nslookup`   | Resolve nomes de domínio (DNS)        |
| 4     | `systeminfo` | Exibe informações do sistema          |

---

## 🧠 Conceito

### O que é um Batch Script?

Um arquivo `.bat` é um **script de automação nativo do Windows**.
Ele executa comandos do Prompt de Comando (CMD) em sequência, como se você os digitasse manualmente — mas com menus, lógica e fluxo de controle.

> **Analogia:** Imagine um controle remoto de TV. Em vez de ir até a TV e pressionar os botões físicos um a um, você usa o controle para acionar tudo de um lugar só. O AKM funciona assim: centraliza comandos de rede em um único ponto de acesso.

### Por que isso é útil no NOC?

No NOC, o analista precisa agir **rápido** diante de alertas e incidentes.
Ter um launcher pronto com os principais comandos de diagnóstico reduz o tempo de resposta e minimiza erros de digitação.

---

## ⚙️ Como Funciona (técnico, simplificado)

### Estrutura do Script

```
AKM_final.bat
│
├── :MENU         → Exibe o menu principal e aguarda input do usuário
├── :PING         → Executa ping com alvo e parâmetros customizáveis
├── :NETSTAT      → Executa netstat com flags opcionais
├── :NSLOOKUP     → Resolve um domínio ou IP via DNS
├── :SYSINFO      → Executa systeminfo (inventário do sistema)
└── :SAIR         → Animação de saída e encerramento do processo
```

### Fluxo de Execução

```
Usuário abre o .bat
       │
       ▼
   Menu Principal
       │
   Digita opção (1-4)
       │
       ▼
  Subrotina correspondente
  (pede alvo/parâmetros)
       │
       ▼
  Executa o comando no CMD
       │
       ▼
  Exibe resultado → pause
       │
       ▼
  Retorna ao Menu Principal
```

### Principais recursos do script

```batch
setlocal enabledelayedexpansion   :: Permite variáveis dinâmicas dentro de loops
set /p "opcao=>> "                :: Lê input do usuário
if "%opcao%"=="1" goto :PING      :: Redireciona para a subrotina correta
chcp 65001                        :: Define encoding UTF-8 (suporte a acentos)
```

---

## 🛠️ Exemplos Práticos

### 1. PING — Testando conectividade

Usado para verificar se um host está acessível na rede.

```bash
# Ping padrão (4 pacotes)
ping 8.8.8.8

# Ping contínuo (útil para monitorar instabilidade)
ping -t 8.8.8.8

# Ping com número definido de pacotes
ping -n 10 google.com
```

**Saída esperada (host ativo):**
```
Resposta de 8.8.8.8: bytes=32 tempo=12ms TTL=118
Resposta de 8.8.8.8: bytes=32 tempo=11ms TTL=118
```

**Saída de falha (host inacessível):**
```
Tempo limite da solicitação esgotado.
Tempo limite da solicitação esgotado.
```

> 💡 No Launcher, você digita o alvo (ex: `8.8.8.8`) e os parâmetros extras (ex: `-t`) diretamente no menu.

---

### 2. NETSTAT — Conexões de Rede Ativas

Exibe todas as conexões TCP/UDP abertas na máquina — essencial para detectar comportamentos suspeitos.

```bash
# Todas as conexões com IPs numéricos e PID do processo
netstat -ano

# Filtrar por porta específica (ex: porta 443)
netstat -ano | findstr :443

# Conexões estabelecidas apenas
netstat -ano | findstr ESTABLISHED
```

**Saída típica:**
```
Proto  Endereço Local       Endereço Externo     Estado          PID
TCP    192.168.1.10:52340   142.250.79.46:443    ESTABLISHED     4512
TCP    0.0.0.0:445          0.0.0.0:0            LISTENING       4
```

> 💡 O PID permite identificar qual processo abriu a conexão — útil para detectar malware ou aplicações não autorizadas.

---

### 3. NSLOOKUP — Resolução de DNS

Verifica se um domínio está sendo resolvido corretamente para o IP esperado.

```bash
# Resolução simples
nslookup google.com

# Consulta DNS reversa (IP → nome)
nslookup 8.8.8.8

# Usar servidor DNS específico
nslookup google.com 1.1.1.1
```

**Saída esperada:**
```
Servidor:  dns.empresa.local
Address:  10.0.0.1

Nome:    google.com
Addresses:  142.250.79.46
```

> 💡 Se o nslookup falha mas o IP funciona, o problema está no DNS — não na conectividade.

---

### 4. SYSTEMINFO — Inventário do Sistema

Exibe informações completas do sistema operacional e da rede local.

```bash
systeminfo
```

**Informações retornadas:**
```
Nome do host:           WORKSTATION-01
Sistema Operacional:    Microsoft Windows 10 Pro
Versão do SO:           10.0.19045
Memória física total:   16.384 MB
Adaptadores de rede:    Ethernet - 192.168.1.50
```

> 💡 Útil para inventário de ativos, abertura de chamados e diagnóstico de problemas de compatibilidade.

---

## 🚨 Cenário Real — Como aparece no NOC

### Cenário 1: Usuário relata lentidão no acesso à internet

**Passo 1 — Verificar conectividade básica:**
```bash
ping 8.8.8.8          # Testa saída para internet
ping 192.168.1.1      # Testa gateway local
```

**Passo 2 — Verificar DNS:**
```bash
nslookup google.com   # DNS está resolvendo?
```

**Passo 3 — Verificar conexões suspeitas:**
```bash
netstat -ano          # Há conexões consumindo banda?
```

> ✅ Com o AKM, o analista executa os três testes em sequência sem precisar sair do menu.

---

### Cenário 2: Suspeita de malware / conexão não autorizada

```bash
# 1. Listar todas conexões com PID
netstat -ano

# 2. Verificar processo suspeito (ex: PID 9988)
tasklist | findstr 9988

# 3. Verificar para onde a conexão aponta
nslookup <IP-suspeito>
```

**Sinal de alerta:** processo desconhecido com conexão `ESTABLISHED` para IP externo em porta incomum (ex: 4444, 6666, 31337).

---

## 📚 Resumo

| Componente         | Descrição                                              |
|--------------------|--------------------------------------------------------|
| **Tipo**           | Batch Script (.bat) — Windows nativo                   |
| **Objetivo**       | Launcher de comandos de diagnóstico de rede            |
| **Público-alvo**   | Analistas NOC, Helpdesk, estudantes de redes           |
| **Comandos**       | ping, netstat, nslookup, systeminfo                    |
| **Diferenciais**   | Menu interativo, parâmetros customizáveis, animação de saída |
| **Nível**          | Iniciante / Intermediário                              |

---
[Uploading AKM_Launcher.bat…]()

> 📁 **Projeto:** AKM Commands
> 👤 **Autor:** Richard
> 🛠️ **Stack:** Batch Script (Windows CMD)
> 📅 **Status:** Em desenvolvimento / Estudo ativo
