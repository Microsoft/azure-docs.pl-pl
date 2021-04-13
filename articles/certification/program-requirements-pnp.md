---
title: Wymagania dotyczące certyfikacji Plug and Play IoT
description: Wymagania programu certyfikacji Plug and Play IoT
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: b26fab6f8b92e3cb996f545f1f6201d32b1eaced
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310516"
---
# <a name="iot-plug-and-play-certification-requirements"></a>Wymagania dotyczące certyfikacji Plug and Play IoT

Ten dokument zawiera informacje o funkcjach specyficznych dla urządzeń, które będą reprezentowane w wykazie urządzeń usługi Azure IoT. Możliwość to pojedynczy atrybut urządzenia, który może być implementacją oprogramowania lub kombinacją implementacji oprogramowania i sprzętu.

## <a name="program-purpose"></a>Cel programu

Wersja zapoznawcza Plug and Play IoT umożliwia konstruktorom rozwiązań integrację urządzeń inteligentnych z ich rozwiązaniami bez konieczności ręcznej konfiguracji. Na początku Plug and Play IoT to model urządzenia, którego urządzenie używa do anonsowania swoich możliwości aplikacji z obsługą Plug and Play IoT. Ten model jest strukturalny jako zestaw elementów: dane telemetryczne, właściwości i polecenia.

Obietnica certyfikatów Plug and Play IoT:

1.  Zdefiniowane modele i interfejsy urządzeń są zgodne z  [językiem definicji cyfrowej przędzy](https://github.com/Azure/opendigitaltwins-dtdl)  
2.  Zabezpieczanie aprowizacji i łatwe przenoszenie własności zakresu identyfikatorów w usługach Device Provisioning
3.  Łatwa integracja z rozwiązaniami opartymi na usłudze Azure IoT za pomocą [interfejsów API cyfrowych przędzy](../iot-pnp/concepts-digital-twin.md)  : Azure IoT Hub i Azure IoT Central
4.  Zweryfikowane produkty prawdziwie na certyfikowanych urządzeniach

## <a name="requirements"></a>Wymagania

**Potrzeb Urządzenie do chmury: celem testu jest upewnienie się, że urządzenia wysyłające dane telemetryczne współdziałają z IoT Hub**

| **Nazwa**                | IoTPnP. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Urządzenie liścia/Urządzenie brzegowe                                      |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi wysyłać do IoT Hub wszystkie schematy telemetrii. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) do wykonywania testów. Urządzenie do chmury (wymagane): **1.** Sprawdza, czy urządzenie może wysyłać komunikat do AICS zarządzanego IoT Hub **2.** Użytkownik musi określić liczbę i częstotliwość komunikatów. **3.** AICS sprawdza, czy dane telemetryczne są odbierane przez wystąpienie centrum |
| **Zasoby**           | [Kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby) |

**Potrzeb DPS: przeznaczenie testu polega na sprawdzeniu, czy urządzenie implementuje i obsługuje IoT Hub Device Provisioning Service przy użyciu jednej z trzech metod zaświadczania**

| **Nazwa**                | IoTPnP. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Dowolne urządzenie                                                   |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi implementować łatwe przenoszenie własności zakresu punktu dystrybucji, bez konieczności ponownego kompilowania kodu osadzonego. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) służący do wykonywania testów w celu sprawdzenia, czy urządzenie obsługuje usługę DPS **1.** Użytkownik musi wybrać jedną z metod zaświadczania (X. 509, moduł TPM i klucz SAS) **2.** W zależności od metody zaświadczania użytkownik musi podejmować odpowiednie **działania, takie jak "Przekaż certyfikat** X. 509 do AICS Managed DPS Scope b" **.** Zaimplementuj klucz sygnatury dostępu współdzielonego i klucz poręczenia na urządzeniu |
| **Zasoby**           | **a)** [Omówienie usługi Device Provisioning](../iot-dps/about-iot-dps.md), **b)** [przykładowy plik KONFIGURACYJNy do przenoszenia zakresu identyfikatorów usługi DPS](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |

**Potrzeb DTDL v2: przeznaczenie testu, aby upewnić się, że zdefiniowane modele urządzeń i interfejsy są zgodne z językiem definicji Digital bliźniaczych reprezentacji w wersji 2.**                                                              

| **Nazwa**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Dowolne urządzenie                                                   |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | [Przepływ pracy portalu](https://certify.azure.com) sprawdza poprawność: **1.** Anonsowanie identyfikatora modelu i upewnienie się, że urządzenie jest połączone przy użyciu protokołu MQTT lub MQTT przez protokół WebSockets **2.** Modele są zgodne z DTDL v2 **3.** Dane telemetryczne, właściwości i polecenia są poprawnie implementowane i współdziałają między IoT Hub cyfr cyfrowych i sznurów urządzeń na urządzeniu |
| **Zasoby**           | [Aktualizacje dotyczące odświeżania z publicznej wersji zapoznawczej](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Potrzeb Modele urządzeń są publikowane w repozytorium modelu publicznego**

| **Nazwa**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Dowolne urządzenie                                                   |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Wszystkie modele urządzeń muszą być publikowane w repozytorium publicznym. Modele urządzeń są rozwiązywane za pośrednictwem modeli dostępnych w repozytorium publicznym **1.** Użytkownik musi ręcznie opublikować modele w repozytorium publicznym przed przesłaniem certyfikatu. **2.** Należy pamiętać, że po opublikowaniu modeli jest ono niezmienne. Zdecydowanie zalecamy opublikowanie tylko wtedy, gdy modele i kod urządzenia osadzonego są finalizowane. * 1 * 1 użytkownik musi skontaktować się z działem pomocy technicznej firmy Microsoft, aby odwołać modele po opublikowaniu w repozytorium modeli **3.** [Przepływ pracy portalu](https://certify.azure.com) sprawdza istnienie modeli w repozytorium publicznym, gdy urządzenie jest połączone z usługą certyfikacji |
| **Zasoby**           | [Repozytorium modeli](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Potrzeb Walidacja urządzenia fizycznego przy użyciu GSG**

| **Nazwa**                                  | IoTPnP.Physicaldevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność**                   | Dostępny teraz                                                |
| **Dotyczy**                            | Dowolne urządzenie                                                   |
| **System operacyjny**                                    | Bez znaczenia                                                     |
| **Typ walidacji**                       | Ręcznie                                                       |
| **Walidacja**                            | Partnerzy muszą mieć kontakt z firmą Microsoft ( [iotcert@microsoft.com](mailto:iotcert@microsoft.com) ), aby podjąć odpowiednie kroki w celu wykonania dodatkowych walidacji na urządzeniu fizycznym. Ze względu na sytuację w COVID-19, poznajemy różne sposoby przeprowadzania weryfikacji urządzenia fizycznego bez wysyłania urządzenia do firmy Microsoft. |
| **Zasoby**                             | Szczegóły są dostępne później                                 |
| **Zalecane platformy Azure**       | Nie dotyczy    |

**[Jeśli zaimplementowane] Interfejs informacji o urządzeniu: przeznaczenie testu polega na sprawdzeniu, czy interfejs informacji o urządzeniu został poprawnie zaimplementowany w kodzie urządzenia**

| **Nazwa**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Dowolne urządzenie                                                   |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | [Przepływ pracy portalu](https://certify.azure.com) sprawdza poprawność kodu urządzenia — implementuje interfejs informacji o urządzeniu **1.** Sprawdza, czy wartości są emitowane przez kod urządzenia do IoT Hub **2.** Sprawdza, czy interfejs jest zaimplementowany w ramach DCM (Ta implementacja zmieni się w DTDL v2) **3.** Sprawdzanie właściwości nie jest możliwe do zapisu (tylko do odczytu) **4.** Sprawdza, czy typ schematu to String i/lub Long, a nie null. |
| **Zasoby**           | [Interfejs zdefiniowany przez firmę Microsoft](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Zalecane platformy Azure**  | Nie dotyczy                                                          |

**[Jeśli zaimplementowane] Chmura do urządzenia: celem testu jest upewnienie się, że komunikaty mogą być wysyłane z chmury do urządzeń**

| **Nazwa**                | IoTPnP. C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Urządzenie liścia/Urządzenie brzegowe                                      |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi być w stanie w chmurze do komunikatów urządzeń z IoT Hub. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) , aby wykonać te testy. Chmura do urządzenia (jeśli jest zaimplementowana): **1.** Sprawdza, czy urządzenie może odebrać komunikat z IoT Hub **2.** AICS wysyła losowy komunikat i sprawdza poprawność przez potwierdzenie wiadomości z urządzenia |
| **Zasoby**           | **1.** [kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby), **2.** [Wysyłanie komunikatów z chmury do urządzeń z IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Jeśli zaimplementowane] Metody bezpośrednie: celem testu jest upewnienie się, że urządzenia współdziałają z IoT Hub i obsługują metody bezpośrednie**

| **Nazwa**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Urządzenie liścia/Urządzenie brzegowe                                      |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi być w stanie odbierać i odpowiadać na żądania poleceń z IoT Hub. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) do wykonywania testów. Metody bezpośrednie (jeśli zaimplementowane): **1.** Użytkownik musi określić ładunek metody metody bezpośredniej. **2.** AICS sprawdza poprawność określonego żądania ładunku wysyłanego z centrum i komunikatu ACK otrzymanego przez urządzenie |
| **Zasoby**           | **1.** [kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby), **2.** [Poznaj metody bezpośrednie z IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Jeśli zaimplementowane] Właściwość przędzy urządzenia: celem testu jest upewnienie się, że urządzenia wysyłające dane telemetryczne współdziałają z IoT Hub i obsługują niektóre funkcje IoT Hub, takie jak metody bezpośrednie, i Właściwość sznurka urządzenia**

| **Nazwa**                | IoTPnP.DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Docelowa dostępność** | Dostępny teraz                                                |
| **Dotyczy**          | Urządzenie liścia/Urządzenie brzegowe                                      |
| **System operacyjny**                  | Bez znaczenia                                                     |
| **Typ walidacji**     | Automatyczny                                                    |
| **Walidacja**          | Urządzenie musi wysyłać do IoT Hub wszystkie schematy telemetrii. Firma Microsoft udostępnia [przepływ pracy portalu](https://certify.azure.com) do wykonywania testów. Właściwość przędzy urządzenia (jeśli jest zaimplementowana): **1.** AICS sprawdza, czy właściwość "Odczyt/zapis" w przypadku urządzenia ze standardem JSON **2.** Użytkownik musi określić ładunek JSON, który ma zostać zmieniony **3.** AICS sprawdza poprawność określonych żądanych właściwości wysyłanych z wiadomości IoT Hub i ACK odebranych przez urządzenie |
| **Zasoby**           | **1.** [kroki certyfikacji](./overview.md) (zawiera wszystkie dodatkowe zasoby), **2.** [Korzystanie z bliźniaczych reprezentacji urządzenia z IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |
