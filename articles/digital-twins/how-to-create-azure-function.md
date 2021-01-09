---
title: Konfigurowanie funkcji na platformie Azure na potrzeby przetwarzania danych
titleSuffix: Azure Digital Twins
description: Zobacz, jak utworzyć funkcję na platformie Azure, która może uzyskać dostęp i wyzwolenie przez Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6c4f23406c97d647002fbb3ab4a3544866303cf4
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051347"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Łączenie aplikacji funkcji na platformie Azure na potrzeby przetwarzania danych

Aktualizowanie cyfrowych bliźniaczych reprezentacji na podstawie danych jest obsługiwane przy użyciu [**tras zdarzeń**](concepts-route-events.md) za pośrednictwem zasobów obliczeniowych, takich jak funkcja, która jest wykonywana przy użyciu [Azure Functions](../azure-functions/functions-overview.md). Funkcje mogą służyć do aktualizowania wielocyfrowej dwuosiowej odpowiedzi na:
* dane telemetryczne urządzenia pochodzące z IoT Hub
* Zmiana właściwości lub inne dane pochodzące z innego cyfrowego sznurka w grafie bliźniaczym

W tym artykule opisano tworzenie funkcji platformy Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji. 

Poniżej przedstawiono przegląd kroków, które zawiera:

1. Tworzenie projektu usługi Azure Functions w programie Visual Studio
2. Napisz funkcję z wyzwalaczem [Event Grid](../event-grid/overview.md)
3. Dodawanie kodu uwierzytelniania do funkcji (aby można było uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji)
4. Publikowanie aplikacji funkcji na platformie Azure
5. Konfigurowanie dostępu [zabezpieczeń](concepts-security.md) do aplikacji funkcji

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Wymaganie wstępne: Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Tworzenie aplikacji funkcji w programie Visual Studio

W programie Visual Studio 2019 wybierz pozycję _plik > nowy > projekt_ i wyszukaj szablon _Azure Functions_ , a następnie wybierz pozycję _dalej_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: okno dialogowe Nowy projekt":::

Określ nazwę aplikacji funkcji i wybierz pozycję _Utwórz_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: Konfigurowanie nowego projektu":::

Wybierz typ Event Grid aplikacji funkcji *wyzwalacz* i wybierz pozycję _Utwórz_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: okno dialogowe wyzwalacza projektu Azure Functions":::

Po utworzeniu aplikacji funkcji program Visual Studio będzie miał automatycznie wypełniony kod przykładowy w pliku **Function.cs** w folderze projektu. Ta krótka funkcja jest używana do rejestrowania zdarzeń.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: okno projektu z przykładowym kodem":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Napisz funkcję z wyzwalaczem Event Grid

Można napisać funkcję, dodając zestaw SDK do aplikacji funkcji. Aplikacja funkcji współdziała z usługą Azure Digital bliźniaczych reprezentacji przy użyciu [usługi Azure Digital bliźniaczych reprezentacji SDK dla platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Aby można było korzystać z zestawu SDK, należy dołączyć następujące pakiety do projektu. Możesz zainstalować pakiety przy użyciu Menedżera pakietów NuGet programu Visual Studio lub dodać pakiety za pomocą `dotnet` narzędzia wiersza polecenia. Wybierz jedną z następujących metod: 

**Opcja 1. Dodaj pakiety przy użyciu Menedżera pakietów programu Visual Studio:**
    
Można to zrobić, wybierając prawym przyciskiem myszy projekt, a następnie wybierając pozycję _Zarządzaj pakietami NuGet_ z listy. Następnie w otwartym oknie wybierz pozycję _Przeglądaj_ kartę i Wyszukaj następujące pakiety. Wybierz pozycję _Zainstaluj_ i _Zaakceptuj_ umowę licencyjną, aby zainstalować pakiety.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Aby konfiguracja potoku zestawu Azure SDK została prawidłowo skonfigurowana dla Azure Functions, potrzebne są również następujące pakiety. Powtórz ten sam proces jak powyżej, aby zainstalować wszystkie pakiety.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Opcja 2. Dodaj pakiety przy użyciu `dotnet` narzędzia wiersza polecenia:**

Alternatywnie można użyć następujących `dotnet add` poleceń w narzędziu wiersza polecenia:
```cmd/sh
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```

Następnie Dodaj dwie więcej zależności do projektu, które będą konieczne do pracy z usługą Azure Digital bliźniaczych reprezentacji. Możesz użyć linków poniżej, aby przejść do pakietów w pakiecie NuGet, gdzie można znaleźć polecenia konsoli (w tym dla interfejsu wiersza polecenia platformy .NET), aby dodać najnowszą wersję każdej do projektu.
 * [**Azure. DigitalTwins. Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Jest to pakiet dla [zestawu SDK Digital bliźniaczych reprezentacji systemu Azure dla platformy .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
 * [**Azure. Identity**](https://www.nuget.org/packages/Azure.Identity). Ta biblioteka zawiera narzędzia, które ułatwiają uwierzytelnianie na platformie Azure.

Następnie w Eksplorator rozwiązań programu Visual Studio Otwórz plik _Function.cs_ , w którym znajduje się przykładowy kod, i Dodaj do funkcji następujące instrukcje _użycia_ . 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Dodawanie kodu uwierzytelniania do funkcji

Teraz deklarujesz zmienne na poziomie klasy i dodajesz kod uwierzytelniania, który umożliwi funkcji dostęp do usługi Azure Digital bliźniaczych reprezentacji. Do funkcji zostanie dodany następujący plik {Twoja nazwa funkcji}. cs.

* Odczytaj adres URL usługi ADT jako zmienną środowiskową. Dobrym sposobem jest odczytanie adresu URL usługi ze zmiennej środowiskowej, a nie kodowanie twarde w funkcji.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Zmienna statyczna do przechowywania wystąpienia HttpClient. Tworzenie HttpClient jest stosunkowo kosztowne i chcemy unikać wykonywania tych czynności w przypadku każdego wywołania funkcji.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* W Azure Functions można użyć poświadczeń tożsamości zarządzanej.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Dodaj zmienną lokalną _DigitalTwinsClient_ wewnątrz funkcji, aby pomieścić wystąpienie klienta usługi Azure Digital bliźniaczych reprezentacji do projektu funkcji. *Nie* ustawiaj tej zmiennej jako statycznej wewnątrz klasy.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Dodaj sprawdzanie wartości null dla _adtInstanceUrl_ i zawiń logikę funkcji w bloku try catch, aby przechwytywać wyjątki.

Po wprowadzeniu tych zmian kod funkcji będzie wyglądać podobnie do poniższego:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

## <a name="publish-the-function-app-to-azure"></a>Publikowanie aplikacji funkcji na platformie Azure

Aby opublikować projekt w aplikacji funkcji na platformie Azure, w obszarze Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt funkcji (a nie rozwiązanie), a następnie wybierz polecenie **Publikuj**.

> [!IMPORTANT] 
> Publikowanie w aplikacji funkcji na platformie Azure wiąże się z dodatkowymi opłatami za subskrypcję, niezależnie od bliźniaczych reprezentacji cyfrowych platformy Azure.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: publikowanie funkcji na platformie Azure":::

Wybierz pozycję **Azure** jako element docelowy publikowania i wybierz pozycję **dalej**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: publikowanie Azure Functions okno dialogowe, Wybieranie platformy Azure ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: publikowanie funkcji okna dialogowego, wybierz pozycję Azure aplikacja funkcji (Windows) lub (Linux) na podstawie swojej maszyny":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: publikowanie funkcji okna dialogowego, tworzenie nowej funkcji platformy Azure":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: publikowanie funkcji okna dialogowego, wypełnij pola i wybierz pozycję Utwórz":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: publikowanie funkcji okna dialogowego, wybierz aplikację funkcji z listy i Zakończ":::

Na następującej stronie wprowadź żądaną nazwę nowej aplikacji funkcji, grupy zasobów i inne szczegóły.
Aby aplikacja funkcji mogła uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, musi mieć tożsamość zarządzaną przez system i mieć uprawnienia dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Następnie można skonfigurować dostęp zabezpieczeń dla funkcji przy użyciu interfejsu wiersza polecenia lub Azure Portal. Wybierz jedną z następujących metod:

## <a name="set-up-security-access-for-the-function-app"></a>Konfigurowanie dostępu zabezpieczeń do aplikacji funkcji
Dostęp zabezpieczeń do aplikacji funkcji można skonfigurować przy użyciu jednej z następujących opcji:

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Opcja 1: Konfigurowanie dostępu zabezpieczeń dla aplikacji funkcji przy użyciu interfejsu wiersza polecenia

Funkcja szkieletu z wcześniejszych przykładów wymaga, aby token okaziciela został przesłany do niego, aby można było uwierzytelniać za pomocą usługi Azure Digital bliźniaczych reprezentacji. Aby upewnić się, że ten token okaziciela jest zakończony, musisz skonfigurować [tożsamość usługi zarządzanej (msi)](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji funkcji. Należy to zrobić tylko raz dla każdej aplikacji funkcji.

Można utworzyć tożsamość zarządzaną przez system i przypisać tożsamość aplikacji funkcji do roli _**właściciela danych Digital bliźniaczych reprezentacji**_ platformy Azure dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Spowoduje to nadanie uprawnienia aplikacji funkcji w wystąpieniu do wykonywania działań płaszczyzny danych. Następnie Udostępnij adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Użyj [Azure Cloud Shell](https://shell.azure.com) , aby uruchomić polecenia.

Użyj następującego polecenia, aby utworzyć tożsamość zarządzaną przez system. Zwróć uwagę na pole _principalId_ w danych wyjściowych.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Użyj wartości _principalId_ w poniższym poleceniu, aby przypisać tożsamość aplikacji funkcji do roli _właściciela danych Digital bliźniaczych reprezentacji platformy_ Azure dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Na koniec możesz wprowadzić adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej. Aby uzyskać więcej informacji na temat ustawiania zmiennych środowiskowych, zobacz [*zmienne środowiskowe*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> Adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji jest tworzony przez dodanie *https://* do początku *nazwy hosta* cyfrowego wystąpienia bliźniaczych reprezentacji platformy Azure. Aby wyświetlić nazwę hosta wraz ze wszystkimi właściwościami wystąpienia, można uruchomić polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Opcja 2: Konfigurowanie dostępu zabezpieczeń dla aplikacji funkcji przy użyciu Azure Portal

Tożsamość zarządzana przypisana przez system umożliwia zasobom platformy Azure uwierzytelnianie w usługach w chmurze (na przykład Azure Key Vault) bez zapisywania poświadczeń w kodzie. Po włączeniu wszystkich wymaganych uprawnień można udzielić za pośrednictwem kontroli dostępu opartej na rolach na platformie Azure. Cykl życia tego typu tożsamości zarządzanej jest powiązany z cyklem życia tego zasobu. Ponadto każdy zasób (na przykład maszyna wirtualna) może mieć tylko jedną tożsamość zarządzaną przypisaną przez system.

W [Azure Portal](https://portal.azure.com/)Wyszukaj _aplikację funkcji_ na pasku wyszukiwania przy użyciu utworzonej wcześniej nazwy aplikacji funkcji. Wybierz z listy *aplikacja funkcji* . 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure Portal: Wyszukaj aplikację funkcji":::

W oknie aplikacja funkcji wybierz pozycję _tożsamość_ na pasku nawigacyjnym po lewej stronie, aby włączyć zarządzaną tożsamość.
W obszarze _przypisana do systemu_ Przełącz _stan_ na wartość włączone i _Zapisz_ go. Zostanie wyświetlone okno podręczne umożliwiające _włączenie tożsamości zarządzanej przypisanej do systemu_.
Wybierz przycisk _tak_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure Portal: Włącz tożsamość zarządzaną przez system":::

Można zweryfikować powiadomienia, że funkcja została pomyślnie zarejestrowana w Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure Portal: powiadomienia":::

Należy również zwrócić uwagę na **Identyfikator obiektu** wyświetlany na stronie _tożsamości_ , ponieważ będzie on używany w następnej sekcji.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Skopiuj identyfikator obiektu do użycia w przyszłości":::

### <a name="assign-access-roles-using-azure-portal"></a>Przypisywanie ról dostępu przy użyciu Azure Portal

Wybierz przycisk _przypisania roli platformy Azure_ , który spowoduje otwarcie strony *przypisania roli platformy Azure* . Następnie wybierz pozycję _+ Dodaj przypisanie roli (wersja zapoznawcza)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure Portal: Dodawanie przypisania roli":::

Na stronie _Dodaj przypisanie roli (wersja zapoznawcza)_ , która zostanie otwarta, wybierz pozycję:

* _Zakres_: grupa zasobów
* _Subskrypcja_: wybierz subskrypcję platformy Azure
* _Grupa zasobów_: Wybierz grupę zasobów z listy rozwijanej
* _Rola_: Wybierz _właściciela danych Digital bliźniaczych reprezentacji platformy Azure_ z listy rozwijanej

Następnie Zapisz szczegóły, naciskając przycisk _Zapisz_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure Portal: Dodawanie przypisania roli (wersja zapoznawcza) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurowanie ustawień aplikacji przy użyciu Azure Portal

Możesz wprowadzić adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej. Aby uzyskać więcej informacji na ten temat, zobacz [*zmienne środowiskowe*](/sandbox/functions-recipes/environment-variables). Ustawienia aplikacji są udostępniane jako zmienne środowiskowe w celu uzyskania dostępu do wystąpienia bliźniaczych reprezentacji Digital. 

Musisz ADT_INSTANCE_URL, aby utworzyć ustawienie aplikacji.

ADT_INSTANCE_URL można uzyskać, dołączając **_https://_** do nazwy hosta wystąpienia. W Azure Portal można znaleźć nazwę hosta wystąpienia Digital bliźniaczych reprezentacji, wyszukując wystąpienie na pasku wyszukiwania. Następnie wybierz pozycję _Przegląd_ na lewym pasku nawigacyjnym, aby wyświetlić _nazwę hosta_. Skopiuj tę wartość, aby utworzyć ustawienie aplikacji.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure Portal: przegląd — > skopiuj nazwę hosta do użycia w polu _Value_.":::

Teraz możesz utworzyć ustawienie aplikacji, wykonując poniższe kroki:

* Wyszukaj aplikację przy użyciu nazwy aplikacji funkcji na pasku wyszukiwania i wybierz aplikację funkcji z listy
* Wybierz pozycję _Konfiguracja_ na pasku nawigacyjnym po lewej stronie, aby utworzyć nowe ustawienie aplikacji
* Na karcie _Ustawienia aplikacji_ wybierz pozycję _+ nowe ustawienie aplikacji_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure Portal: wyszukiwanie istniejącej aplikacji funkcji":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal: Konfigurowanie ustawień aplikacji":::

W otwartym oknie Użyj wartości skopiowanej z powyżej, aby utworzyć ustawienie aplikacji. \
_Nazwa_  : ADT_SERVICE_URL \
_Wartość_ : https://{Twoja-Azure-Digital-bliźniaczych reprezentacji-hostname}

Wybierz _przycisk OK_ , aby utworzyć ustawienie aplikacji.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal: Dodaj ustawienia aplikacji.":::

Ustawienia aplikacji można wyświetlić za pomocą nazwy aplikacji w polu _Nazwa_ . Następnie Zapisz ustawienia aplikacji, wybierając przycisk _Zapisz_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal: Wyświetl aplikację utworzoną i uruchom ponownie aplikację":::

Wszelkie zmiany w ustawieniach aplikacji wymagają ponownego uruchomienia aplikacji. Wybierz pozycję _Kontynuuj_ , aby ponownie uruchomić aplikację.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal: Zapisz ustawienia aplikacji":::

Ustawienia aplikacji można wyświetlić, wybierając ikonę _powiadomienia_ . Jeśli ustawienie aplikacji nie zostanie utworzone, można ponowić próbę dodania ustawienia aplikacji, wykonując powyższy proces.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure Portal: powiadomienia dotyczące aktualizowania ustawień aplikacji":::

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano kroki konfigurowania aplikacji funkcji na platformie Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji. Następnie można subskrybować funkcję do Event Grid, aby nasłuchiwać w punkcie końcowym. Ten punkt końcowy może:
* Punkt końcowy Event Grid dołączony do usługi Azure Digital bliźniaczych reprezentacji do przetwarzania komunikatów pochodzących z samej usługi Azure Digital bliźniaczych reprezentacji (takich jak komunikaty o zmianach właściwości, komunikaty telemetryczne generowane przez [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) w grafie bliźniaczym lub komunikaty o cyklu życia)
* Tematy systemowe IoT używane przez IoT Hub do wysyłania danych telemetrycznych i innych urządzeń
* Punkt końcowy Event Grid otrzymuje komunikaty z innych usług

Następnie zapoznaj się z tematem jak skompilować swoją podstawową funkcję, aby uzyskać IoT Hub dane do usługi Azure Digital bliźniaczych reprezentacji:
* [*Instrukcje: pozyskiwanie danych telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md)
