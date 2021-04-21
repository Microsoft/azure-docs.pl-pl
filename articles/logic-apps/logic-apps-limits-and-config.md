---
title: Limity i konfiguracja
description: Limity usług, takie jak czas trwania, przepływność i pojemność, a także wartości konfiguracji, takie jak adresy IP, które mają być Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/16/2021
ms.openlocfilehash: 286da1412e8a74ffbf34e4abb493241914d4f925
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764877"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limits and configuration information for Azure Logic Apps (Limity i informacje o konfiguracji dla usługi Azure Logic Apps)

W tym artykule opisano limity i szczegóły konfiguracji dotyczące tworzenia i uruchamiania zautomatyzowanych przepływów pracy za Azure Logic Apps. Aby uzyskać Power Automate temat [Limits and configuration in Power Automate (Limity i konfiguracja w programie Power Automate](/flow/limits-and-config)).

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Limity definicji aplikacji logiki

Poniżej podano limity dla jednej definicji aplikacji logiki:

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcje na przepływ pracy | 500 | Aby rozszerzyć ten limit, można w razie potrzeby dodać zagnieżdżone przepływy pracy. |
| Dozwolona głębokość zagnieżdżania dla akcji | 8 | Aby rozszerzyć ten limit, można w razie potrzeby dodać zagnieżdżone przepływy pracy. |
| Przepływy pracy na region na subskrypcję | 1000 | |
| Wyzwalacze na przepływ pracy | 10 | Podczas pracy w widoku kodu, a nie w projektancie |
| Limit przypadków przełączania zakresu | 25 | |
| Zmienne na przepływ pracy | 250 | |
| Nazwa `action` dla lub `trigger` | 80 znaków | |
| Znaki na wyrażenie | 8192 | |
| Długość `description` | 256 znaków | |
| Maksymalna liczba `parameters` | 50 | |
| Maksymalna liczba `outputs` | 10 | |
| Maksymalny rozmiar dla `trackedProperties` | 16 000 znaków |
| Akcja kodu wbudowanego — maksymalna liczba znaków kodu | 1024 znaki | Aby rozszerzyć ten limit do 100 000 znaków, utwórz aplikacje logiki z typem zasobu Aplikacja logiki **(wersja zapoznawcza)** przy użyciu usługi [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) lub przy użyciu programu Visual Studio Code i [rozszerzenia Azure Logic Apps **(wersja**](create-stateful-stateless-workflows-visual-studio-code.md)zapoznawcza). |
| Akcja kodu wbudowanego — maksymalny czas trwania uruchamiania kodu | 5 sekund | Aby rozszerzyć ten limit do 15 sekund, utwórz aplikacje logiki z typem zasobu Aplikacja logiki **(wersja zapoznawcza)** przy użyciu usługi Azure Portal lub przy użyciu programu [Visual Studio Code](create-stateful-stateless-workflows-azure-portal.md) i rozszerzenia [Azure Logic Apps **(wersja**](create-stateful-stateless-workflows-visual-studio-code.md)zapoznawcza). |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Limity czasu trwania i historii przechowywania przebiegów

Poniżej podano limity dla pojedynczego uruchomienia aplikacji logiki:

| Nazwa | Limit wielu dzierżaw | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Czas trwania przebiegu | 90 dni | 366 dni | Czas trwania uruchomienia jest obliczany przy użyciu czasu rozpoczęcia uruchomienia i limitu określonego w ustawieniu przepływu pracy [**Przechowywanie**](#change-duration) historii przebiegów w dniach w tym czasie rozpoczęcia. <p><p>Aby zmienić domyślny limit, zobacz [Zmienianie czasu trwania uruchamiania i przechowywania historii w magazynie](#change-duration). |
| Przechowywanie historii uruchamiania w magazynie | 90 dni | 366 dni | Jeśli czas trwania uruchomienia przekracza bieżący limit przechowywania historii przebiegów, przebieg zostanie usunięty z historii przebiegów w magazynie. Bez względu na to, czy przebieg zostanie ukończony, czy przekroczy limit czasu, okres przechowywania historii uruchamiania jest zawsze obliczany przy użyciu czasu rozpoczęcia uruchomienia i bieżącego limitu określonego w ustawieniu przepływu pracy Przechowywanie historii przebiegów w [**dniach**](#change-retention). Niezależnie od poprzedniego limitu bieżący limit jest zawsze używany do obliczania okresu przechowywania. <p><p>Aby zmienić domyślny limit i uzyskać więcej informacji, zobacz Zmienianie czasu trwania i [przechowywania historii uruchamiania w magazynie](#change-retention). Aby zwiększyć maksymalny limit, [skontaktuj się z zespołem Logic Apps,](mailto://logicappspm@microsoft.com) aby uzyskać pomoc w zakresie wymagań. |
| Minimalny interwał cyklu | 1 sekunda | 1 sekunda ||
| Maksymalny interwał cyklu | 500 dni | 500 dni ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Zmienianie czasu trwania uruchamiania i przechowywania historii w magazynie

To samo ustawienie określa maksymalną liczbę dni, przez które przepływ pracy może działać, oraz przechowywanie historii uruchamiania w magazynie. Aby zmienić domyślny lub bieżący limit dla tych właściwości, wykonaj następujące kroki.

* W przypadku aplikacji logiki na wielodostępnych platformach Azure domyślny limit 90 dni jest taki sam jak limit maksymalny. Tę wartość można zmniejszyć tylko.

* W przypadku aplikacji logiki w środowisku usługi integracji można zmniejszyć lub zwiększyć domyślny limit 90 dni.

Załóżmy na przykład, że zmniejszasz limit przechowywania z 90 dni do 30 dni. 60-dniowy przebieg zostanie usunięty z historii przebiegów. Jeśli zwiększysz okres przechowywania z 30 dni do 60 dni, 20-dniowy przebieg pozostanie w historii przebiegów przez kolejne 40 dni.

> [!IMPORTANT]
> Jeśli czas trwania uruchomienia przekracza bieżący limit przechowywania historii uruchamiania, przebieg zostanie usunięty z historii przebiegów w magazynie. Aby uniknąć utraty historii uruchamiania, upewnij  się, że limit przechowywania jest zawsze dłuższy niż najdłuższy możliwy czas trwania uruchomienia.

1. W polu [Azure Portal](https://portal.azure.com) wyszukiwania znajdź i wybierz pozycję **Aplikacje logiki.**

1. Znajdź i wybierz aplikację logiki. Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki wybierz pozycję **Ustawienia przepływu pracy.**

1. W **obszarze Opcje środowiska uruchomieniowego** z listy Przechowywanie historii uruchamiania w **dniach** wybierz pozycję **Niestandardowy.**

1. Przeciągnij suwak, aby zmienić liczbę dni, przez które chcesz.

1. Gdy wszystko będzie gotowe, na pasku narzędzi Ustawienia **przepływu** pracy wybierz pozycję **Zapisz.**

Jeśli wygenerujesz szablon Azure Resource Manager dla aplikacji logiki, to ustawienie będzie wyświetlane jako właściwość w definicji zasobu przepływu pracy, co opisano w informacji o szablonie przepływów pracy [Microsoft.Logic:](/azure/templates/microsoft.logic/workflows)

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limity współbieżności, pętli i chybienia

Poniżej podano limity dla pojedynczego uruchomienia aplikacji logiki:

### <a name="loops"></a>Pętle

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Elementy tablicy Foreach | 100 000 | Ten limit opisuje maksymalną liczbę elementów tablicy, które mogą być przetwarzane w pętli "for each". <p><p>Aby filtrować większe tablice, możesz użyć [akcji zapytania](logic-apps-perform-data-operations.md#filter-array-action). |
| Współbieżność Foreach | Przy wyłączonej współbieżności: 20 <p><p>Ze współbieżnością na: <p><p>— Wartość domyślna: 20 <br>- Min. 1 <br>— Maks.: 50 | Ten limit to maksymalna liczba iteracji pętli "dla każdej", które mogą być uruchamiane w tym samym czasie lub równolegle. <p><p>Aby zmienić ten limit, zobacz Zmiana limitu współbieżności ["dla każdego"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) lub Sekwencyjnie uruchom pętle ["for each".](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each) |
| Iteracje Until | — Wartość domyślna: 60 <br>- Min. 1 <br>— Maks.: 5000 | Maksymalna liczba cykli, które może mieć pętla "Until" podczas uruchamiania aplikacji logiki. <p><p>Aby zmienić ten limit, w kształcie pętli "Until" wybierz pozycję **Zmień** limity i określ wartość właściwości **Count.** |
| Do momentu przeoczania limitu czasu | - Ustawienie domyślne: PT1H (1 godzina) | Jak najwięcej czasu może działać pętla "Until" przed zamknięciem i jest określona w [formacie ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601) Wartość limitu czasu jest oceniana dla każdego cyklu pętli. Jeśli jakakolwiek akcja w pętli trwa dłużej niż limit czasu, bieżący cykl nie zostanie zatrzymany. Jednak następny cykl nie rozpoczyna się, ponieważ warunek limitu nie jest spełniony. <p><p>Aby zmienić ten limit, w kształcie pętli "Until" wybierz pozycję **Zmień** limity i określ wartość właściwości **Limit** czasu. |
||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Współbieżność i współbieżność

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Współbieżność wyzwalacza | Przy wyłączonej współbieżności: Nieograniczone <p><p>Przy użyciu współbieżności, której nie można cofnąć po włączeniu: <p><p>— Wartość domyślna: 25 <br>- Min. 1 <br>— Maks.: 100 | Ten limit to maksymalna liczba wystąpień aplikacji logiki, które mogą być uruchamiane jednocześnie lub równolegle. <p><p>**Uwaga:** Po włączeniu współbieżności limit SplitOn jest zmniejszany do 100 elementów dla [tablic dzielenia na elementy](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Aby zmienić ten limit, zobacz Sekwencyjne zmienianie limitu [współbieżności](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) wyzwalacza lub [Sekwencyjne wystąpienia wyzwalacza](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maksymalna liczba przebiegów oczekiwania | Przy wyłączonej współbieżności: <p><p>- Min. 1 <br>— Maks.: 50 <p><p>Ze współbieżnością na: <p><p>- Minimum: 10 plus liczba współbieżnych przebiegów (współbieżność wyzwalacza) <br>- Maksymalna: 100 | Ten limit to maksymalna liczba wystąpień aplikacji logiki, które mogą czekać na uruchomienie, gdy aplikacja logiki już uruchamia maksymalną liczbę współbieżnych wystąpień. <p><p>Aby zmienić ten limit, zobacz [Zmienianie limitu przebiegów oczekiwania](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Elementy SplitOn | Przy wyłączonej współbieżności: 100 000 <p><p>Ze współbieżnością na: 100 | W przypadku wyzwalaczy, które zwracają tablicę, można określić wyrażenie, [](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) które używa właściwości "SplitOn", która dzieli lub rozdziela elementy tablicy na wiele wystąpień przepływu pracy do przetwarzania, zamiast używać pętli "Foreach". To wyrażenie odwołuje się do tablicy w celu utworzenia i uruchomienia wystąpienia przepływu pracy dla każdego elementu tablicy. <p><p>**Uwaga:** po włączeniu współbieżności limit SplitOn jest zmniejszany do 100 elementów. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limity przepływności

Poniżej podano limity dla pojedynczej definicji aplikacji logiki:

### <a name="multi-tenant-logic-apps-service"></a>Usługa wielodostępna Logic Apps dzierżawy

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Akcja: Wykonania na 5-minutowy interwał stopniowy | - 100 000 wykonań (ustawienie domyślne) <p><p>— 300 000 wykonań (maksymalnie w trybie wysokiej przepływności)  | Aby podnieść domyślny limit do maksymalnego limitu dla aplikacji logiki, zobacz [Uruchamianie](#run-high-throughput-mode)w trybie wysokiej przepływności , który jest w wersji zapoznawczej. Możesz też w razie [potrzeby rozłożyć obciążenie](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) na więcej niż jedną aplikację logiki. |
| Akcja: Współbieżne wywołania wychodzące | ~2500 | W razie potrzeby możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania. |
| Punkt końcowy środowiska uruchomieniowego: Równoczesne wywołania przychodzące | Około 1000 | W razie potrzeby możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania. |
| Punkt końcowy środowiska uruchomieniowego: odczyt wywołań na 5 minut  | 60 000 | Ten limit dotyczy wywołań, które odbierają nieprzetworzone dane wejściowe i wyjściowe z historii uruchamiania aplikacji logiki. W razie potrzeby można rozłożyć obciążenie na więcej niż jedną aplikację. |
| Punkt końcowy środowiska uruchomieniowego: wywołaj wywołania na 5 minut | 45 000 | W razie potrzeby można dystrybuować obciążenie między więcej niż jedną aplikacją. |
| Przepływność zawartości na 5 minut | 600 MB | W razie potrzeby można dystrybuować obciążenie między więcej niż jedną aplikacją. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Uruchamianie w trybie wysokiej przepływności

W przypadku pojedynczej definicji aplikacji logiki liczba akcji wykonywanych co 5 minut ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Aby zwiększyć domyślny limit [](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) do maksymalnego limitu dla aplikacji logiki, czyli trzykrotnie więcej niż limit domyślny, możesz włączyć tryb wysokiej przepływności, który jest w wersji zapoznawczej. Możesz też w razie [potrzeby rozłożyć obciążenie na więcej niż jedną aplikację logiki.](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling)

1. W menu Azure Portal logiki w obszarze Ustawienia **wybierz** pozycję Ustawienia **przepływu pracy.**

1. W **obszarze Opcje środowiska**  >  **uruchomieniowego Wysoka przepływność** zmień ustawienie na **Wł.**.

   ![Zrzut ekranu przedstawiający menu aplikacji logiki w Azure Portal z opcjami "Ustawienia przepływu pracy" i "Wysoka przepływność" ustawionymi na "Wł.".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Aby włączyć to ustawienie w szablonie usługi ARM do wdrażania aplikacji logiki, w obiekcie definicji zasobu aplikacji logiki dodaj obiekt z właściwością `properties` `runtimeConfiguration` `operationOptions` ustawioną na `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Aby uzyskać więcej informacji na temat definicji zasobu aplikacji logiki, zobacz Omówienie: automatyzowanie wdrażania aplikacji [Azure Logic Apps użyciu szablonów Azure Resource Manager logiki.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)

### <a name="integration-service-environment-ise"></a>Środowisko usługi integracji (ISE)

* [Deweloper ise SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)zapewnia do 500 wykonań na minutę, ale należy pamiętać o tych zagadnieniach:

  * Upewnij się, że używasz tej sku tylko do eksploracji, eksperymentów, testowania lub testowania — nie do testowania produkcyjnego lub wydajnościowego. Ta sku nie ma umowy dotyczącej poziomu usług (SLA), możliwości skalowania w górę ani nadmiarowości podczas przetwarzania, co oznacza, że mogą wystąpić opóźnienia lub przestoje.

  * Aktualizacje zaplecza mogą sporadycznie przerywać działanie usługi.

* [Premium ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)w poniższej tabeli opisano limity przepływności tej jednostki SKU, ale aby [](mailto://logicappsemail@microsoft.com) przekroczyć te limity podczas normalnego przetwarzania lub uruchomić testy obciążeniowe, które mogą przekroczyć te limity, skontaktuj się z zespołem Logic Apps, aby uzyskać pomoc w zakresie wymagań.

  | Nazwa | Limit | Uwagi |
  |------|-------|-------|
  | Limit wykonywania jednostek podstawowych | Ograniczanie przepustowości systemu, gdy pojemność infrastruktury osiągnie 80% | Zapewnia około 4000 wykonań akcji na minutę, czyli około 160 milionów wykonań akcji miesięcznie |
  | Limit wykonywania jednostek skalowania | Ograniczanie przepustowości systemu, gdy pojemność infrastruktury osiągnie 80% | Każda jednostka skalowania może zapewnić około 2000 dodatkowych wykonań akcji na minutę, czyli około 80 milionów kolejnych wykonań akcji miesięcznie |
  | Maksymalna liczba jednostek skalowania, które można dodać | 10 | |
  ||||

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limity bramy

Azure Logic Apps obsługuje operacje zapisu, w tym wstawiania i aktualizacji, za pośrednictwem bramy. Jednak te operacje mają [limity dotyczące rozmiaru ładunku.](/data-integration/gateway/service-gateway-onprem#considerations)

<a name="http-limits"></a>

## <a name="http-limits"></a>Limity protokołu HTTP

Poniżej podano limity dla pojedynczego wywołania przychodzącego lub wychodzącego:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Czas trwania limitu czasu

Niektóre operacje łącznika nasłuchują żądań typu webhook lub nasłuchują wywołań asynchronicznych, więc limit czasu dla tych operacji może być dłuższy niż te limity. Aby uzyskać więcej informacji, zobacz szczegóły techniczne określonego łącznika, a także [wyzwalacze i akcje przepływu pracy.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

| Nazwa | Logic Apps (wielodostępna) | Logic Apps (wersja zapoznawcza) | Środowisko usługi integracji | Uwagi |
|------|---------------------------|----------------------|---------------------------------|-------|
| Żądanie wychodzące | 120 sekund <br>(2 min) | 230 sekund <br>(3,9 min) | 240 sekund <br>(4 min) | Przykłady żądań wychodzących obejmują wywołania wykonane przez wyzwalacz LUB akcję HTTP. Aby uzyskać więcej informacji na temat wersji zapoznawczej, zobacz [Azure Logic Apps Zapoznawcza.](logic-apps-overview-preview.md) <p><p>**Porada:** W przypadku dłuższych operacji użyj wzorca [asynchronicznego sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern) lub pętli [until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Aby omiąć limity czasu w przypadku wywołania innej aplikacji logiki, która ma wywoływalny punkt [końcowy,](logic-apps-http-endpoint.md)możesz zamiast tego użyć wbudowanej akcji Azure Logic Apps, którą można znaleźć w s wyboru łącznika w obszarze Wbudowany **.** |
| Żądanie przychodzące | 120 sekund <br>(2 min) | 230 sekund <br>(3,9 min) | 240 sekund <br>(4 min) | Przykłady żądań przychodzących obejmują wywołania odebrane przez wyzwalacz żądania, wyzwalacz http webhook i akcję http webhook. Aby uzyskać więcej informacji na temat wersji zapoznawczej, zobacz [Azure Logic Apps Zapoznawcza.](logic-apps-overview-preview.md) <p><p>**Uwaga:** Aby oryginalny obiekt wywołujący uzyskać odpowiedź, wszystkie kroki w odpowiedzi muszą zakończyć się w ramach limitu, chyba że wywołasz inną aplikację logiki jako zagnieżdżony przepływ pracy. Aby uzyskać więcej informacji, zobacz [Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki.](../logic-apps/logic-apps-http-endpoint.md) |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Rozmiar komunikatu

| Nazwa | Limit wielu dzierżaw | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Rozmiar komunikatu | 100 MB | 200 MB | Aby ominął ten limit, zobacz [Handle large messages with chunking (Obsługa dużych komunikatów z fragmentami).](../logic-apps/logic-apps-handle-large-messages.md) Jednak niektóre łączniki i interfejsy API mogą nie obsługiwać fragmentowania, a nawet domyślnego limitu. <p><p>- Łączniki, takie jak AS2, X12 i EDIFACT, mają własne limity [komunikatów B2B.](#b2b-protocol-limits) <br>— Łączniki ISE używają limitu ise, a nie limitów łączników innych niż ise. |
| Rozmiar komunikatu z fragmentami | 1 GB | 5 GB | Ten limit dotyczy akcji, które natywnie obsługują fragmentowanie lub umożliwiają fragmentowanie w konfiguracji środowiska uruchomieniowego. <p><p>Jeśli używasz platformy ISE, aparat usługi Logic Apps obsługuje ten limit, ale łączniki mają własne limity fragmentowania do limitu aparatu, na przykład zobacz odwołanie do interfejsu [API](/connectors/azureblob/)łącznika Azure Blob Storage. Aby uzyskać więcej informacji na temat fragmentowania, zobacz [Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md)(Obsługa dużych komunikatów z fragmentami). |
|||||

#### <a name="character-limits"></a>Limity znaków

| Nazwa | Limit | Uwagi |
|------|-------|-------|
| Limit oceniania wyrażeń | 131 072 znaki | Wyrażenia `@concat()` `@base64()` , , nie mogą być dłuższe niż `@string()` ten limit. |
| Limit znaków adresu URL żądania | 16 384 znaki | |
||||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Zasady ponawiania

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Liczba ponownych prób | 90 | Wartość domyślna to 4. Aby zmienić wartość domyślną, użyj [parametru zasad ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maksymalna opóźnienie ponowienia próby | 1 dzień | Aby zmienić wartość domyślną, użyj [parametru zasad ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimalne opóźnienie ponowienia próby | 5 sekund | Aby zmienić wartość domyślną, użyj [parametru zasad ponawiania](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Limity uwierzytelniania

Poniżej podano limity dla aplikacji logiki, która rozpoczyna się od wyzwalacza żądania i umożliwia usłudze [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) autoryzację wywołań przychodzących do wyzwalacza żądania:

| Nazwa | Limit | Uwagi |
| ---- | ----- | ----- |
| Zasady autoryzacji usługi Azure AD | 5 | |
| Oświadczenia na zasady autoryzacji | 10 | |
| Wartość oświadczenia — maksymalna liczba znaków | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limity łącznika niestandardowego

Poniżej znajdują się limity łączników niestandardowych, które można tworzyć za pomocą internetowych interfejsów API.

| Nazwa | Limit wielu dzierżaw | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| Liczba łączników niestandardowych | 1000 na subskrypcję platformy Azure | 1000 na subskrypcję platformy Azure ||
| Liczba żądań na minutę dla łącznika niestandardowego | 500 żądań na minutę na połączenie | 2000 żądań na minutę na łącznik *niestandardowy* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Tożsamości zarządzane

| Nazwa | Limit |
|------|-------|
| Tożsamości zarządzane na aplikację logiki | Tożsamość przypisana przez system lub 1 tożsamość przypisana przez użytkownika |
| Liczba aplikacji logiki, które mają tożsamość zarządzaną w subskrypcji platformy Azure na region | 1000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity konta integracji

Każda subskrypcja platformy Azure ma następujące limity konta integracji:

* Jedno [konto integracji w](../logic-apps/logic-apps-pricing.md#integration-accounts) warstwie Bezpłatna na region platformy Azure. Ta warstwa jest dostępna tylko w regionach publicznych na platformie Azure, na przykład w regionach Zachodnie stany USA lub Azja Południowo-Wschodnia, ale nie dla [regionów Azure (Chiny) — 21Vianet](/azure/china/overview-operations) lub [Azure Government](../azure-government/documentation-government-welcome.md).

* 1000 łącznie kont integracji, w tym kont integracji we wszystkich środowiskach usługi integracji [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) w ramach zarówno deweloperskich, jak i [premium jednostki SKU.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Każda usługa ISE, zarówno deweloperska, jak i [Premium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)może korzystać z jednego konta integracji bez dodatkowych kosztów, chociaż uwzględniony typ konta różni się w zależności od wersji SKU środowiska ISE. Możesz utworzyć więcej kont integracji dla środowiska ISE do całkowitego limitu dla [dodatkowego kosztu:](logic-apps-pricing.md#fixed-pricing)

  | ISE SKU | Limity konta integracji |
  |---------|----------------------------|
  | **Premium** | 20 wszystkich kont, w tym jedno konto standardowe bez dodatkowych kosztów. W przypadku tej wersji SKU można mieć tylko [konta standardowe.](../logic-apps/logic-apps-pricing.md#integration-accounts) Konta bezpłatne i podstawowe nie są dozwolone. |
  | **Deweloper** | 20 wszystkich kont, w tym jedno [bezpłatne](../logic-apps/logic-apps-pricing.md#integration-accounts) konto (ograniczone do 1). Ta sku może mieć jedną kombinację: <p>— Bezpłatne konto i maksymalnie 19 [kont standardowych.](../logic-apps/logic-apps-pricing.md#integration-accounts) <br>— Brak bezpłatnego konta i maksymalnie 20 kont standardowych. <p>Nie są dozwolone żadne podstawowe ani dodatkowe bezpłatne konta. <p><p>**Ważne:** użyj [sku dewelopera do](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) eksperymentowania, tworzenia i testowania, ale nie do testowania produkcyjnego lub wydajnościowego. |
  |||

Aby dowiedzieć się, jak działają ceny i rozliczenia dla platform ISE, zobacz [Logic Apps cennika](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać informacje o cenach, [zobacz Logic Apps cennika.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limity artefaktów na konto integracji

Poniżej znajdują się limity liczby artefaktów dla każdej warstwy konta integracji. Aby uzyskać informacje o cenach, [zobacz Logic Apps cennika.](https://azure.microsoft.com/pricing/details/logic-apps/) Aby dowiedzieć się, jak działają ceny i rozliczenia dla kont integracji, zobacz [Logic Apps cennika](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Warstwy Bezpłatna należy używać tylko w scenariuszach eksploracyjne, a nie w scenariuszach produkcyjnych. Ta warstwa ogranicza przepływność i użycie i nie ma umowy dotyczącej poziomu usług (SLA).

| Artefakt | Bezpłatna | Podstawowa | Standardowa (Standard) |
|----------|------|-------|----------|
| Umowy handlowe EDI | 10 | 1 | 1000 |
| Partnerzy handlowi EDI | 25 | 2 | 1000 |
| Maps | 25 | 500 | 1000 |
| Schematy | 25 | 500 | 1000 |
| Zestawy | 10 | 25 | 1000 |
| Certyfikaty | 25 | 2 | 1000 |
| Konfiguracje usługi Batch | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity pojemności artefaktów

| Artefakt | Limit | Uwagi |
| -------- | ----- | ----- |
| Zestaw | 8 MB | Aby przekazać pliki większe niż 2 MB, użyj konta [usługi Azure Storage i kontenera obiektów blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Map (plik XSLT) | 8 MB | Aby przekazać pliki większe niż 2 MB, użyj interfejsu API REST usługi [Azure Logic Apps Maps.](/rest/api/logic/maps/createorupdate) <p><p>**Uwaga:** Ilość danych lub rekordów, które mapa może pomyślnie przetworzyć, zależy od rozmiaru komunikatu i limitów czasu akcji w Azure Logic Apps. Jeśli na przykład używasz akcji HTTP na podstawie limitu czasu i rozmiaru komunikatu [HTTP,](#http-limits)mapa może przetwarzać dane do limitu rozmiaru komunikatu HTTP, jeśli operacja zostanie ukończona w ramach limitu czasu HTTP. |
| Schemat | 8 MB | Aby przekazać pliki większe niż 2 MB, użyj konta [usługi Azure Storage i kontenera obiektów blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Limity przepływności

| Punkt końcowy środowiska uruchomieniowego | Bezpłatna | Podstawowa | Standardowa (Standard) | Uwagi |
|------------------|------|-------|----------|-------|
| Wywołania odczytu na 5 minut | 3000 | 30,000 | 60 000 | Ten limit dotyczy wywołań, które odbierają nieprzetworzone dane wejściowe i wyjściowe z historii uruchamiania aplikacji logiki. W razie potrzeby można dystrybuować obciążenie na więcej niż jednym koncie. |
| Wywoływanie wywołań na 5 minut | 3000 | 30,000 | 45 000 | W razie potrzeby można dystrybuować obciążenie na więcej niż jednym koncie. |
| Śledzenie wywołań na 5 minut | 3000 | 30,000 | 45 000 | W razie potrzeby można dystrybuować obciążenie na więcej niż jednym koncie. |
| Blokowanie współbieżnych wywołań | Około 1000 | Około 1000 | Około 1000 | Taka sama dla wszystkich jednostki SKU. W razie potrzeby możesz zmniejszyć liczbę równoczesnych żądań lub skrócić czas trwania. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Rozmiar komunikatu protokołu B2B (AS2, X12, EDIFACT)

Poniżej podano limity rozmiaru komunikatów, które mają zastosowanie do protokołów B2B:

| Nazwa | Limit wielu dzierżaw | Limit środowiska usługi integracji | Uwagi |
|------|--------------------|---------------------------------------|-------|
| AS2 | Wersja 2 — 100 MB<br>Wersja 1 – 25 MB | Wersja 2 — 200 MB <br>Wersja 1 – 25 MB | Dotyczy dekodowania i kodowania |
| X12 | 50 MB | 50 MB | Dotyczy dekodowania i kodowania |
| EDIFACT | 50 MB | 50 MB | Dotyczy dekodowania i kodowania |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Wyłączanie lub usuwanie aplikacji logiki

Po wyłączeniu aplikacji logiki żadne nowe przebiegi nie są uruchamiane. Wszystkie przebiegi w toku i oczekujące będą kontynuowane do momentu ich zakończenia, co może zająć trochę czasu.

Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Konfiguracja zapory: adresy IP i tagi usług

Gdy aplikacja logiki musi komunikować się za pośrednictwem zapory, która ogranicza  ruch [](#inbound) do określonych adresów [IP,](#outbound) ta zapora musi zezwalać na dostęp zarówno dla przychodzących, jak i wychodzących adresów IP używanych przez usługę Logic Apps lub środowisko uruchomieniowe w regionie świadczenia usługi Azure, w którym istnieje aplikacja logiki. *Wszystkie* aplikacje logiki w tym samym regionie używają tych samych zakresów adresów IP.

Na przykład w celu obsługi wywołań, które aplikacje logiki w regionie Zachodnie stany USA wysyłają lub odbierają  za pośrednictwem wbudowanych wyzwalaczy  i akcji, takich jak wyzwalacz lub akcja [HTTP,](../connectors/connectors-native-http.md)zapora musi zezwalać na dostęp dla wszystkich przychodzących adresów IP usługi Logic Apps i wychodzących adresów IP istniejących w regionie Zachodnie stany USA.

Jeśli aplikacja logiki używa również łączników zarządzanych, takich jak łącznik office 365 Outlook lub łącznik  SQL, lub łączników niestandardowych, [](/connectors/custom-connectors/)zapora musi również zezwalać na dostęp do wszystkich wychodzących adresów [IP](#outbound) łącznika zarządzanego w regionie platformy Azure aplikacji logiki. [](../connectors/managed.md) Ponadto jeśli używasz łączników niestandardowych, które umożliwiają dostęp do zasobów lokalnych za pośrednictwem zasobu lokalnej bramy danych na platformie [Azure,](logic-apps-gateway-connection.md)musisz skonfigurować instalację bramy, aby zezwolić na dostęp do odpowiednich adresów IP ruchu wychodzącego łączników *[zarządzanych.](#outbound)*

Aby uzyskać więcej informacji na temat konfigurowania ustawień komunikacji w bramie, zobacz następujące tematy:

* [Dostosowywanie ustawień komunikacji dla lokalnej bramy danych](/data-integration/gateway/service-gateway-communication)
* [Konfigurowanie ustawień serwera proxy dla lokalnej bramy danych](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Zagadnienia dotyczące konfiguracji adresu IP zapory

Przed skonfigurowaniem zapory przy użyciu adresów IP zapoznaj się z tymi zagadnieniami:

* Jeśli używasz usługi [Power Automate](/power-automate/getting-started), niektóre akcje, takie jak **HTTP** i HTTP **+ OpenAPI,** są bezpośrednio za pośrednictwem usługi Azure Logic Apps i pochodzą z adresów IP wymienionych tutaj. Aby uzyskać więcej informacji na temat adresów IP używanych przez Power Automate, zobacz [Limits and configuration for Power Automate (Limity i konfiguracja dla usługi Power Automate](/flow/limits-and-config#ip-address-configuration)).

* W [Azure (Chiny) — 21Vianet](/azure/china/)przypadku łączników niestandardowych i [](../logic-apps/custom-connector-overview.md) łączników zarządzanych, takich jak Azure Storage, SQL Server, Office 365 Outlook itp., [](../connectors/managed.md)stałe lub zastrzeżone adresy IP są niedostępne dla łączników niestandardowych i łączników zarządzanych.

* Jeśli aplikacje logiki działają w środowisku usługi integracji [(ISE),](connect-virtual-network-vnet-isolated-environment-overview.md)upewnij się, że [te porty również są otwarte.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* Aby uprościć wszystkie reguły zabezpieczeń, które chcesz utworzyć, [](../virtual-network/service-tags-overview.md) możesz opcjonalnie użyć tagów usługi zamiast określać prefiksy adresów IP dla każdego regionu. Tagi te działają w regionach, w których Logic Apps usługa jest dostępna:

  * **LogicAppsManagement:** reprezentuje prefiksy przychodzących adresów IP dla Logic Apps usługi.

  * **LogicApps:** reprezentuje prefiksy wychodzących adresów IP dla Logic Apps usługi.

  * **AzureConnectors:** reprezentuje prefiksy adresów IP dla łączników zarządzanych, które wywołują wywołania zwrotne przychodzącego elementu webhook do usługi Logic Apps i wychodzące wywołania do odpowiednich usług, takich jak Azure Storage lub Azure Event Hubs.

* Jeśli aplikacje logiki mają problemy z dostępem do kont usługi Azure Storage, które używają zapór i reguł [zapory,](../storage/common/storage-network-security.md)dostępne są różne [inne opcje umożliwiające dostęp.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  Na przykład aplikacje logiki nie mogą bezpośrednio uzyskać dostępu do kont magazynu, które korzystają z reguł zapory i istnieją w tym samym regionie. Jeśli jednak zezwolisz na wychodzące adresy [IP](../logic-apps/logic-apps-limits-and-config.md#outbound)dla łączników zarządzanych w Twoim regionie, aplikacje logiki będą mieć dostęp do kont magazynu, które znajdują się w innym regionie, z wyjątkiem sytuacji, gdy używasz łączników usługi Azure Table Storage lub Azure Queue Storage. Aby uzyskać dostęp do Table Storage lub Queue Storage, można zamiast tego użyć wyzwalacza i akcji HTTP. Aby uzyskać inne opcje, zobacz Access storage accounts behind firewalls (Uzyskiwanie [dostępu do kont magazynu za zaporami).](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Przychodzące adresy IP

Ta sekcja zawiera listę adresów IP dla ruchu przychodzącego tylko Azure Logic Apps usługi. Jeśli masz już Azure Government, [zobacz Azure Government — przychodzące adresy IP.](#azure-government-inbound)

> [!TIP]
> Aby zmniejszyć złożoność podczas tworzenia reguł zabezpieczeń, możesz opcjonalnie użyć tagu usługi [,](../virtual-network/service-tags-overview.md) **LogicAppsManagement,** zamiast określać prefiksy adresów IP ruchu Logic Apps przychodzącego dla każdego regionu. Opcjonalnie można również użyć tagu usługi **AzureConnectors** dla łączników zarządzanych, które wywołują wywołania zwrotne przychodzące elementu webhook do usługi Logic Apps, zamiast określać prefiksy adresów IP łącznika zarządzanego dla ruchu przychodzącego dla każdego regionu. Tagi te działają w regionach, w których usługa Logic Apps jest dostępna.
>
> Następujące łączniki wywołują wywołania zwrotne przychodzącego Logic Apps webhook:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign Demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \***SAP:** zwracany wywołujący zależy od tego, czy środowisko wdrażania jest wielodostępną platformą Azure, czy środowiskiem ISE. W środowisku wielodostępnym lokalna brama danych wykonuje wywołanie z powrotem do Logic Apps wirtualnej. W przypadku środowiska ISE łącznik SAP wykonuje wywołanie z powrotem do Logic Apps usługi.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Wielodostępna platforma Azure — adresy IP ruchu przychodzącego

| Region z wieloma dzierżawami | Adres IP |
|---------------------|----|
| Australia Wschodnia | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australia Południowo-Wschodnia | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazylia Południowa | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Brazylia Południowo-Wschodnia | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Kanada Środkowa | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada Wschodnia | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indie Środkowe | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Central US | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Azja Wschodnia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| East US | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| Wschodnie stany USA 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Francja Środkowa | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Francja Południowa | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Niemcy Północne | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Niemcy Zachodnio-środkowe | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Japonia Wschodnia | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japonia Zachodnia | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea Środkowa | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea Południowa | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Północno-środkowe stany USA | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa Północna | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Holandia Wschodnia | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Północna Republika Południowej Afryki | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Zachodnia Republika Południowej Afryki | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| South Central US | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie Południowe | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Southeast Asia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Korea Północna | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Europa Zachodnia | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| Środkowe Zjednoczone Emiraty Zjednoczone | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Północne Zjednoczone Emiraty Zjednoczone | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Południowe Zjednoczone Królestwo | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Zachodnie Zjednoczone Królestwo | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| Zachodnio-środkowe stany USA | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| West Europe | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Indie Zachodnie | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| Zachodnie stany USA | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| Zachodnie stany USA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government — przychodzące adresy IP

| Azure Government region | Adres IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Teksas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov Wirginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD (region środkowy) | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Wychodzące adresy IP

W tej sekcji wymieniono adresy IP ruchu wychodzącego dla usługi Azure Logic Apps łączników zarządzanych. Jeśli masz już Azure Government, [zobacz Azure Government — adresy IP ruchu wychodzącego.](#azure-government-outbound)

> [!TIP]
> Aby zmniejszyć złożoność podczas tworzenia reguł zabezpieczeń, możesz opcjonalnie użyć [tagu](../virtual-network/service-tags-overview.md)usługi , **LogicApps,** zamiast określać prefiksy adresów IP Logic Apps dla każdego regionu. Opcjonalnie można również użyć tagu usługi **AzureConnectors** dla łączników zarządzanych, które wykonują wywołania wychodzące do odpowiednich usług, takich jak Azure Storage lub Azure Event Hubs, zamiast określać prefiksy adresów IP łącznika zarządzanego ruchu wychodzącego dla każdego regionu. Tagi te działają w regionach, w których Logic Apps usługa jest dostępna.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Wielodostępna platforma Azure — adresy IP ruchu wychodzącego

| Region z wieloma dzierżawami | Logic Apps IP | Adres IP łączników zarządzanych |
|---------------------|---------------|-----------------------|
| Australia Wschodnia | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Australia Południowo-Wschodnia | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Brazylia Południowa | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Brazylia Południowo-Wschodnia | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 | 23.97.120.109, 23.97.121.26 |
| Kanada Środkowa | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Kanada Wschodnia | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Indie Środkowe | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| Central US | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Azja Wschodnia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| East US | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| Wschodnie stany USA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| Francja Środkowa | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Francja Południowa | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Niemcy Północne | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Niemcy Zachodnio-środkowe | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Japonia Wschodnia | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Japonia Zachodnia | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Korea Środkowa | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Korea Południowa | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| Północno-środkowe stany USA | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Europa Północna | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Holandia Wschodnia | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Północna Republika Południowej Afryki | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Zachodnia Republika Południowej Afryki | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| South Central US | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Indie Południowe | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Southeast Asia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Korea Północna | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Europa Zachodnia | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| Środkowe Zjednoczone Emiraty Zjednoczone | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Północne Zjednoczone Emiraty Północne | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Południowe Zjednoczone Królestwo | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Zachodnie Zjednoczone Królestwo | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| Zachodnio-środkowe stany USA | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| West Europe | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Indie Zachodnie | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| Zachodnie stany USA | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| Zachodnie stany USA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government — adresy IP ruchu wychodzącego

| Region (Region) | Logic Apps IP | Adres IP łączników zarządzanych |
|--------|---------------|-----------------------|
| US DoD (region środkowy) | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| US Gov Teksas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| US Gov Wirginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Dowiedz się więcej o [typowych przykładach i scenariuszach](../logic-apps/logic-apps-examples-and-scenarios.md)
