---
title: Funkcje rozwiązania połączonej fabryki — Azure | Microsoft Docs
description: W tym artykule opisano Omówienie funkcji wstępnie skonfigurowanego rozwiązania połączonej fabryki, takie jak pulpit nawigacyjny, reguły i alerty w chmurze.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: e492ebf70f69c985691852a1c1f2351d1e09578e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425014"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co to jest Akcelerator rozwiązania do podłączenia fabryki IoT?

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Połączona fabryka to implementacja architektury referencyjnej platformy Azure przemysłowego firmy Microsoft, spakowana jako rozwiązanie typu "open source". Można go użyć jako punktu wyjścia dla produktu komercyjnego. Możesz wdrożyć wstępnie utworzoną wersję rozwiązania połączonej fabryki w ramach subskrypcji platformy Azure z [akceleratorów rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Pulpit nawigacyjny rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-features/dashboard.png)

Kod akceleratora rozwiązania połączonej fabryki [jest dostępny w serwisie GitHub](https://github.com/Azure/azure-iot-connected-factory).

Połączona fabryka obejmuje następujące funkcje:

## <a name="industrial-device-interoperability"></a>Współdziałanie urządzeń przemysłowych

- Połącz się z zasobami przemysłowymi przy użyciu interfejsu OPC UA.
- Aby wyświetlić dane telemetryczne na żywo, użyj symulowanych linii produkcyjnych (z uruchomionymi serwerami OPC UA w kontenerach platformy Docker).
- Przeglądaj model informacyjny OPC UA serwerów OPC UA z poziomu pulpitu nawigacyjnego w chmurze.

## <a name="remote-management"></a>Zdalne zarządzanie

- Konfigurowanie zasobów OPC UA na pulpicie nawigacyjnym chmury (wywoływanie metod, Odczyt i zapis danych).
- Publikowanie i cofanie publikowania danych telemetrycznych z zasobów OPC UA z poziomu pulpitu nawigacyjnego chmury.

## <a name="cloud-dashboard"></a>Pulpit nawigacyjny chmury

- Wyświetl podglądy danych telemetrycznych bezpośrednio na pulpicie nawigacyjnym w chmurze.
- Wyświetlanie trendów w danych telemetrycznych i tworzenie korelacji przy użyciu pulpitu nawigacyjnego Eksploratora Time Series Insights.
- Zobacz obliczona ogólna wydajność sprzętu (OEE) i kluczowe wskaźniki wydajności (KPI) na pulpicie nawigacyjnym chmury.
- Wyświetl hierarchie zasobów przemysłowych w topologii drzewa, a także na mapie interaktywnej.
- Wyświetlanie, Potwierdzanie i zamykanie alertów z poziomu pulpitu nawigacyjnego w chmurze.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) jest skompilowany do przechowywania, wizualizowania i wykonywania zapytań dotyczących dużych ilości danych szeregów czasowych. Połączona fabryka korzysta z tej usługi.
- Połączona fabryka integruje się z tą usługą, umożliwiając przeprowadzanie dokładnej analizy danych urządzenia w czasie rzeczywistym.

## <a name="rules-and-alerts"></a>Reguły i alerty

[Skonfiguruj reguły oparte na progach dla alertów](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Kompleksowe zabezpieczenia

- Skonfiguruj uprawnienia zabezpieczeń dla użytkowników korzystających z kontroli dostępu opartej na rolach (RBAC).
- Kompleksowe szyfrowanie jest implementowane przy użyciu uwierzytelniania OPC UA (przy użyciu certyfikatów X. 509) oraz tokenów zabezpieczających.

## <a name="customizability"></a>Szerszym

- Dostosuj rozwiązanie pod kątem spełnienia określonych wymagań firmy.
- Pełny kod źródłowy, dostępny w serwisie GitHub. Zobacz repozytorium [wstępnie skonfigurowanego rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat akceleratora połączonej fabryki, zobacz Przewodnik Szybki Start [dotyczący rozwiązania do zarządzania urządzeniami z systemem IoT w chmurze](quickstart-connected-factory-deploy.md).
