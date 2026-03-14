### 📋 Extração de Faturas de Imagem com IA ###

**Robô de Automação para Extração Inteligente de Dados de Faturas**

Um projeto de RPA (Robotic Process Automation) que utiliza o framework REFrameWork do UiPath e integra-se com a API Gemini do Google para extrair e processar dados de imagens de notas fiscais de forma inteligente e automatizada.

---

## 🎯 Objetivos

- Capturar imagens de faturas/notas fiscais
- Converter imagens para Base64 para envio à IA
- Extrair dados estruturados usando modelo Google Gemini
- Processar informações em formato JSON estruturado
- Registrar e atualizar status de transações no Orquestrador
- Proporcionar tratamento de exceções e retry automático

---

## 🏗️ Arquitetura do Projeto

Este projeto é construído sobre o **REFrameWork (Robotic Enterprise Framework)** com as seguintes características:

- **State Machine** para controle de fluxo de fases
- **Logging avançado** para rastreamento de execução
- **Tratamento de exceções** com recuperação automática
- **Configurações externas** mantidas em arquivos e assets do Orquestrador
- **Screenshots automáticas** em caso de exceções do sistema

---

## 📊 Dados Extraídos

O robô extrai os seguintes dados de cada fatura:

| Campo | Tipo | Descrição |
|-------|------|-----------|
| Invoice No | Integer | Número sequencial da fatura |
| Date | String (DD/MM/YYYY) | Data de emissão em formato brasileiro |
| Vendor | String | Nome do fornecedor/empresa |
| Subtotal | Float | Subtotal sem impostos |
| Tax | Float | Impostos cobrados |
| Total | Float | Valor total da fatura |

**Formato de saída:** JSON estruturado

---

## 🔄 Fluxo de Execução

### 1. **INICIALIZAR PROCESSO**
- `./Framework/InitAllSettings.xaml` - Carrega configurações do arquivo Config.xlsx e assets do Orquestrador
- `./Framework/InitAllApplications.xaml` - Inicializa aplicações necessárias

### 2. **OBTER DADOS DE TRANSAÇÃO**
- `./Framework/GetTransactionData.xaml` - Busca transações da fila do Orquestrador ou fonte de dados configurada

### 3. **PROCESSAR TRANSAÇÃO**
Etapas principais do processamento:

- **`./Framework/ProcessBox/ConvertImgToBase64.xaml`** - Converte imagem de fatura para formato Base64
- **`./Framework/ProcessBox/RequestAPI.xaml`** - Envia imagem e prompt para API Gemini
  - URL: `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent`
  - Autenticação: `x-goog-api-key`
  - Content-Type: `application/json`
- **`./Framework/ProcessBox/InvoiceProcess.xaml`** - Processa resposta da IA e estrutura dados
- **`./Framework/ProcessBox/ConvetDtExcel.xaml`** - Converte dados para formato Excel
- **`./Framework/SetTransactionStatus.xaml`** - Atualiza status (Sucesso, Exceção de Regra de Negócio ou Exceção do Sistema)

### 4. **ENCERRAR PROCESSO**
- `./Framework/CloseAllApplications.xaml` - Encerra aplicações utilizadas

---

## 📦 Estrutura de Pastas

```
.
├── Main.xaml                    # Workflow principal (State Machine)
├── project.json                 # Configuração do projeto UiPath
├── Framework/                   # Workflows do framework
│   ├── InitAllSettings.xaml
│   ├── InitAllApplications.xaml
│   ├── GetTransactionData.xaml
│   ├── Process.xaml
│   ├── SetTransactionStatus.xaml
│   └── ProcessBox/              # Workflows específicos do processo
│       ├── ConvertImgToBase64.xaml
│       ├── InvoiceProcess.xaml
│       ├── RequestAPI.xaml
│       └── ConvetDtExcel.xaml
├── Data/
│   ├── Input/                   # Arquivos de entrada
│   │   ├── prompt_json.txt      # Prompt para IA Gemini
│   │   └── Setup.txt            # Configuração da API
│   ├── Output/                  # Resultados processados
│   └── Temp/                    # Arquivos temporários
├── Tests/                       # Casos de teste automatizados
└── Documentation/               # Documentação adicional
```

---

## 🔧 Pré-requisitos

### Software
- UiPath Studio 24.10.18 ou superior
- .NET Framework
- Navegador web (para inicialização de aplicações)

### Dependências NuGet
- `UiPath.Excel.Activities` v2.24.4
- `UiPath.System.Activities` v24.10.8
- `UiPath.UIAutomation.Activities` v24.10.14
- `UiPath.WebAPI.Activities` v2.4.0
- `UiPath.Testing.Activities` v24.10.4

### Configuração Externa
- **Google API Key** - Chave para acesso à API Gemini
- **Fila do Orquestrador** - Configurada para receber transações com imagens de faturas
- **Prompt JSON** - Armazenado em `Data/Input/prompt_json.txt`

---

## ⚙️ Configuração Inicial

1. **Configure a API Gemini**
   - Acesse https://aistudio.google.com/
   - Obtenha sua chave API
   - Atualize a configuração com a chave em `Data/Input/Setup.txt`

2. **Ajuste o arquivo de Configuração**
   - Localize `Config.xlsx` (se utilizado)
   - Configure a fila do Orquestrador: `OrchestratorQueueName`
   - Configure a pasta de entrada de imagens

3. **Customize o Prompt de IA**
   - Edite `Data/Input/prompt_json.txt` conforme necessário
   - O prompt define os campos a serem extraídos da imagem

4. **Implante os Workflows**
   - Certifique-se de que todos os workflows estão presentes em `./Framework/`
   - Atualize as referências em `Main.xaml` se necessário

---

## 🌐 Configuração da API Gemini

### Links Importantes
- **Google AI Studio**: https://aistudio.google.com/ (obtenha sua chave API)
- **Documentação API Gemini**: https://ai.google.dev/gemini-api/docs?hl=pt-br#rest
- **Ferramentas Base64**: https://www.base64decode.net/

### Detalhes da Requisição HTTP

```
Request method: POST
Request URL: https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent

Headers:
  - x-goog-api-key: "SUA_CHAVE_API"
  - Content-Type: application/json
```

### Estrutura do Payload JSON

```json
{
  "contents": [
    {
      "parts": [
        {
          "inlineData": {
            "mimeType": "image/jpeg",
            "data": "<BASE64_DA_IMAGEM>"
          }
        },
        {
          "text": "<PROMPT_JSON>"
        }
      ]
    }
  ]
}
```

### Exemplo de Implementação em VB.NET

Para construir o payload em Visual Basic dentro do UiPath:

```vb
New Dictionary(Of String, Object) From {
    {"contents", New List(Of Object) From {
        New Dictionary(Of String, Object) From {
            {"parts", New List(Of Object) From {
                New Dictionary(Of String, Object) From {
                    {"inlineData", New Dictionary(Of String, Object) From {
                        {"mimeType", "image/jpeg"}, 
                        {"data", Base64String}
                        }
                    }
                },
                New Dictionary(Of String, Object) From {
                    {"text", InputPrompt}
                }
            }
            }
        }
    }
    }
}
```

### Arquivo de Saída (Excel)

O robô gera um arquivo Excel com as seguintes colunas:

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Invoice No | Número | Identificador da fatura |
| Date | Data | Data de emissão |
| Vendor | Texto | Nome do fornecedor |
| Subtotal | Moeda | Subtotal |
| Tax | Moeda | Impostos |
| Total | Moeda | Total a pagar |

**Local**: `Data/Output/` (após processamento bem-sucedido)

---

## 🚀 Execução

```bash
# Executar através do Orquestrador UiPath
# ou 

# Executar localmente no UiPath Studio
# Abra Main.xaml e clique em "Run"
```

---

## 📄 Exemplo de Entrada/Saída

### Entrada
- Imagem de fatura em formato JPEG/PNG

### Saída (JSON)
```json
[
    {
        "Invoice No": 1726,
        "Date": "21/09/2022",
        "Vendor": "ADATUM CORPORATION",
        "Subtotal": 147.29,
        "Tax": 2.96,
        "Total": 150.25
    }
]
```

---

## 🐛 Tratamento de Erros

- **Exceção de Negócio**: Fatura com dados incompletos ou ilegíveis
- **Exceção do Sistema**: Erro na API, arquivo não encontrado, timeout
- **Retry Automático**: `./Framework/RetryCurrentTransaction.xaml` (configurável)
- **Screenshots**: Capturadas em `./Exceptions_Screenshots/` para análise

---

## 📚 Documentação
Para informações sobre a API Gemini, visite: https://ai.google.dev/gemini-api/docs

---

## 🔐 Segurança

- As credenciais são armazenadas no **Credential Manager do Windows** ou **Assets do Orquestrador**
- Dados sensíveis (como `password`, `api-key`) são excluídos dos logs conforme configurado
- As chaves de API devem ser rotacionadas regularmente

---

## 📝 Notas Importantes

- **Formato de Data**: Sempre em formato brasileiro (DD/MM/YYYY)
- **Números**: Extraídos sem símbolos de moeda ou separadores
- **Base64**: Necessário para transmissão de imagens via API REST
- **Timeout**: Configure conforme necessário para o tamanho das imagens
