---
title: Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais
author: rick-anderson
description: Descubra artigos com base em projetos ASP.NET Core criados com contas de usuário individuais.
ms.author: riande
ms.date: 12/11/2019
uid: security/authentication/individual
ms.openlocfilehash: 7ef0d5eabded61d04fb9fe7be384a663ad7ea5f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828705"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Artigos baseados em projetos ASP.NET Core criados com contas de usuário individuais

ASP.NET Core identidade está incluída nos modelos de projeto no Visual Studio com a opção "contas de usuário individuais".

Os modelos de autenticação estão disponíveis em CLI do .NET Core com `-au Individual`:

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

Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5833) para autenticação da API Web.

<a name="no"></a>

## <a name="no-authentication"></a>Sem Autenticação

A autenticação é especificada no CLI do .NET Core com a opção `-au`. No Visual Studio, a caixa de diálogo **alterar autenticação** está disponível para novos aplicativos Web. O padrão para novos aplicativos Web no Visual Studio **não é autenticação**.

Projetos criados sem autenticação:

* Não conte páginas da Web e interface do usuário para entrar e sair.
* Não contêm código de autenticação.

<a name="win"></a>

## <a name="windows-authentication"></a>Autenticação do Windows

A autenticação do Windows é especificada para novos aplicativos Web no CLI do .NET Core com a opção `-au Windows`. No Visual Studio, a caixa de diálogo **alterar autenticação** fornece as opções de **autenticação do Windows** .

Se a autenticação do Windows for selecionada, o aplicativo será configurado para usar o [módulo IIS de autenticação do Windows](xref:host-and-deploy/iis/modules). A autenticação do Windows destina-se a sites da intranet.

## <a name="dotnet-new-webapp-authentication-options"></a>novas opções de autenticação de webapp do dotnet

A tabela a seguir mostra as opções de autenticação disponíveis para novos aplicativos Web:

| Opção | Tipo de autenticação | Link para mais informações |
 | ----------------- | ------------ | ---------- |
| {1&gt;Nenhum&lt;1}            |  Sem autenticação | | 
| Individual      |  Autenticação individual | <xref:security/authentication/identity>
| IndividualB2C   |  Autenticação individual hospedada na nuvem com o Azure AD B2C | [B2C do AD do Azure](/azure/active-directory-b2c/) |
| SingleOrg       |  Autenticação organizacional para um único locatário | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  Autenticação organizacional para vários locatários | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Portal         |  Autenticação do Windows | [Autenticação do Windows](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Novas opções de autenticação webapp do Visual Studio

A tabela a seguir mostra as opções de autenticação disponíveis ao criar um novo aplicativo Web com o Visual Studio:

| Opção | Tipo de autenticação | Link para mais informações |
 | ----------------- | ------------ | ---------- |
| {1&gt;Nenhum&lt;1}            |  Sem autenticação | | 
| Contas de usuário individuais/armazenar contas de usuário no aplicativo |  Autenticação individual | <xref:security/authentication/identity> |
| Contas de usuário individuais/Conecte-se a um armazenamento de usuário existente na nuvem |  Autenticação individual hospedada na nuvem com o Azure AD B2C | [B2C do AD do Azure](/azure/active-directory-b2c/) |
| Nuvem corporativa ou de estudante/organização única  |  Autenticação organizacional para um único locatário | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Nuvem corporativa ou de estudante/várias org |  Autenticação organizacional para vários locatários | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Portal         |  Autenticação do Windows | [Autenticação do Windows](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Recursos adicionais

Os artigos a seguir mostram como usar o código gerado em ASP.NET Core modelos que usam contas de usuário individuais:

* [Confirmação de conta e de recuperação de senha no ASP.NET Core](xref:security/authentication/accconfirm)
* [Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização](xref:security/authorization/secure-data)
