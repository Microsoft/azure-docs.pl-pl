---
title: Konfigurowanie funkcji na platformie Azure na potrzeby przetwarzania danych
titleSuffix: Azure Digital Twins
description: Zobacz, jak utworzyć funkcję na platformie Azure, która może uzyskać dostęp i wyzwolenie przez Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1178b0ab5af3642026fe78c7de788f354691b13a
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701168"
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

W programie Visual Studio 2019 wybierz pozycję _plik > nowy > projekt_ i wyszukaj szablon _Azure Functions_ . Wybierz opcję _Dalej_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Zrzut ekranu programu Visual Studio pokazujący okno dialogowe Nowy projekt. Szablon projektu Azure Functions jest wyróżniony.":::

Określ nazwę aplikacji funkcji i wybierz pozycję _Utwórz_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Zrzut ekranu programu Visual Studio pokazujący okno dialogowe umożliwiające skonfigurowanie nowego projektu, w tym nazwy projektu, lokalizacji zapisywania, wybór tworzenia nowego rozwiązania i nazwy rozwiązania.":::

Wybierz typ aplikacji funkcji *Event Grid wyzwalacza* , a następnie wybierz pozycję _Utwórz_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Zrzut ekranu programu Visual Studio pokazujący okno dialogowe umożliwiające utworzenie nowej aplikacji Azure Functions. Opcja wyzwalacza Event Grid jest wyróżniona.":::

Po utworzeniu aplikacji funkcji program Visual Studio wygeneruje przykład kodu w pliku **Function1.cs** w folderze projektu. Ta krótka funkcja jest używana do rejestrowania zdarzeń.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Zrzut ekranu programu Visual Studio w oknie projektu dla nowego projektu, który został utworzony. Istnieje kod dla przykładowej funkcji o nazwie Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Napisz funkcję z wyzwalaczem Event Grid

Można napisać funkcję, dodając zestaw SDK do aplikacji funkcji. Aplikacja funkcji współdziała z usługą Azure Digital bliźniaczych reprezentacji przy użyciu [usługi Azure Digital bliźniaczych reprezentacji SDK dla platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Aby można było korzystać z zestawu SDK, należy dołączyć następujące pakiety do projektu. Możesz zainstalować pakiety przy użyciu Menedżera pakietów NuGet programu Visual Studio lub dodać pakiety przy użyciu `dotnet` narzędzia wiersza polecenia. Wykonaj poniższe kroki dla preferowanej metody.

**Opcja 1. Dodaj pakiety przy użyciu Menedżera pakietów programu Visual Studio:**
    
Wybierz projekt prawym przyciskiem myszy i wybierz pozycję _Zarządzaj pakietami NuGet_ z listy. Następnie w otwartym oknie Wybierz kartę _Przeglądaj_ i Wyszukaj następujące pakiety. Wybierz pozycję _Zainstaluj_ i _Zaakceptuj_ umowę licencyjną, aby zainstalować pakiety.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**Opcja 2. Dodaj pakiety przy użyciu `dotnet` narzędzia wiersza polecenia:**

Alternatywnie można użyć następujących `dotnet add` poleceń w narzędziu wiersza polecenia:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Następnie w Eksplorator rozwiązań programu Visual Studio Otwórz plik _Function1.cs_ , w którym znajduje się przykładowy kod, i Dodaj następujące `using` instrukcje do funkcji. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Dodawanie kodu uwierzytelniania do funkcji

Teraz deklarujesz zmienne na poziomie klasy i dodajesz kod uwierzytelniania, który umożliwi funkcji dostęp do usługi Azure Digital bliźniaczych reprezentacji. Do funkcji w pliku _Function1.cs_ zostanie dodana następująca wartość.

* Kod odczytywania adresu URL usługi Digital bliźniaczych reprezentacji platformy Azure jako zmiennej środowiskowej. Dobrym sposobem jest odczytanie adresu URL usługi ze zmiennej środowiskowej, a nie kodowanie twarde w funkcji.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Zmienna statyczna do przechowywania wystąpienia HttpClient. Tworzenie HttpClient jest stosunkowo kosztowne i chcemy unikać wykonywania tych czynności w przypadku każdego wywołania funkcji.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* W Azure Functions można użyć poświadczeń tożsamości zarządzanej.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Dodaj zmienną lokalną _DigitalTwinsClient_ wewnątrz funkcji, aby pomieścić wystąpienie klienta cyfrowego bliźniaczych reprezentacji platformy Azure. *Nie* ustawiaj tej zmiennej jako statycznej wewnątrz klasy.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Dodaj sprawdzanie wartości null dla _adtInstanceUrl_ i zawiń logikę funkcji w bloku try/catch, aby przechwytywać wyjątki.

Po wprowadzeniu tych zmian kod funkcji będzie wyglądać podobnie do poniższego:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Teraz, gdy aplikacja jest zapisywana, możesz ją opublikować na platformie Azure, wykonując kroki opisane w następnej sekcji.

## <a name="publish-the-function-app-to-azure"></a>Publikowanie aplikacji funkcji na platformie Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>Konfigurowanie dostępu zabezpieczeń do aplikacji funkcji

Dostęp zabezpieczeń do aplikacji funkcji można skonfigurować przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal. Postępuj zgodnie z instrukcjami dla preferowanej opcji poniżej.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Opcja 1: Konfigurowanie dostępu zabezpieczeń dla aplikacji funkcji przy użyciu interfejsu wiersza polecenia

Funkcja szkieletu z wcześniejszych przykładów wymaga, aby token okaziciela został przesłany do niego, aby można było uwierzytelniać za pomocą usługi Azure Digital bliźniaczych reprezentacji. Aby upewnić się, że ten token okaziciela jest zakończony, musisz skonfigurować [tożsamość usługi zarządzanej (msi)](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji funkcji. Należy to zrobić tylko raz dla każdej aplikacji funkcji.

Można utworzyć tożsamość zarządzaną przez system i przypisać tożsamość aplikacji funkcji do roli _**właściciela danych Digital bliźniaczych reprezentacji**_ platformy Azure dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Spowoduje to nadanie uprawnienia aplikacji funkcji w wystąpieniu do wykonywania działań płaszczyzny danych. Następnie Udostępnij adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Użyj [Azure Cloud Shell](https://shell.azure.com) , aby uruchomić polecenia.

Użyj następującego polecenia, aby utworzyć tożsamość zarządzaną przez system. Zwróć uwagę na pole _principalId_ w danych wyjściowych.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Użyj wartości _principalId_ w poniższym poleceniu, aby przypisać tożsamość aplikacji funkcji do roli _Właściciel danych usługi Azure Digital Twins_ dla wystąpienia usługi Azure Digital Twins.

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

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: Nazwa aplikacji funkcji jest przeszukiwana na pasku wyszukiwania portalu, a wynik wyszukiwania zostanie wyróżniony.":::

W oknie aplikacja funkcji wybierz pozycję _tożsamość_ na pasku nawigacyjnym po lewej stronie, aby włączyć zarządzaną tożsamość.
W obszarze _przypisana do systemu_ Przełącz _stan_ na wartość włączone i _Zapisz_ go. Zostanie wyświetlone okno podręczne umożliwiające _włączenie tożsamości zarządzanej przypisanej do systemu_.
Wybierz przycisk _tak_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na stronie tożsamość aplikacji funkcji jest ustawiona opcja włączania tożsamości zarządzanej przypisanej do systemu. Opcja stan jest ustawiona na wartość włączone.":::

Można zweryfikować powiadomienia, że funkcja została pomyślnie zarejestrowana w Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na liście powiadomień wybierz ikonę w kształcie dzwonka na górnym pasku portalu. Istnieje powiadomienie, że użytkownik włączył tożsamość zarządzaną przypisaną przez system.":::

Należy również zwrócić uwagę na **Identyfikator obiektu** wyświetlany na stronie _tożsamości_ , ponieważ będzie on używany w następnej sekcji.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: wyróżnienie wokół pola identyfikatora obiektu na stronie tożsamości funkcji platformy Azure.":::

### <a name="assign-access-roles-using-azure-portal"></a>Przypisywanie ról dostępu przy użyciu Azure Portal

Wybierz przycisk _przypisania roli platformy Azure_ , który spowoduje otwarcie strony *przypisania roli platformy Azure* . Następnie wybierz pozycję _+ Dodaj przypisanie roli (wersja zapoznawcza)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: wyróżniony przycisk &quot;przypisań roli platformy Azure&quot; w obszarze uprawnienia na stronie tożsamości funkcji platformy Azure.":::

Na stronie _Dodaj przypisanie roli (wersja zapoznawcza)_ , która zostanie otwarta, wybierz pozycję:

* _Zakres_: grupa zasobów
* _Subskrypcja_: wybierz subskrypcję platformy Azure
* _Grupa zasobów_: Wybierz grupę zasobów z listy rozwijanej
* _Rola_: Wybierz _właściciela danych Digital bliźniaczych reprezentacji platformy Azure_ z listy rozwijanej

Następnie Zapisz szczegóły, naciskając przycisk _Zapisz_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Azure Portal: aby dodać nowe przypisanie roli (wersja zapoznawcza). Istnieją pola dla zakresu, subskrypcji, grupy zasobów i roli.":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurowanie ustawień aplikacji przy użyciu Azure Portal

Możesz wprowadzić adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej. Aby uzyskać więcej informacji na ten temat, zobacz [*zmienne środowiskowe*](/sandbox/functions-recipes/environment-variables). Ustawienia aplikacji są udostępniane jako zmienne środowiskowe w celu uzyskania dostępu do wystąpienia bliźniaczych reprezentacji Digital. 

Aby ustawić zmienną środowiskową przy użyciu adresu URL wystąpienia, należy najpierw uzyskać adres URL, wyszukując nazwę hosta wystąpienia bliźniaczych reprezentacji cyfrowych platformy Azure. Wyszukaj swoje wystąpienie na pasku wyszukiwania [Azure Portal](https://portal.azure.com) . Następnie wybierz pozycję _Przegląd_ na lewym pasku nawigacyjnym, aby wyświetlić _nazwę hosta_. Skopiuj tę wartość.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na stronie Przegląd wystąpienia usługi Azure Digital bliźniaczych reprezentacji zostanie wyróżniona wartość Nazwa hosta.":::

Teraz możesz utworzyć ustawienie aplikacji, wykonując poniższe kroki:

1. Wyszukaj aplikację funkcji na pasku wyszukiwania portalu i wybierz ją z wyników
1. Wybierz pozycję _Konfiguracja_ na pasku nawigacyjnym po lewej stronie, aby utworzyć nowe ustawienie aplikacji
1. Na karcie _Ustawienia aplikacji_ wybierz pozycję _+ nowe ustawienie aplikacji_

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: Nazwa aplikacji funkcji jest przeszukiwana na pasku wyszukiwania portalu, a wynik wyszukiwania zostanie wyróżniony.":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na stronie Konfiguracja aplikacji funkcji zostanie wyróżniony przycisk służący do tworzenia nowego ustawienia aplikacji.":::

W otwartym oknie Użyj wartości Nazwa hosta skopiowanej powyżej, aby utworzyć ustawienie aplikacji.
* **Nazwa**: ADT_SERVICE_URL
* **Wartość**: https://{The-Azure-Digital-bliźniaczych reprezentacji-Host-Name}

Wybierz _przycisk OK_ , aby utworzyć ustawienie aplikacji.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: przycisk OK zostanie wyróżniony po wypełnieniu pól Nazwa i wartość na stronie Dodawanie/Edytowanie ustawienia aplikacji.":::

Ustawienia aplikacji można wyświetlić za pomocą nazwy aplikacji w polu _Nazwa_ . Następnie Zapisz ustawienia aplikacji, wybierając przycisk _Zapisz_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: Strona Ustawienia aplikacji z wyróżnionym nowym ustawieniem ADT_SERVICE_URL. Przycisk Zapisz jest również wyróżniony.":::

Wszelkie zmiany w ustawieniach aplikacji wymagają ponownego uruchomienia aplikacji. Wybierz pozycję _Kontynuuj_ , aby ponownie uruchomić aplikację.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: należy zauważyć, że wszelkie zmiany ustawień aplikacji z ponownym uruchomieniem aplikacji. Przycisk Kontynuuj jest wyróżniony.":::

Ustawienia aplikacji można wyświetlić, wybierając ikonę _powiadomienia_ . Jeśli ustawienie aplikacji nie zostanie utworzone, można ponowić próbę dodania ustawienia aplikacji, wykonując powyższy proces.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na liście powiadomień wybierz ikonę w kształcie dzwonka na górnym pasku portalu. Istnieje powiadomienie, że ustawienia aplikacji sieci Web zostały pomyślnie zaktualizowane.":::

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano kroki konfigurowania aplikacji funkcji na platformie Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji.

Następnie zapoznaj się z tematem jak skompilować swoją podstawową funkcję, aby uzyskać IoT Hub dane do usługi Azure Digital bliźniaczych reprezentacji:
* [*Instrukcje: pozyskiwanie danych telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md)
