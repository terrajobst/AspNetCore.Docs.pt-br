---
title: Desenvolver ASP.NET Core aplicativos usando o OpenAPI
author: ryanbrandenburg
description: Demonstra como usar a ferramenta ' Microsoft. dotnet-openapi ' para adicionar referências a arquivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317768"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Desenvolver ASP.NET Core aplicativos usando ferramentas OpenAPI

Por Ryan Brandenburg

[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) é uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools) para gerenciar referências do [openapi](https://github.com/OAI/OpenAPI-Specification) em um projeto.

## <a name="installation"></a>Instalação

Para instalar `Microsoft.dotnet-openapi`o, execute o seguinte comando:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Adicionar

Adicionar uma referência de openapi usando qualquer um dos comandos nessa página adiciona um `<OpenApiReference />` elemento semelhante ao seguinte ao arquivo *. csproj* :

```xml
<OpenApiReference Include="openapi.json" />
```

A referência anterior é necessária para que o aplicativo chame o código do cliente gerado.

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Adicionar arquivo

#### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição | Exemplo |
|-------|------|-------|---------|
| -v|--Detalhado | Mostrar saída detalhada. |dotnet openapi Adicionar arquivo *-v* .\OpenAPI.JSON |
| -p|--updateProject | O projeto no qual operar. |dotnet openapi Adicionar arquivo *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -c|--gerador de código| O gerador de código a ser aplicado à referência. As opções `NSwagCSharp` são `NSwagTypeScript`e. Se `--code-generator` não for especificado, as ferramentas padrão serão `NSwagCSharp`.|dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código
| -h|--ajuda|Mostrar informações de ajuda|dotnet openapi Adicionar arquivo--ajuda|

#### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição | Exemplo |
|-------------|-------------|---------|
| arquivo de origem | A origem da qual criar uma referência. Deve ser um arquivo OpenAPI. |dotnet openapi Adicionar arquivo *.\OpenAPI.JSON* |

### <a name="add-url"></a>Adicionar URL

#### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição | Exemplo |
|-------|------|-------------|---------|
| -v|--Detalhado | Mostrar saída detalhada. |dotnet openapi Adicionar URL *-v*`https://contoso.com/openapi.json` |
| -p|--updateProject | O projeto no qual operar. |dotnet openapi Adicionar URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -o|--arquivo de saída | Onde posicionar a cópia local do arquivo OpenAPI. |dotnet openapi Adicionar URL `https://contoso.com/openapi.json` *--saída-arquivo myclient. JSON* |
| -c|--gerador de código| O gerador de código a ser aplicado à referência. As opções `NSwagCSharp` são `NSwagTypeScript`e. |dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código
| -h|--ajuda|Mostrar informações de ajuda|dotnet openapi Adicionar URL--ajuda|

#### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição | Exemplo |
|-------------|-------------|---------|
| origem-URL | A origem da qual criar uma referência. Deve ser uma URL. |dotnet openapi Adicionar URL`https://contoso.com/openapi.json` |

## <a name="remove"></a>Remover

Remove a referência OpenAPI correspondente ao nome do arquivo *. csproj* fornecido. Quando a referência de OpenAPI for removida, os clientes não serão gerados. Os arquivos local *. JSON* e *. YAML* são excluídos.

### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição| Exemplo |
|-------|------|------------|---------|
| -v|--Detalhado | Mostrar saída detalhada. |dotnet openapi remover *-v*|
| -p|--updateProject | O projeto no qual operar. |dotnet openapi remover *--updateProject .\Ref.csproj* .\OpenAPI.JSON |
| -h|--ajuda|Mostrar informações de ajuda|dotnet openapi remover--ajuda|

### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição| Exemplo |
| ------------|------------|---------|
| arquivo de origem | A origem para a qual remover a referência. |dotnet openapi remover *.\OpenAPI.JSON* |

## <a name="refresh"></a>Atualizar

Atualiza a versão local de um arquivo que foi baixado usando o conteúdo mais recente da URL de download.

### <a name="options"></a>Opções

| Opção curta| Opção Long| Descrição | Exemplo |
|-------|------|-------------|---------|
| -v|--Detalhado | Mostrar saída detalhada. | dotnet openapi atualização *– v*`https://contoso.com/openapi.json` |
| -p|--updateProject | O projeto no qual operar. | dotnet openapi atualização *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -h|--ajuda|Mostrar informações de ajuda|dotnet openapi atualização--ajuda|

### <a name="arguments"></a>Argumentos

|  Argumento  | Descrição | Exemplo |
| ------------|-------------|---------|
| origem-URL | A URL da qual atualizar a referência. | atualização do dotnet openapi`https://contoso.com/openapi.json` |
