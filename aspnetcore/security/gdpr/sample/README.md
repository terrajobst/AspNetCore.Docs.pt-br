# <a name="gdpr-sample"></a>Exemplo de GDPR

* Em *appSettings. JSON*, defina `CheckNotConsentNeeded` como `false` para exigir consentimento; caso contrário, defina como true ou omita. Teste o aplicativo com `CheckNotConsentNeeded` definido como `false` e defina como `true`.
* Crie cookies essenciais e não essenciais com cada variação de `CheckConsentNeeded` e consentimento concedidos.
* Registrar um usuário.
* Excluir cookies.
