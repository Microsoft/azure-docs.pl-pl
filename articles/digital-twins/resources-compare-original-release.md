---
title: Różnice od wersji oryginalnej
titleSuffix: Azure Digital Twins
description: Dowiedz się, co zmieniło się w nowej wersji usługi Azure Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99072723"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Co to jest nowa usługa Azure Digital bliźniaczych reprezentacji? Jak różni się od wersji oryginalnej (2018)?

Pierwsza publiczna wersja zapoznawcza usługi Azure Digital bliźniaczych reprezentacji została wydana w październiku 2018. Chociaż podstawowe koncepcje z tej oryginalnej wersji zostały przeniesione do bieżącej usługi, wiele interfejsów i szczegółów implementacji uległo zmianie, aby usługa była bardziej elastyczna i dostępna. Te zmiany zostały zauzasadnione przez Opinie klientów.

> [!IMPORTANT]
> W świetle rozszerzonych możliwości usługi, oryginalna usługa Azure Digital bliźniaczych reprezentacji została wycofana. Od stycznia 2021, jej interfejsy API i skojarzone dane nie są już dostępne.

Jeśli podczas pierwszej publicznej wersji zapoznawczej była używana pierwsza wersja usługi Azure Digital bliźniaczych reprezentacji, Skorzystaj z informacji i najlepszych rozwiązań w tym artykule, aby dowiedzieć się, jak korzystać z bieżącej usługi, i korzystać z jej funkcji.

## <a name="differences-by-topic"></a>Różnice według tematu

Poniższy wykres zawiera szczegółowe informacje o pojęciach, które uległy zmianie w stosunku do oryginalnej wersji usługi i bieżącej usługi.

| Temat | W wersji oryginalnej | W bieżącej wersji |
| --- | --- | --- | --- |
| **Modelowanie**<br>*Bardziej elastyczny* | Oryginalna wersja została zaprojektowana wokół przestrzeni inteligentnej, więc pochodzi z wbudowaną słownictwem dla budynków. | Bieżąca usługa Azure Digital bliźniaczych reprezentacji to Domain-niezależny od. Możesz zdefiniować własne niestandardowe słownictwo i modele niestandardowe dla swojego rozwiązania, aby reprezentować więcej rodzajów środowisk w bardziej elastyczny sposób.<br><br>Dowiedz się więcej na temat [*koncepcji: modele niestandardowe*](concepts-models.md). |
| **Topologia**<br>*Bardziej elastyczny*| Oryginalne wydanie obsługuje strukturę danych drzewa, która jest dostosowana do inteligentnych obszarów. Cyfrowy bliźniaczych reprezentacji został połączony z relacjami hierarchicznymi. | W bieżącej wersji cyfrowy bliźniaczych reprezentacji może być połączony z dowolnymi topologiami grafów, jednak zorganizowany. Zapewnia to większą elastyczność do wyrażania złożonych relacji świata rzeczywistego.<br><br>Dowiedz się więcej na temat [*koncepcji: Digital bliźniaczych reprezentacji i wykres bliźniaczy*](concepts-twins-graph.md). |
| **Środowisko obliczeniowe**<br>*Bogatsze i bardziej elastyczne* | W pierwotnej wersji logika przetwarzania zdarzeń i danych telemetrycznych została zdefiniowana w funkcjach języka JavaScript zdefiniowanych przez użytkownika (UDF). Debugowanie za pomocą UDF było ograniczone. | Bieżąca wersja zawiera otwarty model obliczeniowy: można utworzyć logikę niestandardową, dołączając zewnętrzne zasoby obliczeniowe, takie jak [Azure Functions](../azure-functions/functions-overview.md). Dzięki temu można korzystać z wybranego języka programowania, uzyskiwać dostęp do niestandardowych bibliotek kodu bez ograniczeń i korzystać z zasobów programistycznych i debugowania, które mogą mieć usługi zewnętrzne.<br><br>Dowiedz się więcej na ten [*temat: Konfigurowanie funkcji platformy Azure do przetwarzania danych*](how-to-create-azure-function.md). |
| **Zarządzanie urządzeniami za pomocą IoT Hub**<br>*Więcej dostępności* | Oryginalne urządzenia zarządzane przez program z wystąpieniem [IoT Hub](../iot-hub/about-iot-hub.md) , które było wewnętrzne w usłudze Azure Digital bliźniaczych reprezentacji. To zintegrowane centrum nie jest w pełni dostępne dla deweloperów. | W bieżącej wersji "można przenieść" Centrum IoT Hub, dołączając niezależnie od siebie utworzone wystąpienie IoT Hub (wraz z urządzeniami, które już zarządza). Zapewnia to pełen dostęp do możliwości IoT Hub i umożliwia kontrolę nad zarządzaniem urządzeniami.<br><br>Dowiedz się więcej w temacie [*How to: pozyskiwanie danych telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Bezpieczeństwo**<br>*Bardziej Standard* | Oryginalna wersja zawierała wstępnie zdefiniowane role, których można użyć do zarządzania dostępem do wystąpienia. | Bieżąca wersja jest zintegrowana z tą samą usługą zaplecza [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md) , która jest używana przez inne usługi platformy Azure. Może to ułatwić uwierzytelnianie między innymi usługami platformy Azure w rozwiązaniu, takimi jak IoT Hub, Azure Functions, Event Grid i wiele innych.<br>Za pomocą RBAC można nadal używać wstępnie zdefiniowanych ról lub można tworzyć i konfigurować role niestandardowe.<br><br>Dowiedz się więcej na temat [*koncepcji: zabezpieczenia dla rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md). |
| **Skalowalność**<br>*Mniejszą* | Oryginalna wersja ma ograniczenia skali dla urządzeń, komunikatów, wykresów i jednostek skalowania. Tylko jedno wystąpienie usługi Azure Digital bliźniaczych reprezentacji było obsługiwane na subskrypcję.  | Bieżąca wersja jest oparta na nowej architekturze z ulepszoną skalowalnością i ma większą moc obliczeniową. Obsługuje ona również 10 wystąpień na region na subskrypcję.<br><br>Zobacz [*limity usługi Azure Digital bliźniaczych reprezentacji*](reference-service-limits.md) , aby uzyskać szczegółowe informacje o limitach w bieżącej wersji. |

## <a name="service-limits"></a>Limity usługi

Listę limitów cyfrowych bliźniaczych reprezentacji na platformie Azure można znaleźć w temacie [*limity usługi Azure Digital bliźniaczych reprezentacji*](reference-service-limits.md).

## <a name="next-steps"></a>Następne kroki

* Szczegółowe do pracy z bieżącą wersją w przewodniku szybki start: [*poznawanie przykładowego scenariusza*](quickstart-adt-explorer.md).

* Lub Rozpocznij odczytywanie najważniejszych koncepcji z [*pojęciami: modele niestandardowe*](concepts-models.md).