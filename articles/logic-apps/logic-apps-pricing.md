---
title: Rozliczenia & modele cenowe
description: Omówienie sposobu działania modeli cen i rozliczeń w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 2b37308bcbcd489876c21dce56878de7e0daf545
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699032"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modele cen i rozliczeń dla Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) pomaga tworzyć i uruchamiać zautomatyzowane przepływy pracy integracji, które można skalować w chmurze. W tym artykule opisano sposób działania modeli rozliczeń i cen dla usługi Logic Apps i powiązanych zasobów. Aby uzyskać określone stawki cenowe, zobacz [Cennik usługi Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps). Aby dowiedzieć się, jak planować i monitorować koszty, zarządzać nimi, zobacz [Planowanie i zarządzanie kosztami Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Ceny z wieloma dzierżawcami

Model cen użycia z opcją płatność za użycie dotyczy aplikacji logiki, które są uruchamiane w publicznej "globalnej" Logic Apps usłudze wielodostępnej. Wszystkie pomyślne i nieudane uruchomienia są naliczane i rozliczane.

Na przykład żądanie wykonywane przez wyzwalacz sondowania jest nadal mierzone jako wykonanie, nawet jeśli ten wyzwalacz zostanie pominięty, a wystąpienie przepływu pracy aplikacji logiki nie zostanie utworzone.

| Elementy | Opis |
|-------|-------------|
| [Wbudowane](../connectors/apis-list.md#built-in) wyzwalacze i akcje | Uruchamiaj natywnie w usłudze Logic Apps i są mierzone przy użyciu ceny [ **akcji**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Na przykład wyzwalacz HTTP i wyzwalacz żądania są wbudowanymi wyzwalaczami, podczas gdy akcja i akcja HTTP są wbudowane. Operacje na danych, operacje wsadowe, operacje zmienne i [Akcje kontroli przepływu pracy](../connectors/apis-list.md#control-workflow), takie jak pętle, warunki, przełączanie, gałęzie równoległe itd., są również akcjami wbudowanymi. |
| Standardowe wyzwalacze i akcje [łącznika](../connectors/apis-list.md#managed-connectors) <p><p>Wyzwalacze i akcje [łącznika niestandardowego](../connectors/apis-list.md#custom) | Mierzony przy użyciu [standardowej ceny łącznika](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Wyzwalacze i akcje [łącznika przedsiębiorstwa](../connectors/apis-list.md#managed-connectors) | Mierzony przy użyciu [ceny łącznika Enterprise](https://azure.microsoft.com/pricing/details/logic-apps/). Jednak w publicznej wersji zapoznawczej Łączniki przedsiębiorstwa są mierzone przy użyciu [ *standardowej* ceny łącznika](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Akcje wewnątrz [pętli](logic-apps-control-flow-loops.md) | Każda akcja uruchamiana w pętli jest naliczana dla każdego cyklu pętli, który działa. <p><p>Załóżmy na przykład, że masz pętlę "for each", która zawiera akcje, które przetwarzają listę. Usługa Logic Apps określa każdą akcję, która jest uruchamiana w tej pętli przez pomnożenie liczby elementów listy z liczbą akcji w pętli, a następnie dodaje akcję, która uruchamia pętlę. Dlatego obliczenia dla listy 10 elementów są (10 * 1) + 1, co skutkuje 11 wykonaniami akcji. |
| Liczba ponownych prób | Aby obsłużyć najbardziej podstawowe wyjątki i błędy, można skonfigurować [zasady ponawiania](logic-apps-exception-handling.md#retry-policies) dla wyzwalaczy i akcji, w których są obsługiwane. Te ponownych prób wraz z pierwotnym żądaniem są rozliczone według stawek w zależności od tego, czy wyzwalacz lub akcja mają typ wbudowany, standardowy, czy Enterprise. Na przykład akcja wykonywana z 2 ponownych prób jest naliczana za 3 wykonania akcji. |
| [Przechowywanie danych i użycie magazynu](#data-retention) | Mierzony przy użyciu ceny przechowywania danych, którą można znaleźć na [stronie cennika Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), w tabeli **szczegóły cennika** . |
|||

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Wyświetlanie metryk związanych z wykonywaniem i użyciem magazynu](plan-manage-costs.md#monitor-billing-metrics)
* [Limity w Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Bez pomiaru

* Wyzwalacze pominięte z powodu warunków niewypełnienia
* Akcje, które nie zostały uruchomione, ponieważ aplikacja logiki została zatrzymana przed zakończeniem
* [Wyłączone Aplikacje logiki](#disabled-apps)

### <a name="other-related-resources"></a>Inne powiązane zasoby

Usługa Logic Apps współpracuje z innymi powiązanymi zasobami, takimi jak konta integracji, lokalne bramy danych i środowiska usługi integracji (ISEs). Aby dowiedzieć się więcej o cenach tych zasobów, zapoznaj się z tymi sekcjami w dalszej części tego tematu:

* [On-premises data gateway (Lokalna brama danych)](#data-gateway)
* [Model cen konta integracji](#integration-accounts)
* [Model cen ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Wskazówki dotyczące oszacowania kosztów zużycia

Aby ułatwić oszacowanie dokładniejszych kosztów zużycia, zapoznaj się z następującymi wskazówkami:

* Należy wziąć pod uwagę potencjalną liczbę komunikatów lub zdarzeń, które mogą się pojawić w danym dniu, zamiast wykonywać obliczenia tylko na podstawie interwału sondowania.

* Gdy zdarzenie lub komunikat spełnia kryteria wyzwalacza, wiele wyzwalaczy natychmiast spróbuje odczytać wszystkie inne oczekujące zdarzenia lub komunikaty, które spełniają kryteria. To zachowanie oznacza, że nawet w przypadku wybrania dłuższego interwału sondowania uruchamiany jest wyzwalacz oparty na liczbie oczekujących zdarzeń lub komunikatów, które kwalifikują się do uruchomienia przepływów pracy. Wyzwalacze zgodne z tym zachowaniem obejmują Azure Service Bus i centrum zdarzeń platformy Azure.

  Załóżmy na przykład, że skonfigurowano wyzwalacz, który sprawdza punkt końcowy codziennie. Gdy wyzwalacz sprawdza punkt końcowy i znajdzie 15 zdarzeń, które spełniają kryteria, wyzwalacz wyzwala i uruchamia odpowiedni przepływ pracy 15 razy. Usługa Logic Apps obejmuje wszystkie akcje wykonywane przez te 15 przepływów pracy, w tym żądania wyzwalacza.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Cennik ISE

Stały model cen ma zastosowanie do aplikacji logiki, które działają w [ *środowisku usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE jest rozliczany przy użyciu [ceny środowisko usługi integracji](https://azure.microsoft.com/pricing/details/logic-apps), która zależy od utworzonego [poziomu ISE lub *jednostki SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) . Ceny te różnią się od cen z wielu dzierżawców, ponieważ płacisz za zarezerwowaną pojemność i zasoby dedykowane niezależnie od tego, czy są one używane.

| JEDNOSTKA SKU ISE | Opis |
|---------|-------------|
| **Premium** | Jednostka podstawowa ma [stałą pojemność](logic-apps-limits-and-config.md#integration-service-environment-ise) i jest [rozliczana według stawki godzinowej dla jednostki SKU Premium](https://azure.microsoft.com/pricing/details/logic-apps). Jeśli potrzebujesz większej przepływności, możesz [dodać więcej jednostek skalowania](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) podczas tworzenia ISE lub później. Każda jednostka skalowania jest rozliczana według [stawki godzinowej, która jest w przybliżeniu połowa stawki jednostki bazowej](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Aby uzyskać informacje o pojemności i limitach, zobacz [limity ISE w Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Deweloper** | Jednostka podstawowa ma [stałą pojemność](logic-apps-limits-and-config.md#integration-service-environment-ise) i jest [rozliczana według stawki godzinowej dla jednostki SKU dewelopera](https://azure.microsoft.com/pricing/details/logic-apps). Jednak ta jednostka SKU nie ma umowy dotyczącej poziomu usług (SLA), skalowania w górę lub nadmiarowości podczas odtwarzania, co oznacza, że mogą wystąpić opóźnienia lub przestoje. Aktualizacje zaplecza mogą sporadycznie przerwać działanie usługi. <p><p>**Ważne**: Upewnij się, że ta jednostka SKU jest używana tylko do eksploracji, eksperymentów, programowania i testowania, a nie do testowania wydajności lub produkcji. <p><p>Aby uzyskać informacje o pojemności i limitach, zobacz [limity ISE w Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Wliczone bez dodatkowych kosztów

| Elementy | Opis |
|-------|-------------|
| [Wbudowane](../connectors/apis-list.md#built-in) wyzwalacze i akcje | Wyświetl **podstawową** etykietę i uruchom w tym samym ISE, co Aplikacje logiki. |
| [Łączniki standardowe](../connectors/apis-list.md#managed-connectors) <p><p>[Łączniki dla przedsiębiorstw](../connectors/apis-list.md#enterprise-connectors) | Łączniki zarządzane, które wyświetlają etykietę **ISE** , są specjalnie zaprojektowane do pracy bez lokalnej bramy danych i działają w tym samym ISE, co Aplikacje logiki. Cennik ISE obejmuje dowolną liczbę połączeń w przedsiębiorstwie. <p><p>— Łączniki, które nie wyświetlają etykiety ISE, są uruchamiane w usłudze Logic Apps wielodostępnej. Jednak Cennik usługi ISE obejmuje te wykonania dla aplikacji logiki, które działają w ISE. |
| Akcje wewnątrz [pętli](logic-apps-control-flow-loops.md) | Cennik ISE obejmuje każdą akcję, która jest uruchamiana w pętli dla każdego cyklu pętli, który działa. <p><p>Załóżmy na przykład, że masz pętlę "for each", która zawiera akcje, które przetwarzają listę. Aby uzyskać łączną liczbę wykonań akcji, pomnóż liczbę elementów listy z liczbą akcji w pętli i Dodaj akcję, która uruchamia pętlę. Dlatego obliczenia dla listy 10 elementów są (10 * 1) + 1, co skutkuje 11 wykonaniami akcji. |
| Liczba ponownych prób | Aby obsłużyć najbardziej podstawowe wyjątki i błędy, można skonfigurować [zasady ponawiania](logic-apps-exception-handling.md#retry-policies) dla wyzwalaczy i akcji, w których są obsługiwane. Cennik ISE obejmuje ponowną próbę wraz z pierwotnym żądaniem. |
| [Przechowywanie danych i użycie magazynu](#data-retention) | Aplikacje Logic Apps w ISE nie powodują ponoszenia kosztów przechowywania i magazynowania. |
| [Konta integracji](#integration-accounts) | Obejmuje użycie jednej warstwy konta integracji na podstawie jednostki SKU ISE bez dodatkowych kosztów. |
|||

Aby uzyskać informacje o limitach, zobacz [limity ISE w Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Konta integracji

[Konto integracji](../logic-apps/logic-apps-pricing.md#integration-accounts) to oddzielny zasób tworzony i połączony z usługą Logic Apps, dzięki czemu można eksplorować, kompilować i testować rozwiązania integracji B2B, które korzystają z możliwości [przetwarzania](logic-apps-enterprise-integration-xml.md) [EDI](logic-apps-enterprise-integration-b2b.md) i XML.

Azure Logic Apps oferują te poziomy kont integracji lub warstwy, które [różnią się w](https://azure.microsoft.com/pricing/details/logic-apps/) zależności od modelu cen i [rozliczeń](logic-apps-pricing.md#integration-accounts), w zależności od tego, czy Twoje aplikacje logiki są oparte na użyciu lub ISE:

| Warstwa | Opis |
|------|-------------|
| **Podstawowa** | W przypadku scenariuszy, w których ma być obsługiwana obsługa komunikatów lub działać jako mały partner biznesowy, który ma relację partnera handlowego z większą jednostką biznesową. <p><p>Obsługiwane przez umowę SLA Logic Apps. |
| **Standardowa** | Scenariusze, w których masz bardziej złożone relacje B2B i większą liczbę jednostek, które należy zarządzać. <p><p>Obsługiwane przez umowę SLA Logic Apps. |
| **Bezpłatna** | W przypadku scenariuszy poznawczych, a nie scenariuszy produkcyjnych. Ta warstwa ma limity dotyczące dostępności regionów, przepływności i użycia. Na przykład warstwa Bezpłatna jest dostępna tylko dla regionów publicznych na platformie Azure, na przykład zachodnie stany USA lub Południowo-Wschodnia, ale nie dla Chin lub [Azure Government](../azure-government/documentation-government-welcome.md) [platformy Azure](/azure/china/overview-operations) . <p><p>**Uwaga**: nie jest obsługiwana przez umowę SLA Logic Apps. |
|||

Aby uzyskać informacje na temat limitów kont integracji, zobacz [limity i konfiguracja dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), takich jak:

* [Limity dla kont integracji na subskrypcję platformy Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limity różnych artefaktów na konto integracji](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Artefakty obejmują partnerów handlowych, umowy, mapy, schematy, zestawy, certyfikaty, konfiguracje partii itd.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Konta integracji dla aplikacji logiki opartych na użyciu

Konta integracji są rozliczane przy użyciu ustalonej [ceny konta integracji](https://azure.microsoft.com/pricing/details/logic-apps/) opartej na używanej warstwie konta.

### <a name="ise-based-logic-apps"></a>Aplikacje logiki oparte na ISE

Bez dodatkowych kosztów ISE obejmuje pojedyncze konto integracji na podstawie jednostki SKU ISE. Aby uzyskać dodatkowe koszty, można utworzyć więcej kont integracji dla ISE, aby użyć do [całkowitego limitu ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Więcej informacji na temat [modelu cen ISE](#fixed-pricing) znajduje się wcześniej w tym temacie.

| JEDNOSTKA SKU ISE | Uwzględnione konto integracji | Dodatkowy koszt |
|---------|------------------------------|-----------------|
| **Premium** | Pojedyncze konto integracji [standardowej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Maksymalnie 19 więcej kont w warstwie Standardowa. Nie są dozwolone żadne konta bezpłatne ani podstawowe. |
| **Deweloper** | Pojedyncze konto [bezpłatnej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integracji | Maksymalnie 19 więcej kont w warstwie Standardowa, jeśli masz już bezpłatne konto lub 20 łącznych kont standardowych, jeśli nie masz bezpłatnego konta. Nie są dozwolone żadne konta podstawowe. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Przechowywanie danych i użycie magazynu

Wszystkie dane wejściowe i wyjściowe w historii uruchamiania aplikacji logiki są przechowywane i mierzone w oparciu o [czas trwania i okres przechowywania historii](logic-apps-limits-and-config.md#run-duration-retention-limits)przez tę aplikację.

* W przypadku usługi Logic Apps w usłudze Logic Apps z wieloma dzierżawcami użycie magazynu jest rozliczane według ustalonej ceny, którą można znaleźć na [stronie cennika Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps), w tabeli **szczegóły cennika** .

* W przypadku usługi Logic Apps w ISEs użycie magazynu nie wiąże się z kosztami przechowywania danych.

Aby monitorować użycie magazynu, zobacz temat [wyświetlanie metryk dotyczących wykonywania i użycia magazynu](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Lokalna brama danych

[Lokalna Brama danych](../logic-apps/logic-apps-gateway-install.md) jest osobnym tworzonym zasobem, dzięki czemu Aplikacje logiki mogą uzyskiwać dostęp do danych lokalnych przy użyciu określonych łączników obsługiwanych przez bramę. Operacje łączników, które są wykonywane za pomocą bramy, ponoszą opłaty, ale sama Brama nie wiąże się z opłatami.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Wyłączone Aplikacje logiki

Wyłączone Aplikacje logiki nie są rozliczone, ponieważ nie mogą tworzyć nowych wystąpień, gdy są one wyłączone. Po wyłączeniu aplikacji logiki wszystkie aktualnie uruchomione wystąpienia mogą upłynąć trochę czasu, zanim zostaną całkowicie zatrzymane.

## <a name="next-steps"></a>Następne kroki

* [Planowanie i zarządzanie kosztami Azure Logic Apps](plan-manage-costs.md)
