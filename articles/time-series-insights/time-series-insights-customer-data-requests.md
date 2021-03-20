---
title: Funkcje żądania danych klienta — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej o funkcjach żądania danych klienta w Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5ecbabe4a5c3458c77d145fbce1281550470e00
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019076"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Azure Time Series Insights to zarządzana usługa w chmurze z magazynem, analizą i składnikami wizualizacji, które ułatwiają pozyskiwanie, przechowywanie, eksplorowanie i analizowanie miliardów zdarzeń jednocześnie.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Aby wyświetlać, eksportować i usuwać dane osobowe, które mogą podlegać żądaniu podmiotu danych, Administrator dzierżawy Azure Time Series Insights może użyć Azure Portal lub interfejsów API REST. Użycie Azure Portal do obsługi żądań podmiotu danych zapewnia mniej skomplikowaną metodę wykonywania tych operacji, które preferują większość użytkowników.

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Azure Time Series Insights traktuje dane osobowe jako dane skojarzone z administratorami i użytkownikami Time Series Insights. Time Series Insights przechowuje Azure Active Directory identyfikator obiektu dla użytkowników mających dostęp do środowiska. Azure Portal wyświetla adresy e-mail użytkowników, ale te adresy e-mail nie są przechowywane w Time Series Insights, są dynamicznie wyszukiwane przy użyciu identyfikatora obiektu Azure Active Directory w Azure Active Directory.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administrator dzierżawy może usunąć dane klienta przy użyciu Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jednak przed usunięciem danych klienta za pośrednictwem portalu należy usunąć zasady dostępu użytkownika ze środowiska Time Series Insights w Azure Portal. Aby uzyskać więcej informacji, przeczytaj temat [udzielanie dostępu do danych w środowisku Time Series Insights przy użyciu Azure Portal](./concepts-access-policies.md).

Można również wykonywać operacje usuwania na zasadach dostępu przy użyciu interfejsu API REST. Aby uzyskać więcej informacji, zobacz [zasady dostępu do odczytu — usuwanie](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/delete).

Time Series Insights jest zintegrowany z blokiem zasad w Azure Portal. Zarówno Time Series Insights, jak i blok zasad umożliwiają wyświetlanie, eksportowanie i usuwanie danych użytkownika przechowywanych w ramach usługi. Każda akcja usuwania podjęta w bloku zasady Azure Portal powoduje usunięcie danych użytkownika w Time Series Insights. Na przykład jeśli użytkownik ma zapisane zapytanie osobiste, to zapytanie zostanie trwale usunięte z Eksploratora Time Series Insights. Jeśli użytkownik ma zapisane zapytanie udostępnione, zapytanie będzie przechowywane, ale informacje o użytkowniku są trwale usuwane. Poniższa Uwaga zawiera instrukcje dotyczące wykonywania tych zadań.

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Podobnie jak w przypadku usuwania danych, Administrator dzierżawy może wyświetlać i eksportować dane przechowywane w Time Series Insights z bloku zasad w Azure Portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jeśli jesteś administratorem dzierżawy, możesz wyświetlić zasady dostępu do danych w środowisku Time Series Insights w Azure Portal. Aby uzyskać więcej informacji, przeczytaj temat [udzielanie dostępu do danych w środowisku Time Series Insights przy użyciu Azure Portal](./concepts-access-policies.md).

Możliwe jest również wykonywanie operacji eksportowania na zasadach dostępu przy użyciu operacji "list by Environment" w podanym interfejsie API REST. Aby uzyskać więcej informacji, zobacz [zasady dostępu do odczytu — lista według środowiska](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Aby usunąć dane przechowywane w Time Series Insights

Dane osobowe mogą być w Time Series Insights magazynem innym scenariuszu niż dane użytkownika i administratora. Jeśli rozważasz dane przechowywane w Time Series Insights jako dane osobowe, możesz wyeksportować i usunąć te dane, wykonując następujące czynności:

### <a name="view-and-export-data"></a>Wyświetlanie i eksportowanie danych

Aby wyświetlić i wyeksportować dane przechowywane w Time Series Insights, należy wyszukać te dane. Do wyświetlania i eksportowania danych można używać interfejsów API Time Series Insights Explorer lub Time Series Insights zapytań. Aby wyświetlić i wyeksportować dane za pomocą Eksploratora Time Series Insights, najpierw Wyszukaj, aby znaleźć dane użytkownika. Po przeszukiwaniu kliknij prawym przyciskiem myszy wykres i wybierz polecenie **Eksploruj zdarzenia**. Zostanie wyświetlona siatka zdarzeń i przedstawiono opcje eksportowania danych jako plików CSV i JSON.

Aby uzyskać więcej informacji, Przeczytaj [Azure Time Series Insights Explorer](time-series-insights-explorer.md).

### <a name="delete-data"></a>Usuwanie danych

Obecnie Time Series Insights nie obsługuje szczegółowego usuwania danych. Jednak Time Series Insights zapewnia możliwość usuwania danych klienta przechowywanych w ramach Time Series Insights przez skonfigurowanie zasad przechowywania. Okres przechowywania całego środowiska Time Series Insights można dostosować do dowolnej liczby dni, aby obsługiwać wymagania dotyczące usuwania.

Aby uzyskać więcej informacji, przeczytaj temat [Konfigurowanie przechowywania w Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Następne kroki

* Przeczytaj więcej [na temat udzielania dostępu do danych w środowisku Azure Time Series Insights](./concepts-access-policies.md).

* Wyświetl [Azure Time Series Insights Explorer](time-series-insights-explorer.md).

* Dowiedz się więcej o [konfigurowaniu przechowywania w Time Series Insights](time-series-insights-how-to-configure-retention.md).