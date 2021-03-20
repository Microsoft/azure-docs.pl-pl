---
title: Stan utrwalania w systemie Linux — Azure Event Grid IoT Edge | Microsoft Docs
description: Utrwalanie metadanych w systemie Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171486"
---
# <a name="persist-state-in-linux"></a>Stan utrwalania w systemie Linux

Tematy i subskrypcje utworzone w module Event Grid są domyślnie przechowywane w systemie plików kontenera. Bez trwałości, jeśli moduł zostanie ponownie wdrożony, wszystkie utworzone metadane zostałyby utracone. Aby zachować dane między wdrożeniami i ponownymi uruchomieniami, należy utrzymać dane poza systemem plików kontenera.

Domyślnie tylko metadane są utrwalane, a Zdarzenia nadal są przechowywane w pamięci w celu zwiększenia wydajności. Postępuj zgodnie z sekcją zdarzenia utrwalania, aby włączyć również trwałość zdarzenia.

W tym artykule przedstawiono procedurę wdrażania modułu Event Grid z trwałością we wdrożeniach systemu Linux.

> [!NOTE]
>Moduł Event Grid działa jako użytkownik z niskim poziomem uprawnień z identyfikatorami UID `2000` i Name `eventgriduser` .

## <a name="persistence-via-volume-mount"></a>Trwałość za pośrednictwem instalacji woluminu

 [Woluminy platformy Docker](https://docs.docker.com/storage/volumes/) są używane do zachowywania danych między wdrożeniami. Możesz zezwolić platformie Docker na automatyczne tworzenie nazwanego woluminu w ramach wdrażania modułu Event Grid. Ta opcja jest najprostszą opcją. Nazwę woluminu, który ma zostać utworzony, można określić w sekcji **powiązania** w następujący sposób:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Nie zmieniaj drugiej części wartości powiązania. Wskazuje konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Linux musi być **/App/metadataDb**.

Na przykład następująca konfiguracja spowoduje utworzenie woluminu **egmetadataDbVol** , gdzie metadane zostaną utrwalone.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Zamiast instalować wolumin, można utworzyć katalog w systemie hosta i zainstalować ten katalog.

## <a name="persistence-via-host-directory-mount"></a>Trwałość przy użyciu instalacji katalogu hosta

Zamiast woluminu platformy Docker można również zainstalować folder hosta.

1. Najpierw Utwórz użytkownika o nazwie **eventgriduser** i identyfikatorze **2000** na komputerze hosta, uruchamiając następujące polecenie:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Utwórz katalog w systemie plików hosta, uruchamiając następujące polecenie.

   ```sh
   md <your-directory-name-here>
   ```

    Na przykład uruchomienie następującego polecenia spowoduje utworzenie katalogu o nazwie **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Następnie utwórz właściciela **eventgriduser** tego folderu, uruchamiając następujące polecenie.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Na przykład

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Użyj **powiązań** , aby zainstalować katalog i ponownie wdrożyć moduł Event Grid z Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Na przykład

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Nie zmieniaj drugiej części wartości powiązania. Wskazuje konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Linux trzeba mieć **/App/metadataDb** i **/App/eventsDb**


## <a name="persist-events"></a>Zdarzenia utrwalania

Aby włączyć trwałość zdarzeń, należy najpierw włączyć trwałość metadanych w ramach instalacji woluminu lub instalacji katalogu hosta za pomocą powyższych sekcji.

Ważne kwestie dotyczące utrwalania zdarzeń:

* Zdarzenia utrwalania są włączane dla każdej subskrypcji zdarzeń i są zgodą na zamontowanie woluminu lub katalogu.
* Trwałość zdarzenia jest konfigurowana w subskrypcji zdarzeń podczas tworzenia i nie można jej modyfikować po utworzeniu subskrypcji zdarzeń. Aby przełączać trwałość zdarzeń, należy usunąć i ponownie utworzyć subskrypcję zdarzeń.
* Utrwalanie zdarzeń jest niemal zawsze wolniejsze niż w przypadku operacji w pamięci, jednak różnica między szybkością zależy od charakterystyki dysku. Kompromis między szybkością i niezawodnością jest nieodłączny dla wszystkich systemów obsługi komunikatów, ale ogólnie mówiąc, tylko w dużej skali.

Aby włączyć trwałość zdarzeń w subskrypcji zdarzeń, ustaw opcję `persistencePolicy` na `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
