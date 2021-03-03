---
title: Omówienie usługi Azure Logic Apps (wersja zapoznawcza)
description: Wersja zapoznawcza Azure Logic Apps to rozwiązanie w chmurze służące do tworzenia zautomatyzowanych stanowych i bezstanowych przepływów pracy, które integrują aplikacje, dane, usługi i systemy z minimalnym kodem dla scenariuszy na poziomie przedsiębiorstwa.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9d8d3cb4bf68f7da2bddabd21272d1011ce92f66
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715211"
---
# <a name="overview-azure-logic-apps-preview"></a>Przegląd: Podgląd Azure Logic Apps

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzięki Azure Logic Apps wersji zapoznawczej można tworzyć rozwiązania do automatyzacji i integracji dla aplikacji, danych, usług w chmurze i systemów przez tworzenie i uruchamianie aplikacji logiki, które obejmują [ *stanowe* i *bezstanowe* przepływy pracy](#stateful-stateless) przy użyciu nowego typu zasobu **aplikacji logiki (wersja zapoznawcza)** . Przy użyciu tego nowego typu aplikacji logiki można utworzyć wiele przepływów pracy, które są obsługiwane przez przeprojektowaną wersję środowiska uruchomieniowego Azure Logic Apps Preview, która zapewnia przenośność, lepszą wydajność i elastyczność wdrażania i uruchamiania w różnych środowiskach hostingu, a nie tylko na platformie Azure, ale również kontenerów platformy Docker.

Jak to możliwe? Przeprojektowany środowisko uruchomieniowe używa [modelu rozszerzalności Azure Functions](../azure-functions/functions-bindings-register.md) i jest hostowane jako rozszerzenie w środowisku uruchomieniowym Azure Functions. Ta architektura oznacza, że można uruchomić nowy typ aplikacji logiki w dowolnym miejscu, w którym Azure Functions uruchamiane. Można hostować przeprojektowane środowisko uruchomieniowe na prawie dowolnej topologii sieci i wybrać dowolny dostępny rozmiar obliczeniowy do obsługi niezbędnego obciążenia, które jest wymagane przez przepływy pracy. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Functions](../azure-functions/functions-overview.md) i [Azure Functions wyzwalacze i powiązania](../azure-functions/functions-triggers-bindings.md).

Zasób **aplikacji logiki (wersja zapoznawcza)** można utworzyć, [rozpoczynając od Azure Portal](create-stateful-stateless-workflows-azure-portal.md) lub przez [utworzenie projektu w Visual Studio Code z rozszerzeniem Azure Logic Apps (wersja zapoznawcza)](create-stateful-stateless-workflows-visual-studio-code.md). Ponadto w Visual Studio Code można tworzyć *i lokalnie uruchamiać* przepływy pracy w środowisku deweloperskim. Bez względu na to, czy korzystasz z portalu, czy Visual Studio Code, możesz wdrożyć i uruchomić nowy typ aplikacji logiki w tym samym rodzaju środowiskach hostingu.

To omówienie obejmuje następujące zagadnienia:

* [Różnice między wersjami zapoznawczymi Azure Logic Apps, Azure Logic Apps środowisku z wieloma dzierżawcami i środowiskiem usługi integracji](#preview-differences).

* [Różnice między stanowymi i bezstanowymi przepływami pracy](#stateful-stateless), w tym różnice między [zagnieżdżonymi stanami i bezstanowymi przepływami pracy](#nested-behavior)

* [Możliwości w tej publicznej wersji zapoznawczej](#public-preview-contents).

* [Jak działa model cen](#pricing-model).

* [Możliwości zmiany, ograniczone, niedostępne lub nieobsługiwane](#limited-unavailable-unsupported).

* [Limity w wersji Zapoznawczej Azure Logic Apps](#limits).

Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami:

* [Azure Logic Apps uruchamianie w dowolnym miejscu — głębokie szczegółowe środowiska uruchomieniowego](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps znane problemy w wersji zapoznawczej (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Jak różni się wersja zapoznawcza Azure Logic Apps?

Środowisko uruchomieniowe Azure Logic Apps w wersji zapoznawczej używa rozszerzalności [Azure Functions](../azure-functions/functions-overview.md) i jest hostowane jako rozszerzenie w środowisku uruchomieniowym Azure Functions. Ta architektura oznacza, że można uruchomić nowy typ aplikacji logiki w dowolnym miejscu, w którym Azure Functions uruchamiane. Środowisko uruchomieniowe Azure Logic Apps w wersji zapoznawczej można hostować na niemal dowolnej topologii sieci, a następnie wybrać dowolny dostępny rozmiar do obsługi wymaganego obciążenia, które jest potrzebne przez przepływ pracy. Aby uzyskać więcej informacji o rozszerzalności Azure Functions, zobacz [zestaw WebJobs SDK: Tworzenie niestandardowych powiązań wejściowych i wyjściowych](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

Dzięki temu nowemu podejściu środowisko uruchomieniowe programu Azure Logic Apps w wersji zapoznawczej i przepływy pracy są częścią aplikacji, którą można spakować razem. Ta funkcja umożliwia wdrażanie i uruchamianie przepływów pracy przez proste Kopiowanie artefaktów do środowiska hostingu i uruchamianie aplikacji. Takie podejście zapewnia również bardziej ustandaryzowane środowisko budowania potoków wdrożenia wokół projektów przepływu pracy do uruchamiania wymaganych testów i walidacji przed wdrożeniem zmian w środowiskach produkcyjnych. Aby uzyskać więcej informacji, zobacz [Azure Logic Apps uruchamianie w dowolnym miejscu — głębokie szczegółowe środowiska uruchomieniowego](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

W poniższej tabeli krótko podsumowano różnice w sposobie, w jaki przepływy pracy współużytkują zasoby, w zależności od środowiska, w którym są uruchamiane. Aby uzyskać różnice w limitach, zobacz [limity w wersji Zapoznawczej Azure Logic Apps](#limits).

| Środowisko | Udostępnianie i użycie zasobów |
|-------------|----------------------------------|
| Azure Logic Apps (wiele dzierżawców) | Przepływy pracy *klientów w wielu dzierżawcach* korzystają z tego samego przetwarzania (obliczeń), magazynu, sieci i tak dalej. |
| Azure Logic Apps (wersja zapoznawcza) | Przepływy pracy *w tej samej aplikacji logiki* korzystają z tego samego przetwarzania (obliczeń), magazynu, sieci i tak dalej. |
| Środowisko usługi integracji (niedostępne w wersji zapoznawczej) | Przepływy pracy w *tym samym środowisku* korzystają z tego samego przetwarzania (obliczeń), magazynu, sieci i tak dalej. |
||||

W tym czasie można nadal utworzyć oryginalny typ aplikacji logiki w Azure Portal i w Visual Studio Code przy użyciu oryginalnego rozszerzenia Azure Logic Apps. Chociaż środowiska programistyczne różnią się między oryginalnymi i nowymi typami aplikacji logiki, Twoja subskrypcja platformy Azure może zawierać oba typy. Możesz wyświetlać i uzyskiwać dostęp do wszystkich wdrożonych aplikacji logiki w ramach subskrypcji platformy Azure, ale aplikacje są zorganizowane w ich własne kategorie i sekcje.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Przepływy pracy stanowych i bezstanowych

* *Stanowe*

  Twórz stanowe przepływy pracy, gdy chcesz zachować, przejrzeć lub odwołać dane z poprzednich zdarzeń. Te przepływy pracy zapisują dane wejściowe i wyjściowe dla każdej akcji i ich stanów w magazynie zewnętrznym, dzięki czemu można przeglądać szczegóły uruchamiania i historię po zakończeniu każdego przebiegu. Bezstanowe przepływy pracy zapewniają wysoką odporność w przypadku wystąpienia awarii. Po przywróceniu usług i systemów można odtworzyć przerwane uruchomienia z zapisanego stanu i ponownie uruchomić przepływy pracy w celu ukończenia. Stanowe przepływy pracy mogą być nadal wykonywane przez maksymalnie rok.

* *Bezstanowe*

  Twórz bezstanowe przepływy pracy, gdy nie musisz zapisywać, przeglądać ani odwoływać danych z poprzednich zdarzeń w magazynie zewnętrznym do późniejszego przeglądu. Te przepływy pracy zapisują dane wejściowe i wyjściowe dla każdej akcji i ich Stanów *tylko w pamięci*, a nie są transferowane do magazynu zewnętrznego. W związku z tym bezstanowe przepływy pracy mają krótsze przebiegi, które zwykle nie są dłuższe niż 5 minut, krótszy czas odpowiedzi, wyższą przepływność i mniejsze koszty działania, ponieważ szczegóły i historia przebiegu nie są przechowywane w magazynie zewnętrznym. Jednak w przypadku wystąpienia awarii przerwane uruchomienia nie są automatycznie przywracane, więc wywołujący musi ręcznie ponownie przesłać przerwane uruchomienia. Te przepływy pracy można uruchamiać tylko synchronicznie.

  Aby ułatwić debugowanie, można włączyć historię uruchamiania dla bezstanowego przepływu pracy, który ma wpływ na wydajność, a następnie wyłączyć historię uruchamiania po zakończeniu. Aby uzyskać więcej informacji, zobacz [Tworzenie przepływów pracy stanowych i bezstanowych w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) lub [Tworzenie stanowych i bezstanowych przepływów pracy w Azure Portal](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Bezstanowe przepływy pracy obsługują obecnie tylko *Akcje* dotyczące [łączników zarządzanych](../connectors/apis-list.md#managed-api-connectors), które są wdrożone na platformie Azure, a nie są wyzwalane. Aby uruchomić przepływ pracy, wybierz [wyzwalacz wbudowane żądanie, Event Hubs lub Service Bus](../connectors/apis-list.md#built-ins). Te wyzwalacze są uruchamiane natywnie w środowisku uruchomieniowym programu Azure Logic Apps w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ograniczonych, niedostępnych lub nieobsługiwanych wyzwalaczy, akcji i łączników, zobacz [zmiany, ograniczone, niedostępne lub nieobsługiwane możliwości](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Różnice w zachowaniu zagnieżdżone między stanowymi i bezstanowymi przepływami pracy

[Przepływ pracy można wywołać](../logic-apps/logic-apps-http-endpoint.md) z innych przepływów pracy, które istnieją w tym samym zasobie **logiki (wersja zapoznawcza)** , korzystając z wyzwalacza [żądania](../connectors/connectors-native-reqres.md), [wyzwalacza elementu webhook protokołu HTTP](../connectors/connectors-native-webhook.md)lub wyzwalaczy łączników zarządzanych, które mają [Typ ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i mogą odbierać żądania HTTPS.

Poniżej przedstawiono wzorce zachowań, które mogą być używane przez zagnieżdżone przepływy pracy, gdy nadrzędny przepływ pracy wywołuje podrzędny przepływ pracy:

* Wzorzec sondowania asynchronicznego

  Element nadrzędny nie czeka na odpowiedź na początkowe wywołanie, ale stale sprawdza historię uruchamiania dziecka do momentu zakończenia działania elementu podrzędnego. Domyślne przepływy pracy są zgodne z tym wzorcem, co jest idealne dla długotrwałych przepływów pracy, które mogą przekroczyć [limity limitu czasu żądania](../logic-apps/logic-apps-limits-and-config.md).

* Wzorzec synchroniczny ("pożar i zapomnij")

  Element podrzędny potwierdza wywołanie przez natychmiastowe zwrócenie `202 ACCEPTED` odpowiedzi, a element nadrzędny kontynuuje następną akcję bez czekania na wyniki z elementu podrzędnego. Zamiast tego obiekt nadrzędny otrzymuje wyniki po zakończeniu działania elementu podrzędnego. Podrzędne bezstanowe przepływy pracy, które nie zawierają akcji odpowiedzi, zawsze podążają za wzorcem synchronicznym. Dla podrzędnych przepływów pracy, historia przebiegów jest dostępna do przejrzenia.

  Aby włączyć to zachowanie, w definicji JSON przepływu pracy Ustaw `operationOptions` Właściwość na `DisableAsyncPattern` . Aby uzyskać więcej informacji, zobacz [typy wyzwalaczy i akcji — opcje operacji](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Wyzwalaj i czekaj

  W przypadku podrzędnego bezstanowego przepływu pracy element nadrzędny czeka na odpowiedź, która zwraca wyniki z elementu podrzędnego. Ten wzorzec działa podobnie do użycia wbudowanego [wyzwalacza http lub akcji](../connectors/connectors-native-http.md) w celu wywołania podrzędnego przepływu pracy. Podrzędne bezstanowe przepływy pracy, które nie zawierają akcji odpowiedzi natychmiast zwracają `202 ACCEPTED` odpowiedź, ale element nadrzędny czeka na zakończenie działania elementu podrzędnego przed przejściem do następnej akcji. Te zachowania mają zastosowanie tylko do podrzędnych bezstanowych przepływów pracy.

Ta tabela określa zachowanie podrzędnego przepływu pracy w zależności od tego, czy nadrzędne i podrzędne są typy stanowe, bezstanowe czy mieszane przepływy pracy:

| Nadrzędny przepływ pracy | Podrzędny przepływ pracy | Zachowanie elementu podrzędnego |
|-----------------|----------------|----------------|
| Stanowe | Stanowe | Asynchroniczne lub synchroniczne z `"operationOptions": "DisableAsyncPattern"` ustawieniem |
| Stanowe | Bezstanowe | Wyzwalaj i czekaj |
| Bezstanowe | Stanowe | Synchronous |
| Bezstanowe | Bezstanowe | Wyzwalaj i czekaj |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Możliwości

Wersja zapoznawcza Azure Logic Apps obejmuje wiele bieżących i dodatkowych możliwości, na przykład:

* Twórz aplikacje logiki i ich przepływy pracy za pomocą programu [390 i łączników](/connectors/connector-reference/connector-reference-logicapps-connectors) w przypadku aplikacji typu "oprogramowanie jako usługa" (SaaS) i "platforma jako usługa" (PaaS) oraz łączników dla systemów lokalnych.

  * Niektóre zarządzane łączniki, takie jak Azure Service Bus, Azure Event Hubs i SQL Server działają podobnie jak wbudowane wyzwalacze i akcje, które są natywne dla środowiska uruchomieniowego programu Azure Logic Apps Preview, na przykład wyzwalacz żądania i akcja HTTP. Aby uzyskać więcej informacji, zobacz [Azure Logic Apps korzystania z rozszerzalności łączników wbudowanych w dowolne miejsce](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Za pomocą akcji B2B dla operacji związanych z operacjami i operacjami XML nie można używać konta integracji. Aby móc korzystać z tych akcji, musisz mieć mapy płynne, mapy XML lub schematy XML, które można przekazać przez odpowiednie akcje w Azure Portal lub dodać do folderu **artefakty** projektu Visual Studio Code za pomocą odpowiednich **map** i folderów **schematów** .

  * Twórz i wdrażaj aplikacje logiki, które można uruchamiać w dowolnym miejscu, ponieważ usługa Azure Logic Apps generuje parametry połączenia sygnatury dostępu współdzielonego, które mogą być używane przez aplikacje logiki do wysyłania żądań do punktu końcowego środowiska uruchomieniowego połączenia w chmurze. Usługa Logic Apps zapisuje te parametry połączenia przy użyciu innych ustawień aplikacji, dzięki czemu można łatwo przechowywać te wartości w Azure Key Vault podczas wdrażania na platformie Azure.

    > [!NOTE]
    > Domyślnie dla zasobu **aplikacji logiki (wersja zapoznawcza)** jest automatycznie włączona [tożsamość zarządzana przypisana przez system](../logic-apps/create-managed-service-identity.md) w celu uwierzytelniania połączeń w czasie wykonywania. Ta tożsamość różni się od poświadczeń uwierzytelniania lub parametrów połączenia, które są używane podczas tworzenia połączenia. Jeśli wyłączysz tę tożsamość, połączenia nie będą działały w czasie wykonywania. Aby wyświetlić to ustawienie, w menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **tożsamość**.

* Twórz aplikacje logiki przy użyciu bezstanowych przepływów pracy, które są uruchamiane tylko w pamięci, dzięki czemu kończą się szybciej, reagują szybszym, mają wyższą przepływność i tańsze do uruchomienia, ponieważ przebiegi i dane między akcjami nie są utrwalane w magazynie zewnętrznym. Opcjonalnie można włączyć historię uruchamiania, aby ułatwić debugowanie. Aby uzyskać więcej informacji, zobacz [stanowe i bezstanowe Aplikacje logiki](#stateful-stateless).

* Lokalnie uruchamiaj, Testuj i Debuguj aplikacje logiki oraz ich przepływy pracy w Visual Studio Code środowisku deweloperskim.

  Przed uruchomieniem i przetestowaniem aplikacji logiki można ułatwić debugowanie, dodając i używając punktów przerwania w **workflow.jsw** pliku dla przepływu pracy. Jednak punkty przerwania są obsługiwane tylko w przypadku akcji, które nie są wyzwalane. Aby uzyskać więcej informacji, zobacz [Tworzenie stanowych i bezstanowych przepływów pracy w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Bezpośrednio Publikuj lub Wdrażaj aplikacje logiki oraz ich przepływy pracy z Visual Studio Code do różnych środowisk hostingu, takich jak platforma Azure i [kontenery platformy Docker](/dotnet/core/docker/introduction).

* Włączenie funkcji rejestrowania i śledzenia diagnostyki dla aplikacji logiki przy użyciu [Application Insights](../azure-monitor/app/app-insights-overview.md) , gdy są one obsługiwane przez ustawienia subskrypcji platformy Azure i aplikacji logiki.

* Uzyskaj dostęp do funkcji sieciowych, takich jak łączenie i integrowanie prywatnie z sieciami wirtualnymi platformy Azure, podobnie jak Azure Functions podczas tworzenia i wdrażania aplikacji logiki przy użyciu [planu Azure Functions Premium](../azure-functions/functions-premium-plan.md). Aby uzyskać więcej informacji, zapoznaj się z następującymi tematami:

  * [Opcje sieciowe usługi Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps uruchamianie dowolnych możliwości sieciowych za pomocą wersji zapoznawczej Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Wygeneruj ponownie klucze dostępu dla połączeń zarządzanych używanych przez poszczególne przepływy pracy w ramach zasobu **aplikacji logiki (wersja zapoznawcza)** . W przypadku tego zadania [wykonaj te same kroki dla zasobu **Logic Apps** , ale na poziomie pojedynczego przepływu pracy, a](logic-apps-securing-a-logic-app.md#regenerate-access-keys)nie na poziomie zasobów aplikacji logiki.

* Dodaj gałęzie równoległe w nowym projektancie, wykonując te same czynności co Projektant niebędący podglądem w wersji zapoznawczej.
 
Aby uzyskać więcej informacji, zobacz sekcję [zmiany, ograniczone, niedostępne i nieobsługiwane](#limited-unavailable-unsupported) oraz [Logic Apps publicznej wersji zapoznawczej w](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)serwisie GitHub.

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Model cen

Po utworzeniu nowego typu aplikacji logiki w Azure Portal lub wdrożeniu z Visual Studio Code musisz wybrać plan hostingu, [App Service lub Premium](../azure-functions/functions-scale.md), aby można było użyć aplikacji logiki. Ten plan określa model cen, który ma zastosowanie do uruchamiania aplikacji logiki. W przypadku wybrania planu App Service należy również wybrać [warstwę cenową](../app-service/overview-hosting-plans.md).

Przepływy pracy *stanowych* korzystają z [magazynu zewnętrznego](../azure-functions/storage-considerations.md#storage-account-requirements), więc [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) dotyczy transakcji magazynu, które wykonuje środowisko uruchomieniowe w wersji zapoznawczej Azure Logic Apps. Na przykład kolejki są używane do planowania, natomiast tabele i obiekty blob są używane do przechowywania stanów przepływu pracy.

> [!NOTE]
> W publicznej wersji zapoznawczej uruchamianie aplikacji logiki na App Service nie wiąże się z *dodatkowymi* opłatami na podstawie wybranego planu.

Aby uzyskać więcej informacji na temat modelu cen, które są stosowane do tego nowego typu zasobu, przejrzyj następujące tematy:

* [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)
* [Skalowanie aplikacji w górę w Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions szczegóły cennika](https://azure.microsoft.com/pricing/details/functions/)
* [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/app-service/)
* [Szczegóły cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Możliwości zmiany, ograniczone, niedostępne lub nieobsługiwane

W Azure Logic Apps wersji zapoznawczej te funkcje zostały zmienione lub są obecnie ograniczone, niedostępne lub nieobsługiwane:

* **Obsługa systemu operacyjnego**: obecnie projektant w Visual Studio Code nie działa w systemie operacyjnym Linux, ale nadal można wdrożyć aplikacje logiki, które używają środowiska uruchomieniowego Logic Apps w wersji zapoznawczej do maszyn wirtualnych opartych na systemie Linux. Na razie możesz skompilować Aplikacje logiki w Visual Studio Code w systemie Windows lub macOS, a następnie wdrożyć je na maszynie wirtualnej z systemem Linux.

* **Wyzwalacze i akcje**: wbudowane wyzwalacze i akcje działają natywnie w środowisku uruchomieniowym Azure Logic Apps w wersji zapoznawczej, podczas gdy łączniki zarządzane są wdrażane na platformie Azure. Niektóre wbudowane wyzwalacze są niedostępne, na przykład przedziały ruchome i wsadowe.

  Aby uruchomić przepływ pracy, użyj [wbudowanego wyzwalacza, żądania, protokołu HTTP, elementu webhook, Event Hubs lub Service Bus](../connectors/apis-list.md). W projektancie wbudowane wyzwalacze i akcje są wyświetlane na karcie **wbudowane** , podczas gdy zarządzane wyzwalacze łącznika i akcje są wyświetlane na karcie **Azure** .

  > [!NOTE]
  > Aby uruchamiać lokalnie w Visual Studio Code, wyzwalacze i akcje oparte na elementach webhook wymagają dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz [Tworzenie stanowych i bezstanowych przepływów pracy w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * W przypadku *bezstanowych przepływów pracy* karta **Azure** nie jest wyświetlana po wybraniu wyzwalacza, ponieważ można wybrać tylko [ *Akcje* łącznika zarządzanego, a nie wyzwalacze](../connectors/apis-list.md#managed-api-connectors). Mimo że można włączyć łączniki zarządzane wdrożone na platformie Azure dla bezstanowych przepływów pracy, Projektant nie pokazuje żadnych wyzwalaczy łączników zarządzanych do dodania.

  * W przypadku *przepływów pracy stanowych* innych niż wyzwalacze i akcje, które są wymienione jako niedostępne poniżej, dostępne są zarówno [wyzwalacze łączników zarządzanych, jak i akcje](../connectors/apis-list.md#managed-api-connectors) do użycia.

  * Te wyzwalacze i akcje zostały zmienione lub są obecnie ograniczone, nieobsługiwane lub niedostępne:

    * [ *Wyzwalacze* lokalnych bram danych](../connectors/apis-list.md#on-premises-connectors) są niedostępne, ale akcje bramy *są* dostępne.

    * [Łączniki niestandardowe](../connectors/apis-list.md#custom-apis-and-connectors) są niedostępne.

    * Wbudowana Akcja [Azure Functions — wybierz funkcję platformy](logic-apps-azure-functions.md) Azure, która jest teraz **operacją usługi Azure Functions — wywołaj funkcję platformy Azure**. Ta akcja działa obecnie tylko w przypadku funkcji, które są tworzone na podstawie szablonu **wyzwalacza http** .

      W Azure Portal można wybrać funkcję wyzwalacza HTTP, do której masz dostęp przez utworzenie połączenia za pośrednictwem środowiska użytkownika. Jeśli sprawdzisz definicję JSON akcji funkcji w widoku kodu lub **workflow.jsw** pliku, Akcja odwołuje się do funkcji przy użyciu `connectionName` odwołania. Ta wersja dzieli informacje o funkcji jako połączenie, które można znaleźć w **connections.jsprojektu na** pliku, który jest dostępny po utworzeniu połączenia.

      > [!NOTE]
      > W wersji zapoznawczej akcja funkcji obsługuje tylko uwierzytelnianie ciągu zapytania. Wersja zapoznawcza Azure Logic Apps Pobiera klucz domyślny z funkcji podczas nawiązywania połączenia, zapisuje ten klucz w ustawieniach aplikacji i używa klucza do uwierzytelniania podczas wywoływania funkcji.
      >
      > Podobnie jak w przypadku wersji oryginalnej, w przypadku odnowienia tego klucza, na przykład za pomocą środowiska Azure Functions w portalu, akcja funkcji nie będzie już działać z powodu nieprawidłowego klucza. Aby rozwiązać ten problem, należy ponownie utworzyć połączenie z funkcją, która ma zostać wywołana lub zaktualizować ustawienia aplikacji przy użyciu nowego klucza.

    * Wbudowana akcja, wbudowany kod [języka JavaScript](logic-apps-add-run-inline-code.md) jest teraz **operacją kodu śródwierszowego — uruchamiaj w wierszu JavaScript**.

      * Akcje operacji kodu wbudowanego nie wymagają już konta integracji.

      * W przypadku systemów macOS i Linux **operacje kodu wbudowanego** są teraz obsługiwane w przypadku używania rozszerzenia Azure Logic Apps (wersja zapoznawcza) w Visual Studio Code.

      * Nie trzeba już ponownie uruchamiać aplikacji logiki, jeśli wprowadzisz zmiany w akcji **operacji w kodzie wbudowanym** .

      * Akcje **operacji kodu wbudowanego** mają [zaktualizowane limity](logic-apps-overview-preview.md#inline-code-limits).

    * Niektóre [wbudowane wyzwalacze i akcje B2B dla kont integracji](../connectors/apis-list.md#integration-account-connectors) są niedostępne, na przykład w przypadku prostych operacji kodowania i dekodowania **plików** .

    * Wbudowana Akcja [Azure Logic Apps — wybierz przepływ pracy aplikacji logiki](logic-apps-http-endpoint.md) to teraz **operacje przepływu pracy — Wywołaj przepływ pracy w tej aplikacji przepływu pracy**.

* **Dostępność planu hostingu**: niezależnie od tego, czy tworzony jest nowy typ zasobu **aplikacji logiki (wersja zapoznawcza)** w Azure Portal lub Deploy from Visual Studio Code, można użyć planu hostingu Premium lub App Service na platformie Azure. Plany hostingu zużycia są niedostępne i nie są obsługiwane w przypadku wdrażania tego typu zasobu. Program można wdrożyć z Visual Studio Code do kontenera platformy Docker, ale nie do [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

* **Debugowanie punktu przerwania w Visual Studio Code**: Chociaż można dodawać punkty przerwania i używać ich w **workflow.js** pliku dla przepływu pracy, punkty przerwania są obsługiwane tylko w przypadku akcji, które nie są wyzwalane. Aby uzyskać więcej informacji, zobacz [Tworzenie stanowych i bezstanowych przepływów pracy w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* **Kontrolka powiększenia**: kontrolka powiększenia jest aktualnie niedostępna w projektancie.

* Historia **i historia uruchamiania wyzwalacza**: dla typu zasobu **aplikacja logiki (wersja zapoznawcza)** , historia wyzwalacza i historia przebiegu w Azure Portal pojawia się na poziomie przepływu pracy, a nie na poziomie aplikacji logiki. Aby znaleźć te dane historyczne, wykonaj następujące kroki:

   * Aby wyświetlić historię uruchamiania, Otwórz przepływ pracy w aplikacji logiki. W menu przepływ pracy w obszarze **deweloper** wybierz pozycję **Monitoruj**.

   * Aby przejrzeć historię wyzwalacza, Otwórz przepływ pracy w aplikacji logiki. W menu przepływ pracy w obszarze **deweloper** wybierz pozycję **Wyzwalaj historie**.

<a name="limits"></a>

## <a name="updated-limits"></a>Zaktualizowane limity

Mimo że wiele limitów Azure Logic Apps wersji zapoznawczej pozostaje taki sam, jak [limity dla Azure Logic Apps z wieloma dzierżawcami](logic-apps-limits-and-config.md), te limity zostały zmienione dla programu Azure Logic Apps Preview.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Limity limitu czasu HTTP

W przypadku pojedynczego wywołania przychodzącego lub wywołania wychodzącego limit czasu wynosi 230 sekund (3,9 minut) dla tych wyzwalaczy i akcji:

* Żądanie wychodzące: wyzwalacz HTTP, Akcja HTTP
* Żądanie przychodzące: wyzwalacz żądania, wyzwalacz elementu webhook protokołu HTTP, akcja elementu webhook protokołu HTTP

Poniżej przedstawiono limity limitu czasu dla tych wyzwalaczy i akcji w innych środowiskach, w których są uruchamiane aplikacje logiki i ich przepływy pracy:

* Azure Logic Apps z wieloma dzierżawcami: 120 sekund (2 minuty)
* Środowisko usługi integracji: 240 sekund (4 minuty)

Aby uzyskać więcej informacji, zobacz [limity protokołu HTTP](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Łączniki zarządzane

Łączniki zarządzane są ograniczone do 50 żądań na minutę za połączenie. Aby obejść problemy związane z ograniczaniem łączników, zobacz temat [Rozwiązywanie problemów z ograniczaniem przepustowości (429-"zbyt wiele żądań") w Azure Logic Apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Operacje kodu wbudowanego (wykonaj kod JavaScript)

W przypadku pojedynczej definicji aplikacji logiki akcja operacji kodu wbudowanego, [**Wykonaj kod JavaScript**](logic-apps-add-run-inline-code.md), ma następujące zaktualizowane limity:

* Maksymalna liczba znaków kodu rośnie od 1 024 znaków do 100 000 znaków.

* Maksymalny czas trwania uruchamiania kodu wzrosną z pięciu sekund do 15 sekund.

Aby uzyskać więcej informacji, zobacz [limity definicji aplikacji logiki](logic-apps-limits-and-config.md#definition-limits).

## <a name="next-steps"></a>Następne kroki

* [Twórz stanowe i bezstanowe przepływy pracy w Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Twórz stanowe i bezstanowe przepływy pracy w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Strona Logic Appse znane problemy w wersji zapoznawczej w serwisie GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Ponadto chcemy się dowiedzieć o Twoich wrażeniach, korzystając z wersji zapoznawczej Azure Logic Apps!

* W przypadku usterek lub problemów [Utwórz swoje problemy w serwisie GitHub](https://github.com/Azure/logicapps/issues).
* Aby uzyskać odpowiedzi na pytania, żądania, komentarze i inne opinie, [Skorzystaj z tej formy opinii](https://aka.ms/lafeedback).
