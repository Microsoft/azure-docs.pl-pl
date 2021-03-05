---
title: Konfigurowanie funkcji na platformie Azure na potrzeby przetwarzania danych
titleSuffix: Azure Digital Twins
description: Zobacz, jak utworzyć funkcję na platformie Azure, która może uzyskać dostęp i wyzwolenie przez Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8ed4e550ea441d5d99a3debb6bf37eb7db2a4a20
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180177"
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

Aby można było korzystać z zestawu SDK, należy dołączyć następujące pakiety do projektu. Możesz zainstalować pakiety przy użyciu Menedżera pakietów NuGet programu Visual Studio lub dodać pakiety przy użyciu `dotnet` narzędzia wiersza polecenia.

* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System .NET. http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. Core](https://www.nuget.org/packages/Azure.Core/)

Następnie w Eksplorator rozwiązań programu Visual Studio Otwórz plik _Function1.cs_ , w którym znajduje się przykładowy kod, i Dodaj następujące `using` instrukcje dla tych pakietów do funkcji. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Dodawanie kodu uwierzytelniania do funkcji

Teraz deklarujesz zmienne na poziomie klasy i dodajesz kod uwierzytelniania, który umożliwi funkcji dostęp do usługi Azure Digital bliźniaczych reprezentacji. Do funkcji w pliku _Function1.cs_ zostanie dodana następująca wartość.

* Kod odczytywania adresu URL usługi Digital bliźniaczych reprezentacji platformy Azure jako **zmiennej środowiskowej**. Dobrym sposobem jest odczytanie adresu URL usługi ze zmiennej środowiskowej, a nie kodowanie twarde w funkcji. Wartość tej zmiennej środowiskowej zostanie ustawiona [w dalszej części tego artykułu](#set-up-security-access-for-the-function-app). Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [*Zarządzanie aplikacją funkcji*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

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

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Te polecenia można uruchomić w [Azure Cloud Shell](https://shell.azure.com) lub w [lokalnej instalacji interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="assign-access-role"></a>Przypisywanie roli dostępu

Funkcja szkieletu z wcześniejszych przykładów wymaga, aby token okaziciela został przesłany do niego, aby można było uwierzytelniać za pomocą usługi Azure Digital bliźniaczych reprezentacji. Aby upewnić się, że ten token okaziciela jest zakończony, musisz skonfigurować uprawnienia [tożsamość usługi zarządzanej (msi)](../active-directory/managed-identities-azure-resources/overview.md) dla aplikacji funkcji, aby uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji. Należy to zrobić tylko raz dla każdej aplikacji funkcji.

Można użyć tożsamości zarządzanej przez system aplikacji funkcji, aby nadać jej rolę _**właściciela danych Digital bliźniaczych reprezentacji**_ na platformie Azure dla Twojego wystąpienia bliźniaczych reprezentacji. Spowoduje to nadanie uprawnienia aplikacji funkcji w wystąpieniu do wykonywania działań płaszczyzny danych. Następnie Udostępnij adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie zmiennej środowiskowej.

1. Użyj poniższego polecenia, aby wyświetlić szczegóły dotyczące tożsamości zarządzanej przez system dla funkcji. Zwróć uwagę na pole _principalId_ w danych wyjściowych.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Jeśli wynik jest pusty zamiast wyświetlania szczegółów tożsamości, Utwórz nową tożsamość zarządzaną przez system dla funkcji za pomocą tego polecenia:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > Następnie dane wyjściowe będą wyświetlały szczegóły tożsamości, w tym wartość _principalId_ , która jest wymagana do następnego kroku. 

1. Użyj wartości _principalId_ w poniższym poleceniu, aby przypisać tożsamość aplikacji funkcji do roli _Właściciel danych usługi Azure Digital Twins_ dla wystąpienia usługi Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Konfigurowanie ustawień aplikacji

Na koniec Udostępnij adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji dostępnego dla funkcji przez ustawienie dla niego **zmiennej środowiskowej** . Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [*Zarządzanie aplikacją funkcji*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> Adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji jest tworzony przez dodanie *https://* do początku *nazwy hosta* wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Aby wyświetlić nazwę hosta wraz ze wszystkimi właściwościami wystąpienia, można uruchomić polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Wykonaj następujące kroki w [Azure Portal](https://portal.azure.com/).

### <a name="assign-access-role"></a>Przypisywanie roli dostępu

Tożsamość zarządzana przypisana przez system umożliwia zasobom platformy Azure uwierzytelnianie w usługach w chmurze (na przykład Azure Key Vault) bez zapisywania poświadczeń w kodzie. Po włączeniu wszystkich wymaganych uprawnień można udzielić za pośrednictwem kontroli dostępu opartej na rolach platformy Azure. Cykl życia tego typu tożsamości zarządzanej jest powiązany z cyklem życia tego zasobu. Ponadto każdy zasób może mieć tylko jedną tożsamość zarządzaną przypisaną przez system.

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj aplikację funkcji, wpisując jej nazwę na pasku wyszukiwania. Wybierz swoją aplikację z wyników. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: Nazwa aplikacji funkcji jest przeszukiwana na pasku wyszukiwania portalu, a wynik wyszukiwania zostanie wyróżniony.":::

1. Na stronie aplikacja funkcji wybierz opcję _tożsamość_ na pasku nawigacyjnym po lewej stronie, aby współpracować z zarządzaną tożsamością dla funkcji. Na stronie _przypisana przez system_ Sprawdź, czy _stan_ jest ustawiony na wartość **włączone** (jeśli nie, ustaw ją teraz i *Zapisz* zmianę).

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na stronie tożsamość aplikacji funkcji jest ustawiona opcja stan na włączone." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Wybierz przycisk _przypisania roli platformy Azure_ , który spowoduje otwarcie strony *przypisania roli platformy Azure* .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: wyróżniony przycisk &quot;przypisań roli platformy Azure&quot; w obszarze uprawnienia na stronie tożsamości funkcji platformy Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Wybierz pozycję _+ Dodaj przypisanie roli (wersja zapoznawcza)_.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: Wyróżnij około + Dodaj przypisanie roli (wersja zapoznawcza) na stronie przydziały ról platformy Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Na stronie _Dodaj przypisanie roli (wersja zapoznawcza)_ , która zostanie otwarta, wybierz następujące wartości:

    * **Zakres**: grupa zasobów
    * **Subskrypcja**: wybierz subskrypcję platformy Azure
    * **Grupa zasobów**: Wybierz grupę zasobów z listy rozwijanej
    * **Rola**: Wybierz _właściciela danych Digital bliźniaczych reprezentacji platformy Azure_ z listy rozwijanej

    Następnie Zapisz szczegóły, naciskając przycisk _Zapisz_ .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Azure Portal: aby dodać nowe przypisanie roli (wersja zapoznawcza). Istnieją pola dla zakresu, subskrypcji, grupy zasobów i roli.":::

### <a name="configure-application-settings"></a>Konfigurowanie ustawień aplikacji

Aby adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji był dostępny dla funkcji, można ustawić dla niej **zmienną środowiskową** . Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [*Zarządzanie aplikacją funkcji*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). Ustawienia aplikacji są udostępniane jako zmienne środowiskowe w celu uzyskania dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. 

Aby ustawić zmienną środowiskową przy użyciu adresu URL wystąpienia, należy najpierw uzyskać adres URL, wyszukując nazwę hosta wystąpienia bliźniaczych reprezentacji cyfrowych platformy Azure. Wyszukaj swoje wystąpienie na pasku wyszukiwania [Azure Portal](https://portal.azure.com) . Następnie wybierz pozycję _Przegląd_ na lewym pasku nawigacyjnym, aby wyświetlić _nazwę hosta_. Skopiuj tę wartość.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na stronie Przegląd wystąpienia usługi Azure Digital bliźniaczych reprezentacji zostanie wyróżniona wartość Nazwa hosta.":::

Teraz można utworzyć ustawienie aplikacji z następującymi krokami:

1. Wyszukaj aplikację funkcji na pasku wyszukiwania portalu i wybierz ją z wyników.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: Nazwa aplikacji funkcji jest przeszukiwana na pasku wyszukiwania portalu, a wynik wyszukiwania zostanie wyróżniony.":::

1. Na pasku nawigacyjnym po lewej stronie wybierz pozycję _Konfiguracja_ . Na karcie _Ustawienia aplikacji_ wybierz pozycję _+ nowe ustawienie aplikacji_.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: na stronie Konfiguracja aplikacji funkcji zostanie wyróżniony przycisk służący do tworzenia nowego ustawienia aplikacji.":::

1. W otwartym oknie Użyj wartości Nazwa hosta skopiowanej powyżej, aby utworzyć ustawienie aplikacji.
    * **Nazwa**: ADT_SERVICE_URL
    * **Wartość**: https://{The-Azure-Digital-bliźniaczych reprezentacji-Host-Name}
    
    Wybierz _przycisk OK_ , aby utworzyć ustawienie aplikacji.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: przycisk OK zostanie wyróżniony po wypełnieniu pól Nazwa i wartość na stronie Dodawanie/Edytowanie ustawienia aplikacji.":::

1. Po utworzeniu ustawienia powinien on zostać wyświetlony z powrotem na karcie _Ustawienia aplikacji_ . Sprawdź, czy *ADT_SERVICE_URL* pojawia się na liście, a następnie Zapisz nowe ustawienie aplikacji, wybierając przycisk _Zapisz_ .

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: Strona Ustawienia aplikacji z wyróżnionym nowym ustawieniem ADT_SERVICE_URL. Przycisk Zapisz jest również wyróżniony.":::

1. Wszelkie zmiany w ustawieniach aplikacji wymagają ponownego uruchomienia aplikacji, dlatego po wyświetleniu monitu wybierz pozycję _Kontynuuj_ , aby ponownie uruchomić aplikację.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal: należy zauważyć, że wszelkie zmiany ustawień aplikacji z ponownym uruchomieniem aplikacji. Przycisk Kontynuuj jest wyróżniony.":::

---

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano kroki konfigurowania aplikacji funkcji na platformie Azure do użycia z usługą Azure Digital bliźniaczych reprezentacji.

Następnie zapoznaj się z tematem jak skompilować swoją podstawową funkcję, aby uzyskać IoT Hub dane do usługi Azure Digital bliźniaczych reprezentacji:
* [*Instrukcje: pozyskiwanie danych telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md)
