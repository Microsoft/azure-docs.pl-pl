---
title: Lista kontrolna wstępnego certyfikacji dla ofert modułu IoT Edge w portalu Azure Marketplace
description: Dowiedz się więcej o wymaganiach dotyczących certyfikacji w przypadku publikowania ofert modułu IoT Edge w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 31c19f62f0328fca05562eaa2f19b7a79c0f3e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562702"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Lista kontrolna wstępnego certyfikacji dla modułów IoT Edge

> [!NOTE]
> Zdecydowanie zalecamy wydawców zapoznanie się z listą kontrolną i sprawdzanie poprawności funkcjonalności modułu przed przesłaniem do certyfikatów. Przyspiesza to proces certyfikacji dzięki skróceniu potrzeb związanych z zmianami i reprzesyłaniem.

## <a name="validation-of-image"></a>Sprawdzanie poprawności obrazu

Po przygotowaniu obrazu modułu krawędzi do przesłania wykonaj następujące kroki, aby upewnić się, że obraz działa tak, jak oczekuje firma Microsoft.

### <a name="steps-to-perform-in-the-azure-portal"></a>Kroki do wykonania w Azure Portal

1. Otwórz witrynę [Azure Portal](https://partner.microsoft.com/).
1. Utwórz grupę zasobów.
1. Tworzenie centrum IoT Hub.
1. Utwórz urządzenie IoT Edge.
1. Skopiuj parametry połączenia i Zapisz je w Notatniku.
1. Wybierz **utworzone moduły na urządzeniu brzegowym**.
1. Dodaj szczegóły ACR, gdzie znajduje się Najnowsza wersja obrazu.
1. Wybierz pozycję **Dodaj moduł IoT Edge** i podaj:
    - Identyfikator URI obrazu w ustawieniu modułu
    - Zmienna środowiskowa (taka sama jak wartość dodana w centrum partnerskim)
    - Opcje tworzenia kontenera (takie same jak w centrum partnerskim)
    - Ustawienie przędzki modułu (taka sama jak wartość dodana w centrum partnerskim)
1. Dodaj trasy (takie same, jak co zostało dodane w centrum partnerskim).
1. Wybierz pozycję **Przejrzyj i utwórz**.

Moduły brzegowe są wdrażane na urządzeniu brzegowym utworzonym na platformie Azure.

### <a name="steps-to-perform-on-the-device"></a>Kroki do wykonania na urządzeniu

#### <a name="device-details"></a>Szczegóły urządzenia

Zespół certyfikacji używa poniższego sprzętu do sprawdzania poprawności obrazów w różnych architekturach:

- W przypadku obrazów x64 maszyna wirtualna platformy Azure ma rozmiar konfiguracji w formacie Standard D2s v3 z systemem Ubuntu Server 18.04/Ubuntu Server 16,04.
- W przypadku obrazów Azure Resource Manager 32 Raspberry Pi 3 model B.
- W przypadku obrazów Azure Resource Manager 64 jest to NVIDIA Jetson Nano 4 GB.

#### <a name="steps"></a>Kroki

1. Upewnij się, że można uzyskać dostęp do utworzonych urządzeń/maszyn wirtualnych za poorednictwem wychodzenia.
1. Pobierz [środowisko uruchomieniowe IoT Edge](../iot-edge/how-to-install-iot-edge.md) na urządzenie.
1. Zaktualizuj parametry połączenia skopiowane w kroku 5 do pliku config. YAML.
1. Uruchom ponownie moduł brzegowy za pomocą `sudo systemctl restart iotedge` .
1. Sprawdź, czy moduł został wdrożony na urządzeniu z `sudo iotedge list` ; powinien być w stanie uruchomienia.
1. Upewnij się, że dzienniki modułu wdrożonego za pomocą programu nie `sudo iotedge logs “Module Name“ -f` mają żadnych błędów. Jeśli występują znane błędy, opisz je w **uwagi do** Centrum partnerskiego przed przesłaniem oferty.

## <a name="metadata-validation"></a>Sprawdzanie poprawności metadanych

Sprawdź następujące informacje:

- Najnowszy tag jest wymieniony w centrum partnerskim i Azure Container Registry.
- Minimalny wymóg wymagania sprzętowego został dodany w opisie oferty.
- Nazwa użytkownika i hasło usługi Azure Container Registry są aktualizowane i dodawane w centrum partnerskim.
- Dokładność odpowiedniej **Właściwości przędzy** , *Jeśli ma zastosowanie*.
- Dokładność żądanych **zmiennych środowiskowych** , *Jeśli ma zastosowanie*.
- Dokładność żądanych **opcji tworzenia** , *Jeśli ma zastosowanie*.
- Parametry połączenia zarządzania potencjalnymi klientami są obecne.
- Zasady zachowania poufności informacji
- Warunki użytkowania obecne
- Dodawanie obsługiwanego linku IoT Edge urządzenia z [wykazu urządzeń usługi Azure IoT](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie modułów z komercyjnej witryny Marketplace](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [Publikowanie modułu brzegowego w centrum partnerskim](./partner-center-portal/azure-iot-edge-module-creation.md)
- [Wdróż moduł IoT Edge](../iot-edge/quickstart-linux.md)