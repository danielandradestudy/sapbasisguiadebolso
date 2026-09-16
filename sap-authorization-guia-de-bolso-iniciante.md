# 📘 SAP BASIS --- Guia de Bolso: Autorizações

> Guia rápido de estudo sobre SAP Authorization, baseado na tarefa
> prática de criação/manutenção de uma Role para acesso à transação
> `SWIA`.

------------------------------------------------------------------------

## 📌 Sumário

-   [1. Conceito básico](#1--conceito-básico)
-   [2. Transações principais](#2--transações-principais)
-   [3. SU53](#3--su53--análise-de-autorização)
-   [4. PFCG](#4--pfcg--roles)
-   [5. Função individual](#5--função-individual)
-   [6. Função composta](#6--função-composta)
-   [7. Objetos de autorização](#7--objetos-de-autorização)
-   [8. S_TCODE](#8--s_tcode--código-da-transação)
-   [9. S_RFC](#9--s_rfc--autorizações-rfc)
-   [10. S_DATASET](#10--s_dataset--acesso-a-arquivos)
-   [11. S_PROGRAM](#11--s_program--execução-de-programas-abap)
-   [12. Fluxo DEV → QAS → PRD](#12--fluxo-dev--qas--prd)
-   [13. Associação da Role ao
    usuário](#13--associação-da-role-ao-usuário)
-   [14. Exemplo prático: SWIA](#14--exemplo-prático-swia)
-   [15. Checklist de atendimento](#15--checklist-de-atendimento)
-   [16. Cola rápida](#16--cola-rápida)
-   [17. O que estudar depois](#17--o-que-estudar-depois)

------------------------------------------------------------------------

# 1. 🔐 Conceito básico

No SAP, o acesso do usuário é controlado por **Roles (Funções)**, que
contêm **Objetos de Autorização** com seus respectivos campos e valores.

Fluxo simplificado:

``` text
USUÁRIO
   ↓
ROLE / FUNÇÃO
   ↓
OBJETOS DE AUTORIZAÇÃO
   ↓
CAMPOS + VALORES
   ↓
VERIFICAÇÕES DO SAP
   ↓
ACESSO
```

### Exemplo

Um usuário precisa utilizar a transação `SWIA`:

``` text
Usuário
   ↓
Z_ADM_REPORT_WORKFLOW
   ↓
S_TCODE
   ↓
TCD = SWIA
   ↓
Autorização relacionada à execução da transação
```

Porém, **S_TCODE não representa necessariamente todas as autorizações
necessárias dentro da transação**.

Durante a execução, o SAP pode realizar outras verificações, como:

``` text
S_RFC
S_PROGRAM
S_DATASET
...
```

Por isso, o acesso a uma transação e o acesso às funcionalidades
disponíveis dentro dela são conceitos relacionados, mas não
necessariamente iguais.

------------------------------------------------------------------------

# 2. 🛠️ Transações principais

  T-Code   Finalidade
  -------- ----------------------------------------------
  `SU53`   Analisar falhas de autorização
  `PFCG`   Criar/manter Roles e autorizações
  `SU01`   Manutenção de usuários e atribuição de Roles
  `SWIA`   Administração de atividades de Workflow
  `STMS`   Administração e transporte de Requests

### 🧠 Para memorizar

``` text
SU53 → O que pode estar faltando?
PFCG → Onde a Role/autorização é administrada?
SU01 → Qual usuário recebe a Role?
STMS → Onde o transporte é administrado?
```

------------------------------------------------------------------------

# 3. 🔎 SU53 --- Análise de autorização

A `SU53` é uma ferramenta de diagnóstico de autorizações.

Quando um usuário tenta executar uma operação e recebe uma mensagem de
autorização, a `SU53` pode mostrar a verificação que não foi atendida.

Fluxo:

``` text
Usuário executa uma operação
        ↓
       ❌
        ↓
      SU53
        ↓
Analisar a verificação de autorização
```

### Exemplo

Pode aparecer:

``` text
Objeto: S_TCODE
Campo:  TCD
Valor:  SWIA
```

Isso indica que houve uma verificação relacionada ao código de transação
`SWIA`.

## ⚠️ Importante

Não devemos simplesmente copiar tudo que aparecer na `SU53`.

A SU53 é uma **ferramenta de diagnóstico**, e a autorização deve ser
analisada antes de ser adicionada ou alterada.

É necessário considerar:

-   O que o usuário realmente precisa executar;
-   Qual objeto está sendo verificado;
-   Qual campo está sendo solicitado;
-   Qual valor deve ser autorizado;
-   Qual Role deve fornecer a autorização;
-   As regras de segurança e o padrão do ambiente.

------------------------------------------------------------------------

# 4. 🧩 PFCG --- Roles

A `PFCG` é uma das principais transações utilizadas para administração
de Roles.

Uma Role pode conter diversos objetos de autorização:

``` text
Z_ADM_REPORT_WORKFLOW
        │
        ├── S_TCODE
        ├── S_RFC
        ├── S_DATASET
        ├── S_PROGRAM
        └── outros objetos
```

A Role representa um conjunto organizado de autorizações necessárias
para determinada função ou atividade.

------------------------------------------------------------------------

# 5. 📦 Função individual

Uma **função individual** é uma Role que contém os objetos de
autorização.

Exemplo:

``` text
Z_ADM_REPORT_WORKFLOW
```

Pode conter:

``` text
S_TCODE
   TCD = SWIA

S_RFC
   ACTVT = 16
   RFC_TYPE = FUNC
   RFC_NAME = ...

S_PROGRAM
   P_ACTION = SUBMIT
   P_GROUP = WF_ADMIN

S_DATASET
   ACTVT = ...
   FILENAME = ...
   PROGRAM = ...
```

### 🧠 Memorize

> **Função individual = Role que contém os objetos de autorização.**

------------------------------------------------------------------------

# 6. 📚 Função composta

Uma **função composta** agrupa várias funções individuais.

Exemplo:

``` text
Z_BASIS_WORKFLOW
       │
       ├── Z_SWIA
       ├── Z_WORKFLOW_DISPLAY
       └── Z_WORKFLOW_ADMIN
```

Analogia:

``` text
Função individual = 📦 uma caixa

Função composta = 📦
                    ├── 📦 Role 1
                    ├── 📦 Role 2
                    └── 📦 Role 3
```

Portanto:

``` text
ROLE COMPOSTA
      │
      ├── ROLE INDIVIDUAL
      ├── ROLE INDIVIDUAL
      └── ROLE INDIVIDUAL
```

A função composta facilita a organização e a atribuição de conjuntos de
Roles.

------------------------------------------------------------------------

# 7. 🔐 Objetos de autorização

Uma Role pode possuir vários objetos de autorização.

Na tarefa estudada, foram observados:

``` text
S_TCODE
S_RFC
S_DATASET
S_PROGRAM
```

Podemos visualizar:

``` text
ROLE
 │
 ├── S_TCODE
 │
 ├── S_RFC
 │
 ├── S_DATASET
 │
 └── S_PROGRAM
```

Cada objeto possui campos que determinam **o que exatamente está sendo
autorizado**.

------------------------------------------------------------------------

# 8. 🔑 S_TCODE --- Código da transação

O objeto:

``` text
S_TCODE
```

está relacionado à verificação do código da transação.

Seu campo principal é:

``` text
TCD
```

### Exemplo da tarefa

``` text
S_TCODE
   │
   └── TCD = SWIA
```

### 🧠 Decore

``` text
S_TCODE
   ↓
TCD
   ↓
Código da transação
```

Isso representa a autorização relacionada à execução da transação
`SWIA`.

> ⚠️ A autorização `S_TCODE` não deve ser interpretada como autorização
> completa para todas as funcionalidades executadas pela transação.

------------------------------------------------------------------------

# 9. 🌐 S_RFC --- Autorizações RFC

O objeto:

``` text
S_RFC
```

é utilizado para verificações de autorização relacionadas a chamadas
RFC.

Na tarefa estudada foram observados os campos:

``` text
ACTVT
RFC_NAME
RFC_TYPE
```

Exemplo:

``` text
S_RFC
│
├── ACTVT
│     └── 16
│
├── RFC_TYPE
│     └── FUNC
│
└── RFC_NAME
      ├── /IWWRK/WF_TGW_QUERY_TASKS
      ├── /IWWRK/WF_TGW_TASK_DEFINITION
      ├── /IWWRK/WF_TGW_TASK_SCN_COUNT
      ├── /IWWRK/WF_TGW_TASK_DET_ACTION
      ├── /IWBEP/FM_MGW_HANDLE_REQUEST
      ├── /IWWRK/WF_TGW_EXPAND
      ├── /IWWRK/WF_TGW_TASK_DET_QUERY
      ├── /IWWRK/WF_TGW_CUSTOM_ATTRIBUTE
      └── /IWWRK/WF_TGW_TASK_DEC_OP_ACT
```

## 9.1 `ACTVT`

Na tarefa:

``` text
ACTVT = 16
```

Representa a atividade definida para a verificação daquele objeto.

> A interpretação exata de um valor `ACTVT` deve ser feita de acordo com
> a documentação do objeto e do contexto SAP; não é recomendável decorar
> o número isoladamente.

## 9.2 `RFC_TYPE`

Na tarefa:

``` text
RFC_TYPE = FUNC
```

Indica que a verificação está relacionada ao tipo de objeto RFC
correspondente a função.

## 9.3 `RFC_NAME`

O campo `RFC_NAME` contém os nomes das funções RFC que a Role contempla.

### Funções observadas na tarefa

``` text
/IWWRK/WF_TGW_QUERY_TASKS
/IWWRK/WF_TGW_TASK_DEFINITION
/IWWRK/WF_TGW_TASK_SCN_COUNT
/IWWRK/WF_TGW_TASK_DET_ACTION
/IWBEP/FM_MGW_HANDLE_REQUEST
/IWWRK/WF_TGW_EXPAND
/IWWRK/WF_TGW_TASK_DET_QUERY
/IWWRK/WF_TGW_CUSTOM_ATTRIBUTE
/IWWRK/WF_TGW_TASK_DEC_OP_ACT
```

> ⚠️ Esses valores representam os nomes observados na tarefa estudada.
> Eles não devem ser copiados automaticamente para outras Roles. Os
> objetos e valores necessários dependem da aplicação, versão SAP e
> desenho de segurança do ambiente.

------------------------------------------------------------------------

# 10. 📁 S_DATASET --- Acesso a arquivos

Outro objeto observado na tarefa:

``` text
S_DATASET
```

Ele está relacionado a verificações de autorização para acesso a
arquivos/datasets.

Na tarefa foram observados campos como:

``` text
ACTVT
FILENAME
PROGRAM
```

Exemplo:

``` text
S_DATASET
│
├── ACTVT
│     └── 33, 34, A6, A7
│
├── FILENAME
│     └── *
│
└── PROGRAM
      └── SAPLSWT1
```

## ⚠️ Atenção ao `*`

O caractere `*` funciona como coringa e pode representar uma abrangência
ampla.

Exemplo:

``` text
FILENAME = *
```

Por isso, não devemos utilizar `*` apenas porque uma autorização mais
ampla resolveu o problema.

O escopo deve estar de acordo com:

-   Necessidade funcional;
-   Segurança;
-   Padrão da empresa;
-   Princípio do menor privilégio.

------------------------------------------------------------------------

# 11. 💻 S_PROGRAM --- Execução de programas ABAP

Outro objeto observado:

``` text
S_PROGRAM
```

Descrição:

> ABAP: verificações da execução de programas

Na tarefa:

``` text
S_PROGRAM
│
├── P_ACTION
│     └── SUBMIT
│
└── P_GROUP
      └── WF_ADMIN
```

## Campos observados

### `P_ACTION`

Na tarefa:

``` text
P_ACTION = SUBMIT
```

Representa a ação relacionada à execução do programa.

### `P_GROUP`

Na tarefa:

``` text
P_GROUP = WF_ADMIN
```

Define o grupo de autorização de programas considerado na verificação.

------------------------------------------------------------------------

# 12. 🚚 Fluxo DEV → QAS → PRD

O processo de alteração estudado segue o conceito:

``` text
DEV → QAS → PRD
```

Fluxo completo:

``` text
        DEV
         │
         ▼
  Criar/Ajustar Role
         │
         ▼
      REQUEST
         │
         ▼
        QAS
         │
         ▼
       TESTE
         │
         ▼
        PRD
         │
         ▼
Associar Role ao usuário
         │
         ▼
       ACESSO
```

## 12.1 DEV

Ambiente onde a Role/autorização é criada ou ajustada.

## 12.2 Request

A alteração é registrada em uma Request de transporte.

### Regra do processo estudado

> **Basis não cria a Request.**

No processo aprendido:

``` text
Analista responsável
        ↓
Cria/ajusta a autorização
        ↓
Request
        ↓
Basis
        ↓
Transporte conforme procedimento
```

Ou seja, é importante separar:

-   **Criação/alteração da autorização**
-   **Criação da Request**
-   **Transporte da Request**

São responsabilidades que podem estar distribuídas entre diferentes
analistas/equipes.

## 12.3 QAS

Ambiente utilizado para validação e testes.

## 12.4 PRD

Ambiente produtivo.

Depois que a Role está disponível em PRD, ela pode ser associada ao
usuário conforme o procedimento definido pela empresa.

------------------------------------------------------------------------

# 13. 👤 Associação da Role ao usuário

Uma Role existir em PRD não significa automaticamente que determinado
usuário terá suas autorizações.

Exemplo:

``` text
Role:
Z_ADM_REPORT_WORKFLOW
```

Usuário:

``` text
USUARIO_X
```

A associação pode ser representada:

``` text
USUARIO_X
    │
    ▼
Z_ADM_REPORT_WORKFLOW
    │
    ▼
Objetos de autorização
    │
    ▼
Acesso
```

Dependendo do procedimento de manutenção da Role, também é necessário
garantir que o **perfil de autorização esteja gerado/atualizado**.

------------------------------------------------------------------------

# 14. 🧪 Exemplo prático --- SWIA

Imagine uma solicitação:

> O usuário precisa acessar a transação `SWIA`.

## Passo 1 --- Identificar a necessidade

``` text
Usuário
   ↓
Precisa utilizar SWIA
```

------------------------------------------------------------------------

## Passo 2 --- Verificar se existe erro de autorização

Caso o usuário já tenha tentado executar:

``` text
Usuário
   ↓
Executa SWIA
   ↓
Erro de autorização
   ↓
SU53
```

A `SU53` ajuda a identificar a verificação que falhou.

------------------------------------------------------------------------

## Passo 3 --- Analisar a Role

No `PFCG`, verificar a Role adequada.

Exemplo:

``` text
S_TCODE
   ↓
TCD = SWIA
```

E verificar também outras autorizações necessárias ao cenário, como:

``` text
S_RFC
S_PROGRAM
S_DATASET
```

------------------------------------------------------------------------

## Passo 4 --- Validar os valores

No exemplo estudado, `S_RFC` continha:

``` text
ACTVT = 16
RFC_TYPE = FUNC
```

e funções RFC específicas no `RFC_NAME`:

``` text
/IWWRK/WF_TGW_QUERY_TASKS
/IWWRK/WF_TGW_TASK_DEFINITION
/IWWRK/WF_TGW_TASK_SCN_COUNT
/IWWRK/WF_TGW_TASK_DET_ACTION
/IWBEP/FM_MGW_HANDLE_REQUEST
/IWWRK/WF_TGW_EXPAND
/IWWRK/WF_TGW_TASK_DET_QUERY
/IWWRK/WF_TGW_CUSTOM_ATTRIBUTE
/IWWRK/WF_TGW_TASK_DEC_OP_ACT
```

------------------------------------------------------------------------

## Passo 5 --- Request

A alteração deve seguir o processo definido pela equipe.

> No processo estudado, a criação da Request não é realizada pelo Basis.

------------------------------------------------------------------------

## Passo 6 --- Transporte

Fluxo:

``` text
DEV
 ↓
QAS
 ↓
PRD
```

------------------------------------------------------------------------

## Passo 7 --- Associação em PRD

A Role é associada ao usuário conforme o procedimento da equipe.

------------------------------------------------------------------------

## Passo 8 --- Teste

O usuário testa:

``` text
SWIA
```

Se ocorrer novamente uma falha de autorização:

``` text
SU53
```

e o processo de análise pode ser repetido.

------------------------------------------------------------------------

# 15. ✅ Checklist de atendimento

Quando receber uma solicitação de autorização, use este raciocínio:

### 🔎 Diagnóstico

-   [ ] Qual transação ou funcionalidade o usuário precisa acessar?
-   [ ] O usuário já tentou executar a operação?
-   [ ] Existe erro de autorização?
-   [ ] Foi analisada a `SU53`?
-   [ ] Qual objeto de autorização está sendo verificado?
-   [ ] Qual campo/valor está sendo solicitado?

### 🧩 Role

-   [ ] Qual Role deve fornecer a autorização?
-   [ ] É uma função individual?
-   [ ] É uma função composta?
-   [ ] A Role já existe?
-   [ ] Quais objetos são necessários?
-   [ ] Os valores estão de acordo com a necessidade?

### 🚚 Transporte

-   [ ] A alteração foi realizada em DEV?
-   [ ] A Request foi criada pelo responsável conforme o processo?
-   [ ] A Request está pronta para transporte?
-   [ ] Foi transportada para QAS?
-   [ ] O teste foi realizado?
-   [ ] Foi transportada para PRD?

### 👤 Usuário

-   [ ] A Role está disponível em PRD?
-   [ ] A Role foi associada ao usuário?
-   [ ] O perfil de autorização foi atualizado/gerado quando necessário?
-   [ ] O usuário realizou o teste?

------------------------------------------------------------------------

# 16. 📝 Cola rápida

``` text
╔══════════════════════════════════════════════╗
║          SAP AUTHORIZATION — BOLSO           ║
╠══════════════════════════════════════════════╣
║ SU53      → diagnóstico de autorização       ║
║ PFCG      → administração de Roles           ║
║ SU01      → usuário / atribuição             ║
║ STMS      → transporte                       ║
╠══════════════════════════════════════════════╣
║ Individual → Role com objetos de autorização ║
║ Composta   → agrupa Roles individuais        ║
╠══════════════════════════════════════════════╣
║ S_TCODE   → código da transação              ║
║ S_RFC     → verificações relacionadas a RFC  ║
║ S_DATASET → acesso a arquivos/datasets       ║
║ S_PROGRAM → execução de programas ABAP       ║
╠══════════════════════════════════════════════╣
║ Fluxo:                                       ║
║ DEV → QAS → PRD                              ║
╠══════════════════════════════════════════════╣
║ PRD:                                         ║
║ Role → Usuário → Acesso                      ║
╚══════════════════════════════════════════════╝
```

------------------------------------------------------------------------

# 17. 🎯 O que estudar depois

Depois de entender o fluxo básico, os próximos assuntos recomendados
são:

-   [ ] PFCG em profundidade
-   [ ] Aba **Authorizations** do PFCG
-   [ ] Status dos objetos de autorização
-   [ ] `ACTVT`
-   [ ] `TCD`
-   [ ] `RFC_NAME`
-   [ ] `RFC_TYPE`
-   [ ] `P_ACTION`
-   [ ] `P_GROUP`
-   [ ] Geração/atualização do perfil de autorização
-   [ ] Atribuição de Roles no `SU01`
-   [ ] Diferença entre Role, Profile e Authorization
-   [ ] `SUIM` para análise de usuários e autorizações
-   [ ] `S_USER_AGR`
-   [ ] `S_USER_GRP`
-   [ ] `S_USER_OBJ`
-   [ ] `S_TABU_DIS`
-   [ ] `S_TABU_NAM`

------------------------------------------------------------------------

# 🧠 Mapa mental final

O conceito inteiro pode ser resumido assim:

``` text
                         SAP AUTHORIZATION
                                │
                  ┌─────────────┴─────────────┐
                  │                           │
                SU53                       PFCG
                  │                           │
            Diagnóstico                  Role
                                              │
                                   ┌──────────┴──────────┐
                                   │                     │
                              Individual             Composta
                                   │                     │
                             Objetos de             Agrupa Roles
                             autorização            individuais
                                   │
                   ┌───────────────┼───────────────┐
                   │               │               │
                S_TCODE          S_RFC         S_PROGRAM
                   │               │
                TCD=SWIA       RFC_NAME
                                RFC_TYPE
                                ACTVT
                   │
                   └─────── S_DATASET
```

## 🚚 Fluxo operacional

``` text
Necessidade do usuário
        ↓
Análise
        ↓
SU53 (quando houver falha)
        ↓
PFCG
        ↓
Role / Objetos / Campos / Valores
        ↓
Request
        ↓
DEV
        ↓
QAS
        ↓
Teste
        ↓
PRD
        ↓
Role → Usuário
        ↓
Acesso
```

------------------------------------------------------------------------

## 📌 Resumo em uma frase

> **SU53 ajuda a identificar a falha, PFCG administra a Role e seus
> objetos de autorização, o transporte leva a alteração de DEV para QAS
> e PRD, e a associação da Role ao usuário disponibiliza as autorizações
> conforme o processo do ambiente.**

------------------------------------------------------------------------

> ⚠️ **Nota de segurança:** este material é destinado a estudo. Objetos,
> campos e valores de autorização devem sempre ser validados de acordo
> com a necessidade funcional, documentação SAP e políticas de segurança
> da organização. Evite conceder `*` ou autorizações amplas sem
> justificativa.

------------------------------------------------------------------------

## 📚 Tarefa prática utilizada como referência

Role observada:

``` text
Z_ADM_REPORT_WORKFLOW
```

Transação principal:

``` text
SWIA
```

Objetos observados:

``` text
S_TCODE
S_RFC
S_DATASET
S_PROGRAM
```

Funções RFC observadas:

``` text
/IWWRK/WF_TGW_QUERY_TASKS
/IWWRK/WF_TGW_TASK_DEFINITION
/IWWRK/WF_TGW_TASK_SCN_COUNT
/IWWRK/WF_TGW_TASK_DET_ACTION
/IWBEP/FM_MGW_HANDLE_REQUEST
/IWWRK/WF_TGW_EXPAND
/IWWRK/WF_TGW_TASK_DET_QUERY
/IWWRK/WF_TGW_CUSTOM_ATTRIBUTE
/IWWRK/WF_TGW_TASK_DEC_OP_ACT
```
