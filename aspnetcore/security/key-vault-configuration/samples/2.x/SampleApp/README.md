# <a name="key-vault-configuration-provider-sample-app"></a>Aplicativo de exemplo do provedor de configuração Key Vault

Este exemplo ilustra o uso do provedor de configuração Azure Key Vault.

O exemplo é executado em um dos dois modos determinados pela instrução `#define` na parte superior do arquivo *Program.cs* . Para obter instruções, consulte [diretivas de pré-processador no código de exemplo](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate` &ndash; demonstra o uso de uma ID de cliente do Azure Key Vault e o certificado X. 509 para acessar os segredos armazenados no Azure Key Vault. Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.
* `Managed` &ndash; demonstra como usar o [identidade de serviço gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) do Azure para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais no código ou na configuração do aplicativo. Uma ID de cliente e um segredo do Azure AD não são necessários para que o aplicativo seja autenticado com Azure Key Vault. Este exemplo deve ser implantado no serviço Azure App para explorar a identidade gerenciada scearnio.

Para obter mais informações, consulte [Azure Key Vault provedor de configuração](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
