# Documentação Técnica - Entrada de CTE

Sistema: Intratech - Grupo Roma
Tipo: Automação Web com Interface Gráfica (GUI) - Processamento de Faturamento

## Sumário
1. Visão Geral
2. Objetivo e Escopo
3. Arquitetura Técnica
4. Dependências e Requisitos
5. Estrutura do Código
6. Fluxo de Execução
7. Configurações e Parâmetros
8. Funções Principais
9. Janela Operacional
10. RUN-ID e Rastreabilidade
11. Tratamento de Erros
12. Exemplos de Uso
13. Troubleshooting

---

## 1. Visão Geral

O script **Automacao_Web_Faturamento_GUI.py** é uma aplicação com interface gráfica (GUI) desenvolvida para automatizar o processo de faturamento em um sistema web (aparentemente baseado em GeneXus), utilizando dados de entrada fornecidos em uma planilha Excel. Ele combina uma interface amigável para seleção de arquivos e controle de execução com a robustez da automação web via Selenium.

### 1.1 Resumo Executivo

| Característica | Detalhe |
| :--- | :--- |
| **Tipo** | Aplicação GUI / Automação Web (Selenium) |
| **Linguagem** | Python 3.x |
| **Propósito** | Automatizar a entrada de dados e confirmação de faturamento em sistema web, lendo dados de Excel. |
| **Interface** | CustomTkinter (GUI) |
| **Frequência de Uso** | Sob demanda (execução manual via GUI) |
| **Dados de Entrada** | Arquivo Excel (`.xlsx` ou `.xls`) |
| **Dados de Saída** | Atualização do arquivo Excel com status/erros |

### 1.2 Fluxo Resumido

1.  **Inicialização:** O usuário executa o script, abrindo a GUI.
2.  **Seleção de Arquivo:** O usuário seleciona o arquivo Excel contendo os dados de faturamento.
3.  **Processamento:** O script inicia o navegador (Chrome), navega até o sistema, e itera sobre cada linha do Excel.
4.  **Automação Web:** Para cada linha, ele preenche campos (como data de movimento e observação), e clica no botão de confirmação.
5.  **Atualização:** Em caso de erro, o script tenta novamente e, após 2 falhas, registra o erro na coluna "ERRO" do Excel e salva o arquivo.

---

## 2. Objetivo e Escopo

### 2.1 Objetivo Principal

Prover uma ferramenta de automação robusta e controlada por interface gráfica para processar grandes volumes de dados de faturamento contidos em planilhas Excel, reduzindo a intervenção manual e o tempo de processamento no sistema web.

### 2.2 Processo Automatizado

1.  **Seleção de Dados:** O usuário define a planilha de trabalho.
2.  **Login:** O script realiza o login no sistema web.
3.  **Iteração:** O script percorre as linhas do DataFrame (Excel).
4.  **Interação Web:** Para cada registro, ele localiza elementos (incluindo dentro de `iframes`), preenche dados (como a data atual e a observação concatenada com o número da NF), e confirma a operação.
5.  **Tratamento de Falhas:** Gerencia falhas de conexão ou de elemento, com um mecanismo de repetição (até 2 erros) antes de registrar a falha no Excel.

### 2.3 Escopo

| Incluído | Não Incluído |
| :--- | :--- |
| Interface gráfica para controle de execução. | Lógica de negócio do sistema web (apenas interação). |
| Automação de login e navegação básica. | Geração ou manipulação complexa de dados no Excel. |
| Leitura de dados de Excel (Pandas/Openpyxl). | Execução em *loop* contínuo ou agendamento (apenas sob demanda). |
| Preenchimento de campos e cliques em botões. | Tratamento de *pop-ups* de alerta não-Selenium. |
| Mecanismo de busca de elementos em `iframes`. | |
| Registro de erros no arquivo Excel de origem. | |

---

## 3. Arquitetura Técnica

### 3.1 Componentes Principais

O script é estruturado em torno de três pilares tecnológicos:

1.  **Interface Gráfica (GUI):** Utiliza `customtkinter` para criar a janela principal, botões de controle (Iniciar, Parar, Selecionar Arquivo) e uma área de log (`scrolledtext`).
2.  **Automação Web:** Utiliza `Selenium` (com Chrome WebDriver) para interagir com o sistema web. Configurações como `headless=new` e `window-size` são definidas para otimizar a execução em segundo plano.
3.  **Processamento de Dados:** Utiliza `pandas` e `openpyxl` para ler, manipular e salvar dados de volta na planilha Excel.

### 3.2 Fluxo de Dados

1.  **Entrada:** Arquivo Excel (`arquivo_excel`) selecionado pelo usuário.
2.  **Processamento:** O script lê o Excel para um DataFrame (`df`).
3.  **Saída:** O script atualiza o DataFrame com o status de erro e salva o DataFrame de volta no arquivo Excel original.

---

## 4. Dependências e Requisitos

### 4.1 Dependências Python

O script requer as seguintes bibliotecas Python, que devem ser instaladas via `pip`:

```bash
pip install selenium pandas openpyxl customtkinter Pillow zoneinfo
```

| Biblioteca | Propósito |
| :--- | :--- |
| `selenium` | Automação de navegador (Chrome WebDriver). |
| `pandas` | Manipulação e processamento de dados do Excel. |
| `openpyxl` | Engine para escrita e leitura de arquivos `.xlsx` pelo Pandas. |
| `customtkinter` | Framework para a interface gráfica (GUI). |
| `Pillow` (PIL) | Manipulação de imagens (usada para o ícone da GUI). |
| `zoneinfo` | Manipulação de fusos horários (necessário para Python 3.9+). |

### 4.2 Requisitos do Sistema

*   **Python:** 3.9+ (para suporte a `zoneinfo`).
*   **Navegador:** Google Chrome instalado (necessário para o Chrome WebDriver).
*   **Chrome WebDriver:** O driver compatível com a versão do Chrome instalada deve estar acessível pelo script (geralmente gerenciado automaticamente pelo Selenium, mas pode requerer configuração manual em alguns ambientes).
*   **Permissões:** Permissão de leitura e escrita no arquivo Excel de entrada.

---

## 5. Estrutura do Código

O código é predominantemente procedural, organizado em blocos lógicos e funções auxiliares.

### 5.1 Seções Principais

| Seção | Descrição |
| :--- | :--- |
| **Imports** | Importação de bibliotecas padrão e de terceiros (Selenium, Pandas, Tkinter, etc.). |
| **Configurações** | Definição das `chrome_options` (modo headless, tamanho da janela) e variáveis globais. |
| **GUI Setup** | Configuração da janela principal (`ctk.CTk`) e dos *widgets* (botões, *scrolledtext*). |
| **Funções Auxiliares** | Funções como `achar_elemento_por_id_em_iframes` e `log_message`. |
| **Função Principal** | A função `tarefa()` que contém a lógica de automação web e processamento do Excel. |
| **Loop Principal** | O `root.mainloop()` para a GUI e o `while True` que chama a `tarefa()` (embora a lógica principal esteja na `tarefa()`). |

### 5.2 Organização de Funções

| Função | Descrição |
| :--- | :--- |
| `log_message(message)` | Função auxiliar para imprimir mensagens no console e na área de log da GUI. |
| `select_file()` | Abre uma caixa de diálogo para o usuário selecionar o arquivo Excel. |
| `start_task()` | Inicia a thread de execução da automação (`tarefa()`). |
| `stop_task()` | Define a flag `parando` para interromper a execução. |
| `achar_elemento_por_id_em_iframes(driver, id_alvo, timeout=10)` | **Crítica:** Procura um elemento pelo ID, alternando entre o conteúdo principal e todos os `iframes` da página. |
| `tarefa()` | Contém a lógica principal: inicialização do WebDriver, leitura do Excel, loop de processamento, interação web e tratamento de erros. |

---

## 6. Fluxo de Execução

O fluxo de execução é iniciado pelo usuário através da interface gráfica.

### 6.1 Sequência de Passos (Início)

1.  O usuário executa o script Python.
2.  A janela principal do CustomTkinter é exibida.
3.  O usuário clica em **"Selecionar Arquivo"** e escolhe a planilha Excel.
4.  O usuário clica em **"Iniciar"**, o que dispara a função `start_task()`, que por sua vez inicia a função `tarefa()` em uma *thread* separada.

### 6.2 Sequência de Passos (Processamento na `tarefa()`)

1.  **Inicialização do WebDriver:** O Chrome é iniciado com as opções configuradas (modo *headless*).
2.  **Leitura do Excel:** O arquivo Excel é lido para um DataFrame (`df`).
3.  **Loop de Processamento:** O script itera sobre cada linha do DataFrame.
4.  **Automação Web:**
    *   Navega para a URL de destino.
    *   Realiza o login (lógica de login não totalmente visível, mas pressuposta).
    *   Busca o campo de **Data de Movimento** (`vNOTAFISCAL_DATAMOVIMENTO`) usando `achar_elemento_por_id_em_iframes`.
    *   Preenche o campo com a data atual (`%d/%m/%Y`).
    *   Busca o campo de **Observação** (`vNOTAFISCAL_OBSERVACAO`).
    *   Concatena o número da NF (`row["NF"]`) à observação existente.
    *   Clica no botão **"CONFIRMA"**.
    *   Trata um possível *pop-up* de confirmação.
5.  **Tratamento de Erros:** Se ocorrer uma exceção, o contador `erro_count` é incrementado. Após 2 erros na mesma nota, o erro é registrado na coluna "ERRO" do Excel, e o arquivo é salvo.

---

## 7. Configurações e Parâmetros

### 7.1 Configurações do WebDriver

As seguintes opções são definidas para o Chrome WebDriver:

| Opção | Valor | Propósito |
| :--- | :--- | :--- |
| `--headless=new` | Ativado | Executa o navegador em segundo plano, sem interface visível. |
| `--window-size` | `1920,1080` | Define a resolução para evitar problemas de layout em modo *headless*. |
| `--no-sandbox` | Ativado | Necessário em alguns ambientes Linux para execução sem privilégios. |
| `--disable-gpu` | Ativado | Recomendado para execução em modo *headless*. |

### 7.2 Parâmetros de Entrada (Excel)

O script espera que o arquivo Excel contenha as colunas necessárias para a automação, sendo a coluna **"NF"** (Nota Fiscal) explicitamente utilizada para preencher a observação.

---

## 8. Funções Principais

### 8.1 `achar_elemento_por_id_em_iframes`

Esta função é crucial para a robustez da automação, especialmente em sistemas como GeneXus que fazem uso intensivo de `iframes`.

```python
def achar_elemento_por_id_em_iframes(driver, id_alvo, timeout=10):
    # ... (lógica de busca fora e dentro de iframes)
```

**Lógica:**
1.  Tenta localizar o elemento no contexto principal da página.
2.  Se não encontrar, itera sobre todos os `iframes` encontrados na página.
3.  Para cada `iframe`, ele troca o contexto do driver para o `iframe` e tenta localizar o elemento.
4.  Retorna o elemento assim que encontrado e visível.

---

## 9. Janela Operacional

Este script **não** possui uma janela operacional definida por horário, como o script orquestrador. Sua execução é **manual** e **sob demanda**, controlada pelo usuário através da GUI.

---

## 10. RUN-ID e Rastreabilidade

O script **não** utiliza um `RUN-ID` formal como o script orquestrador. A rastreabilidade é feita através:

1.  **Logs da GUI:** Mensagens de *log* em tempo real na área de texto da aplicação.
2.  **Atualização do Excel:** O registro de erros na coluna **"ERRO"** do arquivo Excel de origem serve como rastreamento de falhas.

---

## 11. Tratamento de Erros

O tratamento de erros é implementado no bloco `try...except` principal dentro do loop de processamento do Excel.

| Tipo de Erro | Ação |
| :--- | :--- |
| **Exceção Geral** (`Exception as e`) | Imprime o erro e o *traceback*. |
| **Contagem de Erros** | O contador `erro_count` é incrementado. |
| **Erro Fatal (2 vezes)** | Após 2 erros consecutivos na mesma linha (nota fiscal), o erro é registrado na coluna **"ERRO"** do DataFrame, o arquivo Excel é salvo, e o contador é resetado. |
| **Interrupção** | A flag `parando` (controlada pelo botão "Parar") permite a interrupção graciosa do loop. |

---

## 12. Exemplos de Uso

### 12.1 Execução Padrão

1.  Execute o script:
    ```bash
    python Automacao_Web_Faturamento_GUI.py
    ```
2.  Clique em **"Selecionar Arquivo"** e escolha o Excel.
3.  Clique em **"Iniciar"**.

### 12.2 Interrupção

1.  Durante a execução, clique em **"Parar"**.
2.  O script definirá a flag `parando = True` e encerrará o loop de processamento após a conclusão da nota fiscal atual.

---

## 13. Troubleshooting

| Problema | Causa Mais Comum | Solução |
| :--- | :--- | :--- |
| **"WebDriverException: Message: 'chromedriver' executable needs to be in PATH"** | O Chrome WebDriver não está instalado ou não está no caminho do sistema. | Instale o Chrome WebDriver compatível com sua versão do Chrome e adicione-o ao PATH, ou use um gerenciador de drivers (como `webdriver-manager`). |
| **"Elemento com ID '...' não encontrado."** | O ID do elemento web mudou, ou o sistema web está lento. | Verifique o ID do elemento no sistema web. Aumente o `timeout` na função `achar_elemento_por_id_em_iframes`. |
| **"Erro ao salvar o Excel"** | O arquivo Excel está aberto em outro programa (ex: Microsoft Excel). | Feche o arquivo Excel antes de iniciar a automação. |
| **O script não inicia o navegador** | Problema com as `chrome_options` ou o driver. | Verifique se o Chrome está instalado. Tente remover a opção `--headless=new` para ver o navegador e diagnosticar o problema visualmente. |
| **O script trava após 2 erros** | Comportamento esperado. | Verifique a nota fiscal que causou o erro no Excel e corrija o problema no sistema web ou nos dados de entrada. |
