---
title: Comece com as APIs de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba como usar as APIs de proteção de dados do ASP.NET Core para proteger e Desproteger dados em um aplicativo.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 25bf099a3d9edd7e6e0872725cbc3707750314e6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087633"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="7ffaf-103">Comece com as APIs de proteção de dados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ffaf-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="7ffaf-104">Em seus dados mais simples, protegendo consiste as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="7ffaf-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="7ffaf-105">Crie dados de um protetor de um provedor de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="7ffaf-106">Chamar o `Protect` método com os dados que você deseja proteger.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="7ffaf-107">Chamar o `Unprotect` método com os dados que você deseja transformar de volta em texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="7ffaf-108">A maioria das estruturas e modelos de aplicativo, como o ASP.NET Core ou SignalR, já que configurar o sistema de proteção de dados e adicioná-lo a um contêiner de serviço que você acessa por meio da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="7ffaf-109">O exemplo a seguir demonstra Configurando um contêiner de serviço para injeção de dependência e registrando a pilha da proteção de dados, recebendo o provedor de proteção de dados por meio da DI, criando um protetor e desproteção em seguida, proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="7ffaf-110">Quando você cria um protetor deve fornecer um ou mais [cadeias de caracteres de finalidade](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="7ffaf-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="7ffaf-111">Uma cadeia de caracteres de finalidade fornece isolamento entre os consumidores.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="7ffaf-112">Por exemplo, um protetor criado com uma cadeia de caracteres de finalidade de "verde" seria capaz de Desproteger dados fornecidos por um protetor, com a finalidade de "roxo".</span><span class="sxs-lookup"><span data-stu-id="7ffaf-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="7ffaf-113">Instâncias do `IDataProtectionProvider` e `IDataProtector` sejam thread-safe para chamadores vários.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="7ffaf-114">Ele deve que depois que um componente obtém uma referência a um `IDataProtector` por meio de uma chamada para `CreateProtector`, ele usará essa referência para várias chamadas para `Protect` e `Unprotect`.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="7ffaf-115">Uma chamada para `Unprotect` lançará CryptographicException se o conteúdo protegido não pode ser verificado ou decifrado.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="7ffaf-116">Alguns componentes talvez queira ignorar erros durante a Desproteger operações; um componente que lê os cookies de autenticação pode manipular esse erro e tratar a solicitação como se ele não tinha nenhum cookie em todos os em vez de falhar a solicitação imediatamente.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="7ffaf-117">Componentes que desejam esse comportamento devem especificamente capturar CryptographicException em vez de assimilação de todas as exceções.</span><span class="sxs-lookup"><span data-stu-id="7ffaf-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
