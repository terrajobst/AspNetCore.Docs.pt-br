## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Encaminhar informações de solicitação com um proxy ou balanceador de carga

Se o aplicativo for implantado atrás de um servidor proxy ou um balanceador de carga, algumas das informações da solicitação original podem ser encaminhadas para o aplicativo nos cabeçalhos de solicitação. Essas informações geralmente incluem o esquema de solicitação segura (`https`), o host e o endereço IP do cliente. Os aplicativos não leem automaticamente esses cabeçalhos de solicitação para descobrir e usar as informações da solicitação original.

O esquema é usado na geração de link que afeta o fluxo de autenticação com provedores externos. Perder o esquema de seguro (`https`) resulta no aplicativo gerando URLs de redirecionamento inseguros incorretos.

Use Middleware de cabeçalhos encaminhados para disponibilizar as informações da solicitação original ao aplicativo para o processamento da solicitação.

Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.
