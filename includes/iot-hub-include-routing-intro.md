---
title: plik dołączania
description: plik dołączania
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85128192"
---
[Routing komunikatów](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) umożliwia wysyłanie danych telemetrycznych z urządzeń IoT do wbudowanych punktów końcowych zgodnych z centrum zdarzeń lub niestandardowych punktów końcowych, takich jak BLOB storage, Service Bus Queues, tematy Service Bus i Event Hubs. Aby skonfigurować niestandardowy Routing komunikatów, tworzysz [zapytania routingu](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) w celu dostosowania trasy zgodnej z określonym warunkiem. Po zakończeniu konfiguracji dane przychodzące są automatycznie rozsyłane do punktów końcowych przez usługę IoT Hub. Jeśli komunikat nie jest zgodny z żadną z określonych zapytań routingu, jest kierowany do domyślnego punktu końcowego.

W tym 2-częściowym samouczku dowiesz się, jak skonfigurować i używać tych niestandardowych zapytań routingu w IoT Hub. Komunikaty z urządzenia IoT są routowane do jednego z wielu punktów końcowych, w tym usługi BLOB Storage i kolejki Service Bus. Komunikaty do kolejki Service Bus są wybierane przez aplikację logiki i wysyłane za pośrednictwem poczty e-mail. Komunikaty, które nie mają zdefiniowanego niestandardowego routingu komunikatów, są wysyłane do domyślnego punktu końcowego, a następnie pobierane przez Azure Stream Analytics i wyświetlane w wizualizacji Power BI.

Aby ukończyć części 1 i 2 tego samouczka, należy wykonać następujące zadania:

**Część I: Tworzenie zasobów, Konfigurowanie routingu komunikatów**
> [!div class="checklist"]
> * Tworzenie zasobów — Centrum IoT Hub, konto magazynu, kolejka Service Bus i symulowane urządzenie. Można to zrobić za pomocą Azure Portal, szablonu Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.
> * Skonfiguruj punkty końcowe i trasy komunikatów w IoT Hub dla konta magazynu i kolejki Service Bus.

**Część II: wysyłanie komunikatów do centrum, wyświetlanie rozesłanych wyników**
> [!div class="checklist"]
> * Tworzenie aplikacji logiki, która jest wyzwalana i wysyła wiadomość e-mail po dodaniu komunikatu do kolejki usługi Service Bus.
> * Pobieranie i uruchamianie aplikacji, która symuluje urządzenie IoT wysyłające komunikaty do centrum przy użyciu różnych opcji routingu.
> * Tworzenie wizualizacji usługi Power BI na potrzeby danych wysyłanych do domyślnego punktu końcowego.
> * Przeglądanie wyników...
> * ...w kolejce usługi Service Bus i wiadomościach e-mail.
> * ...na koncie magazynu.
> * ...w wizualizacji usługi Power BI.

## <a name="prerequisites"></a>Wymagania wstępne

* W przypadku części 1 tego samouczka:
  - Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* W przypadku części 2 tego samouczka:
  - Należy wykonać część 1 tego samouczka i nadal dostępne zasoby.
  - Zainstaluj [program Visual Studio](https://www.visualstudio.com/).
  - Uzyskaj dostęp do konta Power BI, aby analizować usługę Stream Analytics domyślnego punktu końcowego. ([Wypróbuj bezpłatnie usługę Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)).
  - Posiadanie konta służbowego do wysyłania wiadomości e-mail z powiadomieniami.
  - Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie w tym samouczku jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
