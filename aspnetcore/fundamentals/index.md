---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/index
---
# <a name="aspnet-core-fundamentals"></a>Conceitos básicos do ASP.NET Core

Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.

## <a name="the-startup-class"></a>A classe Startup

A classe `Startup` é o local em que:

* Quaisquer serviços exigidos pelo aplicativo são configurados.
* O pipeline de tratamento de solicitação é definido.

* Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`. *Serviços* são componentes usados pelo aplicativo. Por exemplo, um objeto de contexto do Entity Framework Core é um serviço.
* O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`. O pipeline é composto como uma série de componentes de *middleware*. Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS. Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.

::: moniker range=">= aspnetcore-2.0"

Aqui está um exemplo de classe `Startup`:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

::: moniker-end

Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).

## <a name="dependency-injection-services"></a>Injeção de dependência (serviços)

O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo. Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário. O parâmetro pode ser o tipo de serviço ou uma interface. O sistema de DI fornece o serviço em tempo de execução.

::: moniker range=">= aspnetcore-2.0"

Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core. A linha realçada é um exemplo de injeção de construtor:

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

::: moniker-end

Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.

Para obter mais informações, consulte [Injeção de dependência](xref:fundamentals/dependency-injection).

## <a name="middleware"></a>Middleware

O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware. Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.

Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`. Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.

::: moniker range=">= aspnetcore-2.0"

O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

::: moniker-end

O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.

Para obter mais informações, veja [Middleware](xref:fundamentals/middleware/index).

<a id="host"/>

## <a name="the-host"></a>O host

Um aplicativo ASP.NET Core cria um *host* na inicialização. O host é um objeto que encapsula todos os recursos do aplicativo, como:

* Uma implementação do servidor HTTP
* Componentes de middleware
* Registrando em log
* DI
* Configuração

O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.

O código para criar um host está em `Program.Main` e segue o [padrão de construtor](https://wikipedia.org/wiki/Builder_pattern). Métodos são chamados para configurar cada recurso que faz parte do host. Um método construtor é chamado para reunir tudo e instanciar o objeto de host.

::: moniker range="<= aspnetcore-2.2"

O ASP.NET Core 2.x usa o Host da Web (a classe `WebHost`) para aplicativos Web. A estrutura fornece métodos de extensão `CreateDefaultBuilder` que configuram um host com opções comumente usadas, como as seguintes:

* Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.
* Carregamento da configuração de *appsettings.json*, de variáveis de ambiente, de argumentos de linha de comando e de outras fontes.
* Envio da saída de log para os provedores de console e de depuração.

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

Aqui está um exemplo de código que cria um host:

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

Para obter mais informações, confira [Host da Web](xref:fundamentals/host/web-host).

::: moniker-end

::: moniker range="> aspnetcore-2.2"

No ASP.NET Core 3.0, o Host da Web (classe `WebHost`) ou o Host Genérico (classe `Host`) podem ser usados em um aplicativo Web. O Host Genérico é o recomendado, e o Host da Web está disponível para compatibilidade com versões anteriores.

A estrutura fornece os métodos de extensão `CreateDefaultBuilder` e `ConfigureWebHostDefaults` que configuram um host com opções comumente usadas, como as seguintes:

* Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.
* Carregamento da configuração de *appsettings.json*, *appsettings.[EnvironmentName].json*, de variáveis de ambiente e de argumentos de linha de comando.
* Envio da saída de log para os provedores de console e de depuração.

Aqui está um exemplo de código que cria um host. Os métodos de extensão que configuram o host com as opções mais usadas são realçados.

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

Para obter mais informações, veja [Host Genérico](xref:fundamentals/host/generic-host) e [Host da Web](xref:fundamentals/host/web-host)

::: moniker-end

### <a name="advanced-host-scenarios"></a>Cenários avançados de host

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

O Host da Web foi projetado para incluir uma implementação de servidor HTTP, que não é necessária para outros tipos de aplicativos .NET. A partir da versão 2.1, o Host Genérico (classe `Host`) está disponível para uso por qualquer aplicativo .NET Core, não apenas para aplicativos ASP.NET Core. O Host Genérico permite que você use funcionalidades abrangentes como registro em log, DI, configuração e gerenciamento de tempo de vida de aplicativo em outros tipos de aplicativos. Para obter mais informações, veja [Host Genérico](xref:fundamentals/host/generic-host).

::: moniker-end

::: moniker range="> aspnetcore-2.2"

O Host Genérico está disponível para uso por qualquer aplicativo .NET Core, não apenas para aplicativos ASP.NET Core. O Host Genérico permite que você use funcionalidades abrangentes como registro em log, DI, configuração e gerenciamento de tempo de vida de aplicativo em outros tipos de aplicativos. Para obter mais informações, veja [Host Genérico](xref:fundamentals/host/generic-host).

::: moniker-end

Você também pode usar o host para executar tarefas em segundo plano. Para obter mais informações, veja [Tarefas em segundo plano](xref:fundamentals/host/hosted-services).

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

O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*. No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet. O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](http://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

Para obter mais informações, veja [Servidores](xref:fundamentals/servers/index).

## <a name="configuration"></a>Configuração

O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração. Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando. Você também pode escrever seus próprios provedores de configuração personalizados.

Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente. Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*. Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.

Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets). Para segredos de produção, recomendamos o [Azure Key Vault](/aspnet/core/security/key-vault-configuration).

Para obter mais informações, consulte [Configuração](xref:fundamentals/configuration/index).

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

Para obter mais informações, veja [Opções](xref:fundamentals/configuration/options).

## <a name="environments"></a>Ambientes

Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core. Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`. O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`. O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.

::: moniker range=">= aspnetcore-2.0"

O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

::: moniker-end

Para obter mais informações, veja [Ambientes](xref:fundamentals/environments).

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

::: moniker range=">= aspnetcore-2.0"

Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

::: moniker-end

A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log. Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados. Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Para obter mais informações, veja [Registro em log](xref:fundamentals/logging/index).

## <a name="routing"></a>Roteamento

Um *rota* é um padrão de URL mapeado para um manipulador. O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware. O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.

Para obter mais informações, consulte [Roteamento](xref:fundamentals/routing).

## <a name="error-handling"></a>Tratamento de erros

O ASP.NET Core tem recursos internos para tratamento de erros, como:

* Uma página de exceção do desenvolvedor
* Páginas de erro personalizadas
* Páginas de código de status estático
* Tratamento de exceção na inicialização

Para obter mais informações, veja [Tratamento de erro](xref:fundamentals/error-handling).

::: moniker range=">= aspnetcore-2.1"

## <a name="make-http-requests"></a>Fazer solicitações HTTP

Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`. O alocador:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, um cliente *github* pode ser registrado e configurado para acessar o GitHub. Um cliente padrão pode ser registrado para outras finalidades.
* Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída. Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core. O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.
* Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.
* Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.
* Adiciona uma experiência de registro em log configurável (via *ILogger*) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

Para obter mais informações, veja [Fazer solicitações HTTP](xref:fundamentals/http-requests).

::: moniker-end

## <a name="content-root"></a>Raiz do conteúdo

A raiz do conteúdo é o caminho base para qualquer conteúdo privado usado pelo aplicativo, como seus arquivos do Razor. Por padrão, a raiz do conteúdo é o caminho base do executável que hospeda o aplicativo. Um local alternativo pode ser especificado ao [criar o host](#host).

::: moniker range="<= aspnetcore-2.2"

Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/web-host#content-root).

::: moniker-end

::: moniker range="> aspnetcore-2.2"

Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/generic-host#content-root).

::: moniker-end

## <a name="web-root"></a>Raiz da Web

A raiz Web (também conhecida como *webroot*) é o caminho base para recursos públicos e estáticos como CSS, JavaScript e arquivos de imagem. O middleware de arquivos estáticos apenas veiculará arquivos do diretório raiz Web (e seus subdiretórios) por padrão. O caminho da raiz Web assume como padrão *\<raiz do conteúdo>/wwwroot*, mas é possível especificar um local diferente ao [criar o host](#host).

Em arquivos (*.cshtml*) do Razor, o til-barra `~/` aponta para a raiz Web. Caminhos que começam com `~/` são denominados caminhos virtuais.

Para obter mais informações, veja [Arquivos estáticos](xref:fundamentals/static-files).
