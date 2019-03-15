---
ms.openlocfilehash: 07abb12af390c0f2a50e98fc5e53545b6635f968
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665503"
---
# <a name="aspnet-core-web-api-controller-sample"></a><span data-ttu-id="98888-101">Exemplo de Controlador da API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98888-101">ASP.NET Core Web API Controller Sample</span></span>

<span data-ttu-id="98888-102">Este aplicativo de exemplo consiste nos seguintes projetos:</span><span class="sxs-lookup"><span data-stu-id="98888-102">This sample app consists of the following projects:</span></span>

- <span data-ttu-id="98888-103">**WebApiSample.Api.22**: um projeto ASP.NET Core 2.2 que tem .NET Core 2.2 como destino.</span><span class="sxs-lookup"><span data-stu-id="98888-103">**WebApiSample.Api.22**: An ASP.NET Core 2.2 project targeting .NET Core 2.2.</span></span>
- <span data-ttu-id="98888-104">**WebApiSample.Api.21**: um projeto ASP.NET Core 2.1 que tem .NET Core 2.1 como destino.</span><span class="sxs-lookup"><span data-stu-id="98888-104">**WebApiSample.Api.21**: An ASP.NET Core 2.1 project targeting .NET Core 2.1.</span></span>
- <span data-ttu-id="98888-105">**WebApiSample.Api.Pre21**: um projeto ASP.NET Core 2.0 que tem .NET Core 2.0 como destino.</span><span class="sxs-lookup"><span data-stu-id="98888-105">**WebApiSample.Api.Pre21**: An ASP.NET Core 2.0 project targeting .NET Core 2.0.</span></span>
- <span data-ttu-id="98888-106">**WebApiSample.DataAccess**: uma biblioteca de classes .NET Standard 2.0 que atua como uma camada de acesso a dados para os projetos da API Web 2.</span><span class="sxs-lookup"><span data-stu-id="98888-106">**WebApiSample.DataAccess**: A .NET Standard 2.0 class library serving as a data access tier for the 2 Web API projects.</span></span>

<span data-ttu-id="98888-107">Este exemplo mostra variações da criação do controlador da API Web:</span><span class="sxs-lookup"><span data-stu-id="98888-107">This sample illustrates variations of Web API controller creation:</span></span>

- [<span data-ttu-id="98888-108">Derivar a classe do ControllerBase</span><span class="sxs-lookup"><span data-stu-id="98888-108">Derive class from ControllerBase</span></span>](https://docs.microsoft.com/aspnet/core/web-api#derive-class-from-controllerbase)
- [<span data-ttu-id="98888-109">Anotar classe com o ApiControllerAttribute</span><span class="sxs-lookup"><span data-stu-id="98888-109">Annotate class with ApiControllerAttribute</span></span>](https://docs.microsoft.com/aspnet/core/web-api#annotate-class-with-apicontrollerattribute)
