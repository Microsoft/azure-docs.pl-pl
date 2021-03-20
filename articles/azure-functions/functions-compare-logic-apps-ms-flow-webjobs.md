---
title: Opcje platformy integracji i automatyzacji na platformie Azure
description: 'Porównanie usług w chmurze firmy Microsoft, które są zoptymalizowane pod kątem zadań związanych z integracją: Automatyzacja, Logic Apps, funkcje i Zadania WebJob.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: 95167791efe13526b0a70c28fa89771542a9d220
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685565"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Wybieranie odpowiednich usług integracji i automatyzacji na platformie Azure

W tym artykule porównano następujące usługi w chmurze firmy Microsoft:

* [Microsoft — Automatyzacja](https://flow.microsoft.com/) (was Microsoft Flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Wszystkie te usługi umożliwiają rozwiązywanie problemów z integracją i automatyzację procesów biznesowych. Mogą one definiować dane wejściowe, akcje, warunki i dane wyjściowe. Każdą z nich można uruchomić zgodnie z harmonogramem lub za pomocą wyzwalacza. Każda usługa ma unikatowe zalety, a w tym artykule opisano różnice między nimi. 

Jeśli szukasz bardziej ogólnego porównania między Azure Functions i innymi opcjami obliczeniowymi platformy Azure, zobacz [kryteria wybierania usługi obliczeniowej platformy Azure](/azure/architecture/guide/technology-choices/compute-comparison) i [wybierania opcji obliczeń Azure dla mikrousług](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Porównanie automatyzacji i Azure Logic Apps Microsoft

Automatyzacja i Logic Apps są usługami integracji w *pierwszej kolejności* , które mogą tworzyć przepływy pracy. Obie te usługi służą do integracji z różnymi aplikacjami SaaS i aplikacjami dla przedsiębiorstw. 

Automatyzacja jest oparta na Logic Apps. Współdzielą one tego samego projektanta przepływów pracy i te same [łączniki](../connectors/apis-list.md). 

Automatyzacja i wyłączanie zapewnia pracownikom pakietu Office możliwość wykonywania prostych integracji (na przykład procesu zatwierdzania w bibliotece dokumentów programu SharePoint) bez przechodzenia przez deweloperów lub. Usługa Logic Apps umożliwia także korzystanie z zaawansowanych integracji (na przykład procesów B2B) wymagających usługi Azure DevOps i rozwiązań dotyczących zabezpieczeń na poziomie przedsiębiorstwa. Typowe dla biznesowego przepływu pracy jest zwiększanie się stopnia skomplikowania wraz z upływem czasu. W związku z tym możesz zacząć od przepływu, a następnie przekonwertować go na aplikację logiki odpowiednio do potrzeb.

Poniższa tabela zawiera informacje o tym, czy Automatyzacja lub Logic Apps jest Najlepsza dla danej integracji:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Użytkownicy** |Pracownicy biurowi, użytkownicy biznesowi, administratorzy programu SharePoint |Profesjonalni integratorzy i programiści, informatycy |
| **Scenariusze** |Samoobsługa |Zaawansowane integracje |
| **Narzędzie do projektowania** |Aplikacja w przeglądarce i aplikacja mobilna, tylko interfejs użytkownika |Aplikacja w przeglądarce i program [Visual Studio](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), dostępny [widok kodu](../logic-apps/logic-apps-author-definitions.md) |
| **Zarządzanie cyklem życia aplikacji** |Projektowanie i testowanie w środowiskach nieprodukcyjnych, przenoszenie do środowiska produkcyjnego, gdy aplikacja jest gotowa |Usługa Azure DevOps: kontrola kodu źródłowego, testowanie, pomoc techniczna, automatyzacja oraz możliwości zarządzania w usłudze [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) |
| **Środowisko administratora** |Zarządzanie środowiskami automatyzacji i zasadami ochrony przed utratą danych (DLP), śledzenie licencjonowania: [Centrum administracyjnego](https://admin.flow.microsoft.com) |Zarządzanie grupami zasobów, połączeniami, zarządzanie dostępem i rejestrowaniem: [Azure Portal](https://portal.azure.com) |
| **Bezpieczeństwo** |Microsoft 365 dzienniki inspekcji zabezpieczeń, DLP, [szyfrowanie w spoczynku](https://wikipedia.org/wiki/Data_at_rest#Encryption) dla poufnych danych |Gwarancja bezpieczeństwa platformy Azure: [zabezpieczenia platformy Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [dzienniki inspekcji](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Porównanie usług Azure Functions i Azure Logic Apps

Usługi Functions i Logic Apps to usługi platformy Azure umożliwiające użycie obciążeń bezserwerowych. Azure Functions to bezserwerowa usługa obliczeniowa platformy Azure, natomiast Azure Logic Apps to usługa udostępniająca bezserwerowe przepływy pracy. Obie umożliwiają tworzenie złożonych *orkiestracji*. Aranżacja to zbiór funkcji i kroków, nazywanych *akcjami* w usłudze Logic Apps, które są wykonywane w celu wykonywania złożonego zadania. Na przykład w celu przetworzenia partii zamówień możesz równolegle wykonać wiele wystąpień funkcji, poczekać na zakończenie wszystkich wystąpień, a następnie wykonać funkcję, która obliczy zagregowany wynik.

W przypadku usługi Azure Functions tworzenie aranżacji odbywa się przez pisanie kodu i używanie [rozszerzenia funkcji trwałych](durable/durable-functions-overview.md). Na potrzeby usługi Logic Apps aranżacje są tworzone za pomocą graficznego interfejsu użytkownika lub przez edycję plików konfiguracyjnych.

Można mieszać i dopasowywać usługi podczas tworzenia aranżacji, wywołując usługę Functions z usługi Logic Apps i na odwrót. Wybierz sposób tworzenia poszczególnych aranżacji na podstawie możliwości usług lub własnych preferencji. W poniższej tabeli wymieniono niektóre kluczowe różnice między tymi elementami:

|  | Trwałe funkcje | Logic Apps |
| --- | --- | --- |
| **Opracowywanie zawartości** | Najpierw kod (imperatywne) | Najpierw projektant (deklaratywne) |
| **Połączenia** | [Informacje o dziesiątych typach powiązań wbudowanych](functions-triggers-bindings.md#supported-bindings), napisać kod dla powiązań niestandardowych | [Duża kolekcja łączników](../connectors/apis-list.md), [pakiet INTEGRACYJNY dla przedsiębiorstw dla scenariuszy B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [Tworzenie łączników niestandardowych](../logic-apps/custom-connector-overview.md) |
| **Akcje** | Każde działanie jest funkcją platformy Azure; pisanie kodu dla funkcji działań |[Duża kolekcja gotowych do użycia akcji](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Monitorowanie** | [Application Insights platformy Azure](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [dzienniki Azure monitor](../logic-apps/monitor-logic-apps.md)|
| **Zarządzanie** | [Interfejs API REST](durable/durable-functions-http-api.md), [program Visual Studio](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2019) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [interfejs API REST](/rest/api/logic/), program [PowerShell](/powershell/module/az.logicapp), [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) |
| **Kontekst wykonywania** | Może działać [lokalnie](functions-runtime-overview.md) lub w chmurze | Działa tylko w chmurze|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Porównanie usług Functions i WebJobs

Podobnie jak usługa Azure Functions, usługa Azure App Service WebJobs z zestawem SDK usługi WebJobs jest usługą integracji typu *najpierw kod* przeznaczoną dla deweloperów. Obie bazują na usłudze [Azure App Service](../app-service/overview.md) i obsługują funkcje, takie jak [integracja kontroli kodu źródłowego](../app-service/deploy-continuous-deployment.md), [uwierzytelnianie](../app-service/overview-authentication-authorization.md) i [monitorowanie z integracją usługi Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Usługa WebJobs i zestaw WebJobs SDK

Funkcja *WebJobs* usługi App Service umożliwia uruchamianie skryptu lub kodu w kontekście aplikacji internetowej usługi App Service. *Zestaw SDK usługi WebJobs* to struktura przeznaczona dla usługi WebJobs, która upraszcza kod pisany w celu reagowania na zdarzenia w usługach platformy Azure. Na przykład możesz zareagować na utworzenie obiektu blob obrazu w usłudze Azure Storage, tworząc obraz miniatury. Zestaw SDK usługi WebJobs działa jako aplikacja konsoli .NET, którą można wdrożyć w zadaniu WebJob. 

Usługa WebJobs i zestaw SDK usługi WebJobs najlepiej działają razem, ale można używać usługi WebJobs bez zestawu SDK usługi WebJobs i na odwrót. Zadanie WebJob może uruchomić dowolny program lub skrypt działający w piaskownicy usługi App Service. Aplikację konsolową zestawu SDK usługi WebJobs można uruchomić w każdym miejscu, w którym jest uruchomiona aplikacja konsolowa, np. na serwerach lokalnych.

### <a name="comparison-table"></a>Tabela porównawcza

Usługa Azure Functions bazuje na zestawie SDK usługi WebJobs, dlatego współużytkuje z innymi usługami platformy Azure liczne wyzwalacze i połączenia. Poniżej przedstawiono kilka czynników, które należy wziąć pod uwagę, wybierając między usługą Azure Functions a usługą WebJobs z zestawem WebJobs SDK:

|  | Funkcje | Usługa WebJobs z zestawem SDK usługi WebJobs |
| --- | --- | --- |
|**[Model aplikacji bez serwera](https://azure.microsoft.com/solutions/serverless/) z [automatycznym skalowaniem](event-driven-scaling.md)**|✔||
|**[Opracowywanie i testowanie w przeglądarce](./functions-get-started.md)** |✔||
|**[Płatność za użycie](consumption-plan.md)**|✔||
|**[Integracja z usługą Logic Apps](functions-twitter-email.md)**|✔||
| **Zdarzenia wyzwalacza** |[Czasomierz](functions-bindings-timer.md)<br>[Kolejki i obiekty blob usługi Azure Storage](functions-bindings-storage-blob.md)<br>[Kolejki i tematy usługi Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Czasomierz](functions-bindings-timer.md)<br>[Kolejki i obiekty blob usługi Azure Storage](functions-bindings-storage-blob.md)<br>[Kolejki i tematy usługi Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[System plików](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| **Obsługiwane języki**  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|**Menedżery pakietów**|NPM i NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Zadania WebJob (bez zestawu SDK WebJob) obsługują Języki C#, Java, JavaScript, bash, cmd, bat, POWERSHELL, php, TypeScript, Python i innych. To nie jest pełna lista. Zadanie WebJob może uruchomić dowolny program lub skrypt, który można uruchomić w piaskownicy usługi App Service.

<sup>2</sup> Usługa WebJobs (bez zestawu WebJobs SDK) obsługuje narzędzia NPM i NuGet.

### <a name="summary"></a>Podsumowanie

Usługa Azure Functions zapewnia większą efektywność pracy deweloperów niż usługa Azure App Service WebJobs. Udostępnia ona także więcej opcji dotyczących języków programowania, środowisk programistycznych, integracji usług platformy Azure i cen. W przypadku większości scenariuszy jest najlepszym wyborem.

Poniżej przedstawiono dwa scenariusze, w których usługa WebJobs może być najlepszym wyborem:

* Potrzebujesz większej kontroli nad kodem nasłuchującym zdarzeń, obiektem `JobHost`. Usługa Functions oferuje ograniczoną liczbę sposobów dostosowywania zachowania obiektu `JobHost` w pliku [host.json](functions-host-json.md). Czasami trzeba wykonać czynności, których nie można określić za pomocą ciągu w pliku JSON. Na przykład tylko zestaw SDK usługi WebJobs pozwala skonfigurować niestandardowe zasady ponawiania dla usługi Azure Storage.
* Masz aplikację usługi App Service, dla której chcesz uruchamiać fragmenty kodu, i chcesz zarządzać nimi razem w tym samym środowisku usługi Azure DevOps.

W innych scenariuszach, w których chcesz uruchamiać fragmenty kodu na potrzeby integracji platformy Azure lub usług innych firm, wybierz usługę Azure Functions, a nie usługę WebJobs z zestawem SDK usługi WebJobs.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Zaawansowane Automatyzacja, Logic Apps, funkcje i Zadania WebJob

Nie trzeba wybierać tylko jednej z tych usług. Integrują się one ze sobą tak dobrze, jak z usługami zewnętrznymi.

Przepływ może wywołać aplikację logiki. Aplikacja logiki może wywołać funkcję, a funkcja może wywołać aplikację logiki. Zobacz na przykład [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](functions-twitter-email.md).

Integracja między automatyzacją, Logic Apps i funkcjami w dalszym ciągu zwiększa się w miarę upływu czasu. Możesz utworzyć jakiś element w jednej usłudze i używać go w innych usługach.

Więcej informacji na temat tych usług integracji możesz uzyskać, korzystając z następujących linków:

* [Korzystanie z usług Azure Functions i Azure App Service w scenariuszach integracji (autor: Christopher Anderson)](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Uproszczone integracje (autor: Charles Lamanna)](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps — emisja internetowa na żywo](https://aka.ms/logicappslive)
* [Automatyzacja często zadawanych pytań](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy od utworzenia pierwszego przepływu, aplikacji logiki lub funkcji aplikacji. Wybierz dowolny z następujących linków:

* [Rozpoczynanie pracy z usługą Power Automate](/power-automate/getting-started)
* [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Tworzenie pierwszej funkcji platformy Azure](./functions-get-started.md)