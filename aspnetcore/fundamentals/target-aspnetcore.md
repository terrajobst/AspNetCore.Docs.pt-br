---
title: Usar ASP.NET Core APIs em uma biblioteca de classes
author: scottaddie
description: Saiba como usar ASP.NET Core APIs em uma biblioteca de classes.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 72096fc2f03033dfe8325b5129e074913a2fbd1f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658063"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Usar ASP.NET Core APIs em uma biblioteca de classes

Por [Scott Addie](https://github.com/scottaddie)

Este documento fornece diretrizes para usar ASP.NET Core APIs em uma biblioteca de classes. Para todas as outras diretrizes de biblioteca, consulte [diretrizes de biblioteca](/dotnet/standard/library-guidance/)de software livre.

## <a name="determine-which-aspnet-core-versions-to-support"></a>Determinar quais versões de ASP.NET Core para dar suporte

ASP.NET Core adere à política de [suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Consulte a política de suporte ao determinar a quais ASP.NET Core versões dar suporte em uma biblioteca. Uma biblioteca deve:

* Tome um esforço para dar suporte a todas as versões ASP.NET Core classificadas como *suporte a longo prazo* (LTS).
* Não se sinta obrigado a dar suporte a ASP.NET Core versões classificadas como *fim da vida útil* (EOL).

À medida que as versões de visualização de ASP.NET Core são disponibilizadas, as alterações significativas são postadas no repositório GitHub [ASPNET/comunicados](https://github.com/aspnet/Announcements/issues) . O teste de compatibilidade das bibliotecas pode ser conduzido conforme os recursos da estrutura estão sendo desenvolvidos.

## <a name="use-the-aspnet-core-shared-framework"></a>Usar a estrutura compartilhada ASP.NET Core

Com o lançamento do .NET Core 3,0, muitos ASP.NET Core assemblies não são mais publicados no NuGet como pacotes. Em vez disso, os assemblies são incluídos na estrutura compartilhada `Microsoft.AspNetCore.App`, que é instalada com os instaladores de SDK do .NET Core e de tempo de execução. Para obter uma lista de pacotes que não estão mais sendo publicados, consulte [remover referências de pacotes obsoletos](xref:migration/22-to-30#remove-obsolete-package-references).

A partir do .NET Core 3,0, os projetos que usam o SDK do MSBuild `Microsoft.NET.Sdk.Web` referenciam implicitamente a estrutura compartilhada. Os projetos que usam o `Microsoft.NET.Sdk` ou o SDK do `Microsoft.NET.Sdk.Razor` devem referenciar ASP.NET Core para usar as APIs do ASP.NET Core na estrutura compartilhada.

Para fazer referência a ASP.NET Core, adicione o seguinte elemento `<FrameworkReference>` ao arquivo de projeto:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

A referência a ASP.NET Core dessa maneira só tem suporte em projetos destinados ao .NET Core 3. x.

## <a name="include-blazor-extensibility"></a>Incluir extensibilidade de mais incrivelmente

O mais incrivelmente dá suporte ao Webassembly (WASM) e aos [modelos de hospedagem](xref:blazor/hosting-models)de servidor. A menos que haja um motivo específico para não, uma biblioteca de [componentes do Razor](xref:blazor/components) deve dar suporte a ambos os modelos de hospedagem. Uma biblioteca de componentes do Razor deve usar o [SDK do Microsoft. net. Sdk. Razor](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Suporte para ambos os modelos de hospedagem

Para dar suporte ao consumo de componentes do Razor nos projetos de [servidor](xref:blazor/hosting-models#blazor-server) e [WASM](xref:blazor/hosting-models#blazor-webassembly) mais podriveres, use as instruções a seguir para o seu editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use o modelo de projeto de **biblioteca de classes Razor** . A caixa de seleção páginas de suporte do modelo **e exibições** deve ser desmarcada.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o seguinte comando no terminal integrado:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Use o modelo de projeto de **biblioteca de classes Razor** .

---

O projeto gerado a partir do modelo faz o seguinte:

* Destinos .NET Standard 2,0.
* Define a propriedade `RazorLangVersion` como `3.0`. `3.0` é o valor padrão para o .NET Core 3. x.
* Adiciona as seguintes referências de pacote:
  * [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft. AspNetCore. Components. Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Suporte a um modelo de hospedagem específico

É muito menos comum oferecer suporte a um único modelo de hospedagem mais simples. Por exemplo, para dar suporte ao consumo de componentes Razor apenas de projetos de [servidor](xref:blazor/hosting-models#blazor-server) mais podestas:

* Direcione o .NET Core 3. x.
* Adicione um elemento `<FrameworkReference>` para a estrutura compartilhada.

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Para obter mais informações sobre bibliotecas que contêm componentes do Razor, consulte [ASP.NET Core bibliotecas de classes de componentes do Razor](xref:blazor/class-libraries).

## <a name="include-mvc-extensibility"></a>Incluir extensibilidade MVC

Esta seção descreve as recomendações para bibliotecas que incluem:

* [Exibições do Razor ou Razor Pages](#razor-views-or-razor-pages)
* [Auxiliares de Marcas](#tag-helpers)
* [Exibir componentes](#view-components)

Esta seção não aborda vários destinos para dar suporte a várias versões do MVC. Para obter orientação sobre como dar suporte a várias versões de ASP.NET Core, consulte [suporte para várias versões de ASP.NET Core](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Exibições do Razor ou Razor Pages

Um projeto que inclui [exibições do Razor](xref:mvc/views/overview) ou [Razor Pages](xref:razor-pages/index) deve usar o [SDK Microsoft. net. Sdk. Razor](xref:razor-pages/sdk).

Se o projeto for destinado ao .NET Core 3. x, ele exigirá:

* Uma propriedade `AddRazorSupportForMvc` do MSBuild definida como `true`.
* Um elemento `<FrameworkReference>` para a estrutura compartilhada.

O modelo de projeto de **biblioteca de classes Razor** atende aos requisitos anteriores para projetos destinados ao .NET Core 3. x. Use as instruções a seguir para o seu editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use o modelo de projeto de **biblioteca de classes Razor** . A caixa de seleção páginas de suporte do modelo **e exibições** deve ser selecionada.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o seguinte comando no terminal integrado:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Não há suporte ao modelo de projeto no momento.

---

Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Se o projeto for direcionado .NET Standard em vez disso, uma referência de pacote [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) será necessária. O pacote de `Microsoft.AspNetCore.Mvc` movido para a estrutura compartilhada no ASP.NET Core 3,0 e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Auxiliares de Marca

Um projeto que inclui [auxiliares de marca](xref:mvc/views/tag-helpers/intro) deve usar o SDK do `Microsoft.NET.Sdk`. Se estiver direcionando o .NET Core 3. x, adicione um elemento `<FrameworkReference>` para a estrutura compartilhada. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se estiver direcionando .NET Standard (para dar suporte a versões anteriores a ASP.NET Core 3. x), adicione uma referência de pacote a [Microsoft. AspNetCore. Mvc. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). O pacote de `Microsoft.AspNetCore.Mvc.Razor` movido para a estrutura compartilhada e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Componentes da exibição

Um projeto que inclui [componentes de exibição](xref:mvc/views/view-components) deve usar o SDK do `Microsoft.NET.Sdk`. Se estiver direcionando o .NET Core 3. x, adicione um elemento `<FrameworkReference>` para a estrutura compartilhada. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Se estiver direcionando .NET Standard (para dar suporte a versões anteriores a ASP.NET Core 3. x), adicione uma referência de pacote a [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). O pacote de `Microsoft.AspNetCore.Mvc.ViewFeatures` movido para a estrutura compartilhada e, portanto, não é mais publicado. Por exemplo:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Suporte a várias versões de ASP.NET Core

O direcionamento múltiplo é necessário para criar uma biblioteca que dê suporte a várias variantes de ASP.NET Core. Considere um cenário no qual uma biblioteca de auxiliares de marcas deve dar suporte às seguintes ASP.NET Core variantes:

* ASP.NET Core 2,1 direcionando .NET Framework 4.6.1
* ASP.NET Core 2. x com direcionamento para .NET Core 2. x
* ASP.NET Core 3. x com direcionamento para o .NET Core 3. x

O arquivo de projeto a seguir dá suporte a essas variantes por meio da propriedade `TargetFrameworks`:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Com o arquivo de projeto anterior:

* O pacote de `Markdig` é adicionado para todos os consumidores.
* Uma referência a [Microsoft. AspNetCore. Mvc. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) é adicionada para consumidores que visam .NET Framework 4.6.1 ou posterior ou o .NET Core 2. x. A versão 2.1.0 do pacote funciona com o ASP.NET Core 2,2 devido à compatibilidade com versões anteriores.
* A estrutura compartilhada é referenciada para os consumidores que visam o .NET Core 3. x. O pacote de `Microsoft.AspNetCore.Mvc.Razor` está incluído na estrutura compartilhada.

Como alternativa, .NET Standard 2,0 poderia ser direcionado em vez de direcionar o .NET Core 2,1 e .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Com o arquivo de projeto anterior, existem as seguintes limitações:

* Como a biblioteca contém apenas auxiliares de marca, é mais simples direcionar para as plataformas específicas em que ASP.NET Core é executado: .NET Core e .NET Framework. Os auxiliares de marca não podem ser usados por outras estruturas de destino compatíveis com .NET Standard 2,0, como Unity, UWP e Xamarin.
* Usar o .NET Standard 2.0 do .NET Framework tem alguns problemas que foram resolvidos no .NET Framework 4.7.2. Você pode melhorar a experiência dos consumidores usando o .NET Framework 4.6.1 até o 4.7.1 direcionando .NET Framework 4.6.1.

Se sua biblioteca precisar chamar APIs específicas da plataforma, direcione as implementações específicas do .NET em vez de .NET Standard. Para obter mais informações, consulte [multi-Targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Usar uma API que não foi alterada

Imagine um cenário no qual você está atualizando uma biblioteca de middleware do .NET Core 2,2 para 3,0. As APIs de middleware ASP.NET Core que estão sendo usadas na biblioteca não foram alteradas entre ASP.NET Core 2,2 e 3,0. Para continuar a oferecer suporte à biblioteca de middleware no .NET Core 3,0, execute as seguintes etapas:

* Siga as [diretrizes da biblioteca padrão](/dotnet/standard/library-guidance/).
* Adicione uma referência de pacote para cada pacote NuGet de API se o assembly correspondente não existir na estrutura compartilhada.

## <a name="use-an-api-that-changed"></a>Usar uma API que foi alterada

Imagine um cenário no qual você está atualizando uma biblioteca do .NET Core 2,2 para o .NET Core 3,0. Uma API de ASP.NET Core que está sendo usada na biblioteca tem uma [alteração significativa](/dotnet/core/compatibility/breaking-changes) no ASP.NET Core 3,0. Considere se a biblioteca pode ser reescrita para não usar a API quebrada em todas as versões.

Se você puder reescrever a biblioteca, faça-a e continue a direcionar uma estrutura de destino anterior (por exemplo, .NET Standard 2,0 ou .NET Framework 4.6.1) com referências de pacote.

Se você não puder reescrever a biblioteca, execute as seguintes etapas:

* Adicione um destino para o .NET Core 3,0.
* Adicione um elemento `<FrameworkReference>` para a estrutura compartilhada.
* Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar o código condicionalmente.

Por exemplo, leituras e gravações síncronas em fluxos de solicitação e resposta HTTP são desabilitadas por padrão a partir de ASP.NET Core 3,0. O ASP.NET Core 2,2 dá suporte ao comportamento síncrono por padrão. Considere uma biblioteca de middleware na qual leituras e gravações síncronas devem ser habilitadas onde a e/s está ocorrendo. A biblioteca deve incluir o código para habilitar recursos síncronos na diretiva de pré-processador apropriada. Por exemplo:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Usar uma API introduzida em 3,0

Imagine que você deseja usar uma API de ASP.NET Core que foi introduzida no ASP.NET Core 3,0. Considere as perguntas a seguir:

1. A biblioteca requer a nova API?
1. A biblioteca pode implementar esse recurso de forma diferente?

Se a biblioteca exigir a API e não houver como implementá-la em nível inferior:

* Somente o .NET Core 3. x de destino.
* Adicione um elemento `<FrameworkReference>` para a estrutura compartilhada.

Se a biblioteca puder implementar o recurso de uma maneira diferente:

* Adicione o .NET Core 3. x como uma estrutura de destino.
* Adicione um elemento `<FrameworkReference>` para a estrutura compartilhada.
* Use a [diretiva de pré-processador #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) com o símbolo de estrutura de destino apropriado para compilar o código condicionalmente.

Por exemplo, o auxiliar de marca a seguir usa a interface <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> introduzida no ASP.NET Core 3,0. Os consumidores que visam o .NET Core 3,0 executam o caminho de código definido pelo símbolo da estrutura de destino `NETCOREAPP3_0`. O tipo de parâmetro do construtor do auxiliar de marca muda para <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para .NET Core 2,1 e .NET Framework consumidores 4.6.1. Essa alteração foi necessária porque ASP.NET Core 3,0 marcado `IHostingEnvironment` como obsoleto e recomendado `IWebHostEnvironment` como substituição.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

O seguinte arquivo de projeto multiplataforma dá suporte a este cenário auxiliar de marca:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Usar uma API removida da estrutura compartilhada

Para usar um assembly ASP.NET Core que foi removido da estrutura compartilhada, adicione a referência de pacote apropriada. Para obter uma lista de pacotes removidos da estrutura compartilhada no ASP.NET Core 3,0, consulte [remover referências de pacote obsoletos](xref:migration/22-to-30#remove-obsolete-package-references).

Por exemplo, para adicionar o cliente de API Web:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [Suporte à implementação do .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Políticas de suporte do .NET](https://dotnet.microsoft.com/platform/support/policy)
