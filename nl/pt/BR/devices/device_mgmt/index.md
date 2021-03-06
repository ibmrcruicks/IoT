---

copyright:
  years: 2015, 2018
lastupdated: "2018-03-08"

---

{:new_window: target="\_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}


# Protocolo de Gerenciamento de Dispositivo
{: #index}

## Introdução
{: #introduction}

O {{site.data.keyword.iot_full}} reconhece dispositivos e gateways como as duas classes de dispositivo. A classe de dispositivo é identificada usando o campo "classId". Os dispositivos na classe de dispositivo podem ser dispositivos gerenciados ou dispositivos não gerenciados.

**Dispositivos gerenciados** são definidos como dispositivos que contêm um agente de gerenciamento de dispositivo. Um agente de gerenciamento de dispositivo é um conjunto de lógica que permite que o dispositivo interaja com o serviço de Gerenciamento de dispositivo do {{site.data.keyword.iot_short_notm}} usando o Protocolo de gerenciamento de dispositivo. Os dispositivos gerenciados podem executar operações de gerenciamento de dispositivo, incluindo atualizações de localização, downloads de firmware e atualizações, reinicializações e reconfigurações de fábrica.

O Protocolo de Gerenciamento de Dispositivo define um conjunto de operações suportadas. Um agente de gerenciamento de dispositivo pode suportar um subconjunto das operações, mas ele deve fazer a solicitação Gerenciar dispositivo. Um dispositivo que suporta operações de ação de firmware também deve suportar observação.

O Protocolo de gerenciamento de dispositivo é construído com base no protocolo de sistema de mensagens MQTT. Para obter mais informações sobre como o Protocolo de gerenciamento de dispositivo interage com MQTT, consulte [Conectividade MQTT para dispositivos](../mqtt.html).


### O ciclo de vida do gerenciamento de dispositivo

1. Um dispositivo e seu tipo de dispositivo associado são criados no {{site.data.keyword.iot_short_notm}} usando o painel ou a API ( interface de programação de aplicativos) REST.
2. Um dispositivo se conecta ao {{site.data.keyword.iot_short_notm}} e faz uma solicitação Gerenciar dispositivo para se tornar um dispositivo gerenciado.
3. É possível visualizar e manipular os metadados para um dispositivo usando a API de REST do {{site.data.keyword.iot_short_notm}}. Essas operações de API - por exemplo, atualização de firmware e reinicialização de dispositivo - são descritas na documentação [Solicitações de gerenciamento de dispositivo](https://console.ng.bluemix.net/docs/services/IoT/devices/device_mgmt/requests.html).
4. Um dispositivo pode comunicar atualizações sobre sua localização, informações de diagnóstico e códigos de erro usando o Protocolo de gerenciamento de dispositivo.
5. Quando um dispositivo for desatribuído, será possível removê-lo do {{site.data.keyword.iot_short_notm}} usando o painel ou a API (interface de programação de aplicativos) REST.

Veja a orientação [Conectar o Raspberry Pi como dispositivo gerenciado ao IBM Watson IoT Platform ![Ícone de link externo](../../../../icons/launch-glyph.svg "Ícone de link externo")](https://developer.ibm.com/recipes/tutorials/connect-raspberry-pi-as-managed-device-to-ibm-iot-foundation/){: new_window}.

## Solicitações Gerenciar dispositivo
{: #manage_device_request}

Um dispositivo usa a solicitação Gerenciar dispositivo para se tornar um dispositivo gerenciado. Um agente de gerenciamento de dispositivo deve enviar uma solicitação Gerenciar dispositivo antes de poder receber solicitações do servidor. Um agente de gerenciamento de dispositivo geralmente envia esse tipo de solicitação ao ser iniciado ou reiniciado.

### Tópico para uma solicitação Gerenciar dispositivo

Um dispositivo publica uma solicitação Gerenciar dispositivo no tópico a seguir:

```
iotdevice-1/mgmt/manage
```

O servidor responde a uma solicitação Gerenciar dispositivo no tópico a seguir:

```
iotdm-1/response
```




### Formato da mensagem para uma solicitação Gerenciar dispositivo


Em uma solicitação Gerenciar dispositivo, o campo ``d`` e todos os seus subcampos são opcionais. Os valores dos campos ``metadata`` e ``deviceInfo`` substituem os atributos correspondentes para o dispositivo de envio se forem enviados.

O campo ``lifetime`` opcional especifica a duração em segundos em que o dispositivo deve enviar outra solicitação Gerenciar dispositivo para evitar que seja considerado inativo e se torne um dispositivo não gerenciado. Se o campo ``lifetime`` for omitido ou configurado como ``0``, o dispositivo gerenciado não se tornará inativo. A configuração mínima suportada para o campo ``lifetime`` é ``3600`` segundos, que é uma hora.

Os campos ``supports.deviceActions`` e ``supports.firmwareActions`` opcionais indicam os recursos do agente de gerenciamento de dispositivo. Se ``supports.deviceActions`` for configurado, então, o agente suportará as ações de reinicialização e de reconfiguração de fábrica. Para um dispositivo que não distingue entre uma reinicialização e uma reconfiguração de fábrica, é aceitável usar o mesmo comportamento para ambas as ações. Se ``supports.firmwareActions`` for configurado, o agente suportará as ações de download de firmware e de atualização de firmware.

A amostra a seguir mostra o formato de solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/mgmt/manage
{
    "d": {
        "metadata":{},
        "lifetime": number,
        "supports": {
            "deviceActions": boolean,
            "firmwareActions": boolean
        },
        "deviceInfo": {
            "serialNumber": "string",
            "manufacturer": "string",
            "model": "string",
            "deviceClass": "string",
            "description" :"string",
            "fwVersion": "string",
            "hwVersion": "string",
            "descriptiveLocation": "string"
        }
    },
    "reqId": "string"
}
```

A amostra a seguir mostra o formato de resposta:

```
Incoming message from the server:

Topic: iotdm-1/response
{
    "rc": number,
    "reqId": "string"
}
```


### Códigos de resposta para uma solicitação Gerenciar dispositivo

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|403   |Proibido (se um dispositivo tenta publicar uma solicitação de gerenciamento solicitando suporte para um conjunto inválido de ações)|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.|
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|


## Solicitações Cancelar gerenciamento de dispositivo
{: #manage-unmanage}


Um dispositivo usa uma solicitação Cancelar gerenciamento de dispositivo quando não precisar mais ser gerenciado. Quando um dispositivo se torna não gerenciado, o {{site.data.keyword.iot_short_notm}} não enviará mais novas solicitações de gerenciamento de dispositivo ao dispositivo. Os dispositivos não gerenciados podem continuar a publicar códigos de erro, mensagens de log e mensagens de localização.

### Tópico para uma solicitação Cancelar gerenciamento de dispositivo


Um dispositivo publica uma solicitação Cancelar gerenciamento de dispositivo no tópico a seguir:

```
iotdevice-1/mgmt/unmanage
```

O servidor responde a uma solicitação Cancelar gerenciamento de dispositivo no tópico a seguir:

```
iotdm-1/response
```

### Formato da mensagem para uma solicitação Cancelar gerenciamento de dispositivo

Formato da solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/mgmt/unmanage
{
    "reqId": "string"
}
```

Formato da resposta:

```
Incoming message from the server:

Topic: iotdm-1/response
{
    "rc": number,
    "reqId": "string"
}
```

### Códigos de resposta para uma solicitação Cancelar gerenciamento de dispositivo

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.|
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|


## Solicitações Atualizar localização
{: #update-location}

Os metadados de localização de um dispositivo podem ser atualizados no{{site.data.keyword.iot_short_notm}} das maneiras a seguir:

### Atualizações automáticas de localização do dispositivo
- Os dispositivos que podem determinar sua localização podem optar por notificar o servidor de gerenciamento de dispositivo do {{site.data.keyword.iot_short_notm}} sobre mudanças de localização. O dispositivo notifica o {{site.data.keyword.iot_short_notm}} sobre a atualização da localização. O dispositivo recupera sua localização, de um receptor GPS, por exemplo, e envia uma mensagem de gerenciamento de dispositivo para a instância do {{site.data.keyword.iot_short_notm}} para atualizar sua localização. O registro de data e hora captura o horário em que a localização foi recuperada do receptor de GPS (sistema de posicionamento global). O registro de data e hora será válido mesmo se houver um atraso no envio da mensagem de atualização da localização. O servidor registra a data e hora do recibo da mensagem e usa essas informações para atualizar os metadados de localização se um registro de data e hora não foi usado.

### Atualizações manuais de localização do dispositivo usando a API (interface de programação de aplicativos) REST
- É possível configurar manualmente os metadados de localização para um dispositivo estático usando a API (interface de programação de aplicativos) REST do {{site.data.keyword.iot_short_notm}} quando o dispositivo for registrado. Também é possível modificar a localização posteriormente. A configuração do registro de data e hora é opcional, mas quando omitida, a data e hora atuais são configuradas nos metadados de localização do dispositivo.

### Tópico para uma solicitação Atualizar localização acionada por um dispositivo:


Um dispositivo publica uma solicitação Atualizar localização no tópico a seguir:

```
iotdevice-1/device/update/location
```

O servidor responde a uma solicitação Atualizar localização no tópico a seguir:

```
iotdm-1/response
```

### Atualização da localização acionada por usuários ou aplicativos


Quando um usuário ou aplicativo atualiza o local de um dispositivo gerenciado ativo, o dispositivo recebe uma mensagem de atualização.




### Tópico para uma solicitação Atualizar localização acionada por usuários ou aplicativos


O servidor publica uma solicitação Atualizar localização no tópico a seguir:

```
iotdm-1/device/update
```

### Formato da mensagem para uma solicitação Atualizar localização


O campo ``measuredDateTime`` é a data e hora de medição da localização.

Sempre que a localização é atualizada, os valores fornecidos para latitude, longitude, elevação e precisão são considerados uma única atualização de multivalor. A latitude e a longitude são obrigatórias e ambas devem ser fornecidas com cada atualização. A latitude e a longitude devem ser especificadas em graus decimais usando o World Geodetic System 1984 (WGS84). A elevação e a precisão são medidas em medidores e são opcionais.

Se um valor opcional for fornecido em uma atualização e, em seguida, omitido em uma atualização posterior, o valor anterior será excluído pela atualização posterior. Cada atualização é considerada um conjunto completo multivalor.


Formato da solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/device/update/location
{
    "d": {
        "longitude": number,
        "latitude": number,

        "elevation": number,
        "measuredDateTime": "string in ISO8601 format",
        "updatedDateTime": "string in ISO8601 format",
        "accuracy": number
    },
    "reqId": "string"
}
```

Formato da resposta:

```
Incoming message from the server:

Topic: iotdm-1/response
{
    "rc": number,
    "reqId": "string"
}
```

### Códigos de resposta para uma solicitação Atualizar localização

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.|
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|


### Atualizações de localização que são acionadas por usuários ou aplicativos


A amostra a seguir esboça o formato de carga útil:

```
Incoming message from the server:

Topic: iotdm-1/device/update
{
    "d": {
        "fields": [
            {
                "field": "location",
                "value": {
                    "latitude": number,
                    "longitude": number,
                    "elevation": number,
                    "accuracy": number,
                    "measuredDateTime": "string in ISO8601 format"
                    "updatedDateTime": "string in ISO8601 format",

                }
            }
        ]
    }
}
```

**Nota:** o parâmetro ``reqID`` não é usado porque o dispositivo não é necessário para responder.

## Solicitações Atualizar atributo de dispositivo
{: #update-attributes}

Usando a API (interface de programação de aplicativos) REST, o {{site.data.keyword.iot_short_notm}} pode enviar uma solicitação a um dispositivo para atualizar o valor de um ou mais dos atributos de dispositivo a seguir:


|Atributo | Mais informações|
|:---|:---|
|localização  | Consulte [Atualizar localização](index.html#update-location)|
|metadata  | Optional|
|deviceInfo | Optional|
|mgmt.firmware | Consulte [Processo de atualização de firmware](requests.html#firmware-actions-update)|

### Tópico para uma solicitação Atualizar atributos de dispositivo


O servidor publica uma solicitação de atualização de dispositivo no tópico a seguir:

```
iotdm-1/device/update
```


### Formato da mensagem para uma solicitação Atualizar atributos de dispositivo


A amostra a seguir esboça o formato de carga útil para a solicitação:

```
Incoming message from the server:

Topic: iotdm-1/device/update
{
    "d": {
        "fields": [
            {
                "field": "location",
                "value": ""
            }
        ]
    }
}
```


## Solicitações Incluir códigos de erro
{: #diag-add-error-code}

Os dispositivos podem optar por notificar o servidor de gerenciamento de dispositivo do {{site.data.keyword.iot_short_notm}} sobre mudanças em seu status de erro usando o tipo de solicitação Incluir códigos de erro.

### Tópico para uma solicitação Incluir códigos de erro


Um dispositivo publica uma solicitação Incluir códigos de erro no tópico a seguir:

```
iotdevice-1/add/diag/errorCodes
```

### Formato da mensagem para uma solicitação Incluir códigos de erro


O valor associado a `errorCode` é o código de erro do dispositivo atual e deve ser incluído no {{site.data.keyword.iot_short_notm}}.

Formato da solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/add/diag/errorCodes
{
    "d": {
        "errorCode": number
    },
    "reqId": "string"
}
```

Formato da resposta:

```
Incoming message from the server:

Topic: iotdm-1/response
{
    "rc": number,
    "reqId": "string"
}
```

### Códigos de resposta para uma solicitação Incluir códigos de erro

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|


## Solicitações Limpar códigos de erro
{: #diag-clear-error-codes}

Os dispositivos podem solicitar ao {{site.data.keyword.iot_short_notm}} para limpar todos os códigos de erro para o dispositivo usando o tipo de solicitação Limpar códigos de erro.

### Tópico para uma solicitação Limpar códigos de erro


Um dispositivo publica esta solicitação ao tópico a seguir:

```
iotdevice-1/clear/diag/errorCodes
```

### Formato da mensagem para uma solicitação Limpar códigos de erro


Formato da solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/clear/diag/errorCodes
{
    "reqId": "string"
}
```

Formato da resposta:

```
Incoming message from the server:

Topic: iotdm-1/response
{
    "rc": 200,
    "reqId": "string"
}
```

### Códigos de resposta para uma solicitação Limpar códigos de erro

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.|
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|


## Solicitações Incluir log
{: #diag-add-log}

Os dispositivos podem escolher se o suporte de gerenciamento de dispositivo do {{site.data.keyword.iot_short_notm}} deve ser notificado sobre as mudanças incluindo uma nova entrada de log. As entradas de log incluem uma mensagem de log, o registro de data e hora, a gravidade e dados diagnósticos opcionais binários codificados por base64.

### Tópico para uma solicitação Incluir log
Um dispositivo publica esta solicitação ao tópico a seguir:

```
iotdevice-1/add/diag/log
```


### Formato da mensagem para uma solicitação Incluir log

A tabela a seguir descreve o formato dos atributos da mensagem não enviada:

|Atributo |Descrição |
|:---|:---|
|`message`|Especifica uma mensagem de diagnóstico que precisa ser incluída no {{site.data.keyword.iot_short_notm}}|
|`timestamp`|Especifica a data e hora da entrada de log no formato ISO8601 |
|`data`|Especifica dados diagnósticos opcionais codificados por base64|
|`severity`|Especifica a gravidade da mensagem (0: informativa, 1: aviso, 2: erro)|


Formato da solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/add/diag/log
{
    "d": {
        "message": "string",
        "timestamp": "string",
        "data": "string",
        "severity": number
    },
    "reqId": "string"
}
```

Formato da resposta:

```
Incoming message from the server:

Topic: iotdm-1/response
{
    "rc": number,
    "reqId": "string"
}
```


### Códigos de resposta para uma solicitação Incluir log

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.|
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|

## Solicitações Limpar logs
{: #diag-clear-logs}

Os dispositivos podem solicitar ao {{site.data.keyword.iot_short_notm}} para limpar todas as entradas de log para o dispositivo usando o tipo de solicitação Limpar logs.

### Tópico para uma solicitação Limpar logs


Um dispositivo publica uma solicitação Limpar logs no tópico a seguir:

```
iotdevice-1/clear/diag/log
```

### Formato da mensagem para uma solicitação Limpar logs


Formato da solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/clear/diag/log
{
    "reqId": "string"
}
```

Formato da resposta:

```
Incoming message from the device:

Topic: iotdm-1/response
{
    "rc": number,
    "reqId": "string"
}
```

### Códigos de resposta para uma solicitação Limpar logs

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.|
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|

## Solicitações Observar mudanças de atributos
{: #observations-observe}

O {{site.data.keyword.iot_short_notm}} pode enviar uma solicitação Observar mudanças de atributos a um dispositivo para observar mudanças de um ou mais atributos de dispositivo usando o tipo de solicitação Observar mudanças de atributos. Quando o dispositivo recebe a solicitação, ele deve enviar uma solicitação de notificação ao {{site.data.keyword.iot_short_notm}} sempre que os valores dos atributos observados mudarem.

**Importante:** os dispositivos devem implementar, observar, notificar e cancelar operações para suportar os tipos de solicitações [Ações de firmware - Atualizar](requests.html#firmware-actions-update).

### Tópico para uma solicitação Observar mudanças de atributos


O servidor publica uma solicitação Observar mudanças de atributos no tópico a seguir:

```
iotdm-1/observe
```

### Formato da mensagem para uma solicitação Observar mudanças de atributos


A matriz `fields` é uma matriz dos atributos de dispositivo do modelo de dispositivo. Se um campo complexo, como `mgmt.firmware` for especificado, espera-se que seus campos subjacentes sejam atualizados ao mesmo tempo para que apenas uma única mensagem de notificação seja gerada.

O parâmetro `message` usado na resposta poderá ser especificado se o valor do parâmetro `rc` não for `200`. Se qualquer valor de parâmetro especificado não puder ser recuperado, o valor do parâmetro `rc` deverá ser configurado como `404` se o atributo não for localizado ou como `500` por qualquer outra razão. Quando os valores para os parâmetros não puderem ser localizados, a matriz `fields` deverá conter elementos que tenham `field` configurado para o nome de cada parâmetro que não pôde ser lido. O parâmetro `value` deve ser omitido. Para o parâmetro de código de resposta ser configurado como `200`, `field` e `value` devem ser especificados, em que `value` é o valor atual de um atributo identificado pelo valor do parâmetro `field`.

Formato da solicitação:

```
Incoming message from the server:

Topic: iotdm-1/observe
{
    "d": {
        "fields": [
            {
                "field": "field_name" }
        ]
    },
    "reqId": "string"
}
```

Formato da resposta:

```
Outgoing message from the device:

Topic: iotdevice-1/response
{
    "rc": number,
    "message": "string",
    "d": {
        "fields": [
            {
                "field": "field_name",
                "value": "field_value"
            }
        ]
    },
    "reqId": "string"
}
```


## Solicitações Cancelar observação de atributos
{: #observations-cancel}

O {{site.data.keyword.iot_short_notm}} pode enviar uma solicitação a um dispositivo para cancelar a observação atual de um ou mais atributos de dispositivo usando o tipo de solicitação Cancelar observação de atributos. A parte `fields` da solicitação é uma matriz de nomes de atributos do dispositivo do modelo de dispositivo, por exemplo, os parâmetros `location`, `mgmt.firmware` ou `mgmt.firmware.state`.

O parâmetro `message` poderá ser especificado se o valor do parâmetro `rc` não for `200`.

**Importante:** os dispositivos devem implementar, observar, notificar e cancelar operações para suportar os tipos de solicitações [Ações de firmware - Atualizar](requests.html#firmware-actions-update).

### Tópico para uma solicitação Cancelar observação de atributos


O servidor publica uma solicitação Cancelar observação de atributos no tópico a seguir:

```
iotdm-1/cancel
```


### Formato da mensagem para uma solicitação Cancelar observação de atributos


Formato da solicitação:

```
Incoming message from the server:

Topic: iotdm-1/cancel
{
    "d": {
        "fields": [
            {
                "field": "field_name" }
        ]
    },
    "reqId": "string"
}
```

Formato da resposta:

```
Outgoing message from the device:

Topic: iotdevice-1/response
{
    "rc": number,
    "message": "string",
    "reqId": "string"
}
```



## Solicitações Notificar mudanças de atributos
{: #observations-notify}

O {{site.data.keyword.iot_short_notm}} pode fazer uma solicitação de observação para um atributo específico ou um conjunto de valores usando o tipo de solicitação Notificar mudanças de atributos. Quando o valor do atributo ou atributos muda, o dispositivo deve enviar uma notificação contendo o valor mais recente.

O valor do parâmetro `field` é o nome do atributo que mudou e `value` é o valor atual do atributo. O atributo pode ser um campo complexo. Se diversos valores em um campo complexo forem atualizados como resultado de uma única operação, apenas uma única mensagem de notificação será enviada.

**Importante:** os dispositivos devem implementar, observar, notificar e cancelar operações para suportar os tipos de solicitações [Ações de firmware - Atualizar](requests.html#firmware-actions-update).


### Tópico para uma solicitação Notificar mudança de atributos


Um dispositivo publica uma solicitação Notificar mudança de atributos no tópico a seguir:

```
iotdevice-1/notify
```


### Formato da mensagem para uma solicitação Notificar mudança de atributos


Formato da solicitação:

```
Outgoing message from the device:

Topic: iotdevice-1/notify
{
    "d": {
        "fields": [
            {
                "field": "field_name",
                "value": "field_value"
            }
        ]
    }
    "reqId": "string"
}
```

Formato da resposta:

```
Incoming message from the server:

Topic: iotdm-1/response
{
    "rc": number,
    "reqId": "string"
}
```

### Códigos de resposta para uma solicitação de Notificar mudança de atributos

|Código da Resposta |Mensagem de e-mail |
|:---|:---|
|200   |A opera‡Æo foi bem-sucedida.|
|400   |A mensagem de entrada não corresponde ao formato esperado ou um dos valores está fora do intervalo válido.|
|404   |O dispositivo não foi registrado com o {{site.data.keyword.iot_short_notm}}.|
|409   |O recurso não pôde ser atualizado devido a um conflito (por exemplo, o recurso está sendo atualizado por duas solicitações simultâneas). A atualização pode ser tentada novamente mais tarde.|
|500   |Ocorreu um erro interno.|
