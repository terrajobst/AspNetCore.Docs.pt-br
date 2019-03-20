---
title: Hospedar e implantar Componentes Razor
author: guardrex
description: 'Descubra como hospedar e implantar aplicativos dos Componentes Razor e do Blazor usando o ASP.NET Core, a CDN (Rede de Distribuição de Conteúdo), servidores de arquivos e páginas do GitHub.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: host-and-deploy/razor-components/index
---
# <a name="host-and-deploy-razor-components"></a>Hospedar e implantar Componentes Razor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a>Publique o aplicativo

Os aplicativos são publicados para implantação na configuração de versão com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish). Um IDE pode manipular a execução do comando `dotnet publish` automaticamente usando recursos de publicação internos, para que não seja necessário executar o comando manualmente usando um prompt de comando, dependendo das ferramentas de desenvolvimento em uso.

```console
dotnet publish -c Release
```

O `dotnet publish` dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação. Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento. A implantação é criada na pasta */bin/Release/&lt;target-framework&gt;/publish*.

Os ativos na pasta *publish* são implantados no servidor Web. A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.

## <a name="host-configuration-values"></a>Valores de configuração do host

Os aplicativos dos Componentes Razor que usam o [modelo de hospedagem do lado do servidor](xref:razor-components/hosting-models#server-side-hosting-model) podem aceitar os [valores de configuração do host da Web](xref:fundamentals/host/web-host#host-configuration-values).

Os aplicativos do Blazor que usam o [modelo de hospedagem do lado do cliente](xref:razor-components/hosting-models#client-side-hosting-model) podem aceitar os seguintes valores de configuração do host como argumentos de linha de comando no tempo de execução no ambiente de desenvolvimento.

### <a name="content-root"></a>Raiz do conteúdo

O argumento `--contentroot` define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Essa configuração é obtida ao executar o aplicativo com o Depurador do Visual Studio e ao executar o aplicativo em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a>Caminho base

O argumento `--pathbase` define o caminho base do aplicativo para um aplicativo executado localmente com um caminho virtual não raiz (a tag `href` `<base>` é definida como um caminho diferente de `/` para preparo e produção). Para obter mais informações, confira a seção [Caminho base do aplicativo](#app-base-path).

> [!IMPORTANT]
> Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`. Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/" />` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Essa configuração é obtida ao executar o aplicativo com o Depurador do Visual Studio e ao executar o aplicativo em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a>URLs

O argumento `--urls` indica os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**. Essa configuração é obtida ao executar o aplicativo com o Depurador do Visual Studio e ao executar o aplicativo em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**. A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a>Implantar um aplicativo do Blazor do lado do cliente

Com o [modelo de hospedagem do lado do cliente](xref:razor-components/hosting-models#client-side-hosting-model):

* O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.
* O aplicativo é executado diretamente no thread da interface do usuário do navegador. Há suporte para todas as estratégias a seguir:
  * O aplicativo do Blazor é atendido por um aplicativo ASP.NET Core. Abordado na seção [Implantação hospedada do Blazor do lado do cliente com o ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core).
  * O aplicativo do Blazor é colocado em um serviço ou um servidor Web de hospedagem estático, em que o .NET não é usado para atender ao aplicativo do Blazor. Abordado na seção [Implantação autônoma do Blazor do lado do cliente](#client-side-blazor-standalone-deployment).
  
### <a name="configure-the-linker"></a>Configurar o vinculador

O Blazor executa a vinculação de IL (linguagem intermediária) em cada build para remover a IL desnecessária dos assemblies de saída. Você pode controlar a vinculação de assembly no build. Para obter mais informações, consulte <xref:host-and-deploy/razor-components/configure-linker>.

### <a name="rewrite-urls-for-correct-routing"></a>Reescrever as URLs para obter o roteamento correto

O roteamento de solicitações para componentes de página em um aplicativo do lado do cliente não é tão simples quanto o roteamento de solicitações para um aplicativo hospedado do lado do servidor. Considere um aplicativo do lado do cliente com duas páginas:

* **_Main.cshtml_** &ndash; É carregado na raiz do aplicativo e contém um link para a página Sobre (`href="About"`).
* **_About.cshtml_** &ndash; Página Sobre.

Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):

1. O navegador faz uma solicitação.
1. A página padrão é retornada, que é geralmente é *index.html*.
1. A *index.html* inicia o aplicativo.
1. O roteador do Blazor é carregado e a página Principal do Razor (*Main.cshtml*) é exibida.

Na página Principal, é possível carregar a página Sobre selecionando o link para ela. A seleção do link para a página Sobre funciona no cliente porque o roteador do Blazor impede que o navegador faça uma solicitação na Internet para `www.contoso.com` de `About` e atende à própria página Sobre. Todas as solicitações de páginas internas *no aplicativo do lado do cliente* funcionam da mesma maneira: Não são disparadas solicitações baseadas em navegador para os recursos hospedados no servidor na Internet. O roteador trata das solicitações internamente.

Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará. Esse recurso não existe no host do aplicativo na Internet, portanto, uma resposta *404 Não Encontrado* é retornada.

Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*. Quando a *index.html* for retornada, o roteador do lado do cliente do aplicativo assumirá o controle e responderá com o recurso correto.

### <a name="app-base-path"></a>Caminho base do aplicativo

O caminho base do aplicativo é o caminho raiz do aplicativo virtual no servidor. Por exemplo, um aplicativo que reside no servidor Contoso em uma pasta virtual em `/CoolApp/` é acessado em `https://www.contoso.com/CoolApp` e tem o caminho base virtual `/CoolApp/`. Quando o caminho base do aplicativo é definido como `CoolApp/`, o aplicativo fica ciente de onde ele reside virtualmente no servidor. O aplicativo pode usar o caminho base para construir URLs relativas à raiz do aplicativo de um componente que não esteja no diretório raiz. Isso permite que os componentes que existem em diferentes níveis da estrutura de diretório criem links para outros recursos em locais em todo o aplicativo. O caminho base do aplicativo também é usado para interceptar cliques em hiperlink em que o destino `href` do link está dentro do espaço do URI do caminho base do aplicativo. O roteador do Blazor manipula a navegação interna.

Em muitos cenários de hospedagem, o caminho virtual do servidor para o aplicativo é a raiz do aplicativo. Nesses casos, o caminho base do aplicativo é uma barra invertida (`<base href="/" />`), que é a configuração padrão de um aplicativo. Em outros cenários de hospedagem, como Páginas do GitHub e diretórios virtuais ou subaplicativos do IIS, o caminho base do aplicativo precisa ser definido como o caminho virtual do servidor para o aplicativo. Para definir o caminho base do aplicativo, adicione ou atualize a tag `<base>` na *index.html* encontrada nos elementos da tag `<head>`. Defina o valor de atributo `href` como `<virtual-path>/` (a barra à direita é necessária), em que `<virtual-path>/` é o caminho raiz do aplicativo virtual completo no servidor do aplicativo. No exemplo anterior, o caminho virtual é definido como `CoolApp/`: `<base href="CoolApp/" />`.

No caso de um aplicativo com um caminho virtual não raiz configurado (por exemplo, `<base href="CoolApp/" />`), o aplicativo não consegue localizar seus recursos *quando é executado localmente*. Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no tempo de execução.

Para passar o argumento base de caminho com o caminho raiz (`/`) ao executar o aplicativo localmente, execute o seguinte comando no diretório do aplicativo:

```console
dotnet run --pathbase=/CoolApp
```

O aplicativo responde localmente em `http://localhost:port/CoolApp`.

Para obter mais informações, confira a seção de [valor de configuração do host base de caminho](#path-base).

> [!IMPORTANT]
> Se um aplicativo usa o [modelo de hospedagem do lado do cliente](xref:razor-components/hosting-models#client-side-hosting-model) (com base no modelo de projeto **Blazor**) e é hospedado como um subaplicativo do IIS em um aplicativo do ASP.NET Core, é importante desabilitar o manipulador de módulo do ASP.NET Core herdado ou verificar se a seção `<handlers>` do aplicativo raiz (pai) no arquivo *web.config* não é herdada pelo subaplicativo.
>
> Remova o manipulador do arquivo *web.config* publicado do aplicativo adicionando uma seção `<handlers>` ao arquivo:
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> Como alternativa, desabilite a herança da seção `<system.webServer>` do aplicativo raiz (pai) usando um elemento `<location>` com `inheritInChildApplications` definido como `false`:
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> A ação de remover o manipulador ou desabilitar a herança é realizada além da ação da configurar o caminho base do aplicativo, conforme descrito nesta seção. Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a>Implantação hospedada do Blazor do lado do cliente com o ASP.NET Core

Uma *implantação hospedada* atende ao aplicativo do Blazor do lado do cliente para navegadores de um [aplicativo do ASP.NET Core](xref:index) que é executado em um servidor.

O aplicativo do Blazor é incluído com o aplicativo do ASP.NET Core na saída publicada para que ambos sejam implantados juntos. É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core. Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto **Blazor (hospedado no ASP.NET Core)** (modelo `blazorhosted` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).

Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.

Para obter informações de como implantar o Serviço de Aplicativo do Azure, confira os tópicos a seguir:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.

### <a name="client-side-blazor-standalone-deployment"></a>Implantação autônoma do Blazor do lado do cliente

Uma *implantação autônoma* atende ao aplicativo do Blazor do lado do cliente como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes. Não é usado é um servidor Web para atender ao aplicativo do Blazor.

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a>Hospedagem autônoma do Blazor do lado do cliente com o IIS

O IIS é um servidor de arquivos estático com capacidade para aplicativos do Blazor. Para configurar o IIS para hospedar o Blazor, confira [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Criar um site estático no IIS).

Os ativos publicados são criados na pasta *\\bin\\Release\\&lt;target-framework&gt;\\publish*. Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.

**web.config**

Quando um projeto Blazor é publicado, um arquivo *web.config* é criado com a seguinte configuração do IIS:

* Os tipos MIME são definidos para as seguintes extensões de arquivo:
  * *\*.dll*: `application/octet-stream`
  * *\*.json*: `application/json`
  * *\*.wasm*: `application/wasm`
  * *\*.woff*: `application/font-woff`
  * *\*.woff2*: `application/font-woff`
* A compactação HTTP está habilitada para os seguintes tipos MIME:
  * `application/octet-stream`
  * `application/wasm`
* As regras do Módulo de Reescrita de URL são estabelecidas:
  * Atender ao subdiretório em que residem os ativos estáticos do aplicativo (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).
  * Criar o roteamento de fallback do SPA, de modo que as solicitações de ativos que não sejam arquivos sejam redirecionadas ao documento padrão do aplicativo na pasta de ativos estáticos dele (*&lt;assembly_name&gt;\\dist\\index.html*).

**Instalar o Módulo de Reescrita de URL**

O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs. O módulo não está instalado por padrão e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS). O módulo precisa ser baixado do site do IIS. Use o Web Platform Installer para instalar o módulo:

1. Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI. Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.
1. Copie o instalador para o servidor. Execute o instalador. Selecione o botão **Instalar** e aceite os termos de licença. Uma reinicialização do servidor não será necessária após a conclusão da instalação.

**Configurar o site**

Defina o **Caminho físico** do site como a pasta do aplicativo. A pasta contém:

* O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.
* A pasta de ativos estática do aplicativo.

**Solução de problemas**

Se um *500 Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Reescrita de URL está instalado. Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS. Isso impede que o Gerenciador do IIS carregue a configuração do site e que o site atenda aos arquivos estáticos do Blazor.

Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:host-and-deploy/iis/troubleshoot>.

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a>Hospedagem autônoma do Blazor do lado do cliente com o Nginx

O arquivo *nginx.conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *Index.html* sempre que ele não puder encontrar um arquivo correspondente no disco.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a>Hospedagem autônoma do Blazor do lado do cliente no Docker

Para hospedar o Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem do Nginx baseada no Alpine. Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.

Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a>Hospedagem autônoma do Blazor do lado do cliente com as Páginas do GitHub

Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*. Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)). Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).

Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*. Defina o valor do atributo `<repository-name>/` do `href`, em que `<repository-name>/` é o nome do repositório do GitHub.

## <a name="deploy-a-server-side-razor-components-app"></a>Implantar um aplicativo dos Componentes Razor do lado do servidor

Com o [modelo de hospedagem do lado do servidor](xref:razor-components/hosting-models#server-side-hosting-model), os Componentes Razor são executados no servidor de dentro de um aplicativo ASP.NET Core. As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão SignalR.

O aplicativo é incluído com o aplicativo do ASP.NET Core na saída publicada para que os dois aplicativos sejam implantados juntos. É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core. Para uma implantação do lado do servidor, o Visual Studio inclui o modelo de projeto **Blazor (do lado do servidor no ASP.NET Core)** (modelo `blazorserver` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

Quando o aplicativo do ASP.NET Core é publicado, o aplicativo dos Componentes Razor é incluído na saída publicada para que ambos possam ser implantados juntos. Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.

Para obter informações de como implantar o Serviço de Aplicativo do Azure, confira os tópicos a seguir:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.
