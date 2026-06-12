<div align="center">
  <img src="https://github.com/user-attachments/assets/f1dcf201-bbbf-4080-aa57-ef57f6fbc998" width="250" alt="Mongito logo"/>
  <p><strong>Gerador de scripts MongoDB a partir de arquivos CSV</strong></p>
  <p>
    <img alt="GitHub release" src="https://img.shields.io/github/v/release/sk9g3/mongito?style=flat-square&color=06d6a0"/>
    <img alt="Downloads" src="https://img.shields.io/github/downloads/sk9g3/mongito/total?style=flat-square&color=06d6a0"/>
    <img alt="macOS" src="https://img.shields.io/badge/macOS-✓-blue?style=flat-square"/>
    <img alt="Windows" src="https://img.shields.io/badge/Windows-✓-blue?style=flat-square"/>
    <img alt="Linux" src="https://img.shields.io/badge/Linux-✓-blue?style=flat-square"/>
    <img alt="Go" src="https://img.shields.io/badge/Go-1.21-00acd7?style=flat-square"/>
  </p>
</div>

---

## O que é

Mongito é um app desktop que lê arquivos CSV e gera scripts `.js` prontos para rodar no **MongoDB Shell** (`mongosh`), usando `bulkWrite` para operações em lote com alta performance.

### Por que Mongito?

O nome nasceu numa sala de espera de veterinário — enquanto minha chow chow se recuperava de uma cirurgia, meus filhos ficaram encantados com os cachorros que chegavam. Surgiu a ideia de um mascote: um texugo, inspirado num capítulo que lemos naquela noite [Intérprete, o nome do texugo hehehe]. *Mongito* = MongoDB + bichinho fofo.

---

## Funcionalidades

- 📂 Carregamento de CSV com detecção automática de delimitador (`,` `;` `\t` `|`)
- 🔍 Inferência automática de tipos: `Text`, `GUID`, `ISODate`, `Number`, `Boolean`
- 🗺️ Mapeamento visual coluna-a-coluna com nome Mongo editável
- ⚙️ Operações: `UpdateOne`, `UpdateMany`, `UpsertOne`, `InsertOne`, `DeleteOne`, `DeleteMany`
- 🔑 Filtros `AND` / `OR` com chaves por valor ou por nulo
- 🕐 Timezone configurável (datas convertidas para UTC automaticamente)
- 👁️ Preview das primeiras 5 linhas antes de gerar
- ⚠️ Validação de dados com relatório no log
- 💾 Exportar/importar mapeamentos em `.json` para reutilizar
- 📦 Geração em lotes de 40.000 registros (streaming, sem carregar tudo na RAM)
- 🌙 Tema claro e escuro

---

## Anteriormente conhecido como Csv To Mongo

Este projeto nasceu como **Csv To Mongo**, uma ferramenta desktop escrita em **C# / Avalonia UI**.

Com a migração para **Go + Wails**, o app foi reescrito do zero, ganhou novas funcionalidades e renomeado para **Mongito** — mais leve, mais rápido e sem precisar instalar o .NET.

| | Csv To Mongo | Mongito |
|---|---|---|
| Linguagem | C# / .NET 9 | Go 1.21 |
| UI | Avalonia (XAML) | Wails v2 (HTML/CSS/JS) |
| Runtime necessário | .NET 9 | Nenhum (binário único) |
| Operações | UpdateOne, UpdateMany | + UpsertOne, InsertOne, DeleteOne, DeleteMany |
| Preview do script | ❌ | ✅ |
| Validação de dados | ❌ | ✅ |
| Exportar mapeamento | ❌ | ✅ |
| Timezone configurável | ❌ | ✅ |
| Tema claro/escuro | ❌ | ✅ |

---

## Download

Acesse a [última versão](../../releases/latest) e baixe o binário para o seu sistema:

| Plataforma | Arquivo |
|---|---|
| macOS (Intel + Apple Silicon) | `mongito-macos.zip` |
| Windows 64-bit | `mongito-windows-amd64.exe` |
| Linux 64-bit (AppImage) | `mongito-linux-amd64.AppImage` |
| Linux 64-bit (binário nativo) | `mongito-linux-amd64` |

---

## Como usar

O processo é simples e dividido em 3 etapas:

### 1. Seleção de Arquivos

- **Arquivo CSV:** Clique no ícone e selecione sua planilha. O app detecta o delimitador (`,` `;` `\t` `|`) e lê os cabeçalhos automaticamente.
- **Pasta Destino:** Escolha onde os scripts serão salvos. Se deixar em branco, cria uma pasta `Output` ao lado do CSV.
- **Nome do Script:** Define o prefixo dos arquivos gerados (ex: `update_clientes` → `update_clientes_1.js`, `update_clientes_2.js`...).

### 2. Configurações do MongoDB

- **Coleção:** Nome da collection no banco (ex: `clientes`, `produtos`).
- **Operação:**

| Operação | Descrição |
| :--- | :--- |
| `UpdateOne` | Atualiza o primeiro documento que bater o filtro |
| `UpdateMany` | Atualiza todos os documentos que baterem o filtro |
| `UpsertOne` | Atualiza se existir, **cria** se não existir |
| `InsertOne` | Insere um documento novo (sem filtro) |
| `DeleteOne` | Deleta o primeiro documento que bater o filtro |
| `DeleteMany` | Deleta todos os documentos que baterem o filtro |

- **Filtro (Where):** Define o operador lógico (`AND` / `OR`) ao usar múltiplas chaves de busca.

### 3. Fuso Horário

O Mongito assume que as datas no CSV estão no fuso selecionado e as converte automaticamente para **UTC** antes de salvar no MongoDB — comportamento padrão do MongoDB que armazena tudo em UTC.

| Fuso | Região |
| :--- | :--- |
| BRT −3 São Paulo | SP, RJ, MG, RS, PR, SC e a maioria dos estados |
| AMT −4 Manaus | AM, RO, MT, MS |
| BRT −3 Belém | PA, AP, MA (sem horário de verão) |
| BRT −3 Fortaleza | CE, PI, RN, PB, PE, AL, SE, BA |
| BRT −3 Recife | Mesma UTC-3 mas fuso oficial diferente |
| FNT −2 Noronha | Fernando de Noronha |
| UTC ±0 | Quando as datas já estão em UTC |

> 💡 Se suas datas já vierem com timezone explícito (formato RFC3339, ex: `2024-03-15T10:00:00-03:00`), o Mongito respeita o offset do próprio valor e ignora o fuso selecionado.

### 4. Mapeamento de Colunas

Para cada coluna do CSV você configura:

| Opção | Descrição |
| :--- | :--- |
| **Nome Mongo** | Nome do campo no banco. Ex: CSV `cd_cli` → Mongo `codigo_cliente` |
| **Tipo** | Formato do dado: `Text`, `Number`, `Boolean`, `Guid` (UUID), `ISODate` |
| **Chave (Val)** | Usa o valor do CSV para filtrar o documento. Ex: `WHERE id = '123'` |
| **Chave (Null)** | Filtra documentos onde o campo é nulo, ignorando o valor do CSV |
| **Ignorar** | Campo não será incluído no `$set` — útil para colunas usadas só como filtro |

#### 💡 Exemplos de Combinação

**Cenário 1: Atualizar dados buscando pelo ID**
- Coluna `ID`: Marque `Chave (Val)` + `Ignorar`
  - _O app usa o ID para encontrar o registro, mas **não** altera seu valor._
- Demais colunas: deixe desmarcado — serão atualizadas normalmente.

**Cenário 2: Preencher campo apenas se ele for nulo no banco**
- Coluna `Status`: Marque `Chave (Null)` + `Ignorar`
  - _Busca apenas documentos onde Status é `null`._
- Coluna `NovoStatus`: deixe desmarcado — será preenchida nesses registros.

**Cenário 3: Inserir novos registros**
- Selecione `InsertOne` — as colunas de chave somem automaticamente.
- Marque `Ignorar` nas colunas que não devem ir para o documento.

### 🛠️ Ferramentas extras

| Botão | O que faz |
| :--- | :--- |
| **👁 Preview** | Mostra as primeiras 5 linhas do script antes de gerar |
| **⚠ Validar** | Varre o CSV e reporta GUIDs, datas e números inválidos no log |
| **↓ Exportar** | Salva o mapeamento atual em `.json` para reutilizar |
| **↑ Importar** | Carrega um mapeamento salvo e aplica nas colunas do CSV atual |

---

## Instalação

### macOS
Após baixar, extraia o `.zip` e na **primeira abertura** rode no terminal:
```bash
xattr -cr Mongito.app
open Mongito.app
```
Nas próximas vezes pode abrir normalmente pelo Finder ou clicando duas vezes.

### Windows
Execute o `.exe` normalmente. Se aparecer aviso do Windows SmartScreen:
> "Mais informações" → "Executar assim mesmo"

### Linux

**AppImage** — recomendado para a maioria dos usuários, sem dependências:
```bash
chmod +x mongito-linux-amd64.AppImage
./mongito-linux-amd64.AppImage
```

**Binário nativo** — para usuários que já têm o webkit instalado:
```bash
# Ubuntu 20.04
sudo apt install libwebkit2gtk-4.0-dev

# Ubuntu 22.04+
sudo apt install libwebkit2gtk-4.1-dev

chmod +x mongito-linux-amd64
./mongito-linux-amd64
```

---

## Exemplo de script gerado

```js
db.clientes.bulkWrite([
{ updateOne: { filter: { "id": UUID("550e8400-e29b-41d4-a716-446655440000") }, update: { $set: { "nome": "João Silva", "ativo": true, "atualizado_em": ISODate("2024-03-15T13:00:00.000Z") } } } },
{ updateOne: { filter: { "id": UUID("6ba7b810-9dad-11d1-80b4-00c04fd430c8") }, update: { $set: { "nome": "Maria Costa", "ativo": false, "atualizado_em": ISODate("2024-03-15T13:00:00.000Z") } } } },
]);
```

---

## Tecnologias

- [Go 1.21](https://go.dev)
- [Wails v2](https://wails.io) — framework desktop Go + Web
- [Vite](https://vitejs.dev) — build do frontend
- Fontes: [Syne](https://fonts.google.com/specimen/Syne) + [DM Mono](https://fonts.google.com/specimen/DM+Mono)

---

## Encontrou um bug? Tem uma sugestão?

Pode mandar a braba [Issue](https://github.com/sk9g3/mongito/issues) descrevendo:

**Para bugs:**
- Sistema operacional e versão
- Versão do Mongito (visível no canto superior direito do app)
- Passos para reproduzir
- O que esperava acontecer vs o que aconteceu

**Para sugestões:**
- Descrição da funcionalidade
- Por que seria útil
- Exemplos de uso se possível

