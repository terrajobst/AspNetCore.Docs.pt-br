---
title: Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais
author: rick-anderson
description: Descubra artigos com base em projetos ASP.NET Core criados com contas de usuário individuais.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080699"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais

ASP.NET Core identidade está incluída nos modelos de projeto no Visual Studio com a opção "contas de usuário individuais".

Os modelos de autenticação estão disponíveis no CLI do .NET Core `-au Individual`com:

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/5833) para autenticação da API Web.

<a name="no"></a>

## <a name="no-authentication"></a>Sem autenticação

A autenticação é especificada no CLI do .NET Core com a `-au` opção. No Visual Studio, a caixa de diálogo **alterar autenticação** está disponível para novos aplicativos Web. O padrão para novos aplicativos Web no Visual Studio **não é autenticação**.

Projetos criados sem autenticação:

* Não conte páginas da Web e interface do usuário para entrar e sair.
* Não contêm código de autenticação.

<a name="win"></a>

## <a name="windows-authentication"></a>Autenticação do Windows

A autenticação do Windows é especificada para novos aplicativos Web no CLI do .NET Core com `-au Windows` a opção. No Visual Studio, a caixa de diálogo **alterar autenticação** fornece as opções de **autenticação do Windows** .

Se a autenticação do Windows for selecionada, o aplicativo será configurado para usar o [módulo IIS de autenticação do Windows](xref:host-and-deploy/iis/modules). A autenticação do Windows destina-se a sites da intranet.

## <a name="additional-resources"></a>Recursos adicionais

Os artigos a seguir mostram como usar o código gerado em ASP.NET Core modelos que usam contas de usuário individuais:

* [Autenticação de dois fatores com SMS](xref:security/authentication/2fa)
* [Confirmação de conta e de recuperação de senha no ASP.NET Core](xref:security/authentication/accconfirm)
* [Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização](xref:security/authorization/secure-data)
