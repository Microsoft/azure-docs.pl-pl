---
title: Model rozliczeń & cen
description: Omówienie sposobu działania modelu cen i rozliczeń dla usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: f47c7412bdd5ada1e50d1005b8e740e3f46ffd8d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536237"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Model cenowy aplikacji logiki azure

[Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) ułatwia tworzenie i uruchamianie zautomatyzowanych przepływów pracy integracji, które można skalować w chmurze. W tym artykule opisano, jak działają rozliczenia i ceny dla usługi Azure Logic Apps. Aby uzyskać informacje o cenach, zobacz [Cennik aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Model cen zużycia

W przypadku nowych aplikacji logiki, które działają publicznie, "globalnej", wielodostępne usługi Azure Logic Apps, płacisz tylko za to, czego używasz. Te aplikacje logiki używają planu opartego na zużyciu i modelu cenowego. W aplikacji logiki każdy krok jest działaniem, a usługa Azure Logic Apps mierzy wszystkie akcje uruchamiane w aplikacji logiki.

Na przykład akcje obejmują:

* [Wyzwalacze](#triggers), które są działaniami specjalnymi. Wszystkie aplikacje logiki wymagają wyzwalacza jako pierwszy krok.

* ["Wbudowane" lub natywne akcje,](../connectors/apis-list.md#built-in) takie jak HTTP, wywołania funkcji platformy Azure i zarządzania interfejsami API, itd.

* Wywołania [łączników zarządzanych,](../connectors/apis-list.md#managed-connectors) takich jak Outlook 365, Dropbox itd.

* [Kontroluj akcje przepływu pracy,](../connectors/apis-list.md#control-workflow) takie jak pętle, instrukcje warunkowe itd.

[Standardowe złącza](../connectors/apis-list.md#managed-connectors) są naliczane po [standardowej cenie złącza](https://azure.microsoft.com/pricing/details/logic-apps). Ogólnie dostępne [łączniki Przedsiębiorstwa](../connectors/apis-list.md#managed-connectors) są naliczane [po cenie łącznika Enterprise,](https://azure.microsoft.com/pricing/details/logic-apps)natomiast złącza public preview Enterprise są naliczane po [standardowej cenie łącznika.](https://azure.microsoft.com/pricing/details/logic-apps)

Dowiedz się więcej o tym, jak działa rozliczenia na [poziomach wyzwalaczy](#triggers) i [działań.](#actions) Aby uzyskać informacje o limitach, zobacz [Limity i konfiguracja usługi Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Model stałej ceny

[ *Środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zapewnia odosobniony sposób tworzenia i uruchamiania aplikacji logiki, które mogą uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure. Aplikacje logiki, które działają w ise nie ponoszą koszty przechowywania danych. Podczas tworzenia ise i tylko podczas tworzenia, można wybrać [poziom ISE lub "SKU",](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)które mają różne [stawki cenowe:](https://azure.microsoft.com/pricing/details/logic-apps)

* **Premium** ISE: Jednostka podstawowa tej jednostki SKU ma stałą pojemność, ale jeśli potrzebujesz większej przepustowości, możesz [dodać więcej jednostek skalowania](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) podczas tworzenia ISE lub później. Aby zapoznać się z limitami ISE, zobacz [Limity i konfiguracja usługi Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Deweloper** ISE: Ta jednostka SKU nie ma możliwości skalowania w górę, nie ma umowy na poziomie usługi (SLA) i nie ma opublikowanych limitów. Tej jednostki SKU należy używać tylko do eksperymentowania, tworzenia i testowania, a nie testowania produkcji lub wydajności.

W przypadku aplikacji logiki, które tworzysz i uruchamiasz w użymie, płacisz [stałą cenę miesięczną](https://azure.microsoft.com/pricing/details/logic-apps) za te funkcje:

* [Wbudowane](../connectors/apis-list.md#built-in) wyzwalacze i akcje

  W środowisku ISE wbudowane wyzwalacze i akcje wyświetlają etykietę **Core** i działają w tym samym środowisku ISE, co aplikacje logiki.

* [Złącza standardowe](../connectors/apis-list.md#managed-connectors) i złącza [Enterprise,](../connectors/apis-list.md#enterprise-connectors) które umożliwiają podłączenie do przedsiębiorstwa, co

   Łączniki standardowe i enterprise, które wyświetlają etykietę **ISE,** są uruchamiane w tym samym środowiskach ISE, co aplikacje logiki. Łączniki, które nie wyświetlają etykiety ISE, są uruchamiane publicznie, "globalna", usługa aplikacji logiki z wieloma dzierżawami. Stałe miesięczne ceny dotyczą również łączników uruchamianych w usłudze wielodostępnej, gdy są używane z aplikacjami logiki, które działają w usłudze ISE.

* Korzystanie z [konta integracyjnego](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) bez dodatkowych kosztów, w oparciu o jednostkę [ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

  * **Premium** Jednostka SKU ISE: jedno konto integracji [warstwy standardowej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Deweloper** Jednostka SKU ISE: jedno konto integracji [warstwy bezpłatnej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Każda jednostka SKU ISE jest ograniczona do 5 kont integracji ogółem. Aby uzyskać dodatkowe koszty, możesz mieć więcej kont integracji na podstawie jednostki SKU ISE:

  * **Premium** ISE SKU: Do czterech kolejnych kont standardowych. Brak kont bezpłatnych lub podstawowych.

  * **Deweloper** Ise SKU: maksymalnie 4 więcej kont standardowych lub maksymalnie 5 kont standardowych. Brak kont podstawowych.

  Aby uzyskać więcej informacji na temat limitów kont integracji, zobacz [Limity i konfiguracja usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Więcej informacji na temat [warstw kont integracji i ich modelu cenowego](#integration-accounts) można dowiedzieć się więcej w dalszej części tego tematu.

<a name="connectors"></a>

## <a name="connectors"></a>Łączniki

Łączniki usługi Azure Logic Apps ułatwią aplikacjom logicznym dostęp do aplikacji, usług i systemów w chmurze lub lokalnie, udostępniając [wyzwalacze,](#triggers) [akcje](#actions)lub oba te elementy. Łączniki są klasyfikowane jako standardowe lub enterprise. Aby uzyskać omówienie tych łączników, zobacz [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md). Jeśli dla interfejsów API REST, które mają być używane w aplikacjach logiki, nie są dostępne żadne wstępnie utworzone łączniki, można utworzyć [łączniki niestandardowe](https://docs.microsoft.com/connectors/custom-connectors), które są tylko otokami wokół tych interfejsów API REST. Łączniki niestandardowe są rozliczane jako łączniki standardowe. Poniższe sekcje zawierają więcej informacji na temat sposobu rozliczania wyzwalaczy i akcji.

<a name="triggers"></a>

## <a name="triggers"></a>Wyzwalacze

Wyzwalacze to akcje specjalne, które tworzą wystąpienie aplikacji logiki, gdy wystąpi określone zdarzenie. Wyzwalacze działają na różne sposoby, co wpływa na sposób pomiaru aplikacji logiki. Oto różne rodzaje wyzwalaczy, które istnieją w usłudze Azure Logic Apps:

* **Wyzwalacz sondowania:** Ten wyzwalacz stale sprawdza punkt końcowy dla wiadomości, które spełniają kryteria tworzenia wystąpienia aplikacji logiki i uruchamiania przepływu pracy. Nawet wtedy, gdy nie zostanie utworzone żadne wystąpienie aplikacji logiki, aplikacje logiki mierzy każde żądanie sondowania jako wykonanie. Aby określić interwał sondowania, należy skonfigurować wyzwalacz za pośrednictwem projektanta aplikacji logiki.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Wyzwalacz elementu webhook:** Ten wyzwalacz czeka na klienta, aby wysłać żądanie do określonego punktu końcowego. Każde żądanie wysłane do punktu końcowego elementu sieci Web jest liczone jako wykonanie akcji. Na przykład wyzwalacz żądania i http webhook są wyzwalacze elementu webhook.

* **Wyzwalacz cyklu:** Ten wyzwalacz tworzy wystąpienie aplikacji logiki na podstawie interwału cyklu skonfigurowanego w wyzwalaczu. Na przykład można skonfigurować wyzwalacz cyklu, który jest uruchamiany co trzy dni lub zgodnie z bardziej złożonym harmonogramem.

<a name="actions"></a>

## <a name="actions"></a>Akcje

Usługa Azure Logic Apps mierzy "wbudowane" akcje, takie jak HTTP, jako akcje macierzyste. Na przykład wbudowane akcje obejmują wywołania HTTP, wywołania z usługi Azure Functions lub usługi API Management oraz kroki przepływu sterowania, takie jak warunki, pętle i instrukcje przełącznika. Każda akcja ma swój własny typ akcji. Na przykład akcje, które [wywołują łączniki](https://docs.microsoft.com/connectors) mają typ "ApiConnection". Złącza te są klasyfikowane jako złącza Standardowe lub Enterprise, które są mierzone na podstawie ich odpowiednich [cen.](https://azure.microsoft.com/pricing/details/logic-apps) Łączniki przedsiębiorstwa w *wersji zapoznawczej* są ładowane jako łączniki standardowe.

Usługa Azure Logic Apps mierzy wszystkie pomyślne i nieudane akcje jako wykonania. Jednak aplikacje logiki nie mierzy tych akcji:

* Akcje, które są pomijane z powodu niezaspokojonych warunków
* Akcje, które nie są uruchamiane, ponieważ aplikacja logiki zatrzymała się przed zakończeniem

Dla akcji, które są uruchamiane wewnątrz pętli, usługa Azure Logic Apps zlicza każdą akcję dla każdego cyklu w pętli. Załóżmy na przykład, że masz pętlę "dla każdego", która przetwarza listę. Logic Apps mierzy akcję w tej pętli, mnożąc liczbę elementów listy z liczbą akcji w pętli i dodaje akcję, która uruchamia pętlę. Tak więc obliczenie dla listy 10 pozycji jest (10 * 1) + 1, co powoduje wykonanie 11 akcji.

## <a name="disabled-logic-apps"></a>Wyłączone aplikacje logiki

Wyłączone aplikacje logiki nie są naliczane, ponieważ nie mogą tworzyć nowych wystąpień, gdy są wyłączone. Po wyłączeniu aplikacji logiki wszystkie aktualnie uruchomione wystąpienia mogą zająć trochę czasu, zanim całkowicie się zatrzymają.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Konta integracji

[Model stałej ceny](https://azure.microsoft.com/pricing/details/logic-apps) ma zastosowanie do kont [integracji,](logic-apps-enterprise-integration-create-integration-account.md) gdzie można eksplorować, rozwijać i testować funkcje przetwarzania [B2B i EDI](logic-apps-enterprise-integration-b2b.md) i [XML](logic-apps-enterprise-integration-xml.md) w usłudze Azure Logic Apps bez dodatkowych kosztów. Każda subskrypcja platformy Azure może mieć do [określonego limitu kont integracji.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Każde konto integracji może przechowywać do określonego [limitu artefaktów,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)które obejmują partnerów handlowych, umów, map, schematów, zestawów, certyfikatów, konfiguracji partii i tak dalej.

Usługa Azure Logic Apps oferuje bezpłatne, podstawowe i standardowe konta integracji. Warstwy Podstawowe i Standardowe są obsługiwane przez umowę na poziomie usługi (SLA) aplikacji logiki, podczas gdy warstwa bezpłatna nie jest obsługiwana przez umowę SLA i ma ograniczenia dotyczące przepływności i użycia. Z wyjątkiem kont integracji bezpłatnej warstwy, można mieć więcej niż jedno konto integracji w każdym regionie platformy Azure. Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps/).

Jeśli masz [ *środowisko usługi integracji* (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) [Premium lub Developer,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)środowisko ISE może mieć 5 kont integracji. Aby dowiedzieć się, jak działa model cen stałych dla ise, zobacz poprzednią [sekcję Stały model cen w](#fixed-pricing) tym temacie. Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps).

Aby wybrać konto integracji bezpłatne, podstawowe lub standardowe, zapoznaj się z opisami przypadków użycia:

* **Bezpłatne**: Jeśli chcesz wypróbować scenariusze odkrywcze, a nie scenariusze produkcyjne

* **Podstawowe**: Jeśli chcesz tylko obsługi wiadomości lub działać jako mały partner biznesowy, który ma relacje z partnerem handlowym z większym podmiotem biznesowym

* **Standard**: W przypadku bardziej złożonych relacji B2B i zwiększonej liczby encji, którymi należy zarządzać

<a name="data-retention"></a>

## <a name="data-retention"></a>Przechowywanie danych

Z wyjątkiem aplikacji logiki, które działają w środowisku usługi integracji (ISE), wszystkie dane wejściowe i wyjściowe, które są przechowywane w historii uruchamiania aplikacji logiki są rozliczane na podstawie [okresu przechowywania uruchomienia](logic-apps-limits-and-config.md#run-duration-retention-limits)aplikacji logiki . Aplikacje logiki, które działają w ise nie ponoszą koszty przechowywania danych. Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps).

Aby ułatwić monitorowanie zużycia magazynu aplikacji logiki, możesz:

* Wyświetl liczbę jednostek magazynu w GB, które aplikacja logiki używa co miesiąc.
* Wyświetl rozmiary danych wejściowych i wyjściowych określonej akcji w historii uruchamiania aplikacji logiki.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Wyświetlanie zużycia magazynu aplikacji logiki

1. W witrynie Azure portal znajdź i otwórz aplikację logiki.

1. Z menu aplikacji logiki w obszarze **Monitorowanie**wybierz pozycję **Metryki**.

1. W okienku po prawej stronie w obszarze **Tytuł wykresu**z listy **Metryka** wybierz pozycję **Użycie rozliczeń dla wykonań zużycia magazynu**.

   Ta metryka zapewnia liczbę jednostek zużycia magazynu w GB miesięcznie, które są coraz rozliczane.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Wyświetlanie rozmiarów wejściowych i wyjściowych akcji

1. W witrynie Azure portal znajdź i otwórz aplikację logiki.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W okienku po prawej stronie w obszarze **Historia przebiegów**wybierz przebieg zawierający dane wejściowe i wyjściowe, które chcesz sprawdzić.

1. W obszarze **Uruchamianie aplikacji Logika**wybierz pozycję **Uruchom szczegóły**.

1. W okienku **szczegółów uruchamiania aplikacji Logika** w tabeli akcje, która zawiera listę stanu i czasu trwania każdej akcji, wybierz akcję, którą chcesz wyświetlić.

1. W okienku **akcji aplikacji Logika** znajdź rozmiary danych wejściowych i wyjściowych tej akcji są wyświetlane odpowiednio w **łączu Wejścia** i **Wyjścia**.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o usłudze Azure Logic Apps](logic-apps-overview.md)
* [Tworzenie pierwszej aplikacji logiki](quickstart-create-first-logic-app-workflow.md)
