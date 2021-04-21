---
title: Modele & rozliczeń
description: Omówienie sposobu działania modeli cen i rozliczeń w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777153"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modele cen i rozliczeń dla Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) umożliwia tworzenie i uruchamianie zautomatyzowanych przepływów pracy integracji, które można skalować w chmurze. W tym artykule opisano sposób działania modeli rozliczeń i cen dla Logic Apps i powiązanych zasobów. Aby uzyskać szczegółowe informacje o cenach, [zobacz Logic Apps Cennik.](https://azure.microsoft.com/pricing/details/logic-apps) Aby dowiedzieć się, jak planować i monitorować koszty oraz zarządzać nimi, zobacz Planowanie i zarządzanie kosztami dla [Azure Logic Apps.](plan-manage-costs.md)

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Cennik z wieloma dzierżawami

Model cenowy z płatnością za użycie dotyczy aplikacji logiki uruchamianych w publicznej, "globalnej" wielodostępnej Logic Apps usługi. Wszystkie przebiegi pomyślne i nieudane są mierzone i rozliczane.

Na przykład żądanie, które wykonuje wyzwalacz sondowania, jest nadal mierzone jako wykonanie, nawet jeśli ten wyzwalacz zostanie pominięty i nie zostanie utworzone żadne wystąpienie przepływu pracy aplikacji logiki.

| Elementy | Opis |
|-------|-------------|
| [Wbudowane wyzwalacze](../connectors/built-in.md) i akcje | Działają natywnie w Logic Apps i są mierzone przy użyciu [ **ceny akcji**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Na przykład wyzwalacz HTTP i wyzwalacz żądania są wbudowanymi wyzwalaczami, a akcja HTTP i akcja Odpowiedź są wbudowanymi akcjami. Operacje na danych, operacje wsadowe, operacje zmiennych i akcje sterowania przepływem [pracy,](../connectors/built-in.md)takie jak pętle, warunki, przełącznik, gałęzie równoległe itp., są również wbudowanymi akcjami. |
| [Wyzwalacze i](../connectors/managed.md) akcje łącznika standardowego <p><p>[Wyzwalacze i](../connectors/apis-list.md#custom-apis-and-connectors) akcje łącznika niestandardowego | Mierzone przy użyciu [standardowej ceny łącznika.](https://azure.microsoft.com/pricing/details/logic-apps/) |
| [Wyzwalacze i](../connectors/managed.md) akcje łącznika przedsiębiorstwa | Mierzone przy użyciu [ceny łącznika przedsiębiorstwa.](https://azure.microsoft.com/pricing/details/logic-apps/) Jednak w publicznej wersji zapoznawczej łączniki dla przedsiębiorstw są mierzone przy użyciu [ *ceny łącznika Standardowa.*](https://azure.microsoft.com/pricing/details/logic-apps/) |
| Akcje wewnątrz [pętli](logic-apps-control-flow-loops.md) | Każda akcja uruchamiana w pętli jest mierzona dla każdego cyklu pętli, który jest uruchamiany. <p><p>Załóżmy na przykład, że masz pętlę "dla każdego", która zawiera akcje, które przetwarzają listę. Usługa Logic Apps miernikuje każdą akcję uruchamianą w tej pętli przez pomnożenie liczby elementów listy z liczbą akcji w pętli i dodaje akcję, która uruchamia pętlę. Dlatego obliczenie dla listy 10 elementów to (10 * 1) + 1, co powoduje wykonanie 11 akcji. |
| Liczba ponownych prób | Aby obsłużyć najbardziej podstawowe wyjątki i błędy, można skonfigurować zasady ponawiania dla wyzwalaczy i akcji, jeśli są obsługiwane. [](logic-apps-exception-handling.md#retry-policies) Te ponowne próby wraz z oryginalnym żądaniem są naliczane według stawek w zależności od tego, czy wyzwalacz lub akcja ma wbudowany typ Standard lub Enterprise. Na przykład za akcję, która jest wykonywana z 2 ponownych próbami, są naliczane opłaty za 3 wykonania akcji. |
| [Przechowywanie danych i zużycie magazynu](#data-retention) | Mierzone przy użyciu ceny przechowywania danych, którą można znaleźć na stronie Logic Apps [cennika](https://azure.microsoft.com/pricing/details/logic-apps/)w tabeli **Szczegóły** cennika. |
|||

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Wyświetlanie metryk dotyczących wykonań i użycia magazynu](plan-manage-costs.md#monitor-billing-metrics)
* [Limity w Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Nie mierzone

* Wyzwalacze, które są pomijane z powodu niesprawnych warunków
* Akcje, które nie były uruchamiane, ponieważ aplikacja logiki zatrzymała się przed zakończeniem
* [Wyłączone aplikacje logiki](#disabled-apps)

### <a name="other-related-resources"></a>Inne powiązane zasoby

Aplikacje logiki pracują z innymi powiązanymi zasobami, takimi jak konta integracji, lokalne bramy danych i środowiska usług integracji (ISE). Aby dowiedzieć się więcej o cenach tych zasobów, zapoznaj się z tymi sekcjami w dalszej części tego tematu:

* [On-premises data gateway (Lokalna brama danych)](#data-gateway)
* [Model cen konta integracji](#integration-accounts)
* [Model cenowy ise](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Porady dotyczące szacowania kosztów użycia

Aby ułatwić oszacowanie dokładniejszych kosztów użycia, zapoznaj się z tymi poradami:

* Rozważ możliwą liczbę komunikatów lub zdarzeń, które mogą pojawić się w danym dniu, zamiast opierać obliczenia tylko na interwale sondowania.

* Gdy zdarzenie lub komunikat spełnia kryteria wyzwalacza, wiele wyzwalaczy natychmiast próbuje odczytać wszystkie inne zdarzenia lub komunikaty oczekujące, które spełniają kryteria. To zachowanie oznacza, że nawet po wybraniu dłuższego interwału sondowania wyzwalacz jest wyzwalany na podstawie liczby oczekujących zdarzeń lub komunikatów, które kwalifikują się do uruchamiania przepływów pracy. Wyzwalacze zgodne z tym zachowaniem obejmują Azure Service Bus i Azure Event Hub.

  Załóżmy na przykład, że skonfigurujemy wyzwalacz, który codziennie sprawdza punkt końcowy. Gdy wyzwalacz sprawdza punkt końcowy i znajduje 15 zdarzeń spełniających kryteria, wyzwalacz jest uruchamiany i uruchamia odpowiedni przepływ pracy 15 razy. Usługa Logic Apps miernikuje wszystkie akcje, które wykonuje te 15 przepływów pracy, w tym żądania wyzwalacza.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Cennik ise

Model cen stałych dotyczy aplikacji logiki uruchamianych w środowisku [ *usługi integracji* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Koszt ise jest rozliczany przy [użyciu środowisko usługi integracji,](https://azure.microsoft.com/pricing/details/logic-apps)która zależy od poziomu ise lub [ *tworzyć SKU.*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) Te ceny różnią się od cen wielodostępnych, ponieważ płacisz za pojemność zarezerwowaną i dedykowane zasoby niezależnie od tego, czy ich używasz.

| ISE SKU | Opis |
|---------|-------------|
| **Premium** | Jednostka podstawowa ma [stałą pojemność](logic-apps-limits-and-config.md#integration-service-environment-ise) i jest rozliczana za stawkę godzinową dla [jednostki SKU Premium.](https://azure.microsoft.com/pricing/details/logic-apps) Jeśli potrzebujesz większej przepływności, możesz [dodać więcej jednostek skalowania](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) podczas tworzenia ise lub później. Każda jednostka skalowania jest rozliczana za stawkę godzinową, która stanowi [mniej więcej połowę podstawowej stawki jednostkowej.](https://azure.microsoft.com/pricing/details/logic-apps) <p><p>Aby uzyskać informacje o pojemności i limitach, zobacz [Limity ise w Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Deweloper** | Jednostka podstawowa ma [stałą pojemność](logic-apps-limits-and-config.md#integration-service-environment-ise) i jest rozliczana godzinowo dla [jednostki SKU dewelopera.](https://azure.microsoft.com/pricing/details/logic-apps) Jednak ta sku nie ma umowy dotyczącej poziomu usług (SLA), możliwości skalowania w górę ani nadmiarowości podczas przetwarzania, co oznacza, że mogą wystąpić opóźnienia lub przestoje. Aktualizacje zaplecza mogą sporadycznie przerywać działanie usługi. <p><p>**Ważne:** Upewnij się, że używasz tej sku tylko do eksploracji, eksperymentów, opracowywania i testowania — a nie do testowania produkcyjnego lub wydajnościowego. <p><p>Aby uzyskać informacje o pojemności i limitach, zobacz [Limity dotyczące platform ISE Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Uwzględnione bez dodatkowych kosztów

| Elementy | Opis |
|-------|-------------|
| [Wbudowane wyzwalacze](../connectors/built-in.md) i akcje | Wyświetl **etykietę Core** i uruchom go w tym samym programie ISE co aplikacje logiki. |
| [Łączniki standardowe](../connectors/managed.md) <p><p>[Łączniki dla przedsiębiorstw](../connectors/managed.md#enterprise-connectors) | — Zarządzane łączniki, które wyświetlają etykietę **ISE,** są specjalnie zaprojektowane do pracy bez lokalnej bramy danych i działają w tym samym środowisku ISE co aplikacje logiki. Cennik ise obejmuje tyle połączeń przedsiębiorstwa, ile chcesz. <p><p>— Łączniki, które nie wyświetlają etykiety ISE, działają w wielodostępnej Logic Apps service. Jednak cennik ise obejmuje te wykonania dla aplikacji logiki, które działają na platformie ISE. |
| Akcje wewnątrz [pętli](logic-apps-control-flow-loops.md) | Cennik ise obejmuje każdą akcję, która jest uruchamiana w pętli dla każdego cyklu pętli, który jest uruchamiany. <p><p>Załóżmy na przykład, że masz pętlę "dla każdego", która zawiera akcje, które przetwarzają listę. Aby uzyskać łączną liczbę wykonań akcji, pomnóż liczbę elementów listy z liczbą akcji w pętli i dodaj akcję, która uruchamia pętlę. Dlatego obliczenie dla listy 10 elementów to (10 * 1) + 1, co powoduje wykonanie 11 akcji. |
| Liczba ponownych prób | Aby obsłużyć najbardziej podstawowe wyjątki i błędy, można skonfigurować zasady ponawiania dla wyzwalaczy i akcji, jeśli są obsługiwane. [](logic-apps-exception-handling.md#retry-policies) Cennik ise obejmuje ponowne próby wraz z oryginalnym żądaniem. |
| [Przechowywanie danych i zużycie magazynu](#data-retention) | Aplikacje logiki w ise nie są naliczane koszty przechowywania i magazynowania. |
| [Integracja kont](#integration-accounts) | Obejmuje użycie dla pojedynczej warstwy konta integracji opartego na sku ise bez dodatkowych kosztów. |
|||

Aby uzyskać informacje o limitach, [zobacz Limity ise w Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Konta integracji

Konto [integracji](../logic-apps/logic-apps-pricing.md#integration-accounts) to oddzielny zasób, który można utworzyć i połączyć z aplikacjami logiki, aby można było eksplorować, kompilować i testować rozwiązania integracji B2B, które korzystają z [możliwości przetwarzania EDI](logic-apps-enterprise-integration-b2b.md) i [XML.](logic-apps-enterprise-integration-xml.md)

Azure Logic Apps oferuje te poziomy lub warstwy kont [](https://azure.microsoft.com/pricing/details/logic-apps/) integracji, które różnią się cenami i modelem [rozliczeń,](logic-apps-pricing.md#integration-accounts)w zależności od tego, czy aplikacje logiki są oparte na zużyciu, czy na ise:

| Warstwa | Opis |
|------|-------------|
| **Podstawowa** | W scenariuszach, w których chcesz tylko obsługi komunikatów lub działać jako mały partner biznesowy, który ma relację z partnerem handlowym z większą jednostką biznesową. <p><p>Obsługiwane przez Logic Apps SLA. |
| **Standardowa** | Scenariusze, w których masz bardziej złożone relacje B2B i zwiększoną liczbę jednostek, które musisz zarządzać. <p><p>Obsługiwane przez Logic Apps SLA. |
| **Bezpłatna** | Scenariusze eksploracyjne, a nie produkcyjne. Ta warstwa ma limity dostępności regionu, przepływności i użycia. Na przykład warstwa Bezpłatna jest dostępna tylko dla regionów publicznych na platformie Azure, na przykład Zachodnie stany USA lub Azja Południowo-Wschodnia, ale nie dla regionów [Azure (Chiny) — 21Vianet](/azure/china/overview-operations) lub [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Uwaga:** nie jest obsługiwana przez Logic Apps SLA. |
|||

Aby uzyskać informacje o limitach konta integracji, zobacz [Limits and configuration for Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), such:

* [Limity kont integracji na subskrypcję platformy Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limity różnych artefaktów na konto integracji](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Artefakty obejmują partnerów handlowych, umowy, mapy, schematy, zestawy, certyfikaty, konfiguracje wsadowe itp.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Konta integracji dla aplikacji logiki opartych na zużyciu

Konta integracji są rozliczane przy użyciu stałej ceny konta [integracji,](https://azure.microsoft.com/pricing/details/logic-apps/) która jest oparta na warstwie konta, z których korzystasz.

### <a name="ise-based-logic-apps"></a>Aplikacje logiki oparte na systemie ISE

Bez dodatkowych kosztów twoje urządzenie ISE obejmuje jedno konto integracji oparte na twojej sku środowiska ISE. Za dodatkową opłatą można utworzyć więcej kont integracji dla środowiska ISE, aby można było ich użyć do całkowitego limitu [środowiska ISE.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Dowiedz się więcej o modelu [cenowym ise](#fixed-pricing) wcześniej w tym temacie.

| ISE SKU | Dołączone konto integracji | Dodatkowy koszt |
|---------|------------------------------|-----------------|
| **Premium** | Pojedyncze konto [integracji w](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) standardowych środowiskach | Do 19 kolejnych kont w standardowych. Konta bezpłatne i podstawowe nie są dozwolone. |
| **Deweloper** | Jedno bezpłatne [konto](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integracji | Do 19 kolejnych kont w standardowych, jeśli masz już bezpłatne konto, lub 20 wszystkich kont w standardowych, jeśli nie masz bezpłatnego konta. Konta podstawowe nie są dozwolone. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Przechowywanie danych i zużycie magazynu

Wszystkie dane wejściowe i wyjściowe w historii uruchamiania aplikacji logiki są przechowywane i mierzone na podstawie czasu trwania i okresu przechowywania [historii tej aplikacji.](logic-apps-limits-and-config.md#run-duration-retention-limits)

* W przypadku aplikacji logiki w wielodostępnej usłudze Logic Apps opłaty za zużycie magazynu są [](https://azure.microsoft.com/pricing/details/logic-apps)naliczane po stałej cenie, którą można znaleźć na stronie Logic Apps cennika w tabeli **Szczegóły** cennika.

* W przypadku aplikacji logiki na platformach ISE zużycie magazynu nie wiąże się z kosztami przechowywania danych.

Aby monitorować użycie magazynu, [zobacz Wyświetlanie metryk wykonań i użycia magazynu.](plan-manage-costs.md#monitor-billing-metrics)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Lokalna brama danych

Lokalna [brama danych](../logic-apps/logic-apps-gateway-install.md) to oddzielny zasób, który można utworzyć, aby aplikacje logiki mogą uzyskać dostęp do danych lokalnych przy użyciu określonych łączników obsługiwanych przez bramę. Za operacje łączników, które są wykonywane za pośrednictwem bramy, są naliczane opłaty, ale za samą bramę nie są naliczane opłaty.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Wyłączone aplikacje logiki

Wyłączone aplikacje logiki nie są naliczane, ponieważ nie mogą tworzyć nowych wystąpień, gdy są wyłączone. Po wyłączeniu aplikacji logiki wszystkie aktualnie uruchomione wystąpienia mogą zająć trochę czasu, zanim całkowicie się zatrzymają.

## <a name="next-steps"></a>Następne kroki

* [Planowanie kosztów dla Azure Logic Apps](plan-manage-costs.md)
