---
title: Opis bliźniaczych urządzeń usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — używanie bliźniąt bliźniąt urządzeń do synchronizowania danych o stanie i konfiguracji między usługą IoT Hub a urządzeniami
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.openlocfilehash: 251c9c9717bae1728bffa48827a45d4535d66c15
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482072"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Opis bliźniaczych urządzeń i używanie ich w Centrum IoT

*Bliźniacze reprezentacje urządzeń* to dokumenty JSON, które przechowują informacje o stanie urządzenia, w tym metadane, konfiguracje i warunki. Usługa Azure IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia połączonego z usługą IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym artykule opisano:

* Struktura bliźniaczej części urządzenia: *znaczniki*, *pożądane* i *zgłoszone właściwości*.
* Operacje, które aplikacje urządzenia i zaplecze można wykonać na bliźniaczych urządzeń.

Użyj bliźniąt urządzeń, aby:

* Przechowuj metadane specyficzne dla urządzenia w chmurze. Na przykład lokalizacja wdrożenia automatu.

* Raportuj bieżące informacje o stanie, takie jak dostępne możliwości i warunki z aplikacji urządzenia. Na przykład urządzenie jest połączone z centrum IoT przez sieć komórkową lub Wi-Fi.

* Synchronizuj stan długotrwałych przepływów pracy między aplikacją urządzenia a aplikacją zaplecza. Na przykład, gdy zaplecze rozwiązania określa nową wersję oprogramowania układowego do zainstalowania, a aplikacja urządzenia raportuje różne etapy procesu aktualizacji.

* Kwerenda metadanych urządzenia, konfiguracji lub stanu.

Aby uzyskać wskazówki dotyczące korzystania ze zgłoszonych właściwości, komunikatów z urządzenia do chmury lub przekazywania plików, zapoznaj się [ze wskazówkami dotyczącymi komunikacji](iot-hub-devguide-d2c-guidance.md) między urządzeniami a chmurą.

Aby uzyskać wskazówki dotyczące korzystania z żądanych właściwości, metod bezpośrednich lub komunikatów z chmury do urządzenia, zapoznaj się [ze wskazówkami dotyczącymi komunikacji](iot-hub-devguide-c2d-guidance.md) z chmury do urządzenia.

## <a name="device-twins"></a>Bliźnięta urządzeń

Bliźniacze urządzenia przechowują informacje związane z urządzeniem, które:

* Urządzenia i zaplecze można używać do synchronizowania warunków i konfiguracji urządzenia.

* Zaplecza rozwiązania można użyć do kwerendy i docelowe długotrwałe operacje.

Cykl życia bliźniaczej reprezentacji urządzenia jest połączony z odpowiednią [tożsamością urządzenia.](iot-hub-devguide-identity-registry.md) Bliźniacze reprezentacje urządzeń są niejawnie tworzone i usuwane podczas tworzenia lub usuwania tożsamości urządzenia w centrum IoT Hub.

Bliźniacza reprezentacja urządzenia to dokument JSON, który zawiera:

* **Tagi**. Sekcja dokumentu JSON, który rozwiązanie zaplecza można odczytać i zapisać do. Tagi nie są widoczne dla aplikacji urządzenia.

* **Żądane właściwości**. Używane wraz ze zgłoszonymi właściwościami do synchronizowania konfiguracji lub warunków urządzenia. Zaplecze rozwiązania można ustawić żądane właściwości, a aplikacja urządzenia można je odczytać. Aplikacja urządzenia może również otrzymywać powiadomienia o zmianach w żądanych właściwościach.

* **Zgłoszone właściwości**. Używane wraz z żądanymi właściwościami do synchronizowania konfiguracji urządzenia lub warunków. Aplikacja urządzenia można ustawić zgłaszane właściwości, a zaplecze rozwiązania można je odczytać i zbadać je.

* **Właściwości tożsamości urządzenia**. Katalog główny dokumentu JSON bliźniaczej reprezentacji urządzenia zawiera właściwości tylko do odczytu z odpowiedniej tożsamości urządzenia przechowywane w [rejestrze tożsamości](iot-hub-devguide-identity-registry.md). Właściwości `connectionStateUpdatedTime` i `generationId` nie będą uwzględniane.

![Zrzut ekranu przedstawiający właściwości bliźniaczej reprezentacji urządzenia](./media/iot-hub-devguide-device-twins/twin.png)

W poniższym przykładzie przedstawiono bliźniaczej reprezentacji urządzenia dokumentu JSON:

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

W obiekcie głównym są właściwości tożsamości urządzenia `tags` i `reported` obiekty `desired` kontenera dla i obu i właściwości. `properties` Kontener zawiera niektóre elementy tylko`$metadata` `$etag`do `$version`odczytu ( , i ) opisane w [device twin metadanych](iot-hub-devguide-device-twins.md#device-twin-metadata) i [optymistyczne współbieżności](iot-hub-devguide-device-twins.md#optimistic-concurrency) sekcje.

### <a name="reported-property-example"></a>Przykład zgłoszonej właściwości

W poprzednim przykładzie bliźniaczej `batteryLevel` reprezentacji urządzenia zawiera właściwość, która jest zgłaszana przez aplikację urządzenia. Ta właściwość umożliwia wykonywanie zapytań i działanie na urządzeniach na podstawie ostatnio zgłoszonego poziomu naładowania baterii. Inne przykłady obejmują funkcje urządzenia raportowania aplikacji urządzenia lub opcje łączności.

> [!NOTE]
> Zgłoszone właściwości upraszczają scenariusze, w których zaplecze rozwiązania jest zainteresowany ostatnią znaną wartością właściwości. Użyj [komunikatów z urządzenia do chmury,](iot-hub-devguide-messages-d2c.md) jeśli zaplecze rozwiązania musi przetwarzać dane telemetryczne urządzenia w postaci sekwencji zdarzeń sygnatury czasowej, takich jak szeregi czasowe.

### <a name="desired-property-example"></a>Przykład żądanej właściwości

W poprzednim przykładzie `telemetryConfig` bliźniaczej reprezentacji urządzenia żądane i zgłaszane właściwości są używane przez zaplecze rozwiązania i aplikacji urządzenia do synchronizowania konfiguracji telemetrii dla tego urządzenia. Przykład:

1. Zaplecze rozwiązania ustawia żądaną właściwość z żądaną wartością konfiguracji. Oto część dokumentu z żądanym zestawem właściwości:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Aplikacja urządzenia jest powiadamiana o zmianie natychmiast, jeśli jest podłączona lub przy pierwszym ponownym połączeniu. Aplikacja urządzenia następnie zgłasza zaktualizowaną konfigurację (lub warunek błędu przy użyciu `status` właściwości). Oto część zgłoszonych właściwości:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Zaplecze rozwiązania można śledzić wyniki operacji konfiguracji na wielu urządzeniach przez [wykonywanie zapytań](iot-hub-devguide-query-language.md) bliźniaczych urządzeń.

> [!NOTE]
> Powyższe fragmenty kodu są przykładami, zoptymalizowane pod kątem czytelności, jeden sposób kodowania konfiguracji urządzenia i jego stanu. Centrum IoT hub nie nakłada określonego schematu dla bliźniaczej reprezentacji urządzenia żądane i zgłaszane właściwości w bliźniaczych reprezentacji urządzenia.
> 

Bliźniacze dane współużycowe umożliwiają synchronizację długotrwałych operacji, takich jak aktualizacje oprogramowania układowego. Aby uzyskać więcej informacji na temat używania właściwości do synchronizowania i śledzenia długotrwałej pracy na różnych urządzeniach, zobacz [Konfigurowanie urządzeń za pomocą żądanych właściwości](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operacje zaplecza

Zaplecze rozwiązania działa na bliźniaczej reprezentacji urządzenia przy użyciu następujących operacji niepodzielnych, udostępniane za pośrednictwem protokołu HTTPS:

* **Pobieranie bliźniaczej reprezentacji urządzenia przez identyfikator**. Ta operacja zwraca dokument bliźniaczej reprezentacji urządzenia, w tym znaczniki i żądane i zgłoszone właściwości systemu.

* **Częściowo zaktualizować bliźniaczej reprezentacji urządzenia**. Ta operacja umożliwia zapleczu rozwiązania do częściowej aktualizacji tagów lub żądanych właściwości w bliźniaczej reprezentacji urządzenia. Częściowa aktualizacja jest wyrażona jako dokument JSON, który dodaje lub aktualizuje dowolną właściwość. Właściwości ustawione `null` na są usuwane. Poniższy przykład tworzy nową żądaną właściwość z `{"newProperty": "newValue"}`wartością, zastępuje istniejącą wartość `existingProperty` z `"otherNewValue"`, i usuwa . `otherOldProperty` Żadne inne zmiany nie są wprowadzane do istniejących żądanych właściwości lub tagów:

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

* **Zamień żądane właściwości**. Ta operacja umożliwia rozwiązaniu zaplecze całkowicie zastąpić wszystkie istniejące żądane właściwości `properties/desired`i zastąpić nowy dokument JSON dla .

* **Zamień znaczniki**. Ta operacja umożliwia rozwiązaniu zaplecze całkowicie zastąpić wszystkie istniejące znaczniki i `tags`zastąpić nowy dokument JSON.

* **Otrzymuj podwójne powiadomienia**. Ta operacja umożliwia rozwiązanie zaplecza, aby otrzymywać powiadomienia, gdy bliźniaczej reprezentacji jest modyfikowany. Aby to zrobić, rozwiązanie IoT musi utworzyć trasę i ustawić źródło danych równe *twinChangeEvents*. Domyślnie nie takie trasy wstępnie istnieje, więc nie podwójne powiadomienia są wysyłane. Jeśli szybkość zmiany jest zbyt wysoka lub z innych powodów, takich jak błędy wewnętrzne, Centrum IoT może wysłać tylko jedno powiadomienie, które zawiera wszystkie zmiany. W związku z tym jeśli aplikacja wymaga niezawodnej inspekcji i rejestrowania wszystkich stanów pośrednich, należy użyć komunikatów z urządzenia do chmury. Komunikat o powiadomieniu bliźniaczej zawiera właściwości i treść.

  - Właściwości

    | Nazwa | Wartość |
    | --- | --- |
    Typ $content | application/json |
    $iothub-enqueuedtime |  Czas wysłania powiadomienia |
    źródło $iothub-message | twinChangeZdowyzowie |
    Kodowanie $content | utf-8 |
    deviceId | Identyfikator urządzenia |
    nazwa centrum | Nazwa Centrum IoT |
    operationStamp | Sygnatura czasowa [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) |
    schemat iothub-message | twinChangeNotification |
    Optype | "replaceTwin" lub "updateTwin" |

    Właściwości systemu wiadomości są poprzedzone `$` symbolem.

  - Treść
        
    Ta sekcja zawiera wszystkie zmiany bliźniaczej reprezentacji w formacie JSON. Używa tego samego formatu co łatka, z tą różnicą, że może zawierać wszystkie sekcje bliźniacze: tagi, properties.reported, properties.desired i że zawiera elementy "$metadata". Na przykład:

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

Wszystkie poprzednie operacje obsługują [optymistyczną współbieżność](iot-hub-devguide-device-twins.md#optimistic-concurrency) i wymagają uprawnienia **ServiceConnect,** zgodnie z definicją w [sekcji Kontrola dostępu do Usługi IoT Hub](iot-hub-devguide-security.md).

Oprócz tych operacji, zaplecza rozwiązania można:

* Kwerenda bliźniaczy urządzenia przy użyciu [języka zapytań IoT Hub](iot-hub-devguide-query-language.md)podobnej do języka SQL .

* Wykonywanie operacji na dużych zestawach bliźniąt urządzeń przy użyciu [zadań](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operacje na urządzeniach

Aplikacja urządzenia działa na bliźniaczej reprezentacji urządzenia przy użyciu następujących operacji atomowych:

* **Pobierz bliźniaczej reprezentacji urządzenia**. Ta operacja zwraca dokument bliźniaczej reprezentacji urządzenia (w tym żądane i zgłoszone właściwości systemu) dla aktualnie podłączonego urządzenia. (Tagi nie są widoczne dla aplikacji urządzenia).

* **Częściowo zaktualizować zgłoszone właściwości**. Ta operacja umożliwia częściową aktualizację zgłoszonych właściwości aktualnie podłączonego urządzenia. Ta operacja używa tego samego formatu aktualizacji JSON, który rozwiązanie zaplecza używa do częściowej aktualizacji żądanych właściwości.

* **Obserwować żądane właściwości**. Aktualnie podłączone urządzenie może wybrać opcję powiadamiania o aktualizacjach żądanych właściwości, gdy się pojawią. Urządzenie otrzymuje taką samą formę aktualizacji (częściowej lub pełnej wymiany) wykonywane przez zaplecze rozwiązania.

Wszystkie poprzednie operacje wymagają uprawnienia **DeviceConnect,** zgodnie z definicją w [sekcji Kontrola dostępu do Usługi IoT Hub](iot-hub-devguide-security.md).

ZestawY [SDK urządzeń IoT platformy Azure](iot-hub-devguide-sdks.md) ułatwiają korzystanie z poprzednich operacji z wielu języków i platform. Aby uzyskać więcej informacji na temat szczegółów ujegłędnych IoT Hub dla żądanej synchronizacji właściwości, zobacz [Przepływ ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Format znaczników i właściwości

Znaczniki, żądane właściwości i zgłaszane właściwości są obiektami JSON z następującymi ograniczeniami:

* **Klawisze:** Wszystkie klucze w obiektach JSON są zakodowane w uikw. Dozwolone znaki wykluczają znaki kontrolne UNICODE (segmenty `.` `$`C0 i C1) oraz , i SP.

* **Wartości:** Wszystkie wartości w obiektach JSON mogą być następujące typy JSON: logiczne, liczba, ciąg, obiekt. Tablice nie są dozwolone.

    * Liczby całkowite mogą mieć minimalną wartość -4503599627370496 i maksymalną wartość 4503599627370495.

    * Wartości ciągów są zakodowane w uinadka UTF-8 i mogą mieć maksymalną długość 4 KB.

* **Głębokość:** Maksymalna głębokość obiektów JSON w znacznikach, żądanych właściwościach i zgłoszonych właściwościach wynosi 10. Na przykład prawidłowy jest następujący obiekt:

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

## <a name="device-twin-size"></a>Podwójny rozmiar urządzenia

Centrum IoT Hub wymusza limit rozmiaru `tags`8 KB wartości i limit rozmiaru 32 KB na wartość `properties/desired` i `properties/reported`. Sumy te nie zawierają elementów `$etag` `$version`tylko `$metadata/$lastUpdated`do odczytu, takich jak , i .

Rozmiar bliźniaczej reprezentacji oblicza się w następujący sposób:

* Dla każdej właściwości w dokumencie JSON Usługa IoT Hub oblicza zbiorczo i dodaje długość klucza i wartości właściwości.

* Klucze właściwości są traktowane jako ciągi zakodowane w u. UTF8.

* Proste wartości właściwości są traktowane jako ciągi kodowane zgodnie z UTF8, wartości liczbowe (8 bajtów) lub wartości logiczne (4 bajty).

* Rozmiar ciągów zakodowanych w uiczonych w uiczonych w ułowieniu jest obliczany przez zliczanie wszystkich znaków, z wyjątkiem znaków kontrolnych UNICODE (segmenty C0 i C1).

* Złożone wartości właściwości (obiekty zagnieżdżone) są obliczane na podstawie agregacji rozmiaru kluczy właściwości i wartości właściwości, które zawierają.

Centrum IoT hub odrzuca z błędem wszystkie operacje, `properties/desired`które `properties/reported` mogłyby zwiększyć rozmiar `tags`, lub dokumentów powyżej limitu.

## <a name="device-twin-metadata"></a>Podwójne metadane urządzenia

Usługa IoT Hub zachowuje sygnaturę czasową ostatniej aktualizacji dla każdego obiektu JSON w bliźniaczej reprezentacji urządzenia żądane i zgłaszane właściwości. Sygnatury czasowe są w formacie UTC i zakodowane w formacie `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) .

Przykład:

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
                    "sendFrequency": "5m",
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

Te informacje są przechowywane na każdym poziomie (nie tylko po liściach struktury JSON), aby zachować aktualizacje, które usuwają klucze obiektów.

## <a name="optimistic-concurrency"></a>Optymistyczna współbieżność

Tagi, żądane i zgłaszane właściwości wszystkie obsługują optymistyczne współbieżności.
Tagi mają ETag, zgodnie z [RFC7232](https://tools.ietf.org/html/rfc7232), który reprezentuje reprezentację JSON tagu. Można użyć tagów ETags w operacjach aktualizacji warunkowej z zaplecza rozwiązania, aby zapewnić spójność.

Bliźniacza reprezentacja urządzenia żądane i zgłaszane właściwości `$version` nie mają tagów ETags, ale mają wartość, która jest gwarantowana przyrostowe. Podobnie jak eTag, wersja może służyć przez stronę aktualizacji, aby wymusić spójność aktualizacji. Na przykład aplikacja urządzenia dla zgłoszonej właściwości lub zaplecza rozwiązania dla żądanej właściwości.

Wersje są również przydatne, gdy agent obserwujący (na przykład aplikacja urządzenia przestrzegająca żądanych właściwości) musi uzgodnić wyścigi między wynikiem operacji pobierania a powiadomieniem o aktualizacji. Sekcja [Przepływ ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow) zawiera więcej informacji.

## <a name="device-reconnection-flow"></a>Przepływ ponownego połączenia urządzenia

Usługa IoT Hub nie zachowuje żądanych właściwości powiadomień o aktualizacji dla urządzeń rozłączonych. Wynika z tego, że urządzenie, które łączy musi pobrać dokument pełne właściwości żądane, oprócz subskrybowania powiadomień aktualizacji. Biorąc pod uwagę możliwość wyścigów między powiadomieniami o aktualizacji i pełnym wyszukiwaniem, należy zapewnić następujący przepływ:

1. Aplikacja urządzenia łączy się z centrum IoT Hub.
2. Aplikacja urządzenia subskrybuje żądane właściwości powiadomień aktualizacji.
3. Aplikacja urządzenia pobiera pełny dokument dla żądanych właściwości.

Aplikacja urządzenia może ignorować `$version` wszystkie powiadomienia z mniej lub równą wersją pełnego pobranego dokumentu. Takie podejście jest możliwe, ponieważ Centrum IoT gwarantuje, że wersje zawsze przyrost.

> [!NOTE]
> Ta logika jest już zaimplementowana w [zestawach SDK urządzeń IoT platformy Azure.](iot-hub-devguide-sdks.md) Ten opis jest przydatny tylko wtedy, gdy aplikacja urządzenia nie może używać żadnych zestawów SDK urządzeń Usługi Azure IoT i musi bezpośrednio zaprogramować interfejs MQTT.
> 

## <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* W [artykule Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) opisano różne punkty końcowe, które każdy centrum IoT udostępnia dla operacji wykonywania i zarządzania.

* W [artykule Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisano przydziały, które mają zastosowanie do usługi Centrum IoT i zachowanie ograniczania przepustowości, których można oczekiwać podczas korzystania z usługi.

* W [artykule Zestaw SDK urządzeń i usług Usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera listę zestawów SDK w różnych językach, których można używać podczas tworzenia aplikacji na urządzenia i usługi, które współdziałają z centrum IoT Hub.

* [Język zapytań Centrum IoT dla bliźniaczych urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md) artykuł zawiera opis języka zapytań Usługi IoT Hub, którego można użyć do pobierania informacji z Usługi IoT Hub o bliźniaczych bliźniąt urządzeń i zadaniach.

* Artykuł [pomocy technicznej MQTT usługi IoT Hub](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi protokołu MQTT w centrum IoT Hub.

## <a name="next-steps"></a>Następne kroki

Teraz dowiedziałeś się o bliźniaczych urządzeń, możesz być zainteresowany następującymi tematami przewodnika dla deweloperów usługi IoT Hub:

* [Opis i używanie bliźniąt modułów w Uirozie IoT Hub](iot-hub-devguide-module-twins.md)
* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)
* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujące samouczki Usługi IoT Hub:

* [Jak korzystać z bliźniaczej reprezentacji urządzenia](iot-hub-node-node-twin-getstarted.md)
* [Jak korzystać z właściwości bliźniaczej reprezentacji urządzenia](tutorial-device-twins.md)
* [Zarządzanie urządzeniami za pomocą narzędzi usługi Azure IoT dla programu VS Code](iot-hub-device-management-iot-toolkit.md)
