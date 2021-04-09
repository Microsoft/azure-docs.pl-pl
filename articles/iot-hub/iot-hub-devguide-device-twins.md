---
title: Informacje o usłudze Azure IoT Hub Device bliźniaczych reprezentacji | Microsoft Docs
description: Przewodnik dla deweloperów — używanie bliźniaczych reprezentacji urządzeń do synchronizowania danych stanu i konfiguracji między IoT Hub i urządzeniami
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: fff4b9157c30203f47c65a74b211e3dbf6426d92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093025"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Zrozumienie i używanie bliźniaczych reprezentacji urządzeń w IoT Hub

*Bliźniaczych reprezentacji urządzeń* to dokumenty JSON, które przechowują informacje o stanie urządzenia, w tym metadane, konfiguracje i warunki. Usługa Azure IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia połączonego z usługą IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura sznurka urządzenia: *Tagi*, *odpowiednie* i *raportowane właściwości*.
* Operacje, które aplikacje i zaplecza urządzeń mogą wykonywać na urządzeniu bliźniaczych reprezentacji.

Użyj bliźniaczych reprezentacji urządzenia, aby:

* Przechowuj metadane specyficzne dla urządzenia w chmurze. Na przykład lokalizacja wdrożenia maszyny sprzedaży.

* Zgłoś bieżące informacje o stanie, takie jak dostępne możliwości i warunki z aplikacji urządzenia. Na przykład urządzenie jest połączone z usługą IoT Hub przez sieć komórkową lub Wi-Fi.

* Zsynchronizuj stan długotrwałych przepływów pracy między aplikacją urządzenia a aplikacją zaplecza. Na przykład jeśli zaplecze rozwiązania określa nową wersję oprogramowania układowego do zainstalowania, a aplikacja urządzenia zgłosi różne etapy procesu aktualizacji.

* Wykonaj zapytanie dotyczące metadanych, konfiguracji lub stanu urządzenia.

Zapoznaj się ze [wskazówkami dotyczącymi komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) , aby uzyskać wskazówki dotyczące korzystania z raportowanych właściwości, komunikatów przesyłanych z urządzeń do chmury lub przekazywania plików.

Zapoznaj się ze [wskazówkami dotyczącymi komunikacji między chmurą i urządzeniami](iot-hub-devguide-c2d-guidance.md) , aby uzyskać wskazówki dotyczące używania żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzenia.

Aby dowiedzieć się, jak bliźniaczych reprezentacji urządzenia odnoszą się do modelu urządzenia używanego przez urządzenie usługi Azure IoT Plug and Play, zobacz [Opis usługi IoT Plug and Play Digital bliźniaczych reprezentacji](../iot-pnp/concepts-digital-twin.md).

## <a name="device-twins"></a>Bliźniaczych reprezentacji urządzenia

Urządzenie bliźniaczych reprezentacji przechowuje informacje dotyczące urządzenia, które:

* Urządzenia i zaplecza mogą służyć do synchronizowania warunków i konfiguracji urządzenia.

* Zaplecze rozwiązania może użyć do wykonywania zapytań i docelowych operacji długotrwałych.

Cykl życia urządzenia ze znakiem jest połączony z odpowiadającą [tożsamością urządzenia](iot-hub-devguide-identity-registry.md). Bliźniaczych reprezentacji urządzeń są niejawnie tworzone i usuwane, gdy tożsamość urządzenia jest tworzona lub usuwana w IoT Hub.

Sznurki urządzenia to dokument JSON, który zawiera:

* **Tagi**. Sekcja dokumentu JSON, z której zaplecze rozwiązania może odczytywać i zapisywać dane. Tagi nie są widoczne dla aplikacji urządzenia.

* **Żądane właściwości**. Używane wraz z raportowanymi właściwościami do synchronizacji konfiguracji lub warunków urządzenia. Zaplecze rozwiązania może ustawić żądane właściwości, a aplikacja urządzenia może je odczytać. Aplikacja urządzenia może również odbierać powiadomienia o zmianach w odpowiednich właściwościach.

* **Raportowane właściwości**. Używane wraz z pożądanymi właściwościami do synchronizowania konfiguracji lub warunków urządzenia. Aplikacja urządzenia może ustawiać raportowane właściwości, a zaplecze rozwiązania może odczytywać i wysyłać do nich zapytania.

* **Właściwości tożsamości urządzenia**. Katalog główny dokumentu JSON dotyczącego urządzeń z dostępem do urządzenia zawiera właściwości tylko do odczytu z odpowiedniej tożsamości urządzenia przechowywanej w [rejestrze tożsamości](iot-hub-devguide-identity-registry.md). Właściwości `connectionStateUpdatedTime` i `generationId` nie zostaną uwzględnione.

![Zrzut ekranu przedstawiający właściwości sznurka urządzenia](./media/iot-hub-devguide-device-twins/twin.png)

W poniższym przykładzie przedstawiono dokument JSON:

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

W obiekcie głównym są właściwości tożsamości urządzenia i obiekty kontenera dla `tags` i obie `reported` i `desired` właściwości. `properties`Kontener zawiera elementy tylko do odczytu ( `$metadata` , `$etag` i `$version` ) opisane w [metadanych przędzy za urządzeniem](iot-hub-devguide-device-twins.md#device-twin-metadata) i [optymistyczne współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) .

### <a name="reported-property-example"></a>Przykład raportowanej właściwości

W poprzednim przykładzie sznurek urządzenia zawiera `batteryLevel` Właściwość, która jest raportowana przez aplikację urządzenia. Ta właściwość umożliwia wykonywanie zapytań i obsługi na urządzeniach na podstawie ostatniego zgłoszonego poziomu baterii. Inne przykłady obejmują możliwości urządzeń z raportowaniem aplikacji urządzeń lub opcje łączności.

> [!NOTE]
> Raportowane właściwości upraszczają scenariusze, w których zaplecze rozwiązania jest zainteresowane ostatnią znaną wartością właściwości. Użyj [komunikatów z urządzenia do chmury](iot-hub-devguide-messages-d2c.md) , jeśli zaplecze rozwiązania musi przetworzyć dane telemetryczne urządzenia w postaci sekwencji zdarzeń z sygnaturami czasowymi, takich jak szeregi czasowe.

### <a name="desired-property-example"></a>Przykład żądanej właściwości

W poprzednim przykładzie `telemetryConfig` pożądane i zgłoszone właściwości są używane przez zaplecze rozwiązania oraz aplikację urządzenia do synchronizacji konfiguracji telemetrii dla tego urządzenia. Na przykład:

1. Zaplecze rozwiązania ustawia odpowiednią właściwość o żądaną wartość konfiguracji. Poniżej znajduje się część dokumentu z żądanym zestawem właściwości:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Aplikacja urządzenia zostanie powiadomiona o zmianie natychmiast po powiązaniu połączenia lub przy pierwszym ponownym połączeniu. Następnie aplikacja urządzenia zgłosi zaktualizowaną konfigurację (lub warunek błędu za pomocą `status` Właściwości). Oto część raportowanych właściwości:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Zaplecze rozwiązania może śledzić wyniki operacji konfiguracji na wielu urządzeniach, [badając](iot-hub-devguide-query-language.md) urządzenie bliźniaczych reprezentacji.

> [!NOTE]
> Poprzednie fragmenty kodu to przykłady, zoptymalizowane pod kątem czytelności, w jednym ze sposobów kodowania konfiguracji urządzeń i jej stanu. IoT Hub nie nakłada określonego schematu dla potrzebnych sznurów urządzeń i zgłosiło właściwości w urządzeniu bliźniaczych reprezentacji.
> 

Za pomocą bliźniaczych reprezentacji można synchronizować długotrwałe operacje, takie jak aktualizacje oprogramowania układowego. Aby uzyskać więcej informacji na temat sposobu użycia właściwości do synchronizowania i śledzenia długotrwałej operacji na różnych urządzeniach, zobacz [Używanie żądanych właściwości do konfigurowania urządzeń](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operacje zaplecza

Zaplecze rozwiązania działa na bliźniaczych urządzeniach przy użyciu następujących operacji niepodzielnych udostępnianych za pośrednictwem protokołu HTTPS:

* **Pobierz sznurki urządzenia według identyfikatora**. Ta operacja zwraca dokument z przędzą na urządzeniu, w tym Tagi i odpowiednie i zgłoszone właściwości systemu.

* **Częściowo Aktualizuj sznurek urządzeń**. Ta operacja umożliwia zapleczu rozwiązania częściowo aktualizowanie tagów lub żądanych właściwości w postaci sznurka urządzenia. Aktualizacja częściowa jest wyrażona jako dokument JSON, który dodaje lub aktualizuje każdą właściwość. Właściwości ustawione na `null` są usuwane. Poniższy przykład tworzy nową pożądaną właściwość o wartości `{"newProperty": "newValue"}` , zastępuje istniejącą wartość `existingProperty` z `"otherNewValue"` i usuwa `otherOldProperty` . Nie wprowadzono żadnych innych zmian do istniejących żądanych właściwości lub tagów:

   ```json
   {
       "properties": {
           "desired": {
               "newProperty": {
                   "nestedProperty": "newValue"
               },
               "existingProperty": "otherNewValue",
               "otherOldProperty": null
           }
       }
   }
   ```

* **Zastąp żądane właściwości**. Ta operacja umożliwia zapleczu rozwiązania całkowicie zastępowanie wszystkich istniejących żądanych właściwości i zastąpienie nowego dokumentu JSON `properties/desired` .

* **Zamień Tagi**. Ta operacja umożliwia zapleczu rozwiązania całkowicie zastępowanie wszystkich istniejących tagów i zastąpienie nowego dokumentu JSON `tags` .

* **Otrzymywanie powiadomień bliźniaczych**. Ta operacja umożliwia zaplecze rozwiązania powiadamianie o modyfikacji dwuosiowej. W tym celu Twoje rozwiązanie IoT musi utworzyć trasę i ustawić źródło danych równe *twinChangeEvents*. Domyślnie żadna taka trasa nie istnieje, więc nie są wysyłane żadne powiadomienia o przędze. Jeśli współczynnik zmiany jest zbyt wysoki lub z innych przyczyn, takich jak błędy wewnętrzne, IoT Hub może wysłać tylko jedno powiadomienie zawierające wszystkie zmiany. W związku z tym, jeśli aplikacja wymaga niezawodnej inspekcji i rejestrowania wszystkich stanów pośrednich, należy użyć komunikatów z urządzenia do chmury. Wiadomość z powiadomieniem o przędzy obejmuje właściwości i treść.

  - Właściwości

    | Nazwa | Wartość |
    | --- | --- |
    Typ $content | application/json |
    $iothub — enqueuedtime |  Godzina wysłania powiadomienia |
    $iothub-komunikat-Źródło | twinChangeEvents |
    $content — kodowanie | UTF-8 |
    deviceId | Identyfikator urządzenia |
    hubName | Nazwa IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) sygnatura czasowa operacji |
    iothub — komunikat-schemat | twinChangeNotification |
    opType | "replaceTwin" lub "updateTwin" |

    Właściwości systemu wiadomości są poprzedzone `$` symbolem.

  - Treść
        
    Ta sekcja zawiera wszystkie zmiany w formacie JSON. Używa tego samego formatu co poprawka, z różnicą, że może zawierać wszystkie sekcje sznurów: Tagi, właściwości. raportowane, właściwości. wymagane i że zawiera elementy "$metadata". Na przykład

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Wszystkie poprzednie operacje obsługują [optymistyczną współbieżność](iot-hub-devguide-device-twins.md#optimistic-concurrency) i wymagają uprawnienia **serviceconnect** , zgodnie z definicją w polu [Kontroluj dostęp do IoT Hub](iot-hub-devguide-security.md).

Oprócz tych operacji zaplecze rozwiązania może:

* Zbadaj bliźniaczych reprezentacji urządzenia, używając [języka zapytań IoT Hub](iot-hub-devguide-query-language.md), takich jak SQL.

* Wykonywanie operacji na dużych zestawach urządzeń bliźniaczych reprezentacji za pomocą [zadań](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operacje na urządzeniach

Aplikacja urządzenia działa na sznurze urządzenia przy użyciu następujących operacji niepodzielnych:

* **Pobierz sznurki urządzeń**. Ta operacja zwraca dokument z sznurka urządzenia (w tym odpowiednie i zgłoszone właściwości systemu) dla aktualnie podłączonego urządzenia. (Tagi nie są widoczne dla aplikacji urządzenia).

* **Częściowo Zaktualizowano raportowane właściwości**. Ta operacja umożliwia częściowe aktualizowanie raportowanych właściwości aktualnie podłączonego urządzenia. Ta operacja używa tego samego formatu aktualizacji JSON, którego zaplecze rozwiązanie używa do częściowej aktualizacji żądanych właściwości.

* **Obserwuj żądane właściwości**. Aktualnie połączone urządzenie może otrzymywać powiadomienia o aktualizacjach żądanych właściwości, gdy wystąpią. Urządzenie otrzymuje tę samą formę aktualizacji (częściowej lub pełnej wymiany) wykonywanej przez zaplecze rozwiązania.

Wszystkie poprzednie operacje wymagają uprawnienia **DeviceConnect** , zgodnie z definicją w polu [kontroluj dostęp do IoT Hub](iot-hub-devguide-security.md).

[Zestawy SDK urządzeń Azure IoT](iot-hub-devguide-sdks.md) ułatwiają używanie powyższych operacji z wielu języków i platform. Aby uzyskać więcej informacji na temat szczegółów IoT Hub podstawowych dla synchronizacji żądanych właściwości, zobacz [przepływ ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formatowanie tagów i właściwości

Tagi, żądane właściwości i raportowane właściwości są obiektami JSON z następującymi ograniczeniami:

* **Klucze**: wszystkie klucze w obiektach JSON są zakodowane w formacie UTF-8, z uwzględnieniem wielkości liter i do 1 KB. Dozwolone znaki wykluczają znaki kontrolne UNICODE (segmenty C0 i C1), oraz `.` , `$` i Sp.

* **Wartości**: wszystkie wartości w obiektach JSON mogą mieć następujące typy JSON: Boolean, Number, String, Object. Tablice są również obsługiwane.

    * Liczby całkowite mogą mieć minimalną wartość-4503599627370496 i maksymalną wartość 4503599627370495.

    * Wartości ciągów są kodowane w formacie UTF-8 i mogą mieć maksymalną długość 4 KB.

* **Głębokość**: Maksymalna głębokość obiektów JSON w tagach, wymaganych właściwościach i raportowanych właściwościach wynosi 10. Na przykład następujący obiekt jest prawidłowy:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Rozmiar przędzy urządzenia

IoT Hub wymusza limit rozmiaru 8 KB dla wartości `tags` , a rozmiar 32 KB jest ograniczony do wartości `properties/desired` i `properties/reported` . Te sumy są wyłącznie poza elementami tylko do odczytu, takimi jak `$etag` , `$version` i `$metadata/$lastUpdated` .

Rozmiar bliźniaczy jest obliczany w następujący sposób:

* Dla każdej właściwości w dokumencie JSON IoT Hub zbiorcze obliczenia i dodaje długość klucza i wartości właściwości.

* Klucze właściwości są uznawane za ciągi kodowane w formacie UTF8.

* Proste wartości właściwości są uznawane za ciągi kodowane w formacie UTF8, wartości liczbowe (8 bajtów) lub wartości logicznych (4 bajty).

* Rozmiar ciągów zakodowanych w formacie UTF8 jest obliczany przez liczenie wszystkich znaków, z wyłączeniem znaków kontrolnych UNICODE (segmenty C0 i C1).

* Złożone wartości właściwości (obiekty zagnieżdżone) są obliczane na podstawie zagregowanego rozmiaru kluczy właściwości i wartości właściwości, które zawierają.

IoT Hub odrzuca z powodu błędu wszystkie operacje, które spowodują zwiększenie rozmiaru `tags` , `properties/desired` lub `properties/reported` dokumentów przekraczających limit.

## <a name="device-twin-metadata"></a>Metadane dotyczące sznurka urządzenia

IoT Hub utrzymuje sygnaturę czasową ostatniej aktualizacji dla każdego obiektu JSON w pożądanej sznurze urządzenia i raportowane właściwości. Sygnatury czasowe są zakodowane w formacie UTC i kodowane w [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) `YYYY-MM-DDTHH:MM:SS.mmmZ` .

Na przykład:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Te informacje są przechowywane na każdym poziomie (nie tylko przed opuszczeniem struktury JSON), aby zachować aktualizacje, które usuwają klucze obiektów.

## <a name="optimistic-concurrency"></a>Współbieżność optymistyczna

Tagi, odpowiednie i raportowane właściwości All obsługują optymistyczną współbieżność.
Tagi mają element ETag, jak na [RFC7232](https://tools.ietf.org/html/rfc7232), który reprezentuje reprezentację JSON znacznika. Aby zapewnić spójność, można użyć elementów ETag w operacjach aktualizacji warunkowej z zaplecza rozwiązania.

Wymagane sznurki urządzenia i zgłoszone właściwości nie mają elementów ETag, ale mają `$version` gwarantowaną wartość, która ma być przyrostowa. Podobnie jak w przypadku elementu ETag, wersja może być używana przez stronę aktualizacji w celu wymuszenia spójności aktualizacji. Na przykład aplikacja urządzenia dla raportowanej właściwości lub zaplecza rozwiązania dla żądanej właściwości.

Wersje są również przydatne, gdy Agent obserwowania (na przykład aplikacja urządzenia obserwowanie żądanych właściwości) musi uzgodnić Races między wynikiem operacji pobierania a powiadomieniem o aktualizacji. [Sekcja przepływ ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow) zawiera więcej informacji.

## <a name="device-reconnection-flow"></a>Przepływ ponownego połączenia urządzenia

IoT Hub nie zachowuje żądanych właściwości powiadomienia o aktualizacjach dla odłączonych urządzeń. Po nawiązaniu połączenia w celu powiadomienia o aktualizacjach należy pobrać pełny dokument z żądanymi właściwościami. Ze względu na możliwość Races między powiadomieniami o aktualizacji i pełnym pobieraniem należy zapewnić następujący przepływ:

1. Aplikacja urządzenia nawiązuje połączenie z usługą IoT Hub.
2. Aplikacja urządzenia subskrybuje powiadomienia o aktualizacjach żądanych właściwości.
3. Aplikacja urządzenia pobiera pełen dokument dla żądanych właściwości.

Aplikacja urządzenia może ignorować wszystkie powiadomienia z `$version` mniejszą lub równą wersji pełnego pobranego dokumentu. Takie podejście jest możliwe, ponieważ IoT Hub gwarantuje, że wersje są zawsze zwiększane.

> [!NOTE]
> Ta logika jest już zaimplementowana w zestawach [SDK urządzeń usługi Azure IoT](iot-hub-devguide-sdks.md). Ten opis jest przydatny tylko wtedy, gdy aplikacja urządzenia nie może używać żadnego z zestawów SDK urządzeń usługi Azure IoT i musi bezpośrednio programować interfejs MQTT.
> 

## <a name="additional-reference-material"></a>Dodatkowe materiały referencyjne

Inne tematy referencyjne w przewodniku dewelopera IoT Hub obejmują:

* W artykule [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md) opisano różne punkty końcowe, które są uwidaczniane przez poszczególne usługi IoT Hub na potrzeby operacji w czasie wykonywania i zarządzania.

* W artykule dotyczącej [ograniczania i przydziałów](iot-hub-devguide-quotas-throttling.md) opisano przydziały, które mają zastosowanie do usługi IoT Hub oraz zachowanie ograniczania, które należy oczekiwać podczas korzystania z usługi.

* W artykule na temat [zestawów SDK urządzeń i usług Azure IoT](iot-hub-devguide-sdks.md) przedstawiono różne zestawy SDK języka, których można używać podczas tworzenia aplikacji dla urządzeń i usług, które współpracują z usługą IoT Hub.

* [Język zapytań IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md) zawiera opis języka zapytań IoT Hub, za pomocą którego można pobrać informacje z IoT Hub o urządzeniu bliźniaczych reprezentacji i zadaniach.

* Artykuł [pomocy technicznej IoT Hub MQTT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi IoT Hub protokołu MQTT.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak bliźniaczych reprezentacji urządzenia, możesz zainteresować następujące tematy dotyczące przewodnika dla deweloperów IoT Hub:

* [Zrozumienie i Używanie modułu bliźniaczych reprezentacji w IoT Hub](iot-hub-devguide-module-twins.md)
* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)
* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować niektóre koncepcje opisane w tym artykule, zobacz następujące samouczki IoT Hub:

* [Jak korzystać z sznurka urządzenia](iot-hub-node-node-twin-getstarted.md)
* [Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)
* [Zarządzanie urządzeniami za pomocą narzędzi usługi Azure IoT dla programu VS Code](iot-hub-device-management-iot-toolkit.md)
