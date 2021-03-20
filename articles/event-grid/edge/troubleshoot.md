---
title: Rozwiązywanie problemów — Azure Event Grid IoT Edge | Microsoft Docs
description: Rozwiązywanie problemów w Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171350"
---
# <a name="common-issues"></a>Typowe problemy

Jeśli wystąpią problemy z używaniem Azure Event Grid na IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika w celu rozwiązywania problemów i rozwiązania problemu.

## <a name="view-event-grid-module-logs"></a>Wyświetlanie dzienników modułu Event Grid

Aby rozwiązać problem, może być konieczne uzyskanie dostępu do dzienników modułu Event Grid. Aby to zrobić, na maszynie wirtualnej, na której wdrożono moduł, uruchom następujące polecenie:

W systemie Windows

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

W systemie Linux

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Nie można wykonać żądań HTTPS

* Najpierw upewnij się, że moduł Event Grid ma **przychodzące: obiektu ServerAuth: tlsPolicy** ustawiony na wartość **Strict** lub **Enabled**.

* W przypadku komunikacji między modułami upewnij się, że nastąpi wywołanie na porcie **4438** , a nazwa modułu pasuje do wdrożonego elementu. 

  Na przykład, jeśli moduł Event Grid został wdrożony o nazwie **eventgridmodule** , adres URL powinien być adresem **https://eventgridmodule:4438** . Upewnij się, że wielkość liter i numer portu są poprawne.
    
* Jeśli jest to moduł inny niż IoT, upewnij się, że port Event Grid jest mapowany na maszynę hosta podczas wdrażania na przykład.

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Nie można wykonać żądań HTTP

* Najpierw upewnij się, że moduł Event Grid ma **przychodzący: obiektu ServerAuth: tlsPolicy** ustawiony na wartość **Enabled** lub **Disabled**.

* W przypadku komunikacji między modułami upewnij się, że nastąpi wywołanie na porcie **5888** , a nazwa modułu pasuje do wdrożonego elementu. 

  Na przykład, jeśli moduł Event Grid został wdrożony o nazwie **eventgridmodule** , adres URL powinien być adresem **http://eventgridmodule:5888** . Upewnij się, że wielkość liter i numer portu są poprawne.
    
* Jeśli jest to moduł inny niż IoT, upewnij się, że port Event Grid jest mapowany na maszynę hosta podczas wdrażania na przykład.

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Łańcuch certyfikatów został wystawiony przez urząd, który nie jest zaufany

Domyślnie moduł Event Grid jest skonfigurowany do uwierzytelniania klientów przy użyciu certyfikatu wystawionego przez demona zabezpieczeń IoT Edge. Upewnij się, że klient przedstawia certyfikat, który jest katalogiem głównym tego łańcucha.

Klasa **IoTSecurity** w programie [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) pokazuje, jak pobrać certyfikaty z demona zabezpieczeń IoT Edge i użyć go do skonfigurowania wywołań wychodzących.

Jeśli jest to Środowisko nieprodukcyjne, można wyłączyć uwierzytelnianie klienta. Aby uzyskać szczegółowe informacje o tym, jak to zrobić [, zobacz Zabezpieczenia i uwierzytelnianie](security-authentication.md) .

## <a name="debug-events-not-received-by-subscriber"></a>Zdarzenia debugowania nieodebrane przez subskrybenta

Typowymi przyczynami tego są:

* Zdarzenie nigdy nie zostało pomyślnie ogłoszone. W przypadku wysyłania zdarzenia do modułu Event Grid należy otrzymać kod StatusCode protokołu HTTP o wartości 200 (OK).

* Sprawdź subskrypcję zdarzeń, aby sprawdzić:
    * Adres URL punktu końcowego jest prawidłowy
    * Wszystkie filtry w subskrypcji nie powodują powstania zdarzenia "upuszczone".

* Sprawdź, czy moduł subskrybenta jest uruchomiony

* Zaloguj się do maszyny wirtualnej, na której wdrożono moduł Event Grid i Wyświetl jego dzienniki.

* Włącz rejestrowanie dostarczania na podstawie ustawienia **brokera: logDeliverySuccess = true** i ponownego wdrażania modułu Event Grid i ponawiania próby żądania. Włączenie rejestrowania na dostarczenie może mieć wpływ na przepływność i opóźnienie, dlatego po zakończeniu debugowania należy włączyć tę opcję z powrotem do **brokera: logDeliverySuccess = false**  i ponowne wdrożenie modułu Event Grid.

* Włącz metryki, ustawiając **metryki: reportertype = konsola** i Wdróż ponownie moduł Event Grid. Wszelkie operacje po tej operacji spowodują, że metryki są rejestrowane na konsoli modułu Event Grid, które mogą być używane do dalszej analizy. Nasze zalecenie polega na włączeniu metryk tylko dla debugowania, a po jej zakończeniu, aby ją wyłączyć przez ustawienie **metryk: reportertype = none** i ponowne wdrożenie modułu Event Grid.

## <a name="next-steps"></a>Następne kroki

Zgłoś wszelkie problemy, sugestie dotyczące używania Event Grid w IoT Edge [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .