---
title: Hospedar o ASP.NET Core no Linux com o Apache
author: rick-anderson
description: Saiba como configurar o Apache como um servidor proxy reverso no CentOS para redirecionar o tráfego HTTP para um aplicativo do Web ASP.NET Core em execução no Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 3a3edd961b08c1952e6ded8038ed7ada381c54b0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657895"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hospedar o ASP.NET Core no Linux com o Apache

Por [Shayne Boyer](https://github.com/spboyer)

Usando este guia, saiba como configurar o [Apache](https://httpd.apache.org/) como um servidor proxy reverso no [CentOS 7](https://www.centos.org/) para redirecionar o tráfego HTTP para um aplicativo Web ASP.NET Core em execução no servidor [Kestrel](xref:fundamentals/servers/kestrel). A [extensão mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) e os módulos relacionados criam o proxy reverso do servidor.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Servidor que executa o CentOS 7 com uma conta de usuário padrão com privilégio sudo.
* Instale o runtime do .NET Core no servidor.
   1. Visite a [página baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Selecione a versão mais recente do .NET Core sem visualização.
   1. Baixe o tempo de execução de não visualização mais recente na tabela em **executar aplicativos-tempo de execução**.
   1. Selecione o link de **instruções do Gerenciador de pacotes** do Linux e siga as instruções do CentOS.
* Um aplicativo ASP.NET Core existente.

A qualquer momento no futuro depois de atualizar a estrutura compartilhada, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.

## <a name="publish-and-copy-over-the-app"></a>Publicar e copiar o aplicativo

Configurar o aplicativo para um [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Se o aplicativo for executado localmente e não estiver configurado para fazer conexões seguras (HTTPS), adote uma das seguintes abordagens:

* Configure o aplicativo para lidar com conexões seguras locais. Para obter mais informações, veja a seção [Configuração de HTTPS](#https-configuration).
* Remova `https://localhost:5001` (se houver) da propriedade `applicationUrl` no arquivo *Properties/launchSettings.json*.

Execute [dotnet publish](/dotnet/core/tools/dotnet-publish) do ambiente de desenvolvimento para empacotar um aplicativo em um diretório (por exemplo, *bin/Release/&lt;target_framework_moniker&gt;/publish*) que pode ser executado no servidor:

```dotnetcli
dotnet publish --configuration Release
```

O aplicativo também poderá ser publicado como uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd) se você preferir não manter o runtime do .NET Core no servidor.

Copie o aplicativo ASP.NET Core para o servidor usando uma ferramenta que se integre ao fluxo de trabalho da organização (por exemplo, SCP, SFTP). É comum para localizar os aplicativos Web no diretório *var* (por exemplo, *var/www/helloapp*).

> [!NOTE]
> Em um cenário de implantação de produção, um fluxo de trabalho de integração contínua faz o trabalho de publicar o aplicativo e copiar os ativos para o servidor.

## <a name="configure-a-proxy-server"></a>Configurar um servidor proxy

Um proxy reverso é uma configuração comum para atender a aplicativos Web dinâmicos. O proxy reverso encerra a solicitação HTTP e a encaminha para o aplicativo ASP.NET.

Um servidor proxy é aquele que encaminha as solicitações de cliente para outro servidor, em vez de atendê-las por conta própria. Um proxy reverso encaminha para um destino fixo, normalmente, em nome de clientes arbitrários. Neste guia, o Apache é configurado como o proxy reverso em execução no mesmo servidor em que o Kestrel está servindo o aplicativo ASP.NET Core.

Uma vez que as solicitações são encaminhadas pelo proxy reverso, use o [Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/). O middleware atualiza o `Request.Scheme` usando o cabeçalho `X-Forwarded-Proto`, de forma que URIs de redirecionamento e outras políticas de segurança funcionam corretamente.

Qualquer componente que dependa do esquema, como autenticação, geração de link, redirecionamentos e localização geográfica, deverá ser colocado depois de invocar o Middleware de Cabeçalhos Encaminhados. Como regra geral, o Middleware de Cabeçalhos Encaminhados deve ser executado antes de outro middleware, exceto middleware de tratamento de erro e de diagnóstico. Essa ordenação garantirá que o middleware conte com informações de cabeçalhos encaminhadas que podem consumir os valores de cabeçalho para processamento.

Invoque o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure` antes de chamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ou outro middleware de esquema de autenticação semelhante. Configure o middleware para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, os cabeçalhos padrão para encaminhar serão `None`.

Os proxies em execução em endereços de loopback (127.0.0.0/8, [::1]), incluindo o endereço de host local padrão (127.0.0.1), são confiáveis por padrão. Se outros proxies ou redes confiáveis em que a organização trata solicitações entre a Internet e o servidor Web, adicione-os à lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. O exemplo a seguir adiciona um servidor proxy confiável no endereço IP 10.0.0.100 ao Middleware de cabeçalhos encaminhados `KnownProxies` em `Startup.ConfigureServices`:

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Instalar o Apache

Atualize os pacotes CentOS para as respectivas versões estáveis mais recentes:

```bash
sudo yum update -y
```

Instale o servidor Web do Apache no CentOS com um único comando `yum`:

```bash
sudo yum -y install httpd mod_ssl
```

Saída de exemplo depois de executar o comando:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> Neste exemplo, o resultado reflete httpd.86_64, pois a versão do CentOS 7 é de 64 bits. Para verificar o local em que o Apache está instalado, execute `whereis httpd` em um prompt de comando.

### <a name="configure-apache"></a>Configurar o Apache

Os arquivos de configuração do Apache estão localizados no diretório `/etc/httpd/conf.d/`. Qualquer arquivo com a extensão *.conf* é processado em ordem alfabética, além dos arquivos de configuração do módulo em `/etc/httpd/conf.modules.d/`, que contém todos os arquivos de configuração necessários para carregar os módulos.

Crie um arquivo de configuração chamado *helloapp.conf* para o aplicativo:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

O bloco `VirtualHost` pode aparecer várias vezes, em um ou mais arquivos em um servidor. No arquivo de configuração anterior, o Apache aceita tráfego público na porta 80. O domínio `www.example.com` está sendo atendido e o alias `*.example.com` é resolvido para o mesmo site. Veja [Suporte a host virtual baseado em nome](https://httpd.apache.org/docs/current/vhosts/name-based.html) para obter mais informações. As solicitações passadas por proxy na raiz para a porta 5000 do servidor em 127.0.0.1. Para a comunicação bidirecional, `ProxyPass` e `ProxyPassReverse` são necessários. Para alterar o IP/porta do Kestrel, veja [Kestrel: configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Falha ao especificar uma [diretiva ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) no bloco **VirtualHost** expõe seu aplicativo para vulnerabilidades de segurança. Associações de curinga de subdomínio (por exemplo, `*.example.com`) não oferecerão esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável). Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.

O registro em log pode ser configurado por `VirtualHost` usando diretivas `ErrorLog` e `CustomLog`. `ErrorLog` é o local em que o servidor registrará em log os erros, enquanto `CustomLog` define o nome de arquivo e o formato do arquivo de log. Nesse caso, esse é o local em que as informações de solicitação são registradas em log. Há uma linha para cada solicitação.

Salve o arquivo e teste a configuração. Se tudo passar, a resposta deverá ser `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Reinicie o Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Monitorar o aplicativo

O Apache agora está configurado para encaminhar solicitações feitas a `http://localhost:80` para o aplicativo ASP.NET Core em execução no Kestrel em `http://127.0.0.1:5000`. No entanto, o Apache não está configurado para gerenciar o processo do Kestrel. Use *systemd* e crie um arquivo de serviço para iniciar e monitorar o aplicativo Web subjacente. *systemd* é um sistema de inicialização que fornece muitos recursos poderosos para iniciar, parar e gerenciar processos.

### <a name="create-the-service-file"></a>Criar o arquivo de serviço

Crie o arquivo de definição de serviço:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Eis um exemplo de arquivo de serviço para o aplicativo:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

No exemplo anterior, o usuário que gerencia o serviço é especificado pela opção `User`. O usuário (`apache`) deve existir e ter a propriedade adequada dos arquivos do aplicativo.

Use `TimeoutStopSec` para configurar a duração do tempo de espera para o aplicativo desligar depois de receber o sinal de interrupção inicial. Se o aplicativo não desligar nesse período, o SIGKILL será emitido para encerrá-lo. Forneça o valor como segundos sem unidade (por exemplo, `150`), um valor de duração (por exemplo, `2min 30s`) ou `infinity` para desabilitar o tempo limite. `TimeoutStopSec` é revertido para o valor padrão de `DefaultTimeoutStopSec` no arquivo de configuração do gerenciador (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf* e *user.conf.d*). O tempo limite padrão para a maioria das distribuições é de 90 segundos.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Alguns valores (por exemplo, cadeias de conexão de SQL) devem ser escapadas para que os provedores de configuração leiam as variáveis de ambiente. Use o seguinte comando para gerar um valor corretamente com caracteres de escape para uso no arquivo de configuração:

```console
systemd-escape "<value-to-escape>"
```

Separadores do tipo dois-pontos (`:`) não são compatíveis com nomes de variáveis de ambiente. Use um sublinhado duplo (`__`) no lugar de dois-pontos. O [provedor de configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converte caracteres de sublinhado duplo em dois-pontos quando as variáveis de ambiente são lidas na configuração. No exemplo a seguir, a chave de cadeia de conexão `ConnectionStrings:DefaultConnection` está definida no arquivo de definição de serviço como `ConnectionStrings__DefaultConnection`:

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Salve o arquivo e habilite o serviço:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Inicie o serviço e verifique se ele está em execução:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Com o proxy reverso configurado e o Kestrel gerenciado por meio de *systemd*, o aplicativo Web está totalmente configurado e pode ser acessado em um navegador no computador local em `http://localhost`. Inspecionando os cabeçalhos de resposta, o cabeçalho **Server** indica que o aplicativo ASP.NET Core é servido pelo Kestrel:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Exibir logs

Já que o aplicativo Web usando Kestrel é gerenciado usando *systemd*, os eventos e os processos são registrados em um diário centralizado. No entanto, esse diário contém todas as entradas para todos os serviços e processos gerenciados por *systemd*. Para exibir os itens específicos de `kestrel-helloapp.service`, use o seguinte comando:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Para filtragem de hora, especifique opções de tempo com o comando. Por exemplo, use `--since today` para filtrar o dia atual ou `--until 1 hour ago` para ver as entradas da hora anterior. Para obter mais informações, confira a [página de manual para journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

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

## <a name="secure-the-app"></a>Proteger o aplicativo

### <a name="configure-firewall"></a>Configurar o firewall

*Firewalld* é um daemon dinâmico para gerenciar o firewall com suporte para zonas de rede. Portas e filtragem de pacote ainda podem ser gerenciados pelo iptables. *Firewalld* deve ser instalado por padrão. `yum` pode ser usado para instalar o pacote ou verificar se ele está instalado.

```bash
sudo yum install firewalld -y
```

Use `firewalld` para abrir somente as portas necessárias para o aplicativo. Nesse caso, as portas 80 e 443 são usadas. Os comandos a seguir definem permanentemente as portas 80 e 443 para estarem abertas:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Recarregue as configurações de firewall. Verifique os serviços e as portas disponíveis na zona padrão. As opções estão disponíveis por meio da inspeção de `firewall-cmd -h`.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>Configuração de HTTPS

**Configurar o aplicativo para conexões seguras (HTTPS) locais**

O comando [dotnet run](/dotnet/core/tools/dotnet-run) usa o arquivo *Properties/launchSettings.json* do aplicativo, que configura o aplicativo para escutar nas URLs fornecidas pela propriedade `applicationUrl` (por exemplo, `https://localhost:5001; http://localhost:5000`).

Configure o aplicativo para usar um certificado no desenvolvimento para o comando `dotnet run` ou no ambiente de desenvolvimento (F5 ou Ctrl + F5 no Visual Studio Code) usando uma das seguintes abordagens:

* [Substituir o certificado padrão da configuração](xref:fundamentals/servers/kestrel#configuration) (*Recomendado*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Configurar o proxy reverso para conexões de cliente seguras (HTTPS)**

Para configurar o Apache para HTTPS, o módulo *mod_ssl* é usado. Quando o módulo *httpd* foi instalado, o módulo *mod_ssl* também foi instalado. Se ele não foi instalado, use `yum` para adicioná-lo à configuração.

```bash
sudo yum install mod_ssl
```

Para impor o HTTPS, instale o módulo `mod_rewrite` para habilitar a regravação de URL:

```bash
sudo yum install mod_rewrite
```

Modifique o arquivo *helloapp.conf* para habilitar a regravação de URL e proteger a comunicação na porta 443:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Este exemplo usa um certificado gerado localmente. **SSLCertificateFile** deve ser o arquivo de certificado primário para o nome de domínio. **SSLCertificateKeyFile** deve ser o arquivo de chave gerado quando o CSR é criado. **SSLCertificateChainFile** deve ser o arquivo de certificado intermediário (se houver) fornecido pela autoridade de certificação.

Salve o arquivo e teste a configuração:

```bash
sudo service httpd configtest
```

Reinicie o Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Sugestões adicionais do Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Reiniciar aplicativos com atualizações de estrutura compartilhadas

Depois de atualizar a estrutura compartilhada no servidor, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.

### <a name="additional-headers"></a>Cabeçalhos adicionais

Para proteção contra ataques mal-intencionados, há alguns cabeçalhos que devem ser modificados ou adicionados. Verifique se o módulo `mod_headers` está instalado:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Proteger o Apache contra ataques de clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), também conhecido como um *ataque por inferência na interface do usuário*, é um ataque mal-intencionado em que o visitante do site é levado a clicar em um link ou botão em uma página diferente daquela que está visitando atualmente. Use `X-FRAME-OPTIONS` para proteger o site.

Para atenuar ataques de clickjacking:

1. Edite o arquivo *httpd.conf*:

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Adicione a linha `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Salve o arquivo.
1. Reinicie o Apache.

#### <a name="mime-type-sniffing"></a>Detecção de tipo MIME

O cabeçalho `X-Content-Type-Options` impedirá o Internet Explorer de *farejar por MIME* (determinar o `Content-Type` de um arquivo com base no conteúdo do arquivo). Se o servidor define o cabeçalho `Content-Type` para `text/html` com a opção `nosniff` definida, o Internet Explorer renderiza o conteúdo como `text/html`, independentemente do conteúdo do arquivo.

Edite o arquivo *httpd.conf*:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Adicione a linha `Header set X-Content-Type-Options "nosniff"`. Salve o arquivo. Reinicie o Apache.

### <a name="load-balancing"></a>Balanceamento de carga

Este exemplo mostra como instalar e configurar o Apache no CentOS 7 e no Kestrel no mesmo computador da instância. Para não ter um ponto único de falha, o uso de *mod_proxy_balancer* e a modificação do **VirtualHost** permitiriam o gerenciamento de várias instâncias dos aplicativos Web protegidos pelo servidor proxy do Apache.

```bash
sudo yum install mod_proxy_balancer
```

No arquivo de configuração mostrado abaixo, uma instância adicional do `helloapp` é configurada para ser executada na porta 5001. A seção *Proxy* é definida com uma configuração de balanceador com dois membros para balancear carga de *byrequests*.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Limites de taxa

Usando *mod_ratelimit*, que está incluído no módulo *httpd*, a largura de banda de clientes pode ser limitada:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

O arquivo de exemplo limita a largura de banda a 600 KB/s no local raiz:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Campos de cabeçalho da solicitação muito grandes

As configurações padrão do servidor proxy normalmente limitam os campos de cabeçalho de solicitação a 8.190 bytes. Um aplicativo pode exigir campos maiores do que o padrão (por exemplo, aplicativos que usam [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Se forem necessários campos mais longos, a diretiva [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) do servidor proxy exigirá ajuste. O valor a ser aplicado depende do cenário. Para obter mais informações, confira a documentação do servidor.

> [!WARNING]
> Não aumente o valor padrão de `LimitRequestFieldSize` a menos que necessário. Aumentar esse valor aumenta o risco de estouro de buffer (estouro) e ataques de DoS (negação de serviço) por usuários mal-intencionados.

## <a name="additional-resources"></a>Recursos adicionais

* [Pré-requisitos para o .NET Core no Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
