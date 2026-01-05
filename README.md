# Resumo dos Scripts de Automação - Grupo Roma

## 📋 Tabela de Scripts

| # | Script | Linhas | Complexidade | Propósito | Status |
|---|--------|--------|--------------|-----------|--------|
| 1 | **Autorizar_NF_Yamaha.py** | 568 | 🟡 Média | Autorizar Notas Fiscais no Via Nuvem | ✅ Documentado |
| 2 | **Emissao_NF_Yamaha.py** | 2857 | 🔴 Muito Alta | Emitir Notas Fiscais no DealerNet | ✅ Documentado |
| 3 | **Fluxo_Unico_Yamaha.py** | 514 | 🟡 Média | Exportar Processos do Via Nuvem | ✅ Documentado |
| 4 | **Fluxo_Dealer_Yamaha.py** | 1157 | 🟠 Alta | Exportar Propostas + Enriquecimento SQL | ✅ Documentado |
| 5 | **Rotina_Completa_Fat_Yamaha.py** | 285 | 🟡 Média | Orquestrador de Ciclos de Faturamento | ✅ Documentado |

---

## 📊 Análise Detalhada

### 1️⃣ Autorizar_NF_Yamaha.py

**Descrição:** Script de automação RPA que autoriza notas fiscais no sistema Via Nuvem.

| Propriedade | Valor |
|-------------|-------|
| **Linhas de Código** | 568 |
| **Complexidade** | Média |
| **Tipo** | Automação Web (Selenium) |
| **Dependências** | selenium, webdriver-manager, openpyxl |
| **Tempo de Execução** | ~5-10 minutos |
| **Modo Padrão** | Headless |
| **Funcionalidades** | Login, navegação, autorização de NF, tratamento de popups |

---

### 2️⃣ Emissao_NF_Yamaha.py

**Descrição:** Script complexo de automação RPA que emite notas fiscais no DealerNet com integração SQL Server.

| Propriedade | Valor |
|-------------|-------|
| **Linhas de Código** | 2857 |
| **Complexidade** | Muito Alta |
| **Tipo** | Automação Web (Selenium) + SQL Server + GeneXus |
| **Dependências** | selenium, webdriver-manager, openpyxl, pyodbc |
| **Tempo de Execução** | ~15-25 minutos |
| **Modo Padrão** | Headless |
| **Funcionalidades** | Login, navegação em iframes, validação de chassi, integração SQL, emissão de NF, tratamento de regras complexas |

---

### 3️⃣ Fluxo_Unico_Yamaha.py

**Descrição:** Script de automação RPA que exporta processos do Via Nuvem e processa dados em Excel.

| Propriedade | Valor |
|-------------|-------|
| **Linhas de Código** | 514 |
| **Complexidade** | Média |
| **Tipo** | Automação Web (Selenium) + Processamento Excel |
| **Dependências** | selenium, webdriver-manager, openpyxl |
| **Tempo de Execução** | ~5-15 minutos |
| **Modo Padrão** | Headless |
| **Funcionalidades** | Login, exportação de processos, ajuste de planilha, deduplicação, extração de Pedido/Chassi |

---

### 4️⃣ Fluxo_Dealer_Yamaha.py

**Descrição:** Script de automação RPA que exporta propostas do DealerNet com enriquecimento de dados via SQL Server.

| Propriedade | Valor |
|-------------|-------|
| **Linhas de Código** | 1157 |
| **Complexidade** | Alta |
| **Tipo** | Automação Web (Selenium) + Processamento Excel + SQL Server |
| **Dependências** | selenium, webdriver-manager, openpyxl, unidecode, pyodbc |
| **Tempo de Execução** | ~10-25 minutos |
| **Modo Padrão** | Headless |
| **Funcionalidades** | Login, navegação em iframes, exportação paginada, decomposição de campos, enriquecimento SQL, logs estruturados |

---

### 5️⃣ Rotina_Completa_Fat_Yamaha.py

**Descrição:** Orquestrador que gerencia o ciclo completo de faturamento executando 3 scripts em sequência.

| Propriedade | Valor |
|-------------|-------|
| **Linhas de Código** | 285 |
| **Complexidade** | Média |
| **Tipo** | Orquestrador / Gerenciador de Ciclos |
| **Dependências** | subprocess, datetime, zoneinfo, pathlib, argparse |
| **Tempo de Execução** | ~30-60 minutos (por ciclo) |
| **Modo Padrão** | Loop contínuo |
| **Funcionalidades** | Orquestração de scripts, verificação de janela operacional, RUN-ID, tratamento de erros, notificações |

---

## 🔄 Fluxo de Integração

```
Rotina_Completa_Fat_Yamaha.py (Orquestrador)
    │
    ├─→ Relatorio_YAMAHA.py
    │   └─ Coleta dados
    │
    ├─→ Emissao_NF_Yamaha.py
    │   ├─ Integração com SQL Server
    │   ├─ Validação de dados
    │   └─ Emissão de NF
    │
    └─→ Autorizar_NF_Yamaha.py
        └─ Autorização de NF

Fluxo_Dealer_Yamaha.py (Paralelo)
    ├─ Exportação de Propostas
    ├─ Decomposição de Campos
    └─ Enriquecimento SQL

Fluxo_Unico_Yamaha.py (Paralelo)
    ├─ Exportação de Processos
    ├─ Ajuste de Planilha
    └─ Deduplicação
```

---

## 📈 Estatísticas Gerais

| Métrica | Valor |
|---------|-------|
| **Total de Scripts** | 5 |
| **Total de Linhas** | 5.381 |
| **Linhas Médias** | 1.076 |
| **Scripts Simples** | 2 (40%) |
| **Scripts Médios** | 2 (40%) |
| **Scripts Complexos** | 1 (20%) |
| **Documentação Gerada** | 5 arquivos Word (.docx) |
| **Páginas de Documentação** | ~50+ |
| **Funções Documentadas** | 100+ |

---

## 🛠️ Tecnologias Utilizadas

### Automação Web
- **Selenium 4.0+** - Automação de navegador
- **WebDriver Manager** - Gerenciamento de ChromeDriver
- **Chrome/Chromium** - Navegador

### Processamento de Dados
- **OpenPyXL 3.6+** - Processamento de Excel
- **Regex** - Extração de dados estruturados
- **Unidecode** - Normalização de texto

### Integração com Banco de Dados
- **PyODBC 4.0+** - Conexão com SQL Server
- **SQL Server 2019+** - Banco de dados

### Orquestração
- **Subprocess** - Execução de processos
- **ZoneInfo** - Gerenciamento de timezone
- **Argparse** - Parsing de argumentos CLI

---

## 📝 Documentação Disponível

Cada script possui documentação técnica completa em formato Word (.docx) contendo:

- ✅ Visão Geral e Resumo Executivo
- ✅ Objetivo e Escopo
- ✅ Arquitetura Técnica
- ✅ Dependências e Requisitos
- ✅ Estrutura do Código
- ✅ Fluxo de Execução com Diagramas
- ✅ Configurações e Parâmetros
- ✅ Documentação de Funções
- ✅ Exemplos de Uso
- ✅ Troubleshooting

---

## 🚀 Como Usar

### Instalação de Dependências

```bash
pip install selenium webdriver-manager openpyxl unidecode pyodbc
```

### Execução Individual

```bash
# Autorizar NF
python Autorizar_NF_Yamaha.py

# Emitir NF
python Emissao_NF_Yamaha.py

# Exportar Processos
python Fluxo_Unico_Yamaha.py

# Exportar Propostas
python Fluxo_Dealer_Yamaha.py

# Orquestrador (recomendado)
python Rotina_Completa_Fat_Yamaha.py
```

### Modo Interativo

```bash
python Rotina_Completa_Fat_Yamaha.py --no-headless
```

### Modo Único

```bash
python Rotina_Completa_Fat_Yamaha.py --once
```

---

## 📞 Suporte

Para dúvidas sobre os scripts, consulte a documentação técnica em Word (.docx)
