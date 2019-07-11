---
title: Migrar do ASP.NET Core 1.x para 2.0
author: scottaddie
description: Este artigo descreve os pré-requisitos e as etapas mais comuns para a migração de um projeto ASP.NET Core 1.x para o ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/1x-to-2x/index
ms.openlocfilehash: 056930f3c586153d13555bbb6036f46587e2352d
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815095"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Migrar do ASP.NET Core 1.x para 2.0

Por [Scott Addie](https://github.com/scottaddie)

Neste artigo, vamos orientá-lo pela atualização de um projeto existente ASP.NET Core 1.x para o ASP.NET Core 2.0. A migração do aplicativo para o ASP.NET Core 2.0 permite que você aproveite [muitos novos recursos e melhorias de desempenho](xref:aspnetcore-2.0).

Os aplicativos ASP.NET Core 1.x existentes baseiam-se em modelos de projeto específicos à versão. Conforme a estrutura do ASP.NET Core evolui, os modelos do projeto e o código inicial contido neles também. Além de atualizar a estrutura ASP.NET Core, você precisa atualizar o código para o aplicativo.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos

Veja a [Introdução ao ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Atualizar TFM (Moniker da Estrutura de Destino)

Projetos direcionados ao .NET Core devem usar o [TFM](/dotnet/standard/frameworks) de uma versão maior ou igual ao .NET Core 2.0. Pesquise o nó `<TargetFramework>` no arquivo *.csproj* e substitua seu texto interno por `netcoreapp2.0`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Projetos direcionados ao .NET Framework devem usar o TFM de uma versão maior ou igual ao .NET Framework 4.6.1. Pesquise o nó `<TargetFramework>` no arquivo *.csproj* e substitua seu texto interno por `net461`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> O .NET Core 2.0 oferece uma área de superfície muito maior do que o .NET Core 1.x. Se você estiver direcionando o .NET Framework exclusivamente devido a APIs ausentes no .NET Core 1.x, o direcionamento do .NET Core 2.0 provavelmente funcionará.

Se o arquivo de projeto contiver `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, confira [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Atualizar a versão do SDK do .NET Core em global.json

Se a solução depender de um arquivo [*global.json*](/dotnet/core/tools/global-json) para direcionar uma versão específica do SDK do .NET Core, atualize sua propriedade `version` para que ela use a versão 2.0 instalada no computador:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Referências do pacote de atualização

O arquivo *.csproj* em um projeto 1.x lista cada pacote NuGet usado pelo projeto.

Em um projeto ASP.NET Core 2.0 direcionado ao .NET Core 2.0, uma única referência de [metapacote](xref:fundamentals/metapackage) no arquivo *.csproj* substitui a coleção de pacotes:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Todos os recursos do ASP.NET Core 2.0 e do Entity Framework Core 2.0 são incluídos no metapacote.

Os projetos do ASP.NET Core 2.0 direcionados ao .NET Framework devem continuar a referenciar pacotes NuGet individuais. Atualize o atributo `Version` de cada nó `<PackageReference />` para 2.0.0.

Por exemplo, esta é a lista de nós `<PackageReference />` usados em um projeto ASP.NET Core 2.0 típico direcionado ao .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Atualizar ferramentas da CLI do .NET Core

No arquivo *.csproj*, atualize o atributo `Version` de cada nó `<DotNetCliToolReference />` para 2.0.0.

Por exemplo, esta é a lista de ferramentas da CLI usadas em um projeto ASP.NET Core 2.0 típico direcionado ao .NET Core 2.0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Renomear a propriedade de Fallback de Destino do Pacote

O arquivo *.csproj* de um projeto 1.x usou um nó `PackageTargetFallback` e uma variável:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Renomeie o nó e a variável como `AssetTargetFallback`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Atualizar o método Main em Program.cs

Em projetos 1.x, o método `Main` de *Program.cs* era parecido com este:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

Em projetos 2.0, o método `Main` de *Program.cs* foi simplificado:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

A adoção desse novo padrão do 2.0 é altamente recomendada e é necessária para que os recursos de produto como as [Migrações do Entity Framework (EF) Core](xref:data/ef-mvc/migrations) funcionem. Por exemplo, a execução de `Update-Database` na janela do Console do Gerenciador de Pacotes ou de `dotnet ef database update` na linha de comando (em projetos convertidos no ASP.NET Core 2.0) gera o seguinte erro:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Adicionar provedores de configuração

Nos projetos 1.x, a adição de provedores de configuração em um aplicativo foi realizada por meio do construtor `Startup`. As etapas incluíram a criação de uma instância de `ConfigurationBuilder`, o carregamento de provedores aplicáveis (variáveis de ambiente, configurações do aplicativo, etc.) e a inicialização de um membro de `IConfigurationRoot`.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

O exemplo anterior carrega o membro `Configuration` com definições de configuração do *appsettings.json*, bem como qualquer arquivo *appsettings.\<EnvironmentName\>.json* correspondente à propriedade `IHostingEnvironment.EnvironmentName`. Estes arquivos estão localizados no mesmo caminho que *Startup.cs*.

Nos projetos 2.0, o código de configuração clichê inerente aos projetos 1.x é executado de modo oculto. Por exemplo, as variáveis de ambiente e as configurações do aplicativo são carregadas na inicialização. O código *Startup.cs* equivalente é reduzido à inicialização `IConfiguration` com a instância injetada:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Para remover os provedores padrão adicionados por `WebHostBuilder.CreateDefaultBuilder`, invoque o método `Clear` na propriedade `IConfigurationBuilder.Sources` dentro de `ConfigureAppConfiguration`. Para adicionar os provedores de volta, utilize o método `ConfigureAppConfiguration` em *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

A configuração usada pelo método `CreateDefaultBuilder` no snippet de código anterior pode ser vista [aqui](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Mover código de inicialização do banco de dados

Em projetos do 1.x usando o EF Core 1.x, um comando como `dotnet ef migrations add` faz o seguinte:

1. Cria uma instância `Startup`
1. Invoca o método `ConfigureServices` para registrar todos os serviços com injeção de dependência (incluindo tipos `DbContext`)
1. Executa suas tarefas de requisito

Em projetos do 2.0 usando o EF Core 2.0, o `Program.BuildWebHost` é chamado para obter os serviços do aplicativo. Ao contrário do 1.x, isso tem o efeito colateral adicional de chamar o `Startup.Configure`. Caso seu aplicativo do 1.x tenha chamado o código de inicialização do banco de dados no seu método `Configure`, problemas inesperados poderão ocorrer. Por exemplo, se o banco de dados não existir ainda, o código de propagação será executado antes da execução do comando EF Core Migrations. Esse problema fará com que um comando `dotnet ef migrations list` falhe se o banco de dados ainda não existir.

Considere o seguinte código de inicialização de propagação do 1.x no método `Configure` de *Startup.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

Em projetos do 2.0, mova a chamada `SeedData.Initialize` para o método `Main` do *Program.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

A partir do 2.0, é uma prática inadequada realizar qualquer ação no `BuildWebHost`, exceto compilação e configuração do host da Web. Tudo relacionado à execução do aplicativo deve ser tratado fora do `BuildWebHost` &mdash;, normalmente no método `Main` do *Program.cs*.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Examinar a configuração de compilação do Modo de Exibição do Razor

Um tempo de inicialização mais rápido do aplicativo e pacotes publicados menores são de extrema importância para você. Por esses motivos, a opção [Compilação de exibição do Razor](xref:mvc/views/view-compilation) é habilitada por padrão no ASP.NET Core 2.0.

A configuração da propriedade `MvcRazorCompileOnPublish` como verdadeiro não é mais necessária. A menos que você esteja desabilitando a compilação de exibição, a propriedade poderá ser removida do arquivo *.csproj*.

Ao direcionar o .NET Framework, você ainda precisa referenciar explicitamente o pacote NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) no arquivo *.csproj*:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Depender dos recursos “Light-Up” do Application Insights

A instalação fácil da instrumentação de desempenho do aplicativo é importante. Agora, você pode contar com os novos recursos “light-up” do [Application Insights](/azure/application-insights/app-insights-overview) disponíveis nas ferramentas do Visual Studio 2017.

Os projetos do ASP.NET Core 1.1 criados no Visual Studio 2017 adicionaram o Application Insights por padrão. Se não estiver usando o SDK do Application Insights diretamente, fora de *Program.cs* e *Startup.cs*, siga estas etapas:

1. Se você estiver direcionando ao .NET Core, remova o nó `<PackageReference />` seguinte do arquivo *.csproj*:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Se você estiver direcionando ao .NET Core, remova a invocação do método de extensão `UseApplicationInsights` de *Program.cs*:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Remova a chamada à API do lado do cliente do Application Insights de *_Layout.cshtml*. Ela inclui as duas seguintes linhas de código:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Se você estiver usando o SDK do Application Insights diretamente, continue fazendo isso. O [metapacote](xref:fundamentals/metapackage) do 2.0 inclui a última versão do Application Insights. Portanto, um erro de downgrade do pacote será exibido se você estiver referenciando uma versão mais antiga.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Adotar melhorias de autenticação/identidade

O ASP.NET Core 2.0 tem um novo modelo de autenticação e uma série de alterações significativas no ASP.NET Core Identity. Se você criar o projeto com a opção Contas de Usuário Individuais habilitada ou se adicionar manualmente a autenticação ou a identidade, consulte [Migrar a autenticação e a identidade para o ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Recursos adicionais

* [Últimas alterações no ASP.NET Core 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
