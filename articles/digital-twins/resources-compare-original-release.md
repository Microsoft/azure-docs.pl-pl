---
title: Różnice w 2018 r. od wersji oryginalnej
titleSuffix: Azure Digital Twins
description: Dowiedz się, co zmieniło się w nowej wersji Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2bfd36b79ad800c14a68948aa8488e842d3f4def
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481178"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Co to jest nowa Azure Digital Twins? Czym różni się od oryginalnej wersji (2018)?

Pierwsza publiczna wersja zapoznawcza Azure Digital Twins została wydana w październiku 2018 r. Chociaż podstawowe koncepcje z tej oryginalnej wersji zostały wykonane w bieżącej usłudze, wiele interfejsów i szczegółów implementacji uległo zmianie, aby usługa została bardziej elastyczna i dostępna. Te zmiany były motywowane opiniami klientów.

> [!IMPORTANT]
> W związku z rozszerzonymi możliwościami nowej usługi oryginalna usługa Azure Digital Twins została wycofana. Od stycznia 2021 r. jej interfejsy API i skojarzone dane nie są już dostępne.

Jeśli używasz pierwszej wersji programu Azure Digital Twins podczas pierwszej publicznej wersji zapoznawczej, skorzystaj z informacji i najlepszych rozwiązań w tym artykule, aby dowiedzieć się, jak pracować z bieżącą usługą i korzystać z jej funkcji.

## <a name="differences-by-topic"></a>Różnice według tematu

Na poniższym wykresie przedstawiono obok siebie koncepcje, które zmieniły się między oryginalną wersją usługi i bieżącą usługą.

| Temat | W oryginalnej wersji | W bieżącej wersji |
| --- | --- | --- | --- |
| **Modelowanie**<br>*Bardziej elastyczne* | Oryginalna wersja została zaprojektowana wokół inteligentnych przestrzeni, więc została opracowana z wbudowanym słownictwem dla budynków. | Bieżąca Azure Digital Twins jest niezależny od domeny. Możesz zdefiniować własne słownictwo niestandardowe i niestandardowe modele dla swojego rozwiązania, aby reprezentować więcej rodzajów środowisk w bardziej elastyczny sposób.<br><br>Aby dowiedzieć się [*więcej, zobacz Pojęcia: Modele niestandardowe.*](concepts-models.md) |
| **Topologia**<br>*Bardziej elastyczne*| Oryginalna wersja obsługuje strukturę danych drzewa dostosowaną do przestrzeni inteligentnych. Bliźniacze bliźniacze reprezentacji zostały połączone za pomocą relacji hierarchicznych. | W bieżącej wersji twoje bliźniacze reprezentacjiki cyfrowe mogą być połączone z dowolną topologią grafów uporządkowaną w dowolny sposób. Zapewnia to większą elastyczność wyrażania złożonych relacji w świecie rzeczywistym.<br><br>Aby dowiedzieć się [*więcej, zobacz Concepts: Digital twins and the twin graph (Pojęcia: bliźniacze reprezentacji cyfrowe i graf bliźniaczych reprezentacji).*](concepts-twins-graph.md) |
| **Środowisko obliczeniowe**<br>*Bogatsze, bardziej elastyczne* | W pierwotnej wersji logika przetwarzania zdarzeń i telemetrii została zdefiniowana w funkcjach zdefiniowanych przez użytkownika (UDF) języka JavaScript. Debugowanie za pomocą UDF było ograniczone. | Bieżąca wersja ma otwarty model obliczeniowy: logikę niestandardową można udostępnić, dołączając zewnętrzne zasoby obliczeniowe, takie [jak Azure Functions](../azure-functions/functions-overview.md). Umożliwia to korzystanie z wybranego języka programowania, uzyskiwanie dostępu do niestandardowych bibliotek kodu bez ograniczeń oraz korzystanie z zasobów programowania i debugowania, które mogą mieć usługi zewnętrzne.<br><br>Dowiedz się więcej [*z tematu Jak skonfigurować funkcję platformy Azure do przetwarzania danych.*](how-to-create-azure-function.md) |
| **Zarządzanie urządzeniami za pomocą IoT Hub**<br>*Bardziej dostępne* | Pierwotne wydanie urządzeń zarządzanych z wystąpieniem [IoT Hub,](../iot-hub/about-iot-hub.md) które było wewnętrzne Azure Digital Twins usługi. To zintegrowane centrum nie było w pełni dostępne dla deweloperów. | W bieżącej wersji "przynieś własne" centrum IoT Hub, dołączając niezależnie utworzone wystąpienie usługi IoT Hub (wraz ze wszystkimi urządzeniami, które już zarządza). Zapewnia to pełny dostęp do IoT Hub i zapewnia kontrolę nad zarządzaniem urządzeniami.<br><br>Aby dowiedzieć się [*więcej, zobacz Jak pozyskać*](how-to-ingest-iot-hub-data.md)dane telemetryczne z IoT Hub . |
| **Bezpieczeństwo**<br>*Więcej standardowych* | Oryginalne wydanie miało wstępnie zdefiniowane role, których można użyć do zarządzania dostępem do wystąpienia. | Bieżąca wersja integruje się z taką samą usługą za pomocą kontroli dostępu na podstawie ról [(RBAC)](../role-based-access-control/overview.md) platformy Azure, z których korzystają inne usługi platformy Azure. Może to ułatwić uwierzytelnianie między innymi innymi usługami platformy Azure w rozwiązaniu, IoT Hub, Azure Functions, Event Grid i nie tylko.<br>Dzięki kontroli RBAC można nadal używać wstępnie zdefiniowanych ról lub tworzyć i konfigurować role niestandardowe.<br><br>Aby dowiedzieć się [*więcej, zobacz Pojęcia: zabezpieczenia Azure Digital Twins rozwiązań.*](concepts-security.md) |
| **Skalowalność**<br>*Większa* | Oryginalna wersja miała ograniczenia skalowania dla urządzeń, komunikatów, grafów i jednostek skalowania. Tylko jedno wystąpienie usługi Azure Digital Twins było obsługiwane na subskrypcję.  | Bieżąca wersja opiera się na nowej architekturze o większej skalowalności i ma większą moc obliczeniową. Obsługuje również 10 wystąpień na region na subskrypcję.<br><br>Zobacz [*Azure Digital Twins limitów usługi,*](reference-service-limits.md) aby uzyskać szczegółowe informacje o limitach w bieżącej wersji. |

## <a name="service-limits"></a>Limity usługi

Aby uzyskać listę limitów Azure Digital Twins, zobacz [*limity Azure Digital Twins usługi*](reference-service-limits.md).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z pracą z bieżącą wydaniem w przewodniku Szybki start: [*Szybki start: eksplorowanie przykładowego scenariusza.*](quickstart-azure-digital-twins-explorer.md)

* Możesz też zacząć czytać o kluczowych pojęciach za pomocą [*tematu Pojęcia: modele niestandardowe.*](concepts-models.md)