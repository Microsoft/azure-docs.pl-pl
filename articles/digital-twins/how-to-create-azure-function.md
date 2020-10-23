---
title: Konfigurowanie funkcji platformy Azure do przetwarzania danych
titleSuffix: Azure Digital Twins
description: Zobacz, jak utworzyć funkcję platformy Azure, która może uzyskiwać dostęp do programu Digital bliźniaczych reprezentacji i uruchamiać ją.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 78addb76e2ce7a2679358e241650cc5cc827791f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461621"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Łączenie Azure Functions aplikacji do przetwarzania danych

Aktualizacja bliźniaczych reprezentacji cyfrowych oparta na danych jest obsługiwana przy użyciu [**tras zdarzeń**](concepts-route-events.md) za pośrednictwem zasobów obliczeniowych, takich jak [Azure Functions](../azure-functions/functions-overview.md). Funkcja platformy Azure może służyć do aktualizowania wielocyfrowej dwuosiowej odpowiedzi na:
* dane telemetryczne urządzenia pochodzące z IoT Hub
* Zmiana właściwości lub inne dane pochodzące z innego cyfrowego sznurka w grafie bliźniaczym

W tym artykule przedstawiono sposób tworzenia funkcji platformy Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji. 

Poniżej przedstawiono przegląd kroków, które zawiera:

1. Tworzenie aplikacji Azure Functions w programie Visual Studio
2. Napisz funkcję platformy Azure z wyzwalaczem [Event Grid](../event-grid/overview.md)
3. Dodawanie kodu uwierzytelniania do funkcji (aby można było uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji)
4. Publikowanie aplikacji funkcji na platformie Azure
5. Konfigurowanie dostępu [zabezpieczeń](concepts-security.md) do aplikacji funkcji platformy Azure

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Tworzenie aplikacji Azure Functions w programie Visual Studio

W programie Visual Studio 2019 wybierz pozycję _plik > nowy > projekt_ i wyszukaj szablon _Azure Functions_ , a następnie wybierz pozycję _dalej_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Określ nazwę aplikacji funkcji i wybierz pozycję _Utwórz_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Wybierz typ Event Grid aplikacji funkcji *wyzwalacz* i wybierz pozycję _Utwórz_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Po utworzeniu aplikacji funkcji program Visual Studio będzie miał automatycznie wypełniony kod przykładowy w pliku **Function.cs** w folderze projektu. Ta krótka funkcja platformy Azure służy do rejestrowania zdarzeń.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Napisz funkcję platformy Azure z wyzwalaczem Event Grid

Możesz napisać funkcję platformy Azure, dodając zestaw SDK do aplikacji funkcji. Aplikacja funkcji współdziała z usługą Azure Digital bliźniaczych reprezentacji przy użyciu [usługi Azure Digital bliźniaczych reprezentacji SDK dla platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true). 

Aby można było korzystać z zestawu SDK, należy dołączyć następujące pakiety do projektu. Możesz zainstalować pakiety przy użyciu Menedżera pakietów NuGet programu Visual Studio lub dodać pakiety za pomocą `dotnet` narzędzia wiersza polecenia. Wybierz jedną z następujących metod: 

**Opcja 1. Dodaj pakiety przy użyciu Menedżera pakietów programu Visual Studio:**
    
Można to zrobić, wybierając prawym przyciskiem myszy projekt, a następnie wybierając pozycję _Zarządzaj pakietami NuGet_ z listy. Następnie w otwartym oknie wybierz pozycję _Przeglądaj_ kartę i Wyszukaj następujące pakiety. Wybierz pozycję _Zainstaluj_ i _Zaakceptuj_ umowę licencyjną, aby zainstalować pakiety.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Aby konfiguracja potoku zestawu Azure SDK została prawidłowo skonfigurowana dla Azure Functions, potrzebne są również następujące pakiety. Powtórz ten sam proces jak powyżej, aby zainstalować wszystkie pakiety.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Opcja 2. Dodaj pakiety przy użyciu `dotnet` narzędzia wiersza polecenia:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Następnie w Eksplorator rozwiązań programu Visual Studio Otwórz plik _Function.cs_ , w którym znajduje się przykładowy kod, i Dodaj następujące instrukcje _using_ do funkcji platformy Azure. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>Dodawanie kodu uwierzytelniania do funkcji platformy Azure

Teraz deklarujesz zmienne na poziomie klasy i dodajesz kod uwierzytelniania, który umożliwi funkcji dostęp do usługi Azure Digital bliźniaczych reprezentacji. Do funkcji platformy Azure zostanie dodany następujący plik {Twoja nazwa funkcji}. cs.

* Odczytaj adres URL usługi ADT jako zmienną środowiskową. Dobrym sposobem jest odczytanie adresu URL usługi ze zmiennej środowiskowej, a nie kodowanie twarde w funkcji.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Zmienna statyczna do przechowywania wystąpienia HttpClient. Tworzenie HttpClient jest stosunkowo kosztowne i chcemy unikać wykonywania tych czynności w przypadku każdego wywołania funkcji.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Możesz użyć poświadczeń tożsamości zarządzanej w usłudze Azure Function.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Dodaj zmienną lokalną _DigitalTwinsClient_ wewnątrz funkcji, aby pomieścić wystąpienie klienta usługi Azure Digital bliźniaczych reprezentacji do projektu funkcji. *Nie* ustawiaj tej zmiennej jako statycznej wewnątrz klasy.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Dodaj sprawdzanie wartości null dla _adtInstanceUrl_ i zawiń logikę funkcji w bloku try catch, aby przechwytywać wyjątki.

Po wprowadzeniu tych zmian kod funkcji będzie wyglądać podobnie do poniższego:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>Publikowanie aplikacji funkcji na platformie Azure

Aby opublikować aplikację funkcji na platformie Azure, w Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt funkcji (a nie rozwiązanie), a następnie wybierz polecenie **Publikuj**.

> [!IMPORTANT] 
> Publikowanie funkcji platformy Azure wiąże się z dodatkowymi opłatami za subskrypcję, niezależnie od bliźniaczych reprezentacji cyfrowych platformy Azure.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Wybierz pozycję **Azure** jako element docelowy publikowania i wybierz pozycję **dalej**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Na następującej stronie wprowadź żądaną nazwę nowej aplikacji funkcji, grupy zasobów i inne szczegóły.
Aby aplikacja Functions mogła uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, musi mieć tożsamość zarządzaną przez system i mieć uprawnienia dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Następnie można skonfigurować dostęp zabezpieczeń dla funkcji przy użyciu interfejsu wiersza polecenia lub Azure Portal. Wybierz jedną z następujących metod:

## <a name="set-up-security-access-for-the-azure-function-app"></a>Konfigurowanie dostępu zabezpieczeń do aplikacji funkcji platformy Azure
Dostęp zabezpieczeń do aplikacji funkcji platformy Azure można skonfigurować przy użyciu jednej z następujących opcji:

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>Opcja 1: Konfigurowanie dostępu zabezpieczeń dla aplikacji funkcji platformy Azure przy użyciu interfejsu wiersza polecenia

Szkielet funkcji platformy Azure z wcześniejszych przykładów wymaga, aby token okaziciela został przesłany do niego, aby można było uwierzytelnić się za pomocą usługi Azure Digital bliźniaczych reprezentacji. Aby upewnić się, że ten token okaziciela jest zakończony, musisz skonfigurować [tożsamość usługi zarządzanej (msi)](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji funkcji. Należy to zrobić tylko raz dla każdej aplikacji funkcji.

Można utworzyć tożsamość zarządzaną przez system i przypisać tożsamość aplikacji funkcji do roli _właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)_ dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Spowoduje to nadanie uprawnienia aplikacji funkcji w wystąpieniu do wykonywania działań płaszczyzny danych. Następnie Udostępnij adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej.

 Użyj [Azure Cloud Shell](https://shell.azure.com) , aby uruchomić polecenia.

Użyj następującego polecenia, aby utworzyć tożsamość zarządzaną przez system. Zwróć uwagę na pole _principalId_ w danych wyjściowych.

```azurecli 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Użyj wartości _principalId_ w poniższym poleceniu, aby przypisać tożsamość aplikacji funkcji do roli _właściciela usługi Azure Digital bliźniaczych reprezentacji (wersja zapoznawcza)_ dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

```azurecli 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
Na koniec możesz wprowadzić adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej. Aby uzyskać więcej informacji na temat ustawiania zmiennych środowiskowych, zobacz [*zmienne środowiskowe*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> Adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji jest tworzony przez dodanie *https://* do początku *nazwy hosta*cyfrowego wystąpienia bliźniaczych reprezentacji platformy Azure. Aby wyświetlić nazwę hosta wraz ze wszystkimi właściwościami wystąpienia, można uruchomić polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>Opcja 2: Konfigurowanie dostępu zabezpieczeń dla aplikacji funkcji platformy Azure przy użyciu Azure Portal

Tożsamość zarządzana przypisana przez system umożliwia zasobom platformy Azure uwierzytelnianie w usługach w chmurze (na przykład Azure Key Vault) bez zapisywania poświadczeń w kodzie. Po włączeniu wszystkich wymaganych uprawnień można udzielić za pośrednictwem kontroli dostępu opartej na rolach na platformie Azure. Cykl życia tego typu tożsamości zarządzanej jest powiązany z cyklem życia tego zasobu. Ponadto każdy zasób (na przykład maszyna wirtualna) może mieć tylko jedną tożsamość zarządzaną przypisaną przez system.

W [Azure Portal](https://portal.azure.com/)Wyszukaj _aplikację funkcji_ na pasku wyszukiwania przy użyciu utworzonej wcześniej nazwy aplikacji funkcji. Wybierz z listy *aplikacja funkcji* . 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

W oknie aplikacja funkcji wybierz pozycję _tożsamość_ na pasku nawigacyjnym po lewej stronie, aby włączyć zarządzaną tożsamość.
W obszarze _przypisana do systemu_ Przełącz _stan_ na wartość włączone i _Zapisz_ go. Zostanie wyświetlone okno podręczne umożliwiające _włączenie tożsamości zarządzanej przypisanej do systemu_.
Wybierz przycisk _tak_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Można zweryfikować powiadomienia, że funkcja została pomyślnie zarejestrowana w Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Należy również zwrócić uwagę na **Identyfikator obiektu** wyświetlany na stronie _tożsamości_ , ponieważ będzie on używany w następnej sekcji.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

### <a name="assign-access-roles-using-azure-portal"></a>Przypisywanie ról dostępu przy użyciu Azure Portal

Wybierz przycisk _przypisania roli platformy Azure_ , który spowoduje otwarcie strony *przypisania roli platformy Azure* . Następnie wybierz pozycję _+ Dodaj przypisanie roli (wersja zapoznawcza)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Na stronie _Dodaj przypisanie roli (wersja zapoznawcza)_ , która zostanie otwarta, wybierz pozycję:

* _Zakres_: grupa zasobów
* _Subskrypcja_: wybierz subskrypcję platformy Azure
* _Grupa zasobów_: Wybierz grupę zasobów z listy rozwijanej
* _Rola_: wybierz pozycję _Azure Digital bliźniaczych reprezentacji Owner (wersja zapoznawcza)_ z listy rozwijanej

Następnie Zapisz szczegóły, naciskając przycisk _Zapisz_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurowanie ustawień aplikacji przy użyciu Azure Portal

Możesz wprowadzić adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej. Aby uzyskać więcej informacji na ten temat, zobacz [*zmienne środowiskowe*](/sandbox/functions-recipes/environment-variables). Ustawienia aplikacji są udostępniane jako zmienne środowiskowe w celu uzyskania dostępu do wystąpienia bliźniaczych reprezentacji Digital. 

Musisz ADT_INSTANCE_URL, aby utworzyć ustawienie aplikacji.

ADT_INSTANCE_URL można uzyskać, dołączając **_https://_** do nazwy hosta wystąpienia. W Azure Portal można znaleźć nazwę hosta wystąpienia Digital bliźniaczych reprezentacji, wyszukując wystąpienie na pasku wyszukiwania. Następnie wybierz pozycję _Przegląd_ na lewym pasku nawigacyjnym, aby wyświetlić _nazwę hosta_. Skopiuj tę wartość, aby utworzyć ustawienie aplikacji.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Teraz możesz utworzyć ustawienie aplikacji, wykonując poniższe kroki:

* Wyszukaj funkcję platformy Azure przy użyciu nazwy funkcji na pasku wyszukiwania i wybierz funkcję z listy
* Wybierz pozycję _Konfiguracja_ na pasku nawigacyjnym po lewej stronie, aby utworzyć nowe ustawienie aplikacji
* Na karcie _Ustawienia aplikacji_ wybierz pozycję _+ nowe ustawienie aplikacji_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

W otwartym oknie Użyj wartości skopiowanej z powyżej, aby utworzyć ustawienie aplikacji. \
_Nazwa_  : ADT_SERVICE_URL \
_Wartość_ : https://{Twoja-Azure-Digital-bliźniaczych reprezentacji-hostname}

Wybierz _przycisk OK_ , aby utworzyć ustawienie aplikacji.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Ustawienia aplikacji można wyświetlić za pomocą nazwy aplikacji w polu _Nazwa_ . Następnie Zapisz ustawienia aplikacji, wybierając przycisk _Zapisz_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Wszelkie zmiany w ustawieniach aplikacji wymagają ponownego uruchomienia aplikacji. Wybierz pozycję _Kontynuuj_ , aby ponownie uruchomić aplikację.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Ustawienia aplikacji można wyświetlić, wybierając ikonę _powiadomienia_ . Jeśli ustawienie aplikacji nie zostanie utworzone, można ponowić próbę dodania ustawienia aplikacji, wykonując powyższy proces.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano kroki konfigurowania funkcji platformy Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji. Następnie można subskrybować funkcję platformy Azure w celu Event Grid, aby nasłuchiwać w punkcie końcowym. Ten punkt końcowy może:
* Punkt końcowy Event Grid dołączony do usługi Azure Digital bliźniaczych reprezentacji do przetwarzania komunikatów pochodzących z samej usługi Azure Digital bliźniaczych reprezentacji (takich jak komunikaty o zmianach właściwości, komunikaty telemetryczne generowane przez [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) w grafie bliźniaczym lub komunikaty o cyklu życia)
* Tematy systemowe IoT używane przez IoT Hub do wysyłania danych telemetrycznych i innych urządzeń
* Punkt końcowy Event Grid otrzymuje komunikaty z innych usług

Następnie zapoznaj się z tematem jak kompilować swoją podstawową funkcję platformy Azure, aby uzyskać IoT Hub dane do usługi Azure Digital bliźniaczych reprezentacji:
* [*Instrukcje: pozyskiwanie danych telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md)