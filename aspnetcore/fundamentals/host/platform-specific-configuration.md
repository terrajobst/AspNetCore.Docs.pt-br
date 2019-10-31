---
title: Usar assemblies de inicialização de hospedagem no ASP.NET Core
author: guardrex
description: Descubra como aprimorar um aplicativo ASP.NET Core por meio de um assembly externo usando uma implementação de IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c1ba742dda64296348898ec6a15ba725501dcb4f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391010"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Usar assemblies de inicialização de hospedagem no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) e [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (inicialização de hospedagem) adiciona aprimoramentos a um aplicativo na inicialização por meio de um assembly externo. Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atributo HostingStartup

Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em tempo de execução.

O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`. A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no tempo de execução da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`. A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Descobrir assemblies de inicialização de hospedagem carregados

Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo. Erros que ocorrem quando os assemblies carregados são registrados em log. Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Desabilitar o carregamento automático de assemblies de inicialização de hospedagem

Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:

* Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:

  * Impedir a configuração do host de inicialização de hospedagem:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.

* Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:

  * Hospedando a definição de configuração de host de assemblies de inicialização:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.

A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.

## <a name="project"></a>Projeto

Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:

* [Biblioteca de classes](#class-library)
* [Aplicativo de console sem um ponto de entrada](#console-app-without-an-entry-point)

### <a name="class-library"></a>Biblioteca de classes

Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes. A biblioteca contém um atributo `HostingStartup`.

O [código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um aplicativo Razor Pages, *HostingStartupApp* e uma biblioteca de classes, *HostingStartupLibrary*. A biblioteca de classes:

* Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`. `ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.

O método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe `ServiceKeyInjection` usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

O [código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*. O pacote tem as mesmas características da biblioteca de classes descrita anteriormente. O pacote:

* Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`. `ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.
* Inclui um atributo `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Aplicativo de console sem um ponto de entrada

*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*

Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`. Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.

Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:

* Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem. Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.
* Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de tempo de execução](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o tempo de execução compartilhado.

Na criação de uma inicialização de hospedagem dinâmica:

* Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:
  * Inclui uma classe que contém a implementação de `IHostingStartup`.
  * Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.
* O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem. Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.
* O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.
* O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime. Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.

O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma classe como uma implementação de `IHostingStartup` para carregamento e execução ao compilar o <xref:Microsoft.AspNetCore.Hosting.IWebHost>. No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Uma classe implementa `IHostingStartup`. O método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo. `IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo tempo de execução antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Ao criar um projeto `IHostingStartup`, o arquivo de dependências ( *.deps.json*) define o local `runtime` do assembly como a pasta *bin*:

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Apenas uma parte do arquivo é mostrada. O nome do assembly no exemplo é `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Configuração fornecida pela inicialização da hospedagem

Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:

1. Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados. A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.
1. Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados. Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Especificar o assembly de inicialização de hospedagem

Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.

Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`. Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Um assembly de inicialização de hospedagem também pode ser definido usando a configuração hospedando a configuração de host assemblies de inicialização:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Quando vários conjuntos de inicialização de hospedagem estão presentes, seus métodos <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> são executados na ordem em que os assemblies são listados.

## <a name="activation"></a>Ativação

As opções para ativação da inicialização de hospedagem são:

* [Repositório de tempo de execução](#runtime-store) &ndash; A ativação não requer uma referência de tempo de compilação para a ativação. O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador. A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.
* Referência de tempo de compilação necessária para a ativação
  * [Pacote do NuGet](#nuget-package)
  * [Pasta Lixeira do projeto](#project-bin-folder)

### <a name="runtime-store"></a>Repositório de runtime

A implementação de inicialização de hospedagem é colocada no [repositório de tempo de execução](/dotnet/core/deploying/runtime-store). Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.

Depois que a inicialização de hospedagem é compilada, um repositório de tempo de execução é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Para o tempo de execução descobrir o repositório de tempo de execução, o local do repositório de tempo de execução é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.

**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**

Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do tempo de execução com `additionalDeps`. `additionalDeps` permite que você:

* Estenda o grafo de biblioteca do aplicativo fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o próprio arquivo *.deps.json* do aplicativo na inicialização.
* Torne o assembly de inicialização de hospedagem detectável e carregável.

A abordagem recomendada para gerar o arquivo de dependências adicionais é:

 1. Executar o `dotnet publish` no arquivo de manifesto do repositório de tempo de execução mencionado na seção anterior.
 1. Remover a referência do manifesto das bibliotecas e a seção `runtime` do arquivo *deps.json* resultante.

No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Coloque o arquivo *.deps.json* no seguinte local:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}` &ndash; Local adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.
* `{SHARED FRAMEWORK NAME}` &ndash; Estrutura compartilhada necessária para esse arquivo de dependências adicionais.
* `{SHARED FRAMEWORK VERSION}` &ndash; Versão mínima de estrutura compartilhada.
* `{ENHANCEMENT ASSEMBLY NAME}` &ndash; Nome do assembly do aprimoramento.

No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Para o tempo de execução descobrir o local do repositório de tempo de execução, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.

No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).

Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).

**Implantação**

Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:

* O repositório de runtime de inicialização de hospedagem.
* O arquivo de dependências de inicialização de hospedagem.
* Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem. Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.

### <a name="nuget-package"></a>Pacote NuGet

Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet. O pacote tem um atributo `HostingStartup`. Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:

* O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação). Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo ( *.deps.json*). Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).
* O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de tempo de execução](#runtime-store) (sem uma referência de tempo de compilação).

Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:

* [Como criar um pacote do NuGet com ferramentas de plataforma cruzada](/dotnet/core/deploying/creating-nuget-packages)
* [Publicando pacotes](/nuget/create-packages/publish-a-package)
* [Repositório de pacote de tempo de execução](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Pasta Lixeira do projeto

Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado. Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:

* O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação). Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo ( *.deps.json*). Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:
  * o projeto de consumo.
  * um local acessível ao projeto de consumo.
* O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de tempo de execução](#runtime-store) (sem uma referência de tempo de compilação).
* Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:
  * caminho base do aplicativo &ndash; a pasta *bin* onde o executável do aplicativo( *.exe*) está localizado.
  * GAC (cache de assembly global) &ndash; o GAC armazena assemblies que vários aplicativos .NET Framework compartilham. Para obter mais informações, consulte [como: instalar um assembly no cache de assembly global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.

## <a name="sample-code"></a>Código de exemplo

O [código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:

* Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:
  * Pacote do NuGet (*HostingStartupPackage*)
  * Biblioteca de classes (*HostingStartupLibrary*)
* Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de tempo de execução (*StartupDiagnostics*). O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:
  * Serviços registrados
  * Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)
  * Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)
  * Cabeçalhos de solicitação
  * Variáveis de ambiente

Para executar a amostra:

**Ativação de um pacote do NuGet**

1. Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Compile e execute o aplicativo. Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação). Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote ( *../HostingStartupPackage/bin/Debug*) como uma origem de pacote. Isso permite que o aplicativo use o pacote sem carregar o pacote em [NuGet.org](https://www.nuget.org/). Para obter mais informações, consulte as observações no arquivo de projeto do HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.

Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local. Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):

```dotnetcli
dotnet nuget locals all --clear
```

**Ativação de uma biblioteca de classes**

1. Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).
1. Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.
1. Compile e execute o aplicativo. Um `<ItemGroup>` no arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (uma referência de tempo de compilação). Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.

**Ativação de um assembly implantado pelo repositório de tempo de execução**

1. O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*. O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1. Para obter o PowerShell em outras plataformas, confira [Instalando o Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Execute o script *build.ps1* na pasta *RuntimeStore*. O script:
   * Gera o pacote `StartupDiagnostics` na pasta *obj\packages*
   * Gera o repositório de tempo de execução para `StartupDiagnostics` na pasta de *armazenamento*. O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de tempo de execução)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows. Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script. O armazenamento em tempo de execução para `StartupDiagnostics` posteriormente seria movido para o armazenamento de tempo de execução do usuário ou do sistema no computador em que o assembly será consumido. O local de instalação do repositório de tempo de execução do usuário para o assembly `StartupDiagnostics` é *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.
   * Gera o `additionalDeps` para `StartupDiagnostics` na pasta *additionalDeps* As dependências adicionais posteriormente seriam movidas para as dependências adicionais do usuário ou do sistema. O local de instalação das dependências adicionais do usuário `StartupDiagnostics` é *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NetCore. app/3.0.0/StartupDiagnostics. deps. JSON*.
   * Coloca o arquivo *deploy.ps1* na pasta *deployment*.
1. Execute o script *deploy.ps1* na pasta *deployment*. O script acrescenta:
   * `StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * O caminho de dependências de inicialização de hospedagem (na pasta de *implantação* do projeto RuntimeStore) para a variável de ambiente `DOTNET_ADDITIONAL_DEPS`.
   * O caminho de armazenamento em tempo de execução (na pasta de *implantação* do projeto RuntimeStore) para a variável de ambiente `DOTNET_SHARED_STORE`.
1. Execute o aplicativo de exemplo.
1. Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo. Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (inicialização de hospedagem) adiciona aprimoramentos a um aplicativo na inicialização por meio de um assembly externo. Por exemplo, uma biblioteca externa pode usar uma implementação de inicialização de hospedagem para fornecer serviços ou provedores de configuração adicionais a um aplicativo.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atributo HostingStartup

Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indica a presença de um assembly de inicialização de hospedagem para ativar em tempo de execução.

O assembly de entrada ou o assembly que contém a classe `Startup` é automaticamente examinado para o atributo `HostingStartup`. A lista de assemblies a ser pesquisada para os atributos `HostingStartup` é carregada no tempo de execução da configuração em [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). A lista de assemblies para excluir da descoberta é carregada de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Para obter mais informações, consulte [Host da Web: assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies) e [Host da Web: assemblies de exclusão da inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

No exemplo a seguir, o namespace do assembly de inicialização de hospedagem é `StartupEnhancement`. A classe que contém o código de inicialização de hospedagem é `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

O atributo `HostingStartup` normalmente está localizado no arquivo de classe de implementação `IHostingStartup` do assembly de inicialização de hospedagem.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Descobrir assemblies de inicialização de hospedagem carregados

Para descobrir os assemblies de inicialização de hospedagem carregados, habilite o registro em log e verifique os logs do aplicativo. Erros que ocorrem quando os assemblies carregados são registrados em log. Os assemblies de inicialização de hospedagem carregados são registrados em log no nível Depuração e todos os erros são registrados.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Desabilitar o carregamento automático de assemblies de inicialização de hospedagem

Para desabilitar o carregamento automático de assemblies de inicialização de hospedagem, use uma das seguintes abordagens:

* Para impedir o carregamento de todos os assemblies de inicialização de hospedagem, defina o seguinte para `true` ou `1`:
  * Configuração do host [Impedir inicialização de hospedagem](xref:fundamentals/host/web-host#prevent-hosting-startup).
  * A variável de ambiente `ASPNETCORE_PREVENTHOSTINGSTARTUP`.
* Para evitar o carregamento de assemblies específicos de inicialização de hospedagem, defina uma das opções a seguir como uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para excluir na inicialização:
  * Configuração do host [Assemblies de exclusão de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).
  * A variável de ambiente `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Se a configuração do host e a variável de ambiente estiverem definidas, a configuração do host controlará o comportamento.

A desabilitação de assemblies de inicialização de hospedagem usando a configuração do host ou a variável de ambiente desabilita o assembly globalmente e poderá desabilitar várias características de um aplicativo.

## <a name="project"></a>Projeto

Crie uma inicialização de hospedagem com qualquer um dos seguintes tipos de projeto:

* [Biblioteca de classes](#class-library)
* [Aplicativo de console sem um ponto de entrada](#console-app-without-an-entry-point)

### <a name="class-library"></a>Biblioteca de classes

Uma melhoria da inicialização de hospedagem pode ser fornecida em uma biblioteca de classes. A biblioteca contém um atributo `HostingStartup`.

O [código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclui um aplicativo Razor Pages, *HostingStartupApp* e uma biblioteca de classes, *HostingStartupLibrary*. A biblioteca de classes:

* Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`. `ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço à configuração do aplicativo usando o provedor de configuração na memória ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Inclui um atributo `HostingStartup` que identifica o namespace e a classe de inicialização de hospedagem.

O método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe `ServiceKeyInjection` usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem da biblioteca de classes:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

O [código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) também inclui um projeto de pacote do NuGet que fornece uma inicialização de hospedagem separada, *HostingStartupPackage*. O pacote tem as mesmas características da biblioteca de classes descrita anteriormente. O pacote:

* Contém uma classe de inicialização de hospedagem, `ServiceKeyInjection`, que implementa `IHostingStartup`. `ServiceKeyInjection` adiciona um par de cadeias de caracteres de serviço para a configuração do aplicativo.
* Inclui um atributo `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

A página de índice do aplicativo lê e renderiza os valores de configuração para as duas chaves definidas pelo assembly de inicialização de hospedagem do pacote:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Aplicativo de console sem um ponto de entrada

*Essa abordagem só está disponível para aplicativos .NET Core, não para .NET Framework.*

Uma melhoria de inicialização de hospedagem dinâmica que não requer uma referência de tempo de compilação para a ativação pode ser fornecida em um aplicativo de console sem um ponto de entrada que contenha um atributo `HostingStartup`. Publicar o aplicativo de console produz um assembly de inicialização de hospedagem que pode ser consumido do repositório de runtime.

Um aplicativo de console sem um ponto de entrada é usado nesse processo porque:

* Um arquivo de dependências é necessário para consumir a inicialização de hospedagem no assembly de inicialização de hospedagem. Um arquivo de dependências é um ativo de aplicativo executável que é produzido ao publicar um aplicativo, não uma biblioteca.
* Uma biblioteca não pode ser adicionada diretamente ao [repositório de pacotes de tempo de execução](/dotnet/core/deploying/runtime-store), o que exige um projeto executável que tem como alvo o tempo de execução compartilhado.

Na criação de uma inicialização de hospedagem dinâmica:

* Um assembly de inicialização de hospedagem é criado no aplicativo de console sem um ponto de entrada que:
  * Inclui uma classe que contém a implementação de `IHostingStartup`.
  * Inclui um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) para identificar a classe de implementação de `IHostingStartup`.
* O aplicativo de console é publicado para obter as dependências da inicialização de hospedagem. Uma consequência de publicar o aplicativo de console é que as dependências não utilizadas são cortadas do arquivo de dependências.
* O arquivo de dependências é modificado para definir o local de runtime do assembly de inicialização de hospedagem.
* O assembly de inicialização de hospedagem e seu arquivo de dependências são colocados no repositório do pacote de runtime. Para descobrir o assembly de inicialização de hospedagem e seu arquivo de dependências, eles são listados em um par de variáveis de ambiente.

O assembly de aplicativo do console referencia o pacote [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Um atributo [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifica uma classe como uma implementação de `IHostingStartup` para carregamento e execução ao compilar o <xref:Microsoft.AspNetCore.Hosting.IWebHost>. No seguinte exemplo, o namespace é `StartupEnhancement` e a classe é `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Uma classe implementa `IHostingStartup`. O método <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> da classe usa um <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> para adicionar aprimoramentos a um aplicativo. `IHostingStartup.Configure` no assembly de inicialização de hospedagem é chamado pelo tempo de execução antes de `Startup.Configure` no código do usuário, o que permite que o código de usuário substitua qualquer configuração fornecida pelo assembly de inicialização de hospedagem.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Ao criar um projeto `IHostingStartup`, o arquivo de dependências ( *.deps.json*) define o local `runtime` do assembly como a pasta *bin*:

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Apenas uma parte do arquivo é mostrada. O nome do assembly no exemplo é `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Configuração fornecida pela inicialização da hospedagem

Existem duas abordagens para lidar com a configuração, dependendo se você deseja que a configuração da inicialização da hospedagem tenha precedência ou que a configuração do aplicativo tenha precedência:

1. Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para carregar a configuração depois que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo forem executados. A configuração de inicialização de hospedagem tem prioridade sobre a configuração do aplicativo que usa essa abordagem.
1. Configure o aplicativo usando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para carregar a configuração antes que os representantes <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> do aplicativo sejam executados. Os valores de configuração do aplicativo têm prioridade sobre aqueles fornecidos pela inicialização da hospedagem que usa essa abordagem.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Especificar o assembly de inicialização de hospedagem

Para uma biblioteca de classes ou inicialização de hospedagem fornecida pelo aplicativo de console, especifique o nome do assembly de inicialização de hospedagem na variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. A variável de ambiente é uma lista de assemblies delimitada por ponto e vírgula.

Apenas assemblies de inicialização de hospedagem são examinados quanto ao atributo `HostingStartup`. Para o aplicativo de exemplo, *HostingStartupApp*, para descobrir as inicializações de hospedagem descritas anteriormente, a variável de ambiente é definida como o seguinte valor:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Um assembly de inicialização de hospedagem também pode ser definido usando a configuração do host [Assemblies de inicialização de hospedagem](xref:fundamentals/host/web-host#hosting-startup-assemblies).

Quando vários conjuntos de inicialização de hospedagem estão presentes, seus métodos <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> são executados na ordem em que os assemblies são listados.

## <a name="activation"></a>Ativação

As opções para ativação da inicialização de hospedagem são:

* [Repositório de tempo de execução](#runtime-store) &ndash; A ativação não requer uma referência de tempo de compilação para a ativação. O aplicativo de exemplo coloca os arquivos de dependências e o assembly de inicialização de hospedagem em uma pasta, *implantação*, para facilitar a implantação da inicialização de hospedagem em um ambiente multicomputador. A pasta *implantação* também inclui um script do PowerShell que cria ou modifica variáveis de ambiente no sistema de implantação para habilitar a inicialização de hospedagem.
* Referência de tempo de compilação necessária para a ativação
  * [Pacote do NuGet](#nuget-package)
  * [Pasta Lixeira do projeto](#project-bin-folder)

### <a name="runtime-store"></a>Repositório de runtime

A implementação de inicialização de hospedagem é colocada no [repositório de tempo de execução](/dotnet/core/deploying/runtime-store). Uma referência de tempo de compilação para o assembly não é exigida pelo aplicativo aprimorado.

Depois que a inicialização de hospedagem é compilada, um repositório de tempo de execução é gerado, usando o arquivo de projeto do manifesto e o comando do [dotnet store](/dotnet/core/tools/dotnet-store).

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

No aplicativo de exemplo (projeto *RuntimeStore*) é usado o seguinte comando:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Para o tempo de execução descobrir o repositório de tempo de execução, o local do repositório de tempo de execução é adicionado à variável de ambiente `DOTNET_SHARED_STORE`.

**Modificar e colocar o arquivo de dependências da inicialização de hospedagem**

Para ativar o aprimoramento sem uma referência de pacote ao aprimoramento, especifique as dependências adicionais do tempo de execução com `additionalDeps`. `additionalDeps` permite que você:

* Estenda o grafo de biblioteca do aplicativo fornecendo um conjunto de arquivos *.deps.json* adicionais a serem mesclados com o próprio arquivo *.deps.json* do aplicativo na inicialização.
* Torne o assembly de inicialização de hospedagem detectável e carregável.

A abordagem recomendada para gerar o arquivo de dependências adicionais é:

 1. Executar o `dotnet publish` no arquivo de manifesto do repositório de tempo de execução mencionado na seção anterior.
 1. Remover a referência do manifesto das bibliotecas e a seção `runtime` do arquivo *deps.json* resultante.

No projeto de exemplo, a propriedade `store.manifest/1.0.0` é removida das seções `targets` e `libraries`:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Coloque o arquivo *.deps.json* no seguinte local:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}` &ndash; Local adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.
* `{SHARED FRAMEWORK NAME}` &ndash; Estrutura compartilhada necessária para esse arquivo de dependências adicionais.
* `{SHARED FRAMEWORK VERSION}` &ndash; Versão mínima de estrutura compartilhada.
* `{ENHANCEMENT ASSEMBLY NAME}` &ndash; Nome do assembly do aprimoramento.

No aplicativo de exemplo (projeto *RuntimeStore*), o arquivo de dependências adicionais é colocado no seguinte local:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Para o tempo de execução descobrir o local do repositório de tempo de execução, o local do arquivo de dependências adicionais é adicionado à variável de ambiente `DOTNET_ADDITIONAL_DEPS`.

No aplicativo de exemplo (projeto *RuntimeStore*), crie o repositório de tempo de execução e gere o arquivo de dependências adicionais usando um script [PowerShell](/powershell/scripting/powershell-scripting).

Para obter exemplos de como definir variáveis de ambiente para vários sistemas operacionais, confira [Usar vários ambientes](xref:fundamentals/environments).

**Implantação**

Para facilitar a implantação de uma inicialização de hospedagem em um ambiente multicomputador, o aplicativo de exemplo cria uma pasta *implantação* na saída publicada que contém:

* O repositório de runtime de inicialização de hospedagem.
* O arquivo de dependências de inicialização de hospedagem.
* Um script do PowerShell que cria ou modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` e `DOTNET_ADDITIONAL_DEPS` para dar suporte à ativação da inicialização de hospedagem. Execute o script de um prompt de comando do PowerShell administrativo no sistema de implantação.

### <a name="nuget-package"></a>Pacote NuGet

Uma melhoria da inicialização de hospedagem pode ser fornecida em pacote do NuGet. O pacote tem um atributo `HostingStartup`. Os tipos de inicialização de hospedagem fornecidos pelo pacote são disponibilizados para o aplicativo usando qualquer uma das seguintes abordagens:

* O arquivo de projeto do aplicativo aprimorado faz uma referência de pacote para a inicialização de hospedagem no arquivo de projeto do aplicativo (uma referência de tempo de compilação). Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo ( *.deps.json*). Essa abordagem se aplica a um pacote de assembly de inicialização de hospedagem publicado para [nuget.org](https://www.nuget.org/).
* O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de tempo de execução](#runtime-store) (sem uma referência de tempo de compilação).

Para obter mais informações sobre pacotes do NuGet e o repositório de runtime, consulte os tópicos a seguir:

* [Como criar um pacote do NuGet com ferramentas de plataforma cruzada](/dotnet/core/deploying/creating-nuget-packages)
* [Publicando pacotes](/nuget/create-packages/publish-a-package)
* [Repositório de pacote de tempo de execução](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Pasta Lixeira do projeto

Uma melhoria da inicialização de hospedagem pode ser fornecida por um assemby implantado na *lixeira* no aplicativo aprimorado. Os tipos de inicialização de hospedagem fornecidos pelo assembly são disponibilizados para o aplicativo usando uma das seguintes abordagens:

* O arquivo de projeto do aplicativo aprimorado faz uma referência de assembly para a inicialização de hospedagem (uma referência de tempo de compilação). Com a referência de tempo de compilação em vigor, o assembly de inicialização de hospedagem e todas as suas dependências são incorporados ao arquivo de dependência do aplicativo ( *.deps.json*). Essa abordagem se aplica quando o cenário de implantação demanda fazer uma referência de tempo de compilação ao assembly da inicialização de hospedagem (arquivo *.dll*) e mover o assembly para:
  * o projeto de consumo.
  * um local acessível ao projeto de consumo.
* O arquivo de dependências da inicialização de hospedagem fica disponível para o aplicativo avançado, conforme descrito na seção [Repositório de tempo de execução](#runtime-store) (sem uma referência de tempo de compilação).
* Ao ter como destino o .NET Framework, o assembly é carregável no contexto de carga padrão, o que, no .NET Framework, significa que ele fica em um dos seguintes locais:
  * caminho base do aplicativo &ndash; a pasta *bin* onde o executável do aplicativo( *.exe*) está localizado.
  * GAC (cache de assembly global) &ndash; o GAC armazena assemblies que vários aplicativos .NET Framework compartilham. Para obter mais informações, consulte [como: instalar um assembly no cache de assembly global](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) na documentação do .NET Framework.

## <a name="sample-code"></a>Código de exemplo

O [código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([como baixar](xref:index#how-to-download-a-sample)) demonstra cenários de implementação de inicialização de hospedagem:

* Dois assemblies de inicialização de hospedagem (bibliotecas de classes) definem um par chave-valor de configuração na memória cada:
  * Pacote do NuGet (*HostingStartupPackage*)
  * Biblioteca de classes (*HostingStartupLibrary*)
* Uma inicialização de hospedagem é ativada de um assembly implantado pelo repositório de tempo de execução (*StartupDiagnostics*). O assembly adiciona dois middlewares ao aplicativo na inicialização que fornecem informações de diagnóstico sobre:
  * Serviços registrados
  * Endereço (esquema, host, base do caminho, caminho, cadeia de caracteres de consulta)
  * Conexão (IP remoto, porta remota, IP local, porta local, certificado do cliente)
  * Cabeçalhos de solicitação
  * Variáveis de ambiente

Para executar a amostra:

**Ativação de um pacote do NuGet**

1. Compile o pacote *HostingStartupPackage* com o comando [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Adicione o nome do assembly do pacote do *HostingStartupPackage* para a variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Compile e execute o aplicativo. Uma referência de pacote está presente no aplicativo aprimorado (uma referência de tempo de compilação). Um `<PropertyGroup>` no arquivo de projeto do aplicativo especifica a saída do projeto de pacote ( *../HostingStartupPackage/bin/Debug*) como uma origem de pacote. Isso permite que o aplicativo use o pacote sem carregar o pacote em [NuGet.org](https://www.nuget.org/). Para obter mais informações, consulte as observações no arquivo de projeto do HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` do pacote.

Se você fizer alterações no projeto *HostingStartupPackage* e recompilá-lo, limpe os caches de pacote do NuGet locais para garantir que o *HostingStartupApp* receba o pacote atualizado e não um pacote obsoleto do cache local. Para limpar os caches locais do NuGet, execute o seguinte comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):

```dotnetcli
dotnet nuget locals all --clear
```

**Ativação de uma biblioteca de classes**

1. Compile a biblioteca de classes *HostingStartupLibrary* com o comando [dotnet build](/dotnet/core/tools/dotnet-build).
1. Adicione nome do assembly da biblioteca de classes do *HostingStartupLibrary* à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. A pasta *lixeira* implanta o assembly da biblioteca de classes para o aplicativo ao copiar o arquivo *HostingStartupLibrary.dll* da saída compilada da biblioteca de classes para a pasta *lixeira/Depurar* do aplicativo.
1. Compile e execute o aplicativo. Um `<ItemGroup>` do arquivo de projeto do aplicativo referencia o assembly da biblioteca de classes ( *.\lixeira\Depurar\netcoreapp2.1\HostingStartupLibrary.dll*) (uma referência de tempo de compilação). Para mais informações, consulte as notas no arquivo de projeto do HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Observe que os valores de chave de configuração do serviço renderizados pela página de índice correspondem aos valores definidos pelo método `ServiceKeyInjection.Configure` da biblioteca de classes.

**Ativação de um assembly implantado pelo repositório de tempo de execução**

1. O projeto *StartupDiagnostics* usa o [PowerShell](/powershell/scripting/powershell-scripting) para modificar seu arquivo *StartupDiagnostics.deps.json*. O PowerShell é instalado por padrão em um sistema operacional Windows começando no Windows 7 SP1 e no Windows Server 2008 R2 SP1. Para obter o PowerShell em outras plataformas, confira [Instalando o Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Execute o script *build.ps1* na pasta *RuntimeStore*. O script:
   * Gera o pacote `StartupDiagnostics` na pasta *obj\packages*
   * Gera o repositório de tempo de execução para `StartupDiagnostics` na pasta de *armazenamento*. O comando `dotnet store` no script usa o `win7-x64` [RID (identificador de tempo de execução)](/dotnet/core/rid-catalog) para uma inicialização de host implantada no Windows. Ao fornecer a inicialização de hospedagem para um runtime diferente, substitua pelo RID correto na linha 37 do script. O armazenamento em tempo de execução para `StartupDiagnostics` posteriormente seria movido para o armazenamento de tempo de execução do usuário ou do sistema no computador em que o assembly será consumido. O local de instalação do repositório de tempo de execução do usuário para o assembly `StartupDiagnostics` é *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.
   * Gera o `additionalDeps` para `StartupDiagnostics` na pasta *additionalDeps* As dependências adicionais posteriormente seriam movidas para as dependências adicionais do usuário ou do sistema. O local de instalação das dependências adicionais do usuário `StartupDiagnostics` é *. dotnet/x64/additionalDeps/StartupDiagnostics/Shared/Microsoft. NetCore. app/2.2.0/StartupDiagnostics. deps. JSON*.
   * Coloca o arquivo *deploy.ps1* na pasta *deployment*.
1. Execute o script *deploy.ps1* na pasta *deployment*. O script acrescenta:
   * `StartupDiagnostics` à variável de ambiente `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * O caminho de dependências de inicialização de hospedagem (na pasta de *implantação* do projeto RuntimeStore) para a variável de ambiente `DOTNET_ADDITIONAL_DEPS`.
   * O caminho de armazenamento em tempo de execução (na pasta de *implantação* do projeto RuntimeStore) para a variável de ambiente `DOTNET_SHARED_STORE`.
1. Execute o aplicativo de exemplo.
1. Solicite o ponto de extremidade `/services` para ver os serviços registrados do aplicativo. Solicite o ponto de extremidade `/diag` para ver as informações de diagnóstico.

::: moniker-end
