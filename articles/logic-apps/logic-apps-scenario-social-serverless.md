---
title: Utwórz pulpit nawigacyjny usługi Customer Insights
description: Zarządzaj opiniami klientów, danymi mediów społecznościowych i innymi, tworząc pulpit nawigacyjny klienta za pomocą Azure Logic Apps i Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 22e0c7304f7a53a86bc5c6739a2061352d738d29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784816"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Utwórz pulpit nawigacyjny usługi przesyłania strumieniowego na platformie Customer Insights zawierający Azure Logic Apps i Azure Functions

Platforma Azure oferuje narzędzia [bezserwerowe](https://azure.microsoft.com/solutions/serverless/) , które ułatwiają szybkie tworzenie i hostowanie aplikacji w chmurze bez konieczności zaopiniowania infrastruktury. W tym samouczku możesz utworzyć pulpit nawigacyjny, który wyzwala Opinie klientów, analizuje Opinie z uczeniem maszynowym i publikuje szczegółowe informacje w źródle, na przykład Power BI lub Azure Data Lake.

W przypadku tego rozwiązania są używane następujące kluczowe składniki platformy Azure dla aplikacji bezserwerowych: [Azure Functions](https://azure.microsoft.com/services/functions/) i [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps zapewnia bezserwerowy aparat przepływu pracy w chmurze, dzięki czemu można tworzyć aranżacje między składnikami bezserwerowymi i łączyć się z 200 usługami i interfejsami API. Azure Functions zapewnia serwerowe przetwarzanie danych w chmurze. To rozwiązanie używa Azure Functions do oflagowania tweetów klientów na podstawie wstępnie zdefiniowanych słów kluczowych.

W tym scenariuszu utworzysz aplikację logiki, która wyzwala wyszukiwanie opinii klientów. Niektóre łączniki, które pomagają odpowiedzieć na Opinie klientów, obejmują Outlook.com, pakiet Office 365, badanie małp, serwis Twitter i [żądanie HTTP z formularza sieci Web](/archive/blogs/logicapps/calling-a-logic-app-from-an-html-form). Utworzony przez Ciebie przepływ pracy monitoruje element hasztagów w serwisie Twitter.

Można [skompilować całe rozwiązanie w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) i [wdrożyć rozwiązanie przy użyciu szablonu Azure Resource Manager](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). Aby zapoznać się ze sposobem tworzenia tego rozwiązania, [Obejrzyj to wideo w kanale 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Wyzwalanie na danych klienta

1. W Azure Portal lub Visual Studio Utwórz pustą aplikację logiki. 

   Jeśli jesteś nowym sposobem korzystania z usługi Logic Apps, zapoznaj się z [przewodnikiem Szybki Start dla Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) lub [przewodnika Szybki Start dla programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. W Projektancie aplikacji logiki Znajdź i Dodaj wyzwalacz usługi Twitter, który ma tę akcję: **po opublikowaniu nowego tweetu**

3. Skonfiguruj wyzwalacz, aby nasłuchiwać tweetów opartych na słowie kluczowym lub nagłówku.

   W przypadku wyzwalaczy opartych na sondach, takich jak wyzwalacz usługi Twitter, właściwość cykl określa, jak często aplikacja logiki sprawdza, czy są nowe elementy.

   ![Przykład wyzwalacza usługi Twitter][1]

Ta aplikacja logiki jest teraz uruchamiana na wszystkich nowych Tweetach. Następnie możesz wykonać i przeanalizować dane tweetu, aby lepiej zrozumieć mowąe. 

## <a name="analyze-tweet-text"></a>Analizuj tekst tweetu

Aby wykryć tonacji za jakiś tekst, możesz użyć [usługi Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. W Projektancie aplikacji logiki w obszarze wyzwalacza wybierz pozycję **nowy krok**.

2. Znajdź łącznik **Analiza tekstu** .

3. Wybierz akcję **Wykryj tonacji** .

4. Jeśli zostanie wyświetlony monit, Podaj prawidłowy klucz Cognitive Services dla usługi analiza tekstu.

5. W obszarze **treść żądania** wybierz pole **tekst tweetu** , które zawiera tekst tweetu jako dane wejściowe dla analizy.

Po otrzymaniu danych tweetów i szczegółowych informacji dotyczących tweetu możesz teraz użyć kilku innych odpowiednich łączników i ich działań:

* **Power BI Dodaj wiersze do zestawu danych przesyłania strumieniowego**: Wyświetl przychodzące Tweety na pulpicie nawigacyjnym Power BI.
* **Azure Data Lake — Dołącz plik**: Dodaj dane klienta do Azure Data Lake zestawu danych, aby dołączyć do zadań analitycznych.
* **SQL — Dodawanie wierszy**: przechowywanie danych w bazie danych na potrzeby późniejszego pobierania.
* **Zapasowy — Wyślij wiadomość**: Powiadom kanał zapasowy o negatywnych opiniach, które mogą wymagać akcji.

Możesz również utworzyć i funkcję platformy Azure, aby umożliwić wykonywanie niestandardowych operacji przetwarzania danych. 

## <a name="process-data-with-azure-functions"></a>Przetwarzanie danych za pomocą Azure Functions

Przed utworzeniem funkcji Utwórz aplikację funkcji w ramach subskrypcji platformy Azure. Ponadto w celu bezpośredniego wywołania funkcji przez aplikację logiki funkcja musi mieć powiązanie wyzwalacza HTTP, na przykład użyć szablonu **HttpTrigger** . Dowiedz się [, jak utworzyć pierwszą aplikację funkcji i funkcję w Azure Portal](../azure-functions/functions-get-started.md).

W tym scenariuszu tekst tweetu jest używany jako treść żądania dla funkcji platformy Azure. W kodzie funkcji Zdefiniuj logikę, która określa, czy tekst tweetu zawiera słowo kluczowe lub frazę. Zachowanie funkcji jako prostej lub złożonej w miarę potrzeb dla scenariusza.
Na końcu funkcji Zwróć odpowiedź do aplikacji logiki z niektórymi danymi, na przykład prostą wartością logiczną, taką jak `containsKeyword` lub obiektem złożonym.

> [!TIP]
> Aby uzyskać dostęp do złożonej odpowiedzi z funkcji w aplikacji logiki, użyj akcji **Przeanalizuj dane JSON** .

Gdy wszystko będzie gotowe, Zapisz funkcję, a następnie Dodaj funkcję jako akcję w aplikacji logiki, która jest tworzona.

## <a name="add-azure-function-to-logic-app"></a>Dodaj funkcję platformy Azure do aplikacji logiki

1. W Projektancie aplikacji logiki w obszarze **Wykryj tonacji** wybierz pozycję **nowy krok**.

2. Znajdź łącznik **Azure Functions** , a następnie wybierz utworzoną funkcję.

3. W obszarze **treść żądania** wybierz pozycję **tekst tweetu**.

![Skonfigurowany krok funkcji platformy Azure][2]

## <a name="run-and-monitor-your-logic-app"></a>Uruchamianie i monitorowanie aplikacji logiki

Aby przejrzeć wszystkie bieżące lub poprzednie uruchomienia aplikacji logiki, można użyć rozbudowanych możliwości debugowania i monitorowania, które Azure Logic Apps dostępne w Azure Portal, Visual Studio lub za pomocą interfejsów API REST platformy Azure i zestawów SDK.

Aby łatwo przetestować aplikację logiki, w Projektancie aplikacji logiki wybierz **Uruchom wyzwalacz**. Wyzwalacz sonduje Tweety na podstawie określonego harmonogramu, aż zostanie znaleziony Tweet spełniający Twoje kryteria. Podczas wykonywania, Projektant wyświetla widok na żywo dla tego przebiegu.

Aby wyświetlić poprzednie historie uruchamiania w programie Visual Studio lub Azure Portal: 

* Otwórz program Visual Studio Cloud Explorer. Znajdź aplikację logiki, otwórz menu skrótów aplikacji. Wybierz pozycję **Otwórz historię uruchamiania**.

  > [!TIP]
  > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

* W Azure Portal Znajdź aplikację logiki. W menu aplikacji logiki wybierz pozycję **Przegląd**. 

## <a name="create-automated-deployment-templates"></a>Tworzenie szablonów automatycznego wdrażania

Po utworzeniu rozwiązania aplikacji logiki można przechwycić i wdrożyć aplikację jako [szablon Azure Resource Manager](../azure-resource-manager/templates/overview.md) w dowolnym regionie świadczenia usługi Azure na całym świecie. Można użyć tej funkcji zarówno do modyfikacji parametrów do tworzenia różnych wersji aplikacji i integracji rozwiązania do Azure Pipelines. Możesz również uwzględnić Azure Functions w szablonie wdrożenia, aby można było zarządzać całym rozwiązaniem przy użyciu wszystkich zależności jako jednego szablonu. Dowiedz się, jak [zautomatyzować wdrażanie aplikacji logiki](logic-apps-azure-resource-manager-templates-overview.md).

Przykładowy szablon wdrożenia z funkcją platformy Azure można znaleźć w [repozytorium szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Następne kroki

* [Znajdź inne przykłady i scenariusze dotyczące Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png