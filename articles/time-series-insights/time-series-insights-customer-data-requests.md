---
title: Funkcje żądania danych klienta — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o funkcjach żądania danych klientów w usłudze Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 04/17/2020
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 3578710bf066e7745215d8efacafd2cf6c005eac
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640492"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klientów

Usługa Azure Time Series Insights to zarządzana usługa w chmurze z komponentami magazynu, analizy i wizualizacji, które ułatwiają jednoczesne pozyskiwania, przechowywania, eksplorowania i analizowania miliardów zdarzeń.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Aby wyświetlić, wyeksportować i usunąć dane osobowe, które mogą podlegać żądaniu osoby, której dane dotyczą, administrator dzierżawy usługi Azure Time Series Insights może używać witryny Azure portal lub interfejsów API REST. Za pomocą witryny Azure portal do obsługi żądań osób, których dane dotyczą, zapewnia mniej złożoną metodę wykonywania tych operacji, które większość użytkowników woli.

## <a name="identifying-customer-data"></a>Identyfikowanie danych klientów

Usługa Azure Time Series Insights uważa dane osobowe za dane skojarzone z administratorami i użytkownikami usługi Time Series Insights. Usługa Time Series Insights przechowuje identyfikator obiektu usługi Azure Active Directory użytkowników z dostępem do środowiska. Portal Azure wyświetla adresy e-mail użytkowników, ale te adresy e-mail nie są przechowywane w usłudze Time Series Insights, są one dynamicznie wyszukiwane przy użyciu identyfikatora obiektu usługi Azure Active Directory w usłudze Azure Active Directory.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administrator dzierżawy może usuwać dane klientów przy użyciu witryny Azure portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jednak przed usunięciem danych klienta za pośrednictwem portalu, należy usunąć zasady dostępu użytkownika ze środowiska usługi Time Series Insights w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Udzielanie dostępu do danych do środowiska usługi Time Series Insights przy użyciu witryny Azure portal](time-series-insights-data-access.md).

Można również wykonać operacje usuwania na zasadach dostępu przy użyciu interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Zasady dostępu — usuwanie](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete).

Usługa Time Series Insights jest zintegrowana z blokem zasad w witrynie Azure portal. Zarówno usługa Time Series Insights, jak i blok Zasady umożliwiają wyświetlanie, eksportowanie i usuwanie danych użytkownika przechowywanych w usłudze. Wszelkie działania usuwania podjęte w bloku zasad witryny Azure portal powoduje usunięcie danych użytkownika w ramach usługi Time Series Insights. Na przykład jeśli użytkownik ma zapisane zapytanie osobiste, kwerenda ta jest trwale usuwana z Eksploratora usługi Time Series Insights. Jeśli użytkownik ma zapisaną kwerendę udostępnioną, kwerenda będzie się powtarzać, ale informacje o użytkowniku zostaną trwale usunięte. Poniższa uwaga zawiera instrukcje dotyczące wykonywania tych zadań.

## <a name="exporting-customer-data"></a>Eksportowanie danych klientów

Podobnie jak usuwanie danych, administrator dzierżawy można wyświetlać i eksportować dane przechowywane w aplikacji Time Series Insights z bloku zasad w witrynie Azure portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jeśli jesteś administratorem dzierżawy, możesz wyświetlić zasady dostępu do danych w środowisku Usługi Time Series Insights w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Udzielanie dostępu do danych do środowiska usługi Time Series Insights przy użyciu witryny Azure portal](time-series-insights-data-access.md).

Istnieje również możliwość wykonywania operacji eksportu na zasadach dostępu przy użyciu operacji "lista według środowiska" w dostarczonym interfejsie API REST. Aby uzyskać więcej informacji, przeczytaj artykuł [Zasady dostępu — lista według środowiska](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Aby usunąć dane przechowywane w aplikacji Time Series Insights

Dane osobowe mogą trafiać do magazynu usługi Time Series Insights, innego scenariusza niż dane użytkownika i administratora. Jeśli dane przechowywane w usłudze Time Series Insights zostaną uznane za dane osobowe, można je wyeksportować i usunąć, wykonując następujące czynności:

**Wyświetlanie i eksportowanie danych**

Aby wyświetlić i wyeksportować dane przechowywane w aplikacji Time Series Insights, należy wyszukać te dane. Za pomocą explorera usługi Time Series Insights lub interfejsów API kwerend usługi Time Series Insights można wyświetlać i eksportować dane. Aby wyświetlić i wyeksportować dane przy użyciu Eksploratora Usługi Time Series Insights, najpierw wyszukaj dane użytkownika. Po wyszukuj prawym przyciskiem myszy wykres i wybierz pozycję **Eksploruj wydarzenia**. Pojawi się siatka zdarzeń i przedstawia opcje eksportowania danych jako CSV i JSON.

Aby uzyskać więcej informacji, przeczytaj [eksploratora usługi Azure Time Series Insights](time-series-insights-explorer.md).

**Usuwanie danych**

Obecnie usługa Time Series Insights nie obsługuje szczegółowego usuwania danych. Jednak usługa Time Series Insights umożliwia usuwanie danych klientów przechowywanych w aplikacji Time Series Insights przez skonfigurowanie zasad przechowywania. Okres przechowywania całego środowiska usługi Time Series Insights można dostosować do dowolnej liczby dni, aby spełnić wymagania dotyczące usuwania.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie przechowywania w aplikacji Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [udzielaniu dostępu do danych w środowisku usługi Azure Time Series Insights.](./time-series-insights-data-access.md)

* Wyświetl [eksploratora usługi Azure Time Series Insights](time-series-insights-explorer.md).

* Dowiedz się więcej o [konfigurowaniu przechowywania w aplikacji Time Series Insights](time-series-insights-how-to-configure-retention.md).