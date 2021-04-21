---
title: Omówienie usługi Azure Logic Apps (wersja zapoznawcza)
description: Azure Logic Apps (wersja zapoznawcza) to rozwiązanie w chmurze do tworzenia zautomatyzowanych, jednodostępnych, stanowych i bez stanowych przepływów pracy, które integrują aplikacje, dane, usługi i systemy z minimalnym kodem dla scenariuszy na poziomie przedsiębiorstwa.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764823"
---
# <a name="overview-azure-logic-apps-preview"></a>Omówienie: Azure Logic Apps zapoznawcza

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzięki Azure Logic Apps w wersji zapoznawczej można tworzyć rozwiązania automatyzacji i integracji w aplikacjach, danych, usługach w chmurze i systemach, tworząc i uruchamiając aplikacje logiki z jedną dzierżawą z nowym typem zasobu Aplikacja logiki **(wersja** zapoznawcza). Przy użyciu tego typu aplikacji logiki [   ](#stateful-stateless) z jedną dzierżawą program może tworzyć wiele stanowych i bez stanowych przepływów pracy obsługiwanych przez przeprojektowane środowisko uruchomieniowe programu Azure Logic Apps Preview, które zapewnia przenośność, lepszą wydajność i elastyczność wdrażania i uruchamiania w różnych środowiskach hostingu, w tym nie tylko na platformie Azure, ale również w kontenerach platformy Docker.

Jak to możliwe? Przeprojektowane środowisko uruchomieniowe używa [Azure Functions rozszerzalności](../azure-functions/functions-bindings-register.md) i jest hostowane jako rozszerzenie w Azure Functions uruchomieniowym. Ta architektura oznacza, że aplikację logiki z jedną dzierżawą można uruchomić w dowolnym miejscu, Azure Functions uruchomiona. Przeprojektowane środowisko uruchomieniowe można hostować w niemal dowolnej topologii sieci i wybrać dowolny dostępny rozmiar obliczeniowy do obsługi niezbędnego obciążenia wymaganego przez przepływy pracy. Aby uzyskać więcej informacji, [zobacz Introduction to Azure Functions](../azure-functions/functions-overview.md) and Azure Functions triggers and bindings (Wprowadzenie do Azure Functions [wyzwalaczy i powiązań).](../azure-functions/functions-triggers-bindings.md)

Zasób aplikacji **logiki (wersja zapoznawcza)** można utworzyć, zaczynając od Azure Portal lub tworząc projekt w u Visual Studio Code z rozszerzeniem Azure Logic Apps [(wersja zapoznawcza).](create-stateful-stateless-workflows-visual-studio-code.md) [](create-stateful-stateless-workflows-azure-portal.md) Ponadto w Visual Studio Code można *kompilować* i uruchamiać lokalnie przepływy pracy w środowisku dewelopera. Niezależnie od tego, czy używasz portalu Visual Studio Code, możesz wdrożyć i uruchomić aplikację logiki z jedną dzierżawą w tego samego rodzaju środowiskach hostingu.

To omówienie obejmuje następujące obszary:

* [Różnice między Azure Logic Apps wersji zapoznawczej, środowiskiem Azure Logic Apps wielodostępnym i środowiskiem usługi integracji](#preview-differences).

* [Różnice między stanowym i bez stanowym](#stateful-stateless)przepływem pracy, w tym różnice w zachowaniu zagnieżdżonych przepływów pracy stanowych i [bez stanowych.](#nested-behavior)

* [Możliwości w tej publicznej wersji zapoznawczej.](#public-preview-contents)

* [Jak działa model cen](#pricing-model).

* [Zmienione, ograniczone, niedostępne lub nieobsługiwane możliwości.](#limited-unavailable-unsupported)

* [Limity w programie Azure Logic Apps (wersja zapoznawcza).](#limits)

Aby uzyskać więcej informacji, zapoznaj się z innymi tematami:

* [Azure Logic Apps Running Anywhere — środowisko uruchomieniowe — deep dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps znane problemy w publicznej wersji zapoznawczej (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Czym różni Azure Logic Apps zapoznawcza?

Środowisko Azure Logic Apps w wersji zapoznawczej [używa Azure Functions](../azure-functions/functions-overview.md) rozszerzalności i jest hostowane jako rozszerzenie w Azure Functions uruchomieniowym. Ta architektura oznacza, że typ aplikacji logiki pojedynczej dzierżawy można uruchomić w dowolnym miejscu, Azure Functions działa. Środowisko uruchomieniowe wersji Azure Logic Apps w wersji zapoznawczej można hostować w niemal dowolnej topologii sieci i wybrać dowolny dostępny rozmiar obliczeniowy do obsługi niezbędnego obciążenia wymaganego przez przepływ pracy. Aby uzyskać więcej informacji na Azure Functions rozszerzalności, zobacz Zestaw [SDK usługi WebJobs: tworzenie niestandardowych powiązań wejściowych i wyjściowych.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

Dzięki temu nowemu podejściu środowisko Azure Logic Apps w wersji zapoznawczej i przepływy pracy są częścią aplikacji, które można spakować razem. Ta funkcja umożliwia wdrażanie i uruchamianie przepływów pracy przez skopiowanie artefaktów do środowiska hostingu i uruchomienie aplikacji. Takie podejście zapewnia również bardziej standardowe środowisko do tworzenia potoków wdrażania wokół projektów przepływu pracy na potrzeby uruchamiania wymaganych testów i weryfikacji przed wdrożeniem zmian w środowiskach produkcyjnych. Aby uzyskać więcej informacji, zobacz Azure Logic Apps Running Anywhere - Runtime Deep Dive (Szczegółowe informacje dotyczące uruchamiania w dowolnym [miejscu — środowisko uruchomieniowe).](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

W poniższej tabeli krótko przedstawiono różnice między sposobem udostępniania zasobów przez przepływy pracy w zależności od środowiska, w którym są uruchamiane. Aby uzyskać informacje o różnicach w limitach, [zobacz Limits in Azure Logic Apps Preview (Limity w Azure Logic Apps wersji zapoznawczej).](#limits)

| Środowisko | Udostępnianie i zużycie zasobów |
|-------------|----------------------------------|
| Azure Logic Apps (wielodostępne) | Przepływy *pracy od klientów w wielu dzierżawach* współdzielą takie samo przetwarzanie (obliczenia), magazyn, sieć i tak dalej. |
| Azure Logic Apps (wersja zapoznawcza, pojedyncza dzierżawa) | Przepływy *pracy w tej samej aplikacji logiki i* jednej dzierżawie mają takie samo przetwarzanie (obliczenia), magazyn, sieć i tak dalej. |
| Środowisko usługi integracji (niedostępne w wersji zapoznawczej) | Przepływy pracy w *tym samym środowisku mają* takie samo przetwarzanie (obliczenia), magazyn, sieć i tak dalej. |
|||

W międzyczasie można nadal tworzyć typ aplikacji logiki z wieloma dzierżawami w Azure Portal i Visual Studio Code za pomocą rozszerzenia Azure Logic Apps dzierżawy. Mimo że środowisko programowe różni się między typami aplikacji logiki wielodostępnych i jednodostępnych, subskrypcja platformy Azure może zawierać oba typy. Możesz wyświetlać wszystkie wdrożone aplikacje logiki i uzyskać do nich dostęp w ramach subskrypcji platformy Azure, ale aplikacje są zorganizowane w ich własnych kategoriach i sekcjach.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Stanowe i bez stanowe przepływy pracy

Za pomocą aplikacji logiki z jedną dzierżawą można tworzyć następujące typy przepływów pracy w ramach tej samej aplikacji logiki:

* *Stanowe*

  Twórz stanowe przepływy pracy, gdy musisz przechowywać, przeglądać lub odwoływać się do danych z poprzednich zdarzeń. Te przepływy pracy zapisują dane wejściowe i wyjściowe dla każdej akcji i ich stanów w magazynie zewnętrznym, dzięki czemu przeglądanie szczegółów i historii przebiegów jest możliwe po zakończeniu każdego uruchomienia. Stanowe przepływy pracy zapewniają wysoką odporność w przypadku awarii. Po przywróceniu usług i systemów można odtworzyć przerwane przebiegi od zapisanego stanu i ponownie uruchomić przepływy pracy do ukończenia. Stanowe przepływy pracy mogą być nadal uruchomione przez maksymalnie rok.

* *Bezstanowe*

  Twórz bez stanowe przepływy pracy, gdy nie musisz zapisywać, przeglądać ani odwoływać się do danych z poprzednich zdarzeń w magazynie zewnętrznym do późniejszego przeglądu. Te przepływy pracy zapisują dane wejściowe i wyjściowe dla każdej akcji i ich stanów tylko w pamięci *,* zamiast przesyłać te dane do magazynu zewnętrznego. W związku z tym bez stanowe przepływy pracy mają krótsze przebiegi, które zwykle nie są dłuższe niż 5 minut, szybsze działanie z krótszymi czasami odpowiedzi, wyższą przepływnością i obniżeniem kosztów działania, ponieważ szczegóły uruchamiania i historia nie są przechowywane w magazynie zewnętrznym. Jeśli jednak nastąpi przerwa w pracy, przerwane przebiegi nie są automatycznie przywracane, więc wywołujący musi ręcznie ponownie przesłać przerwane przebiegi. Te przepływy pracy mogą być uruchamiane tylko synchronicznie.

  Aby ułatwić debugowanie, możesz włączyć historię uruchamiania dla bez stanowego przepływu pracy, co ma pewien wpływ na wydajność, a następnie wyłączyć historię uruchamiania po jej uruchomieniu. Aby uzyskać więcej informacji, zobacz Create [stateful and stateless workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) (Tworzenie stanowych i bez stanowych przepływów pracy w usługach Visual Studio Code) lub [Create stateful and stateless workflows](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)in the Azure Portal (Tworzenie stanowych i bez stanowych przepływów pracy w Azure Portal).

  > [!NOTE]
  > Bez stanowe przepływy pracy obsługują obecnie tylko *akcje dla* [zarządzanych](../connectors/managed.md)łączników , które są wdrażane na platformie Azure, a nie wyzwalaczy. Aby uruchomić przepływ pracy, wybierz wbudowany wyzwalacz [Żądanie, Event Hubs lub Service Bus wyzwalacz .](../connectors/built-in.md) Te wyzwalacze są uruchamiane natywnie w środowisku uruchomieniowym Azure Logic Apps w wersji zapoznawczej. Aby uzyskać więcej informacji o ograniczonych, niedostępnych lub nieobsługiwanych wyzwalaczach, akcjach i łącznikach, zobacz [Zmienione, ograniczone, niedostępne lub nieobsługiwane możliwości.](#limited-unavailable-unsupported)

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Zagnieżdżone różnice w zachowaniu między przepływami pracy stanowym i bez stanowym

Możesz [](../logic-apps/logic-apps-http-endpoint.md) ustawić przepływ pracy jako wywoływany z innych przepływów pracy, które istnieją w tym samym zasobie aplikacji **logiki (wersja zapoznawcza),** używając wyzwalacza [Żądania,](../connectors/connectors-native-reqres.md)wyzwalacza http [webhook](../connectors/connectors-native-webhook.md)lub wyzwalaczy łącznika zarządzanego, które mają typ [ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i mogą odbierać żądania HTTPS.

Poniżej podano wzorce zachowań, które mogą być zgodne z zagnieżdżonych przepływów pracy po wywołaniu podrzędnego przepływu pracy przez nadrzędny przepływ pracy:

* Wzorzec sondowania asynchronicznego

  Element nadrzędny nie czeka na odpowiedź na początkowe wywołanie, ale stale sprawdza historię uruchamiania elementu podrzędnego do momentu zakończenia działania elementu podrzędnego. Domyślnie stanowe przepływy pracy są zgodne z tym wzorcem, który jest idealny dla długotrwałych przepływów pracy podrzędnej, które mogą przekraczać [limity czasu żądań.](../logic-apps/logic-apps-limits-and-config.md)

* Wzorzec synchroniczny ("fire and forget")

  Element podrzędny potwierdza wywołanie, natychmiast zwracając odpowiedź, a element nadrzędny kontynuuje do następnej akcji bez oczekiwania `202 ACCEPTED` na wyniki z elementu podrzędnego. Zamiast tego element nadrzędny otrzymuje wyniki po zakończeniu działania elementu podrzędnego. Podrzędne stanowe przepływy pracy, które nie zawierają akcji Odpowiedź, zawsze są zgodne ze wzorcem synchronicznym. W przypadku stanowych przepływów pracy podrzędnej historia uruchamiania jest dostępna do przejrzenia.

  Aby włączyć to zachowanie, w definicji JSON przepływu pracy ustaw `operationOptions` właściwość na `DisableAsyncPattern` wartość . Aby uzyskać więcej informacji, zobacz [Typy wyzwalaczy i akcji — opcje operacji](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Wyzwalanie i oczekiwanie

  W przypadku bez stanowego przepływu pracy podrzędnego element nadrzędny czeka na odpowiedź, która zwraca wyniki z elementu podrzędnego. Ten wzorzec działa podobnie do wywoływania podrzędnego przepływu pracy za pomocą wbudowanego wyzwalacza [HTTP](../connectors/connectors-native-http.md) lub akcji. Podrzędne bez stanowe przepływy pracy, które nie zawierają akcji Odpowiedź, natychmiast zwracają odpowiedź, ale element nadrzędny czeka na zakończenie zadania podrzędnego przed kontynuowaniem do `202 ACCEPTED` następnej akcji. Te zachowania dotyczą tylko przepływów pracy bez stanu podrzędnego.

Ta tabela określa zachowanie podrzędnego przepływu pracy na podstawie tego, czy nadrzędny i podrzędny są stanowe, bez stanowe lub są mieszanymi typami przepływów pracy:

| Nadrzędny przepływ pracy | Podrzędny przepływ pracy | Zachowanie podrzędne |
|-----------------|----------------|----------------|
| Stanowe | Stanowe | Asynchroniczne lub synchroniczne z `"operationOptions": "DisableAsyncPattern"` ustawieniem |
| Stanowe | Bezstanowe | Wyzwalanie i oczekiwanie |
| Bezstanowe | Stanowe | Synchronous |
| Bezstanowe | Bezstanowe | Wyzwalanie i oczekiwanie |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Możliwości

Azure Logic Apps zapoznawcza zawiera wiele bieżących i dodatkowych możliwości, na przykład:

* Twórz aplikacje logiki i ich przepływy pracy na podstawie [ponad 400](/connectors/connector-reference/connector-reference-logicapps-connectors) łączników dla aplikacji typu oprogramowanie jako usługa (SaaS) i paaS (platforma jako usługa) oraz łączników dla systemów lokalnych.

  * Niektóre łączniki zarządzane są teraz dostępne jako wbudowane wersje, które działają podobnie do wbudowanych wyzwalaczy i akcji, takich jak wyzwalacz żądania i akcja HTTP, które są uruchamiane natywnie w środowisku uruchomieniowym Azure Logic Apps Preview. Na przykład te nowe wbudowane łączniki obejmują Azure Service Bus, Azure Event Hubs, SQL Server i MQ.

    > [!NOTE]
    > W przypadku wbudowanego łącznika SQL Server  tylko akcja Wykonaj zapytanie może łączyć się bezpośrednio z sieciami wirtualnymi platformy Azure bez konieczności użycia lokalnej [bramy danych.](logic-apps-gateway-connection.md)

  * Utwórz własne wbudowane łączniki dla dowolnej usługi, korzystając z struktury rozszerzalności [wersji zapoznawczej](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Podobnie jak w przypadku łączników wbudowanych, takich jak Azure Service Bus [](../connectors/apis-list.md#custom-apis-and-connectors) i SQL Server, ale w przeciwieństwie do łączników niestandardowych, które nie są obecnie obsługiwane w wersji zapoznawczej, te łączniki zapewniają wyższą przepływność, małe opóźnienia, łączność lokalną i działają natywnie w tym samym procesie co środowisko uruchomieniowe w wersji zapoznawczej.

    Możliwość tworzenia jest obecnie dostępna tylko w Visual Studio Code, ale nie jest domyślnie włączona. Aby utworzyć te łączniki, przełącz projekt z rozszerzenia opartego na pakiecie (Node.js) na oparty na [pakiecie NuGet (.NET).](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring) Aby uzyskać więcej informacji, zobacz [Azure Logic Apps Running Anywhere - Built-in connector extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)(Uruchamianie w dowolnym miejscu — rozszerzalność wbudowanego łącznika).

  * Akcji B2B można używać w przypadku operacji Liquid i operacji XML bez konta integracji. Aby użyć tych akcji, musisz mieć mapy Liquid, mapy XML lub schematy XML, które można przekazać za pośrednictwem odpowiednich akcji w programie  Azure Portal lub dodać do folderu **Artifacts** projektu Visual Studio Code przy użyciu odpowiednich folderów Mapy i **Schematy.**

  * Twórz i wdrażaj aplikacje logiki, które mogą być uruchamiane w dowolnym miejscu, ponieważ usługa Azure Logic Apps generuje parametry połączenia sygnatury dostępu współdzielonego (SAS), których te aplikacje logiki mogą używać do wysyłania żądań do punktu końcowego środowiska uruchomieniowego połączenia z chmurą. Usługa Logic Apps zapisuje te parametry połączenia z innymi ustawieniami aplikacji, dzięki czemu można łatwo przechowywać te wartości w Azure Key Vault podczas wdrażania na platformie Azure.

    > [!NOTE]
    > Domyślnie zasób aplikacji **logiki (wersja zapoznawcza)** ma automatycznie przypisaną przez [system](../logic-apps/create-managed-service-identity.md) tożsamość zarządzaną w celu uwierzytelniania połączeń w czasie wykonywania. Ta tożsamość różni się od poświadczeń uwierzytelniania lub parametrów połączenia, które są używane podczas tworzenia połączenia. Jeśli wyłączysz tę tożsamość, połączenia nie będą działać w czasie wykonywania. Aby wyświetlić to ustawienie, w menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Tożsamość**.

* Twórz aplikacje logiki z bez stanowym przepływem pracy, które działają tylko w pamięci, aby szybciej odpowiadały, zapewniały wyższą przepływność i kosztowały mniej, ponieważ historie uruchamiania i dane między akcjami nie są utrwalane w magazynie zewnętrznym. Opcjonalnie możesz włączyć historię uruchamiania, aby ułatwić debugowanie. Aby uzyskać więcej informacji, zobacz [Stanowe i bez stanowe aplikacje logiki.](#stateful-stateless)

* Lokalne uruchamianie, testowanie i debugowanie aplikacji logiki i ich przepływów pracy w Visual Studio Code dewelopera.

  Przed uruchomieniem i przetestowanie aplikacji logiki możesz ułatwić debugowanie, dodając punkty przerwania i używając ich wworkflow.js **pliku** dla przepływu pracy. Punkty przerwania są jednak obsługiwane tylko w przypadku akcji w tej chwili, a nie wyzwalaczy. Aby uzyskać więcej informacji, zobacz [Tworzenie stanowych](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)i bez stanowych przepływów pracy w Visual Studio Code .

* Bezpośrednio publikuj lub wdrażaj aplikacje logiki i ich przepływy pracy Visual Studio Code różnych środowiskach hostingu, takich jak kontenery platformy Azure [i platformy Docker.](/dotnet/core/docker/introduction)

* Włącz funkcje rejestrowania i śledzenia diagnostyki dla aplikacji [logiki](../azure-monitor/app/app-insights-overview.md) przy użyciu Application Insights, jeśli są obsługiwane przez subskrypcję platformy Azure i ustawienia aplikacji logiki.

* Uzyskaj dostęp do funkcji sieciowych, takich jak prywatne łączenie i integrowanie z sieciami wirtualnymi platformy Azure, podobnie jak w przypadku Azure Functions podczas tworzenia i wdrażania aplikacji logiki przy użyciu [planu Azure Functions Premium.](../azure-functions/functions-premium-plan.md) Aby uzyskać więcej informacji, zapoznaj się z tymi tematami:

  * [Opcje sieciowe usługi Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps Running Anywhere — możliwości sieci z Azure Logic Apps zapoznawcza](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Ponowne generowanie kluczy dostępu dla połączeń zarządzanych używanych przez poszczególne przepływy pracy w zasobie aplikacji logiki pojedynczej dzierżawy **(wersja zapoznawcza).** W tym zadaniu wykonaj te same kroki dla zasobu aplikacji [ **wielodostępnych Logic Apps**](logic-apps-securing-a-logic-app.md#regenerate-access-keys)ale na poziomie indywidualnego przepływu pracy , a nie na poziomie zasobu aplikacji logiki.

* Dodaj gałęzie równoległe w projektancie z jedną dzierżawą, korzystając z tych samych kroków co projektant wielodostępny.

Aby uzyskać więcej informacji, zobacz [zmienione, ograniczone,](#limited-unavailable-unsupported) niedostępne i nieobsługiwane możliwości oraz stronę Znane problemy w Logic Apps Publicznej wersji zapoznawczej w [witrynie GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Model cen

Podczas tworzenia typu aplikacji logiki z jedną dzierżawą w aplikacji Azure Portal lub wdrażania z usługi Visual Studio Code należy wybrać plan hostingu, App Service lub [Premium,](../azure-functions/functions-scale.md)do użycia przez aplikację logiki. Ten plan określa model cenowy, który ma zastosowanie do uruchamiania aplikacji logiki. Jeśli wybierzesz plan App Service, musisz również wybrać warstwę [cenową](../app-service/overview-hosting-plans.md).

*Stanowe przepływy* pracy używają magazynu [zewnętrznego,](../azure-functions/storage-considerations.md#storage-account-requirements)więc cennik usługi [Azure Storage](https://azure.microsoft.com/pricing/details/storage/) ma zastosowanie do transakcji magazynu wykonywanych przez środowisko Azure Logic Apps w wersji zapoznawczej. Na przykład kolejki są używane do planowania, a tabele i obiekty blob są używane do przechowywania stanów przepływu pracy.

> [!NOTE]
> W publicznej wersji zapoznawczej uruchamianie aplikacji logiki  na App Service nie spowoduje naliczenie dodatkowych opłat w ramach wybranego planu.

Aby uzyskać więcej informacji na temat modeli cenowych, które mają zastosowanie do typu zasobu pojedynczej dzierżawy, zapoznaj się z tymi tematami:

* [Skalowanie i hosting usługi Azure Functions](../azure-functions/functions-scale.md)
* [Skalowanie aplikacji w górę w Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions szczegóły cennika](https://azure.microsoft.com/pricing/details/functions/)
* [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/app-service/)
* [Szczegóły cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Zmienione, ograniczone, niedostępne lub nieobsługiwane możliwości

W Azure Logic Apps zapoznawczej te możliwości uległy zmianie lub są obecnie ograniczone, niedostępne lub nieobsługiwane:

* Obsługa systemu **operacyjnego:** obecnie projektant w programie Visual Studio Code nie działa w systemie operacyjnym Linux, ale nadal można wdrażać aplikacje logiki, które używają środowiska uruchomieniowego Logic Apps Preview na maszynach wirtualnych z systemem Linux. Na razie możesz tworzyć aplikacje logiki w programie Visual Studio Code w systemie Windows lub macOS, a następnie wdrażać je na maszynie wirtualnej z systemem Linux.

* **Wyzwalacze i akcje:** wbudowane wyzwalacze i akcje są uruchamiane natywnie w środowisku uruchomieniowym Azure Logic Apps Preview, podczas gdy zarządzane łączniki są wdrażane na platformie Azure. Niektóre wbudowane wyzwalacze są niedostępne, takie jak okno przesuwania i usługa Batch.

  Aby uruchomić przepływ pracy, użyj wbudowanego wyzwalacza [Cykl, Żądanie, HTTP, HTTP webhook, Event Hubs lub Service Bus .](../connectors/apis-list.md) W projektancie wbudowane wyzwalacze i akcje  są wyświetlane na karcie Wbudowane, a wyzwalacze i akcje łącznika zarządzanego są wyświetlane na karcie **Azure.**

  > [!NOTE]
  > Aby uruchamiać je lokalnie Visual Studio Code, wyzwalacze i akcje oparte na elementach webhook wymagają dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz Tworzenie stanowych i [bez stanowych](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)przepływów pracy w Visual Studio Code .

  * W *przypadku bez stanowych przepływów* pracy karta **platformy Azure** nie jest wyświetlana po wybraniu wyzwalacza, ponieważ można wybrać tylko akcje łącznika [ *zarządzanego,*](../connectors/managed.md)a nie wyzwalacze . Mimo że można włączyć łączniki zarządzane wdrożone przez platformę Azure dla bez stanowych przepływów pracy, projektant nie pokazuje żadnych wyzwalaczy łącznika zarządzanego, które można dodać.

  * W *przypadku stanowych przepływów* pracy , innych niż wyzwalacze [](../connectors/managed.md) i akcje wymienione poniżej jako niedostępne, można użyć zarówno wyzwalaczy łącznika zarządzanego, jak i akcji.

  * Te wyzwalacze i akcje uległy zmianie lub są obecnie ograniczone, nieobsługiwane lub niedostępne:

    * [Wyzwalacze lokalnej bramy *danych są*](../connectors/managed.md#on-premises-connectors) niedostępne, ale dostępne są *akcje* bramy.

    * Wbudowana akcja, Azure Functions — wybierz funkcję platformy [Azure](logic-apps-azure-functions.md) to teraz Operacje funkcji platformy Azure **— wywołaj funkcję platformy Azure.** Ta akcja obecnie działa tylko w przypadku funkcji utworzonych na podstawie **szablonu wyzwalacza HTTP.**

      W Azure Portal możesz wybrać funkcję wyzwalacza HTTP, do której masz dostęp, tworząc połączenie za pośrednictwem interfejsu użytkownika. Jeśli sprawdzisz definicję JSON akcji funkcji w widoku kodu lubworkflow.js **pliku,** akcja odwołuje się do funkcji przy użyciu `connectionName` odwołania. Ta wersja tworzy abstrakcję informacji funkcji jako połączenie, które można  znaleźć w plikuconnections.jspliku, który jest dostępny po utworzeniu połączenia.

      > [!NOTE]
      > W wersji z jedną dzierżawą akcja funkcji obsługuje tylko uwierzytelnianie ciągów zapytań. Azure Logic Apps zapoznawcza pobiera domyślny klucz z funkcji podczas tworzenia połączenia, zapisuje ten klucz w ustawieniach aplikacji i używa klucza do uwierzytelniania podczas wywoływania funkcji.
      >
      > Podobnie jak w przypadku wersji z wieloma dzierżawami, jeśli odnowisz ten klucz, na przykład za pośrednictwem witryny Azure Functions w portalu, akcja funkcji nie będzie już działać z powodu nieprawidłowego klucza. Aby rozwiązać ten problem, musisz ponownie utworzyć połączenie z funkcją, którą chcesz wywołać, lub zaktualizować ustawienia aplikacji przy użyciu nowego klucza.

    * Wbudowana akcja Kod wbudowany — Wykonywanie kodu [JavaScript](logic-apps-add-run-inline-code.md) to teraz wbudowane operacje kodu — uruchamianie **wbudowanego kodu JavaScript.**

      * Akcje wbudowanej operacji kodu nie wymagają już konta integracji.

      * W przypadku systemów macOS i Linux operacje **kodu** wbudowanego są teraz obsługiwane w przypadku używania rozszerzenia Azure Logic Apps (wersja zapoznawcza) w programie Visual Studio Code.

      * Nie trzeba już ponownie uruchamiać aplikacji logiki, jeśli wprowadzasz zmiany w akcji Operacje **kodu wbudowanego.**

      * **Akcje operacji kodu wbudowanego** mają [zaktualizowane limity](logic-apps-overview-preview.md#inline-code-limits).

    * Niektóre wbudowane wyzwalacze i akcje [B2B](../connectors/managed.md#integration-account-connectors) dla kont integracji  są niedostępne, na przykład akcje kodowania i dekodowania plików flat.

    * Wbudowana akcja, Azure Logic Apps — Wybierz przepływ pracy [aplikacji](logic-apps-http-endpoint.md) logiki to teraz Operacje przepływu pracy — Wywołaj przepływ pracy w tej **aplikacji przepływu pracy.**

* [Łączniki niestandardowe](../connectors/apis-list.md#custom-apis-and-connectors) nie są obecnie obsługiwane w wersji zapoznawczej.

* **Dostępność planu hostingu:** niezależnie od tego, czy tworzysz typ zasobu aplikacji logiki z jedną dzierżawą **(wersja zapoznawcza)** w usłudze Azure Portal, czy wdrażasz z usługi Visual Studio Code, możesz używać tylko planu hostingu Premium lub App Service na platformie Azure. Plany hostingu zużycia są niedostępne i nieobsługiwane w przypadku wdrażania tego typu zasobu. Można wdrażać z Visual Studio Code do kontenera platformy Docker, ale nie w środowisku [usługi integracji (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

* Debugowanie punktów przerwania w **programie Visual Studio Code:** chociaż w plikuworkflow.jsmożna dodawać punkty przerwania i używać ich, punkty przerwania są obecnie obsługiwane tylko dla akcji, **a** nie wyzwalaczy. Aby uzyskać więcej informacji, zobacz Tworzenie stanowych i [bez stanowych](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)przepływów pracy w Visual Studio Code .

* **Kontrolka powiększenia:** kontrolka powiększenia jest obecnie niedostępna w projektancie.

* **Historia wyzwalaczy** i historia uruchamiania: w przypadku typu zasobu Aplikacja logiki **(wersja zapoznawcza)** historia wyzwalaczy i historia uruchamiania w Azure Portal są wyświetlane na poziomie przepływu pracy, a nie na poziomie aplikacji logiki. Aby znaleźć te dane historyczne, wykonaj następujące kroki:

   * Aby wyświetlić historię uruchamiania, otwórz przepływ pracy w aplikacji logiki. W menu przepływu pracy w obszarze **Deweloper** wybierz pozycję **Monitoruj.**

   * Aby przejrzeć historię wyzwalaczy, otwórz przepływ pracy w aplikacji logiki. W menu przepływu pracy w obszarze **Deweloper** wybierz pozycję **Historie wyzwalacza.**

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Zezwalanie na ruch w rygorystycznych scenariuszach sieci i zapory

Jeśli środowisko ma ścisłe wymagania dotyczące sieci lub zapory ograniczające ruch, musisz zezwolić na dostęp do dowolnych połączeń wyzwalaczy lub akcji w przepływach pracy aplikacji logiki.

Aby znaleźć w pełni kwalifikowane nazwy domen dla tych połączeń, zapoznaj się z odpowiednimi sekcjami w tych tematach:

* [Uprawnienia zapory dla aplikacji logiki pojedynczej dzierżawy — Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Uprawnienia zapory dla aplikacji logiki pojedynczej dzierżawy — Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Zaktualizowane limity

Mimo że wiele limitów Azure Logic Apps wersji zapoznawczej jest takich samych jak limity dla wielodostępnych [Azure Logic Apps,](logic-apps-limits-and-config.md)te limity zostały zmienione dla wersji Azure Logic Apps Zapoznawcza.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Limity czasu http

W przypadku pojedynczego wywołania przychodzącego lub wychodzącego limit czasu wynosi 230 sekund (3,9 minuty) dla tych wyzwalaczy i akcji:

* Żądanie wychodzące: wyzwalacz HTTP, akcja HTTP
* Żądanie przychodzące: wyzwalacz żądania, wyzwalacz http webhook, akcja http webhook

Dla porównania poniżej podano limity czasu dla tych wyzwalaczy i akcji w innych środowiskach, w których działają aplikacje logiki i ich przepływy pracy:

* Tryb wielodostępny Azure Logic Apps: 120 sekund (2 minuty)
* Środowisko usługi integracji: 240 sekund (4 minuty)

Aby uzyskać więcej informacji, zobacz [Limity PROTOKOŁU HTTP.](logic-apps-limits-and-config.md#http-limits)

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Łączniki zarządzane

Łączniki zarządzane są ograniczone do 50 żądań na minutę na połączenie. Aby rozwiązać problemy z ograniczaniem przepustowości łącznika, zobacz [Handle throttling problems (429 - "Too many requests" error)](handle-throttling-problems-429-errors.md#connector-throttling)(Obsługa problemów z ograniczaniem przepustowości (błąd 429 — zbyt wiele żądań) w Azure Logic Apps .

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Operacje kodu wbudowanego (wykonywanie kodu JavaScript)

W przypadku pojedynczej definicji aplikacji logiki akcja Operacje kodu wbudowanego Execute [**JavaScript Code**](logic-apps-add-run-inline-code.md)ma następujące zaktualizowane limity:

* Maksymalna liczba znaków kodu zwiększa się z 1024 do 100 000 znaków.

* Maksymalny czas trwania uruchamiania kodu zwiększa się z 5 sekund do 15 sekund.

Aby uzyskać więcej informacji, zobacz [Logic app definition limits (Limity definicji aplikacji logiki).](logic-apps-limits-and-config.md#definition-limits)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie stanowych i bez stanowych przepływów pracy w Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Tworzenie stanowych i bez stanowych przepływów pracy w Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps znane problemy w publicznej wersji zapoznawczej w usłudze GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Ponadto chcemy się do Ciebie odesłuchić o twoich doświadczeniach z Azure Logic Apps wersji zapoznawczej!

* W przypadku usterek lub problemów [utwórz problemy w usłudze GitHub.](https://github.com/Azure/logicapps/issues)
* W przypadku pytań, żądań, komentarzy i innych opinii skorzystaj [z tego formularza opinii.](https://aka.ms/lafeedback)
