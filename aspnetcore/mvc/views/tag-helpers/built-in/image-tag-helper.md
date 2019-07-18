---
title: Auxiliar de Marca de Imagem no ASP.NET Core
author: pkellner
description: Mostra como trabalhar com o Auxiliar de Marca de Imagem.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 964072ad276f7e3e411ee41cb03a2efb9d05c585
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856121"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Auxiliar de Marca de Imagem no ASP.NET Core

Por [Peter Kellner](https://peterkellner.net)

O Auxiliar de Marca de Imagem aprimora a marca `<img>` para fornecer comportamento de extrapolação de cache para arquivos de imagem estática.

Uma cadeia de caracteres de extrapolação de cache é um valor exclusivo que representa o hash do arquivo de imagem estática acrescentado à URL do ativo. A cadeia de caracteres exclusiva solicita que os clientes (e alguns proxies) recarreguem a imagem do servidor Web do host, e não do cache do cliente.

Se a origem da imagem (`src`) for um arquivo estático no servidor Web de host:

* Uma cadeia de caracteres exclusiva de extrapolação de cache será anexada como um parâmetro de consulta à origem da imagem.
* Se o arquivo no servidor Web host for alterado, será gerada uma URL de solicitação exclusiva que inclua o parâmetro de solicitação atualizado.

Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Atributos de Auxiliar de Marca de Imagem

### <a name="src"></a>src

Para ativar o Auxiliar de Marca de Imagem, o atributo `src` é obrigatório no elemento `<img>`.

A origem da imagem (`src`) deve apontar para um arquivo estático físico no servidor. Se `src` for um URI remoto, o parâmetro de cadeia de caracteres de consulta de extrapolação de cache não será gerado.

### <a name="asp-append-version"></a>asp-append-version

Quando `asp-append-version` for especificado com um valor `true` junto com um atributo `src`, o Auxiliar de Marca de Imagem será invocado.

O exemplo a seguir usa um Auxiliar de Marca de Imagem:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Se o arquivo estático existe no diretório */wwwroot/images/* , o HTML gerado é semelhante ao seguinte (o hash será diferente):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

O valor atribuído ao parâmetro `v` é o valor de hash do arquivo *asplogo.png* em disco. Se o servidor Web não conseguir obter acesso de leitura ao arquivo estático, nenhum parâmetro `v` será adicionado ao atributo `src` na marcação renderizada.

## <a name="hash-caching-behavior"></a>Comportamento de armazenamento em cache de hash

O Auxiliar de Marca de Imagem usa o provedor de cache no servidor Web local para armazenar o hash `Sha512` calculado de um determinado arquivo. Se o arquivo for solicitado várias vezes, o hash não será recalculado. O cache é invalidado por um observador de arquivo anexado ao arquivo quando o hash `Sha512` do arquivo é calculado. Quando o arquivo muda no disco, um novo hash é calculado e armazenado em cache.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/caching/memory>
