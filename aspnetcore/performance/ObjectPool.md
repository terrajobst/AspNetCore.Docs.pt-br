---
title: Reutilização do objeto com ObjectPool no ASP.NET Core
author: rick-anderson
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core usando ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 4/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 92106d5add7dbda36e451614429baa0db420f0e8
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724830"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="55483-103">Reutilização do objeto com ObjectPool no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55483-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="55483-104">Por [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="55483-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="55483-105"><xref:Microsoft.Extensions.ObjectPool> é parte da infraestrutura do ASP.NET Core que dá suporte a manter um grupo de objetos na memória para reutilização em vez de permitir que os objetos a serem lixo coletado.</span><span class="sxs-lookup"><span data-stu-id="55483-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="55483-106">Você talvez queira usar o pool de objetos, se os objetos que estão sendo gerenciados são:</span><span class="sxs-lookup"><span data-stu-id="55483-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="55483-107">Caro para alocar/inicializar.</span><span class="sxs-lookup"><span data-stu-id="55483-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="55483-108">Representam algum recurso limitado.</span><span class="sxs-lookup"><span data-stu-id="55483-108">Represent some limited resource.</span></span>
- <span data-ttu-id="55483-109">Usados frequentemente e previsível.</span><span class="sxs-lookup"><span data-stu-id="55483-109">Used predictably and frequently.</span></span>

<span data-ttu-id="55483-110">Por exemplo, a estrutura do ASP.NET Core usa o pool de objetos em alguns locais reutilizar <xref:System.Text.StringBuilder> instâncias.</span><span class="sxs-lookup"><span data-stu-id="55483-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="55483-111">`StringBuilder` aloca e gerencia seus próprio buffers para manter os dados de caractere.</span><span class="sxs-lookup"><span data-stu-id="55483-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="55483-112">O ASP.NET Core usa regularmente `StringBuilder` para implementar recursos e reutilizá-los fornece um benefício de desempenho.</span><span class="sxs-lookup"><span data-stu-id="55483-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="55483-113">Pool de objetos sempre não melhora o desempenho:</span><span class="sxs-lookup"><span data-stu-id="55483-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="55483-114">A menos que o custo de inicialização de um objeto é alto, é geralmente mais lento obter o objeto do pool.</span><span class="sxs-lookup"><span data-stu-id="55483-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="55483-115">Não são gerenciados pelo pool de objetos desalocados até que o pool será desalocado.</span><span class="sxs-lookup"><span data-stu-id="55483-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="55483-116">Use o pool de objetos somente depois de coletar dados de desempenho usando cenários realistas para seu aplicativo ou biblioteca.</span><span class="sxs-lookup"><span data-stu-id="55483-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="55483-117">**AVISO: O `ObjectPool` não implementa `IDisposable`. Não recomendamos usá-lo com tipos que precisam de descarte.**</span><span class="sxs-lookup"><span data-stu-id="55483-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="55483-118">**OBSERVAÇÃO: O ObjectPool não coloca um limite no número de objetos que ele alocará, ele coloca um limite no número de objetos que será mantido.**</span><span class="sxs-lookup"><span data-stu-id="55483-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="55483-119">Conceitos</span><span class="sxs-lookup"><span data-stu-id="55483-119">Concepts</span></span>

<span data-ttu-id="55483-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -a abstração de pool do objeto básico.</span><span class="sxs-lookup"><span data-stu-id="55483-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="55483-121">Usado para obter e retornam objetos.</span><span class="sxs-lookup"><span data-stu-id="55483-121">Used to get and return objects.</span></span>

<span data-ttu-id="55483-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -implementar isso para personalizar como um objeto é criado e como ele é *redefinir* quando retornado ao pool.</span><span class="sxs-lookup"><span data-stu-id="55483-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="55483-123">Isso pode ser passado para um pool de objetos que você construir diretamente... OU</span><span class="sxs-lookup"><span data-stu-id="55483-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="55483-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> atua como um alocador para criar pools de objeto.</span><span class="sxs-lookup"><span data-stu-id="55483-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="55483-125">O ObjectPool pode ser usado em um aplicativo de várias maneiras:</span><span class="sxs-lookup"><span data-stu-id="55483-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="55483-126">Criando uma instância de um pool.</span><span class="sxs-lookup"><span data-stu-id="55483-126">Instantiating a pool.</span></span>
* <span data-ttu-id="55483-127">Registrar um pool no [injeção de dependência](xref:fundamentals/dependency-injection) (DI) como uma instância.</span><span class="sxs-lookup"><span data-stu-id="55483-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="55483-128">Registrando o `ObjectPoolProvider<>` na DI e usá-lo como uma fábrica.</span><span class="sxs-lookup"><span data-stu-id="55483-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="55483-129">Como usar ObjectPool</span><span class="sxs-lookup"><span data-stu-id="55483-129">How to use ObjectPool</span></span>

<span data-ttu-id="55483-130">Chame <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> para obter um objeto e <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> para retornar o objeto.</span><span class="sxs-lookup"><span data-stu-id="55483-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="55483-131">Não há nenhum requisito de que você retorne todos os objetos.</span><span class="sxs-lookup"><span data-stu-id="55483-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="55483-132">Se você não retornar um objeto, ele será coletado como lixo.</span><span class="sxs-lookup"><span data-stu-id="55483-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="55483-133">Exemplo de ObjectPool</span><span class="sxs-lookup"><span data-stu-id="55483-133">ObjectPool sample</span></span>

<span data-ttu-id="55483-134">O código a seguir:</span><span class="sxs-lookup"><span data-stu-id="55483-134">The following code:</span></span>

* <span data-ttu-id="55483-135">Adiciona `ObjectPoolProvider` para o [injeção de dependência](xref:fundamentals/dependency-injection) contêiner (DI).</span><span class="sxs-lookup"><span data-stu-id="55483-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="55483-136">Adiciona e configura `ObjectPool<StringBuilder>` para o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="55483-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="55483-137">Adiciona o `BirthdayMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="55483-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="55483-138">O código a seguir implementa `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="55483-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
