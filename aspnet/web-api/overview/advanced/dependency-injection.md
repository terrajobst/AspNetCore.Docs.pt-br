---
uid: web-api/overview/advanced/dependency-injection
title: Injeção de dependência no ASP.NET Web API 2 | Microsoft Docs
author: MikeWasson
description: Este tutorial mostra como injetar dependências em seu controlador de API Web do ASP.NET. Versões de software usadas no tutorial Web API 2 Unity Application Block...
ms.author: riande
ms.date: 01/20/2014
ms.assetid: e3d3e7ba-87f0-4032-bdd3-31f3c1aa9d9c
msc.legacyurl: /web-api/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 318a2f1c587feb360212a390bb5de7bdc127513d
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341820"
---
<a name="dependency-injection-in-aspnet-web-api-2"></a>Injeção de dependência no ASP.NET Web API 2
====================
por [Mike Wasson](https://github.com/MikeWasson)

[Baixe o projeto concluído](http://code.msdn.microsoft.com/ASP-NET-Web-API-Tutorial-468ee148)

> Este tutorial mostra como injetar dependências em seu controlador de API Web do ASP.NET.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
> 
> 
> - API Web 2
> - [Unity Application Block](https://www.nuget.org/packages/Unity/)
> - Entity Framework 6 (versão 5 também funciona)


## <a name="what-is-dependency-injection"></a>O que é a injeção de dependência?

Uma *dependência* é qualquer objeto exigido por outro objeto. Por exemplo, é comum para definir um [repositório](http://martinfowler.com/eaaCatalog/repository.html) que manipula o acesso a dados. Vamos ilustrar um exemplo. Primeiro, vamos definir um modelo de domínio:

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

Aqui está uma classe de repositório simples que armazena itens em um banco de dados, usando o Entity Framework.

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

Agora vamos definir um controlador de API da Web que dá suporte a solicitações GET para `Product` entidades. (Estou deixando de fora POST e outros métodos para manter a simplicidade.) Aqui está uma primeira tentativa:

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

Observe que a classe de controlador depende `ProductRepository`, e podemos está permitindo que o controlador de criar o `ProductRepository` instância. No entanto, é uma boa ideia para codificar a dependência dessa forma, por vários motivos.

- Se você quiser substituir `ProductRepository` com uma implementação diferente, você também precisará modificar a classe do controlador.
- Se o `ProductRepository` tem dependências, você deve configurá-los dentro do controlador. Para um projeto grande com vários controladores, seu código de configuração se torna espalhado em seu projeto.
- É difícil para o teste de unidade, porque o controlador é embutido em código para consultar o banco de dados. Para um teste de unidade, você deve usar um repositório de simulação ou stub, que não é possível com o design de currect.

Podemos pode resolver esses problemas por *injetando* o repositório no controlador. Primeiro, refatorar o `ProductRepository` classe em uma interface:

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

Em seguida, forneça o `IProductRepository` como um parâmetro de construtor:

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

Este exemplo usa [injeção de construtor](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection). Você também pode usar *injeção de setter*, em que você definir a dependência por meio de um método de setter ou propriedade.

Mas, agora há um problema, pois seu aplicativo não cria o controlador diretamente. API da Web cria o controlador quando ele encaminha a solicitação e a API da Web não sabe nada sobre `IProductRepository`. Isso é onde entra o resolvedor de dependência de API da Web.

## <a name="the-web-api-dependency-resolver"></a>O resolvedor de dependência de API da Web

API Web define o **IDependencyResolver** interface para resolver as dependências. Aqui está a definição da interface:

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

O **IDependencyScope** interface tem dois métodos:

- **GetService** cria uma instância de um tipo.
- **GetServices** cria uma coleção de objetos de um tipo especificado.

O **IDependencyResolver** método herda **IDependencyScope** e adiciona os **BeginScope** método. Vou falar sobre escopos posteriormente no tutorial.

Quando a API da Web cria uma instância de controlador, ela primeiro chama **IDependencyResolver.GetService**, passando o tipo de controlador. Você pode usar esse gancho de extensibilidade para criar o controlador, a resolução de todas as dependências. Se **GetService** retorna null, a API da Web procura um construtor sem parâmetros na classe do controlador.

## <a name="dependency-resolution-with-the-unity-container"></a>Resolução de dependência com o contêiner do Unity

Embora você poderia escrever uma completa **IDependencyResolver** implementação do zero, a interface é realmente projetada para atuar como ponte entre a API da Web e contêineres de IoC existentes.

Um contêiner IoC é um componente de software que é responsável por gerenciar as dependências. Você registra tipos com o contêiner e, em seguida, usa o contêiner para criar objetos. O contêiner automaticamente detecta as relações de dependência. Vários contêineres IoC também permitem controlar coisas como o tempo de vida do objeto e o escopo.

> [!NOTE]
> "IoC" significa "inversão de controle", que é um padrão geral em que uma estrutura chama o código do aplicativo. Um contêiner IoC constrói seus objetos para você, o que "inverte" o fluxo normal de controle.


Para este tutorial, usaremos [Unity](https://msdn.microsoft.com/library/ff647202.aspx) da Microsoft Patterns &amp; práticas. (Outras bibliotecas populares incluem [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Ninject](http://www.ninject.org/), e [StructureMap ](http://structuremap.github.io/documentation/).) Você pode usar o Gerenciador de pacotes NuGet para instalar o Unity. Dos **ferramentas** menu no Visual Studio, selecione **Gerenciador de pacotes NuGet**, em seguida, selecione **Package Manager Console**. Na janela do Console do Gerenciador de pacotes, digite o seguinte comando:

[!code-console[Main](dependency-injection/samples/sample7.cmd)]

Aqui está uma implementação de **IDependencyResolver** que encapsula um contêiner do Unity.

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

> [!NOTE]
> Se o **GetService** método não é possível resolver um tipo, ele deverá retornar **nulo**. Se o **GetServices** método não é possível resolver um tipo, ele deverá retornar um objeto de coleção vazia. Não lançam exceções para tipos desconhecidos.


## <a name="configuring-the-dependency-resolver"></a>Configurando o resolvedor de dependência

Definir o resolvedor de dependência na **DependencyResolver** propriedade global **HttpConfiguration** objeto.

O código a seguir registra o `IProductRepository` da interface com o Unity e, em seguida, cria um `UnityResolver`.

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

## <a name="dependency-scope-and-controller-lifetime"></a>Escopo de dependência e tempo de vida do controlador

Controladores são criados por solicitação. Para gerenciar o tempo de vida do objeto, **IDependencyResolver** usa o conceito de uma *escopo*.

O resolvedor de dependência anexada para o **HttpConfiguration** objeto tem escopo global. Quando a API da Web cria um controlador, ele chama **BeginScope**. Esse método retorna um **IDependencyScope** que representa um escopo filho.

Em seguida, chama uma API Web **GetService** no escopo filho para criar o controlador. Quando a solicitação for concluída, a API da Web chama **Dispose** no escopo filho. Use o **Dispose** método descartar as dependências do controlador.

Como você implementar **BeginScope** depende do contêiner de IoC. Para o Unity, o escopo corresponde a um contêiner filho:

[!code-csharp[Main](dependency-injection/samples/sample10.cs)]

A maioria dos contêineres de IoC têm equivalentes semelhantes.
