---
title: Configurar a localização de objeto portátil no ASP.NET Core
author: sebastienros
description: Este artigo apresenta arquivos de Objeto Portátil e descreve as etapas para usá-los em um aplicativo ASP.NET Core com a estrutura Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 08002564eb68bc04eebaeafed560202d0d69958a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656187"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Configurar a localização de objeto portátil no ASP.NET Core

Por [Sébastien Ros](https://github.com/sebastienros) e [Scott Addie](https://twitter.com/Scott_Addie)

Este artigo explica as etapas para usar arquivos PO (Objeto Portátil) em um aplicativo ASP.NET Core com a estrutura [Orchard Core](https://github.com/OrchardCMS/OrchardCore).

**Observação:** o Orchard Core não é um produto da Microsoft. Consequentemente, a Microsoft não fornece suporte para esse recurso.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>O que é um arquivo PO?

Os arquivos PO são distribuídos como arquivos de texto que contém cadeias de caracteres traduzidas em determinado idioma. Algumas vantagens do uso de arquivos PO em vez de arquivos *.resx* incluem:
- Os arquivos PO dão suporte à pluralização, ao contrário dos arquivos *.resx*.
- Os arquivos PO não são compilados como os arquivos *.resx*. Dessa forma, não são necessárias ferramentas especializadas nem etapas de build.
- Os arquivos PO funcionam bem com ferramentas de colaboração de edição online.

### <a name="example"></a>{1&gt;Exemplo&lt;1}

Este é um arquivo PO de exemplo que contém a tradução de duas cadeias de caracteres em francês, incluindo uma com sua forma plural:

*fr.po*

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

Este exemplo usa a seguinte sintaxe:

- `#:`: um comentário que indica o contexto da cadeia de caracteres a ser traduzida. A mesma cadeia de caracteres pode ser traduzida de modo diferente, dependendo do local em que ela está sendo usada.
- `msgid`: a cadeia de caracteres não traduzida.
- `msgstr`: a cadeia de caracteres traduzida.

No caso de suporte à pluralização, entradas adicionais podem ser definidas.

- `msgid_plural`: a cadeia de caracteres no plural não traduzida.
- `msgstr[0]`: a cadeia de caracteres traduzida para o caso 0.
- `msgstr[N]`: a cadeia de caracteres traduzida para o caso N.

A especificação de arquivo PO pode ser encontrada [aqui](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Configurando o suporte a arquivos PO no ASP.NET Core

Este exemplo se baseia em um aplicativo ASP.NET Core MVC gerado com base em um modelo de projeto do Visual Studio 2017.

### <a name="referencing-the-package"></a>Referenciando o pacote

Adicione uma referência ao pacote NuGet `OrchardCore.Localization.Core`. Ele está disponível em [MyGet](https://www.myget.org/) na fonte do pacote a seguir: https://www.myget.org/F/orchardcore-preview/api/v3/index.json

O arquivo *.csproj* agora contém uma linha semelhante à seguinte (o número de versão pode variar):

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Registrando o serviço

Adicione os serviços necessários ao método `ConfigureServices` de *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Adicione o middleware necessário ao método `Configure` de *Startup.cs*:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Adicione o código a seguir à exibição do Razor de sua escolha. *About.cshtml* é usado neste exemplo.

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

Uma instância `IViewLocalizer` é injetada e usada para traduzir o texto “Olá, Mundo!”.

### <a name="creating-a-po-file"></a>Criando um arquivo PO

Crie um arquivo chamado *\<culture code>.po* na pasta raiz do aplicativo. Neste exemplo, o nome do arquivo é *fr.po* porque o idioma francês é usado:

[!code-text[](localization/sample/POLocalization/fr.po)]

Esse arquivo armazena a cadeia de caracteres a ser traduzida e a cadeia de caracteres traduzida do francês. As traduções são revertidas para a cultura pai, se necessário. Neste exemplo, o arquivo *fr.po* é usado se a cultura solicitada é `fr-FR` ou `fr-CA`.

### <a name="testing-the-application"></a>Testando o aplicativo

Execute o aplicativo e navegue para a URL `/Home/About`. O texto **Olá, Mundo!** seja exibido.

Navegue para a URL `/Home/About?culture=fr-FR`. O texto **Bonjour le monde!** seja exibido.

## <a name="pluralization"></a>Pluralização

Os arquivos PO dão suporte a formas de pluralização, que são úteis quando a mesma cadeia de caracteres precisa ser traduzida de modo diferente de acordo com uma cardinalidade. Essa tarefa torna-se complicada pelo fato de que cada idioma define regras personalizadas para selecionar qual cadeia de caracteres será usada de acordo com a cardinalidade.

O pacote de Localização do Orchard fornece uma API para invocar essas diferentes formas plurais automaticamente.

### <a name="creating-pluralization-po-files"></a>Criando arquivos PO de pluralização

Adicione o seguinte conteúdo ao arquivo *fr.po* mencionado anteriormente:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Consulte [O que é um arquivo PO?](#what-is-a-po-file) para obter uma explicação do que representa cada entrada neste exemplo.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Adicionando um idioma usando diferentes formas de pluralização

Cadeias de caracteres em inglês e francês foram usadas no exemplo anterior. O inglês e o francês têm apenas duas formas de pluralização e compartilham as mesmas regras de forma, o que significa que uma cardinalidade de um é mapeada para a primeira forma plural. Qualquer outra cardinalidade é mapeada para a segunda forma plural.

Nem todos os idiomas compartilham as mesmas regras. Isso é ilustrado com o idioma tcheco, que tem três formas plurais.

Crie o arquivo `cs.po` da seguinte maneira e observe como a pluralização precisa de três traduções diferentes:

[!code-text[](localization/sample/POLocalization/cs.po)]

Para aceitar localizações para o tcheco, adicione `"cs"` à lista de culturas com suporte no método `ConfigureServices`:

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

Edite o arquivo *Views/Home/About.cshtml* para renderizar cadeias de caracteres localizadas no plural para várias cardinalidades:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Observação:** em um cenário do mundo real, uma variável é usada para representar a contagem. Aqui, repetimos o mesmo código com três valores diferentes para expor um caso muito específico.

Ao mudar as culturas, o seguinte é observado:

Para `/Home/About`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Para `/Home/About?culture=fr`:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Para `/Home/About?culture=cs`:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Observe que, para a cultura do tcheco, as três traduções são diferentes. As culturas do francês e do inglês compartilham a mesma construção para as duas últimas cadeias de caracteres traduzidas.

## <a name="advanced-tasks"></a>Tarefas avançadas

### <a name="contextualizing-strings"></a>Contextualizando cadeias de caracteres

Os aplicativos costumam conter as cadeias de caracteres a serem traduzidas em vários locais. A mesma cadeia de caracteres pode ter uma tradução diferente em determinados locais em um aplicativo (exibições do Razor ou arquivos de classe). Um arquivo PO dá suporte à noção de um contexto de arquivo, que pode ser usado para categorizar a cadeia de caracteres que está sendo representada. Usando um contexto de arquivo, uma cadeia de caracteres pode ser traduzida de forma diferente, dependendo do contexto de arquivo (ou de sua ausência).

Os serviços de localização de PO usam o nome da classe completa ou da exibição que é usado ao traduzir uma cadeia de caracteres. Isso é feito definindo o valor na entrada `msgctxt`.

Considere uma adição mínima ao exemplo anterior de *fr.po*. Uma exibição do Razor localizada em *Views/Home/About.cshtml* pode ser definida com o contexto de arquivo definindo o valor da entrada `msgctxt` reservada:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Com o `msgctxt` definido assim, a tradução de texto ocorre durante a navegação para `/Home/About?culture=fr-FR`. A tradução não ocorre durante a navegação para `/Home/Contact?culture=fr-FR`.

Quando não é encontrada a correspondência de nenhuma entrada específica com um contexto de arquivo fornecido, o mecanismo de fallback do Orchard Core procura um arquivo PO apropriado sem contexto. Supondo que não haja nenhum contexto de arquivo específico definido para *Views/Home/Contact.cshtml*, a navegação para `/Home/Contact?culture=fr-FR` carrega um arquivo PO, como:

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Alterando o local dos arquivos PO

A localização padrão dos arquivos PO pode ser alterada em `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Neste exemplo, os arquivos PO são carregados da pasta *Localization*.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implementando uma lógica personalizada para encontrar arquivos de localização

Quando uma lógica mais complexa é necessária para localizar arquivos PO, a interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` pode ser implementada e registrada como um serviço. Isso é útil quando arquivos PO podem ser armazenados em locais variáveis ou quando os arquivos precisam ser encontrados em uma hierarquia de pastas.

### <a name="using-a-different-default-pluralized-language"></a>Usando um idioma pluralizado padrão diferente

O pacote inclui um método de extensão `Plural` específico a duas formas plurais. Para idiomas que exigem mais formas plurais, crie um método de extensão. Com um método de extensão, você não precisará fornecer nenhum arquivo de localização para o idioma padrão – as cadeias de caracteres originais já estão disponíveis diretamente no código.

Use a sobrecarga `Plural(int count, string[] pluralForms, params object[] arguments)` mais genérica que aceita uma matriz de cadeia de caracteres de traduções.
