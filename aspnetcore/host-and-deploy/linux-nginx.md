---
title: Host ASP.NET Core no Linux com Nginx
author: guardrex
description: Saiba como configurar o Nginx como um proxy reverso no Ubuntu 16.04 para encaminhar o tráfego HTTP para um aplicativo Web ASP.NET Core em execução no Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 1a299cbd5fb9d971176d7d440efdad68e3780231
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809335"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Host ASP.NET Core no Linux com Nginx

Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Este guia explica como configurar um ambiente ASP.NET Core pronto para produção em um servidor Ubuntu 16.04. Essas instruções provavelmente funcionarão com versões mais recentes do Ubuntu, mas as instruções não foram testadas com versões mais recentes.

Para saber mais sobre outras distribuições do Linux compatíveis com o ASP.NET Core, veja [Pré-requisitos para o .NET Core no Linux](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Para Ubuntu 14.04, o *supervisord* é recomendado como uma solução para monitorar o processo do Kestrel. O *systemd* não está disponível no Ubuntu 14.04. Para obter instruções Ubuntu 14.04, veja a [versão anterior deste tópico](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

Este guia:

* Coloca um aplicativo ASP.NET Core existente em um servidor proxy reverso.
* Configura o servidor proxy reverso para encaminhar solicitações ao servidor Web do Kestrel.
* Assegura que o aplicativo Web seja executado na inicialização como um daemon.
* Configura uma ferramenta de gerenciamento de processo para ajudar a reiniciar o aplicativo Web.

## <a name="prerequisites"></a>Pré-requisitos

1. Acesso a um servidor Ubuntu 16.04 com uma conta de usuário padrão com privilégio sudo.
1. Instale o tempo de execução do .NET Core no servidor.
   1. Acesse a [página Todos os Downloads do .NET Core](https://www.microsoft.com/net/download/all).
   1. Selecione o tempo de execução não de versão prévia mais recente da lista em **Tempo de Execução**.
   1. Selecione e siga as instruções para Ubuntu que correspondem à versão Ubuntu do servidor.
1. Um aplicativo ASP.NET Core existente.

## <a name="publish-and-copy-over-the-app"></a>Publicar e copiar o aplicativo

Configurar o aplicativo para um [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Se o aplicativo for executado localmente e não estiver configurado para fazer conexões seguras (HTTPS), adote uma das seguintes abordagens:

* Configure o aplicativo para lidar com conexões seguras locais. Para obter mais informações, veja a seção [Configuração de HTTPS](#https-configuration).
* Remova `https://localhost:5001` (se houver) da propriedade `applicationUrl` no arquivo *Properties/launchSettings.json*.

Execute [dotnet publish](/dotnet/core/tools/dotnet-publish) do ambiente de desenvolvimento para empacotar um aplicativo em um diretório (por exemplo, *bin/Release/&lt;target_framework_moniker&gt;/publish*) que pode ser executado no servidor:

```console
dotnet publish --configuration Release
```

O aplicativo também poderá ser publicado como uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd) se você preferir não manter o tempo de execução do .NET Core no servidor.

Copie o aplicativo ASP.NET Core para o servidor usando uma ferramenta que se integre ao fluxo de trabalho da organização (por exemplo, SCP, SFTP). É comum para localizar os aplicativos Web no diretório *var* (por exemplo, *var/www/helloapp*).

> [!NOTE]
> Em um cenário de implantação de produção, um fluxo de trabalho de integração contínua faz o trabalho de publicar o aplicativo e copiar os ativos para o servidor.

Teste o aplicativo:

1. Na linha de comando, execute o aplicativo: `dotnet <app_assembly>.dll`.
1. Em um navegador, vá para `http://<serveraddress>:<port>` para verificar se o aplicativo funciona no Linux localmente.

## <a name="configure-a-reverse-proxy-server"></a>Configurar um servidor proxy reverso

Um proxy reverso é uma configuração comum para atender a aplicativos Web dinâmicos. Um proxy reverso encerra a solicitação HTTP e a encaminha para o aplicativo ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Usar um servidor proxy reverso

O Kestrel é excelente para servir conteúdo dinâmico do ASP.NET Core. No entanto, as funcionalidades de servidor Web não têm tantos recursos quanto servidores como IIS, Apache ou Nginx. Um servidor proxy reverso pode descarregar trabalho como servir conteúdo estático, armazenar solicitações em cache, compactar solicitações e terminar HTTPS do servidor HTTP. Um servidor proxy reverso pode residir em um computador dedicado ou pode ser implantado junto com um servidor HTTP.

Para os fins deste guia, uma única instância de Nginx é usada. Ela é executada no mesmo servidor, junto com o servidor HTTP. Com base nos requisitos, uma configuração diferente pode ser escolhida.

Uma vez que as solicitações são encaminhadas pelo proxy reverso, use o [Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/). O middleware atualiza o `Request.Scheme` usando o cabeçalho `X-Forwarded-Proto`, de forma que URIs de redirecionamento e outras políticas de segurança funcionam corretamente.

Qualquer componente que dependa do esquema, como autenticação, geração de link, redirecionamentos e localização geográfica, deverá ser colocado depois de invocar o Middleware de Cabeçalhos Encaminhados. Como regra geral, o Middleware de Cabeçalhos Encaminhados deve ser executado antes de outro middleware, exceto middleware de tratamento de erro e de diagnóstico. Essa ordenação garantirá que o middleware conte com informações de cabeçalhos encaminhadas que podem consumir os valores de cabeçalho para processamento.

Invoque o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure` antes de chamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ou outro middleware de esquema de autenticação semelhante. Configure o middleware para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, os cabeçalhos padrão para encaminhar serão `None`.

Os proxies em execução em endereços de loopback (127.0.0.0/8, [::1]), incluindo o endereço de host local padrão (127.0.0.1), são confiáveis por padrão. Se outros proxies ou redes confiáveis em que a organização trata solicitações entre a Internet e o servidor Web, adicione-os à lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. O exemplo a seguir adiciona um servidor proxy confiável no endereço IP 10.0.0.100 ao Middleware de cabeçalhos encaminhados `KnownProxies` em `Startup.ConfigureServices`:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Instalar o Nginx

Use `apt-get` para instalar o Nginx. O instalador cria um script de inicialização *systemd* que executa o Nginx como daemon na inicialização do sistema. Siga as instruções de instalação do Ubuntu no [Nginx: pacotes Debian/Ubuntu oficiais](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Se módulos Nginx opcionais forem exigidos, poderá haver necessidade de criar o Nginx da origem.

Já que Nginx foi instalado pela primeira vez, inicie-o explicitamente executando:

```bash
sudo service nginx start
```

Verifique se um navegador exibe a página de aterrissagem padrão do Nginx. A página de aterrissagem é acessível em `http://<server_IP_address>/index.nginx-debian.html`.

### <a name="configure-nginx"></a>Configurar o Nginx

Para configurar o Nginx como um proxy inverso para encaminhar solicitações para o nosso aplicativo ASP.NET Core, modifique */etc/nginx/sites-available/default*. Abra-o em um editor de texto arquivo e substitua o conteúdo pelo mostrado a seguir:

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Quando nenhum `server_name` corresponde, o Nginx usa o servidor padrão. Se nenhum servidor padrão é definido, o primeiro servidor no arquivo de configuração é o servidor padrão. Como prática recomendada, adicione um servidor padrão específico que retorna um código de status 444 no arquivo de configuração. Um exemplo de configuração de servidor padrão é:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

Com o servidor padrão e o arquivo de configuração anterior, o Nginx aceita tráfego público na porta 80 com um cabeçalho de host `example.com` ou `*.example.com`. Solicitações que não correspondam a esses hosts não serão encaminhadas para o Kestrel. O Nginx encaminha as solicitações correspondentes para o Kestrel em `http://localhost:5000`. Veja [Como o nginx processa uma solicitação](https://nginx.org/docs/http/request_processing.html) para obter mais informações. Para alterar o IP/porta do Kestrel, veja [Kestrel: configuração do ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Falha ao especificar uma [diretiva server_name](https://nginx.org/docs/http/server_names.html) expõe seu aplicativo para vulnerabilidades de segurança. Associações de curinga de subdomínio (por exemplo, `*.example.com`) não oferecerão esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável). Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.

Quando a configuração Nginx é estabelecida, execute `sudo nginx -t` para verificar a sintaxe dos arquivos de configuração. Se o teste do arquivo de configuração for bem-sucedido, você poderá forçar o Nginx a acompanhar as alterações executando `sudo nginx -s reload`.

Para executar o aplicativo diretamente no servidor:

1. Navegue até o diretório do aplicativo.
1. Execute o aplicativo: `dotnet <app_assembly.dll>`, em que `app_assembly.dll` é o nome do arquivo do assembly do aplicativo.

Se o aplicativo for executado no servidor, mas não responder pela Internet, verifique o firewall do servidor e confirme que a porta 80 está aberta. Se você estiver usando uma VM do Azure Ubuntu, adicione uma regra NSG (Grupo de Segurança de Rede) que permite tráfego de entrada na porta 80. Não é necessário habilitar uma regra de saída da porta 80, uma vez que o tráfego de saída é concedido automaticamente quando a regra de entrada é habilitada.

Quando terminar de testar o aplicativo, encerre o aplicativo com `Ctrl+C` no prompt de comando.

## <a name="monitor-the-app"></a>Monitorar o aplicativo

O servidor agora está configurado para encaminhar solicitações feitas a `http://<serveraddress>:80` ao aplicativo ASP.NET Core em execução no Kestrel em `http://127.0.0.1:5000`. No entanto, o Nginx não está configurado para gerenciar o processo do Kestrel. É possível usar o *systemd* para criar um arquivo de serviço para iniciar e monitorar o aplicativo Web subjacente. *systemd* é um sistema de inicialização que fornece muitos recursos poderosos para iniciar, parar e gerenciar processos. 

### <a name="create-the-service-file"></a>Criar o arquivo de serviço

Crie o arquivo de definição de serviço:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

A seguir, um exemplo de arquivo de serviço para o aplicativo:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

Se o usuário *www-data* não é usado pela configuração, o usuário definido aqui deve ser criado primeiro e a propriedade adequada dos arquivos deve ser concedida a ele.

Use `TimeoutStopSec` para configurar a duração do tempo de espera para o aplicativo desligar depois de receber o sinal de interrupção inicial. Se o aplicativo não desligar nesse período, o SIGKILL será emitido para encerrá-lo. Forneça o valor como segundos sem unidade (por exemplo, `150`), um valor de duração (por exemplo, `2min 30s`) ou `infinity` para desabilitar o tempo limite. `TimeoutStopSec` é revertido para o valor padrão de `DefaultTimeoutStopSec` no arquivo de configuração do gerenciador (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf* e *user.conf.d*). O tempo limite padrão para a maioria das distribuições é de 90 segundos.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

O Linux tem um sistema de arquivos que diferencia maiúsculas de minúsculas. Definir ASPNETCORE_ENVIRONMENT para "Production" resulta em uma pesquisa pelo arquivo de configuração *appsettings.Production.json*, e não *appsettings.production.json*.

Alguns valores (por exemplo, cadeias de conexão de SQL) devem ser escapadas para que os provedores de configuração leiam as variáveis de ambiente. Use o seguinte comando para gerar um valor corretamente com caracteres de escape para uso no arquivo de configuração:

```console
systemd-escape "<value-to-escape>"
```

Separadores do tipo dois-pontos (`:`) não são compatíveis com nomes de variáveis de ambiente. Use um sublinhado duplo (`__`) no lugar de dois-pontos. O [provedor de configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converte caracteres de sublinhado duplo em dois-pontos quando as variáveis de ambiente são lidas na configuração. No exemplo a seguir, a chave de cadeia de conexão `ConnectionStrings:DefaultConnection` está definida no arquivo de definição de serviço como `ConnectionStrings__DefaultConnection`:

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Salve o arquivo e habilite o serviço.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Inicie o serviço e verifique se ele está em execução.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Com o proxy reverso configurado e o Kestrel gerenciado por meio de systemd, o aplicativo Web está totalmente configurado e pode ser acessado em um navegador no computador local em `http://localhost`. Ele também é acessível por meio de um computador remoto, bloqueando qualquer firewall que o possa estar bloqueando. Inspecionando os cabeçalhos de resposta, o cabeçalho `Server` mostra o aplicativo ASP.NET Core sendo servido pelo Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Exibir logs

Já que o aplicativo Web usando Kestrel é gerenciado usando `systemd`, todos os eventos e os processos são registrados em um diário centralizado. No entanto, esse diário contém todas as entradas para todos os serviços e processos gerenciados pelo `systemd`. Para exibir os itens específicos de `kestrel-helloapp.service`, use o seguinte comando:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Para obter mais filtragem, opções de hora como `--since today`, `--until 1 hour ago` ou uma combinação delas, pode reduzir a quantidade de entradas retornadas.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Proteção de dados

A [pilha de proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) do ASP.NET Core, incluindo o middleware de autenticação (por exemplo, o middleware de cookie) e as proteções de CSRF (solicitação intersite forjada). Mesmo se as APIs de Proteção de Dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente. Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.

Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:

* Todos os tokens de autenticação baseados em cookies serão invalidados.
* Os usuários precisam entrar novamente na próxima solicitação deles.
* Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados. Isso pode incluir os [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [cookies TempData do MVC do ASP.NET Core](xref:fundamentals/app-state#tempdata).

Para configurar a proteção de dados de modo que ela mantenha e criptografe o token de autenticação, consulte:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>Campos de cabeçalho da solicitação muito grandes

Se o aplicativo exigir campos de cabeçalho de solicitação mais longos que o permitido pelas configurações padrão do servidor proxy (normalmente de 4K ou 8K dependendo da plataforma), as seguintes diretivas precisarão de ajustes. Os valores que serão aplicados são dependentes de cenário. Para obter mais informações, confira a documentação do servidor.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Não aumente os valores padrão de buffers de proxy a menos que necessário. Aumentar esses valores aumenta o risco de estouro de buffer (estouro) e ataques de DoS (negação de serviço) por usuários mal-intencionados.

## <a name="secure-the-app"></a>Proteger o aplicativo

### <a name="enable-apparmor"></a>Habilitar AppArmor

O LSM (Módulos de Segurança do Linux) é uma estrutura que é parte do kernel do Linux desde o Linux 2.6. O LSM dá suporte a diferentes implementações de módulos de segurança. O [AppArmor](https://wiki.ubuntu.com/AppArmor) é um LSM que implementa um sistema de controle de acesso obrigatório que permite restringir o programa a um conjunto limitado de recursos. Verifique se o AppArmor está habilitado e configurado corretamente.

### <a name="configure-the-firewall"></a>Configurar o firewall

Feche todas as portas externas que não estão em uso. Firewall descomplicado (ufw) fornece um front-end para `iptables` fornecendo uma interface de linha de comando para configurar o firewall.

> [!WARNING]
> Um firewall impedirá o acesso a todo o sistema se não ele estiver configurado corretamente. Se houver falha ao especificar a porta SSH correta, você será bloqueado do sistema se estiver usando o SSH para se conectar a ele. A porta padrão é a 22. Para obter mais informações, consulte a [introdução ao ufw](https://help.ubuntu.com/community/UFW) e o [manual](http://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Instale o `ufw` e configure-o para permitir o tráfego em todas as portas necessárias.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Proteger o Nginx

#### <a name="change-the-nginx-response-name"></a>Alterar o nome da resposta do Nginx

Edite *src/http/ngx_http_header_filter_module.c*:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Configurar opções

Configure o servidor com os módulos adicionais necessários. Considere usar um firewall de aplicativo Web como [ModSecurity](https://www.modsecurity.org/) para fortalecer o aplicativo.

#### <a name="https-configuration"></a>Configuração de HTTPS

**Configurar o aplicativo para conexões seguras (HTTPS) locais**

O comando [dotnet run](/dotnet/core/tools/dotnet-run) usa o arquivo *Properties/launchSettings.json* do aplicativo, que configura o aplicativo para escutar nas URLs fornecidas pela propriedade `applicationUrl` (por exemplo, `https://localhost:5001; http://localhost:5000`).

Configure o aplicativo para usar um certificado no desenvolvimento para o comando `dotnet run` ou no ambiente de desenvolvimento (F5 ou Ctrl + F5 no Visual Studio Code) usando uma das seguintes abordagens:

* [Substituir o certificado padrão da configuração](xref:fundamentals/servers/kestrel#configuration) (*Recomendado*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Configurar o proxy reverso para conexões de cliente seguras (HTTPS)**

* Configure o servidor para escutar tráfego HTTPS na porta `443` especificando um certificado válido emitido por uma AC (autoridade de certificação) confiável.

* Aprimore a segurança, empregando algumas das práticas descritas no arquivo */etc/nginx/nginx.conf* a seguir. Exemplos incluem a escolha de uma criptografia mais forte e o redirecionamento de todo o tráfego por meio de HTTP para HTTPS.

* A adição de um cabeçalho `HTTP Strict-Transport-Security` (HSTS) garante que todas as próximas solicitações feitas pelo cliente sejam por HTTPS.

* Não adicione o cabeçalho HSTS ou escolha um `max-age` apropriado, se quiser desabilitar o HTTPS futuramente.

Adicione o arquivo de configuração */etc/nginx/proxy.conf*:

[!code-nginx[](linux-nginx/proxy.conf)]

Edite o arquivo de configuração */etc/nginx/nginx.conf*. O exemplo contém ambas as seções `http` e `server` em um arquivo de configuração.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Proteger o Nginx de clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), também conhecido como um *ataque por inferência na interface do usuário*, é um ataque mal-intencionado em que o visitante do site é levado a clicar em um link ou botão em uma página diferente daquela que está visitando atualmente. Use `X-FRAME-OPTIONS` para proteger o site.

Para atenuar ataques de clickjacking:

1. Edite o arquivo *nginx.conf*:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Adicione a linha `add_header X-Frame-Options "SAMEORIGIN";`.
1. Salve o arquivo.
1. Reinicie o Nginx.

#### <a name="mime-type-sniffing"></a>Detecção de tipo MIME

Esse cabeçalho evita que a maioria dos navegadores faça detecção MIME de uma resposta distante do tipo de conteúdo declarado, visto que o cabeçalho instrui o navegador para não substituir o tipo de conteúdo de resposta. Com a opção `nosniff`, se o servidor informa que o conteúdo é "text/html", o navegador renderiza-a como "text/html".

Edite o arquivo *nginx.conf*:

```bash
sudo nano /etc/nginx/nginx.conf
```

Adicione a linha `add_header X-Content-Type-Options "nosniff";` e salve o arquivo, depois reinicie o Nginx.

## <a name="additional-resources"></a>Recursos adicionais

* [Pré-requisitos para o .NET Core no Linux](/dotnet/core/linux-prerequisites)
* [Nginx: versões binárias: pacotes Debian/Ubuntu oficiais](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: usando o cabeçalho Encaminhado](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
