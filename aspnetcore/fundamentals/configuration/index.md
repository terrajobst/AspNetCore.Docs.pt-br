---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2019
uid: fundamentals/configuration/index
---
# <a name="configuration-in-aspnet-core"></a>Configuração no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*. Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:

::: moniker range=">= aspnetcore-2.1"

* Azure Key Vault
* Argumentos de linha de comando
* Provedores personalizados (instalados ou criados)
* Arquivos de diretório
* Variáveis de ambiente
* Objetos do .NET na memória
* Arquivos de configurações

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

* Azure Key Vault
* Argumentos de linha de comando
* Provedores personalizados (instalados ou criados)
* Variáveis de ambiente
* Objetos do .NET na memória
* Arquivos de configurações

::: moniker-end

::: moniker range="= aspnetcore-1.0"

* Argumentos de linha de comando
* Provedores personalizados (instalados ou criados)
* Variáveis de ambiente
* Objetos do .NET na memória
* Arquivos de configurações

::: moniker-end

O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico. As opções usam classes para representar grupos de configurações relacionadas. Para saber mais sobre como usar o padrão de opções, confira <xref:fundamentals/configuration/options>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-2.1"

Esses três pacotes estão incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Esses três pacotes estão incluídos no [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage).

::: moniker-end

## <a name="host-vs-app-configuration"></a>Configuração do host versus aplicativo

Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado. O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo. O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico. Os pares chave-valor de configuração do host se tornam parte da configuração global do aplicativo. Para saber mais sobre como os provedores de configuração são usados quando o host for criado e como as fontes de configuração afetam o host e a configuração, veja [O host](xref:fundamentals/index#host).

## <a name="default-configuration"></a>Configuração padrão

Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host. `CreateDefaultBuilder` fornece a configuração padrão para o aplicativo.

* A configuração do host é fornecida de:
  * Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).
* A configuração do aplicativo é fornecida de (na seguinte ordem):
  * *appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * *appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * [Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.
  * Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).

Os provedores de configuração são explicados posteriormente neste tópico. Para obter mais informações sobre o host e `CreateDefaultBuilder`, consulte <xref:fundamentals/host/web-host#set-up-a-host>.

## <a name="security"></a>Segurança

Adote as melhores práticas a seguir:

* Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.
* Não use segredos de produção em ambientes de teste ou de desenvolvimento.
* Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.

Saiba mais sobre [como usar vários ambientes](xref:fundamentals/environments) e gerenciar o [armazenamento seguro de segredos de aplicativo em desenvolvimento com o Gerenciador de Segredo](xref:security/app-secrets) (inclui recomendações sobre como usar variáveis de ambiente para armazenar dados confidenciais). O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local. O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.

O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) é uma opção para o armazenamento seguro de segredos do aplicativo. Para obter mais informações, consulte <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Dados de configuração hierárquica

A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.

No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração. As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração. Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists). `GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

## <a name="conventions"></a>Convenções

Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.

Os Provedores de Configuração de Arquivo têm a capacidade de recarregar a configuração quando um arquivo de configurações subjacente é alterado após a inicialização do aplicativo. O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.

<xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo. Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.

As chaves de configuração adotam as convenções a seguir:

* As chaves não diferenciam maiúsculas de minúsculas. Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.
* Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.
* Chaves hierárquicas
  * Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.
  * Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas. Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido em dois-pontos.
  * No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador. Você deve fornecer o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.
* O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).

Os valores de configuração adotam as convenções a seguir:

* Os valores são cadeias de caracteres.
* Não é possível armazenar valores nulos na configuração ou associá-los a objetos.

## <a name="providers"></a>Provedores

A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.

::: moniker range=">= aspnetcore-2.1"

| Provider | Fornece a configuração de &hellip; |
| -------- | ----------------------------------- |
| [Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*) | Azure Key Vault |
| [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider) | Parâmetros de linha de comando |
| [Provedor de Configuração personalizado](#custom-configuration-provider) | Fonte personalizada |
| [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider) | Variáveis de ambiente |
| [Provedor de Configuração de Arquivo](#file-configuration-provider) | Arquivos (INI, JSON, XML) |
| [Provedor de Configuração de Chave por Arquivo](#key-per-file-configuration-provider) | Arquivos de diretório |
| [Provedor de Configuração de Memória](#memory-configuration-provider) | Coleções na memória |
| [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*) | Arquivo no diretório de perfil do usuário |

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

| Provider | Fornece a configuração de &hellip; |
| -------- | ----------------------------------- |
| [Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*) | Azure Key Vault |
| [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider) | Parâmetros de linha de comando |
| [Provedor de Configuração personalizado](#custom-configuration-provider) | Fonte personalizada |
| [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider) | Variáveis de ambiente |
| [Provedor de Configuração de Arquivo](#file-configuration-provider) | Arquivos (INI, JSON, XML) |
| [Provedor de Configuração de Memória](#memory-configuration-provider) | Coleções na memória |
| [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*) | Arquivo no diretório de perfil do usuário |

::: moniker-end

::: moniker range="= aspnetcore-1.0"

| Provider | Fornece a configuração de &hellip; |
| -------- | ----------------------------------- |
| [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider) | Parâmetros de linha de comando |
| [Provedor de Configuração personalizado](#custom-configuration-provider) | Fonte personalizada |
| [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider) | Variáveis de ambiente |
| [Provedor de Configuração de Arquivo](#file-configuration-provider) | Arquivos (INI, JSON, XML) |
| [Provedor de Configuração de Memória](#memory-configuration-provider) | Coleções na memória |
| [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*) | Arquivo no diretório de perfil do usuário |

::: moniker-end

Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração. Os provedores de configuração descritos neste tópico estão descritos em ordem alfabética, não na ordem na qual seu código pode organizá-los. Organize os provedores de configuração em seu código para atender às suas prioridades para as fontes de configuração subjacentes.

Uma sequência comum de provedores de configuração é:

1. Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)
1. Variáveis de ambiente
1. Argumentos de linha de comando

É comum posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.

::: moniker range=">= aspnetcore-2.0"

Essa sequência de provedores é aplicada quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>. Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Essa sequência de provedores pode ser criada para o aplicativo (não o host) com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> e uma chamada para seu método <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*> em `Startup`:

```csharp
public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
            reloadOnChange: true);

    var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));

    if (appAssembly != null)
    {
        builder.AddUserSecrets(appAssembly, optional: true);
    }

    builder.AddEnvironmentVariables();

    Configuration = builder.Build();
}

public IConfiguration Configuration { get; }

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IConfiguration>(Configuration);
}
```

No exemplo anterior, o nome do ambiente (`env.EnvironmentName`) e o nome do assembly de aplicativo (`env.ApplicationName`) são fornecidos pelo <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>. Para obter mais informações, consulte <xref:fundamentals/environments>. O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).
.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

::: moniker-end

## <a name="command-line-configuration-provider"></a>Provedor de Configuração de Linha de Comando

O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em tempo de execução.

Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

::: moniker range=">= aspnetcore-2.0"

`AddCommandLine` é chamado automaticamente quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>. Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).

`CreateDefaultBuilder` também carrega:

* Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).
* Variáveis de ambiente.

`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último. Os argumentos de linha de comando passados em tempo de execução substituem a configuração definida por outros provedores.

`CreateDefaultBuilder` age quando o host é construído. Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.

`AddCommandLine` já foi chamado por `CreateDefaultBuilder`. Se precisar oferecer configuração de aplicativo e ainda poder substituir essa configuração por argumentos de linha de comando, chame os provedores adicionais do aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> e chame `AddCommandLine` por fim.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>.

`AddCommandLine` já foi chamado por `CreateDefaultBuilder` quando `UseConfiguration` é chamado. Se precisar oferecer configuração de aplicativo e ainda poder substituir essa configuração por argumentos de linha de comando, chame os provedores adicionais do aplicativo em um `ConfigurationBuilder` e chame `AddCommandLine` por fim.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call other providers here and call AddCommandLine last.
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Para ativar a configuração de linha de comando, chame o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Chamar o provedor por último permite que os argumentos de linha de comando passados em tempo de execução substituam a configuração definida por outros provedores de configuração.

Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

**Exemplo**

::: moniker range=">= aspnetcore-2.0"

O aplicativo de exemplo 2.x aproveita a vantagem do método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

O aplicativo de exemplo 1.x chama <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> em um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

::: moniker-end

1. Abra um prompt de comando no diretório do projeto.
1. Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.
1. Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.

### <a name="arguments"></a>Arguments

O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço. O valor pode ser nulo se um sinal de igual for usado (por exemplo, `CommandLineKey=`).

| Prefixo da chave               | Exemplo                                                |
| ------------------------ | ------------------------------------------------------ |
| Nenhum prefixo                | `CommandLineKey1=value1`                               |
| Dois traços (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Barra (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.

Exemplo de comandos:

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Mapeamentos de comutador

Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave. Quando você cria manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, pode fornecer um dicionário de substituições de opções para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando. Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo. Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).

Regras de chave do dicionário de mapeamentos de comutador:

* Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).
* O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.

::: moniker range=">= aspnetcore-2.1"

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

Conforme mostrado no exemplo anterior, a chamada para `CreateDefaultBuilder` não deve passar argumentos ao usar mapeamentos de opção. A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`. Se os argumentos incluírem uma opção mapeada e forem passados para `CreateDefaultBuilder`, o provedor `AddCommandLine` não inicializará com um <xref:System.FormatException>. A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var switchMappings = new Dictionary<string, string>
            {
                { "-CLKey1", "CommandLineKey1" },
                { "-CLKey2", "CommandLineKey2" }
            };

        var config = new ConfigurationBuilder()
            .AddCommandLine(args, switchMappings)
            .Build();

        // Do not pass the args to CreateDefaultBuilder
        return WebHost.CreateDefaultBuilder()
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Conforme mostrado no exemplo anterior, a chamada para `CreateDefaultBuilder` não deve passar argumentos ao usar mapeamentos de opção. A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`. Se os argumentos incluírem uma opção mapeada e forem passados para `CreateDefaultBuilder`, o provedor `AddCommandLine` não inicializará com um <xref:System.FormatException>. A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    var switchMappings = new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    var config = new ConfigurationBuilder()
        .AddCommandLine(args, switchMappings)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .UseStartup<Startup>()
        .Start();

    using (host)
    {
        Console.ReadLine();
    }
}
```

::: moniker-end

Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.

| Chave       | Valor             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.

| Chave               | Valor    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Provedor de Configuração de Variáveis de Ambiente

O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em tempo de execução.

Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Ao trabalhar com chaves hierárquicas nas variáveis de ambiente, talvez um separador de dois-pontos (`:`) não funcione em todas as plataformas. Um sublinhado duplo (`__`) é compatível com todas as plataformas e é substituído por dois-pontos.

O [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) permite que você defina variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o Provedor de Configuração de Variáveis de Ambiente. Para obter mais informações, confira [Aplicativos Azure: Substituir a configuração do aplicativo no Portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

::: moniker range=">= aspnetcore-2.0"

`AddEnvironmentVariables` é chamado automaticamente para as variáveis de ambiente prefixadas com `ASPNETCORE_` quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).

`CreateDefaultBuilder` também carrega:

* Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.
* Configuração opcional de *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).
* Argumentos de linha de comando.

O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*. Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em tempo de execução para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo.

Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>e chame `AddEnvironmentVariables` com o prefixo.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow environment
                // variables to override values from other providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Chame o método de extensão `AddEnvironmentVariables` em uma instância de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>.

Se precisar fornecer configuração do aplicativo com base em variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>e chame `AddEnvironmentVariables` com o prefixo.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call additional providers here as needed.
            // Call AddEnvironmentVariables last if you need to allow environment
            // variables to override values from other providers.
            .AddEnvironmentVariables(prefix: "PREFIX_")
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

**Exemplo**

::: moniker range=">= aspnetcore-2.0"

O aplicativo de exemplo 2.x aproveita a vantagem do método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

O aplicativo de exemplo 1.x chama `AddEnvironmentVariables` em um `ConfigurationBuilder`.

::: moniker-end

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`. O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.

Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente para mostrar as que começam com o seguinte:

* ASPNETCORE_
* urls
* Registrando em log
* AMBIENTE
* contentRoot
* AllowedHosts
* applicationName
* CommandLine

::: moniker range=">= aspnetcore-2.0"

Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Pages/Index.cshtml.cs* para o seguinte:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Se você quiser expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *Controllers/HomeController.cs* para o seguinte:

::: moniker-end

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Prefixos

Variáveis de ambiente carregadas na configuração do aplicativo são filtradas quando você fornece um prefixo para o método `AddEnvironmentVariables`. Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

O prefixo é removido na criação dos pares chave-valor de configuração.

::: moniker range=">= aspnetcore-2.0"

O método de conveniência estático `CreateDefaultBuilder` cria um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> para estabelecer o host do aplicativo. Quando `WebHostBuilder` é criado, ele encontra a configuração de seu host nas variáveis de ambiente prefixadas com `ASPNETCORE_`.

::: moniker-end

**Prefixos de cadeia de conexão**

A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo. As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.

| Prefixo da cadeia de conexão | Provider |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Provedor personalizado |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:

* A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).
* Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).

| Chave de variável de ambiente | Chave de configuração convertida | Entrada de configuração do provedor                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | Entrada de configuração não criada.                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | Chave: `ConnectionStrings:<KEY>_ProviderName`:<br>Valor: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | Chave: `ConnectionStrings:<KEY>_ProviderName`:<br>Valor: `System.Data.SqlClient`  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | Chave: `ConnectionStrings:<KEY>_ProviderName`:<br>Valor: `System.Data.SqlClient`  |

## <a name="file-configuration-provider"></a>Provedor de Configuração de Arquivo

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos. Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:

* [Provedor de Configuração INI](#ini-configuration-provider)
* [Provedor de Configuração JSON](#json-configuration-provider)
* [Provedor de Configuração XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Provedor de Configuração INI

O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em tempo de execução.

Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

::: moniker range=">= aspnetcore-2.1"

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile("config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddIniFile("config.ini", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Um exemplo genérico de um arquivo de configuração INI:

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>Provedor de Configuração JSON

O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em tempo de execução.

Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

::: moniker range=">= aspnetcore-2.0"

`AddJsonFile` é chamado automaticamente duas vezes quando você inicializa um novo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>. O método é chamado para carregar a configuração de:

* *appsettings.json* &ndash; Esse arquivo é lido primeiro. A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.
* *appsettings.{Environment}.json* &ndash; A versão de ambiente do arquivo é carregada com base em [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Para obter mais informações, confira [Host da Web: Configurar um host](xref:fundamentals/host/web-host#set-up-a-host).

`CreateDefaultBuilder` também carrega:

* Variáveis de ambiente.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (no ambiente de desenvolvimento).
* Argumentos de linha de comando.

O Provedor de Configuração JSON é estabelecido primeiro. Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile("config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Você pode chamar diretamente o método de extensão `AddJsonFile` em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("config.json", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

**Exemplo**

::: moniker range=">= aspnetcore-2.0"

O aplicativo de exemplo 2.x aproveita a vantagem do método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`. A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

O aplicativo de exemplo 1.x chama `AddJsonFile` duas vezes em um `ConfigurationBuilder`. A configuração é carregada de *appsettings.json* e de *appsettings.{Environment}.json*.

::: moniker-end

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém pares chave-valor para a configuração mostrada na tabela de acordo com o ambiente. Chaves de configuração de registro usam os dois-pontos (`:`) como um separador hierárquico.

| Chave                        | Valor de Desenvolvimento | Valor de Produção |
| -------------------------- | :---------------: | :--------------: |
| Logging:LogLevel:System    | Informações       | Informações      |
| Logging:LogLevel:Microsoft | Informações       | Informações      |
| Logging:LogLevel:Default   | Depurar             | Erro            |
| AllowedHosts               | *                 | *                |

### <a name="xml-configuration-provider"></a>Provedor de Configuração XML

O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em tempo de execução.

Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados. Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.

::: moniker range=">= aspnetcore-2.1"

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile("config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

É possível usar atributos para fornecer valores:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

O arquivo de configuração anterior carrega as seguintes chaves com `value`:

* key:attribute
* section:key:attribute

::: moniker range=">= aspnetcore-2.1"

## <a name="key-per-file-configuration-provider"></a>Provedor de Configuração de Chave por Arquivo

O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração. A chave é o nome do arquivo. O valor contém o conteúdo do arquivo. O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.

Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. O `directoryPath` para os arquivos deve ser um caminho absoluto.

As sobrecargas permitem especificar:

* Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.
* Se o diretório é opcional, e o caminho para o diretório.

O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo. Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

O caminho base é definido com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>. `SetBasePath` está no pacote [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

::: moniker-end

## <a name="memory-configuration-provider"></a>Provedor de Configuração de Memória

O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.

Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.

::: moniker range=">= aspnetcore-2.1"

Chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> ao criar o host para especificar a configuração do aplicativo:

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Ao chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

        var config = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Ao criar um <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> diretamente, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> com a configuração:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Aplique a configuração ao <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> com o método <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>:

::: moniker-end

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a>GetValue

[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um valor de configuração com uma chave especificada e o converte para o tipo especificado. Uma sobrecarga permite que você forneça um valor padrão se a chave não for encontrada.

O exemplo a seguir extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`, digita o valor como um `int` e armazena o valor na variável `intValue`. Se `NumberKey` não for encontrado em chaves de configuração, `intValue` recebe o valor padrão de `99`:

```csharp
var intValue = config.GetValue<int>("NumberKey", 99);
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren e Exists

Para os exemplos a seguir, considere o seguinte arquivo JSON. Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada. `GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` não tem um valor, apenas uma chave e um caminho.

Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` nunca retorna `null`. Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.

Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.

### <a name="getchildren"></a>GetChildren

Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

::: moniker range=">= aspnetcore-2.0"

### <a name="exists"></a>Exists

Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.

::: moniker-end

## <a name="bind-to-a-class"></a>Associar a uma classe

A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*. Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object). `Bind` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/starship.json)]

::: moniker-end

Os seguintes pares chave-valor de configuração são criados:

| Chave                   | Valor                                             |
| --------------------- | ------------------------------------------------- |
| starship:name         | USS Enterprise                                    |
| starship:registry     | NCC-1701                                          |
| starship:class        | Constituição                                      |
| starship:length       | 304,8                                             |
| starship:commissioned | False                                             |
| marca             | Paramount Pictures Corp. http://www.paramount.com |

O aplicativo de exemplo chama `GetSection` com a chave `starship`. Os pares chave-valor `starship` são isolados. O método `Bind` é chamado na subseção passando uma instância da classe `Starship`. Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_starship)]

::: moniker-end

`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

## <a name="bind-to-an-object-graph"></a>Associar a um gráfico de objeto

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro. `Bind` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-xml[](index/samples/1.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`. A instância associada é atribuída a uma propriedade para renderização:

::: moniker range=">= aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
viewModel.TvShow = tvShow;
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado. O `Get<T>` é mais conveniente do que usar `Bind`. O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_tvshow)]

::: moniker-end

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). `Get<T>` está disponível no ASP.NET Core 1.1 ou posterior. `GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*

O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. Qualquer formato de matriz que exponha um segmento de chave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar matrizes a uma matriz de classe POCO. `Bind`` está no pacote [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

> [!NOTE]
> A associação é fornecida por convenção. Provedores de configuração personalizados não são necessários para implementar a associação de matriz.

**Processamento de matriz na memória**

Considere as chaves de configuração e os valores mostrados na tabela a seguir.

| Chave             | Valor  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=5-12,16)]

::: moniker-end

A matriz ignora um valor para o índice &num;3. O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.

No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

Os dados de configuração estão associados ao objeto:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

`GetSection` está no pacote [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), que está no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

::: moniker range=">= aspnetcore-1.1"

A sintaxe [ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_array)]

::: moniker-end

O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.

| Índice `ArrayExample.Entries` | Valor `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`. Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto. Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.

O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração. Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

::: moniker range=">= aspnetcore-2.0"

No <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

No construtor `Startup`:

```csharp
.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

O par chave-valor mostrado na tabela é carregado na configuração.

| Chave             | Valor  |
| :-------------: | :----: |
| array:entries:3 | value3 |

Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.

| Índice `ArrayExample.Entries` | Valor `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Processamento de matriz JSON**

Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero. No arquivo de configuração a seguir, `subsection` é uma matriz:

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/json_array.json)]

::: moniker-end

O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:

| Chave                     | Valor  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

Após a associação, `JsonArrayExample.Key` contém o valor `valueA`. Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.

| Índice `JsonArrayExample.Subsection` | Valor `JsonArrayExample.Subsection` |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>Provedor de Configuração personalizado

O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).

O provedor tem as seguintes características:

* O banco de dados EF na memória é usado para fins de demonstração. Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.
* O provedor lê uma tabela de banco de dados na configuração na inicialização. O provedor não consulta o banco de dados em uma base por chave.
* O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.

Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.

*Models/EFConfigurationValue.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.

*EFConfigurationProvider/EFConfigurationContext.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. O provedor de configuração inicializa o banco de dados quando ele está vazio.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=24)]

::: moniker-end

## <a name="access-configuration-during-startup"></a>Acessar a configuração durante a inicialização

Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`. Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, confira [Inicialização do aplicativo: Métodos de conveniência](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Acessar a configuração em uma página do Razor Pages ou exibição do MVC

Para acessar definições de configuração em uma página do Razor Pages ou uma exibição do MVC, adicione [usando diretiva](xref:mvc/views/razor#using) ([referência de C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespace Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e injete <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou na exibição.

Em uma página do Razor:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

Em uma exibição do MVC:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>Adicionar configuração de um assembly externo

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo. Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/options>
* [Detalhes da configuração da Microsoft](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)
