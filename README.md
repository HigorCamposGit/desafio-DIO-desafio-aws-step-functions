# Desafio-DIO-desafio-aws-step-functions.
# 🛵 Criando meu Primeiro Workflow no AWS Step Functions.

Olá! 👋 Seja bem-vindo(a) ao meu repositório. 

Este projeto foi desenvolvido durante o desafio prático da **DIO (Digital Innovation One)**. Do curso de **Formação AWS Cloud Foundations** Como estou no início da minha jornada em **Nuvem e AWS**, está documentado o que aprendi, os conceitos básicos que entendi e como montei meu primeiro fluxo de automação usando o **AWS Step Functions**.

---

## 📌 O que você vai encontrar aqui?
- [O que é o AWS Step Functions? (Em palavras simples)](#-o-que-é-o-aws-step-functions-em-palavras-simples).
- [O Desafio: Simulando um Assistente de Entrega](#-o-desafio-simulando-um-assistente-de-entrega).
- [Conceitos Principais que Aprendi](#-conceitos-principais-que-aprendi).
- [Como o Fluxo Funciona (Passo a Passo)](#-como-o-fluxo-funciona-passo-a-passo).
- [Código do Workflow (JSON)](#-código-do-workflow-json).
- [Meus Principais Aprendizados](#-meus-principais-aprendizados).

---

## 💡 O que é o AWS Step Functions? (Palavras simples).

Pense no **AWS Step Functions** como um "maestro" de uma orquestra. 

Em um sistema moderno, temos várias pequenas tarefas rodando separadas (como checar um pagamento, atualizar o estoque ou enviar um e-mail). O Step Functions é a ferramenta que organiza a ordem em que tudo isso deve acontecer: *"Primeiro faz isso, se der certo faz aquilo, se der errado avisa o usuário"*. (aplicação de estruturas condicionais).

Tudo isso é feito de forma visual, o que facilita para quem está começando a entender arquitetura em nuvem!

---

## 🛵 O Desafio: Simulando um Assistente de Entrega.

O objetivo deste laboratório foi criar o fluxo de um **Assistente de Entrega** (estilo um aplicativo de delivery). 

O sistema precisa seguir algumas regras simples:
1. Receber o pedido e validar os dados.
2. Se os dados estiverem certos então tenta processar o pagamento.
3. Se o pagamento for aprovado então agenda a entrega do produto.
4. Se algo der errado no caminho, o sistema cancela e avisa sobre o erro.

---

## 🧠 Conceitos Principais que Aprendi.

Durante as aulas e a prática, entendi que o **Step Functions** funciona com alguns blocos fundamentais:

* **State Machine (Máquina de Estados):** É o nome chique que se dá para o fluxo completo do processo.
* **Task (Tarefa):** É uma ação que precisa ser feita (exemplo: chamar uma função na AWS para calcular o frete).
* **Choice (Escolha):** É um ponto de decisão no fluxo (um "Se / Senão"). Exemplo: *Se o pagamento for aprovado, vá para a etapa A; Senão, vá para a etapa B*.
* **Succeed / Fail:** Blocos que indicam se o processo terminou com **sucesso** ou com **falha**.

---

## 📐 Como o Fluxo Funciona (Passo a Passo).

Fiz um esquema bem simples para ilustrar o caminho que o pedido faz dentro do meu workflow:

[ Início: Pedido Recebido ]
│
▼
[ Checar dados do Pedido ]
│
┌──────┴──────┐
( Válido )     ( Inválido )
│             │
▼             ▼
[ Pagamento ]  [ Cancelar ] ──► [ Fim: Falha ❌ ]
│
┌──┴──┐
( Ok ) ( Erro )
│      │
▼      └───────────────────► [ Fim: Falha ❌ ]
[ Agendar Entrega ]
│
▼
[ Fim: Sucesso 🎉 ]


---

## 📝 Código do Workflow ( ARQUIVO JSON).

Na AWS, nós podemos montar o fluxo arrastando blocos na tela ou usando um código estruturado em JSON (chamado de **ASL - Amazon States Language**). 

Abaixo está o código que usei para estruturar meu desafio:

```json
{
  "Comment": "Meu primeiro workflow de assistente de entrega",
  "StartAt": "ValidarPedido",
  "States": {
    "ValidarPedido": {
      "Type": "Task",
      "Resource": "arn:aws:states:::lambda:invoke",
      "Parameters": {
        "FunctionName": "MinhaFuncaoValidarPedido"
      },
      "Next": "PedidoEValido"
    },
    "PedidoEValido": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.status",
          "StringEquals": "OK",
          "Next": "ProcessarPagamento"
        }
      ],
      "Default": "PedidoInvalido"
    },
    "ProcessarPagamento": {
      "Type": "Task",
      "Resource": "arn:aws:states:::lambda:invoke",
      "Parameters": {
        "FunctionName": "MinhaFuncaoPagamento"
      },
      "Next": "AgendarEntrega"
    },
    "AgendarEntrega": {
      "Type": "Task",
      "Resource": "arn:aws:states:::lambda:invoke",
      "Parameters": {
        "FunctionName": "MinhaFuncaoEntrega"
      },
      "Next": "Sucesso"
    },
    "PedidoInvalido": {
      "Type": "Fail",
      "Cause": "O pedido possui dados incorretos.",
      "Error": "DadosInvalidos"
    },
    "Sucesso": {
      "Type": "Succeed"
    }
  }
}
```
🔍 Meus Principais Aprendizados
Visibilidade: Foi muito legal ver visualmente por onde o processo passa e onde ele para se acontecer algum tipo de erro.

Não precisa reinventar a roda: Em vez de escrever centenas de linhas de código para tratar erros e tentativas, o próprio **Step Functions** já tem opções prontas para reexecutar tarefas que falharam.

Prática é fundamental: Assistir às aulas é importante, mas colocar a mão no console da AWS e ver o fluxo rodando fez toda a diferença para o meu aprendizado.

🎓 Projeto desenvolvido para o Curso: "Formação AWS Cloud Foundations" na plataforma DIO.
