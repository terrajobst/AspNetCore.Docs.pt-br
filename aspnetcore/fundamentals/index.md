---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/index
ms.openlocfilehash: 7173a732a04bf3e598adef298fa9120c15dd52fb
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799380"
---
# <a name="aspnet-core-fundamentals"></a>Conceitos básicos do ASP.NET Core

Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.

## <a name="the-startup-class"></a>A classe Startup

A classe `Startup` é o local em que:

* Os serviços exigidos pelo aplicativo são configurados.
* O pipeline de tratamento de solicitação é definido.

*Serviços* são componentes usados pelo aplicativo. Por exemplo, um componente de registro em log é um serviço. Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.

O pipeline de tratamento de solicitações é composto como uma série de componentes de *middleware*. Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS. Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação. O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.

Aqui está um exemplo de classe `Startup`:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injeção de dependência (serviços)

O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo. Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário. O parâmetro pode ser o tipo de serviço ou uma interface. O sistema de DI fornece o serviço em runtime.

Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core. A linha realçada é um exemplo de injeção de construtor:

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.

Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware. Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.

Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`. Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.

O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.

Para obter mais informações, consulte <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Um aplicativo ASP.NET Core cria um *host* na inicialização. O host é um objeto que encapsula todos os recursos do aplicativo, como:

* Uma implementação do servidor HTTP
* Componentes de middleware
* Registrando em log
* DI
* Configuração

O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.

::: moniker range=">= aspnetcore-3.0"

Dois hosts estão disponíveis: o Host Genérico e o Host Web. O Host Genérico é o recomendado, e o Host Web está disponível apenas para compatibilidade com versões anteriores.

O código para criar um host está em `Program.Main`:

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

Os métodos `CreateDefaultBuilder` e `ConfigureWebHostDefaults` configuram um host com as opções usadas com frequência, como as seguintes:

* Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.
* Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.
* Envio da saída de log para os provedores de console e de depuração.

Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dois hosts estão disponíveis: o Host Web e o Host Genérico. No ASP.NET Core 2.x, o Host Genérico é somente para cenários não Web.

O código para criar um host está em `Program.Main`:

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

O método `CreateDefaultBuilder` configura um host com as opções usadas com frequência, como as seguintes:

* Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.
* Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.
* Envio da saída de log para os provedores de console e de depuração.

Para obter mais informações, consulte <xref:fundamentals/host/web-host>.

::: moniker-end

### <a name="non-web-scenarios"></a>Cenários não Web

O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos. Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servidores

Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP. O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

O ASP.NET Core vem com as seguintes implementações de servidor:

* *Kestrel* é um servidor Web multiplataforma. O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/). No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.
* O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS. Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.
* *HTTP.sys* é um servidor para Windows que não é usado com IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

O ASP.NET Core vem com as seguintes implementações de servidor:

* *Kestrel* é um servidor Web multiplataforma. O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/). No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.
* *HTTP.sys* é um servidor para Windows que não é usado com IIS.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

Para obter mais informações, consulte <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuração

O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração. Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando. Você também pode escrever seus próprios provedores de configuração personalizados.

Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente. Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*. Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.

Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets). Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).

Para obter mais informações, consulte <xref:fundamentals/configuration/index>.

## <a name="options"></a>Opções

Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração. O padrão de opções usa classes para representar grupos de configurações relacionadas.

Por exemplo, o código a seguir define as opções de WebSockets:

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

Para obter mais informações, consulte <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Ambientes

Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core. Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`. O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`. O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.

O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

Para obter mais informações, consulte <xref:fundamentals/environments>.

## <a name="logging"></a>Registrando em log

O ASP.NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados. Provedores disponíveis incluem os seguintes:

* Console
* Depurar
* Rastreamento de Eventos no Windows
* Log de Eventos do Windows
* TraceSource
* Serviço de Aplicativo do Azure
* Azure Application Insights

Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.

Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log. Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados. Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Para obter mais informações, consulte <xref:fundamentals/logging/index>.

## <a name="routing"></a>Roteamento

Um *rota* é um padrão de URL mapeado para um manipulador. O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware. O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="error-handling"></a>Tratamento de erros

O ASP.NET Core tem recursos internos para tratamento de erros, como:

* Uma página de exceção do desenvolvedor
* Páginas de erro personalizadas
* Páginas de código de status estático
* Tratamento de exceção na inicialização

Para obter mais informações, consulte <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Fazer solicitações HTTP

Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`. O alocador:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, um cliente *github* pode ser registrado e configurado para acessar o GitHub. Um cliente padrão pode ser registrado para outras finalidades.
* Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída. Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core. O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.
* Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.
* Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.
* Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

Para obter mais informações, consulte <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Raiz do conteúdo

A raiz do conteúdo é o caminho base para:

* Executável que hospeda o aplicativo ( *. exe*).
* Assemblies compilados que compõem o aplicativo ( *. dll*).
* Arquivos de conteúdo que não são de código usados pelo aplicativo, como:
  * Arquivos Razor ( *. cshtml*, *. Razor*)
  * Arquivos de configuração ( *. JSON*, *. xml*)
  * Arquivos de dados ( *. db*)
* [Raiz da Web](#web-root), normalmente a pasta *wwwroot* publicada.

Durante o desenvolvimento:

* A raiz do conteúdo assume como padrão o diretório raiz do projeto.
* O diretório raiz do projeto é usado para criar:
  * Caminho para os arquivos de conteúdo sem código do aplicativo no diretório raiz do projeto.
  * [Raiz da Web](#web-root), normalmente a pasta *wwwroot* no diretório raiz do projeto.

::: moniker range=">= aspnetcore-3.0"

Um caminho de raiz de conteúdo alternativo pode ser especificado ao [criar o host](#host). Para obter mais informações, consulte <xref:fundamentals/host/generic-host#contentrootpath>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um caminho de raiz de conteúdo alternativo pode ser especificado ao [criar o host](#host). Para obter mais informações, consulte <xref:fundamentals/host/web-host#content-root>.

::: moniker-end

## <a name="web-root"></a>Raiz da Web

A raiz da Web é o caminho base para arquivos de recurso estáticos, não de código e públicos, como:

* Folhas de estilo ( *. css*)
* JavaScript ( *. js*)
* Imagens ( *. png*, *. jpg*)

Os arquivos estáticos são servidos apenas por padrão no diretório raiz da Web (e subdiretórios).

::: moniker range=">= aspnetcore-3.0"

O caminho raiz da Web tem como padrão *{Content root}/wwwroot*, mas uma raiz da Web diferente pode ser especificada ao [criar o host](#host). Para obter mais informações, consulte <xref:fundamentals/host/generic-host#webroot>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O caminho raiz da Web tem como padrão *{Content root}/wwwroot*, mas uma raiz da Web diferente pode ser especificada ao [criar o host](#host). Para obter mais informações, confira [Diretório base](xref:fundamentals/host/web-host#web-root).

::: moniker-end

Impedir a publicação de arquivos em *wwwroot* com o [\<conteúdo > item de projeto](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo de projeto. O exemplo a seguir impede a publicação de conteúdo no diretório *wwwroot/local* e nos subpastas:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Em arquivos Razor ( *. cshtml*), a barra de tils (`~/`) aponta para a raiz da Web. Um caminho que começa com `~/` é conhecido como um *caminho virtual*.

Para obter mais informações, consulte <xref:fundamentals/static-files>.
