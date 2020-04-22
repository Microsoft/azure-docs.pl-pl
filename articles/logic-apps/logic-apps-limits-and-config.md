---
title: Limity i konfiguracja
description: Limity usług, takie jak czas trwania, przepływność i pojemność, a także wartości konfiguracji, takie jak adresy IP, aby zezwolić na usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: c650cfcbfeddaa83d8bf3127024ac77b93456a57
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683144"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limits and configuration information for Azure Logic Apps (Limity i informacje o konfiguracji dla usługi Azure Logic Apps)

W tym artykule opisano ograniczenia i szczegóły konfiguracji dotyczące tworzenia i uruchamiania zautomatyzowanych przepływów pracy za pomocą usługi Azure Logic Apps. Aby uzyskać informacje o automatyce zasilania, zobacz [Limity i konfiguracja w trybie Power Automatyzuj](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limity definicji

Oto limity dla pojedynczej definicji aplikacji logiki:

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcje na przepływ pracy | 500 | Aby rozszerzyć ten limit, można dodać zagnieżdżone przepływy pracy zgodnie z potrzebami. |
| Dozwolona głębokość zagnieżdżania dla akcji | 8 | Aby rozszerzyć ten limit, można dodać zagnieżdżone przepływy pracy zgodnie z potrzebami. |
| Przepływy pracy na region na subskrypcję | 1000 | |
| Wyzwalacze na przepływ pracy | 10 | Podczas pracy w widoku kodu, nie projektant |
| Limit przypadków zakresu przełączników | 25 | |
| Zmienne na przepływ pracy | 250 | |
| Znaki na wyrażenie | 8192 | |
| Maksymalny rozmiar dla`trackedProperties` | 16 000 znaków |
| Nazwa `action` lub`trigger` | 80 znaków | |
| Długość`description` | 256 znaków | |
| Maksymalna`parameters` | 50 | |
| Maksymalna`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Uruchom limity czasu trwania i przechowywania

Oto limity dla pojedynczego uruchomienia aplikacji logiki:

| Nazwa | Limit wielu dzierżawców | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Czas trwania przebiegu | 90 dni | 366 dni | Czas trwania uruchamiania jest obliczany przy użyciu czasu rozpoczęcia uruchomienia i limitu określonego *w czasie rozpoczęcia* przez ustawienie przepływu pracy, Uruchom [**retencję historii w dniach**](#change-duration). <p><p>Aby zmienić domyślny limit, który wynosi 90 dni, zobacz [zmiana czasu trwania uruchomienia](#change-duration). |
| Uruchamianie przechowywania w magazynie | 90 dni | 366 dni | Uruchom retencji jest obliczana przy użyciu czasu rozpoczęcia uruchomienia i limitu, który jest określony *w bieżącym czasie* przez ustawienie przepływu pracy, Uruchom [**retencji historii w dniach**](#change-retention). Niezależnie od tego, czy przebieg zostanie ukończony, czy limit czasu, obliczenia przechowywania zawsze używają czasu rozpoczęcia uruchomienia. Gdy czas trwania uruchomienia przekracza *bieżący* limit przechowywania, uruchom jest usuwany z historii przebiegów. <p><p>Jeśli zmienisz to ustawienie, bieżący limit jest zawsze używany do obliczania przechowywania, bez względu na poprzedni limit. Na przykład jeśli zmniejszyć limit przechowywania z 90 dni do 30 dni, uruchomienie, które jest 60 dni życia jest usuwany z historii przebiegów. Jeśli wydłużysz okres przechowywania z 30 dni do 60 dni, bieg, który ma 20 dni, pozostanie w historii przebiegów przez kolejne 40 dni. <p><p>Aby zmienić domyślny limit, który wynosi 90 dni, zobacz [zmiana przechowywania uruchamiania w magazynie](#change-retention). |
| Minimalny interwał cyklu | 1 sekunda | 1 sekunda ||
| Maksymalny interwał cyklu | 500 dni | 500 dni ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>Zmienianie czasu trwania uruchamiania i uruchamianie przechowywania w magazynie

Aby zmienić domyślny limit czasu trwania uruchamiania i uruchomić przechowywanie w magazynie, wykonaj następujące kroki. Aby zwiększyć maksymalny limit, [skontaktuj się z zespołem aplikacji logiki,](mailto://logicappsemail@microsoft.com) aby uzyskać pomoc dotyczącą wymagań.

> [!NOTE]
> W przypadku aplikacji logiki na platformie Azure z wieloma dzierżawcami domyślny limit 90 dni jest taki sam jak maksymalny limit. Tę wartość można tylko zmniejszyć.
> W przypadku aplikacji logiki w środowisku usługi integracji można zmniejszyć lub zwiększyć domyślny limit 90 dni.

1. Przejdź do [witryny Azure portal](https://portal.azure.com). W polu wyszukiwania portalu znajdź i wybierz **pozycję Aplikacje logiki**.

1. Wybierz, a następnie otwórz aplikację logiki w Logic App Designer.

1. W menu aplikacji logiki wybierz pozycję **Ustawienia przepływu pracy**.

1. W obszarze **Opcje środowiska uruchomieniego**z listy **Uruchom retencję historii w dniach** wybierz pozycję **Niestandardowe**.

1. Przeciągnij suwak, aby zmienić odpowiednią liczbę dni.

1. Po zakończeniu na pasku **narzędzi Ustawienia przepływu pracy** wybierz pozycję **Zapisz**.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limity współbieżności, pętli i debaty

Oto limity dla pojedynczego uruchomienia aplikacji logiki:

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Współbieżność wyzwalania | - Nieograniczony, gdy kontrola współbieżności jest wyłączona <p><p>- 25 jest domyślny limit, gdy włączy się formant współbieżności, którego nie można cofnąć po włączeniu współbieżności. Wartość domyślną można zmienić na wartość z 1 do 50 włącznie. | Ten limit opisuje największą liczbę wystąpień aplikacji logiki, które można uruchomić w tym samym czasie lub równolegle. <p><p>**Uwaga:** Gdy współbieżność jest włączona, limit SplitOn jest zredukowany do 100 elementów dla [dyskusyjnych tablic](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Aby zmienić domyślny limit na wartość z 1 do 50 włącznie, zobacz [Zmienianie limitu współbieżności wyzwalacza](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) lub [Wyzwalanie wystąpień sekwencyjnie](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maksymalna liczba oczekujących przebiegów | - Bez współbieżności minimalna liczba oczekujących przebiegów wynosi 1, podczas gdy maksymalna liczba wynosi 50. <p><p>- Przy współbieżności minimalna liczba oczekujących przebiegów wynosi 10 plus liczba równoczesnych przebiegów (współbieżność wyzwalacza). Możesz zmienić maksymalną liczbę do 100 włącznie. | Ten limit opisuje największą liczbę wystąpień aplikacji logiki, które mogą czekać na uruchomienie, gdy aplikacja logiki jest już uruchomiony maksymalną równoczesne wystąpienia. <p><p>Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Elementy tablicy Foreach | 100 000 | Ten limit opisuje największą liczbę elementów tablicy, które "dla każdego" pętli może przetwarzać. <p><p>Aby filtrować większe tablice, można użyć [akcji kwerendy](logic-apps-perform-data-operations.md#filter-array-action). |
| Współbieżność foreach | 20 jest domyślnym limitem, gdy formant współbieżności jest wyłączony. Wartość domyślną można zmienić na wartość z 1 do 50 włącznie. | Ten limit jest największą liczbą iteracji pętli "dla każdego", które można uruchomić w tym samym czasie lub równolegle. <p><p>Aby zmienić domyślny limit na wartość z 1 do 50 włącznie, zobacz [Zmienianie limitu współbieżności "dla każdego"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) lub [Uruchom kolejno "dla każdej" pętli](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Elementy SplitOn | - 100.000 bez współbieżności wyzwalania <p><p>- 100 z współbieżnością wyzwalacza | Dla wyzwalaczy, które zwracają tablicy, można określić wyrażenie, które używa właściwości "SplitOn", który [dzieli lub debatches elementów tablicy do wielu wystąpień przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) do przetwarzania, zamiast używać pętli "Foreach". To wyrażenie odwołuje się do tablicy używanej do tworzenia i uruchamiania wystąpienia przepływu pracy dla każdego elementu tablicy. <p><p>**Uwaga:** Gdy współbieżność jest włączona, limit SplitOn jest zredukowany do 100 elementów. |
| Iteracje Until | - Domyślnie: 60 <p><p>- Maksymalnie: 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limity przepływności

Oto limity dla pojedynczej definicji aplikacji logiki:

### <a name="multi-tenant-logic-apps-service"></a>Usługa aplikacji logiki dla wielu dzierżawców

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcja: Egzekucje na 5 minut | 100 000 jest domyślnym limitem, ale 300 000 to maksymalny limit. | Aby zmienić domyślny limit, zobacz [Uruchamianie aplikacji logiki w trybie "wysoka przepływność",](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)który znajduje się w wersji zapoznawczej. Lub można rozpowszechniać obciążenia w więcej niż jednej aplikacji logiki w razie potrzeby. |
| Akcja: Równoczesne połączenia wychodzące | ~2500 | Można zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
| Punkt końcowy środowiska uruchomieniowego: równoczesne wywołania przychodzące | ~1 000 | Można zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
| Punkt końcowy środowiska wykonawczego: odczytywanie połączeń na 5 minut  | 60 000 | W razie potrzeby można dystrybuować obciążenie między więcej niż jedną aplikacją. |
| Punkt końcowy środowiska uruchomieniowego: wywoływanie wywołań na 5 minut | 45 000 | W razie potrzeby można dystrybuować obciążenie między więcej niż jedną aplikacją. |
| Przepustowość zawartości przez 5 minut | 600 MB | W razie potrzeby można dystrybuować obciążenie między więcej niż jedną aplikacją. |
||||

### <a name="integration-service-environment-ise"></a>Środowisko usług integracji (ISE)

Oto limity przepływności dla jednostki [SKU Premium ISE:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

| Nazwa | Limit | Uwagi |
|------|-------|-------|
| Limit wykonania jednostki podstawowej | Przepustowość systemu ograniczona, gdy przepustowość infrastruktury osiągnie 80% | Zapewnia ~4000 wykonań akcji na minutę, co stanowi ~160 milionów wykonań akcji miesięcznie | |
| Limit wykonania jednostki skalowania | Przepustowość systemu ograniczona, gdy przepustowość infrastruktury osiągnie 80% | Każda jednostka skalowania może zapewnić ~2000 dodatkowych wykonań akcji na minutę, co stanowi ~80 milionów więcej wykonań akcji miesięcznie | |
| Maksymalne jednostki skali, które można dodać | 10 | |
||||

Aby przekroczyć te limity w normalnym przetwarzaniu lub uruchomić testowanie obciążenia, które mogą przekroczyć te limity, skontaktuj się z [zespołem aplikacji logiki,](mailto://logicappsemail@microsoft.com) aby uzyskać pomoc dotyczącą wymagań.

> [!NOTE]
> Jednostka [SKU dewelopera ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) nie ma opublikowanych limitów, żadnych możliwości skalowania w górę i nie ma umowy dotyczącej poziomu usług (SLA). Tej jednostki SKU należy używać tylko do eksperymentowania, tworzenia i testowania, a nie testowania produkcji lub wydajności.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limity bram

Usługa Azure Logic Apps obsługuje operacje zapisu, w tym wstawia i aktualizacje, za pośrednictwem bramy. Jednak te operacje mają [ograniczenia dotyczące ich wielkości ładunku](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>Limity HTTP

Oto limity dla pojedynczego wychodzącego lub przychodzącego połączenia HTTP:

#### <a name="timeout"></a>Limit czasu

Niektóre operacje łącznika wykonać wywołania asynchroniczne lub nasłuchiwają żądań elementu webhook, więc limit czasu dla tych operacji może być dłuższy niż te limity. Aby uzyskać więcej informacji, zobacz szczegóły techniczne dotyczące określonego łącznika, a także [wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Nazwa | Limit wielu dzierżawców | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Żądanie ruchu wychodzącego | 120 sekund <br>(2 minuty) | 240 sekund <br>(4 minuty) | Przykłady żądań wychodzących obejmują wywołania wywoływane przez wyzwalacze HTTP. <p><p>**Wskazówka:** W przypadku dłuższych operacji, użyj [asynchronii wzorca sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern) lub [pętli do do.](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action) |
| Żądanie przychodzące | 120 sekund <br>(2 minuty) | 240 sekund <br>(4 minuty) | Przykłady żądań przychodzących obejmują wywołania odebrane przez wyzwalacze żądań i wyzwalacze elementu webhook. <p><p>**Uwaga:** Aby oryginalny obiekt wywołujący, aby uzyskać odpowiedź, wszystkie kroki w odpowiedzi musi zakończyć się w ramach limitu, chyba że wywołasz inną aplikację logiki jako zagnieżdżonego przepływu pracy. Aby uzyskać więcej informacji, zobacz [Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md). |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Rozmiar komunikatu

| Nazwa | Limit wielu dzierżawców | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Rozmiar komunikatu | 100 MB | 200 MB | Złącza oznaczone etykietą ISE używają limitu ISE, a nie ich limitów złączy innych niż ISE. <p><p>Aby obejść ten limit, zobacz [Obsłużanie dużych wiadomości z fragmentami](../logic-apps/logic-apps-handle-large-messages.md). Jednak niektóre łączniki i interfejsy API mogą nie obsługiwać fragmentowania lub nawet domyślnego limitu. |
| Rozmiar wiadomości z fragmentami | 1 GB | 5 GB | Ten limit ma zastosowanie do akcji, które natywnie obsługują fragmentowanie lub umożliwiają włączanie fragmentowania w konfiguracji środowiska uruchomieniowego. <p><p>W środowisku usługi integracji aparat aplikacji logiki obsługuje ten limit, ale łączniki mają własne limity fragmentowania do limitu aparatu, na przykład zobacz [odwołanie do interfejsu API łącznika usługi Azure Blob Storage.](https://docs.microsoft.com/connectors/azureblob/) Aby uzyskać więcej informacji na temat fragmentowania, zobacz [Obsługa dużych wiadomości z fragmentami](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Limity znaków

| Nazwa | Uwagi |
|------|-------|
| Limit oceniania wyrażeń | 131 072 znaki | Wyrażenia `@concat()` `@base64()`, `@string()` nie mogą być dłuższe niż ten limit. |
| Żądanie limitu znaków adresu URL | 16 384 znaków |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Zasady ponawiania

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Liczba ponownych prób | 90 | Wartość domyślna to 4. Aby zmienić wartość domyślną, użyj [parametru zasad ponawiania próby](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maksymalna opóźnienie ponowienia próby | 1 dzień | Aby zmienić wartość domyślną, użyj [parametru zasad ponawiania próby](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimalne opóźnienie ponowienia próby | 5 sekund | Aby zmienić wartość domyślną, użyj [parametru zasad ponawiania próby](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Niestandardowe limity złączy

Oto limity dla łączników niestandardowych, które można tworzyć za pomocą internetowych interfejsów API.

| Nazwa | Limit wielu dzierżawców | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Liczba łączników niestandardowych | 1000 na subskrypcję platformy Azure | 1000 na subskrypcję platformy Azure ||
| Liczba żądań na minutę dla łącznika niestandardowego | 500 żądań na minutę na połączenie | 2000 żądań na minutę na *łącznik niestandardowy* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Tożsamości zarządzane

| Nazwa | Limit |
|------|-------|
| Zarządzane tożsamości na aplikację logiki | Tożsamość przypisana do systemu lub 1 tożsamość przypisana przez użytkownika |
| Liczba aplikacji logiki, które mają tożsamość zarządzaną w subskrypcji platformy Azure na region | 250 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity kont integracji

Każda subskrypcja platformy Azure ma następujące limity kont integracji:

* Jedno bezpłatne konto integracji [warstwy](../logic-apps/logic-apps-pricing.md#integration-accounts) w regionie platformy Azure

* 1000 kont integracji, w tym kont integracji w dowolnym [środowisku usług integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zarówno [dla deweloperów, jak i jednostek SKU Premium.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Każdy ISE, [deweloper lub Premium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)jest ograniczony do 5 kont integracji całkowitej:

  | ISE SKU | Limity kont integracji |
  |---------|----------------------------|
  | **Premium** | 5 razem — tylko konta [standardowe,](../logic-apps/logic-apps-pricing.md#integration-accounts) w tym jedno konto standardowe za darmo. Nie są dozwolone żadne konta bezpłatne lub podstawowe. |
  | **Developer** | 5 razem - [Bezpłatne](../logic-apps/logic-apps-pricing.md#integration-accounts) (ograniczone do 1 konta) i [standardowe](../logic-apps/logic-apps-pricing.md#integration-accounts) łącznie lub wszystkie konta standardowe. Nie są dozwolone żadne konta podstawowe. Użyj jednostki [SKU dewelopera](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) do eksperymentowania, tworzenia i testowania, ale nie do testowania produkcji lub wydajności. |
  |||

Dodatkowe koszty mają zastosowanie do kont integracji, które można dodać poza kont integracji, które są uwzględniane w ise. Aby dowiedzieć się, jak działają ceny i rozliczenia dla firm ISE, zobacz [model cenowy Aplikacji logiki](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limity artefaktów na konto integracji

Oto limity liczby artefaktów dla każdej warstwy kont integracji.
Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps/). Aby dowiedzieć się, jak działają ceny i rozliczenia dla kont [integracyjnych, zobacz model cenowy Aplikacji logiki](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Warstwa bezpłatna służy tylko do scenariuszy odkrywczych, a nie scenariuszy produkcyjnych. Ta warstwa ogranicza przepływność i użycie i nie ma umowy dotyczącej poziomu usług (SLA).

| Artefakt | Bezpłatna | Podstawowy | Standardowa |
|----------|------|-------|----------|
| Umowy handlowe EDI | 10 | 1 | 1000 |
| Partnerzy handlowi EDI | 25 | 2 | 1000 |
| Maps | 25 | 500 | 1000 |
| Schematy | 25 | 500 | 1000 |
| Zestawy | 10 | 25 | 1000 |
| Certyfikaty | 25 | 2 | 1000 |
| Konfiguracje wsadowe | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity pojemności artefaktów

| Artefakt | Limit | Uwagi |
| -------- | ----- | ----- |
| Zestaw | 8 MB | Aby przekazać pliki większe niż 2 MB, użyj [konta magazynu platformy Azure i kontenera obiektów blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mapa (plik XSLT) | 8 MB | Aby przekazać pliki większe niż 2 MB, użyj [interfejsu API REST aplikacji azure logic apps — Mapy](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). <p><p>**Uwaga:** Ilość danych lub rekordów, które mapa może pomyślnie przetworzyć, zależy od limitów rozmiaru wiadomości i limitu czasu akcji w usłudze Azure Logic Apps. Jeśli na przykład używana jest akcja HTTP oparta na [limitach rozmiaru i limitu limitu czasu wiadomości HTTP,](#request-limits)mapa może przetwarzać dane do limitu rozmiaru wiadomości HTTP, jeśli operacja zostanie ukończona w ramach limitu limitu czasu HTTP. |
| Schemat | 8 MB | Aby przekazać pliki większe niż 2 MB, użyj [konta magazynu platformy Azure i kontenera obiektów blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Limity przepływności

| Punkt końcowy środowiska uruchomieniowego | Bezpłatna | Podstawowy | Standardowa | Uwagi |
|------------------|------|-------|----------|-------|
| Odczytywanie połączeń przez 5 minut | 3000 | 30,000 | 60 000 | W razie potrzeby można rozdzielić obciążenie na więcej niż jedno konto. |
| Wywoływanie połączeń na 5 minut | 3000 | 30,000 | 45 000 | W razie potrzeby można rozdzielić obciążenie na więcej niż jedno konto. |
| Śledzenie połączeń na 5 minut | 3000 | 30,000 | 45 000 | W razie potrzeby można rozdzielić obciążenie na więcej niż jedno konto. |
| Blokowanie równoczesnych połączeń | ~1 000 | ~1 000 | ~1 000 | To samo dotyczy wszystkich jednostek SKU. Można zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania w razie potrzeby. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Rozmiar komunikatu protokołu B2B (AS2, X12, EDIFACT)

Oto limity rozmiaru wiadomości, które mają zastosowanie do protokołów B2B:

| Nazwa | Limit wielu dzierżawców | Limit środowiska usług integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 50 MB | v2 - 200 MB <br>v1 - 50 MB | Dotyczy dekodowania i kodowania |
| X12 | 50 MB | 50 MB | Dotyczy dekodowania i kodowania |
| EDIFACT | 50 MB | 50 MB | Dotyczy dekodowania i kodowania |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Wyłączanie lub usuwanie aplikacji logiki

Po wyłączeniu aplikacji logiki nie zostaną utworzyć żadnych nowych uruchomień. Wszystkie przebiegi w toku i oczekujące są kontynuowane do czasu ich zakończenia, co może zająć trochę czasu.

Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Konfiguracja zapory: adresy IP i tagi usług

Adresy IP używane przez usługę Azure Logic Apps dla wywołań przychodzących i wychodzących zależą od regionu, w którym istnieje aplikacja logiki. *Wszystkie* aplikacje logiki w tym samym regionie używają tych samych zakresów adresów IP. Niektóre wywołania [automatyzacji zasilania,](https://docs.microsoft.com/power-automate/getting-started) takie jak żądania **HTTP** i **HTTP + OpenAPI,** przechodzą bezpośrednio za pośrednictwem usługi Azure Logic Apps i pochodzą z adresów IP, które są wymienione w tym miejscu. Aby uzyskać więcej informacji na temat adresów IP używanych przez program Power Automate, zobacz [Limity i konfiguracja w programie Power Automate](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

> [!TIP]
> Aby zmniejszyć złożoność podczas tworzenia reguł zabezpieczeń, można opcjonalnie użyć [tagów usługi](../virtual-network/service-tags-overview.md), zamiast określać adresy IP aplikacji logiki dla każdego regionu, opisane w dalszej części tej sekcji. Te tagi działają w regionach, w których dostępna jest usługa Logic Apps:
>
> * **LogicAppsManagement:** Reprezentuje przychodzące prefiksy adresów IP dla usługi Aplikacje logiki.
> * **LogicApps:** Reprezentuje wychodzące prefiksy adresów IP dla usługi Aplikacje logiki.

* W przypadku [platformy Azure China 21Vianet](https://docs.microsoft.com/azure/china/)stałe lub zastrzeżone adresy IP są niedostępne dla [łączników niestandardowych](../logic-apps/custom-connector-overview.md) i [łączników zarządzanych](../connectors/apis-list.md#managed-api-connectors), na przykład usługi Azure Storage, SQL Server, Office 365 Outlook i tak dalej.

* Aby obsługiwać wywołania, które aplikacje logiki bezpośrednio nawiązują za pomocą [protokołu HTTP,](../connectors/connectors-native-http.md) [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)i innych żądań HTTP, skonfiguruj zaporę z [wszystkimi przychodzącymi](#inbound) *i* [wychodzącymi](#outbound) adresami IP używanymi przez usługę Logic Apps, w zależności od regionów, w których istnieją aplikacje logiki. Adresy te są wyświetlane w nagłówkach **przychodzących** i **wychodzących** w tej sekcji i są sortowane według regionu.

* Aby obsługiwać wywołania, które tworzą [łączniki zarządzane,](../connectors/apis-list.md#managed-api-connectors) skonfiguruj zaporę ze *wszystkimi* [wychodzącymi](#outbound) adresami IP używanymi przez te łączniki na podstawie regionów, w których istnieją aplikacje logiki. Adresy te są wyświetlane w nagłówku **Wychodzącym** w tej sekcji i są sortowane według regionu.

* Aby włączyć komunikację dla aplikacji logiki, które działają w środowisku usługi integracji (ISE), upewnij się, że [te porty są otwarte.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* Jeśli aplikacje logiki mają problemy z dostępem do kont magazynu platformy Azure, które używają [zapór i reguł zapory,](../storage/common/storage-network-security.md)masz [różne opcje, aby włączyć dostęp](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Na przykład aplikacje logiki nie mogą bezpośrednio uzyskiwać dostępu do kont magazynu, które używają reguł zapory i istnieją w tym samym regionie. Jeśli jednak zezwolisz [na wychodzące adresy IP dla łączników zarządzanych w twoim regionie,](../logic-apps/logic-apps-limits-and-config.md#outbound)aplikacje logiki mogą uzyskiwać dostęp do kont magazynu, które znajdują się w innym regionie, z wyjątkiem przypadków, gdy używasz łączników usługi Azure Table Storage lub Azure Queue Storage. Aby uzyskać dostęp do magazynu tabel lub magazynu kolejek, można użyć wyzwalacza HTTP i akcji. Aby uzyskać inne opcje, zobacz [Konta magazynu programu Access za zaporami](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Przychodzące adresy IP

W tej sekcji wymieniono tylko przychodzące adresy IP dla usługi Azure Logic Apps. Jeśli masz platformę Azure Government, zobacz [Azure Government — przychodzące adresy IP](#azure-government-inbound).

> [!TIP]
> Aby zmniejszyć złożoność podczas tworzenia reguł zabezpieczeń, można opcjonalnie użyć [tagu usługi](../virtual-network/service-tags-overview.md), **LogicAppsManagement**, zamiast określać przychodzące prefiksy adresów IP aplikacji logiki dla każdego regionu. Ten tag działa w regionach, w których dostępna jest usługa Logic Apps.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Platforma Azure dla wielu dzierżaw — przychodzące adresy IP

| Region wielodostępny | Adres IP |
|---------------------|----|
| Australia Wschodnia | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australia Południowo-Wschodnia | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazylia Południowa | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Kanada Środkowa | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada Wschodnia | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indie Środkowe | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Środkowe stany USA | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Azja Wschodnia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| Wschodnie stany USA | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| Wschodnie stany USA 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Francja Środkowa | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Francja Południowa | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Japonia Wschodnia | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japonia Zachodnia | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea Środkowa | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea Południowa | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Północno-środkowe stany USA | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa Północna | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Republika Południowej Afryki Północ | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Republika Południowej Afryki Zachód | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| Południowo-środkowe stany USA | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie Południowe | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Azja Południowo-Wschodnia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emirat | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Południowe Zjednoczone Królestwo | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Zachodnie Zjednoczone Królestwo | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| Zachodnio-środkowe stany USA | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa Zachodnia | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| Indie Zachodnie | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| Zachodnie stany USA | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| Zachodnie stany USA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government — przychodzące adresy IP

| Region azure dla instytucji rządowych | Adres IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Teksas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov Wirginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD (region środkowy) | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Wychodzące adresy IP

W tej sekcji wymieniono wychodzące adresy IP dla usługi Azure Logic Apps i łączników zarządzanych. Jeśli masz platformę Azure Government, zobacz [Azure Government — Wychodzące adresy IP](#azure-government-outbound).

> [!TIP]
> Aby zmniejszyć złożoność podczas tworzenia reguł zabezpieczeń, można opcjonalnie użyć [tagu usługi](../virtual-network/service-tags-overview.md), **LogicApps**, zamiast określać wychodzące prefiksy adresów IP aplikacji logiki dla każdego regionu. Ten tag działa w regionach, w których dostępna jest usługa Logic Apps. W przypadku łączników zarządzanych należy nadal używać adresów IP.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Platforma Azure z wieloma dzierżawami — wychodzące adresy IP

| Region wielodostępny | Adres IP aplikacji logiki | Adres IP łączników zarządzanych |
|---------------------|---------------|-----------------------|
| Australia Wschodnia | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72 |
| Australia Południowo-Wschodnia | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202 |
| Brazylia Południowa | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157 |
| Kanada Środkowa | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.31.197, 52.237.24.126, 52.237.32.212 |
| Kanada Wschodnia | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112 |
| Indie Środkowe | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129 |
| Środkowe stany USA | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27 |
| Azja Wschodnia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131 |
| Wschodnie stany USA | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 40.114.40.132, 40.71.249.139 |
| Wschodnie stany USA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144 |
| Francja Środkowa | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239 |
| Francja Południowa | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154 |
| Japonia Wschodnia | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230 |
| Japonia Zachodnia | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24 |
| Korea Środkowa | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104 |
| Korea Południowa | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173 |
| Północno-środkowe stany USA | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4 |
| Europa Północna | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181 |
| Republika Południowej Afryki Północ | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.168.167 |
| Republika Południowej Afryki Zachód | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.72.85 |
| Południowo-środkowe stany USA | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92 |
| Indie Południowe | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Azja Południowo-Wschodnia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69 |
| Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emirat | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.28, 20.45.67.45, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Południowe Zjednoczone Królestwo | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150 |
| Zachodnie Zjednoczone Królestwo | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185 |
| Zachodnio-środkowe stany USA | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 13.78.132.82, 52.161.101.204 |
| Europa Zachodnia | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89 |
| Indie Zachodnie | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124 |
| Zachodnie stany USA | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62 |
| Zachodnie stany USA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 52.191.164.250 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government — wychodzące adresy IP

| Region | Adres IP aplikacji logiki | Adres IP łączników zarządzanych |
|--------|---------------|-----------------------|
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91 |
| US Gov Teksas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225 |
| US Gov Wirginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
| US DoD (region środkowy) | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136 |
||||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Dowiedz się więcej o [typowych przykładach i scenariuszach](../logic-apps/logic-apps-examples-and-scenarios.md)
