---
title: Reutilização do objeto com ObjectPool no ASP.NET Core
author: rick-anderson
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core usando ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815519"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Reutilização do objeto com ObjectPool no ASP.NET Core

Por [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), e [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> é parte da infraestrutura do ASP.NET Core que dá suporte a manter um grupo de objetos na memória para reutilização em vez de permitir que os objetos a serem lixo coletado.

Você talvez queira usar o pool de objetos, se os objetos que estão sendo gerenciados são:

- Caro para alocar/inicializar.
- Representam algum recurso limitado.
- Usados frequentemente e previsível.

Por exemplo, a estrutura do ASP.NET Core usa o pool de objetos em alguns locais reutilizar <xref:System.Text.StringBuilder> instâncias. `StringBuilder` aloca e gerencia seus próprio buffers para manter os dados de caractere. O ASP.NET Core usa regularmente `StringBuilder` para implementar recursos e reutilizá-los fornece um benefício de desempenho.

Pool de objetos sempre não melhora o desempenho:

- A menos que o custo de inicialização de um objeto é alto, é geralmente mais lento obter o objeto do pool.
- Não são gerenciados pelo pool de objetos desalocados até que o pool será desalocado.

Use o pool de objetos somente depois de coletar dados de desempenho usando cenários realistas para seu aplicativo ou biblioteca.

**AVISO: O `ObjectPool` não implementa `IDisposable`. Não recomendamos usá-lo com tipos que precisam de descarte.**

**OBSERVAÇÃO: O ObjectPool não coloca um limite no número de objetos que ele alocará, ele coloca um limite no número de objetos que será mantido.**

## <a name="concepts"></a>Conceitos

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -a abstração de pool do objeto básico. Usado para obter e retornam objetos.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -implementar isso para personalizar como um objeto é criado e como ele é *redefinir* quando retornado ao pool. Isso pode ser passado para um pool de objetos que você construir diretamente... OU

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> atua como um alocador para criar pools de objeto.
<!-- REview, there is no ObjectPoolProvider<T> -->

O ObjectPool pode ser usado em um aplicativo de várias maneiras:

* Criando uma instância de um pool.
* Registrar um pool no [injeção de dependência](xref:fundamentals/dependency-injection) (DI) como uma instância.
* Registrando o `ObjectPoolProvider<>` na DI e usá-lo como uma fábrica.

## <a name="how-to-use-objectpool"></a>Como usar ObjectPool

Chame <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> para obter um objeto e <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> para retornar o objeto.  Não há nenhum requisito de que você retorne todos os objetos. Se você não retornar um objeto, ele será coletado como lixo.

## <a name="objectpool-sample"></a>Exemplo de ObjectPool

O código a seguir:

* Adiciona `ObjectPoolProvider` para o [injeção de dependência](xref:fundamentals/dependency-injection) contêiner (DI).
* Adiciona e configura `ObjectPool<StringBuilder>` para o contêiner de injeção de dependência.
* Adiciona o `BirthdayMiddleware`.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

O código a seguir implementa `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
