---
title: Auxiliar de Marca de Ambiente no ASP.NET Core
author: pkellner
description: Definição de Auxiliar de Marca de Ambiente do ASP.NET Core, incluindo todas as propriedades
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: e2e038fe69da696b67f7aef61795e23dc8512fdf
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856128"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Auxiliar de Marca de Ambiente no ASP.NET Core

Por [Peter Kellner](https://peterkellner.net), [Hisham Bin Ateya](https://twitter.com/hishambinateya) e [Luke Latham](https://github.com/guardrex)

O Auxiliar de Marca de Ambiente renderiza condicionalmente seu conteúdo contido com base no [ambiente de hospedagem](xref:fundamentals/environments) atual. Atributo único do Auxiliar de Marca de Ambiente, `names`, é uma lista separada por vírgulas de nomes de ambiente. Se nenhum dos nomes de ambiente fornecido corresponder ao ambiente atual, o conteúdo contido será renderizado.

Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Atributos do Auxiliar de Marca de Ambiente

### <a name="names"></a>nomes

`names` aceita um único nome de ambiente de hospedagem ou uma lista separada por vírgula de nomes de ambiente de hospedagem que disparam a renderização do conteúdo contido.

Valores de ambiente são comparados com o valor retornado por [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). A comparação ignora o uso de maiúsculas.

O exemplo a seguir usa um Auxiliar de Marca de Ambiente. O conteúdo será renderizado se o ambiente de hospedagem for De Preparo ou de Produção:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>incluir e excluir atributos

Os atributos `include` & `exclude` controlam a renderização do conteúdo contido com base nos nomes de ambiente de hospedagem incluídos ou excluídos.

### <a name="include"></a>include

A propriedade `include` exibe um comportamento semelhante para o atributo `names`. Um ambiente listado no valor do atributo `include` deve corresponder ao ambiente de hospedagem do aplicativo ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) para renderizar o conteúdo da marcação `<environment>`.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

Em contraste com o atributo `include`, o conteúdo da marcação `<environment>` é processado quando o ambiente de hospedagem não corresponde a um ambiente listado no valor do atributo `exclude`.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/environments>
