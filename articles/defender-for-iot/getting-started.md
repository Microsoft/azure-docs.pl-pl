---
title: Opcje wdrożenia
description: Zacznij od zapoznania się z podstawowym przepływem pracy usługi Defender dla funkcji i usług IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 6aa525fd7f2d82194baa2e2a0c910cb71509c2d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340020"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Wprowadzenie do usługi Azure Defender dla IoT

W tym artykule opisano procesy wdrażania i dołączania niezbędne do pobrania usługi Azure Defender dla IoT. Wymagane są również dodatkowe czynności. Zalecamy zapoznanie się z tymi krokami i zaznajomienie się z informacjami w dokumentach towarzyszących.

Po wykonaniu wszystkich kroków usługa Azure Defender dla czujników IoT będzie monitorować sieć. W zależności od sposobu skonfigurowania rozwiązania wykrywania mogą być również wysyłane do lokalnej konsoli zarządzania lub do IoT Hub.

Wykonaj następujące kroki, aby uzyskać dostęp do usługi Azure Defender w celu zapewnienia jej działania.

## <a name="1-set-up-azure"></a>1. Skonfiguruj platformę Azure

- Skonfiguruj konto platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta platformy Azure](/learn/modules/create-an-azure-account/).

- Zapora lub serwer proxy: Jeśli masz zaporę lub podobne urządzenie sieciowe, które jest skonfigurowane tak, aby zezwalać na określone połączenia, sprawdź, czy na zaporze lub serwerze proxy jest otwarta azure-devices.net:443 *. Jeśli symbole wieloznaczne nie są obsługiwane lub chcesz mieć większą kontrolę, należy otworzyć określoną IoT Hub FQDN w sieci lub na serwerze proxy. Aby uzyskać więcej informacji, zobacz [punkty końcowe IoT Hub referencyjnych](../iot-hub/iot-hub-devguide-endpoints.md).

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. Wdróż sprzęt, oprogramowanie i Dołącz do czujnika

- Kup sprzęt czujnika i zainstaluj oprogramowanie. Wykonaj kroki opisane tutaj, a aby uzyskać więcej informacji, zobacz ten artykuł oraz [Podręcznik sprzętu usługi Defender for IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) i [Podręcznik instalacji](https://aka.ms/AzureDefenderforIoTInstallSensorISO).

  - Po zainstalowaniu czujnika należy bezpiecznie zarejestrować poświadczenia logowania czujnika. Aby przekazać plik aktywacji do czujnika, potrzebne są poświadczenia.

  - Jeśli pracujesz z czujnikami zarządzanymi lokalnie, bezpiecznie Rejestruj adres IP czujnika lub nazwę czujnika zdefiniowanego w instalacji. Możesz chcieć użyć jej podczas tworzenia nazwy czujnika podczas rejestrowania czujnika w portalu Defender for IoT. Można ich użyć później w celu łatwiejszego śledzenia i spójnego nazewnictwa między nazwą rejestracji w portalu usługi Azure Defender dla IoT i adresem IP wdrożonego czujnika wyświetlanego w konsoli czujnika.

- Zarejestruj czujnik przy użyciu portalu Defender for IoT i Pobierz plik aktywacji czujnika.

- Przekaż plik aktywacji do czujnika.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. przeprowadź konfigurację sieci na potrzeby monitorowania i zarządzania czujnikami

- Podłącz czujnik do sieci. Opisane w [przewodniku konfigurowania sieci](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. Rozpocznij odnajdywanie sieci

- Dostosowywanie ustawień systemowych w konsoli czujnika.

- Połącz czujniki z lokalną konsolą zarządzania.

Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika usługi Azure Defender for IoT sensor](https://aka.ms/AzureDefenderforIoTUserGuide) i [Podręcznik użytkownika programu Defender for IoT w lokalnej konsoli zarządzania](https://aka.ms/DefenderForIoTManagementConsole).

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. Wypełnij informacje o alercie na platformie Azure.

- Aby wysłać informacje o alercie do usługi Azure, należy [skonfigurować platformę](how-to-configure-with-sentinel.md)Azure
 

## <a name="next-steps"></a>Następne kroki

- Włączanie usługi [Defender for IoT](quickstart-onboard-iot-hub.md)
- Skonfiguruj [rozwiązanie](quickstart-configure-your-solution.md)
- [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)
- Konfigurowanie [alertów niestandardowych](quickstart-create-custom-alerts.md)
- [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)