---
title: Provedores de arquivos no ASP.NET Core
author: guardrex
description: Saiba como o ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 93eb48d81a853061a874641e84b4875849690a93
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886611"
---
# <a name="file-providers-in-aspnet-core"></a>Provedores de arquivos no ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e [Luke Latham](https://github.com/guardrex)

O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos. Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:

* [IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) expõe o conteúdo raiz do aplicativo e a raiz da Web como tipos `IFileProvider`.
* O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.
* [Razor](xref:mvc/views/razor) usa provedores de arquivos para localizar páginas e modos de exibição.
* As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfaces de provedor de arquivo

A interface principal é [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider). `IFileProvider` expõe métodos para:

* Obter informações sobre o arquivo ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).
* Obter informações sobre o diretório ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).
* Configurar notificações de alteração (usando um [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).

`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:

* [Exists](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [IsDirectory](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [Nome](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* [Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (em bytes)
* Data de [LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified)

Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream).

O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementações do provedor de arquivos

Três implementações de `IFileProvider` estão disponíveis.

| Implementação | Descrição |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | O provedor físico é usado para acessar os arquivos físicos do sistema. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | O provedor inserido de manifesto é usado para acessar arquivos incorporados em assemblies. |
| [CompositeFileProvider](#compositefileprovider) | O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais provedores. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

O [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) fornece acesso ao sistema de arquivos físico. O `PhysicalFileProvider` usa o tipo [System.IO.File](/dotnet/api/system.io.file) (para o provedor físico) e delimita todos os caminhos para um diretório e seus filhos. Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos. Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor. Você pode criar uma instância de um provedor `PhysicalFileProvider` diretamente, ou pode solicitar um `IFileProvider` em um construtor por meio de uma [injeção de dependência](xref:fundamentals/dependency-injection).

**Tipos estáticos**

O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Tipos no exemplo anterior:

* `provider` é um `IFileProvider`.
* `contents` é um `IDirectoryContents`.
* `fileInfo` é um `IFileInfo`.

O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo. O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.

O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

**Obter tipos de provedor de arquivos com injeção de dependência**

Injete o provedor em qualquer construtor de classe e atribua-o a um campo local. Use o campo em todos os métodos da classe para acessar arquivos.

No aplicativo de amostra, a classe `IndexModel` recebe uma instância `IFileProvider` para obter o conteúdo do diretório para o caminho base do aplicativo.

*Pages/Index.cshtml.cs*:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

Os `IDirectoryContents` são iterados na página.

*Pages/Index.cshtml*:

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

O [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) é usado para acessar arquivos inseridos em assemblies. O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.

Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`. Especifique os arquivos para inserir com [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.

O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

Sobrecargas adicionais permitem:

* Especificar um caminho de arquivo relativo.
* Delimitar os arquivos segundo a data da última modificação.
* Nomear o recurso inserido que contém o manifesto do arquivo inserido.

| Sobrecarga | Descrição |
| -------- | ----------- |
| [ManifestEmbeddedFileProvider(Assembly, String)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | Aceita um parâmetro de caminho relativo `root` opcional. Especifique o `root` para delimitar as chamadas para [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) para os recursos no caminho fornecido. |
| [ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` ([DateTimeOffset](/dotnet/api/system.datetimeoffset)). A data de `lastModified` tem como escopo a data da última modificação para as instâncias de [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) retornadas pelo [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider). |
| [ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`. O `manifestName` representa o nome do recurso inserido que contém o manifesto. |

### <a name="compositefileprovider"></a>CompositeFileProvider

O [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores. Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.

No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Monitorar as alterações

O método [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações. `Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos. `Watch` retorna um [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken). O token de alteração expõe:

* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): Uma propriedade que poderá ser inspecionada para determinar se uma alteração ocorreu.
* [RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Chamada quando são detectadas alterações na cadeia de caracteres do caminho especificado. Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração. Para permitir o monitoramento constante, use um [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.

No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável. Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.

## <a name="glob-patterns"></a>Padrões glob

Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*. Especifique grupos de arquivos com esses padrões. Os dois caracteres curinga são `*` e `**`:

**`*`**  
Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo. As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.

**`**`**  
Coincide a qualquer coisa em vários níveis de diretório. Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.

**Exemplos de padrão glob**

**`directory/file.txt`**  
Corresponde a um arquivo específico em um diretório específico.

**`directory/*.txt`**  
Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.

**`directory/*/appsettings.json`**  
Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.

**`directory/**/*.txt`**  
Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.
