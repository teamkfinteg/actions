{% include "./readme-src/readme-pre.md" ignore missing %}


### Initial Configuration of PAM Provider
In order to allow Keyfactor to use the new {{ name }}, the definition needs to be added to the application database.
This is done by running the provided [add_PAMProvider.sql](./add_PAMProvider.sql) script on the Keyfactor application database, which only needs to be done one time.

If you have a hosted environment or need assistance completing this step, please contact Keyfactor Support.

##### Usage
In order to use the PAM Provider, the provider's configuration must be set in the Keyfactor Platform. In the settings menu (upper right cog) you can select the ___Priviledged Access Management___ option to configure your provider instance.

![](./images/setting.png)

### Configuring Parameters
The following are the parameter names and a description of the values needed to configure the {{ name }}.

| Initialization parameter | Display Name | Description | Example | 
| :---: | :---: | --- | :---: | 
| Path | KV Engine Path | The path to secrets in the Vault | By default this would be at `{{ about.pam.kvEnginePath }}` |
| Token | Vault Token | The access token for the Vault | {{about.pam.vaultToken }} |
| Host | Vault Host | The IP address or URL of the Vault instance, including any port number | {{ about.pam.vaultHost }}  |

![](./images/config.png)


## PAM Server Password 
After it is set up, you can now use your PAM Provider when configuring certificate stores. Any field that is treated as a Keyfactor secret, such as server passwords and certificate store passwords can be retrieved from your PAM Provider instead of being entered in directly as a secret.

| Instance parameter | Display Name | Description | Example | 
| :---: | :---: | --- | :---: | 
| Server secret | Secret Source | Where to get secrets | Load From PAM Provider |
| Server Provider | Providers | Drop-down selector for Server provider name | {{ about.pam.providers }} |
| Key | KV Secret Key | The key to the key-value pair of the secret to access | {{ about.pam.secretKey }}  |
| Secret | KV Secret Name | The name of the secret in the Vault | {{ about.pam.secretName }} |

![](./images/password.png)


{% include "./readme-src/readme-config.md" %}

#### In Keyfactor - PAM Provider
##### Installation
In order to setup a new PAM Provider in the Keyfactor Platform for the first time, you will need to run [the SQL Installation Script](./add_PAMProvider.sql) against your Keyfactor application database.

After the installation is run, the DLLs need to be installed to the correct location for the PAM Provider to function. From the release, the {{ about.pam.regDLL }}.dll should be copied to the following folder locations in the Keyfactor installation. Once the DLL has been copied to these folders, edit the corresponding config file. You will need to add a new Unity entry as follows under `<container>`, next to other `<register>` tags.

When enabling a PAM provider for Orchestrators only, the first line for `WebAgentServices` is the only installation needed.

The Keyfactor service and IIS Server should be restarted after making these changes.

```xml
<register type="IPAMProvider" mapTo="Keyfactor.Extensions.Pam.{{ about.pam.qualifiedName }}, {{ about.pam.RegDLL }}" name="{{ about.pam.dbName }}" />
```
{% include "./readme-src/readme-register.md" ignore missing %}

| Install Location | DLL Binary Folder | Config File |
| --- | --- | --- |
| WebAgentServices | WebAgentServices\bin\ | WebAgentServices\web.config |
| Service | Service\ | Service\CMSTimerService.exe.config |
| KeyfactorAPI | KeyfactorAPI\bin\ | KeyfactorAPI\web.config |
| WebConsole | WebConsole\bin\ | WebConsole\web.config |

