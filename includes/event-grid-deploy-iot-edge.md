---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050435"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Wdróż moduł IoT Edge Event Grid

Istnieje kilka sposobów wdrażania modułów na urządzeniu IoT Edge i wszystkie z nich pracują Azure Event Grid w IoT Edge. W tym artykule opisano kroki wdrażania Event Grid na IoT Edge z Azure Portal.

>[!NOTE]
> W tym samouczku zostanie wdrożony moduł Event Grid bez trwałości. Oznacza to, że wszystkie tematy i subskrypcje utworzone w tym samouczku zostaną usunięte po ponownym wdrożeniu modułu. Aby uzyskać więcej informacji na temat sposobu konfiguracji trwałości, zobacz następujące artykuły: [utrwalanie stanu w systemie Linux](../articles/event-grid/edge/persist-state-linux.md) lub [w stanie utrwalania systemu Windows](../articles/event-grid/edge/persist-state-windows.md). W przypadku obciążeń produkcyjnych zaleca się zainstalowanie modułu Event Grid z trwałością.

>[!IMPORTANT]
> W tym samouczku moduł Event Grid zostanie wdrożony z włączonym uwierzytelnianiem klienta i zezwolił subskrybentom protokołu HTTP. W przypadku obciążeń produkcyjnych zaleca się włączenie tylko żądań HTTPS i subskrybentów z włączonym uwierzytelnianiem klienta. Aby uzyskać więcej informacji na temat bezpiecznego konfigurowania modułu Event Grid, zobacz [zabezpieczenia i uwierzytelnianie](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Wybierz urządzenie IoT Edge

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do IoT Hub.
1. Wybierz pozycję **IoT Edge** z menu w sekcji **Automatyczne zarządzanie urządzeniami** . 
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń
1. Wybierz pozycję **Ustaw moduły**. Nie otwieraj strony. Wykonaj kroki opisane w następnej sekcji.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON.  Składa się z trzech kroków: **Dodawanie modułów**, **Określanie tras** i **przeglądanie wdrożenia**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **moduły wdrażania** wybierz pozycję **Dodaj** .
1. Z typów modułów na liście rozwijanej wybierz pozycję **IoT Edge module**
1. Podaj nazwę, obraz i opcje tworzenia kontenera:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nazwa**: eventgridmodule
   * **Identyfikator URI obrazu**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opcje tworzenia kontenera**:

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
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

 1. Kliknij pozycję **Zapisz**.
 1. Kliknij przycisk **dalej** , aby przejść do sekcji trasy

    > [!NOTE]
    > Jeśli używasz maszyny wirtualnej platformy Azure jako urządzenia brzegowego, Dodaj regułę portu przychodzącego, aby zezwolić na ruch przychodzący na porcie 4438. Aby uzyskać instrukcje dotyczące dodawania reguły, zobacz [Jak otworzyć porty na maszynie wirtualnej](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Skonfiguruj trasy

 Zachowaj trasy domyślne, a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd

### <a name="review-deployment"></a>Przegląd wdrożenia

1. Sekcja Przegląd przedstawia manifest wdrożenia JSON, który został utworzony na podstawie wybranych opcji w poprzednich dwóch sekcjach. Upewnij się, że na liście znajdują się dwa moduły: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się na IoT Edge środowisko uruchomieniowe i są wymagane wartości domyślne w każdym wdrożeniu.
1. Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**.

### <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

1. Po przesłaniu wdrożenia wrócisz do IoT Edge stronie Centrum IoT Hub.
1. Wybierz **urządzenie IoT Edge** , do którego należy wdrożenie, aby otworzyć jego szczegóły.
1. Na stronie Szczegóły urządzenia sprawdź, czy moduł Event Grid jest wymieniony jako **określony we wdrożeniu** i **zgłoszony przez urządzenie**.

Uruchomienie modułu na urządzeniu może potrwać kilka minut, a następnie zgłoszone z powrotem do IoT Hub. Odśwież stronę, aby zobaczyć zaktualizowany stan.