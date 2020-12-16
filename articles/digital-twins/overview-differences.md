---
title: Różnice od pierwszego wydania
titleSuffix: Azure Digital Twins
description: Dowiedz się, co zmieniło się w nowej wersji usługi Azure Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: fb0d3e3c57a26f7ca14b74edc42cb657ba6074c3
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605005"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>Co to jest nowa usługa Azure Digital bliźniaczych reprezentacji? Czym różni się od poprzedniej wersji (2018)?

Pierwsza publiczna wersja zapoznawcza usługi Azure Digital bliźniaczych reprezentacji została wydana w październiku 2018. Chociaż podstawowe koncepcje z tej pierwszej wersji zostały przeniesione do nowej usługi, wiele interfejsów i szczegółów implementacji uległo zmianie, aby usługa była bardziej elastyczna i dostępna. Te zmiany zostały zauzasadnione przez Opinie klientów.

> [!IMPORTANT]
> W świetle rozszerzonych możliwości usługi, poprzednia usługa Azure Digital bliźniaczych reprezentacji zostanie wycofana, a jej interfejsy API i powiązane dane nie będą już dostępne od 18 stycznia 2021.

Jeśli podczas pierwszej publicznej wersji zapoznawczej była używana pierwsza wersja usługi Azure Digital bliźniaczych reprezentacji, Skorzystaj z informacji i najlepszych rozwiązań w tym artykule, aby dowiedzieć się, jak korzystać z nowej usługi, i korzystać z jej funkcji.

## <a name="differences-by-topic"></a>Różnice według tematu

Poniższy wykres zawiera szczegółowe informacje o pojęciach, które uległy zmianie między poprzednią wersją usługi i nową (aktualną) usługą.

| Temat | W poprzedniej wersji | W nowej wersji |
| --- | --- | --- | --- |
| **Modelowanie**<br>*Bardziej elastyczny* | Poprzednia wersja została zaprojektowana wokół przestrzeni inteligentnej, więc pochodzi z wbudowanego słownika dla budynków. | Nowa usługa Azure Digital bliźniaczych reprezentacji to domena-niezależny od. Możesz zdefiniować własne niestandardowe słownictwo i modele niestandardowe dla swojego rozwiązania, aby reprezentować więcej rodzajów środowisk w bardziej elastyczny sposób.<br><br>Dowiedz się więcej na temat [*koncepcji: modele niestandardowe*](concepts-models.md). |
| **Topologia**<br>*Bardziej elastyczny*| Poprzednie wydanie obsługuje strukturę danych drzewa, która jest dostosowana do inteligentnych obszarów. Cyfrowy bliźniaczych reprezentacji został połączony z relacjami hierarchicznymi. | W nowej wersji cyfrowy bliźniaczych reprezentacji może być połączony z dowolnymi topologiami grafów, jednak zorganizowany w odpowiedni sposób. Zapewnia to większą elastyczność do wyrażania złożonych relacji świata rzeczywistego.<br><br>Dowiedz się więcej na temat [*koncepcji: Digital bliźniaczych reprezentacji i wykres bliźniaczy*](concepts-twins-graph.md). |
| **Środowisko obliczeniowe**<br>*Bogatsze i bardziej elastyczne* | W poprzedniej wersji logika przetwarzania zdarzeń i danych telemetrycznych została zdefiniowana w funkcjach języka JavaScript zdefiniowanych przez użytkownika (UDF). Debugowanie za pomocą UDF było ograniczone. | Nowa wersja ma otwarty model obliczeniowy: Możesz zapewnić logikę niestandardową, dołączając zewnętrzne zasoby obliczeniowe, takie jak [Azure Functions](../azure-functions/functions-overview.md). Dzięki temu można korzystać z wybranego języka programowania, uzyskiwać dostęp do niestandardowych bibliotek kodu bez ograniczeń i korzystać z zasobów programistycznych i debugowania, które mogą mieć usługi zewnętrzne.<br><br>Dowiedz się więcej na ten [*temat: Konfigurowanie funkcji platformy Azure do przetwarzania danych*](how-to-create-azure-function.md). |
| **Zarządzanie urządzeniami za pomocą IoT Hub**<br>*Więcej dostępności* | Poprzednie wydanie zarządzane urządzenia z wystąpieniem [IoT Hub](../iot-hub/about-iot-hub.md) , które było wewnętrzne w usłudze Azure Digital bliźniaczych reprezentacji. To zintegrowane centrum nie jest w pełni dostępne dla deweloperów. | W nowej wersji możesz "przenieść swoje centrum IoT", dołączając niezależnie od siebie wystąpienie IoT Hub (wraz z urządzeniami, które już się zarządza). Zapewnia to pełen dostęp do możliwości IoT Hub i umożliwia kontrolę nad zarządzaniem urządzeniami.<br><br>Dowiedz się więcej w temacie [*How to: pozyskiwanie danych telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Bezpieczeństwo**<br>*Bardziej Standard* | Poprzednie wydanie zawierało wstępnie zdefiniowane role, których można użyć do zarządzania dostępem do wystąpienia. | Nowa wersja jest zintegrowana z tą samą usługą zaplecza [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md) , która jest używana przez inne usługi platformy Azure. Może to ułatwić uwierzytelnianie między innymi usługami platformy Azure w rozwiązaniu, takimi jak IoT Hub, Azure Functions, Event Grid i wiele innych.<br>Za pomocą RBAC można nadal używać wstępnie zdefiniowanych ról lub można tworzyć i konfigurować role niestandardowe.<br><br>Dowiedz się więcej na temat [*koncepcji: zabezpieczenia dla rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md). |
| **Skalowalność**<br>*Mniejszą* | W poprzedniej wersji obowiązują ograniczenia skalowania dla urządzeń, komunikatów, wykresów i jednostek skalowania. Tylko jedno wystąpienie usługi Azure Digital bliźniaczych reprezentacji było obsługiwane na subskrypcję.  | Nowe wydanie polega na nowej architekturze o zwiększonej skalowalności i mocy obliczeniowej. Obsługuje ona również 10 wystąpień na region na subskrypcję.<br><br>Zobacz temat informacje o limitach [*usługi*](reference-service-limits.md) , aby uzyskać szczegółowe informacje o limitach w bieżącej wersji. |

## <a name="service-limits"></a>Limity usługi

Listę limitów cyfrowych bliźniaczych reprezentacji na platformie Azure można znaleźć w temacie [*Reference: Limits Service*](reference-service-limits.md).

## <a name="next-steps"></a>Następne kroki

Następnie szczegółowe do pracy z usługą Azure Digital bliźniaczych reprezentacji przy użyciu pierwszego samouczka:

[*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)