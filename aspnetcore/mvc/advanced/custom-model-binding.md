---
title: Model binding personalizado no ASP.NET Core
author: ardalis
description: Saiba como o model binding permite que as ações do controlador trabalhem diretamente com os tipos de modelo no ASP.NET Core.
ms.author: riande
ms.date: 11/13/2018
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 91f42393ffee3249f9167e10eaea7b279a7cb70b
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878403"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Model binding personalizado no ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

O model binding permite que as ações do controlador funcionem diretamente com tipos de modelo (passados como argumentos de método), em vez de solicitações HTTP. O mapeamento entre os dados de solicitação de entrada e os modelos de aplicativo é manipulado por associadores de modelos. Os desenvolvedores podem estender a funcionalidade de model binding interna implementando associadores de modelos personalizados (embora, normalmente, você não precise escrever seu próprio provedor).

[Exibir ou baixar a amostra do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a>Limitações dos associadores de modelos padrão

Os associadores de modelos padrão dão suporte à maioria dos tipos de dados comuns do .NET Core e devem atender à maior parte das necessidades dos desenvolvedores. Eles esperam associar a entrada baseada em texto da solicitação diretamente a tipos de modelo. Talvez seja necessário transformar a entrada antes de associá-la. Por exemplo, quando você tem uma chave que pode ser usada para pesquisar dados de modelo. Use um associador de modelos personalizado para buscar dados com base na chave.

## <a name="model-binding-review"></a>Análise do model binding

O model binding usa definições específicas para os tipos nos quais opera. Um *tipo simples* é convertido de uma única cadeia de caracteres na entrada. Um *tipo complexo* é convertido de vários valores de entrada. A estrutura determina a diferença de acordo com a existência de um `TypeConverter`. Recomendamos que você crie um conversor de tipo se tiver um mapeamento `string` -> `SomeType` simples que não exige recursos externos.

Antes de criar seu próprio associador de modelos personalizado, vale a pena analisar como os associadores de modelos existentes são implementados. Considere o [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder), que pode ser usado para converter cadeias de caracteres codificadas em Base64 em matrizes de bytes. As matrizes de bytes costumam ser armazenadas como arquivos ou campos BLOB do banco de dados.

### <a name="working-with-the-bytearraymodelbinder"></a>Trabalhando com o ByteArrayModelBinder

Cadeias de caracteres codificadas em Base64 podem ser usadas para representar dados binários. Por exemplo, a imagem a seguir pode ser codificada como uma cadeia de caracteres.

![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")

Uma pequena parte da cadeia de caracteres codificada é mostrada na seguinte imagem:

![dotnet bot codificado](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")

Siga as instruções do [LEIAME da amostra](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) para converter a cadeia de caracteres codificada em Base64 em um arquivo.

O ASP.NET Core MVC pode usar uma cadeia de caracteres codificada em Base64 e usar um `ByteArrayModelBinder` para convertê-la em uma matriz de bytes. O [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) que implementa [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) mapeia argumentos `byte[]` para `ByteArrayModelBinder`:

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

Ao criar seu próprio associador de modelos personalizado, você pode implementar seu próprio tipo `IModelBinderProvider` ou usar o [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).

O seguinte exemplo mostra como usar `ByteArrayModelBinder` para converter uma cadeia de caracteres codificada em Base64 em um `byte[]` e salvar o resultado em um arquivo:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

Execute POST em uma cadeia de caracteres codificada em Base64 para esse método de API usando uma ferramenta como o [Postman](https://www.getpostman.com/):

![postman](custom-model-binding/images/postman.png "postman")

Desde que o associador possa associar dados de solicitação a propriedades ou argumentos nomeados de forma adequada, o model binding terá êxito. O seguinte exemplo mostra como usar `ByteArrayModelBinder` com um modelo de exibição:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Amostra de associador de modelos personalizado

Nesta seção, implementaremos um associador de modelos personalizado que:

- Converte dados de solicitação de entrada em argumentos de chave fortemente tipados.
- Usa o Entity Framework Core para buscar a entidade associada.
- Passa a entidade associada como um argumento para o método de ação.

A seguinte amostra usa o atributo `ModelBinder` no modelo `Author`:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

No código anterior, o atributo `ModelBinder` especifica o tipo de `IModelBinder` que deve ser usado para associar parâmetros de ação `Author`.

A classe `AuthorEntityBinder` a seguir associa um parâmetro `Author` efetuando fetch da entidade de uma fonte de dados usando o Entity Framework Core e um `authorId`:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> A classe `AuthorEntityBinder` precedente é destinada a ilustrar um associador de modelos personalizado. A classe não é destinada a ilustrar as melhores práticas para um cenário de pesquisa. Para pesquisa, associe o `authorId` e consulte o banco de dados em um método de ação. Essa abordagem separa falhas de model binding de casos de `NotFound`.

O seguinte código mostra como usar o `AuthorEntityBinder` em um método de ação:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

O atributo `ModelBinder` pode ser usado para aplicar o `AuthorEntityBinder` aos parâmetros que não usam convenções padrão:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

Neste exemplo, como o nome do argumento não é o `authorId` padrão, ele é especificado no parâmetro com o atributo `ModelBinder`. O controlador e o método de ação são simplificados em comparação com a pesquisa da entidade no método de ação. A lógica para buscar o autor usando o Entity Framework Core é movida para o associador de modelos. Isso pode ser uma simplificação considerável quando há vários métodos associados ao modelo `Author`.

Aplique o atributo `ModelBinder` a propriedades de modelo individuais (como em um viewmodel) ou a parâmetros de método de ação para especificar um associador de modelos ou nome de modelo específico para apenas esse tipo ou essa ação.

### <a name="implementing-a-modelbinderprovider"></a>Implementando um ModelBinderProvider

Em vez de aplicar um atributo, você pode implementar `IModelBinderProvider`. É assim que os associadores de estrutura interna são implementados. Quando você especifica o tipo no qual o associador opera, você especifica o tipo de argumento que ele produz, **não** a entrada aceita pelo associador. O provedor de associador a seguir funciona com o `AuthorEntityBinder`. Quando ele for adicionado à coleção do MVC de provedores, não será necessário usar o atributo `ModelBinder` nos parâmetros `Author` ou de tipo `Author`.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Observação: o código anterior retorna um `BinderTypeModelBinder`. O `BinderTypeModelBinder` atua como um alocador para associadores de modelos e fornece a DI (injeção de dependência). O `AuthorEntityBinder` exige que a DI acesse o EF Core. Use `BinderTypeModelBinder` se o associador de modelos exigir serviços da DI.

Para usar um provedor de associador de modelos personalizado, adicione-o a `ConfigureServices`:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

Ao avaliar associadores de modelos, a coleção de provedores é examinada na ordem. O primeiro provedor que retorna um associador é usado.

A imagem a seguir mostra os associadores de modelos padrão do depurador.

![associadores de modelo padrão](custom-model-binding/images/default-model-binders.png "default model binders")

A adição do provedor ao final da coleção pode resultar na chamada a um associador de modelos interno antes que o associador personalizado tenha uma oportunidade. Neste exemplo, o provedor personalizado é adicionado ao início da coleção para garantir que ele é usado para argumentos de ação `Author`.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a>Associação de modelo polimórfico

A associação a diferentes modelos de tipos derivados é conhecida como associação de modelo polimórfico. A associação de modelo personalizado é necessária quando o valor da solicitação deve ser associado ao tipo de modelo derivado específico. A menos que essa abordagem seja necessária, é recomendável evitar a associação de modelo polimórfico. A associação de modelo polimórfico dificulta o raciocínio sobre os modelos associados. No entanto, se um aplicativo exigir Associação de modelo polimórfico, uma implementação poderá ser semelhante ao seguinte código:

A associação a diferentes modelos de tipos derivados é conhecida como associação de modelo polimórfico. A associação de modelo personalizado é necessária quando o valor da solicitação deve ser associado ao tipo de modelo derivado específico. Associação de modelo polimórfico:

* Não é comum para uma API REST projetada para interoperar com todos os idiomas.
* Dificulta a razão dos modelos associados.

No entanto, se um aplicativo exigir Associação de modelo polimórfico, uma implementação poderá ser semelhante ao seguinte código:

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Recomendações e melhores práticas

Associadores de modelos personalizados:

- Não devem tentar definir códigos de status ou retornar resultados (por exemplo, 404 Não Encontrado). Se o model binding falhar, um [filtro de ação](xref:mvc/controllers/filters) ou uma lógica no próprio método de ação deverá resolver a falha.
- São muito úteis para eliminar código repetitivo e interesses paralelos de métodos de ação.
- Normalmente, não devem ser usados para converter uma cadeia de caracteres em um tipo personalizado; um [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) geralmente é uma opção melhor.
