
# 📄 Documentação — Repro_Fiscal.py

## 1. Importações e Constantes

```python
import time
from datetime import date, timedelta
from typing import Tuple
import pandas as pd

from selenium import webdriver
from selenium.webdriver import ActionChains
from selenium.webdriver.chrome.options import Options as ChromeOptions
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import (
    TimeoutException, NoSuchElementException, StaleElementReferenceException,
    UnexpectedAlertPresentException, ElementClickInterceptedException
)

from webdriver_manager.chrome import ChromeDriverManager

DEALERNET_URL = "https://dealernetroma.com.br/LoginAux.aspx?Windows"
```

### 📌 Função de cada importação
- **time, datetime, timedelta** → controle de tempo e manipulação de datas  
- **pandas** → leitura da planilha Excel contendo parâmetros  
- **selenium** → automação web (Chrome, XPATH, ID, eventos, etc.)  
- **webdriver_manager.chrome** → gerencia drivers, embora não esteja sendo usado no script  
- **DEALERNET_URL** → URL da página de login do DealerNet  

---

## 2. Lendo Parâmetros do Excel

```python
arquivo_excel = "Repro_Fiscal.xlsx"
aba = "Repro_Fiscal"

df = pd.read_excel(arquivo_excel, sheet_name=aba)

# Datas (A3 e B3)
DT_INICIO = df.iloc[2, 0]   # A3
DATA_FINAL = df.iloc[2, 1]  # B3

DT_INICIO = pd.to_datetime(DT_INICIO).date()
DATA_FINAL = pd.to_datetime(DATA_FINAL).date()

# Usuário e Senha (C3 e D3)
USUARIO = df.iloc[2, 3]   # C3
SENHA = df.iloc[2, 4]     # D3
```

### 🔍 O que está acontecendo?
- Abre o arquivo **Repro_Fiscal.xlsx**  
- Pega:
  - Data inicial (A3)  
  - Data final (B3)  
  - Usuário (C3)  
  - Senha (D3)  
- Converte as datas para o tipo `date`  

```python
DT_INICIO += timedelta(days=-1)
DT_FIM    = DT_INICIO
```

Isso volta 1 dia na data inicial para alinhar com o loop, pois dentro do `while` o script sempre começa somando 1 dia.

---

## 3. Abrindo o Chrome e Fazendo Login

```python
driver = webdriver.Chrome()
driver.get(DEALERNET_URL)
```

### 3.1 Preenchendo Usuário
```python
user_inp = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.ID, "vUSUARIO_IDENTIFICADORALTERNATIVO"))
)

driver.execute_script("arguments[0].scrollIntoView({block:'center'});", user_inp)
driver.execute_script("arguments[0].click();", user_inp)

user_inp.clear()
user_inp.send_keys(USUARIO)
```

✔ Aguarda o campo aparecer  
✔ Faz scroll  
✔ Clica via JavaScript  
✔ Limpa  
✔ Digita o usuário  

### 3.2 Preenchendo Senha
```python
user_senha = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.ID, "vUSUARIOSENHA_SENHA"))
)

driver.execute_script("arguments[0].scrollIntoView({block:'center'});", user_senha)
driver.execute_script("arguments[0].click();", user_senha)

user_senha.clear()
user_senha.send_keys(SENHA)
```

Mesma lógica do campo de usuário.

### 3.3 Confirmando Login
```python
btn_conf = WebDriverWait(driver, 10).until(
    EC.element_to_be_clickable((By.ID, "IMAGE3"))
)
btn_conf.send_keys(Keys.ENTER)
```

✔ Envia ENTER ao botão de login  
✔ Efetua o login  

---

## 4. Navegação até Escrita Fiscal → Apuração → Escrituração

### 4.1 Abrindo "Escrita Fiscal"
```python
btn = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.ID, "ext-gen38"))
)

driver.execute_script("arguments[0].scrollIntoView({block:'center'});", btn)
ActionChains(driver).move_to_element(btn).perform()
driver.execute_script("arguments[0].click();", btn)
```

### 4.2 Abrindo "Apuração"
```python
el = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, "//span[text()='Apuração']"))
)
ActionChains(driver).move_to_element(el).perform()
```

### 4.3 Acessando "Escrituração"
```python
els = WebDriverWait(driver, 35).until(
    EC.element_to_be_clickable((By.XPATH, "//span[text()='Escrituração']"))
)
driver.execute_script("arguments[0].click();", els)
```

Pronto: você chegou na tela de escrituração.

---

## 5. Loop de Processamento Diário
```python
while DT_INICIO <= DATA_FINAL:
    DT_INICIO += timedelta(days=1)
    DT_FIM += timedelta(days=1)
```

Cada volta do loop processa um único dia.

---

## 6. Função de Fechar Popup “OK”
- Procura botão OK  
- Na página principal ou iframes  
- Se achar → clica e retorna `True`  
- Se não → retorna `False`  

Essa função aparece várias vezes no script — dá pra consolidar.

---

## 7. Preenchendo Data Inicial
```python
set_date_field(driver, "vDATAINICIAL", DT_INICIO)
```

---

## 8. Preenchendo Data Final
```python
set_date_final_field(driver, data=DT_FIM)
```

---

## 9. Selecionando "Saída"
```python
radio = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, "//input[@name='vMOVIMENTO' and @value='S']"))
)
driver.execute_script("arguments[0].click();", radio)
```

---

## 10. Novo Fechamento de Popup
Outro bloco repetido da função `fechar_popup_sessao_ok`.

---

## 11. Marcar Checkbox "ICMS"
```python
cb = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.XPATH, "//input[@id='vISGRUPOTRIBUTOICMS']"))
)

if not cb.is_selected():
    driver.execute_script("arguments[0].click();", cb)
```

---

## 12. Lógica: "Completa" e "Valores Auxiliares"
Fluxo:
- Se Valores Auxiliares estiver clicável → segue o jogo  
- Se não estiver:
  - Tenta clicar em Completa  
  - Depois tenta clicar em Valores Auxiliares novamente  
- Se nada for clicável → apenas prossegue  

---

## 13. Consultar
```python
btn_consulta = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.ID, "IMGCONSULTAR"))
)
driver.execute_script("arguments[0].click();", btn_consulta)
```

---

## 14. Marcar Todos
```python
marcar_todos = WebDriverWait(driver, 20).until(
    EC.element_to_be_clickable((By.XPATH, "//a[text()='Marcar Todos']"))
)
driver.execute_script("arguments[0].click();", marcar_todos)
```

---

## 15. Escriturar
```python
btn_escriturar = WebDriverWait(driver, 20).until(
    EC.presence_of_element_located((By.ID, "ESCRITURARNFPROPRIA"))
)
driver.execute_script("arguments[0].click();", btn_escriturar)
```

---

## 16. Aguardar Resultado
A função procura duas mensagens:
- "Nota(s) escriturada(s) com sucesso!"  
- "Nenhuma Nota Fiscal foi selecionada!"  

---

## 17. Fechamento Final de Popup
Mais uma chamada da função para fechar popups residuais.

---

## 18. 🧠 Resumo do Fluxo Diário
- Avança um dia  
- Fecha popups  
- Preenche Data Inicial / Final  
- Seleciona "Saída"  
- Marca ICMS  
- Ajusta checkboxes "Completa" / "Valores Auxiliares"  
- Executa Consultar  
- Executa Marcar Todos  
- Executa Escriturar NF Própria  
- Fecha popups  
- Aguarda mensagem final  
- Vai para o próximo dia  

O loop continua até atingir a data final do Excel.
