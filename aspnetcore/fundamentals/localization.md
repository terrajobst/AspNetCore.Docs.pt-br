---
title: Globalização e localização no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core fornece serviços e middleware para localização de conteúdo em diferentes idiomas e culturas.
ms.author: riande
ms.date: 01/14/2017
uid: fundamentals/localization
ms.openlocfilehash: 4e87423a02a275eae7e2f6054e7a3b6c22cd7cee
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320207"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalização e localização no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)

A criação de um site multilíngue com o ASP.NET Core permitirá que seu site alcance um público maior. O ASP.NET Core fornece serviços e middleware para localização em diferentes idiomas e culturas.

A internacionalização envolve [Globalização](/dotnet/api/system.globalization) e [Localização](/dotnet/standard/globalization-localization/localization). Globalização é o processo de criação de aplicativos que dão suporte a diferentes culturas. A globalização adiciona suporte para entrada, exibição e saída de um conjunto definido de scripts de idiomas relacionados a áreas geográficas específicas.

Localização é o processo de adaptar um aplicativo globalizado, que você já processou para possibilidade de localização, a determinada cultura/localidade. Para obter mais informações, consulte **Termos de globalização e localização** próximo ao final deste documento.

A localização de aplicativos envolve o seguinte:

1. Tornar o conteúdo do aplicativo localizável

2. Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte

3. Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Tornar o conteúdo do aplicativo localizável

Introduzidos no ASP.NET Core `IStringLocalizer` e `IStringLocalizer<T>` foram projetados para melhorar a produtividade ao desenvolver aplicativos localizados. `IStringLocalizer` usa o [ResourceManager](/dotnet/api/system.resources.resourcemanager) e o [ResourceReader](/dotnet/api/system.resources.resourcereader) para fornecer recursos específicos a uma cultura em tempo de execução. A interface simples tem um indexador e um `IEnumerable` para retornar cadeias de caracteres localizadas. `IStringLocalizer` não exige o armazenamento das cadeias de caracteres de idioma padrão em um arquivo de recurso. Você pode desenvolver um aplicativo direcionado à localização e não precisa criar arquivos de recurso no início do desenvolvimento. O código abaixo mostra como encapsular a cadeia de caracteres "About Title" para localização.

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

No código acima, a implementação `IStringLocalizer<T>` é obtida da [Injeção de Dependência](dependency-injection.md). Se o valor localizado de "About Title" não é encontrado, a chave do indexador é retornada, ou seja, a cadeia de caracteres "About Title". Deixe as cadeias de caracteres literais de idioma padrão no aplicativo e encapsule-as no localizador, de modo que você possa se concentrar no desenvolvimento do aplicativo. Você pode desenvolve seu aplicativo com o idioma padrão e prepará-lo para a etapa de localização sem primeiro criar um arquivo de recurso padrão. Como alternativa, você pode usar a abordagem tradicional e fornecer uma chave para recuperar a cadeia de caracteres de idioma padrão. Para muitos desenvolvedores, o novo fluxo de trabalho de não ter um arquivo *.resx* de idioma padrão e simplesmente encapsular os literais de cadeia de caracteres pode reduzir a sobrecarga de localizar um aplicativo. Outros desenvolvedores preferirão o fluxo de trabalho tradicional, pois ele pode facilitar o trabalho com literais de cadeia de caracteres mais longas e a atualização de cadeias de caracteres localizadas.

Use a implementação `IHtmlLocalizer<T>` para recursos que contêm HTML. O `IHtmlLocalizer` codifica em HTML os argumentos que são formatados na cadeia de caracteres de recurso, mas não codifica em HTML a cadeia de caracteres de recurso em si. Na amostra realçada abaixo, apenas o valor do parâmetro `name` é codificado em HTML.

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.

No nível mais baixo, você pode obter `IStringLocalizerFactory` com a [Injeção de Dependência](dependency-injection.md):

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

O código acima demonstra cada um dos dois métodos Create de alocador.

Você pode particionar as cadeias de caracteres localizadas por controlador, área ou ter apenas um contêiner. No aplicativo de exemplo, uma classe fictícia chamada `SharedResource` é usada para recursos compartilhados.

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

Alguns desenvolvedores usam a classe `Startup` para conter cadeias de caracteres globais ou compartilhadas. Na amostra abaixo, os localizadores `InfoController` e `SharedResource` são usados:

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localização de exibição

O serviço `IViewLocalizer` fornece cadeias de caracteres localizadas para uma [exibição](xref:mvc/views/overview). A classe `ViewLocalizer` implementa essa interface e encontra o local do recurso no caminho do arquivo de exibição. O seguinte código mostra como usar a implementação padrão de `IViewLocalizer`:

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

A implementação padrão de `IViewLocalizer` encontra o arquivo de recurso com base no nome de arquivo da exibição. Não há nenhuma opção para usar um arquivo de recurso compartilhado global. `ViewLocalizer` implementa o localizador usando `IHtmlLocalizer` e, portanto, o Razor não codifica em HTML a cadeia de caracteres localizada. Parametrize cadeias de recurso e o `IViewLocalizer` codificará em HTML os parâmetros, mas não a cadeia de caracteres de recurso. Considere a seguinte marcação do Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Um arquivo de recurso em francês pode conter o seguinte:

| Chave | Valor |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

A exibição renderizada contém a marcação HTML do arquivo de recurso.

**Observação:** em geral, recomendamos localizar somente o texto e não o HTML.

Para usar um arquivo de recurso compartilhado em uma exibição, injete `IHtmlLocalizer<T>`:

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localização de DataAnnotations

As mensagens de erro de DataAnnotations são localizadas com `IStringLocalizer<T>`. Usando a opção `ResourcesPath = "Resources"`, as mensagens de erro em `RegisterViewModel` podem ser armazenadas em um dos seguintes caminhos:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

No ASP.NET Core MVC 1.1.0 e superior, atributos que não sejam de validação são localizados. O ASP.NET Core MVC 1.0 **não** pesquisa cadeias de caracteres localizadas para atributos que não sejam de validação.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Usando uma cadeia de caracteres de recurso para várias classes

O seguinte código mostra como usar uma cadeia de caracteres de recurso para atributos de validação com várias classes:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

No código anterior, `SharedResource` é a classe correspondente ao resx em que as mensagens de validação são armazenadas. Com essa abordagem, DataAnnotations usará apenas `SharedResource`, em vez de o recurso para cada classe.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Fornecer recursos localizados para as culturas e os idiomas aos quais você dá suporte

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures e SupportedUICultures

O ASP.NET Core permite que você especifique dois valores de cultura, `SupportedCultures` e `SupportedUICultures`. O objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina os resultados das funções dependentes de cultura, como data, hora, número e formatação de moeda. `SupportedCultures` também determina a ordem de classificação de texto, convenções de uso de maiúsculas e comparações de cadeia de caracteres. Consulte [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obter mais informações sobre como o servidor obtém a Cultura. O `SupportedUICultures` determina quais cadeias de caracteres traduzidas (de arquivos *.resx*) são pesquisadas pelo [ResourceManager](/dotnet/api/system.resources.resourcemanager). O `ResourceManager` apenas pesquisa cadeias de caracteres específicas a uma cultura determinadas por `CurrentUICulture`. Cada thread no .NET tem objetos `CurrentCulture` e `CurrentUICulture`. O ASP.NET Core inspeciona esses valores durante a renderização de funções dependentes de cultura. Por exemplo, se a cultura do thread atual estiver definida como "en-US" (inglês, Estados Unidos), `DateTime.Now.ToLongDateString()` exibirá "Thursday, February 18, 2016", mas se `CurrentCulture` estiver definida como "es-ES" (espanhol, Espanha), o resultado será "jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Arquivos de recurso

Um arquivo de recurso é um mecanismo útil para separar cadeias de caracteres localizáveis do código. Cadeias de caracteres traduzidas para o idioma não padrão são arquivos de recurso *.resx* isolados. Por exemplo, talvez você queira criar um arquivo de recurso em espanhol chamado *Welcome.es.resx* contendo cadeias de caracteres traduzidas. "es" são o código de idioma para o espanhol. Para criar esse arquivo de recurso no Visual Studio:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta que conterá o arquivo de recurso > **Adicionar** > **Novo Item**.

    ![Menu contextual aninhado: no Gerenciador de Soluções, um menu contextual é aberto para Recursos. Um segundo menu contextual é aberto para Adicionar mostrando o comando Novo Item realçado.](localization/_static/newi.png)

2. Na caixa **Pesquisar modelos instalados**, insira "recurso" e nomeie o arquivo.

    ![Caixa de diálogo Adicionar Novo Item](localization/_static/res.png)

3. Insira o valor da chave (cadeia de caracteres nativa) na coluna **Nome** e a cadeia de caracteres traduzida na coluna **Valor**.

    ![Arquivo Welcome.es.resx (o arquivo de recurso Welcome em espanhol) com a palavra Hello na coluna Nome e a palavra Hola (Olá, em espanhol) na coluna valor](localization/_static/hola.png)

    O Visual Studio mostra o arquivo *Welcome.es.resx*.

    ![Gerenciador de Soluções mostrando o arquivo de recurso Welcome em espanhol (es)](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nomenclatura do arquivo de recurso

Os recursos são nomeados com o nome completo do tipo de sua classe menos o nome do assembly. Por exemplo, um recurso em francês em um projeto cujo assembly principal é `LocalizationWebsite.Web.dll` para a classe `LocalizationWebsite.Web.Startup` será nomeado *Startup.fr.resx*. Um recurso para a classe `LocalizationWebsite.Web.Controllers.HomeController` será nomeado *Controllers.HomeController.fr.resx*. Se o namespace da classe de destino não for o mesmo que o nome do assembly, você precisará do nome completo do tipo. Por exemplo, no projeto de exemplo, um recurso para o tipo `ExtraNamespace.Tools` será nomeado *ExtraNamespace.Tools.fr.resx*.

No projeto de exemplo, o método `ConfigureServices` define o `ResourcesPath` como "Resources", de modo que o caminho relativo do projeto para o arquivo de recurso em francês do controlador principal seja *Resources/Controllers.HomeController.fr.resx*. Como alternativa, você pode usar pastas para organizar arquivos de recurso. Para o controlador principal, o caminho será *Resources/Controllers/HomeController.fr.resx*. Se você não usar a opção `ResourcesPath`, o arquivo *.resx* entrará no diretório base do projeto. O arquivo de recurso para `HomeController` será nomeado *Controllers.HomeController.fr.resx*. A opção de usar a convenção de nomenclatura de ponto ou caminho depende de como você deseja organizar os arquivos de recurso.

| Nome do recurso | Nomenclatura de ponto ou caminho |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Ponto  |
| Resources/Controllers/HomeController.fr.resx  | Caminho |
|    |     |

Os arquivos de recurso que usam `@inject IViewLocalizer` em exibições do Razor seguem um padrão semelhante. O arquivo de recurso de uma exibição pode ser nomeado usando a nomenclatura de ponto ou de caminho. Os arquivos de recurso da exibição do Razor simulam o caminho de seu arquivo de exibição associado. Supondo que definimos o `ResourcesPath` como "Resources", o arquivo de recurso em francês associado à exibição *Views/Home/About.cshtml* pode ser um dos seguintes:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Se você não usar a opção `ResourcesPath`, o arquivo *.resx* de uma exibição estará localizado na mesma pasta da exibição.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

O atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) fornece o namespace raiz de um assembly quando o namespace raiz de um assembly é diferente do nome do assembly. 

Se o namespace raiz de um assembly é diferente do nome do assembly:

* A localização não funciona por padrão.
* A localização falha devido à maneira como os recursos são pesquisados dentro do assembly. `RootNamespace` é um valor de tempo de build que não está disponível para o processo em execução. 

Se o `RootNamespace` é diferente de `AssemblyName`, inclua o seguinte no *AssemblyInfo.cs* (com valores de parâmetro substituídos pelos valores reais):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

O código anterior permite a resolução bem-sucedida de arquivos resx.

## <a name="culture-fallback-behavior"></a>Comportamento de fallback da cultura

Ao procurar por um recurso, a localização participa do "fallback de cultura". Começando com a cultura solicitada, se ela não for encontrada, ela será revertida para a cultura pai dessa cultura. Além disso, a propriedade [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa a cultura pai. Isso normalmente (mas nem sempre) significa a remoção do significante do ISO. Por exemplo, o dialeto do espanhol falado no México é "es-MX". Ele tem o pai "es", ou seja, espanhol, não específico de nenhum país.

Imagine que seu site receba uma solicitação de um recurso “Boas-vindas" usando a cultura"fr-CA". O sistema de localização procura os seguintes recursos, em ordem, e seleciona a primeira correspondência:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (se o `NeutralResourcesLanguage` for "fr-CA")

Por exemplo, se você remover o designador de cultura ".fr" e tiver a cultura definida como francês, o arquivo de recurso padrão será lido e as cadeias de caracteres serão localizadas. O Gerenciador de Recursos designa um padrão ou um recurso de fallback para quando nenhuma opção atende à cultura solicitada. Se você deseja apenas retornar a chave quando um recurso está ausente para a cultura solicitada, não deve ter um arquivo de recurso padrão.

### <a name="generate-resource-files-with-visual-studio"></a>Gerar arquivos de recurso com o Visual Studio

Se você criar um arquivo de recurso no Visual Studio sem uma cultura no nome do arquivo (por exemplo, *Welcome.resx*), o Visual Studio criará uma classe do C# com uma propriedade para cada cadeia de caracteres. Geralmente isso não é o desejado com o ASP.NET Core. Normalmente você não tem um arquivo de recurso *.resx* padrão (um arquivo *.resx* sem o nome de cultura). Sugerimos que você crie o arquivo *.resx* com um nome de cultura (por exemplo *Welcome.fr.resx*). Quando você criar um arquivo *.resx* com um nome de cultura, o Visual Studio não gerará o arquivo de classe. A nossa previsão é que muitos desenvolvedores não criarão um arquivo de recurso de idioma padrão.

### <a name="add-other-cultures"></a>Adicionar outras culturas

Cada combinação de idioma e cultura (que não seja o idioma padrão) exige um arquivo de recurso exclusivo. Crie arquivos de recurso para diferentes culturas e localidades criando novos arquivos de recurso, nos quais os códigos ISO e de idioma fazem parte do nome do arquivo (por exemplo, **en-us**, **fr-ca** e **en-gb**). Esses códigos ISO são colocados entre o nome do arquivo e a extensão de arquivo *.resx*, como em *Welcome.es-MX.resx* (espanhol/México).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementar uma estratégia para selecionar o idioma e a cultura para cada solicitação

### <a name="configure-localization"></a>Configurar a localização

A localização é configurada no método `Startup.ConfigureServices`:

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Adiciona os serviços de localização ao contêiner de serviços. O código acima também define o caminho de recursos como "Resources".

* `AddViewLocalization` Adiciona suporte para os arquivos de exibição localizados. Nesta amostra, a localização de exibição se baseia no sufixo do arquivo de exibição. Por exemplo, "fr" no arquivo *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` Adiciona suporte para as mensagens de validação `DataAnnotations` localizadas por meio de abstrações `IStringLocalizer`.

### <a name="localization-middleware"></a>Middleware de localização

A cultura atual em uma solicitação é definida no [Middleware](xref:fundamentals/middleware/index) de localização. O middleware de localização é habilitado no método `Startup.Configure`. O middleware de localização precisa ser configurado antes de qualquer middleware que possa verificar a cultura de solicitação (por exemplo, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

`UseRequestLocalization` inicializa um objeto `RequestLocalizationOptions`. Em cada solicitação, a lista de `RequestCultureProvider` em `RequestLocalizationOptions` é enumerada e o primeiro provedor que pode determinar com êxito a cultura de solicitação é usado. Os provedores padrão são obtidos da classe `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

A lista padrão é apresentada do mais específico ao menos específico. Mais adiante neste artigo, veremos como você pode alterar a ordem e até mesmo adicionar um provedor de cultura personalizado. Se nenhum dos provedores pode determinar a cultura de solicitação, o `DefaultRequestCulture` é usado.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Alguns aplicativos usarão uma cadeia de caracteres de consulta para definir a [cultura e a cultura da interface do usuário](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Para aplicativos que usam a abordagem do cabeçalho Accept-Language ou do cookie, a adição de uma cadeia de caracteres de consulta à URL é útil para depurar e testar o código. Por padrão, o `QueryStringRequestCultureProvider` é registrado como o primeiro provedor de localização na lista `RequestCultureProvider`. Passe os parâmetros `culture` e `ui-culture` da cadeia de caracteres de consulta. O seguinte exemplo define a cultura específica (idioma e região) como espanhol/México:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Se você passar somente uma das duas (`culture` ou `ui-culture`), o provedor da cadeia de caracteres de consulta definirá os dois valores usando aquela que foi passada. Por exemplo, a definição apenas da cultura definirá a `Culture` e a `UICulture`:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

Em geral, aplicativos de produção fornecerão um mecanismo para definir a cultura com o cookie de cultura do ASP.NET Core. Use o método `MakeCookieValue` para criar um cookie.

O `CookieRequestCultureProvider` `DefaultCookieName` retorna o nome padrão do cookie usado para acompanhar as informações de cultura preferencial do usuário. O nome padrão do cookie é `.AspNetCore.Culture`.

O formato do cookie é `c=%LANGCODE%|uic=%LANGCODE%`, em que `c` é `Culture` e `uic` é `UICulture`, por exemplo:

    c=en-UK|uic=en-US

Se você especificar apenas as informações de cultura e a cultura da interface do usuário, a cultura especificada será usada para as informações de cultura e para a cultura da interface do usuário.

### <a name="the-accept-language-http-header"></a>O cabeçalho HTTP Accept-Language

O [cabeçalho Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) é configurável na maioria dos navegadores e originalmente foi criado para especificar o idioma do usuário. Essa configuração indica que o navegador foi definido para enviar ou herdou do sistema operacional subjacente. O cabeçalho HTTP Accept-Language de uma solicitação do navegador não é uma maneira infalível de detectar o idioma preferencial do usuário (consulte [Definindo preferências de idioma em um navegador](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Um aplicativo de produção deve incluir uma maneira para que um usuário personalize sua opção de cultura.

### <a name="set-the-accept-language-http-header-in-ie"></a>Definir o cabeçalho HTTP Accept-Language no IE

1. No ícone de engrenagem, toque em **Opções da Internet**.

2. Toque em **Idiomas**.

    ![Opções da Internet](localization/_static/lang.png)

3. Toque em **Definir Preferências de Idioma**.

4. Toque em **Adicionar um idioma**.

5. Adicione o idioma.

6. Toque no idioma e, em seguida, em **Mover Para Cima**.

### <a name="use-a-custom-provider"></a>Usar um provedor personalizado

Suponha que você deseje permitir que os clientes armazenem seus idiomas e culturas nos bancos de dados. Você pode escrever um provedor para pesquisar esses valores para o usuário. O seguinte código mostra como adicionar um provedor personalizado:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Use `RequestLocalizationOptions` para adicionar ou remover provedores de localização.

### <a name="set-the-culture-programmatically"></a>Definir a cultura de forma programática

Este projeto de exemplo **Localization.StarterWeb** no [GitHub](https://github.com/aspnet/entropy) contém a interface do usuário para definir a `Culture`. O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* permite que você selecione a cultura na lista de culturas compatíveis:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

O arquivo *Views/Shared/_SelectLanguagePartial.cshtml* é adicionado à seção `footer` do arquivo de layout para que ele fique disponível para todos os exibições:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

O método `SetLanguage` define o cookie de cultura.

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Não é possível conectar o *_SelectLanguagePartial.cshtml* ao código de exemplo para este projeto. O projeto **Localization.StarterWeb** no [GitHub](https://github.com/aspnet/entropy) contém o código para o fluxo do `RequestLocalizationOptions` para uma parcial do Razor por meio do contêiner de [Injeção de Dependência](dependency-injection.md).

## <a name="globalization-and-localization-terms"></a>Termos de globalização e localização

O processo de localização do aplicativo também exige uma compreensão básica dos conjuntos de caracteres relevantes geralmente usados no desenvolvimento moderno de software e uma compreensão dos problemas associados a eles. Embora todos os computadores armazenem texto como números (códigos), diferentes sistemas armazenam o mesmo texto usando números diferentes. O processo de localização se refere à tradução da interface do usuário do aplicativo para uma cultura/localidade específica.

[Possibilidade de localização](/dotnet/standard/globalization-localization/localizability-review) é um processo intermediário usado para verificar se um aplicativo globalizado está pronto para localização.

O formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para o nome de cultura é `<languagecode2>-<country/regioncode2>`, em que `<languagecode2>` é o código de idioma e `<country/regioncode2>` é o código de subcultura. Por exemplo, `es-CL` para Espanhol (Chile), `en-US` para inglês (Estados Unidos) e `en-AU` para inglês (Austrália). O [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) é uma combinação de um código de cultura de duas letras minúsculas ISO 639 associado a um idioma e um código de subcultura de duas letras maiúsculas ISO 3166 associado a um país ou uma região. Consulte [Nome da cultura de idioma](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Muitas vezes, internacionalização é abreviada como "I18N". A abreviação usa a primeira e última letras e o número de letras entre elas e, portanto, 18 significa o número de letras entre o primeiro "I" e o último "N". O mesmo se aplica a Globalização (G11N) e Localização (L10N).

Termos:

* Globalização (G11N): o processo de fazer com que um aplicativo dê suporte a diferentes idiomas e regiões.
* Localização (L10N): o processo de personalizar um aplicativo para determinado idioma e região.
* Internacionalização (I18N): descreve a globalização e a localização.
* Cultura: um idioma e, opcionalmente, uma região.
* Cultura neutra: uma cultura que tem um idioma especificado, mas não uma região. (por exemplo, "en", "es")
* Cultura específica: uma cultura que tem um idioma e uma região especificados. (por exemplo, "en-US", "en-GB", "es-CL")
* Cultura pai: a cultura neutra que contém uma cultura específica. (por exemplo, "en" é a cultura pai de "en-US" e "en-GB")
* Localidade: uma localidade é o mesmo que uma cultura.

[!INCLUDE[](~/includes/currency.md)]

## <a name="additional-resources"></a>Recursos adicionais

* O [projeto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado no artigo.
* [Globalizando e localizando aplicativos do .NET](/dotnet/standard/globalization-localization/index)
* [Recursos em arquivos .resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Kit de Ferramentas de Aplicativo Multilíngue da Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
