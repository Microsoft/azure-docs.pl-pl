---
title: Opcje architektury rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule opisano architekturę i opcje techniczne wykonywane w ramach zdalnego monitorowania
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 27a65d8a499f6eba130dc9537de3cb2b3dc3abe8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446763"
---
# <a name="remote-monitoring-architectural-choices"></a>Wybory dotyczące architektury związane z monitorowaniem zdalnym

Akcelerator rozwiązania do monitorowania zdalnego usługi Azure IoT jest akceleratorem "open source", który jest licencjonowany dla rozwiązania. Aby ułatwić przyspieszenie procesu tworzenia aplikacji IoT, można wyświetlić typowe scenariusze IoT, takie jak:

- Łączność urządzeń
- Zarządzanie urządzeniami
- Przetwarzanie strumienia

Rozwiązanie do monitorowania zdalnego jest zgodne z zalecaną [architekturą referencyjną usługi Azure IoT](/azure/architecture/reference-architectures/iot).

W tym artykule opisano opcje architektury i techniczne dotyczące poszczególnych podsystemów monitorowania zdalnego. Jednak opcje techniczne firmy Microsoft wykonywane w ramach rozwiązania do zdalnego monitorowania nie są jedynym sposobem implementacji rozwiązania IoT do monitorowania zdalnego. Należy traktować implementację techniczną jako linię bazową do tworzenia aplikacji, która została pomyślnie utworzona, i należy ją zmodyfikować, aby:

- Dopasuj dostępne umiejętności i doświadczenia w organizacji.
- Spełnij potrzeby aplikacji w pionie.

## <a name="architectural-choices"></a>Wybory dotyczące architektury

Architektura zalecana przez firmę Microsoft dla aplikacji IoT to natywna, mikrousługowa i bezserwerowa w chmurze. Należy utworzyć różne podsystemy aplikacji IoT jako odrębne usługi, które można wdrożyć i skalować niezależnie. Te atrybuty zapewniają większą skalę, większą elastyczność podczas aktualizowania poszczególnych podsystemów i zapewniają elastyczność pozwalającą wybrać odpowiednią technologię dla każdego podsystemu.

Mikrousługi można zaimplementować przy użyciu więcej niż jednej technologii. Na przykład można wybrać jedną z następujących opcji, aby zaimplementować mikrousługę:

- Użyj technologii kontenera, takiej jak Docker z technologią bezserwerową, taką jak Azure Functions.
- Hostowanie mikrousług w usługach PaaS Services, takich jak Azure App Services.

## <a name="technology-choices"></a>Wybór technologi

Ta sekcja zawiera szczegółowe informacje na temat technologii dostępnych w rozwiązaniu do zdalnego monitorowania dla każdego z podsystemów podstawowych.

![Diagram podstawowy](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Brama chmury

Usługa Azure IoT Hub jest używana jako brama rozwiązania do monitorowania zdalnego. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) oferuje bezpieczną, dwukierunkową komunikację z urządzeniami.

W przypadku łączności z urządzeniem IoT można użyć:

- [Zestawy SDK urządzeń IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) do implementowania natywnej aplikacji klienckiej dla Twojego urządzenia. Zestawy SDK oferują otoki wokół interfejsu API REST IoT Hub i obsługują scenariusze, takie jak ponownych prób.
- Integracja z Azure IoT Edge do wdrażania modułów niestandardowych działających w kontenerach na urządzeniach i zarządzania nimi.
- Integracja z automatycznym zarządzaniem urządzeniami w IoT Hub, aby zarządzać połączonymi urządzeniami zbiorczo.

### <a name="stream-processing"></a>Przetwarzanie strumienia

W przypadku przetwarzania strumieniowego rozwiązanie do zdalnego monitorowania używa Azure Stream Analytics do przetwarzania złożonych reguł. Jeśli chcesz używać prostszych reguł, jest to niestandardowa mikrousługa z obsługą przetwarzania prostej reguły, chociaż ta konfiguracja nie jest częścią wdrożenia wbudowanego. Architektura referencyjna zaleca Azure Functions do przetwarzania prostych reguł i Azure Stream Analytics do przetwarzania złożonych reguł.

### <a name="storage"></a>Magazyn

W przypadku usługi Storage Akcelerator rozwiązania do zdalnego monitorowania używa zarówno Azure Time Series Insights, jak i Azure Cosmos DB. Azure Time Series Insights przechowuje komunikaty przychodzące przez IoT Hub z połączonych urządzeń. Akcelerator rozwiązania używa Azure Cosmos DB dla wszystkich innych magazynów, takich jak chłodny magazyn, definicje reguł, alerty i ustawienia konfiguracji.

Azure Cosmos DB to zalecane rozwiązanie do magazynowania ciepłego ogólnego przeznaczenia dla aplikacji IoT. Jednak rozwiązania takie jak Azure Time Series Insights i Azure Data Lake są odpowiednie dla wielu przypadków użycia. Dzięki Azure Time Series Insights możesz uzyskać dokładniejszy wgląd w dane czujników szeregów czasowych, wykrywania trendów trendy i anomalie. Ta funkcja umożliwia przeprowadzanie analiz głównych przyczyn i uniknięcie kosztownych przestojów.

> [!NOTE]
> Time Series Insights nie jest obecnie dostępna w chmurze platformy Azure w Chinach. Nowe wdrożenia akceleratora rozwiązań do monitorowania zdalnego w przypadku chmury platformy Azure w Chinach Użyj Cosmos DB dla wszystkich magazynów.

### <a name="business-integration"></a>Integracja biznesowa

Integracja biznesowa w rozwiązaniu do zdalnego monitorowania jest ograniczona do generacji alertów, które są umieszczane w magazynie w pamięci masowej. Połącz rozwiązanie z Azure Logic Apps, aby zaimplementować dokładniejsze scenariusze integracji biznesowej.

### <a name="user-interface"></a>Interfejs użytkownika

Interfejs użytkownika sieci Web jest zbudowany z obsługą skryptów JavaScript. Reagowanie oferuje powszechnie używaną branżową strukturę interfejsu użytkownika sieci Web i jest podobna do innych popularnych platform, takich jak elementy kątowe.

### <a name="runtime-and-orchestration"></a>Środowisko uruchomieniowe i aranżacja

Rozwiązanie do zdalnego monitorowania używa kontenerów platformy Docker do uruchamiania podsystemów z Kubernetes jako koordynator dla skalowania w poziomie. Ta architektura włącza poszczególne definicje skalowania dla każdego podsystemu. Jednak ta architektura wiąże się z DevOps kosztów, aby zapewnić aktualność maszyn wirtualnych i kontenerów oraz zapewnić ich bezpieczeństwo.

Alternatywy dla platformy Docker obejmują obsługę mikrousług w usługach PaaS, takich jak Azure App Service. Alternatywy Kubernetes obejmują koordynatorów, takie jak Service Fabric, DC/OS lub Swarm.

## <a name="next-steps"></a>Następne kroki

* [Tutaj](https://www.azureiotsolutions.com/)Wdróż rozwiązanie do monitorowania zdalnego.
* Poznaj kod GitHub w [językach C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) i [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Więcej informacji na temat architektury referencyjnej IoT [znajdziesz tutaj](/azure/architecture/reference-architectures/iot).