---
title: Wymagania dotyczące urządzeń z certyfikatem platformy Azure
description: Wymagania programowe dotyczące urządzeń z certyfikatem platformy Azure
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 948fe25da8468e887693fe8c9f75f675dfbea858
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969405"
---
# <a name="azure-certified-device-requirements"></a>Wymagania dotyczące urządzeń z certyfikatem platformy Azure 
(wcześniej znane jako IoT Hub)

Ten dokument zawiera informacje o możliwościach urządzenia, które zostaną przedstawione w katalogu certyfikowanych urządzeń platformy Azure. Możliwość to pojedynczy atrybut urządzenia, który może być implementacją oprogramowania lub kombinacją implementacji oprogramowania i sprzętu. 

## <a name="program-purpose"></a>Cel programu

Firma Microsoft upraszcza certyfikat usługi IoT i certyfikowania urządzeń z certyfikatem platformy Azure, aby zapewnić, że wszystkie typy urządzeń są bezpiecznie obsługiwane na platformie Azure IoT Hub.

Obietnica certyfikacji urządzeń z certyfikatem platformy Azure:

1. Dane telemetryczne obsługi urządzeń, które współdziałają z IoT Hub
2.  IoT Hub Device Provisioning Service obsługi urządzeń (DPS) w celu bezpiecznego aprowizacji do usługi Azure IoT Hub
3.  Urządzenie obsługuje łatwą w obsłudze transfer zakresu identyfikatorów dla docelowego punktu dystrybucji bez konieczności ponownego kompilowania kodu osadzonego przez użytkownika.
4.  Opcjonalnie weryfikuje inne elementy, takie jak chmura do komunikatów urządzeń, metody bezpośrednie i sznurki urządzeń 

## <a name="requirements"></a>Wymagania

**Potrzeb Urządzenie do chmury: celem testu jest upewnienie się, że urządzenia wysyłające dane telemetryczne współdziałają z IoT Hub**

| **Nazwa**                | AzureCertified. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Urządzenie liścia/Urządzenie brzegowe                                      |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi wysyłać do IoT Hub wszystkie schematy telemetrii. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.come) do wykonywania testów. Urządzenie do chmury (wymagane): **1.** Sprawdza, czy urządzenie może wysyłać komunikat do AICS zarządzanego IoT Hub **2.** Użytkownik musi określić liczbę i częstotliwość komunikatów. **3.** AICS sprawdza, czy dane telemetryczne są odbierane przez wystąpienie centrum |
| **Zasoby**           | [Kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby) |

**Potrzeb DPS: przeznaczenie testu polega na sprawdzeniu, czy urządzenie implementuje i obsługuje IoT Hub Device Provisioning Service przy użyciu jednej z trzech metod zaświadczania**

| **Nazwa**                | AzureCertified. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Nowy                                                          |
| **Dotyczy**          | Dowolne urządzenie                                                   |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie obsługuje łatwe wprowadzanie wartości docelowej własności zakresu identyfikatora platformy DPS bez konieczności ponownego kompilowania kodu osadzonego. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) służący do wykonywania testów w celu sprawdzenia, czy urządzenie obsługuje usługę DPS **1.** Użytkownik musi wybrać jedną z metod zaświadczania (X. 509, moduł TPM i klucz SAS) **2.** W zależności od metody zaświadczania użytkownik musi podejmować odpowiednie **działania, takie jak "Przekaż certyfikat** X. 509 do AICS Managed DPS Scope b" **.** Zaimplementuj klucz sygnatury dostępu współdzielonego i klucz poręczenia na urządzeniu |
| **Zasoby**           | [Omówienie usługi Device Provisioning](../iot-dps/about-iot-dps.md) |

**[Jeśli zaimplementowane] Chmura do urządzenia: celem testu jest upewnienie się, że komunikaty mogą być wysyłane z chmury do urządzeń**                                                              

| **Nazwa**                | AzureCertified. C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                            |
| **Dotyczy**          | Urządzenie liścia/Urządzenie brzegowe                                                   |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi być w stanie w chmurze do komunikatów urządzeń z IoT Hub. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) , aby wykonać te testy. Chmura do urządzenia (jeśli jest zaimplementowana): **1.** Sprawdza, czy urządzenie może odebrać komunikat z IoT Hub **2.** AICS wysyła losowy komunikat i sprawdza poprawność przez potwierdzenie wiadomości z urządzenia  |
| **Zasoby**           | **a)** [kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby) **b)** [wysyłanie komunikatów z chmury do urządzenia z IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Jeśli zaimplementowane] Metody bezpośrednie: celem testu jest upewnienie się, że urządzenia współdziałają z IoT Hub i obsługują metody bezpośrednie**

| **Nazwa**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                            |
| **Dotyczy**          | Urządzenie liścia/Urządzenie brzegowe                                                   |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi być w stanie odbierać i odpowiadać na żądania poleceń z IoT Hub. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) do wykonywania testów. Metody bezpośrednie (jeśli zostały zaimplementowane) **1.** Użytkownik musi określić ładunek metody metody bezpośredniej. **2.** AICS sprawdza poprawność określonego żądania ładunku wysyłanego z centrum i komunikatu ACK otrzymanego przez urządzenie |
| **Zasoby**           | **a)** [kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby) **b)** [Poznaj metody bezpośrednie z IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Jeśli zaimplementowane] Właściwość przędzy urządzenia: celem testu jest upewnienie się, że urządzenia wysyłające dane telemetryczne współdziałają z IoT Hub i obsługują niektóre funkcje IoT Hub, takie jak metody bezpośrednie, i Właściwość sznurka urządzenia**

| **Nazwa**                                  | AzureCertified.DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność**                   | Dostępny teraz                                            |
| **Dotyczy**                            | Urządzenie liścia/Urządzenie brzegowe                                                   |
| **System operacyjny**                                    | Bez znaczenia                                                     |
| **Typ walidacji**                       | Automatyczny                                                       |
| **Walidacja**                            | Urządzenie musi wysyłać do IoT Hub wszystkie schematy telemetrii. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) do wykonywania testów. Właściwość przędzy urządzenia (jeśli jest zaimplementowana) **1.** AICS sprawdza, czy właściwość "Odczyt/zapis" w przypadku urządzenia ze standardem JSON **2.** Użytkownik musi określić ładunek JSON, który ma zostać zmieniony **3.** AICS sprawdza poprawność określonych żądanych właściwości wysyłanych z wiadomości IoT Hub i ACK odebranych przez urządzenie |
| **Zasoby**                             | **a)** [kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby) **b)** [użycie bliźniaczych reprezentacji urządzenia z IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |