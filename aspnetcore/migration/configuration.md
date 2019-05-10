---
title: Migrar a configuração para o ASP.NET Core
author: ardalis
description: Saiba como migrar a configuração de um projeto do ASP.NET MVC para um projeto ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894923"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrar a configuração para o ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)

No artigo anterior, começamos [migrar um projeto ASP.NET MVC para ASP.NET Core MVC](xref:migration/mvc). Neste artigo, vamos migrar a configuração.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Configuração da instalação

O ASP.NET Core não usa o *global. asax* e *Web. config* arquivos utilizadas de versões anteriores do ASP.NET. Em versões anteriores do ASP.NET, a lógica de inicialização do aplicativo foi colocada em uma `Application_StartUp` método dentro *global. asax*. Posteriormente, no ASP.NET MVC, uma *Startup.cs* arquivo foi incluído na raiz do projeto; e ele foi chamado quando o aplicativo foi iniciado. ASP.NET Core tem adotado essa abordagem completamente colocando toda lógica de inicialização na *Startup.cs* arquivo.

O *Web. config* arquivo também foi substituído no ASP.NET Core. Configuração em si agora pode ser configurada como parte do procedimento de inicialização de aplicativo descrito em *Startup.cs*. Configuração ainda pode utilizar arquivos XML, mas normalmente projetos do ASP.NET Core serão coloca valores de configuração em um arquivo formatado em JSON, como *appSettings. JSON*. Sistema de configuração do ASP.NET Core também poderá acessar facilmente as variáveis de ambiente, que podem fornecer um [mais local seguro e robusto](xref:security/app-secrets) para valores específicos do ambiente. Isso é especialmente verdadeiro para segredos, como cadeias de caracteres de conexão e as chaves de API que não devem ser verificadas no controle de origem. Ver [configuração](xref:fundamentals/configuration/index) para saber mais sobre a configuração no ASP.NET Core.

Neste artigo, estamos começando com o projeto ASP.NET Core migrado parcialmente desde [o artigo anterior](xref:migration/mvc). A configuração da instalação, adicione o seguinte construtor e propriedade como o *Startup.cs* arquivo localizado na raiz do projeto:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Observe que neste ponto, o *Startup.cs* arquivo não será compilado, pois precisamos adicionar o seguinte `using` instrução:

```csharp
using Microsoft.Extensions.Configuration;
```

Adicionar um *appSettings. JSON* arquivo na raiz do projeto usando o modelo de item apropriado:

![Adicione AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrar definições da configuração da Web. config

Nosso projeto ASP.NET MVC incluído a cadeia de caracteres de conexão de banco de dados necessários no *Web. config*, no `<connectionStrings>` elemento. Em nosso projeto do ASP.NET Core, vamos armazenar essas informações na *appSettings. JSON* arquivo. Abra *appSettings. JSON*e observe que ele já inclui o seguinte:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Na linha realçada descrita acima, altere o nome do banco de dados **_CHANGE_ME** para o nome do seu banco de dados.

## <a name="summary"></a>Resumo

ASP.NET Core coloca toda lógica de inicialização para o aplicativo em um único arquivo, em que os serviços necessários e dependências podem ser definidas e configuradas. Ele substitui o *Web. config* arquivo com um recurso de configuração flexíveis que pode aproveitar uma variedade de formatos de arquivo, como JSON, bem como as variáveis de ambiente.
