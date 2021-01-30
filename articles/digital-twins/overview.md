---
title: Co to jest usługa Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: Informacje o tym, co można zrobić za pomocą usługi Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: c0bc245a4be1ab7c326fa27cc1422878aca6331d
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071685"
---
# <a name="what-is-azure-digital-twins"></a>Co to jest usługa Azure Digital Twins?

**Usługa Azure Digital bliźniaczych reprezentacji** to oferta platformy jako usługi (PaaS), która umożliwia tworzenie wykresów wiedzy na podstawie cyfrowych modeli całego środowiska. Te środowiska mogą być budynkami, fabrykami, gospodarstwami, sieciami energetycznymi, liniami, Stadium i innymi — nawet w całym miejscowości. Te modele cyfrowe mogą służyć do uzyskiwania szczegółowych informacji, które zapewniają lepsze produkty, zoptymalizowane działania, zmniejszone koszty i przełomowe środowiska klienta.

Skorzystaj z doświadczenia w domenie na platformie Azure Digital bliźniaczych reprezentacji, aby tworzyć dostosowane, połączone rozwiązania, które:
* Modelowanie dowolnego środowiska i bliźniaczych reprezentacji cyfrowe w sposób skalowalny i bezpieczny
* Łączenie zasobów, takich jak urządzenia IoT i istniejące systemy biznesowe
* Korzystanie z niezawodnego systemu zdarzeń do tworzenia dynamicznej logiki biznesowej i przetwarzania danych
* Integruj się z usługami Azure Data, Analytics i AI, aby ułatwić śledzenie przeszłości i prognozowanie przyszłości

## <a name="azure-digital-twins-capabilities"></a>Możliwości usługi Azure Digital bliźniaczych reprezentacji

Poniżej znajduje się podsumowanie funkcji oferowanych przez usługę Azure Digital bliźniaczych reprezentacji.

### <a name="open-modeling-language"></a>Otwórz język modelowania

W usłudze Azure Digital bliźniaczych reprezentacji definiujemy jednostki cyfrowe, które reprezentują osoby, miejsca i rzeczy w Twoim środowisku fizycznym przy użyciu niestandardowych typów sznurka nazywanych [**modelami**](concepts-models.md). 

Można traktować te definicje modeli jako wyspecjalizowane słownictwo do opisywania firmy. Na przykład w przypadku rozwiązania do zarządzania kompilowaniem można definiować modele takie jak "budynek", "piętro" i "windka". Następnie można utworzyć **cyfrowy bliźniaczych reprezentacji** na podstawie tych modeli do reprezentowania określonego środowiska.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

Modele są zdefiniowane w języku podobnym do notacji JSON o nazwie [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)i opisują bliźniaczych reprezentacji pod względem właściwości stanu, zdarzeń telemetrii, poleceń, składników i relacji.
* Modele definiują **relacje** semantyczne między swoimi obiektami, aby można było połączyć bliźniaczych reprezentacji z grafem wiedzy, który odzwierciedla ich interakcje. Modele można traktować jako rzeczowniki w opisie świata i relacje jako czasowniki.
* Możesz również specjalizacji bliźniaczych reprezentacji przy użyciu dziedziczenia modelu. Jeden model może dziedziczyć z innego.

DTDL jest używany dla modeli danych w innych usługach Azure IoT, w tym [Plug and Play IoT (PNP)](../iot-pnp/overview-iot-plug-and-play.md) i [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). Dzięki temu można zapewnić łączność i zgodność rozwiązania Azure Digital bliźniaczych reprezentacji z innymi częściami ekosystemu platformy Azure.

### <a name="live-execution-environment"></a>Środowisko wykonawcze na żywo

Modele cyfrowe w usłudze Azure Digital bliźniaczych reprezentacji są na bieżąco z aktualnymi reprezentacjami świata rzeczywistego. Korzystając z relacji w niestandardowych modelach DTDL, nastąpi połączenie bliźniaczych reprezentacji do **grafu na żywo** reprezentującego Twoje środowisko.

Możesz wyświetlić wizualizację grafu Digital bliźniaczych reprezentacji na platformie Azure za pomocą przykładowej aplikacji, programu [**Azure Digital bliźniaczych reprezentacji Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/).

Oto widok wizualizacji przykładowej:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Zrzut ekranu przedstawiający przykładową aplikację w Eksploratorze Digital bliźniaczych reprezentacji" lightbox="media/includes/azure-digital-twins-explorer.png":::

Usługa Azure Digital bliźniaczych reprezentacji zapewnia rozbudowany **system zdarzeń** , który umożliwia aktualność tego wykresu przy użyciu przetwarzania danych i logiki biznesowej. Zewnętrzne zasoby obliczeniowe, takie jak [Azure Functions](../azure-functions/functions-overview.md), mogą być połączone w elastyczny sposób i dostosowane metody.

Możesz również wyodrębnić szczegółowe informacje ze środowiska wykonywania na żywo przy użyciu **interfejsu API** zaawansowanego zapytania usługi Azure Digital bliźniaczych reprezentacji. Interfejs API umożliwia wykonywanie zapytań z rozbudowanymi warunkami wyszukiwania, takimi jak wartości właściwości, relacje, właściwości relacji, informacje o modelu itd. Możesz również łączyć zapytania, gromadzić szeroką gamę informacji o środowisku i odpowiadać na niestandardowe pytania, które są dla Ciebie ważne.

### <a name="input-from-iot-and-business-systems"></a>Dane wejściowe z systemów IoT i Business

Aby zapewnić aktualną wersję środowiska wykonywania na żywo usługi Azure Digital bliźniaczych reprezentacji w świecie rzeczywistym, możesz użyć [IoT Hub](../iot-hub/about-iot-hub.md) , aby połączyć swoje rozwiązanie z urządzeniami IoT i IoT Edge. Te urządzenia zarządzane przez centrum są reprezentowane jako część grafu bliźniaczyego i zapewniają dane, które są dyskami modelu.

Nowe IoT Hub można utworzyć w tym celu za pomocą usługi Azure Digital bliźniaczych reprezentacji lub połączyć istniejące IoT Hub wraz z urządzeniami, które już zarządza.

Możesz także dyskować usługę Azure Digital bliźniaczych reprezentacji z innych źródeł danych przy użyciu interfejsów API REST lub łączników z innymi usługami, takimi jak [Logic Apps](../logic-apps/logic-apps-overview.md).

### <a name="output-to-tsi-storage-and-analytics"></a>Dane wyjściowe do TSI, magazynu i analizy

Dane w modelu Digital bliźniaczych reprezentacji na platformie Azure mogą być kierowane do usług platformy Azure w sieci podrzędnej w celu uzyskania dodatkowej analizy lub magazynu. Jest to zapewniane za pomocą **tras zdarzeń**, które używają [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)lub [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) do kierowania żądanych przepływów danych.

Oto kilka rzeczy, które można wykonywać za pomocą tras zdarzeń:
* Przechowywanie danych usługi Azure Digital Twins w usłudze [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* Analizowanie danych usługi Azure Digital Twins za pomocą usługi [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) lub innych narzędzi do analizy danych firmy Microsoft
* Integrowanie większych przepływów pracy z usługą Logic Apps
* Łączenie usługi Azure Digital bliźniaczych reprezentacji w celu Time Series Insights śledzenia historii poszczególnych sznurów czasowych
* Wyrównywanie modelu szeregów czasowych w usłudze Time Series Insights ze źródłem w usłudze Azure Digital Twins

Jest to inny sposób, w jaki usługa Azure Digital bliźniaczych reprezentacji może łączyć się z większym rozwiązaniem i wspierać niestandardowe potrzeby w zakresie ciągłego działania z tymi informacjami.

## <a name="azure-digital-twins-in-a-solution-context"></a>Usługa Azure Digital bliźniaczych reprezentacji w kontekście rozwiązania

Usługa Azure Digital bliźniaczych reprezentacji jest często używana w połączeniu z innymi usługami platformy Azure jako częścią większego rozwiązania IoT. 

Kompletne rozwiązanie przy użyciu usługi Azure Digital bliźniaczych reprezentacji może zawierać następujące części:
* Wystąpienie usługi Digital bliźniaczych reprezentacji na platformie Azure. Umożliwia to przechowywanie modeli bliźniaczych i grafów bliźniaczych oraz organizowanie przetwarzania zdarzeń.
* Co najmniej jedna aplikacja kliencka, która tworzy wystąpienie usługi Azure Digital bliźniaczych reprezentacji przez skonfigurowanie modeli, utworzenie topologii i wyodrębnienie szczegółowych informacji z grafu sznurów.
* Co najmniej jeden zewnętrzny zasób obliczeniowy do przetwarzania zdarzeń generowanych przez usługę Azure Digital bliźniaczych reprezentacji lub połączone źródła danych, takie jak urządzenia. Typowym sposobem zapewnienia zasobów obliczeniowych jest za pośrednictwem [Azure Functions](../azure-functions/functions-overview.md).
* Centrum IoT w celu zapewnienia możliwości zarządzania urządzeniami i strumieni danych IoT.
* Usługi podrzędne obsługujące zadania, takie jak integracja przepływu pracy (na przykład [Logic Apps](../logic-apps/logic-apps-overview.md), chłodna pamięć, integracja z seriami czasowymi lub analiza).

Na poniższym diagramie przedstawiono, gdzie usługa Azure Digital bliźniaczych reprezentacji znajduje się w kontekście większego rozwiązania Azure IoT.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagram przedstawiający źródła danych wejściowych, usługi wyjściowe i dwukierunkową komunikację z aplikacjami klienckimi i zewnętrznymi zasobami obliczeniowymi." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Limity usługi

Listę limitów cyfrowych bliźniaczych reprezentacji na platformie Azure można znaleźć w temacie [*limity usługi Azure Digital bliźniaczych reprezentacji*](reference-service-limits.md).

## <a name="next-steps"></a>Następne kroki

* Szczegółowe do pracy z usługą Azure Digital bliźniaczych reprezentacji w szybkim samouczku: [*Szybki Start: Eksplorowanie przykładowego scenariusza*](quickstart-adt-explorer.md).

* Lub zacznij czytać informacje o pojęciach dotyczących usługi Azure Digital bliźniaczych reprezentacji z [*koncepcjami: modelami niestandardowymi*](concepts-models.md).