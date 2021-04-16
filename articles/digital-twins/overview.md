---
title: Co to jest usługa Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: Omówienie tego, co można zrobić za pomocą Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: d03a4865c8db52f74f4130c458fec3028f5b95a4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481671"
---
# <a name="what-is-azure-digital-twins"></a>Co to jest usługa Azure Digital Twins?

**Azure Digital Twins** to oferta platformy jako usługi (PaaS), która umożliwia tworzenie grafów wiedzy opartych na cyfrowych modelach całych środowisk. Mogą to być budynki, fabryki, farmy, sieci energetyczne, miejskich, miasta i inne — nawet w całych miastach. Te modele cyfrowe mogą służyć do uzyskania szczegółowych informacji, które wpływają na lepsze produkty, zoptymalizowane operacje, obniżone koszty i przełomowe doświadczenia klientów.

Wykorzystaj swoją wiedzę specjalistyczną na Azure Digital Twins do tworzenia dostosowanych, połączonych rozwiązań, które:
* Modelowanie dowolnego środowiska i zapewnianie możliwości życia cyfrowych bliźniaczych reprezentacji w skalowalny i bezpieczny sposób
* Łączenie zasobów, takich jak urządzenia IoT i istniejące systemy biznesowe
* Tworzenie dynamicznej logiki biznesowej i przetwarzania danych przy użyciu niezawodnego systemu zdarzeń
* Integracja z danymi, analizą i usługami AI na platformie Azure, aby ułatwić śledzenie przeszłości, a następnie przewidywanie przyszłości

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins możliwości

Poniżej znajduje się podsumowanie funkcji dostarczonych przez Azure Digital Twins.

### <a name="open-modeling-language"></a>Otwarty język modelowania

W Azure Digital Twins definiowanie jednostek cyfrowych reprezentujących osoby, miejsca i rzeczy w środowisku fizycznym przy użyciu niestandardowych typów bliźniaczych reprezentacji nazywanych [**modelami**](concepts-models.md). 

Definicje modeli można uważać za specjalistyczne słownictwo opisujące Twoją firmę. Na przykład w przypadku rozwiązania do zarządzania budynku można zdefiniować modele takie jak "budynek", "łódka" i "winda". Następnie możesz tworzyć **bliźniacze reprezentacje cyfrowe** na podstawie tych modeli, aby reprezentować określone środowisko.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

Modele są definiowane w języku przypominacym JSON o nazwie [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)i opisują bliźniacze reprezentacji pod względem właściwości stanu, zdarzeń telemetrii, poleceń, składników i relacji.
* Modele definiują **semantyczne** relacje między jednostkami, aby można było połączyć bliźniacze reprezentacji z grafem wiedzy, który odzwierciedla ich interakcje. Modele można myśleć jak o rzeczownikach w opisie świata, a relacje jako czasowniki.
* Można również specjalizować bliźniacze reprezentacji przy użyciu dziedziczenia modelu. Jeden model może dziedziczyć z innego.

DtDL jest używany w przypadku modeli danych w innych usługach Azure IoT, w tym [IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) [i Time Series Insights (TSI).](../time-series-insights/overview-what-is-tsi.md) Dzięki temu twoje rozwiązanie Azure Digital Twins połączone i zgodne z innymi częściami ekosystemu platformy Azure.

### <a name="live-execution-environment"></a>Środowisko wykonawcze na żywo

Modele cyfrowe w Azure Digital Twins są aktualnymi reprezentacjami świata rzeczywistego. Korzystając z relacji w niestandardowych modelach DTDL, połączysz bliźniacze reprezentacje z wykresem na żywo **reprezentującym** Twoje środowisko.

Możesz wyświetlić wizualizację wykresu Azure Digital Twins za pomocą przykładowej aplikacji, Azure Digital Twins [**eksploratora**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

Oto widok przykładowej wizualizacji:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Zrzut ekranu przedstawiający Azure Digital Twins eksploratora aplikacji z grafem węzłów reprezentujących bliźniacze reprezentacje cyfrowe" lightbox="media/includes/azure-digital-twins-explorer.png":::

Azure Digital Twins zapewnia rozbudowany **system zdarzeń, który** zapewnia aktualną graf z przetwarzaniem danych i logiką biznesową. Możesz połączyć zewnętrzne zasoby obliczeniowe, takie [jak Azure Functions](../azure-functions/functions-overview.md), aby przetwarzać te dane w elastyczny, dostosowany sposób.

Możesz również wyodrębnić szczegółowe informacje ze środowiska wykonywania na żywo przy użyciu zaawansowanego interfejsu API Azure Digital Twins **zapytań.** Interfejs API umożliwia wykonywanie zapytań przy użyciu rozbudowanych warunków wyszukiwania, w tym wartości właściwości, relacji, właściwości relacji, informacji o modelu i innych. Możesz również łączyć zapytania, zbierać szeroką gamę szczegółowych informacji o środowisku i odpowiadać na pytania niestandardowe, które są dla Ciebie ważne.

### <a name="input-from-iot-and-business-systems"></a>Dane wejściowe z IoT i systemów biznesowych

Aby zapewnić, że środowisko wykonywania na żywo usługi Azure Digital Twins jest aktualne w świecie rzeczywistym, możesz użyć usługi [IoT Hub](../iot-hub/about-iot-hub.md) do połączenia rozwiązania z urządzeniami IoT i IoT Edge urządzeniami. Te urządzenia zarządzane przez centrum są reprezentowane jako część grafu bliźniaczej reprezentacji i zapewniają dane, które wpływają na model.

W tym celu możesz utworzyć nową IoT Hub za pomocą Azure Digital Twins lub połączyć istniejącą IoT Hub z urządzeniami, które już zarządza.

Możesz również prowadzić Azure Digital Twins z innych źródeł danych przy użyciu interfejsów API REST lub łączników do innych usług, [takich jak Logic Apps](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Dane wyjściowe do usługi TSI, magazynu i analizy

Dane w modelu Azure Digital Twins mogą być kierowane do usług podrzędnego platformy Azure w celu dodatkowej analizy lub magazynowania. Jest to udostępniane za **pośrednictwem tras zdarzeń**, które używają usługi [Event Hub,](../event-hubs/event-hubs-about.md) [Event Grid](../event-grid/overview.md)Service Bus lub [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) do obsługi żądanych przepływów danych.

Niektóre czynności, które można wykonać za pomocą tras zdarzeń, to:
* Przechowywanie danych usługi Azure Digital Twins w usłudze [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* Analizowanie danych usługi Azure Digital Twins za pomocą usługi [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) lub innych narzędzi do analizy danych firmy Microsoft
* Integrowanie większych przepływów pracy z usługą Logic Apps
* Łączenie Azure Digital Twins z Time Series Insights w celu śledzenia historii szeregów czasu każdej bliźniaczej reprezentacji
* Wyrównywanie modelu szeregów czasowych w usłudze Time Series Insights ze źródłem w usłudze Azure Digital Twins

Jest to inny sposób, Azure Digital Twins połączyć się z większym rozwiązaniem i obsługiwać niestandardowe potrzeby w zakresie dalszej pracy z tymi informacjami.

## <a name="azure-digital-twins-in-a-solution-context"></a>Azure Digital Twins w kontekście rozwiązania

Azure Digital Twins jest często używany w połączeniu z innymi usługami platformy Azure w ramach większego rozwiązania IoT. 

Kompletne rozwiązanie korzystające Azure Digital Twins może zawierać następujące części:
* Wystąpienie Azure Digital Twins usługi. Przechowuje modele bliźniaczych reprezentacji oraz graf bliźniaczych reprezentacji ze stanem i orkiestruje przetwarzanie zdarzeń.
* Co najmniej jedna aplikacja klienca, która Azure Digital Twins wystąpieniem, konfigurując modele, tworząc topologię i wyodrębniając szczegółowe informacje z grafu bliźniaczej reprezentacji.
* Co najmniej jeden zewnętrzny za zasoby obliczeniowe do przetwarzania zdarzeń generowanych przez Azure Digital Twins lub połączone źródła danych, takie jak urządzenia. Jednym z typowych sposobów zapewnienia zasobów obliczeniowych jest [Azure Functions](../azure-functions/functions-overview.md).
* Centrum IoT w celu zapewnienia możliwości zarządzania urządzeniami i strumienia danych IoT.
* Usługi podrzędne do obsługi zadań, takich jak integracja przepływu pracy (na przykład [Logic Apps,](../logic-apps/logic-apps-overview.md)zimny magazyn, integracja szeregów czasowych lub analiza).

Na poniższym diagramie pokazano, Azure Digital Twins znajduje się w kontekście większego rozwiązania Azure IoT.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagram przedstawiający źródła danych wejściowych, usługi wyjściowe i komunikację dwukierunkową zarówno z aplikacjami klienckimi, jak i zewnętrznymi zasobami obliczeniowymi." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Limity usługi

Informacje na temat **limitów usługi dla Azure Digital Twins** można znaleźć tutaj: Azure Digital Twins [limity usługi](reference-service-limits.md). Może to być przydatne podczas pracy z usługą w celu zrozumienia ograniczeń funkcjonalnych i szybkości usługi, a także limitów, które można dostosować w razie potrzeby.

## <a name="terminology"></a>Terminologia

Listę typowych terminów IoT i ich zastosowań w usługach Azure **IoT,** w tym informacje dotyczące usługi Azure Digital Twins, można znaleźć tutaj: Słownik [usługi Azure IoT.](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json) Może to być przydatna referencja podczas rozpoczynania pracy z Azure Digital Twins i tworzenia rozwiązania IoT.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat Azure Digital Twins z przewodnika Szybki start: [*Szybki start: eksplorowanie przykładowego scenariusza.*](quickstart-azure-digital-twins-explorer.md)

* Możesz też zacząć czytać o Azure Digital Twins pojęciach z [*tematem Pojęcia: Modele niestandardowe.*](concepts-models.md)