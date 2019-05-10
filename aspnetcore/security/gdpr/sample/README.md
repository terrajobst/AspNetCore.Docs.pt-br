# <a name="gdpr-sample"></a>Exemplo GDPR

* Na *appSettings. JSON*, defina `CheckNotConsentNeeded` para `false` exigir consentimento; caso contrário, definida como true ou omitir. Testar o aplicativo com `CheckNotConsentNeeded` definido como `false` e defina como `true`.
* Criar cookies essenciais e não essenciais com cada variação de `CheckConsentNeeded` e consentimento concedido.
* Registre-se um usuário.
* Exclua cookies.
