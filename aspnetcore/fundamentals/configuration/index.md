---
title: Configuração no ASP.NET Core
author: guardrex
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 3dcabae3f76d81e641057c346dbb9097c2da42c7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656327"
---
# <a name="configuration-in-aspnet-core"></a>Configuração no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*. Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:

* Cofre de Chave do Azure
* Configuração de Aplicativo do Azure
* Argumentos de linha de comando
* Provedores personalizados (instalados ou criados)
* Arquivos de diretório
* Variáveis de ambiente
* Objetos do .NET na memória
* Arquivos de configurações

Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos implicitamente pela estrutura.

Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:

```csharp
using Microsoft.Extensions.Configuration;
```

O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico. As opções usam classes para representar grupos de configurações relacionadas. Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Configuração do host versus do aplicativo

Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado. O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo. O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico. Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo. Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Outra configuração

Este tópico pertence apenas à *configuração do aplicativo*. Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:

* *Launch. json*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:
  * Em <xref:fundamentals/environments#development>.
  * No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.
* o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="default-configuration"></a>Configuração padrão

Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> ao criar um host. `CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:

O seguinte se aplica a aplicativos que usam o [host genérico](xref:fundamentals/host/generic-host). Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* A configuração do host é fornecida de:
  * Variáveis ​​de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider). O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).
* A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):
  * O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.
  * Adicione o middleware de filtragem de host.
  * Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.
  * Habilite a integração de IIS.
* A configuração do aplicativo é fornecida de:
  * *appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * *appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * [Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.
  * Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).

## <a name="security"></a>Segurança

Adote as seguintes práticas para proteger dados de configuração confidenciais:

* Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.
* Não use segredos de produção em ambientes de teste ou de desenvolvimento.
* Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.

Para obter mais informações, consulte estes tópicos:

* <xref:fundamentals/environments>
* <xref:security/app-secrets> &ndash; inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais. O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local. O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.

O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core. Para obter mais informações, consulte <xref:security/key-vault-configuration>.

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

Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração. Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Convenções

### <a name="sources-and-providers"></a>Origens e provedores

Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.

Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada. Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.

<xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo. <xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetada em um Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.

Nos exemplos a seguir, o campo `_config` é usado para acessar os valores de configuração:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.

### <a name="keys"></a>Keys

As chaves de configuração adotam as convenções a seguir:

* As chaves não diferenciam maiúsculas de minúsculas. Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.
* Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.
* Chaves hierárquicas
  * Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.
  * Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas. Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.
  * No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador. Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.
* O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).

### <a name="values"></a>Valores

Os valores de configuração adotam as convenções a seguir:

* Os valores são cadeias de caracteres.
* Não é possível armazenar valores nulos na configuração ou associá-los a objetos.

## <a name="providers"></a>Provedores

A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.

| Provedor | Fornece a configuração de &hellip; |
| -------- | ----------------------------------- |
| [Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*) | Cofre de Chave do Azure |
| [Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure) | Configuração de Aplicativo do Azure |
| [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider) | Parâmetros de linha de comando |
| [Provedor de Configuração personalizado](#custom-configuration-provider) | Fonte personalizada |
| [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider) | Variáveis de ambiente |
| [Provedor de Configuração de Arquivo](#file-configuration-provider) | Arquivos (INI, JSON, XML) |
| [Provedor de Configuração de Chave por Arquivo](#key-per-file-configuration-provider) | Arquivos de diretório |
| [Provedor de Configuração de Memória](#memory-configuration-provider) | Coleções na memória |
| [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*) | Arquivo no diretório de perfil do usuário |

Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração. Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza. Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.

Uma sequência comum de provedores de configuração é:

1. Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)
1. [Cofre da Chave do Azure](xref:security/key-vault-configuration)
1. [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)
1. Variáveis de ambiente
1. Argumentos de linha de comando

Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.

A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a>Configurar o construtor de host com ConfigureHostConfiguration

Para configurar o construtor de host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> e forneça a configuração. `ConfigureHostConfiguration` é usado para inicializar o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> para uso posterior no processo de build. `ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Substituir a configuração anterior por argumentos de linha de comando

Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Remover provedores adicionados por CreateDefaultBuilder

Para remover os provedores adicionados por `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Consumir a configuração durante a inicialização do aplicativo

a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`. Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).

## <a name="command-line-configuration-provider"></a>Provedor de Configuração de Linha de Comando

O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.

Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.
* Variáveis de ambiente.

`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último. Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.

`CreateDefaultBuilder` age quando o host é construído. Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.

Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`. Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Abra um prompt de comando no diretório do projeto.
1. Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.
1. Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.

### <a name="arguments"></a>Argumentos

O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço. O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).

| Prefixo da chave               | {1&gt;Exemplo&lt;1}                                                |
| ------------------------ | ------------------------------------------------------ |
| Nenhum prefixo                | `CommandLineKey1=value1`                               |
| Dois traços (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Barra (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.

Exemplo de comandos:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Mapeamentos de comutador

Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave. Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, forneça um dicionário de substituições de switch para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando. Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo. Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).

Regras de chave do dicionário de mapeamentos de comutador:

* Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).
* O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.

Crie um dicionário de mapeamentos de opção. No exemplo a seguir, dois mapeamentos de opção são criados:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos. A chamada `CreateDefaultBuilder` do método `AddCommandLine` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`. A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `ConfigurationBuilder` do método `AddCommandLine` processe os dois argumentos e o dicionário de mapeamento de opções.

Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.

| Chave       | {1&gt;Valor&lt;1}             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.

| Chave               | {1&gt;Valor&lt;1}    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Provedor de Configuração de Variáveis de Ambiente

O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.

Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente. Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `DOTNET_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host genérico](xref:fundamentals/host/generic-host) e `CreateDefaultBuilder` é chamado. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.
* Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.
* Argumentos de linha de comando.

O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*. Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.

Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Chame `AddEnvironmentVariables` último para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`. O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.

Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente. Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.

Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Prefixos

As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o método `AddEnvironmentVariables`. Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

O prefixo é removido na criação dos pares chave-valor de configuração.

Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente. Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).

**Prefixos de cadeia de conexão**

A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo. As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.

| Prefixo da cadeia de conexão | Provedor |
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
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuração não criada.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |

**Exemplo**

Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:

* Nome &ndash; `CUSTOMCONNSTR_ReleaseDB`
* Valor &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Se `IConfiguration` for injetada e atribuída a um campo chamado `_config`, leia o valor:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Provedor de Configuração de Arquivo

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos. Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:

* [Provedor de Configuração INI](#ini-configuration-provider)
* [Provedor de Configuração JSON](#json-configuration-provider)
* [Provedor de Configuração XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Provedor de Configuração INI

O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.

Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

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

O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.

Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`. O método é chamado para carregar a configuração de:

* *appSettings. json* &ndash; este arquivo é lido primeiro. A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.
* *appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Variáveis de ambiente.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.
* Argumentos de linha de comando.

O Provedor de Configuração JSON é estabelecido primeiro. Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`:

* A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*. Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo. O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.
1. Execute o aplicativo de exemplo novamente no ambiente de produção:
   1. Abra o arquivo *Properties/launchSettings. JSON* .
   1. No perfil de `ConfigurationSample`, altere o valor da variável de ambiente `ASPNETCORE_ENVIRONMENT` para `Production`.
   1. Salve o arquivo e execute o aplicativo com `dotnet run` em um shell de comando.
1. As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*. O nível de log para a `Logging:LogLevel:Default` de chave é `Information`.

### <a name="xml-configuration-provider"></a>Provedor de Configuração XML

O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.

Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados. Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

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

## <a name="key-per-file-configuration-provider"></a>Provedor de Configuração de Chave por Arquivo

O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração. A chave é o nome do arquivo. O valor contém o conteúdo do arquivo. O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.

Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. O `directoryPath` para os arquivos deve ser um caminho absoluto.

As sobrecargas permitem especificar:

* Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.
* Se o diretório é opcional, e o caminho para o diretório.

O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo. Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Provedor de Configuração de Memória

O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.

Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.

No exemplo a seguir, um dicionário de configuração é criado:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado. Uma sobrecarga aceita um valor padrão.

O exemplo a seguir:

* extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`. Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.
* Digita o valor como um `int`.
* Armazena o valor na propriedade `NumberConfig` para uso pela página.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
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

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.

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

### <a name="exists"></a>Exists

Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.

## <a name="bind-to-a-class"></a>Associar a uma classe

A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*. Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object). O associador associa valores a todas as propriedades públicas de leitura/gravação do tipo fornecido. Os campos **não** estão associados.

O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

Os seguintes pares chave-valor de configuração são criados:

| Chave                   | {1&gt;Valor&lt;1}                                             |
| --------------------- | ------------------------------------------------- |
| starship:name         | USS Enterprise                                    |
| starship:registry     | NCC-1701                                          |
| starship:class        | Constituição                                      |
| starship:length       | 304,8                                             |
| starship:commissioned | False                                             |
| marca             | Paramount Pictures Corp. https://www.paramount.com |

O aplicativo de exemplo chama `GetSection` com a chave `starship`. Os pares chave-valor `starship` são isolados. O método `Bind` é chamado na subseção passando uma instância da classe `Starship`. Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a>Associar a um gráfico de objeto

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro. Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.

O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`. A instância associada é atribuída a uma propriedade para renderização:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado. O `Get<T>` é mais conveniente do que usar `Bind`. O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*

O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. Qualquer formato de matriz que expõe um segmento de chave numérico (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar a matriz a uma matriz de classe POCO.

> [!NOTE]
> A associação é fornecida por convenção. Provedores de configuração personalizados não são necessários para implementar a associação de matriz.

**Processamento de matriz na memória**

Considere as chaves de configuração e os valores mostrados na tabela a seguir.

| Chave             | {1&gt;Valor&lt;1}  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

A matriz ignora um valor para o índice &num;3. O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.

No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Os dados de configuração estão associados ao objeto:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

A sintaxe [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.

| Índice `ArrayExample.Entries` | `ArrayExample.Entries` Valor |
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

Em `ConfigureAppConfiguration`:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

O par chave-valor mostrado na tabela é carregado na configuração.

| Chave             | {1&gt;Valor&lt;1}  |
| :-------------: | :----: |
| array:entries:3 | value3 |

Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.

| Índice `ArrayExample.Entries` | `ArrayExample.Entries` Valor |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Processamento de matriz JSON**

Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero. No arquivo de configuração a seguir, `subsection` é uma matriz:

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:

| Chave                     | {1&gt;Valor&lt;1}  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Após a associação, `JsonArrayExample.Key` contém o valor `valueA`. Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.

| Índice `JsonArrayExample.Subsection` | `JsonArrayExample.Subsection` Valor |
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

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. O provedor de configuração inicializa o banco de dados quando ele está vazio. Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

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

Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*. Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:

* Cofre de Chave do Azure
* Configuração de Aplicativo do Azure
* Argumentos de linha de comando
* Provedores personalizados (instalados ou criados)
* Arquivos de diretório
* Variáveis de ambiente
* Objetos do .NET na memória
* Arquivos de configurações

Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:

```csharp
using Microsoft.Extensions.Configuration;
```

O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico. As opções usam classes para representar grupos de configurações relacionadas. Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Configuração do host versus do aplicativo

Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado. O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo. O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico. Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo. Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Outra configuração

Este tópico pertence apenas à *configuração do aplicativo*. Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:

* *Launch. json*/*launchSettings. JSON* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:
  * Em <xref:fundamentals/environments#development>.
  * No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.
* o *Web. config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="default-configuration"></a>Configuração padrão

Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host. `CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:

O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host). Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).

* A configuração do host é fornecida de:
  * Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider). O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).
* A configuração do aplicativo é fornecida de:
  * *appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * *appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).
  * [Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.
  * Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).
  * Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).

## <a name="security"></a>Segurança

Adote as seguintes práticas para proteger dados de configuração confidenciais:

* Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.
* Não use segredos de produção em ambientes de teste ou de desenvolvimento.
* Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.

Para obter mais informações, consulte estes tópicos:

* <xref:fundamentals/environments>
* <xref:security/app-secrets> &ndash; inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais. O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local. O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.

O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core. Para obter mais informações, consulte <xref:security/key-vault-configuration>.

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

Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração. Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Convenções

### <a name="sources-and-providers"></a>Origens e provedores

Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.

Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada. Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.

<xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo. <xref:Microsoft.Extensions.Configuration.IConfiguration> pode ser injetada em um Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.

Nos exemplos a seguir, o campo `_config` é usado para acessar os valores de configuração:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.

### <a name="keys"></a>Keys

As chaves de configuração adotam as convenções a seguir:

* As chaves não diferenciam maiúsculas de minúsculas. Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.
* Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.
* Chaves hierárquicas
  * Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.
  * Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas. Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.
  * No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador. Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.
* O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).

### <a name="values"></a>Valores

Os valores de configuração adotam as convenções a seguir:

* Os valores são cadeias de caracteres.
* Não é possível armazenar valores nulos na configuração ou associá-los a objetos.

## <a name="providers"></a>Provedores

A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.

| Provedor | Fornece a configuração de &hellip; |
| -------- | ----------------------------------- |
| [Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*) | Cofre de Chave do Azure |
| [Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure) | Configuração de Aplicativo do Azure |
| [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider) | Parâmetros de linha de comando |
| [Provedor de Configuração personalizado](#custom-configuration-provider) | Fonte personalizada |
| [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider) | Variáveis de ambiente |
| [Provedor de Configuração de Arquivo](#file-configuration-provider) | Arquivos (INI, JSON, XML) |
| [Provedor de Configuração de Chave por Arquivo](#key-per-file-configuration-provider) | Arquivos de diretório |
| [Provedor de Configuração de Memória](#memory-configuration-provider) | Coleções na memória |
| [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*) | Arquivo no diretório de perfil do usuário |

Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração. Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza. Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.

Uma sequência comum de provedores de configuração é:

1. Arquivos (*appsettings.json*, *appsettings.{Environment}.json*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)
1. [Cofre da Chave do Azure](xref:security/key-vault-configuration)
1. [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)
1. Variáveis de ambiente
1. Argumentos de linha de comando

Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.

A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

## <a name="configure-the-host-builder-with-useconfiguration"></a>Configurar o construtor de host com UseConfiguration

Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.

```csharp
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
```

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Substituir a configuração anterior por argumentos de linha de comando

Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Remover provedores adicionados por CreateDefaultBuilder

Para remover os provedores adicionados por `CreateDefaultBuilder`, chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Consumir a configuração durante a inicialização do aplicativo

a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`. Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).

## <a name="command-line-configuration-provider"></a>Provedor de Configuração de Linha de Comando

O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.

Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.
* Variáveis de ambiente.

`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último. Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.

`CreateDefaultBuilder` age quando o host é construído. Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.

Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`. Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Abra um prompt de comando no diretório do projeto.
1. Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.
1. Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.

### <a name="arguments"></a>Argumentos

O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço. O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).

| Prefixo da chave               | {1&gt;Exemplo&lt;1}                                                |
| ------------------------ | ------------------------------------------------------ |
| Nenhum prefixo                | `CommandLineKey1=value1`                               |
| Dois traços (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Barra (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.

Exemplo de comandos:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Mapeamentos de comutador

Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave. Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, forneça um dicionário de substituições de switch para o método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando. Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo. Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).

Regras de chave do dicionário de mapeamentos de comutador:

* Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).
* O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.

Crie um dicionário de mapeamentos de opção. No exemplo a seguir, dois mapeamentos de opção são criados:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos. A chamada `CreateDefaultBuilder` do método `AddCommandLine` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`. A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `ConfigurationBuilder` do método `AddCommandLine` processe os dois argumentos e o dicionário de mapeamento de opções.

Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.

| Chave       | {1&gt;Valor&lt;1}             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.

| Chave               | {1&gt;Valor&lt;1}    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Provedor de Configuração de Variáveis de Ambiente

O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.

Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente. Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado. Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.
* Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.
* Argumentos de linha de comando.

O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*. Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.

Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Chame `AddEnvironmentVariables` último para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`. O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.

Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente. Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.

Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Prefixos

As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o método `AddEnvironmentVariables`. Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

O prefixo é removido na criação dos pares chave-valor de configuração.

Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente. Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).

**Prefixos de cadeia de conexão**

A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo. As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.

| Prefixo da cadeia de conexão | Provedor |
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
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuração não criada.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Chave: `ConnectionStrings:{KEY}_ProviderName`:<br>Valor: `System.Data.SqlClient`  |

**Exemplo**

Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:

* Nome &ndash; `CUSTOMCONNSTR_ReleaseDB`
* Valor &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Se `IConfiguration` for injetada e atribuída a um campo chamado `_config`, leia o valor:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Provedor de Configuração de Arquivo

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos. Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:

* [Provedor de Configuração INI](#ini-configuration-provider)
* [Provedor de Configuração JSON](#json-configuration-provider)
* [Provedor de Configuração XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Provedor de Configuração INI

O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.

Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

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

O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.

Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

`AddJsonFile` é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com `CreateDefaultBuilder`. O método é chamado para carregar a configuração de:

* *appSettings. json* &ndash; este arquivo é lido primeiro. A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.
* *appSettings. {Environment}. JSON* &ndash; a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).

`CreateDefaultBuilder` também carrega:

* Variáveis de ambiente.
* [Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.
* Argumentos de linha de comando.

O Provedor de Configuração JSON é estabelecido primeiro. Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Exemplo**

O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile`:

* A primeira chamada para `AddJsonFile` carrega a configuração de *appSettings. JSON*:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*. Para *appSettings. Development. JSON* no aplicativo de exemplo, o seguinte arquivo é carregado:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Execute o aplicativo de exemplo. Abra um navegador para o aplicativo em `http://localhost:5000`.
1. A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo. O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.
1. Execute o aplicativo de exemplo novamente no ambiente de produção:
   1. Abra o arquivo *Properties/launchSettings. JSON* .
   1. No perfil de `ConfigurationSample`, altere o valor da variável de ambiente `ASPNETCORE_ENVIRONMENT` para `Production`.
   1. Salve o arquivo e execute o aplicativo com `dotnet run` em um shell de comando.
1. As configurações em *appSettings. O Development. JSON* não substitui mais as configurações em *appSettings. JSON*. O nível de log para a `Logging:LogLevel:Default` de chave é `Warning`.

### <a name="xml-configuration-provider"></a>Provedor de Configuração XML

O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.

Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

As sobrecargas permitem especificar:

* Se o arquivo é opcional.
* Se a configuração será recarregada caso o arquivo seja alterado.
* O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.

O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados. Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

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

## <a name="key-per-file-configuration-provider"></a>Provedor de Configuração de Chave por Arquivo

O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração. A chave é o nome do arquivo. O valor contém o conteúdo do arquivo. O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.

Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. O `directoryPath` para os arquivos deve ser um caminho absoluto.

As sobrecargas permitem especificar:

* Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.
* Se o diretório é opcional, e o caminho para o diretório.

O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo. Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Provedor de Configuração de Memória

O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.

Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.

Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.

No exemplo a seguir, um dicionário de configuração é criado:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado. Uma sobrecarga aceita um valor padrão.

O exemplo a seguir:

* extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`. Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.
* Digita o valor como um `int`.
* Armazena o valor na propriedade `NumberConfig` para uso pela página.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
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

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.

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

### <a name="exists"></a>Exists

Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.

## <a name="bind-to-a-class"></a>Associar a uma classe

A configuração pode ser associada a classes que representam grupos de configurações relacionadas usando o *padrão de opções*. Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

Os valores de configuração retornam como cadeias de caracteres, mas chamar <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> permite a construção de objetos [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object). O associador associa valores a todas as propriedades públicas de leitura/gravação do tipo fornecido. Os campos **não** estão associados.

O aplicativo de exemplo contém um modelo `Starship` (*Models/Starship.cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

A seção `starship` do arquivo *starship.json* cria a configuração quando o aplicativo de exemplo usa o Provedor de Configuração JSON para carregar a configuração:

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

Os seguintes pares chave-valor de configuração são criados:

| Chave                   | {1&gt;Valor&lt;1}                                             |
| --------------------- | ------------------------------------------------- |
| starship:name         | USS Enterprise                                    |
| starship:registry     | NCC-1701                                          |
| starship:class        | Constituição                                      |
| starship:length       | 304,8                                             |
| starship:commissioned | False                                             |
| marca             | Paramount Pictures Corp. https://www.paramount.com |

O aplicativo de exemplo chama `GetSection` com a chave `starship`. Os pares chave-valor `starship` são isolados. O método `Bind` é chamado na subseção passando uma instância da classe `Starship`. Depois de associar os valores de instância, a instância é atribuída a uma propriedade para renderização:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a>Associar a um gráfico de objeto

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro. Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.

O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`. A instância associada é atribuída a uma propriedade para renderização:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e retorna o tipo especificado. O `Get<T>` é mais conveniente do que usar `Bind`. O código a seguir mostra como usar `Get<T>` com o exemplo anterior, que permite que a instância associada seja diretamente atribuída à propriedade usada para renderização:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*

O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração. Qualquer formato de matriz que expõe um segmento de chave numérico (`:0:`, `:1:`, &hellip; `:{n}:`) é capaz de associar a matriz a uma matriz de classe POCO.

> [!NOTE]
> A associação é fornecida por convenção. Provedores de configuração personalizados não são necessários para implementar a associação de matriz.

**Processamento de matriz na memória**

Considere as chaves de configuração e os valores mostrados na tabela a seguir.

| Chave             | {1&gt;Valor&lt;1}  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

A matriz ignora um valor para o índice &num;3. O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.

No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Os dados de configuração estão associados ao objeto:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

A sintaxe [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) também pode ser usada, o que resulta em um código mais compacto:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.

| Índice `ArrayExample.Entries` | `ArrayExample.Entries` Valor |
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

Em `ConfigureAppConfiguration`:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

O par chave-valor mostrado na tabela é carregado na configuração.

| Chave             | {1&gt;Valor&lt;1}  |
| :-------------: | :----: |
| array:entries:3 | value3 |

Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.

| Índice `ArrayExample.Entries` | `ArrayExample.Entries` Valor |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Processamento de matriz JSON**

Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero. No arquivo de configuração a seguir, `subsection` é uma matriz:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:

| Chave                     | {1&gt;Valor&lt;1}  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Após a associação, `JsonArrayExample.Key` contém o valor `valueA`. Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.

| Índice `JsonArrayExample.Subsection` | `JsonArrayExample.Subsection` Valor |
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

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. O provedor de configuração inicializa o banco de dados quando ele está vazio.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

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

Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).

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

::: moniker-end
