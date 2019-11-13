---
title: Solucionar problemas de Localização no ASP.NET Core
author: hishamco
description: Saiba como diagnosticar problemas com a localização em aplicativos do ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963342"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Solucionar problemas de Localização no ASP.NET Core

Por [Hisham Bin Ateya](https://github.com/hishamco)

Este artigo contém instruções sobre como diagnosticar problemas de localização em aplicativos do ASP.NET Core.

## <a name="localization-configuration-issues"></a>Problemas de configuração da localização

**Ordem do middleware de localização**  
O aplicativo pode não localizar porque o middleware de localização não está ordenado conforme o esperado.

Para resolver esse problema, verifique se o middleware de localização foi registrado antes do middleware do MVC. Caso contrário, o middleware de localização não será aplicado.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Caminho dos recursos de localização não encontrado**

**As culturas compatíveis em RequestCultureProvider não correspondem ao registrado uma vez**  

## <a name="resource-file-naming-issues"></a>Problemas de nomenclatura do arquivo de recurso

O ASP.NET Core tem regras e diretrizes predefinidas para a nomenclatura de arquivos de recurso de localização, que são descritas em detalhes [aqui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Recursos ausentes

As causas comuns para não localizar recursos incluem:

- Nomes de recursos digitados incorretamente no arquivo `resx` ou na solicitação do localizador.
- O recurso está ausente do `resx` para alguns idiomas, mas presente em outros.
- Se ainda houver problemas, verifique as mensagens de log de localização (que estão no nível de log `Debug`) para saber mais detalhes sobre os recursos ausentes.

_**Dica:** Ao usar `CookieRequestCultureProvider`, verifique se aspas simples não são usadas com as culturas dentro do valor do cookie de localização. Por exemplo, `c='en-UK'|uic='en-US'` é um valor de cookie inválido, enquanto `c=en-UK|uic=en-US` é válido._

## <a name="resources--class-libraries-issues"></a>Problemas de bibliotecas de recursos e classes

Por padrão, o ASP.NET Core oferece uma maneira de permitir que as bibliotecas de classes localizem seus arquivos de recurso por meio de [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

Problemas comuns com bibliotecas de classes incluem:
- Ausência do `ResourceLocationAttribute` em uma biblioteca de classes evitará que `ResourceManagerStringLocalizerFactory` descubra os recursos.
- Nomenclatura do arquivo de recurso. Para saber mais, confira a seção [Problemas de nomenclatura do arquivo de recurso](#resource-file-naming-issues).
- Altere o namespace raiz da biblioteca de classes. Para saber mais, confira a seção [Problemas do namespace raiz](#root-namespace-issues).

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider não funciona conforme o esperado

A classe `RequestLocalizationOptions` tem três provedores padrão:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

O [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) permite personalizar como a cultura de localização é fornecida no aplicativo. O `CustomRequestCultureProvider` é usado quando os provedores padrão não atendem aos seus requisitos.

- Um motivo comum para o provedor personalizado não funcionar corretamente é não ser o primeiro provedor na lista `RequestCultureProviders`. Para resolver esse problema:

- Insira o provedor personalizado na posição 0 da lista `RequestCultureProviders`, desta maneira:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- Use o método de extensão `AddInitialRequestCultureProvider` para definir o provedor personalizado como provedor inicial.

## <a name="root-namespace-issues"></a>Problemas do namespace raiz

Quando o namespace raiz de um assembly for diferente do nome do assembly, a localização não funcionará por padrão. Para evitar esse problema, use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), que é descrito em detalhes [aqui](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)

> [!WARNING]
> Isso pode ocorrer quando o nome de um projeto não é um identificador .NET válido. Por exemplo, `my-project-name.csproj` usará o namespace raiz `my_project_name` e o nome do assembly `my-project-name` levando a esse erro. 

## <a name="resources--build-action"></a>Recursos e ação de build

Se você usa arquivos de recurso para localização, é importante que eles tenham uma ação de build correta. Eles precisam ser um **Recurso inserido**; caso contrário, o `ResourceStringLocalizer` não conseguirá localizar os recursos.
