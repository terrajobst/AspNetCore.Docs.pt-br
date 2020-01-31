---
title: SDK do ASP.NET Core Web
author: Rick-Anderson
description: Visão geral de Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869760"
---
# <a name="aspnet-core-web-sdk"></a>SDK do ASP.NET Core Web

### <a name="overview"></a>{1&gt;Visão Geral&lt;1}

`Microsoft.NET.Sdk.Web` é um [SDK de projeto do MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para compilar ASP.NET Core aplicativos. É possível criar um aplicativo ASP.NET Core sem esse SDK, no entanto, o SDK da Web é:

* Adaptado em direção à oferta de uma experiência de primeira classe.
* O destino recomendado para a maioria dos usuários.

Use o Web. SDK em um projeto do:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Recursos habilitados usando o SDK da Web:

* Projetos voltados para o .NET Core 3,0 ou posterior referência implícita:

  * A [estrutura compartilhada ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analisadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) projetados para a criação de aplicativos ASP.NET Core.
* O SDK da Web importa destinos do MSBuild que habilitam o uso de perfis de publicação e publicação usando o WebDeploy.

### <a name="properties"></a>{1&gt;Propriedades&lt;1}

| propriedade | Descrição |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Desabilita a referência implícita para a estrutura compartilhada `Microsoft.AspNetCore.App`. |
| `DisableImplicitAspNetCoreAnalyzers` | Desabilita a referência implícita a ASP.NET Core analisadores. |
| `DisableImplicitComponentsAnalyzers` | Desabilita a referência implícita aos analisadores de componentes do Razor ao compilar aplicativos de Blazor (servidor). |
