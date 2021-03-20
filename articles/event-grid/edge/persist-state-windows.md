---
title: Stan utrwalania w systemie Windows Azure Event Grid IoT Edge | Microsoft Docs
description: Stan utrwalania w systemie Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002821"
---
# <a name="persist-state-in-windows"></a>Stan utrwalania w systemie Windows

Tematy i subskrypcje utworzone w module Event Grid są domyślnie przechowywane w systemie plików kontenera. Bez trwałości, jeśli moduł zostanie ponownie wdrożony, wszystkie utworzone metadane zostałyby utracone. Aby zachować dane między wdrożeniami i ponownymi uruchomieniami, należy utrzymać dane poza systemem plików kontenera. 

Domyślnie tylko metadane są utrwalane, a Zdarzenia nadal są przechowywane w pamięci w celu zwiększenia wydajności. Postępuj zgodnie z sekcją zdarzenia utrwalania, aby włączyć również trwałość zdarzenia.

Ten artykuł zawiera kroki niezbędne do wdrożenia modułu Event Grid z trwałością we wdrożeniach systemu Windows.

> [!NOTE]
>Moduł Event Grid jest uruchamiany jako **ContainerUser** użytkownika z niskim poziomem uprawnień w systemie Windows.

## <a name="persistence-via-volume-mount"></a>Trwałość za pośrednictwem instalacji woluminu

[Woluminy platformy Docker](https://docs.docker.com/storage/volumes/) są używane do zachowywania danych między wdrożeniami. Aby zainstalować wolumin, należy go utworzyć przy użyciu poleceń platformy Docker, udzielić uprawnień, aby umożliwić odczytywanie i zapisywanie w kontenerze, a następnie wdrożenie modułu.

1. Utwórz wolumin, uruchamiając następujące polecenie:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Na przykład

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Pobierz katalog hosta, na który jest mapowany wolumin, uruchamiając poniższe polecenie

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Na przykład

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Przykładowe dane wyjściowe:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Dodaj grupę **Użytkownicy** do wartości wskazywanej przez **mountpoint** w następujący sposób:
    1. Uruchom Eksploratora plików.
    1. Przejdź do folderu wskazywanego przez **mountpoint**.
    1. Kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Właściwości**.
    1. Wybierz opcję **Zabezpieczenia**.
    1. W obszarze * nazwy grup lub użytkowników wybierz pozycję **Edytuj**.
    1. Wybierz pozycję **Dodaj**, wprowadź `Users` , wybierz pozycję **Sprawdź nazwy**, a następnie wybierz pozycję **OK**.
    1. W obszarze *uprawnienia dla użytkowników* wybierz pozycję **Modyfikuj**, a następnie wybierz pozycję **OK**.
1. Użyj **powiązań** , aby zainstalować ten wolumin i ponownie wdrożyć moduł Event Grid z Azure Portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Nie zmieniaj drugiej części wartości powiązania. Wskazuje on konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Windows musi to być **C: \\ App \\ metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Trwałość przy użyciu instalacji katalogu hosta

Zamiast instalować wolumin, można utworzyć katalog w systemie hosta i zainstalować ten katalog.

1. Utwórz katalog w systemie plików hosta, uruchamiając następujące polecenie.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Na przykład

   ```sh
   mkdir C:\myhostdir
   ```
1. Użyj **powiązań** , aby zainstalować katalog i ponownie wdrożyć moduł Event Grid z Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Nie zmieniaj drugiej części wartości powiązania. Wskazuje on konkretną lokalizację w module. W przypadku modułu Event Grid w systemie Windows musi to być **C: \\ App \\ metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Zdarzenia utrwalania

Aby włączyć trwałość zdarzeń, należy najpierw włączyć trwałość zdarzeń przez zainstalowanie woluminu lub instalację katalogu hosta za pomocą powyższych sekcji.

Ważne kwestie dotyczące utrwalania zdarzeń:

* Zdarzenia utrwalania są włączane dla każdej subskrypcji zdarzeń i są zgodą na zamontowanie woluminu lub katalogu.
* Trwałość zdarzenia jest konfigurowana w subskrypcji zdarzeń podczas tworzenia i nie można jej modyfikować po utworzeniu subskrypcji zdarzeń. Aby przełączać trwałość zdarzeń, należy usunąć i ponownie utworzyć subskrypcję zdarzeń.
* Utrwalanie zdarzeń jest niemal zawsze wolniejsze niż w przypadku operacji w pamięci, jednak różnica między szybkością zależy od charakterystyki dysku. Kompromis między szybkością i niezawodnością jest nieodłączny dla wszystkich systemów obsługi komunikatów, ale tylko w dużej skali.

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