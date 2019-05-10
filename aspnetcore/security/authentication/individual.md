---
title: Artigos baseados em projetos do ASP.NET Core criados com contas de usuário individuais
author: rick-anderson
description: Descubra artigos baseados em projetos do ASP.NET Core criados com contas de usuário individuais.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: f9c1be16386da935382275815bb5fd5c72894b1c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892523"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Artigos baseados em projetos do ASP.NET Core criados com contas de usuário individuais

O ASP.NET Core Identity é incluído nos modelos de projeto no Visual Studio com a opção "Contas de usuário individuais".

Os modelos de autenticação estão disponíveis na CLI do .NET Core com `-au Individual`:

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Ver [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/5833) para autenticação de API da web.

<a name="no"></a>

## <a name="no-authentication"></a>Sem autenticação

Autenticação é especificada na CLI do .NET Core com o `-au` opção. No Visual Studio, o **alterar autenticação** caixa de diálogo está disponível para novos aplicativos da web. É o padrão para novos aplicativos web no Visual Studio **sem autenticação**.

Projetos criados com nenhuma autenticação:

* Não contêm páginas da web e a interface do usuário para entrar e sair.
* Não contêm o código de autenticação.

<a name="win"></a>

## <a name="windows-authentication"></a>Autenticação do Windows

Autenticação do Windows é especificada para novos aplicativos web na CLI do .NET Core com o `-au Windows` opção. No Visual Studio, o **alterar autenticação** caixa de diálogo fornece o **autenticação do Windows** opções.

Se a autenticação do Windows é selecionada, o aplicativo está configurado para usar o [módulo do IIS da autenticação Windows](xref:host-and-deploy/iis/modules). Autenticação do Windows destina-se a sites da Intranet.

## <a name="additional-resources"></a>Recursos adicionais

Os artigos a seguir mostram como usar o código gerado em modelos do ASP.NET Core que usam contas de usuário individual:

* [Autenticação de dois fatores com SMS](xref:security/authentication/2fa)
* [Confirmação de conta e de recuperação de senha no ASP.NET Core](xref:security/authentication/accconfirm)
* [Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização](xref:security/authorization/secure-data)
