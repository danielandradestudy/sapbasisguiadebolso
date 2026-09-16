# 🔐 SAP BASIS — Autorizações | Guia de Bolso

> Material introdutório para estudar o básico de **SAP Authorization**.

---

## 🧠 1. A ideia principal

Pense assim:

```text
USUÁRIO
   ↓
ROLE
   ↓
AUTORIZAÇÕES
   ↓
ACESSO
```

Exemplo:

```text
Usuário
   ↓
Z_ADM_REPORT_WORKFLOW
   ↓
S_TCODE
   ↓
SWIA
   ↓
Acesso à transação
```

---

## 🔎 2. SU53 — "O que está faltando?"

Use a `SU53` quando o usuário receber um erro de autorização.

```text
Usuário tenta acessar
        ↓
       ❌
        ↓
      SU53
        ↓
Verificar qual autorização falhou
```

### 🧠 Decore

> **SU53 = descobrir o problema**

⚠️ Não significa que devemos simplesmente adicionar tudo que aparecer.

---

## 🛠️ 3. PFCG — "Onde vejo a Role?"

A `PFCG` é usada para trabalhar com **Roles** e suas autorizações.

```text
PFCG
 ↓
ROLE
 ↓
OBJETOS DE AUTORIZAÇÃO
```

### 🧠 Decore

> **PFCG = administrar Roles**

---

## 📦 4. Função Individual

É uma Role que contém as autorizações.

Exemplo:

```text
Z_ADM_REPORT_WORKFLOW
        │
        ├── S_TCODE
        ├── S_RFC
        ├── S_DATASET
        └── S_PROGRAM
```

### 🧠 Decore

> **Individual = Role com autorizações**

---

## 📚 5. Função Composta

Agrupa várias funções individuais.

```text
Z_BASIS_WORKFLOW
       │
       ├── Z_SWIA
       ├── Z_WORKFLOW
       └── Z_ADMIN
```

### 🧠 Decore

> **Composta = conjunto de Roles**

---

# 🔑 6. Objetos que apareceram na tarefa

## S_TCODE

Controla o código da transação.

```text
S_TCODE
   ↓
TCD = SWIA
```

> **S_TCODE → transação**

---

## S_RFC

Relacionado a autorizações de RFC.

Campos que você viu:

```text
ACTVT
RFC_NAME
RFC_TYPE
```

Exemplo:

```text
ACTVT    = 16
RFC_TYPE = FUNC
```

> **S_RFC → RFC**

---

## S_DATASET

Relacionado ao acesso a arquivos.

Campos:

```text
ACTVT
FILENAME
PROGRAM
```

Exemplo:

```text
FILENAME = *
```

⚠️ `*` é um coringa e pode representar uma abrangência grande.

> **S_DATASET → arquivos**

---

## S_PROGRAM

Relacionado a verificações para execução de programas ABAP.

Campos vistos:

```text
P_ACTION = SUBMIT
P_GROUP  = WF_ADMIN
```

> **S_PROGRAM → programas ABAP**

---

# 🚚 7. Fluxo da alteração

O fluxo que você aprendeu hoje pode ser memorizado assim:

```text
DEV
 ↓
QAS
 ↓
PRD
```

De forma mais completa:

```text
Analista
   ↓
Cria/Ajusta Role
   ↓
Request
   ↓
QAS
   ↓
Teste
   ↓
PRD
   ↓
Role → Usuário
```

### ⚠️ Importante no seu processo

> **Basis não cria a Request.**

A Request é criada pelo analista responsável, conforme o processo da equipe.

O Basis atua no transporte conforme o fluxo definido.

---

# 👤 8. Role + Usuário

A Role estar em PRD não significa que todo mundo tenha acesso.

É necessário associar a Role ao usuário:

```text
ROLE
 ↓
USUÁRIO
 ↓
ACESSO
```

Exemplo:

```text
Z_ADM_REPORT_WORKFLOW
          ↓
       USUARIO_X
          ↓
        SWIA
```

---

# 🧪 9. Exemplo: usuário não consegue acessar SWIA

### Passo 1

Usuário tenta acessar:

```text
SWIA
```

### Passo 2

Recebe erro.

### Passo 3

Consultar:

```text
SU53
```

### Passo 4

Identificar a autorização que falhou.

### Passo 5

Verificar a Role no:

```text
PFCG
```

### Passo 6

Após a alteração seguir o fluxo:

```text
DEV → QAS → PRD
```

### Passo 7

Associar a Role ao usuário em PRD.

### Passo 8

Usuário testa novamente.

Se houver outro erro:

```text
SU53
```

---

# ⚡ 10. Cola rápida

| T-Code / Objeto | Lembrete |
|---|---|
| `SU53` | 🔎 Descobrir falha |
| `PFCG` | 🛠️ Administrar Role |
| `SU01` | 👤 Usuário |
| `STMS` | 🚚 Transporte |
| `S_TCODE` | 🔑 Transação |
| `S_RFC` | 🌐 RFC |
| `S_DATASET` | 📁 Arquivos |
| `S_PROGRAM` | 💻 Programas ABAP |

---

# 🧠 11. O que decorar primeiro

Não tente decorar tudo.

Comece por estas 6 ideias:

```text
SU53  → descobrir a falha

PFCG  → Role

S_TCODE → transação

Individual → Role com autorizações

Composta → agrupa Roles

DEV → QAS → PRD
```

Depois disso, vá aprendendo os objetos:

```text
S_RFC
S_DATASET
S_PROGRAM
...
```

---

# 📌 Mapa mental

```text
                SAP AUTHORIZATION
                       │
          ┌────────────┴────────────┐
          │                         │
        SU53                       PFCG
          │                         │
   Descobrir erro                 ROLE
                                    │
                         ┌──────────┴──────────┐
                         │                     │
                    Individual             Composta
                         │                     │
                    Autorizações         Várias Roles
                         │
              ┌──────────┼──────────┐
              │          │          │
           S_TCODE      S_RFC    S_PROGRAM
              │
            SWIA

               + S_DATASET
```

---

## 🎯 Regra de ouro

> **Usuário → Role → Objeto → Campo → Valor → Acesso**

Não tente decorar tudo de uma vez.

Primeiro entenda **a lógica**. Depois os objetos de autorização começam a fazer sentido.
