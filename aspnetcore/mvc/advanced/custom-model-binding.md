---
title: Model binding personalizado no ASP.NET Core
author: ardalis
description: Saiba como o model binding permite que as ações do controlador trabalhem diretamente com os tipos de modelo no ASP.NET Core.
ms.author: riande
ms.date: 11/13/2018
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b2fbe6a9f11315d1fb8863fbf62e8929c7ff3fc2
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186884"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="9b58c-103">Model binding personalizado no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b58c-103">Custom Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="9b58c-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9b58c-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9b58c-105">O model binding permite que as ações do controlador funcionem diretamente com tipos de modelo (passados como argumentos de método), em vez de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b58c-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="9b58c-106">O mapeamento entre os dados de solicitação de entrada e os modelos de aplicativo é manipulado por associadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="9b58c-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="9b58c-107">Os desenvolvedores podem estender a funcionalidade de model binding interna implementando associadores de modelos personalizados (embora, normalmente, você não precise escrever seu próprio provedor).</span><span class="sxs-lookup"><span data-stu-id="9b58c-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

[<span data-ttu-id="9b58c-108">Exibir ou baixar a amostra do GitHub</span><span class="sxs-lookup"><span data-stu-id="9b58c-108">View or download sample from GitHub</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a><span data-ttu-id="9b58c-109">Limitações dos associadores de modelos padrão</span><span class="sxs-lookup"><span data-stu-id="9b58c-109">Default model binder limitations</span></span>

<span data-ttu-id="9b58c-110">Os associadores de modelos padrão dão suporte à maioria dos tipos de dados comuns do .NET Core e devem atender à maior parte das necessidades dos desenvolvedores.</span><span class="sxs-lookup"><span data-stu-id="9b58c-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="9b58c-111">Eles esperam associar a entrada baseada em texto da solicitação diretamente a tipos de modelo.</span><span class="sxs-lookup"><span data-stu-id="9b58c-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="9b58c-112">Talvez seja necessário transformar a entrada antes de associá-la.</span><span class="sxs-lookup"><span data-stu-id="9b58c-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="9b58c-113">Por exemplo, quando você tem uma chave que pode ser usada para pesquisar dados de modelo.</span><span class="sxs-lookup"><span data-stu-id="9b58c-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="9b58c-114">Use um associador de modelos personalizado para buscar dados com base na chave.</span><span class="sxs-lookup"><span data-stu-id="9b58c-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="9b58c-115">Análise do model binding</span><span class="sxs-lookup"><span data-stu-id="9b58c-115">Model binding review</span></span>

<span data-ttu-id="9b58c-116">O model binding usa definições específicas para os tipos nos quais opera.</span><span class="sxs-lookup"><span data-stu-id="9b58c-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="9b58c-117">Um *tipo simples* é convertido de uma única cadeia de caracteres na entrada.</span><span class="sxs-lookup"><span data-stu-id="9b58c-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="9b58c-118">Um *tipo complexo* é convertido de vários valores de entrada.</span><span class="sxs-lookup"><span data-stu-id="9b58c-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="9b58c-119">A estrutura determina a diferença de acordo com a existência de um `TypeConverter`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="9b58c-120">Recomendamos que você crie um conversor de tipo se tiver um mapeamento `string` -> `SomeType` simples que não exige recursos externos.</span><span class="sxs-lookup"><span data-stu-id="9b58c-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="9b58c-121">Antes de criar seu próprio associador de modelos personalizado, vale a pena analisar como os associadores de modelos existentes são implementados.</span><span class="sxs-lookup"><span data-stu-id="9b58c-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="9b58c-122">Considere o [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder), que pode ser usado para converter cadeias de caracteres codificadas em Base64 em matrizes de bytes.</span><span class="sxs-lookup"><span data-stu-id="9b58c-122">Consider the [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="9b58c-123">As matrizes de bytes costumam ser armazenadas como arquivos ou campos BLOB do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9b58c-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="9b58c-124">Trabalhando com o ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="9b58c-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="9b58c-125">Cadeias de caracteres codificadas em Base64 podem ser usadas para representar dados binários.</span><span class="sxs-lookup"><span data-stu-id="9b58c-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="9b58c-126">Por exemplo, a imagem a seguir pode ser codificada como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="9b58c-126">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="9b58c-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="9b58c-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="9b58c-128">Uma pequena parte da cadeia de caracteres codificada é mostrada na seguinte imagem:</span><span class="sxs-lookup"><span data-stu-id="9b58c-128">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="9b58c-129">![dotnet bot codificado](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span><span class="sxs-lookup"><span data-stu-id="9b58c-129">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="9b58c-130">Siga as instruções do [LEIAME da amostra](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) para converter a cadeia de caracteres codificada em Base64 em um arquivo.</span><span class="sxs-lookup"><span data-stu-id="9b58c-130">Follow the instructions in the [sample's README](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="9b58c-131">O ASP.NET Core MVC pode usar uma cadeia de caracteres codificada em Base64 e usar um `ByteArrayModelBinder` para convertê-la em uma matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="9b58c-131">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="9b58c-132">O [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) que implementa [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) mapeia argumentos `byte[]` para `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="9b58c-132">The [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

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

<span data-ttu-id="9b58c-133">Ao criar seu próprio associador de modelos personalizado, você pode implementar seu próprio tipo `IModelBinderProvider` ou usar o [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span><span class="sxs-lookup"><span data-stu-id="9b58c-133">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="9b58c-134">O seguinte exemplo mostra como usar `ByteArrayModelBinder` para converter uma cadeia de caracteres codificada em Base64 em um `byte[]` e salvar o resultado em um arquivo:</span><span class="sxs-lookup"><span data-stu-id="9b58c-134">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="9b58c-135">Execute POST em uma cadeia de caracteres codificada em Base64 para esse método de API usando uma ferramenta como o [Postman](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="9b58c-135">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="9b58c-136">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="9b58c-136">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="9b58c-137">Desde que o associador possa associar dados de solicitação a propriedades ou argumentos nomeados de forma adequada, o model binding terá êxito.</span><span class="sxs-lookup"><span data-stu-id="9b58c-137">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="9b58c-138">O seguinte exemplo mostra como usar `ByteArrayModelBinder` com um modelo de exibição:</span><span class="sxs-lookup"><span data-stu-id="9b58c-138">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="9b58c-139">Amostra de associador de modelos personalizado</span><span class="sxs-lookup"><span data-stu-id="9b58c-139">Custom model binder sample</span></span>

<span data-ttu-id="9b58c-140">Nesta seção, implementaremos um associador de modelos personalizado que:</span><span class="sxs-lookup"><span data-stu-id="9b58c-140">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="9b58c-141">Converte dados de solicitação de entrada em argumentos de chave fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="9b58c-141">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="9b58c-142">Usa o Entity Framework Core para buscar a entidade associada.</span><span class="sxs-lookup"><span data-stu-id="9b58c-142">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="9b58c-143">Passa a entidade associada como um argumento para o método de ação.</span><span class="sxs-lookup"><span data-stu-id="9b58c-143">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="9b58c-144">A seguinte amostra usa o atributo `ModelBinder` no modelo `Author`:</span><span class="sxs-lookup"><span data-stu-id="9b58c-144">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="9b58c-145">No código anterior, o atributo `ModelBinder` especifica o tipo de `IModelBinder` que deve ser usado para associar parâmetros de ação `Author`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-145">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="9b58c-146">A classe `AuthorEntityBinder` a seguir associa um parâmetro `Author` efetuando fetch da entidade de uma fonte de dados usando o Entity Framework Core e um `authorId`:</span><span class="sxs-lookup"><span data-stu-id="9b58c-146">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="9b58c-147">A classe `AuthorEntityBinder` precedente é destinada a ilustrar um associador de modelos personalizado.</span><span class="sxs-lookup"><span data-stu-id="9b58c-147">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="9b58c-148">A classe não é destinada a ilustrar as melhores práticas para um cenário de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9b58c-148">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="9b58c-149">Para pesquisa, associe o `authorId` e consulte o banco de dados em um método de ação.</span><span class="sxs-lookup"><span data-stu-id="9b58c-149">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="9b58c-150">Essa abordagem separa falhas de model binding de casos de `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-150">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="9b58c-151">O seguinte código mostra como usar o `AuthorEntityBinder` em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="9b58c-151">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="9b58c-152">O atributo `ModelBinder` pode ser usado para aplicar o `AuthorEntityBinder` aos parâmetros que não usam convenções padrão:</span><span class="sxs-lookup"><span data-stu-id="9b58c-152">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="9b58c-153">Neste exemplo, como o nome do argumento não é o `authorId` padrão, ele é especificado no parâmetro com o atributo `ModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-153">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="9b58c-154">O controlador e o método de ação são simplificados em comparação com a pesquisa da entidade no método de ação.</span><span class="sxs-lookup"><span data-stu-id="9b58c-154">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="9b58c-155">A lógica para buscar o autor usando o Entity Framework Core é movida para o associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="9b58c-155">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="9b58c-156">Isso pode ser uma simplificação considerável quando há vários métodos associados ao modelo `Author`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-156">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="9b58c-157">Aplique o atributo `ModelBinder` a propriedades de modelo individuais (como em um viewmodel) ou a parâmetros de método de ação para especificar um associador de modelos ou nome de modelo específico para apenas esse tipo ou essa ação.</span><span class="sxs-lookup"><span data-stu-id="9b58c-157">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="9b58c-158">Implementando um ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="9b58c-158">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="9b58c-159">Em vez de aplicar um atributo, você pode implementar `IModelBinderProvider`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-159">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="9b58c-160">É assim que os associadores de estrutura interna são implementados.</span><span class="sxs-lookup"><span data-stu-id="9b58c-160">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="9b58c-161">Quando você especifica o tipo no qual o associador opera, você especifica o tipo de argumento que ele produz, **não** a entrada aceita pelo associador.</span><span class="sxs-lookup"><span data-stu-id="9b58c-161">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="9b58c-162">O provedor de associador a seguir funciona com o `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-162">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="9b58c-163">Quando ele for adicionado à coleção do MVC de provedores, não será necessário usar o atributo `ModelBinder` nos parâmetros `Author` ou de tipo `Author`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-163">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="9b58c-164">Observação: o código anterior retorna um `BinderTypeModelBinder`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-164">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="9b58c-165">O `BinderTypeModelBinder` atua como um alocador para associadores de modelos e fornece a DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="9b58c-165">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="9b58c-166">O `AuthorEntityBinder` exige que a DI acesse o EF Core.</span><span class="sxs-lookup"><span data-stu-id="9b58c-166">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="9b58c-167">Use `BinderTypeModelBinder` se o associador de modelos exigir serviços da DI.</span><span class="sxs-lookup"><span data-stu-id="9b58c-167">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="9b58c-168">Para usar um provedor de associador de modelos personalizado, adicione-o a `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9b58c-168">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="9b58c-169">Ao avaliar associadores de modelos, a coleção de provedores é examinada na ordem.</span><span class="sxs-lookup"><span data-stu-id="9b58c-169">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="9b58c-170">O primeiro provedor que retorna um associador é usado.</span><span class="sxs-lookup"><span data-stu-id="9b58c-170">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="9b58c-171">A imagem a seguir mostra os associadores de modelos padrão do depurador.</span><span class="sxs-lookup"><span data-stu-id="9b58c-171">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="9b58c-172">![associadores de modelo padrão](custom-model-binding/images/default-model-binders.png "default model binders")</span><span class="sxs-lookup"><span data-stu-id="9b58c-172">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="9b58c-173">A adição do provedor ao final da coleção pode resultar na chamada a um associador de modelos interno antes que o associador personalizado tenha uma oportunidade.</span><span class="sxs-lookup"><span data-stu-id="9b58c-173">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="9b58c-174">Neste exemplo, o provedor personalizado é adicionado ao início da coleção para garantir que ele é usado para argumentos de ação `Author`.</span><span class="sxs-lookup"><span data-stu-id="9b58c-174">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a><span data-ttu-id="9b58c-175">Associação de modelo polimórfico</span><span class="sxs-lookup"><span data-stu-id="9b58c-175">Polymorphic model binding</span></span>

<span data-ttu-id="9b58c-176">A associação a diferentes modelos de tipos derivados é conhecida como associação de modelo polimórfico.</span><span class="sxs-lookup"><span data-stu-id="9b58c-176">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="9b58c-177">A associação de modelo personalizado polimórfico é necessária quando o valor da solicitação deve ser associado ao tipo de modelo derivado específico.</span><span class="sxs-lookup"><span data-stu-id="9b58c-177">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="9b58c-178">Associação de modelo polimórfico:</span><span class="sxs-lookup"><span data-stu-id="9b58c-178">Polymorphic model binding:</span></span>

* <span data-ttu-id="9b58c-179">Não é comum para uma API REST projetada para interoperar com todos os idiomas.</span><span class="sxs-lookup"><span data-stu-id="9b58c-179">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="9b58c-180">Dificulta a razão dos modelos associados.</span><span class="sxs-lookup"><span data-stu-id="9b58c-180">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="9b58c-181">No entanto, se um aplicativo exigir Associação de modelo polimórfico, uma implementação poderá ser semelhante ao seguinte código:</span><span class="sxs-lookup"><span data-stu-id="9b58c-181">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="9b58c-182">Recomendações e melhores práticas</span><span class="sxs-lookup"><span data-stu-id="9b58c-182">Recommendations and best practices</span></span>

<span data-ttu-id="9b58c-183">Associadores de modelos personalizados:</span><span class="sxs-lookup"><span data-stu-id="9b58c-183">Custom model binders:</span></span>

- <span data-ttu-id="9b58c-184">Não devem tentar definir códigos de status ou retornar resultados (por exemplo, 404 Não Encontrado).</span><span class="sxs-lookup"><span data-stu-id="9b58c-184">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="9b58c-185">Se o model binding falhar, um [filtro de ação](xref:mvc/controllers/filters) ou uma lógica no próprio método de ação deverá resolver a falha.</span><span class="sxs-lookup"><span data-stu-id="9b58c-185">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="9b58c-186">São muito úteis para eliminar código repetitivo e interesses paralelos de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="9b58c-186">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="9b58c-187">Normalmente, não devem ser usados para converter uma cadeia de caracteres em um tipo personalizado; um [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) geralmente é uma opção melhor.</span><span class="sxs-lookup"><span data-stu-id="9b58c-187">Typically shouldn't be used to convert a string into a custom type, a [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
