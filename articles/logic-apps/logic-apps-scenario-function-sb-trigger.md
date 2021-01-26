---
title: Wywoływanie aplikacji logiki za pomocą usługi Azure Functions
description: Wywołaj lub Wyzwól Aplikacje logiki przy użyciu Azure Functions i Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a7df9ba1318f40de8af392cfaedbe51d7a5df755
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784938"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Wywołaj lub Wyzwól Aplikacje logiki przy użyciu Azure Functions i Azure Service Bus

Możesz użyć [Azure Functions](../azure-functions/functions-overview.md) , aby wyzwolić aplikację logiki, gdy zachodzi potrzeba wdrożenia długotrwałego odbiornika lub zadania. Na przykład można utworzyć funkcję, która nasłuchuje w kolejce [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) i natychmiast uruchamia aplikację logiki jako wyzwalacz wypychania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Przestrzeń nazw Azure Service Bus. Jeśli nie masz przestrzeni nazw, [najpierw utwórz swój obszar nazw](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Aplikacja funkcji, która jest kontenerem dla funkcji. Jeśli nie masz aplikacji funkcji, [najpierw Utwórz aplikację funkcji](../azure-functions/functions-get-started.md)i upewnij się, że wybrano pozycję .NET jako stos środowiska uruchomieniowego.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

W tym scenariuszu masz funkcję uruchamiającego każdą aplikację logiki, która ma zostać wyzwolona. Najpierw Utwórz aplikację logiki, która rozpoczyna się od wyzwalacza żądania HTTP. Funkcja wywołuje ten punkt końcowy za każdym razem, gdy zostanie odebrany komunikat kolejki.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Utwórz pustą aplikację logiki.

   Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj się z [przewodnikiem Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W polu wyszukiwania wpisz `http request`. Z listy Wyzwalacze wybierz wyzwalacz **po odebraniu żądania HTTP** .

   ![Wybierz wyzwalacz](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Przy użyciu wyzwalacza żądania można opcjonalnie wprowadzić schemat JSON, który ma być używany z komunikatem kolejki. Schematy JSON pomagają projektantowi aplikacji logiki zrozumieć strukturę danych wejściowych i ułatwiają korzystanie z nich w przepływie pracy.

1. Aby określić schemat, wprowadź schemat w polu **schemat JSON treści żądania** , na przykład:

   ![Określ schemat JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Jeśli nie masz schematu, ale masz przykładowy ładunek w formacie JSON, możesz wygenerować schemat z tego ładunku.

   1. W wyzwalaczu żądania wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W obszarze **wprowadzanie lub wklejanie przykładowego ładunku JSON** wprowadź przykładowy ładunek, a następnie wybierz pozycję **gotowe**.

      ![Wprowadź przykładowy ładunek](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Ten przykładowy ładunek generuje ten schemat, który pojawia się w wyzwalaczu:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Dodaj inne akcje, które mają być uruchamiane po odebraniu komunikatu w kolejce.

   Na przykład możesz wysłać wiadomość e-mail za pomocą łącznika programu Outlook dla pakietu Office 365.

1. Zapisz aplikację logiki, która generuje adres URL wywołania zwrotnego dla wyzwalacza w tej aplikacji logiki. Później Użyj tego adresu URL wywołania zwrotnego w kodzie dla wyzwalacza kolejki Azure Service Bus.

   Adres URL wywołania zwrotnego jest wyświetlany w właściwości **http post adresu URL** .

   ![Wygenerowany adres URL wywołania zwrotnego dla wyzwalacza](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>Tworzenie funkcji

Następnie Utwórz funkcję, która działa jako wyzwalacz i nasłuchuje kolejki.

1. W Azure Portal Otwórz i rozwiń swoją aplikację funkcji, jeśli nie została jeszcze otwarta. 

1. W obszarze Nazwa aplikacji funkcji rozwiń pozycję **funkcje**. W okienku **funkcje** wybierz pozycję **Nowa funkcja**.

   ![Rozwiń "funkcje" i wybierz pozycję "Nowa funkcja"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Wybierz ten szablon na podstawie tego, czy utworzono nową aplikację funkcji, w której wybrano platformę .NET jako stos środowiska uruchomieniowego, czy używasz istniejącej aplikacji funkcji.

   * W przypadku nowych aplikacji funkcji wybierz ten szablon: **Service Bus wyzwalacz kolejki**

     ![Wybierz szablon dla nowej aplikacji funkcji](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * W przypadku istniejącej aplikacji funkcji wybierz ten szablon: **Service Bus wyzwalacza kolejki — C#**

     ![Wybieranie szablonu istniejącej aplikacji funkcji](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. W okienku **Azure Service Bus wyzwalacza kolejki** Podaj nazwę wyzwalacza i skonfiguruj **połączenie Service Bus** dla kolejki, która używa odbiornika zestawu Azure Service Bus SDK `OnMessageReceive()` , a następnie wybierz pozycję **Utwórz**.

1. Napisz podstawową funkcję do wywołania utworzonego wcześniej punktu końcowego aplikacji logiki przy użyciu komunikatu kolejki jako wyzwalacza. Przed zapisaniem funkcji zapoznaj się z następującymi kwestiami:

   * W tym przykładzie jest użyty `application/json` Typ zawartości komunikatu, ale w razie potrzeby można zmienić ten typ.
   
   * Ze względu na możliwe współbieżnie uruchomione funkcje, duże ilości lub duże obciążenia, należy unikać tworzenia wystąpienia [klasy HttpClient](/dotnet/api/system.net.http.httpclient) za pomocą `using` instrukcji i bezpośrednio tworzyć wystąpienia HttpClient na żądanie. Aby uzyskać więcej informacji, zobacz [Używanie HttpClientFactory do implementowania odpornych żądań HTTP](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Jeśli to możliwe, ponownie Użyj wystąpienia klientów HTTP. Aby uzyskać więcej informacji, zobacz [Zarządzanie połączeniami w Azure Functions](../azure-functions/manage-connections.md).

   W tym przykładzie zastosowano [ `Task.Run` metodę](/dotnet/api/system.threading.tasks.task.run) w trybie [asynchronicznym](/dotnet/csharp/language-reference/keywords/async) . Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne z Async i await](/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Aby przetestować funkcję, Dodaj komunikat w kolejce przy użyciu narzędzia, takiego jak [eksplorator Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

   Aplikacja logiki jest wyzwalana natychmiast po odebraniu komunikatu przez funkcję.

## <a name="next-steps"></a>Następne kroki

* [Wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md)