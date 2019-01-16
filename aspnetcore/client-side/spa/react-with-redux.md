---
title: Usar o modelo de projeto do React com Redux com o ASP.NET Core
author: SteveSandersonMS
description: Saiba como começar a trabalhar com o modelo de projeto do SPA (Aplicativo de Página Única) do ASP.NET Core para React com Redux e create-react-app.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react-with-redux
ms.openlocfilehash: c1aedcf1e14a9e7b339b60dd02c4267cd5945a49
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341598"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a>Usar o modelo de projeto do React com Redux com o ASP.NET Core

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> Esta documentação não pertencem ao modelo de projeto React com Redux incluído no ASP.NET Core 2.0. Ele se refere ao modelo de reagir com Redux mais recente que você pode atualizar manualmente. O modelo está disponível no ASP.NET Core 2.1 ou posterior.

::: moniker-end

O modelo de projeto do React com Redux atualizado fornece um ponto inicial conveniente para aplicativos do ASP.NET Core usando convenções do React, do Redux e de CRA [(create-react-app)](https://github.com/facebookincubator/create-react-app) para implementar uma IU (interface do usuário) avançada do lado do cliente.

Com exceção do comando de criação de projeto, todas as informações sobre o modelo React com Redux são as mesmas que aquelas sobre o modelo React. Para criar esse tipo de projeto, execute `dotnet new reactredux` em vez de `dotnet new react`. Para obter mais informações sobre a funcionalidade comum para ambos os modelos baseados em reagir, confira a [Documentação do modelo React](xref:spa/react).

Para obter informações sobre como configurar um aplicativo de subpropriedades React com Redux no IIS, consulte [ReactRedux modelo 2.1: Não é possível usar o SPA no IIS (aspnet/modelagem &num;555)](https://github.com/aspnet/Templating/issues/555).
