---
title: Wymagania dotyczące certyfikacji zarządzanej krawędzi
description: Wymagania programu certyfikacji zarządzane przez program Edge
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969402"
---
# <a name="azure-certification-edge-managed"></a>Zarządzane brzegi certyfikacji platformy Azure 

Ten dokument zawiera informacje o możliwościach urządzenia, które zostaną przedstawione w katalogu certyfikowanych urządzeń platformy Azure. Możliwość to pojedynczy atrybut urządzenia, który może opisywać urządzenie. 

## <a name="program-purpose"></a>Cel programu

Certyfikacja zarządzana przy użyciu usługi Edge, przyrostowa Certyfikacja poza podstawową certyfikacją urządzenia z certyfikatem na platformie Azure Zarządzane przez brzegowe zespoły dotyczą standardów zarządzania urządzeniami podłączonymi do urządzeń z systemem Azure i sprawdza zgodność środowiska uruchomieniowego IoT Edge w przypadku wdrażania modułów i zarządzania nimi. (Wcześniej ten program został zidentyfikowany jako IoT Edge program certyfikacji). 

Certyfikacja zarządzana przez usługi Edge weryfikuje IoT Edge zgodności środowiska uruchomieniowego w celu wdrożenia modułów i zarządzania nim. Ten program zapewnia zaufanie do zarządzania urządzeniami IoT połączonymi z platformą Azure.

## <a name="requirements"></a>Wymagania

Certyfikacja zarządzana przez usługę Edge wymaga, aby wszystkie wymagania zostały spełnione przez [program bazowy urządzenia z certyfikatem platformy Azure](.\program-requirements-azure-certified-device.md).

**DPS: przeznaczenie testu polega na sprawdzeniu, czy urządzenie implementuje i obsługuje IoT Hub Device Provisioning Service przy użyciu jednej z trzech metod zaświadczania**

| **Nazwa**                | AzureReady. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Zapłon (w wersji zapoznawczej)                                                |
| **Dotyczy**          | Dowolne urządzenie                                      |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | AICS sprawdza poprawność obsługi kodu urządzenia. **1.** Użytkownik musi wybrać jedną z metod zaświadczania (X. 509, moduł TPM i klucz SAS). **2.** W zależności od metody zaświadczania użytkownik musi podejmować odpowiednie **działania, takie jak "Przekaż certyfikat** X. 509 do AICS Managed DPS scope **b** ". Zaimplementuj klucz sygnatury dostępu współdzielonego i klucz poręczenia na urządzeniu. **3.** Następnie użytkownik zobaczy przycisk "Połącz", aby nawiązać połączenie z usługą AICS Managed IoT Hub za pomocą funkcji DPS                                                    |
| **Zasoby**           |                                                      |
| **Zalecane platformy Azure:**     | Nie dotyczy                                                    |

## <a name="iot-edge"></a>IoT Edge

**Środowisko uruchomieniowe Edge istnieje: celem testu jest upewnienie się, że urządzenie zawiera IoT Edge środowisko uruchomieniowe ($edgehub i $edgeagent) działa poprawnie.**

| **Nazwa**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                          |
| **Dotyczy**          | Urządzenie usługi IoT Edge                                                   |
| **System operacyjny**                  | [Pomoc i SVR system operacyjny](../iot-edge/support.md)                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | AICS sprawdza możliwości wdrożenia zainstalowanego IoT Edge RT. **1.** Użytkownik musi określić określony system operacyjny (nie jest to system operacyjny, który nie znajduje się na liście pomoc/2) **.** AICS generuje swój plik config. YAML i wdraża kanoniczny, [symulowany moduł](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **.** AICS sprawdza, czy podsystem kontenerów zgodnych z platformą Docker (Moby) jest zainstalowany na urządzeniu **4.** Wynik testu jest określany na podstawie pomyślnego wdrożenia symulowanego i funkcjonalnego modułu dokującego czujnika                                                    |
| **Zasoby**           | **a)** [AICS blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby), **c)** [wymagania](./program-requirements-azure-certified-device.md) |
| **Zalecane platformy Azure:**     | Nie dotyczy                                                    |

### <a name="capability-template"></a>Szablon możliwości:

**IoT Edge łatwą konfigurację: celem testu jest upewnienie się, że IoT Edge urządzenie jest łatwe do skonfigurowania i sprawdza, czy środowisko uruchomieniowe IoT Edge jest preinstalowane podczas weryfikacji urządzenia fizycznego**

| **Nazwa**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępne teraz (obecnie wstrzymane z powodu COVID-19)                                            |
| **Dotyczy**          | Urządzenie usługi IoT Edge                                                   |
| **System operacyjny**                  | [Pomoc i SVR system operacyjny](../iot-edge/support.md)                                                     |
| **Typ walidacji**     | Zweryfikowane ręcznie/laboratorium                                                    |
| **Walidacja**          | Producent OEM musi dostarczyć urządzenie fizyczne do administracji IoT (HCL). HCL dokonuje ręcznej walidacji na urządzeniu fizycznym w celu sprawdzenia: **1.** EdgeRT korzysta z podsystemu Moby (dozwolona wersja redystrybucyjna). Nie Docker **2.** Wybierz najnowszy moduł krawędzi, aby zweryfikować możliwość wdrożenia krawędzi.                                                     |
| **Zasoby**           | **a)** [AICS blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [wymagania dotyczące](./program-requirements-azure-certified-device.md) [certyfikacji](./overview.md) , **c)** |
| **Zalecane platformy Azure:**     | Nie dotyczy                                                    |