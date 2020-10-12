---
title: Przykłady & typowych scenariuszach
description: Znajdź przykłady, typowe scenariusze, samouczki i wskazówki dotyczące Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 207b597bc865c8234d447759ab8b0f53dc35413c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090284"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Typowe scenariusze, przykłady, samouczki i przewodniki dotyczące Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ułatwia organizację i integrację różnych usług, zapewniając [setki gotowych do użycia łączników](../connectors/apis-list.md), od SQL Server lub SAP do platformy Azure Cognitive Services. Usługa Logic Apps jest "bezserwerowa", więc nie trzeba martwić się o skalowanie ani wystąpienia. Wystarczy zdefiniować przepływ pracy z wyzwalaczem i akcjami wykonywanymi przez przepływ pracy. Bazowa Platforma obsługuje skalowanie, dostępność i wydajność. Logic Apps jest szczególnie przydatna w przypadku przypadków użycia i scenariuszy, w których należy koordynować akcje w wielu systemach i usługach.

Aby ułatwić zapoznanie się z możliwościami i wzorcami obsługiwanymi przez Azure Logic Apps, w tym artykule opisano typowe punkty wyjścia, przykłady i scenariusze.

## <a name="common-starting-points-for-logic-app-workflows"></a>Wspólne punkty wyjścia dla przepływów pracy aplikacji logiki

Każda aplikacja logiki rozpoczyna się od [*wyzwalacza*](../logic-apps/logic-apps-overview.md#logic-app-concepts)i tylko jeden wyzwalacz, który uruchamia przepływ pracy aplikacji logiki i przekazuje dane w ramach tego wyzwalacza. Niektóre łączniki udostępniają wyzwalacze, które są dostępne w następujących typach:

* *Wyzwalacze sondowania*: regularnie sprawdza punkt końcowy usługi pod kątem nowych danych. Gdy istnieją nowe dane, wyzwalacz tworzy i uruchamia nowe wystąpienie przepływu pracy z danymi jako dane wejściowe.

* *Wyzwalacze wypychania*: nasłuchuje danych w punkcie końcowym usługi i czeka, aż wystąpi określone zdarzenie. Gdy wystąpi zdarzenie, wyzwalacz jest uruchamiany natychmiast, tworząc i uruchamiając nowe wystąpienie przepływu pracy, które używa dowolnych dostępnych danych jako danych wejściowych.

Oto przykłady opisujące często używane wyzwalacze:

* Wyzwalacze *sondowania* :

  * [Wyzwalacz **cyklu** ](../connectors/connectors-native-recurrence.md) pozwala ustawić datę i godzinę rozpoczęcia oraz cykl uruchamiania aplikacji logiki. Na przykład możesz wybrać dni tygodnia i godziny, w których ma być wyzwalana aplikacja logiki. Więcej informacji można znaleźć w następujących tematach:<p>

    * [Planowanie i uruchamianie cyklicznych automatycznych zadań, procesów i przepływów pracy przy użyciu usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Samouczek: Tworzenie zautomatyzowanych, cyklicznych przepływów pracy opartych na harmonogramie przy użyciu Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Wyzwalacz **po odebraniu wiadomości e-mail** umożliwia aplikacji logiki Sprawdzanie nowej wiadomości e-mail od dowolnego dostawcy poczty obsługiwanego przez Logic Apps, na przykład [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/)i tak dalej.

    > [!IMPORTANT]
    > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

    Więcej informacji można znaleźć w następujących tematach:<p>

    * [Samouczek: tworzenie automatycznych przepływów pracy opartych na zatwierdzaniu przy użyciu Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Samouczek: Automatyzowanie zadań do przetwarzania wiadomości e-mail przy użyciu Azure Logic Apps, Azure Functions i usługi Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Wyzwalacz [ **http** ](../connectors/connectors-native-http.md) może wywoływać punkt końcowy usługi za pośrednictwem protokołu HTTP lub https. Aby uzyskać więcej informacji, zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych http](../logic-apps/logic-apps-http-endpoint.md).

* Wyzwalacze *wypychania* :

  * Wyzwalacz [ **żądania** ](../connectors/connectors-native-reqres.md) może odbierać przychodzące żądania HTTPS.

  * Wyzwalacz [ **elementu webhook protokołu HTTP** ](../connectors/connectors-native-webhook.md) subskrybuje punkt końcowy usługi przez zarejestrowanie *adresu URL wywołania zwrotnego* przy użyciu tej usługi. Dzięki temu usługa może po prostu powiadomić wyzwalacz, gdy wystąpi określone zdarzenie, aby wyzwalacz nie musiał sondować usługi.

Po wystąpieniu określonego zdarzenia wyzwalany jest wyzwalacz, który tworzy nowe wystąpienie przepływu pracy aplikacji logiki i uruchamia akcje w przepływie pracy. Możesz uzyskać dostęp do dowolnych danych z wyzwalacza w przepływie pracy. Na przykład, serwis Twitter **na nowym** wyzwoleniu przekaże zawartość tweetu do przebiegu aplikacji logiki. Aby rozpocząć pracę z Azure Logic Apps, wypróbuj następujące tematy szybkiego startu:

* [Szybki Start: Tworzenie pierwszego zautomatyzowanego przepływu pracy przy użyciu Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Szybki Start: Tworzenie zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Szybki Start: tworzenie automatycznych przepływów pracy aplikacji logiki i zarządzanie nimi przy użyciu Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Przepływ sterowania i możliwości obsługi błędów

Aplikacje logiki obejmują bogate możliwości zaawansowanego przepływu sterowania, takie jak warunki, przełączniki, pętle i zakresy. Aby zapewnić odporne rozwiązania, można również zaimplementować obsługę błędów i wyjątków w przepływach pracy.

* Wykonywanie różnych akcji na podstawie [instrukcji warunkowych](../logic-apps/logic-apps-control-flow-conditional-statement.md) i [instrukcji switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Powtórz kroki lub przetwórz elementy w tablicach i kolekcjach za pomocą pętli](../logic-apps/logic-apps-control-flow-loops.md)
* [Grupuj akcje razem z zakresami](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Dodawanie obsługi błędów i wyjątków do przepływu pracy](../logic-apps/logic-apps-exception-handling.md)
* [Przypadek użycia: jak firma usługi opieki zdrowotnej używa obsługi wyjątków aplikacji logiki dla przepływów pracy HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Tworzenie niestandardowych interfejsów API i łączników

W przypadku systemów i usług, które nie mają opublikowanych łączników, można także rozciągnąć Aplikacje logiki.

* [Tworzenie niestandardowych interfejsów API do wywołania z Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Regularnie sprawdzaj dostępność nowych danych lub zdarzeń przy użyciu wzorca wyzwalacza sondowania](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Zaczekaj i nasłuchuje nowych danych lub zdarzeń za pomocą wzorca wyzwalacza elementu webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Wykonywanie długotrwałych zadań przy użyciu wzorca akcji sondowania](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Wykonywanie długotrwałych zadań przy użyciu wzorca akcji elementu webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Łączniki niestandardowe w Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Tworzenie rozwiązań między firmami (B2B, Business-to-Business)

W przypadku rozwiązań integracji dla przedsiębiorstw i bezproblemowej komunikacji między organizacjami można tworzyć zautomatyzowane, skalowalne przepływy pracy dla tych scenariuszy przy użyciu Pakiet integracyjny dla przedsiębiorstw (EIP) z Azure Logic Apps. Chociaż organizacje używają różnych protokołów i formatów, mogą wymieniać wiadomości elektronicznie. Element EIP przekształca różne formaty w format, który systemy organizacji mogą przetwarzać i obsługuje protokoły standardowe branżowe, w tym AS2, X12, EDIFACT i RosettaNet. Aby skompilować te rozwiązania, należy utworzyć konto integracji, czyli osobny zasób platformy Azure, który zapewnia bezpieczny, skalowalny i zarządzany kontener dla artefaktów, które są definiowane i używane z przepływami pracy aplikacji logiki. Na przykład artefakty obejmują partnerów handlowych, umowy, mapy, schematy, certyfikaty i konfiguracje usługi Batch.

* [Przegląd: rozwiązania B2B Enterprise Integration z Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Tworzenie kont integracji na potrzeby integracji przedsiębiorstw B2B i zarządzanie nimi w usłudze Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Dostęp do zasobów usługi Azure Virtual Network

Czasami Aplikacje logiki i konta integracji muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne i inne systemy i usługi, które znajdują się w sieci wirtualnej platformy Azure. Aby skonfigurować ten dostęp, można utworzyć środowisko usługi integracji (ISE), w którym można kompilować i uruchamiać aplikacje logiki. ISE jest prywatnym i izolowanym wystąpieniem usługi Logic Apps, która używa dedykowanych zasobów, takich jak magazyn, i działa niezależnie od publicznej, "globalnej" usługi Logic Apps wielodostępnej. Oddzielenie wyizolowanego wystąpienia prywatnego i publicznego wystąpienia globalnego pomaga również ograniczyć wpływ innych dzierżawców platformy Azure na wydajność aplikacji, które są również znane jako efekt "sąsiedzi".

* [Przegląd: dostęp do zasobów sieci wirtualnej platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Łączenie z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Wdrażanie i monitorowanie aplikacji logiki oraz zarządzanie nimi

Możesz w pełni opracowywać i wdrażać aplikacje logiki za pomocą programu Visual Studio, platformy Azure DevOps lub dowolnej innej kontroli źródła i zautomatyzowanych narzędzi kompilacji. Aby obsługiwać wdrożenie dla przepływów pracy i połączeń zależnych w szablonie zasobu, Aplikacje logiki używają szablonów wdrażania zasobów platformy Azure. Narzędzia Visual Studio Tools automatycznie generują te szablony, które można zaewidencjonować do kontroli źródła w celu przechowywania wersji. W przypadku dzienników powiadomień i diagnostyki dla stanu przebiegu przepływu pracy Azure Logic Apps udostępnia również monitorowanie i alerty.

### <a name="deploy"></a>Wdrażanie

* [Szybki Start: Tworzenie zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Przegląd: Automatyzowanie wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Tworzenie szablonów usługi Azure Resource Manager w celu zautomatyzowania wdrożenia usługi Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Wdrażanie szablonów usługi Azure Resource Manager dla usługi Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Przykład: łączenie z kolejkami Azure Service Bus z Azure Logic Apps i wdrażania przy użyciu Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: łączenie z kontami usługi Azure Storage z Azure Logic Apps i wdrażanie za pomocą Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Konfigurowanie akcji aplikacji funkcji dla Azure Logic Apps i wdrażanie za pomocą Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Nawiązywanie połączenia z kontem integracji z Azure Logic Apps i wdrażanie za pomocą Azure Pipelines na platformie Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Przykład: organizowanie Azure Pipelines przy użyciu Azure Logic Apps](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Zarządzanie

* [Zarządzanie aplikacjami logiki przy użyciu programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Tworzenie kont integracji dla integracji z przedsiębiorstwem B2B i zarządzanie nimi](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Zarządzanie środowiskiem usługi integracji (ISE) w Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitorowanie

* [Monitoruj stan przebiegu, przeglądaj historię wyzwalaczy i konfiguruj alerty w usłudze Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Konfigurowanie Azure Monitor dzienników i zbieranie danych diagnostycznych dla Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dotyczących komunikatów B2B w usłudze Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Wyświetlaj i twórz zapytania dotyczące monitorowania i śledzenia w Azure Monitor dziennikach dla Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Obsługa typów zawartości, konwersji i transformacji

Można uzyskać dostęp do, konwertować i przetwarzać wiele typów zawartości za pomocą wielu funkcji w Azure Logic Apps [języku definicji przepływu pracy](https://aka.ms/logicappsdocs). Na przykład można dokonać konwersji między ciągami, JSON i XML za pomocą `@json()` `@xml()` wyrażeń i. Aparat Logic Apps zachowuje typy zawartości obsługujące transfer zawartości w sposób bezstratny między usługami.

* [Obsługa typów zawartości w Azure Logic Apps](../logic-apps/logic-apps-content-type.md), takich jak `application/` , `application/octet-stream` , i `multipart/formdata`
* [Przewodnik referencyjny dotyczący używania funkcji w wyrażeniach dla Azure Logic Apps i automatyzacji](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schemat języka definicji przepływu pracy dla Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Inne integracje i możliwości

Azure Logic Apps integruje się z wieloma usługami, takimi jak Azure Functions, API Management platformy Azure, Azure App Service i niestandardowe punkty końcowe HTTP, na przykład REST i SOAP.

* [Wywołaj Azure Functions z Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Samouczek: wywoływanie lub wyzwalanie aplikacji logiki przy użyciu Azure Functions i Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Samouczek: tworzenie pulpitu nawigacyjnego usługi przesyłania strumieniowego dla usługi Customer Insights przy użyciu Azure Logic Apps i Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Samouczek: Tworzenie funkcji, która integruje się z usługami Azure Logic Apps i Azure Cognitive Services do analizowania tonacji post usługi Twitter](../azure-functions/functions-twitter-email.md)
* [Samouczek: tworzenie pulpitu nawigacyjnego społecznościowego z systemem AI przy użyciu Power BI i Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Samouczek: Monitorowanie zmian maszyn wirtualnych przy użyciu usług Azure Event Grid i Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Samouczek: zdalne monitorowanie i powiadomienia w usłudze IoT przy użyciu Azure Logic Apps łączenia Centrum IoT i skrzynki pocztowej](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: wywoływanie usług SOAP przy użyciu Azure Logic Apps](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Kompleksowe scenariusze

* [Oficjalny dokument: integracja kompleksowego zarządzania przypadkami z usługami platformy Azure, takimi jak Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Historie klientów

Dowiedz się, w jaki sposób Azure Logic Apps, wraz z innymi usługami platformy Azure i produktami firmy Microsoft, które pomogły zwiększyć elastyczność i skupić się na [podstawowych firmach](https://aka.ms/logic-apps-customer-stories) dzięki uproszczeniu, organizowaniu, automatyzowaniu i organizowania złożonych procesów.

## <a name="next-steps"></a>Następne kroki

* Więcej informacji [na temat łączników dla Logic Apps](../connectors/apis-list.md)
* Dowiedz się więcej [na temat scenariuszy integracji B2B Enterprise z Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
