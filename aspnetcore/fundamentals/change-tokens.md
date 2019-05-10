---
title: Detectar alterações com tokens de alteração no ASP.NET Core
author: guardrex
description: Saiba como usar tokens de alteração para controlar alterações.
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886871"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Detectar alterações com tokens de alteração no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Um *token de alteração* é um bloco de construção de uso geral e de baixo nível, usado para controlar as alterações.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interface IChangeToken

[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propaga notificações de que ocorreu uma alteração. `IChangeToken` reside no namespace [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives). Para aplicativos que não usam o metapacote [Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior), veja o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) no arquivo de projeto.

`IChangeToken` tem duas propriedades:

* [ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indique se o token gera retornos de chamada de forma proativa. Se `ActiveChangedCallbacks` é definido como `false`, um retorno de chamada nunca é chamado e o aplicativo precisa sondar `HasChanged` em busca de alterações. Também é possível que um token nunca seja cancelado se não ocorrerem alterações ou o ouvinte de alteração subjacente for removido ou desabilitado.
* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) obtém um valor que indica se uma alteração ocorreu.

A interface tem um método, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), que registra um retorno de chamada que é invocado quando o token é alterado. `HasChanged` precisa ser definido antes de o retorno de chamada ser invocado.

## <a name="changetoken-class"></a>Classe ChangeToken

`ChangeToken` é uma classe estática usada para propagar notificações de que ocorreu uma alteração. `ChangeToken` reside no namespace [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives). Para aplicativos que não usam o metapacote [Microsoft.AspNetCore.All](xref:fundamentals/metapackage-app), veja o pacote NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) no arquivo de projeto.

O método `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) registra um `Action` para chamar sempre que o token é alterado:

* `Func<IChangeToken>` produz o token.
* `Action` é chamado quando o token é alterado.

`ChangeToken` tem uma sobrecarga [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) que usa um parâmetro `TState` adicional que é passado para o consumidor de token `Action`.

`OnChange` retorna um [IDisposable](/dotnet/api/system.idisposable). A chamada a [Dispose](/dotnet/api/system.idisposable.dispose) interrompe a escuta do token de outras alterações e libera os recursos do token.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Usos de exemplo de tokens de alteração no ASP.NET Core

Tokens de alteração são usados nas áreas proeminentes do monitoramento do ASP.NET Core de alterações em objetos:

* Para monitorar as alterações em arquivos, o método [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) de [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) cria um `IChangeToken` para os arquivos especificados ou para pasta a ser inspecionada.
* Tokens `IChangeToken` podem ser adicionados a entradas de cache para disparar remoções do cache após as alterações.
* Para alterações `TOptions`, a implementação padrão de [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) de [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) tem uma sobrecarga que aceita uma ou mais instâncias [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1). Cada instância retorna um `IChangeToken` para registrar um retorno de chamada de notificação de alteração para o controle de alterações de opções.

## <a name="monitoring-for-configuration-changes"></a>Monitorando alterações de configuração

Por padrão, os modelos do ASP.NET Core usam [arquivos de configuração JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) para carregar as definições de configuração do aplicativo.

Esses arquivos são configurados com o método de extensão [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) no [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) que aceita um parâmetro `reloadOnChange` (ASP.NET Core 1.1 e posterior). `reloadOnChange` indica se a configuração deve ser recarregada após alterações de arquivo. Confira essa configuração no método prático [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) do [WebHost](/dotnet/api/microsoft.aspnetcore.webhost):

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

A configuração baseada em arquivo é representada por [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource). A `FileConfigurationSource` usa o [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) para monitorar arquivos.

Por padrão, o `IFileMonitor` é fornecido por um [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), que usa [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) para monitorar as alterações no arquivo de configuração.

O aplicativo de exemplo demonstra duas implementações para monitorar as alterações de configuração. Se o arquivo *appsettings.json* é alterado ou a versão do Ambiente do arquivo é alterada, cada implementação executa um código personalizado. O aplicativo de exemplo grava uma mensagem no console.

O `FileSystemWatcher` de um arquivo de configuração pode disparar vários retornos de chamada de token para uma única alteração de arquivo de configuração. A implementação da amostra protege contra esse problema verificando os hashes de arquivo nos arquivos de configuração. A verificação de hashes de arquivo garante que, pelo menos, um dos arquivos de configuração foi alterado antes de executar o código personalizado. A amostra usa o hash de arquivo SHA1 (*Utilities/Utilities.cs*):

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   Uma nova tentativa é implementada com uma retirada exponencial. A nova tentativa está presente porque o bloqueio de arquivo pode ocorrer, o que impede temporariamente a computação de um novo hash em um dos arquivos.

### <a name="simple-startup-change-token"></a>Token de alteração de inicialização simples

Registre um retorno de chamada `Action` do consumidor de token para notificações de alteração no token de recarregamento de configuração (*Startup.cs*):

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

`config.GetReloadToken()` fornece o token. O retorno de chamada é o método `InvokeChanged`:

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

O `state` do retorno de chamada é usado para passar o `IHostingEnvironment`. Isso é útil para determinar o arquivo de configuração JSON *appsettings* correto a ser monitorado, *appsettings.&lt;Environment&gt;.json*. Hashes de arquivo são usados para impedir que a instrução `WriteConsole` seja executada várias vezes, devido a vários retornos de chamada de token quando o arquivo de configuração é alterado somente uma vez.

Esse sistema é executado, desde que o aplicativo esteja em execução e não possa ser desabilitado pelo usuário.

### <a name="monitoring-configuration-changes-as-a-service"></a>Monitorando alterações de configuração como um serviço

A amostra implementa:

* Monitoramento de token de inicialização básica.
* Monitoramento como serviço.
* Um mecanismo para habilitar e desabilitar o monitoramento.

A amostra estabelece uma interface `IConfigurationMonitor` (*Extensions/ConfigurationMonitor.cs*):

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

O construtor da classe implementada, `ConfigurationMonitor`, registra um retorno de chamada para notificações de alteração:

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` fornece o token. `InvokeChanged` é o método de retorno de chamada. O `state` nesta instância é uma referência à instância `IConfigurationMonitor` que é usada para acessar o estado de monitoramento. Duas propriedades são usadas:

* `MonitoringEnabled` indica se o retorno de chamada deve executar seu código personalizado.
* `CurrentState` descreve o estado atual de monitoramento para uso na interface do usuário.

O método `InvokeChanged` é semelhante à abordagem anterior, exceto que ele:

* Não executa o código, a menos que `MonitoringEnabled` seja `true`.
* Anota o `state` atual e sua saída `WriteConsole`.

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Uma instância `ConfigurationMonitor` é registrada como um serviço em `ConfigureServices` de *Startup.cs*:

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

A página Índice oferece ao usuário o controle sobre o monitoramento de configuração. A instância de `IConfigurationMonitor` é injetada no `IndexModel`:

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

Um botão habilita e desabilita o monitoramento:

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

Quando `OnPostStartMonitoring` é disparado, o monitoramento é habilitado e o estado atual é desmarcado. Quando `OnPostStopMonitoring` é disparado, o monitoramento é desabilitado e o estado é definido para refletir que o monitoramento não está ocorrendo.

## <a name="monitoring-cached-file-changes"></a>Monitorando alterações de arquivos armazenados em cache

O conteúdo do arquivo pode ser armazenado em cache em memória usando [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). O cache na memória é descrito no tópico [Cache na memória](xref:performance/caching/memory). Sem realizar etapas adicionais, como a implementação descrita abaixo, dados *obsoletos* (desatualizados) são retornados de um cache se os dados de origem são alterados.

Não levando em conta o status de um arquivo de origem armazenado em cache durante a renovação de um período de [expiração deslizante](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) resulta em dados de cache obsoletos. Cada solicitação de dados renova o período de expiração deslizante, mas o arquivo nunca é recarregado no cache. Os recursos do aplicativo que usam o conteúdo armazenado em cache do arquivo estão sujeitos ao possível recebimento de conteúdo obsoleto.

O uso de tokens de alteração em um cenário de cache de arquivo impede o conteúdo de arquivo obsoleto no cache. O aplicativo de exemplo demonstra uma implementação da abordagem.

A amostra usa `GetFileContent` para:

* Retornar o conteúdo do arquivo.
* Implementar um algoritmo de repetição com retirada exponencial para abranger casos em que um bloqueio de arquivo está impedindo temporariamente a leitura de um arquivo.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

Um `FileService` é criado para manipular pesquisas de arquivos armazenados em cache. A chamada de método `GetFileContent` do serviço tenta obter o conteúdo do arquivo do cache em memória e retorná-lo para o chamador (*Services/FileService.cs*).

Se o conteúdo armazenado em cache não é encontrado com a chave de cache, as seguintes ações são executadas:

1. O conteúdo do arquivo é obtido com `GetFileContent`.
1. Um token de alteração é obtido do provedor de arquivo com [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch). O retorno de chamada do token é disparado quando o arquivo é modificado.
1. O conteúdo do arquivo é armazenado em cache com um período de [expiração deslizante](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration). O token de alteração é anexado com [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) para remover a entrada do cache se o arquivo é alterado enquanto ele é armazenado em cache.

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

O `FileService` é registrado no contêiner de serviço junto com o serviço de cache da memória (*Startup.cs*):

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

O modelo de página carrega o conteúdo do arquivo usando o serviço (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Classe CompositeChangeToken

Para representar uma ou mais instâncias de `IChangeToken` em um único objeto, use a classe [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken).

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

`HasChanged` nos relatórios de token compostos `true` se um token representado `HasChanged` é `true`. `ActiveChangeCallbacks` nos relatórios de token compostos `true` se um token representado `ActiveChangeCallbacks` é `true`. Se ocorrerem vários eventos de alteração simultâneos, o retorno de chamada de alteração composto será invocado exatamente uma vez.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
