---
title: Auxiliar de Marca de Ambiente no ASP.NET Core
author: pkellner
description: Definição de Auxiliar de Marca de Ambiente do ASP.NET Core, incluindo todas as propriedades
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 308e7db47104ebd4d6bb8d08c64f14bbd118898b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663985"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="95fbd-103">Auxiliar de Marca de Ambiente no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95fbd-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="95fbd-104">Por [Peter Kellner](https://peterkellner.net) e [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="95fbd-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="95fbd-105">O Auxiliar de Marca de Ambiente renderiza condicionalmente seu conteúdo contido com base no [ambiente de hospedagem](xref:fundamentals/environments) atual.</span><span class="sxs-lookup"><span data-stu-id="95fbd-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="95fbd-106">Atributo único do Auxiliar de Marca de Ambiente, `names`, é uma lista separada por vírgulas de nomes de ambiente.</span><span class="sxs-lookup"><span data-stu-id="95fbd-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="95fbd-107">Se nenhum dos nomes de ambiente fornecido corresponder ao ambiente atual, o conteúdo contido será renderizado.</span><span class="sxs-lookup"><span data-stu-id="95fbd-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="95fbd-108">Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="95fbd-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="95fbd-109">Atributos do Auxiliar de Marca de Ambiente</span><span class="sxs-lookup"><span data-stu-id="95fbd-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="95fbd-110">nomes</span><span class="sxs-lookup"><span data-stu-id="95fbd-110">names</span></span>

<span data-ttu-id="95fbd-111">`names` aceita um único nome de ambiente de hospedagem ou uma lista separada por vírgula de nomes de ambiente de hospedagem que disparam a renderização do conteúdo contido.</span><span class="sxs-lookup"><span data-stu-id="95fbd-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="95fbd-112">Valores de ambiente são comparados com o valor retornado por [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="95fbd-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="95fbd-113">A comparação ignora o uso de maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="95fbd-113">The comparison ignores case.</span></span>

<span data-ttu-id="95fbd-114">O exemplo a seguir usa um Auxiliar de Marca de Ambiente.</span><span class="sxs-lookup"><span data-stu-id="95fbd-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="95fbd-115">O conteúdo será renderizado se o ambiente de hospedagem for De Preparo ou de Produção:</span><span class="sxs-lookup"><span data-stu-id="95fbd-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="95fbd-116">incluir e excluir atributos</span><span class="sxs-lookup"><span data-stu-id="95fbd-116">include and exclude attributes</span></span>

<span data-ttu-id="95fbd-117">`include` & `exclude` o controle de atributos que processam o conteúdo incluído com base nos nomes de ambiente de hospedagem incluídos ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="95fbd-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="95fbd-118">include</span><span class="sxs-lookup"><span data-stu-id="95fbd-118">include</span></span>

<span data-ttu-id="95fbd-119">A propriedade `include` exibe um comportamento semelhante para o atributo `names`.</span><span class="sxs-lookup"><span data-stu-id="95fbd-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="95fbd-120">Um ambiente listado no valor do atributo `include` deve corresponder ao ambiente de hospedagem do aplicativo ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) para renderizar o conteúdo da marcação `<environment>`.</span><span class="sxs-lookup"><span data-stu-id="95fbd-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="95fbd-121">excluir</span><span class="sxs-lookup"><span data-stu-id="95fbd-121">exclude</span></span>

<span data-ttu-id="95fbd-122">Em contraste com o atributo `include`, o conteúdo da marcação `<environment>` é processado quando o ambiente de hospedagem não corresponde a um ambiente listado no valor do atributo `exclude`.</span><span class="sxs-lookup"><span data-stu-id="95fbd-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="95fbd-123">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="95fbd-123">Additional resources</span></span>

* <xref:fundamentals/environments>
