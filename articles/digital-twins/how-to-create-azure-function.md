---
title: Konfigurowanie funkcji platformy Azure do przetwarzania danych
titleSuffix: Azure Digital Twins
description: Zobacz, jak utworzyć funkcję platformy Azure, która może uzyskiwać dostęp do programu Digital bliźniaczych reprezentacji i uruchamiać ją.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3b416e6ccb035ede06a360c2697a9b20ca417d98
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613194"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Łączenie Azure Functions aplikacji do przetwarzania danych

W trakcie okresu zapoznawczego aktualizowanie cyfrowego bliźniaczych reprezentacji na podstawie danych jest obsługiwane przy użyciu [**tras zdarzeń**](concepts-route-events.md) za pośrednictwem zasobów obliczeniowych, takich jak [Azure Functions](../azure-functions/functions-overview.md). Funkcja platformy Azure może być używana do aktualizowania wielocyfrowego przędzy w odpowiedzi na:
* dane telemetryczne urządzenia pochodzące z IoT Hub
* Zmiana właściwości lub inne dane pochodzące z innego cyfrowego sznurka w grafie bliźniaczym

W tym artykule przedstawiono sposób tworzenia funkcji platformy Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji. 

Poniżej przedstawiono przegląd kroków, które zawiera:

1. Tworzenie aplikacji Azure Functions w programie Visual Studio
2. Napisz funkcję platformy Azure z wyzwalaczem [Event Grid](../event-grid/overview.md)
3. Dodawanie kodu uwierzytelniania do funkcji (aby można było uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji)
4. Publikowanie aplikacji funkcji na platformie Azure
5. Skonfiguruj dostęp do [zabezpieczeń](concepts-security.md) . Aby funkcja platformy Azure mogła odbierać token dostępu w czasie wykonywania w celu uzyskania dostępu do usługi, należy skonfigurować tożsamość usługi zarządzanej dla aplikacji funkcji.

W pozostałej części tego artykułu przedstawiono kroki konfiguracji funkcji platformy Azure, po jednej naraz.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Tworzenie aplikacji Azure Functions w programie Visual Studio

W programie Visual Studio 2019 wybierz pozycję *plik > nowy projekt*. Wyszukaj szablon *Azure Functions* , zaznacz go, a następnie naciśnij przycisk Dalej.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Określ nazwę aplikacji funkcji i naciśnij przycisk "Utwórz".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: okno dialogowe Konfigurowanie projektu":::

Wybierz *wyzwalacz Event Grid* i naciśnij przycisk "Utwórz".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: okno dialogowe wyzwalacza projektu funkcji platformy Azure":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Napisz funkcję platformy Azure z wyzwalaczem Event Grid

Poniższy kod tworzy krótką funkcję platformy Azure, której można użyć do rejestrowania zdarzeń: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Jest to podstawowa funkcja platformy Azure.

### <a name="run-and-debug-the-azure-function-app"></a>Uruchamianie i debugowanie aplikacji funkcji platformy Azure

Teraz można kompilować i uruchamiać funkcję. Podczas gdy usługa Azure Functions jest ostatecznie przeznaczona do uruchamiania w chmurze, można również uruchamiać i debugować usługi Azure Functions lokalnie.

Aby uzyskać więcej informacji na ten temat, zobacz [debugowanie Event Grid wyzwalacza lokalnie](../azure-functions/functions-debug-event-grid-trigger-local.md).

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Dodawanie zestawu SDK usługi Azure Digital bliźniaczych reprezentacji do aplikacji funkcji platformy Azure

Aplikacja funkcji współdziała z usługą Azure Digital bliźniaczych reprezentacji za pomocą [biblioteki klienta usługi Azure IoT Digital bliźniaczych dla platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Aby można było korzystać z zestawu SDK, w projekcie należy uwzględnić następujące pakiety:
* `Azure.DigitalTwins.Core`(wersja `1.0.0-preview.2` )
* `Azure.Identity`

Aby konfiguracja potoku zestawu Azure SDK została prawidłowo skonfigurowana dla Azure Functions, potrzebne są również:
* `Azure.Net.Http`
* `Azure.Core`

W zależności od wybranych narzędzi możesz to zrobić za pomocą Menedżera pakietów programu Visual Studio lub `dotnet` narzędzia wiersza polecenia. 

Dodaj następujące instrukcje using do funkcji platformy Azure.

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Dodawanie kodu uwierzytelniania do funkcji platformy Azure

Następnie Dodaj kod uwierzytelniania, który umożliwi funkcji dostęp do usługi Azure Digital bliźniaczych reprezentacji.

Dodaj zmienne do klasy funkcji dla następujących wartości: 
* Identyfikator aplikacji Digital bliźniaczych reprezentacji systemu Azure
* Adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Dobrym sposobem jest odczytanie adresu URL usługi ze zmiennej środowiskowej, a nie kodowanie twarde w funkcji.
* Zmienna statyczna do przechowywania wystąpienia HttpClient. Tworzenie HttpClient jest stosunkowo kosztowne i chcemy unikać wykonywania tych czynności w przypadku każdego wywołania funkcji.

Dodaj również zmienną lokalną wewnątrz funkcji, aby przechowywać wystąpienie klienta Digital bliźniaczych reprezentacji na platformie Azure do projektu funkcji. *Nie* należy określać tej zmiennej statycznej wewnątrz klasy.

Na koniec Zmień sygnaturę funkcji na asynchroniczną.

Po wprowadzeniu tych zmian kod funkcji powinien wyglądać podobnie do poniższego:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Aby aplikacja Functions mogła uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, musi mieć tożsamość zarządzaną przez system i mieć uprawnienia dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Użyj następującego polecenia, aby utworzyć tożsamość zarządzaną przez system. Zwróć uwagę na pole *principalId* w danych wyjściowych.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Użyj wartości *principalId* w poniższym poleceniu, aby przypisać tożsamość aplikacji funkcji do roli *właściciela* dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Aby uzyskać więcej informacji na temat tożsamości zarządzanej, zobacz [jak używać tożsamości zarządzanych dla App Service i Azure Functions](../app-service/overview-managed-identity.md).

Na koniec możesz wprowadzić adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej. Aby uzyskać więcej informacji na ten temat, zobacz [zmienne środowiskowe](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables).

> [!TIP]
> Adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji jest tworzony przez dodanie *https://* do początku *nazwy hosta*cyfrowego wystąpienia bliźniaczych reprezentacji platformy Azure. Aby wyświetlić nazwę hosta wraz ze wszystkimi właściwościami wystąpienia, można uruchomić polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Publikowanie aplikacji funkcji platformy Azure

Aby opublikować aplikację funkcji na platformie Azure, w Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt funkcji (a nie rozwiązanie), a następnie wybierz polecenie *Publikuj ()*.

Zostanie wyświetlona następująca karta:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: publikowanie funkcji — okno dialogowe, Strona 1":::

Wybierz lub Utwórz plan App Service, który ma być używany z Azure Functions. Jeśli nie masz pewności, zacznij korzystać z domyślnego planu zużycia.

> [!IMPORTANT] 
> Publikowanie funkcji platformy Azure wiąże się z dodatkowymi opłatami za subskrypcję, niezależnie od bliźniaczych reprezentacji cyfrowych platformy Azure.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: publikowanie funkcji — okno dialogowe, Strona 2":::

Na następującej stronie wprowadź żądaną nazwę nowej aplikacji funkcji, grupy zasobów i inne szczegóły.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Konfigurowanie dostępu zabezpieczeń do aplikacji funkcji platformy Azure

Szkielet funkcji platformy Azure z wcześniejszych przykładów wymaga, aby token okaziciela był do niego przekazywać, aby można było uwierzytelniać za pomocą usługi Azure Digital bliźniaczych reprezentacji. Aby upewnić się, że ten token okaziciela jest zakończony, musisz skonfigurować [tożsamość usługi zarządzanej (msi)](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji funkcji. Należy to zrobić tylko raz dla każdej aplikacji funkcji.

Aby je skonfigurować, przejdź do [Azure Portal](https://portal.azure.com/) i przejdź do aplikacji funkcji.

Na karcie *funkcje platformy* wybierz pozycję *tożsamość*:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure Portal: Wybieranie tożsamości dla funkcji platformy Azure":::

Na stronie tożsamość ustaw wartość *opcji Przełącz na wartość* *wł*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure Portal: Włączanie stanu tożsamości":::

Należy również zwrócić uwagę na **Identyfikator obiektu** pokazywany na tej stronie, ponieważ będzie on używany w następnej sekcji.

### <a name="assign-access-roles"></a>Przypisywanie ról dostępu

Ponieważ usługa Azure Digital bliźniaczych reprezentacji używa kontroli dostępu opartej na rolach do zarządzania dostępem (zobacz [pojęcia: zabezpieczenia dla rozwiązań usługi Azure Digital bliźniaczych reprezentacji](concepts-security.md) , aby uzyskać więcej informacji na ten temat), należy również dodać rolę dla każdej aplikacji funkcji, która ma zezwalać na dostęp do usługi Azure Digital bliźniaczych reprezentacji.

Aby przypisać rolę, potrzebny jest **Identyfikator zasobu** utworzonego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Jeśli nie zarejestrowano go wcześniej podczas tworzenia wystąpienia, można je pobrać przy użyciu tego polecenia:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
Identyfikator zasobu będzie częścią danych wyjściowych, jako długi ciąg o nazwie "ID" zaczynający się od liter "/Subscriptions/...".

Użyj identyfikatora zasobu wraz z IDENTYFIKATORem obiektu funkcji platformy Azure wcześniej w poniższym poleceniu:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano kroki konfigurowania funkcji platformy Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji. Następnie można subskrybować funkcję platformy Azure w celu Event Grid, aby nasłuchiwać w punkcie końcowym. Ten punkt końcowy może:
* Punkt końcowy Event Grid dołączony do usługi Azure Digital bliźniaczych reprezentacji do przetwarzania komunikatów pochodzących z samej usługi Azure Digital bliźniaczych reprezentacji (takich jak komunikaty o zmianach właściwości, komunikaty telemetryczne generowane przez [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) w grafie bliźniaczym lub komunikaty o cyklu życia)
* Tematy systemowe IoT używane przez IoT Hub do wysyłania danych telemetrycznych i innych urządzeń
* Punkt końcowy Event Grid otrzymuje komunikaty z innych usług

Następnie zapoznaj się z tematem jak kompilować swoją podstawową funkcję platformy Azure, aby uzyskać IoT Hub dane do usługi Azure Digital bliźniaczych reprezentacji:
* [Instrukcje: pozyskiwanie danych telemetrycznych z IoT Hub](how-to-ingest-iot-hub-data.md)