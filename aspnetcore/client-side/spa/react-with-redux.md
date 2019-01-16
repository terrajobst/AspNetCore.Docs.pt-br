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
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="27e8e-103">Usar o modelo de projeto do React com Redux com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="27e8e-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> <span data-ttu-id="27e8e-104">Esta documentação não pertencem ao modelo de projeto React com Redux incluído no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="27e8e-104">This documentation doesn't pertain to the React-with-Redux project template included in ASP.NET Core 2.0.</span></span> <span data-ttu-id="27e8e-105">Ele se refere ao modelo de reagir com Redux mais recente que você pode atualizar manualmente.</span><span class="sxs-lookup"><span data-stu-id="27e8e-105">It pertains to the newer React-with-Redux template that you can update manually.</span></span> <span data-ttu-id="27e8e-106">O modelo está disponível no ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="27e8e-106">The template is available in ASP.NET Core 2.1 or later.</span></span>

::: moniker-end

<span data-ttu-id="27e8e-107">O modelo de projeto do React com Redux atualizado fornece um ponto inicial conveniente para aplicativos do ASP.NET Core usando convenções do React, do Redux e de CRA [(create-react-app)](https://github.com/facebookincubator/create-react-app) para implementar uma IU (interface do usuário) avançada do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="27e8e-107">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="27e8e-108">Com exceção do comando de criação de projeto, todas as informações sobre o modelo React com Redux são as mesmas que aquelas sobre o modelo React.</span><span class="sxs-lookup"><span data-stu-id="27e8e-108">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="27e8e-109">Para criar esse tipo de projeto, execute `dotnet new reactredux` em vez de `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="27e8e-109">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="27e8e-110">Para obter mais informações sobre a funcionalidade comum para ambos os modelos baseados em reagir, confira a [Documentação do modelo React](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="27e8e-110">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="27e8e-111">Para obter informações sobre como configurar um aplicativo de subpropriedades React com Redux no IIS, consulte [ReactRedux modelo 2.1: Não é possível usar o SPA no IIS (aspnet/modelagem &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="27e8e-111">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
