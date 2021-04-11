---
title: Konfigurowanie wydawcy OPC — Azure | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania wydawcy programu OPC w celu określenia zmian danych węzła OPC UA, zdarzeń OPC UA do opublikowania oraz formatu telemetrii.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 405fa04a3b6af7f0c87888aa48aba9bdffeaf282
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646448"
---
# <a name="configure-opc-publisher"></a>Konfigurowanie wydawcy OPC

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Można skonfigurować OPC wydawcę, aby określić:

- Dane węzła OPC UA zmienią się na publikowanie.
- Zdarzenia OPC UA do opublikowania.
- Format telemetrii.

Wydawcę OPC można skonfigurować przy użyciu plików konfiguracyjnych lub wywołań metod.

## <a name="use-configuration-files"></a>Korzystanie z plików konfiguracji

W tej sekcji opisano opcje konfigurowania publikowania węzłów OPC UA przy użyciu plików konfiguracyjnych.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Użycie pliku konfiguracji w celu skonfigurowania zmian danych do opublikowania

Najprostszym sposobem skonfigurowania węzłów OPC UA do opublikowania jest plik konfiguracji. Format pliku konfiguracji jest udokumentowany w [publishednodes.js](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) w repozytorium.

Składnia pliku konfiguracji została zmieniona z upływem czasu. Program Publisher OPC nadal odczytuje stare formaty, ale konwertuje je do najnowszego formatu, gdy zachowuje konfigurację.

Poniższy przykład przedstawia format pliku konfiguracji:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Konfigurowanie zdarzeń publikowania przy użyciu pliku konfiguracji

Aby opublikować zdarzenia OPC UA, należy użyć tego samego pliku konfiguracji, co w przypadku zmian danych.

Poniższy przykład pokazuje, jak skonfigurować publikowanie dla zdarzeń generowanych przez [serwer SimpleEvents](https://github.com/OPCFoundation/UA-.NETStandard-Samples/tree/master/Workshop/SimpleEvents/Server). Serwer SimpleEvents można znaleźć w [repozytorium OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard-Samples) :

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Użyj wywołań metod

W tej sekcji opisano wywołania metod, których można użyć w celu skonfigurowania wydawcy OPC.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurowanie przy użyciu wywołań metod OPC UA

Wydawca OPC zawiera serwer OPC UA, do którego można uzyskać dostęp na porcie 62222. Jeśli nazwa hosta jest **wydawcą**, identyfikator URI punktu końcowego to: `opc.tcp://publisher:62222/UA/Publisher` .

Ten punkt końcowy udostępnia cztery następujące metody:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurowanie przy użyciu wywołań metod bezpośrednich IoT Hub

Wydawca OPC implementuje następujące IoT Hub bezpośrednie wywołania metody:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

Format ładunku JSON żądania metody i odpowiedzi są zdefiniowane w [opcpublisher/HubMethodModel. cs](https://github.com/Azure/iot-edge-opc-publisher/tree/master/opcpublisher).

W przypadku wywołania nieznanej metody w module reaguje na ciąg informujący, że metoda nie jest zaimplementowana. Nieznaną metodę można wywołać w celu wysłania polecenia ping do modułu.

### <a name="configure-username-and-password-for-authentication"></a>Konfigurowanie nazwy użytkownika i hasła na potrzeby uwierzytelniania

Tryb uwierzytelniania można ustawić za pomocą wywołań metody bezpośredniej IoT Hub. Ładunek musi zawierać właściwość **OpcAuthenticationMode** oraz nazwę użytkownika i hasło:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Hasło jest szyfrowane przez klienta obciążenia IoT Hub i przechowywane w konfiguracji wydawcy. Aby zmienić uwierzytelnianie z powrotem na anonimowe, użyj metody z następującym ładunkiem:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Jeśli właściwość **OpcAuthenticationMode** nie jest ustawiona w ładunku, ustawienia uwierzytelniania pozostaną niezmienione w konfiguracji.

## <a name="configure-telemetry-publishing"></a>Konfigurowanie publikowania danych telemetrycznych

Gdy Wydawca OPC odbiera powiadomienie o zmianie wartości w opublikowanym węźle, generuje komunikat w formacie JSON, który jest wysyłany do IoT Hub.

Zawartość tego komunikatu w formacie JSON można skonfigurować przy użyciu pliku konfiguracji. Jeśli dla opcji nie określono pliku konfiguracji `--tc` , zostanie użyta domyślna konfiguracja zgodna z [akceleratorem rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory).

Jeśli OPC Wydawca jest skonfigurowany do tworzenia komunikatów wsadowych, są one wysyłane jako prawidłowa tablica JSON.

Dane telemetryczne pochodzą z następujących źródeł:

- Konfiguracja węzła wydawcy OPC dla węzła
- Obiekt **MonitoredItem** stosu OPC UA, dla którego Wydawca programu OPC otrzymał powiadomienie.
- Argument przesłany do tego powiadomienia, który zawiera szczegółowe informacje dotyczące zmiany wartości danych.

Dane telemetryczne, które są umieszczane w wiadomości w formacie JSON, to wybór ważnych właściwości tych obiektów. Jeśli potrzebujesz więcej właściwości, musisz zmienić bazę kodu wydawcy OPC.

Składnia pliku konfiguracji jest następująca:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is omitted (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak skonfigurować program OPC Publisher, sugerowanym następnym krokiem jest zapoznanie się z tematem [Uruchamianie programu OPC Publisher](howto-opc-publisher-run.md).
