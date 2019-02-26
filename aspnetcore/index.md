---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: 'Obtenha uma introdução ao ASP.NET Core, uma estrutura de software livre, plataforma cruzada e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem.'
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: index
---
# <a name="introduction-to-aspnet-core"></a>Introdução ao ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Shaun Luttin](https://twitter.com/dicshaunary)

O ASP.NET Core é uma estrutura de [software livre](https://github.com/aspnet/home), de multiplaforma e alto desempenho para a criação de aplicativos modernos conectados à Internet e baseados em nuvem. Com o ASP.NET Core, você pode:

* Compilar aplicativos e serviços Web, aplicativos [IoT](https://www.microsoft.com/internet-of-things/) e back-ends móveis.
* Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.
* Implantar na nuvem ou local.
* Executar no [.NET Core ou no .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-use-aspnet-core"></a>Por que usar o ASP.NET Core?

Milhões de desenvolvedores usaram (e continuam usando) o [ASP.NET 4.x](/aspnet/overview) para criar aplicativos Web. O ASP.NET Core é uma reformulação do ASP.NET 4.x, com alterações de arquitetura que resultam em uma estrutura mais enxuta e modular.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC

O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):

* O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.
* O [Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna mais fácil e produtiva a criação da interface do usuário da Web.
* A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).
* Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.
* O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.
* O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.
* A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.

## <a name="client-side-development"></a>Desenvolvimento do lado do cliente

O ASP.NET Core integra-se perfeitamente a estruturas e bibliotecas conhecidas do lado do cliente, incluindo [Componentes Razor](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/). Para obter mais informações, consulte [Componentes Razor](xref:razor-components/index) e os tópicos relacionados em *Desenvolvimento do lado do cliente*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>ASP.NET Core direcionado para o .NET Framework

O ASP.NET Core 2.x pode ser direcionado para o .NET Core ou ao .NET Framework. Os aplicativos do ASP.NET Core direcionados ao .NET Framework não são multiplataforma,&mdash; são executados somente no Windows. Em geral, o ASP.NET Core 2.x é composto de bibliotecas do [.NET Standard](/dotnet/standard/net-standard). Aplicativos criados com o .NET Standard 2.0 são executados em qualquer lugar com suporte para ele.

O ASP.NET Core 2.x dá suporte para as versões do .NET Framework compatíveis com o .NET Standard 2.0:

* O .NET Framework 4.7.1 e versões posteriores são fortemente recomendados.
* .NET Framework 4.6.1 e versões posteriores.

O ASP.NET Core 3.0 e posterior somente executará no .NET Core. Para obter mais detalhes sobre essa alteração, confira [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Uma primeira análise das alterações no ASP.NET Core 3.0).

Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão. Algumas vantagens do .NET Core em relação ao .NET Framework incluem:

* Multiplataforma. É executado no Windows, no Linux e no macOS.
* Desempenho aprimorado
* Controle de versão lado a lado
* Novas APIs
* Código Aberto

Estamos trabalhando duro para diminuir a diferença de API entre o .NET Framework e o .NET Core. O [Pacote de Compatibilidade do Windows](/dotnet/core/porting/windows-compat-pack) disponibilizou milhares de APIs exclusivas do Windows no .NET Core. Essas APIs não estavam disponíveis no .NET Core 1.x.

## <a name="recommended-learning-path"></a>Caminho de aprendizado recomendado

Recomendamos a seguinte sequência de tutoriais e artigos para obter uma introdução ao desenvolvimento de aplicativos ASP.NET Core:

1. Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter:

   |Tipo de aplicativo  |Cenário  |Tutorial  |
   |----------|----------|----------|
   |Aplicativo Web       | Para novo desenvolvimento        |[Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplicativo Web       | Para manter um aplicativo MVC |[Introdução ao MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |API Web       |                            |[Criar uma API Web](xref:tutorials/first-web-api)\*  |
   |Aplicativo em tempo real |                            |[Introdução ao SignalR](xref:tutorials/signalr) |

1. Siga um tutorial que mostra como fazer o acesso a dados básicos:

   |Cenário  |Tutorial  |
   |----------|----------|
   | Para novo desenvolvimento        |[Razor Pages com o Entity Framework Core](xref:data/ef-rp/intro) |
   | Para manter um aplicativo MVC |[MVC com o Entity Framework Core](xref:data/ef-mvc/intro)

1. Leia uma visão geral dos recursos do ASP.NET Core que se aplicam a todos os tipos de aplicativo:

   * [Conceitos básicos](xref:fundamentals/index)

1. Pesquise no Sumário outros tópicos de interesse.

\* Há um novo [tutorial de API Web que você segue totalmente no navegador](https://docs.microsoft.com/learn/modules/build-web-api-net-core), sem necessidade de instalação local do IDE.  O código é executado em um [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e [curl](https://curl.haxx.se/) é usado para teste.

## <a name="how-to-download-a-sample"></a>Como baixar uma amostra

Muitos dos artigos e tutoriais incluem links para exemplos de código.

1. [Baixe o arquivo zip do repositório ASP.NET](https://codeload.github.com/aspnet/Docs/zip/master).
1. Descompacte o arquivo *Docs-master.zip*.
1. Use a URL no link de exemplo para ajudá-lo a navegar até o diretório de exemplo.

### <a name="preprocessor-directives-in-sample-code"></a>Diretivas do pré-processador no código de exemplo

Para demonstrar vários cenários, os aplicativos de exemplo usam as instruções C# `#define` e `#if-#else/#elif-#endif` para compilar e executar diferentes seções de código de exemplo de forma seletiva. Para esses exemplos que usam essa abordagem, defina a instrução `#define` na parte superior dos arquivos C# para o símbolo associado ao cenário que deseja executar. Alguns exemplos podem exigir que você defina o símbolo na parte superior de vários arquivos para executar um cenário.

Por exemplo, a seguinte lista de símbolo `#define` indica que quatro cenários estão disponíveis (um cenário por símbolo). A configuração da amostra atual executa o cenário `TemplateCode`:

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Para alterar a amostra que executará o cenário `ExpandDefault`, defina o símbolo `ExpandDefault` e deixe os símbolos restantes comentados de fora:

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Para obter mais informações sobre como usar [diretivas de pré-processador C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar seletivamente as seções de código, consulte [#define (Referência C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referência C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Regiões no código de exemplo

Alguns aplicativos de exemplo contém seções de código cercadas pelas instruções C# [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion). O sistema de build de documentação injeta essas regiões nos tópicos renderizados da documentação.  

Os nomes das regiões geralmente contêm a palavra "snippet". O exemplo a seguir mostra uma região chamada `snippet_FilterInCode`:

```csharp
#region snippet_FilterInCode
WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter("System", LogLevel.Debug)
            .AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Trace))
            .Build();
#endregion
```

O snippet de código C# precedente é referenciado no arquivo de markdown do tópico com a seguinte linha:

```
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_FilterInCode)]
```

Você pode ignorar (ou remover) com segurança as instruções `#region` e `#endregion` que envolvem o código. Não altere o código dentro dessas instruções se você planeja executar os cenários de exemplo descritos no tópico. Fique à vontade para alterar o código ao experimentar com outros cenários.

Para obter mais informações, veja [Contribuir para a documentação do ASP.NET: snippets de código](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Conceitos básicos do ASP.NET Core](xref:fundamentals/index)
* O [Community Standup semanal do ASP.NET](https://live.asp.net/) aborda o progresso e os planos da equipe. Ele apresenta o novo software de terceiros e blogs.
