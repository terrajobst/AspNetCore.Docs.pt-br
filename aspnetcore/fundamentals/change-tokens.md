---
title: Detectar alterações com tokens de alteração no ASP.NET Core
author: guardrex
description: Saiba como usar tokens de alteração para controlar alterações.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: bb30d7a4c7dc82200821c60a49c314b246562111
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007215"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Detectar alterações com tokens de alteração no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Um *token de alteração* é um bloco de construção de uso geral e de baixo nível, usado para controlar as alterações de estado.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interface IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificações de que ocorreu uma alteração. `IChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. O pacote NuGet [Microsoft. Extensions. primitivas](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) é fornecido implicitamente para os aplicativos ASP.NET Core.

`IChangeToken` tem duas propriedades:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica se o token de forma proativa gera retornos de chamada. Se `ActiveChangedCallbacks` é definido como `false`, um retorno de chamada nunca é chamado e o aplicativo precisa sondar `HasChanged` em busca de alterações. Também é possível que um token nunca seja cancelado se não ocorrerem alterações ou o ouvinte de alteração subjacente for removido ou desabilitado.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recebe um valor que indica se uma alteração ocorreu.

A interface `IChangeToken` inclui o método [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra um retorno de chamada que é invocado quando o token é alterado. `HasChanged` precisa ser definido antes de o retorno de chamada ser invocado.

## <a name="changetoken-class"></a>Classe ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> é uma classe estática usada para propagar notificações de que ocorreu uma alteração. `ChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. O pacote NuGet [Microsoft. Extensions. primitivas](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) é fornecido implicitamente para os aplicativos ASP.NET Core.

O método [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra um `Action` para chamar sempre que o token é alterado:

* `Func<IChangeToken>` produz o token.
* `Action` é chamado quando o token é alterado.

A sobrecarga [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) usa um parâmetro `TState` adicional que é passado para o consumidor de token `Action`.

`OnChange` retorna um <xref:System.IDisposable>. A chamada <xref:System.IDisposable.Dispose*> interrompe a escuta do token de outras alterações e libera os recursos do token.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Usos de exemplo de tokens de alteração no ASP.NET Core

Os tokens de alteração são usados nas áreas proeminentes do ASP.NET Core para monitorar alterações em objetos:

* Para monitorar as alterações em arquivos, o método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> cria um `IChangeToken` para os arquivos especificados ou para pasta a ser inspecionada.
* Tokens `IChangeToken` podem ser adicionados a entradas de cache para disparar remoções do cache após as alterações.
* Para as alterações de `TOptions`, a implementação <xref:Microsoft.Extensions.Options.OptionsMonitor`1> padrão de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tem uma sobrecarga que aceita uma ou mais instâncias <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Cada instância retorna um `IChangeToken` para registrar um retorno de chamada de notificação de alteração para o controle de alterações de opções.

## <a name="monitor-for-configuration-changes"></a>Monitorar as alterações de configuração

Por padrão, os modelos do ASP.NET Core usam [arquivos de configuração JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) para carregar as definições de configuração do aplicativo.

Esses arquivos são configurados com o método de extensão [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) no <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que aceita um parâmetro `reloadOnChange`. `reloadOnChange` indica se a configuração deve ser recarregada após alterações de arquivo. Essa configuração é exibida no método de conveniência <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> de <xref:Microsoft.Extensions.Hosting.Host>:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

A configuração baseada em arquivo é representada por <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` usa <xref:Microsoft.Extensions.FileProviders.IFileProvider> para monitorar arquivos.

Por padrão, o `IFileMonitor` é fornecido por um <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, que usa <xref:System.IO.FileSystemWatcher> para monitorar as alterações do arquivo de configuração.

O aplicativo de exemplo demonstra duas implementações para monitorar as alterações de configuração. Se qualquer um dos arquivos *appsettings* forem alterados, ambas as implementações de monitoramento de arquivo executam código personalizado&mdash;o aplicativo de exemplo grava uma mensagem no console.

O `FileSystemWatcher` de um arquivo de configuração pode disparar vários retornos de chamada de token para uma única alteração de arquivo de configuração. Para garantir que o código personalizado é executado somente depois que os vários retornos de chamada de token são acionados, a implementação da amostra verifica os hashes do arquivo. O exemplo usa o hash de arquivo SHA1. Uma nova tentativa é implementada com uma retirada exponencial. A nova tentativa está presente porque o bloqueio de arquivo pode ocorrer, o que impede temporariamente a computação de um novo hash em um arquivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Token de alteração de inicialização simples

Registre um retorno de chamada `Action` do consumidor de token para notificações de alteração no token de recarregamento de configuração.

No `Startup.Configure`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` fornece o token. O retorno de chamada é o método `InvokeChanged`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

O `state` do retorno de chamada é usado para passar no `IWebHostEnvironment`, que é útil para especificar o arquivo de configuração *appsettings* correto para monitorar (por exemplo, *appsettings.Development.JSON* quando estiver no Ambiente de desenvolvimento). Hashes de arquivo são usados para impedir que a instrução `WriteConsole` seja executada várias vezes, devido a vários retornos de chamada de token quando o arquivo de configuração é alterado somente uma vez.

Esse sistema é executado, desde que o aplicativo esteja em execução e não possa ser desabilitado pelo usuário.

### <a name="monitor-configuration-changes-as-a-service"></a>Monitorar alterações de configuração como um serviço

A amostra implementa:

* Monitoramento de token de inicialização básica.
* Monitoramento como serviço.
* Um mecanismo para habilitar e desabilitar o monitoramento.

A amostra estabelece uma interface `IConfigurationMonitor`.

*Extensions/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

O construtor da classe implementada, `ConfigurationMonitor`, registra um retorno de chamada para notificações de alteração:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` fornece o token. `InvokeChanged` é o método de retorno de chamada. O `state` nesta instância é uma referência à instância `IConfigurationMonitor` que é usada para acessar o estado de monitoramento. Duas propriedades são usadas:

* `MonitoringEnabled` &ndash; indica se o retorno de chamada deve executar seu código personalizado.
* `CurrentState` &ndash; descreve o estado atual de monitoramento para uso na interface do usuário.

O método `InvokeChanged` é semelhante à abordagem anterior, exceto que ele:

* Não executa o código, a menos que `MonitoringEnabled` seja `true`.
* Gera saída do `state` atual em sua saída `WriteConsole`.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Uma instância `ConfigurationMonitor` é registrada como um serviço em `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

A página Índice oferece ao usuário o controle sobre o monitoramento de configuração. A instância de `IConfigurationMonitor` é injetada no `IndexModel`.

*Pages/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

O monitor de configuração (`_monitor`) é usado para habilitar ou desabilitar o monitoramento e definir o estado atual de comentários de interface do usuário:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Quando `OnPostStartMonitoring` é disparado, o monitoramento é habilitado e o estado atual é desmarcado. Quando `OnPostStopMonitoring` é disparado, o monitoramento é desabilitado e o estado é definido para refletir que o monitoramento não está ocorrendo.

Os botões na interface do usuário habilitam e desabilitar o monitoramento.

*Pages/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitor alterações de arquivos armazenados em cache

O conteúdo do arquivo pode ser armazenado em cache em memória usando <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. O cache na memória é descrito no tópico [Cache na memória](xref:performance/caching/memory). Sem realizar etapas adicionais, como a implementação descrita abaixo, dados *obsoletos* (desatualizados) são retornados de um cache se os dados de origem são alterados.

Por exemplo, não levando em conta o status de um arquivo de origem armazenado em cache durante a renovação de um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) resulta em dados de arquivo de cache obsoletos. Cada solicitação de dados renova o período de expiração deslizante, mas o arquivo nunca é recarregado no cache. Os recursos do aplicativo que usam o conteúdo armazenado em cache do arquivo estão sujeitos ao possível recebimento de conteúdo obsoleto.

O uso de tokens de alteração em um cenário de cache de arquivo impede a presença do conteúdo de arquivo obsoleto no cache. O aplicativo de exemplo demonstra uma implementação da abordagem.

A amostra usa `GetFileContent` para:

* Retornar o conteúdo do arquivo.
* Implementar um algoritmo de repetição com retirada exponencial para abranger casos em que um bloqueio de arquivo impede temporariamente a leitura do arquivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Um `FileService` é criado para manipular pesquisas de arquivos armazenados em cache. A chamada de método `GetFileContent` do serviço tenta obter o conteúdo do arquivo do cache em memória e retorná-lo para o chamador (*Services/FileService.cs*).

Se o conteúdo armazenado em cache não é encontrado com a chave de cache, as seguintes ações são executadas:

1. O conteúdo do arquivo é obtido com `GetFileContent`.
1. Um token de alteração é obtido do provedor de arquivo com [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). O retorno de chamada do token é disparado quando o arquivo é modificado.
1. O conteúdo do arquivo é armazenado em cache com um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). O token de alteração é anexado com [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para remover a entrada do cache se o arquivo é alterado enquanto ele é armazenado em cache.

No exemplo a seguir, os arquivos são armazenados na raiz do [conteúdo](xref:fundamentals/index#content-root)do aplicativo. `IWebHostEnvironment.ContentRootFileProvider` é usado para obter um apontador de <xref:Microsoft.Extensions.FileProviders.IFileProvider> no @no__t do aplicativo-2. O `filePath` é obtido com [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

O `FileService` é registrado no contêiner de serviço junto com o serviço de cache da memória.

No `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

O modelo de página carrega o conteúdo do arquivo usando o serviço.

No método `OnGet` da página de índice (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Classe CompositeChangeToken

Para representar uma ou mais instâncias de `IChangeToken` em um único objeto, use a classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged` nos relatórios de token compostos `true` se um token representado `HasChanged` é `true`. `ActiveChangeCallbacks` nos relatórios de token compostos `true` se um token representado `ActiveChangeCallbacks` é `true`. Se ocorrerem vários eventos de alteração simultâneos, o retorno de chamada de alteração composto será invocado uma vez.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um *token de alteração* é um bloco de construção de uso geral e de baixo nível, usado para controlar as alterações de estado.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interface IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> propaga notificações de que ocorreu uma alteração. `IChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Para aplicativos que não usam o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), crie uma referência de pacote para o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

`IChangeToken` tem duas propriedades:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indica se o token de forma proativa gera retornos de chamada. Se `ActiveChangedCallbacks` é definido como `false`, um retorno de chamada nunca é chamado e o aplicativo precisa sondar `HasChanged` em busca de alterações. Também é possível que um token nunca seja cancelado se não ocorrerem alterações ou o ouvinte de alteração subjacente for removido ou desabilitado.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> recebe um valor que indica se uma alteração ocorreu.

A interface `IChangeToken` inclui o método [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*), que registra um retorno de chamada que é invocado quando o token é alterado. `HasChanged` precisa ser definido antes de o retorno de chamada ser invocado.

## <a name="changetoken-class"></a>Classe ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> é uma classe estática usada para propagar notificações de que ocorreu uma alteração. `ChangeToken` reside no namespace <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Para aplicativos que não usam o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), crie uma referência de pacote para o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

O método [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registra um `Action` para chamar sempre que o token é alterado:

* `Func<IChangeToken>` produz o token.
* `Action` é chamado quando o token é alterado.

A sobrecarga [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) usa um parâmetro `TState` adicional que é passado para o consumidor de token `Action`.

`OnChange` retorna um <xref:System.IDisposable>. A chamada <xref:System.IDisposable.Dispose*> interrompe a escuta do token de outras alterações e libera os recursos do token.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Usos de exemplo de tokens de alteração no ASP.NET Core

Os tokens de alteração são usados nas áreas proeminentes do ASP.NET Core para monitorar alterações em objetos:

* Para monitorar as alterações em arquivos, o método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> cria um `IChangeToken` para os arquivos especificados ou para pasta a ser inspecionada.
* Tokens `IChangeToken` podem ser adicionados a entradas de cache para disparar remoções do cache após as alterações.
* Para as alterações de `TOptions`, a implementação <xref:Microsoft.Extensions.Options.OptionsMonitor`1> padrão de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> tem uma sobrecarga que aceita uma ou mais instâncias <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Cada instância retorna um `IChangeToken` para registrar um retorno de chamada de notificação de alteração para o controle de alterações de opções.

## <a name="monitor-for-configuration-changes"></a>Monitorar as alterações de configuração

Por padrão, os modelos do ASP.NET Core usam [arquivos de configuração JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) para carregar as definições de configuração do aplicativo.

Esses arquivos são configurados com o método de extensão [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) no <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> que aceita um parâmetro `reloadOnChange`. `reloadOnChange` indica se a configuração deve ser recarregada após alterações de arquivo. Essa configuração é exibida no método de conveniência <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de <xref:Microsoft.AspNetCore.WebHost>:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

A configuração baseada em arquivo é representada por <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` usa <xref:Microsoft.Extensions.FileProviders.IFileProvider> para monitorar arquivos.

Por padrão, o `IFileMonitor` é fornecido por um <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, que usa <xref:System.IO.FileSystemWatcher> para monitorar as alterações do arquivo de configuração.

O aplicativo de exemplo demonstra duas implementações para monitorar as alterações de configuração. Se qualquer um dos arquivos *appsettings* forem alterados, ambas as implementações de monitoramento de arquivo executam código personalizado&mdash;o aplicativo de exemplo grava uma mensagem no console.

O `FileSystemWatcher` de um arquivo de configuração pode disparar vários retornos de chamada de token para uma única alteração de arquivo de configuração. Para garantir que o código personalizado é executado somente depois que os vários retornos de chamada de token são acionados, a implementação da amostra verifica os hashes do arquivo. O exemplo usa o hash de arquivo SHA1. Uma nova tentativa é implementada com uma retirada exponencial. A nova tentativa está presente porque o bloqueio de arquivo pode ocorrer, o que impede temporariamente a computação de um novo hash em um arquivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Token de alteração de inicialização simples

Registre um retorno de chamada `Action` do consumidor de token para notificações de alteração no token de recarregamento de configuração.

No `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` fornece o token. O retorno de chamada é o método `InvokeChanged`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

O `state` do retorno de chamada é usado para passar no `IHostingEnvironment`, que é útil para especificar o arquivo de configuração *appsettings* correto para monitorar (por exemplo, *appsettings.Development.JSON* quando estiver no Ambiente de desenvolvimento). Hashes de arquivo são usados para impedir que a instrução `WriteConsole` seja executada várias vezes, devido a vários retornos de chamada de token quando o arquivo de configuração é alterado somente uma vez.

Esse sistema é executado, desde que o aplicativo esteja em execução e não possa ser desabilitado pelo usuário.

### <a name="monitor-configuration-changes-as-a-service"></a>Monitorar alterações de configuração como um serviço

A amostra implementa:

* Monitoramento de token de inicialização básica.
* Monitoramento como serviço.
* Um mecanismo para habilitar e desabilitar o monitoramento.

A amostra estabelece uma interface `IConfigurationMonitor`.

*Extensions/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

O construtor da classe implementada, `ConfigurationMonitor`, registra um retorno de chamada para notificações de alteração:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` fornece o token. `InvokeChanged` é o método de retorno de chamada. O `state` nesta instância é uma referência à instância `IConfigurationMonitor` que é usada para acessar o estado de monitoramento. Duas propriedades são usadas:

* `MonitoringEnabled` &ndash; indica se o retorno de chamada deve executar seu código personalizado.
* `CurrentState` &ndash; descreve o estado atual de monitoramento para uso na interface do usuário.

O método `InvokeChanged` é semelhante à abordagem anterior, exceto que ele:

* Não executa o código, a menos que `MonitoringEnabled` seja `true`.
* Gera saída do `state` atual em sua saída `WriteConsole`.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Uma instância `ConfigurationMonitor` é registrada como um serviço em `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

A página Índice oferece ao usuário o controle sobre o monitoramento de configuração. A instância de `IConfigurationMonitor` é injetada no `IndexModel`.

*Pages/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

O monitor de configuração (`_monitor`) é usado para habilitar ou desabilitar o monitoramento e definir o estado atual de comentários de interface do usuário:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Quando `OnPostStartMonitoring` é disparado, o monitoramento é habilitado e o estado atual é desmarcado. Quando `OnPostStopMonitoring` é disparado, o monitoramento é desabilitado e o estado é definido para refletir que o monitoramento não está ocorrendo.

Os botões na interface do usuário habilitam e desabilitar o monitoramento.

*Pages/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitor alterações de arquivos armazenados em cache

O conteúdo do arquivo pode ser armazenado em cache em memória usando <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. O cache na memória é descrito no tópico [Cache na memória](xref:performance/caching/memory). Sem realizar etapas adicionais, como a implementação descrita abaixo, dados *obsoletos* (desatualizados) são retornados de um cache se os dados de origem são alterados.

Por exemplo, não levando em conta o status de um arquivo de origem armazenado em cache durante a renovação de um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) resulta em dados de arquivo de cache obsoletos. Cada solicitação de dados renova o período de expiração deslizante, mas o arquivo nunca é recarregado no cache. Os recursos do aplicativo que usam o conteúdo armazenado em cache do arquivo estão sujeitos ao possível recebimento de conteúdo obsoleto.

O uso de tokens de alteração em um cenário de cache de arquivo impede a presença do conteúdo de arquivo obsoleto no cache. O aplicativo de exemplo demonstra uma implementação da abordagem.

A amostra usa `GetFileContent` para:

* Retornar o conteúdo do arquivo.
* Implementar um algoritmo de repetição com retirada exponencial para abranger casos em que um bloqueio de arquivo impede temporariamente a leitura do arquivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Um `FileService` é criado para manipular pesquisas de arquivos armazenados em cache. A chamada de método `GetFileContent` do serviço tenta obter o conteúdo do arquivo do cache em memória e retorná-lo para o chamador (*Services/FileService.cs*).

Se o conteúdo armazenado em cache não é encontrado com a chave de cache, as seguintes ações são executadas:

1. O conteúdo do arquivo é obtido com `GetFileContent`.
1. Um token de alteração é obtido do provedor de arquivo com [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). O retorno de chamada do token é disparado quando o arquivo é modificado.
1. O conteúdo do arquivo é armazenado em cache com um período de [expiração deslizante](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration). O token de alteração é anexado com [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) para remover a entrada do cache se o arquivo é alterado enquanto ele é armazenado em cache.

No exemplo a seguir, os arquivos são armazenados na raiz do [conteúdo](xref:fundamentals/index#content-root)do aplicativo. [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) é usado para obter <xref:Microsoft.Extensions.FileProviders.IFileProvider> apontando para <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> do aplicativo. O `filePath` é obtido com [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

O `FileService` é registrado no contêiner de serviço junto com o serviço de cache da memória.

No `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

O modelo de página carrega o conteúdo do arquivo usando o serviço.

No método `OnGet` da página de índice (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Classe CompositeChangeToken

Para representar uma ou mais instâncias de `IChangeToken` em um único objeto, use a classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged` nos relatórios de token compostos `true` se um token representado `HasChanged` é `true`. `ActiveChangeCallbacks` nos relatórios de token compostos `true` se um token representado `ActiveChangeCallbacks` é `true`. Se ocorrerem vários eventos de alteração simultâneos, o retorno de chamada de alteração composto será invocado uma vez.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
