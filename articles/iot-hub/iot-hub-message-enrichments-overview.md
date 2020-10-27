---
title: Omówienie wzbogacania komunikatów IoT Hub platformy Azure
description: Ten artykuł zawiera informacje o wzbogacaniu komunikatów, które dają IoT Hub możliwość sygnatury wiadomości z dodatkowymi informacjami przed wysłaniem komunikatów do określonego punktu końcowego.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 3975a57c095a8593e392e932bd125308853d3756
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541523"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Wzbogacanie komunikatów dla komunikatów IoT Hub między urządzeniami a chmurą

*Wzbogacanie komunikatów* polega na tym, że IoT Hub *sygnatury* wiadomości z dodatkowymi informacjami przed wysłaniem komunikatów do określonego punktu końcowego. Jednym z powodów użycia wzbogacania komunikatów jest dołączenie danych, które mogą być używane do uproszczenia przetwarzania podrzędnego. Na przykład wzbogacanie komunikatów telemetrycznych urządzeń za pomocą znacznika sznurka urządzenia może zmniejszyć obciążenie klientów, aby umożliwić wywoływanie interfejsu API z użyciem urządzeń z systemem dla tych informacji.

![Przepływ wzbogacania komunikatów](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Wzbogacanie komunikatów ma trzy kluczowe elementy:

* Nazwa lub klucz wzbogacenia

* Wartość

* Co najmniej jeden [punkt końcowy](iot-hub-devguide-endpoints.md) , dla którego ma zostać zastosowane wzbogacanie.

**Klucz** jest ciągiem. Klucz może zawierać tylko znaki alfanumeryczne lub znaki specjalne: Łącznik ( `-` ), podkreślenie ( `_` ) i kropka ( `.` ).

Może **to** być dowolny z następujących przykładów:

* Dowolny ciąg statyczny. Wartości dynamiczne, takie jak warunki, logika, operacje i funkcje, są niedozwolone. Na przykład jeśli tworzysz aplikację SaaS, która jest używana przez kilku klientów, można przypisać identyfikator do każdego klienta i udostępnić ten identyfikator w aplikacji. Gdy aplikacja zostanie uruchomiona, IoT Hub będzie sygnaturą komunikatów telemetrycznych urządzenia z identyfikatorem klienta, dzięki czemu można przetwarzać komunikaty inaczej dla każdego klienta.

* Nazwa wysyłanego przez Centrum IoT Hub. Ta wartość jest *$iothubname* .

* Informacje z sznurka urządzenia, takie jak jego ścieżka. Przykładami mogą być *$Twin. Tags. Field* i *$Twin. Tags. Latitude* .

   > [!NOTE]
   > W tej chwili tylko $iothubname, $twin. Tags, $twin. Properties. pożądaną i $twin. Properties. raportowane są obsługiwane zmienne do wzbogacania komunikatów.

Wzbogacanie komunikatów są dodawane jako właściwości aplikacji do komunikatów wysyłanych do wybranych punktów końcowych.  

## <a name="applying-enrichments"></a>Stosowanie wzbogacania

Komunikaty mogą pochodzić z dowolnego źródła danych obsługiwanego przez [IoT Hub Routing komunikatów](iot-hub-devguide-messages-d2c.md), w tym następujące przykłady:

* dane telemetryczne urządzenia, takie jak temperatura lub ciśnienie
* powiadomienia o zmianie przędzy urządzenia — zmiany w bliźniaczych urządzeniach
* zdarzenia cyklu życia urządzenia, takie jak podczas tworzenia lub usuwania urządzenia

Można dodawać wzbogacenia do komunikatów, które przechodzą do wbudowanego punktu końcowego IoT Hub, lub komunikatów, które są kierowane do niestandardowych punktów końcowych, takich jak Azure Blob Storage, Service Bus Queue lub Service Bus temat.

Można dodać wzbogacanie do komunikatów, które są publikowane w Event Grid przez wybranie punktu końcowego jako Event Grid. Utworzymy domyślną trasę w IoT Hub do telemetrii urządzenia w oparciu o subskrypcję Event Grid. Ta pojedyncza trasa może obsłużyć wszystkie subskrypcje Event Grid. Można skonfigurować wzbogacenia punktu końcowego usługi Event Grid po utworzeniu subskrypcji usługi Event Grid na potrzeby telemetrii urządzenia. Aby uzyskać więcej informacji, zobacz [Centrum IoT i Event Grid](iot-hub-event-grid.md).

Wzbogacania są stosowane w odniesieniu do punktu końcowego. Jeśli określisz pięć wzbogaceń dla określonego punktu końcowego, wszystkie komunikaty przechodzą do tego punktu końcowego będą znakowane z tymi samymi pięcioma wzbogacaniem.

Wzbogacania można skonfigurować przy użyciu następujących metod:

| **Metoda** | **Polecenie** |
| ----- | -----| 
| Portal | [Witryna Azure Portal](https://portal.azure.com) | Zapoznaj się z [samouczkiem dotyczącym wzbogacania komunikatów](tutorial-message-enrichments.md) | 
| Interfejs wiersza polecenia platformy Azure   | [AZ IoT Hub Message-wzbogacanie](/cli/azure/iot/hub/message-enrichment) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](/powershell/module/az.iothub/add-aziothubmessageenrichment) |

Dodanie wzbogacania komunikatów nie powoduje dodania opóźnienia do routingu komunikatów.

Aby wypróbować wzbogacanie komunikatów, zobacz [Samouczek dotyczący wzbogacania komunikatów](tutorial-message-enrichments.md)

## <a name="limitations"></a>Ograniczenia

* Można dodać do 10 wzbogaceń IoT Hub dla tych centrów w warstwie Standardowa lub podstawowa. W przypadku centrów IoT w warstwie Bezpłatna można dodać do 2 wzbogacania.

* W niektórych przypadkach, jeśli stosujesz wzbogacanie z wartością ustawioną na tag lub właściwość w bliźniaczym urządzeniu, wartość zostanie zastosowana jako wartość ciągu. Na przykład jeśli wartość wzbogacania jest ustawiona na $twin. Tags. pole, komunikaty będą oznaczone ciągiem "$twin. Tags. Field" zamiast wartości tego pola z sznurka. Dzieje się tak w następujących przypadkach:

   * IoT Hub znajduje się w warstwie Podstawowa. Centra IoT warstwy Podstawowa nie obsługują bliźniaczych reprezentacji urządzeń.

   * IoT Hub znajduje się w warstwie Standardowa, ale urządzenie wysyłające wiadomość nie ma sznurka urządzenia.

   * IoT Hub znajduje się w warstwie Standardowa, ale nie istnieje ścieżka ze osiową urządzenia użytą dla wartości wzbogacania. Na przykład jeśli wartość wzbogacania jest ustawiona na $twin. Tags. Location, a sznurki urządzenia nie mają właściwości Location w obszarze Tagi, komunikat jest oznaczany ciągiem "$twin. Tags. Location". 

* Aktualizacje sznurka urządzenia mogą potrwać do 5 minut, aby można było je odzwierciedlić w odpowiedniej wartości wzbogacania.

* Łączny rozmiar komunikatów, łącznie z wzbogacami, nie może przekroczyć 256 KB. Jeśli rozmiar komunikatu przekracza 256 KB, IoT Hub spowoduje porzucenie komunikatu. Za pomocą [metryk IoT Hub](monitor-iot-hub-reference.md#metrics) można identyfikować i debugować błędy podczas usuwania komunikatów. Można na przykład monitorować *niezgodność komunikatów telemetrycznych* ( *D2C. telemetrii.* ruch wychodzący. nieprawidłowa) w [metrykach routingu](monitor-iot-hub-reference.md#routing-metrics). Aby dowiedzieć się więcej, zobacz [Monitor IoT Hub](monitor-iot-hub.md).

* Wzbogacania komunikatów nie mają zastosowania do wieloosiowych zdarzeń zmiany.

## <a name="pricing"></a>Cennik

Wzbogacanie komunikatów są dostępne bez dodatkowych opłat. Obecnie opłata jest naliczana w przypadku wysyłania komunikatu do IoT Hub. Opłata jest naliczana tylko raz dla tego komunikatu, nawet jeśli komunikat przejdzie do wielu punktów końcowych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi artykułami, aby uzyskać więcej informacji na temat routingu komunikatów do IoT Hub:

* [Samouczek wzbogacania komunikatów](tutorial-message-enrichments.md)

* [Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](iot-hub-devguide-messages-d2c.md)

* [Samouczek: Routing IoT Hub](tutorial-routing.md)