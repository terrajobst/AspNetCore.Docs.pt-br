---
title: Auxiliares de marca de autor no ASP.NET Core
author: rick-anderson
description: Saiba como criar auxiliares de marcação no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/29/2019
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: f0c7e114583b2ca2e681c507bef3487c863d8cd0
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589867"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="e3d0e-103">Auxiliares de marca de autor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3d0e-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="e3d0e-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e3d0e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e3d0e-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e3d0e-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="e3d0e-106">Introdução aos Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="e3d0e-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="e3d0e-107">Este tutorial fornece uma introdução à programação de auxiliares de marcação.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="e3d0e-108">[Introdução aos auxiliares de marcação](intro.md) descreve os benefícios que os auxiliares de marcação fornecem.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="e3d0e-109">Um auxiliar de marca é qualquer classe que implementa a interface `ITagHelper`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="e3d0e-110">No entanto, quando cria um auxiliar de marca, você geralmente deriva de `TagHelper`, o que fornece acesso ao método `Process`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="e3d0e-111">Crie um novo projeto do ASP.NET Core chamado **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="e3d0e-112">Você não precisará de autenticação para esse projeto.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="e3d0e-113">Criar uma pasta para armazenar os auxiliares de marca chamados *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="e3d0e-114">A pasta *TagHelpers* pasta *não* é necessária, mas é uma convenção comum.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="e3d0e-115">Agora vamos começar a escrever alguns auxiliares de marca simples.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="e3d0e-116">Um auxiliar de marca mínimo</span><span class="sxs-lookup"><span data-stu-id="e3d0e-116">A minimal Tag Helper</span></span>

<span data-ttu-id="e3d0e-117">Nesta seção, você escreve um auxiliar de marca que atualiza uma marca de email.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="e3d0e-118">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="e3d0e-119">O servidor usará nosso auxiliar de marca de email para converter essa marcação como a seguir:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="e3d0e-120">Ou seja, uma marca de âncora que torna isso um link de email.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="e3d0e-121">Talvez você deseje fazer isso se estiver escrevendo um mecanismo de blog e precisar que ele envie emails para o marketing, suporte e outros contatos, todos para o mesmo domínio.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="e3d0e-122">Adicione a classe `EmailTagHelper` a seguir à pasta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="e3d0e-123">Auxiliares de marcação usam uma convenção de nomenclatura que tem como alvo os elementos do nome da classe raiz (menos o *TagHelper* parte do nome de classe).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="e3d0e-124">Neste exemplo, o nome da raiz **EmailTagHelper** é *email* e, portanto, a marca `<email>` será direcionada.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="e3d0e-125">Essa convenção de nomenclatura deve funcionar para a maioria dos auxiliares de marcação, posteriormente, mostrarei como substituí-la.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="e3d0e-126">O `EmailTagHelper` classe deriva de `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="e3d0e-127">O `TagHelper` classe fornece métodos e propriedades para gravar tag helpers.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="e3d0e-128">O método `Process` substituído controla o que o auxiliar de marca faz quando é executado.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="e3d0e-129">A classe `TagHelper` também fornece uma versão assíncrona (`ProcessAsync`) com os mesmos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="e3d0e-130">O parâmetro de contexto para `Process` (e `ProcessAsync`) contém informações associadas à execução da marca HTML atual.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="e3d0e-131">O parâmetro de saída para `Process` (e `ProcessAsync`) contém um elemento HTML com estado que representa a fonte original usada para gerar uma marca HTML e o conteúdo.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="e3d0e-132">Nosso nome de classe tem um sufixo **TagHelper**, que *não* é necessário, mas que é considerado uma convenção de melhor prática.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="e3d0e-133">Você pode declarar a classe como:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="e3d0e-134">Para disponibilizar a classe `EmailTagHelper` para todas as nossas exibições do Razor, adicione a diretiva `addTagHelper` ao arquivo *Views/_ViewImports.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="e3d0e-135">O código acima usa a sintaxe de curinga para especificar que todos os auxiliares de marca em nosso assembly estarão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="e3d0e-136">A primeira cadeia de caracteres após `@addTagHelper` especifica o auxiliar de marca a ser carregado (use "\*" para todos os auxiliares de marca) e a segunda cadeia de caracteres "AuthoringTagHelpers" especifica o assembly no qual o auxiliar de marca se encontra.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="e3d0e-137">Além disso, observe que a segunda linha traz os auxiliares de marcação MVC ASP.NET Core usando a sintaxe curinga (esses auxiliares são discutidos na [introdução aos auxiliares de marca](intro.md).) É a diretiva `@addTagHelper` que torna o auxiliar de marca disponível para a exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="e3d0e-138">Como alternativa, você pode fornecer o FQN (nome totalmente qualificado) de um auxiliar de marca, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="e3d0e-139">Para adicionar um auxiliar de marca para uma exibição usando um FQN, primeiro adicione o FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) e, em seguida, o **nome do assembly** (*AuthoringTagHelpers*, não necessariamente o `namespace`).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="e3d0e-140">A maioria dos desenvolvedores vão preferir usar a sintaxe de curinga.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="e3d0e-141">[Introdução ao tag helpers](intro.md) apresenta detalhes sobre a sintaxe de adição, remoção, hierarquia e curinga do tag helper.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="e3d0e-142">Atualize a marcação no arquivo *Views/Home/Contact.cshtml* com essas alterações:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="e3d0e-143">Execute o aplicativo e use seu navegador favorito para exibir o código-fonte HTML para verificar se as marcas de email são substituídas pela marcação de âncora (por exemplo, `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="e3d0e-144">*Suporte* e *Marketing* são renderizados como links, mas não têm um atributo `href` para torná-los funcionais.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="e3d0e-145">Corrigiremos isso na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="e3d0e-146">SetAttribute e SetContent</span><span class="sxs-lookup"><span data-stu-id="e3d0e-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="e3d0e-147">Nesta seção, atualizaremos o `EmailTagHelper` para que ele crie uma marca de âncora válida para email.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="e3d0e-148">Vamos atualizá-lo para obter informações de uma exibição do Razor (na forma de um atributo `mail-to`) e usar isso na geração da âncora.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="e3d0e-149">Atualize a classe `EmailTagHelper` com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="e3d0e-150">Nomes de classe e de propriedade na formatação Pascal Case para auxiliares de marcações são convertidos em [Kebab Case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="e3d0e-151">Portanto, para usar o atributo `MailTo`, você usará o equivalente de `<email mail-to="value"/>`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="e3d0e-152">A última linha define o conteúdo concluído para nosso tag helper minimamente funcional.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="e3d0e-153">A linha realçada mostra a sintaxe para adicionar atributos:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="e3d0e-154">Essa abordagem funciona para o atributo "href" como no momento, ele não existe na coleção de atributos.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="e3d0e-155">Você também pode usar o `output.Attributes.Add` para adicionar um atributo do tag helper ao final da coleção de atributos de marca.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="e3d0e-156">Atualize a marcação no arquivo *Views/Home/Contact.cshtml* com essas alterações:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="e3d0e-157">Execute o aplicativo e verifique se ele gera os links corretos.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="e3d0e-158">Se você pretende escrever o autofechamento da marca de email (`<email mail-to="Rick" />`), a saída final também é o autofechamento.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="e3d0e-159">Para permitir a capacidade de gravar a marca com uma marca de início (`<email mail-to="Rick">`), é necessário decorar a classe com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must decorate the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="e3d0e-160">Com um auxiliar de marca da marca de email com autofechamento, a saída será `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="e3d0e-161">As marcas de âncora com autofechamento são um HTML inválido. Portanto, não é recomendável criá-las, mas talvez criar um auxiliar de marca com autofechamento.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="e3d0e-162">Auxiliares de marca definem o tipo da propriedade `TagMode` após a leitura de uma marca.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="e3d0e-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="e3d0e-163">ProcessAsync</span></span>

<span data-ttu-id="e3d0e-164">Nesta seção, escreveremos um auxiliar de email assíncrono.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="e3d0e-165">Substitua a classe `EmailTagHelper` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="e3d0e-166">**Observações:**</span><span class="sxs-lookup"><span data-stu-id="e3d0e-166">**Notes:**</span></span>

   * <span data-ttu-id="e3d0e-167">Essa versão usa o método `ProcessAsync` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="e3d0e-168">O `GetChildContentAsync` assíncrono retorna uma `Task` que contém o `TagHelperContent`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="e3d0e-169">Use o parâmetro `output` para obter o conteúdo do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="e3d0e-170">Faça a alteração a seguir no arquivo *Views/Home/Contact.cshtml* para que o auxiliar de marca possa obter o email de destino.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="e3d0e-171">Execute o aplicativo e verifique se ele gera links de email válidos.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="e3d0e-172">RemoveAll, PreContent.SetHtmlContent e PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="e3d0e-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="e3d0e-173">Adicione a classe `BoldTagHelper` a seguir à pasta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="e3d0e-174">O atributo `[HtmlTargetElement]` passa um parâmetro de atributo que especifica que qualquer elemento HTML que contenha um atributo HTML nomeado "bold" terá uma correspondência e que o método de substituição `Process` na classe será executado.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="e3d0e-175">Em nossa amostra, o método `Process` remove o atributo "bold" e envolve a marcação contida com `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="e3d0e-176">Como você não deseja substituir a conteúdo de marca existente, você precisa escrever a marca `<strong>` de abertura com o método `PreContent.SetHtmlContent` e a marca `</strong>` de fechamento com o método `PostContent.SetHtmlContent`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="e3d0e-177">Modifique a exibição *About.cshtml* para que ela contenha um valor de atributo `bold`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="e3d0e-178">O código completo é mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-178">The completed code is shown below.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="e3d0e-179">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-179">Run the app.</span></span> <span data-ttu-id="e3d0e-180">Use seu navegador favorito para inspecionar a origem e verificar a marcação.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="e3d0e-181">O `[HtmlTargetElement]` atributo acima se destina somente a marcação HTML que fornece um nome de atributo de "bold".</span><span class="sxs-lookup"><span data-stu-id="e3d0e-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="e3d0e-182">O `<bold>` elemento não foi modificado pelo tag helper.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="e3d0e-183">Comente a linha de atributo `[HtmlTargetElement]` e ela usará como padrão as marcações `<bold>` de direcionamento, ou seja, a marcação HTML do formato `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="e3d0e-184">Lembre-se de que a convenção de nomenclatura padrão fará a correspondência do nome da classe **Bold**TagHelper com as marcações `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-184">Remember, the default naming convention will match the class name **Bold**TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="e3d0e-185">Execute o aplicativo e verifique se a marca `<bold>` é processada pelo auxiliar de marca.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="e3d0e-186">A decoração de uma classe com vários atributos `[HtmlTargetElement]` resulta em um OR lógico dos destinos.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="e3d0e-187">Por exemplo, o uso do código a seguir, uma marca de negrito ou um atributo de negrito terá uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="e3d0e-188">Quando vários atributos são adicionados à mesma instrução, o tempo de execução trata-os como um AND lógico.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="e3d0e-189">Por exemplo, no código abaixo, um elemento HTML precisa ser nomeado "bold" com um atributo nomeado "bold" (`<bold bold />`) para que haja a correspondência.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="e3d0e-190">Também use o `[HtmlTargetElement]` para alterar o nome do elemento de destino.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="e3d0e-191">Por exemplo, se você deseja que o `BoldTagHelper` seja direcionado a marcações `<MyBold>`, use o seguinte atributo:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="e3d0e-192">Passe um model para um tag helper</span><span class="sxs-lookup"><span data-stu-id="e3d0e-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="e3d0e-193">Adicionar uma pasta *models*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="e3d0e-194">Adicione a seguinte classe `WebsiteContext` à pasta *Models*:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="e3d0e-195">Adicione a classe `WebsiteInformationTagHelper` a seguir à pasta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="e3d0e-196">Conforme mencionado anteriormente, os auxiliares de marcações convertem nomes de classes e de propriedades dos auxiliares de marcações do C# na formatação Pascal Case em [Kebab Case](https://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="e3d0e-197">Portanto, para usar o `WebsiteInformationTagHelper` no Razor, você escreverá `<website-information />`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="e3d0e-198">Você não identifica de forma explícita o elemento de destino com o atributo `[HtmlTargetElement]`. Portanto, o padrão de `website-information` será o destino.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="e3d0e-199">Se você aplicou o seguinte atributo (observe que não está em kebab case, mas corresponde ao nome da classe):</span><span class="sxs-lookup"><span data-stu-id="e3d0e-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="e3d0e-200">A marcação `<website-information />` em Kebab Case não terá uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="e3d0e-201">Caso deseje usar o atributo `[HtmlTargetElement]`, use o kebab case, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="e3d0e-202">Os elementos com autofechamento não têm nenhum conteúdo.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="e3d0e-203">Para este exemplo, a marcação do Razor usará uma marca com autofechamento, mas o auxiliar de marca criará um elemento [section](https://www.w3.org/TR/html5/sections.html#the-section-element) (que não tem autofechamento e você escreve o conteúdo dentro do elemento `section`).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="e3d0e-204">Portanto, você precisa definir `TagMode` como `StartTagAndEndTag` para escrever a saída.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="e3d0e-205">Como alternativa, você pode comentar a linha definindo `TagMode` e escrever a marcação com uma marca de fechamento.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="e3d0e-206">(A marcação de exemplo é fornecida mais adiante neste tutorial.)</span><span class="sxs-lookup"><span data-stu-id="e3d0e-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="e3d0e-207">O `$` (cifrão) na seguinte linha usa uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span><span class="sxs-lookup"><span data-stu-id="e3d0e-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="e3d0e-208">Adicione a marcação a seguir à exibição *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="e3d0e-209">A marcação realçada exibe as informações do site.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-209">The highlighted markup displays the web site information.</span></span>

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="e3d0e-210">Na marcação do Razor mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="e3d0e-211">O Razor reconhece que o atributo `info` é uma classe, e não uma cadeia de caracteres, bem como que você deseja escrever o código C#.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="e3d0e-212">Qualquer atributo do auxiliar de marca que não seja uma cadeia de caracteres deve ser escrito sem o caractere `@`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="e3d0e-213">Execute o aplicativo e navegue para a exibição About sobre para ver as informações do site.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="e3d0e-214">Você pode usar a seguinte marcação com uma marca de fechamento e remova a linha com `TagMode.StartTagAndEndTag` no tag helper:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="e3d0e-215">Tag helper condicional</span><span class="sxs-lookup"><span data-stu-id="e3d0e-215">Condition Tag Helper</span></span>

<span data-ttu-id="e3d0e-216">O auxiliar de marca de condição renderiza a saída quando recebe um valor true.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="e3d0e-217">Adicione a classe `ConditionTagHelper` a seguir à pasta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="e3d0e-218">Substitua o conteúdo do arquivo *Views/Home/Index.cshtml* pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="e3d0e-219">Substitua o método `Index` no controlador `Home` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="e3d0e-220">Execute o aplicativo e navegue para a home page.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="e3d0e-221">A marcação no `div` condicional não será renderizada.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="e3d0e-222">Acrescente a cadeia de caracteres de consulta `?approved=true` à URL (por exemplo, `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="e3d0e-223">`approved` é definido como verdadeiro e a marcação condicional será exibida.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="e3d0e-224">Use o [nameof](/dotnet/csharp/language-reference/keywords/nameof) operador para especificar o atributo de destino em vez de especificar uma cadeia de caracteres como você fez com o tag helper em negrito:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="e3d0e-225">O operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) protegerá o código, caso ele seja refatorado (recomendamos alterar o nome para `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="e3d0e-226">Evitar conflitos de tag helper</span><span class="sxs-lookup"><span data-stu-id="e3d0e-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="e3d0e-227">Nesta seção, você escreve um par de tag helpers de vinculação automática.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="e3d0e-228">O primeiro substituirá a marcação que contém uma URL iniciada por HTTP para um HTML âncora marca que contém a mesma URL (e, portanto, resultando em um link de URL).</span><span class="sxs-lookup"><span data-stu-id="e3d0e-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="e3d0e-229">O segundo fará o mesmo para uma URL começando com WWW.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="e3d0e-230">Como esses dois auxiliares estão intimamente relacionados e você poderá refatorá-los no futuro, vamos mantê-los no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="e3d0e-231">Adicione a classe `AutoLinkerHttpTagHelper` a seguir à pasta *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="e3d0e-232">A classe `AutoLinkerHttpTagHelper` é direcionada a elementos `p` e usa o [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) para criar a âncora.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="e3d0e-233">Adicione a seguinte marcação ao final do arquivo *Views/Home/Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="e3d0e-234">Execute o aplicativo e verifique se o tag helper renderiza a âncora corretamente.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="e3d0e-235">Atualize a classe `AutoLinker` para incluir o `AutoLinkerWwwTagHelper` que converterá o texto www em uma marca de âncora que também contém o texto www original.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="e3d0e-236">O código atualizado é realçado abaixo:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="e3d0e-237">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-237">Run the app.</span></span> <span data-ttu-id="e3d0e-238">Observe que o texto www é renderizado como um link, ao contrário do texto HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="e3d0e-239">Se você colocar um ponto de interrupção em ambas as classes, poderá ver que a classe do auxiliar de marca HTTP é executada primeiro.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="e3d0e-240">O problema é que a saída do auxiliar de marca é armazenada em cache e quando o auxiliar de marca WWW é executado, ele substitui a saída armazenada em cache do auxiliar de marca HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="e3d0e-241">Mais adiante no tutorial, veremos como controlar a ordem na qual os auxiliares de marca são executados.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="e3d0e-242">Corrigiremos o código com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="e3d0e-243">Na primeira edição os tag helpers de vinculação automática, você obteve o conteúdo do destino com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="e3d0e-244">Ou seja, você chama `GetChildContentAsync` usando a `TagHelperOutput` passada para o método `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="e3d0e-245">Conforme mencionado anteriormente, como a saída é armazenada em cache, o último auxiliar de marca a ser executado vence.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="e3d0e-246">Você corrigiu o problema com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="e3d0e-247">O código acima verifica se o conteúdo foi modificado e, em caso afirmativo, ele obtém o conteúdo do buffer de saída.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="e3d0e-248">Execute o aplicativo e verifique se os dois links funcionam conforme esperado.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="e3d0e-249">Embora possa parecer que nosso auxiliar de marca de vinculador automático está correto e completo, ele tem um problema sutil.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="e3d0e-250">Se o auxiliar de marca WWW for executado primeiro, os links www não estarão corretos.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="e3d0e-251">Atualize o código adicionando a sobrecarga `Order` para controlar a ordem em que a marca é executada.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="e3d0e-252">A propriedade `Order` determina a ordem de execução em relação aos outros auxiliares de marca direcionados ao mesmo elemento.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="e3d0e-253">O valor de ordem padrão é zero e as instâncias com valores mais baixos são executadas primeiro.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="e3d0e-254">O código acima garante que o auxiliar de marca HTTP seja executado antes do auxiliar de marca WWW.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="e3d0e-255">Altere `Order` para `MaxValue` e verifique se a marcação gerada para a marcação WWW está incorreta.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="e3d0e-256">Inspecionar e recuperar o conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="e3d0e-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="e3d0e-257">Os tag helpers fornecem várias propriedades para recuperar o conteúdo.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="e3d0e-258">O resultado de `GetChildContentAsync` pode ser acrescentado ao `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="e3d0e-259">Inspecione o resultado de `GetChildContentAsync` com `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="e3d0e-260">Se você modificar `output.Content`, o corpo da TagHelper não será executado ou renderizado, a menos que você chame `GetChildContentAsync` como em nossa amostra de vinculador automático:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="e3d0e-261">Várias chamadas a `GetChildContentAsync` retornam o mesmo valor e não executam o corpo `TagHelper` novamente, a menos que você passe um parâmetro falso indicando para não usar o resultado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="e3d0e-262">Carregar a exibição parcial minimizada TagHelper</span><span class="sxs-lookup"><span data-stu-id="e3d0e-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="e3d0e-263">Em ambientes de produção, o desempenho pode ser melhorado com o carregamento de exibições parciais minimizadas.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="e3d0e-264">Para tirar proveito da exibição parcial minimizada em produção:</span><span class="sxs-lookup"><span data-stu-id="e3d0e-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="e3d0e-265">Crie/configure um processo de pré-compilação que minimize as exibições parciais.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="e3d0e-266">Use o seguinte código para carregar as exibições parciais minimizadas em ambientes que não sejam de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e3d0e-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
