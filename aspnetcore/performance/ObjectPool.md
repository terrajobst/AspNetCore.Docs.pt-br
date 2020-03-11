---
title: Reutilização de objeto com objectpool no ASP.NET Core
author: rick-anderson
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core usando o objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666106"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="cf197-103">Reutilização de objeto com objectpool no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf197-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="cf197-104">Por [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cf197-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cf197-105"><xref:Microsoft.Extensions.ObjectPool> faz parte da infraestrutura de ASP.NET Core que dá suporte à manutenção de um grupo de objetos na memória para reutilização em vez de permitir que os objetos sejam coletados como lixo.</span><span class="sxs-lookup"><span data-stu-id="cf197-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="cf197-106">Talvez você queira usar o pool de objetos se os objetos que estão sendo gerenciados forem:</span><span class="sxs-lookup"><span data-stu-id="cf197-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="cf197-107">Caro de alocar/inicializar.</span><span class="sxs-lookup"><span data-stu-id="cf197-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="cf197-108">Representam alguns recursos limitados.</span><span class="sxs-lookup"><span data-stu-id="cf197-108">Represent some limited resource.</span></span>
- <span data-ttu-id="cf197-109">Usado de forma previsível e frequente.</span><span class="sxs-lookup"><span data-stu-id="cf197-109">Used predictably and frequently.</span></span>

<span data-ttu-id="cf197-110">Por exemplo, o ASP.NET Core Framework usa o pool de objetos em alguns lugares para reutilizar <xref:System.Text.StringBuilder> instâncias.</span><span class="sxs-lookup"><span data-stu-id="cf197-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="cf197-111">`StringBuilder` aloca e gerencia seus próprios buffers para armazenar dados de caractere.</span><span class="sxs-lookup"><span data-stu-id="cf197-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="cf197-112">O ASP.NET Core usa regularmente `StringBuilder` para implementar recursos e reusá-los proporciona um benefício de desempenho.</span><span class="sxs-lookup"><span data-stu-id="cf197-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="cf197-113">O pool de objetos nem sempre melhora o desempenho:</span><span class="sxs-lookup"><span data-stu-id="cf197-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="cf197-114">A menos que o custo de inicialização de um objeto seja alto, é geralmente mais lento obter o objeto do pool.</span><span class="sxs-lookup"><span data-stu-id="cf197-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="cf197-115">Os objetos gerenciados pelo pool não são desalocados até que o pool seja desalocado.</span><span class="sxs-lookup"><span data-stu-id="cf197-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="cf197-116">Use o pool de objetos somente depois de coletar dados de desempenho usando cenários realistas para seu aplicativo ou biblioteca.</span><span class="sxs-lookup"><span data-stu-id="cf197-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="cf197-117">**Aviso: o `ObjectPool` não implementa `IDisposable`. Não é recomendável usá-lo com tipos que precisam de descarte.**</span><span class="sxs-lookup"><span data-stu-id="cf197-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="cf197-118">**Observação: o objectpool não coloca um limite no número de objetos que ele alocará, ele coloca um limite no número de objetos que será mantido.**</span><span class="sxs-lookup"><span data-stu-id="cf197-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="cf197-119">Conceitos</span><span class="sxs-lookup"><span data-stu-id="cf197-119">Concepts</span></span>

<span data-ttu-id="cf197-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-a abstração básica do pool de objetos.</span><span class="sxs-lookup"><span data-stu-id="cf197-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="cf197-121">Usado para obter e retornar objetos.</span><span class="sxs-lookup"><span data-stu-id="cf197-121">Used to get and return objects.</span></span>

<span data-ttu-id="cf197-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-implemente isso para personalizar como um objeto é criado e como ele é *redefinido* quando retornado ao pool.</span><span class="sxs-lookup"><span data-stu-id="cf197-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="cf197-123">Isso pode ser passado para um pool de objetos que você constrói diretamente... OR</span><span class="sxs-lookup"><span data-stu-id="cf197-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="cf197-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> atua como um alocador para a criação de pools de objetos.</span><span class="sxs-lookup"><span data-stu-id="cf197-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="cf197-125">O objectpool pode ser usado em um aplicativo de várias maneiras:</span><span class="sxs-lookup"><span data-stu-id="cf197-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="cf197-126">Instanciando um pool.</span><span class="sxs-lookup"><span data-stu-id="cf197-126">Instantiating a pool.</span></span>
* <span data-ttu-id="cf197-127">Registrando um pool na [injeção de dependência](xref:fundamentals/dependency-injection) (di) como uma instância.</span><span class="sxs-lookup"><span data-stu-id="cf197-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="cf197-128">Registrar o `ObjectPoolProvider<>` em DI e usá-lo como uma fábrica.</span><span class="sxs-lookup"><span data-stu-id="cf197-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="cf197-129">Como usar o objectpool</span><span class="sxs-lookup"><span data-stu-id="cf197-129">How to use ObjectPool</span></span>

<span data-ttu-id="cf197-130">Chame <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> para obter um objeto e <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> para retornar o objeto.</span><span class="sxs-lookup"><span data-stu-id="cf197-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="cf197-131">Não há nenhum requisito para que você retorne todos os objetos.</span><span class="sxs-lookup"><span data-stu-id="cf197-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="cf197-132">Se você não retornar um objeto, ele será coletado como lixo.</span><span class="sxs-lookup"><span data-stu-id="cf197-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="cf197-133">Exemplo de objectpool</span><span class="sxs-lookup"><span data-stu-id="cf197-133">ObjectPool sample</span></span>

<span data-ttu-id="cf197-134">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cf197-134">The following code:</span></span>

* <span data-ttu-id="cf197-135">Adiciona `ObjectPoolProvider` ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="cf197-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="cf197-136">Adiciona e configura `ObjectPool<StringBuilder>` para o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="cf197-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="cf197-137">Adiciona o `BirthdayMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="cf197-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="cf197-138">O código a seguir implementa `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="cf197-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
