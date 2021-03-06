---

copyright:

years: 2017, 2018
lastupdated: "2018-03-26"

---

{:new_window: target="\_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}


# APIs
{: #api_overview}

Várias APIs estão disponíveis para desenvolver código para dispositivos, gateways e aplicativos que se conectam ao {{site.data.keyword.iot_full}}.

As APIs HTTP são protegidas com autenticação básica HTTP. Quando você gera uma chave API usando o painel, são apresentados uma chave e um token de autenticação. Para obter mais informações sobre chaves e tokens API, veja [Conexão da chave API](../platform_authorization.html#api-key).


## Sobre APIs HTTP
{: #api_about}

Depois de registrar-se em sua própria organização, você receberá um ID da organização com 6 caracteres que será necessário no nome do host para qualquer chamada API HTTP. A URL base para sua organização pode ser acessada no endereço a seguir:

https://<**orgId**>.internetofthings.ibmcloud.com/api/v0002

Para autenticar solicitações para a API do aplicativo, configure o nome do usuário para a chave API e a senha para o token de autenticação.

Para APIs do sistema de mensagens, use o endereço a seguir:

https://<**orgId**>.messaging.internetofthings.ibmcloud.com/api/v0002

## APIs HTTP
{: #api_http}

API do                     | Use para...       
------------- | -------------
[Administração da organização ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/orgAdmin.html){: new_window} | Configurar uma organização (incluindo a criação e exclusão de dispositivos), verificar o uso, o status de serviço e diagnosticar problemas de conexão de dispositivo.
[Segurança ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/security.html){: new_window} | Gerencie a autenticação e a autorização de usuários, chaves API e dispositivos.
[Gerenciamento de informações ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/info-mgmt.html){: new_window} |  Acessar dados do evento de dispositivo, bem como obter e atualizar a localização do dispositivo e obter informações do clima para esse local. 
[Gerenciamento de dados ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/state-mgmt.html){: new_window}   |   Organizar e integrar dados entrando e saindo do {{site.data.keyword.iot_short_notm}}.
[Gerenciamento de dispositivo ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/deviceMgmt.html){: new_window} | Interagir com dispositivos gerenciados usando o protocolo de gerenciamento de dispositivo.
[Sistema de mensagens ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/http-messaging.html){: new_window}   | Publicar eventos e enviar comandos usando HTTP.
[Gerenciamento de risco ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/riskmgmt.html){: new_window}   | Gerenciar as políticas e os relatórios de Gerenciamento de risco.

## APIs HTTP de extensão
{: #api_extension}

API do                     | Use para...       
------------- | -------------
[Extensão AT&T ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/ext-atnt.html){: new_window} | Administrar dispositivos AT&.
[Extensão Jasper  ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/ext-jasper.html){: new_window} | Administrar dispositivos Jasper.
[Extensão Orange ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002/ext-orange.html){: new_window} | Visualizar dados do cartão SIM de dispositivos que estão conectados à sua organização do {{site.data.keyword.iot_short_notm}} e têm um cartão SIM da Orange instalado.

## APIs HTTP Beta
{: #api_beta}

API do                     | Use para...       
------------- | -------------
[ Restaurar dispositivos excluídos ![Ícone de link externo](../../../icons/launch-glyph.svg)](https://docs.internetofthings.ibmcloud.com/apis/swagger/v0002-beta/restore-device-beta.html){: new_window} | Se um dispositivo é excluído por engano, é possível restaurá-lo dentro de 14 dias.
