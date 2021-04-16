---
title: Konfigurowanie funkcji na platformie Azure w celu przetwarzania danych
titleSuffix: Azure Digital Twins
description: Zobacz, jak utworzyć na platformie Azure funkcję, która może uzyskać dostęp do usługi Digital Twins i może być wyzwalana przez tę funkcję.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480771"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Łączenie aplikacji funkcji na platformie Azure w celu przetwarzania danych

Bliźniacze reprezentacji cyfrowe można aktualizować na podstawie danych przy użyciu [tras zdarzeń za](concepts-route-events.md) pośrednictwem zasobów obliczeniowych. Na przykład funkcja wykonana przy użyciu usługi [Azure Functions](../azure-functions/functions-overview.md) może zaktualizować cyfrową bliźniacę w odpowiedzi na:
* Dane telemetryczne urządzenia z Azure IoT Hub.
* Zmiana właściwości lub inne dane z innej bliźniaczej reprezentacji cyfrowej w grafie bliźniaczej reprezentacji.

W tym artykule pokazano, jak utworzyć funkcję na platformie Azure do użycia z Azure Digital Twins. Aby utworzyć funkcję, wykonaj następujące podstawowe kroki:

1. Utwórz projekt Azure Functions w Visual Studio.
2. Napisz funkcję, która ma [Azure Event Grid](../event-grid/overview.md) wyzwalacz.
3. Dodaj kod uwierzytelniania do funkcji , aby uzyskać dostęp do Azure Digital Twins.
4. Opublikuj aplikację funkcji na platformie Azure.
5. Skonfiguruj [zabezpieczenia](concepts-security.md) dla aplikacji funkcji.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Wymaganie wstępne: Konfigurowanie Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Tworzenie aplikacji funkcji w Visual Studio

W Visual Studio 2019 r. wybierz pozycję **File** New Project  >  **(Plik nowy**  >  **projekt).** Wyszukaj szablon **Azure Functions** szablonu. Wybierz opcję **Dalej**.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okno dialogowe nowego projektu. Szablon Azure Functions projektu został wyróżniony.":::

Określ nazwę aplikacji funkcji, a następnie wybierz pozycję __Utwórz.__

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okno dialogowe służące do konfigurowania nowego projektu. Ustawienia obejmują nazwę projektu, lokalizację zapisu, wybór utworzenia nowego rozwiązania i nazwę rozwiązania.":::

Wybierz typ aplikacji funkcji dla **Event Grid,** a następnie wybierz pozycję __Utwórz.__

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Zrzut ekranu Visual Studio przedstawiający okno dialogowe służące do tworzenia nowej Azure Functions aplikacji. Opcja Event Grid wyzwalacza jest wyróżniona.":::

Po utworzeniu aplikacji funkcji program Visual Studio przykładowy kod w pliku *Function1.cs* w folderze projektu. Ta krótka funkcja służy do rejestrować zdarzenia.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Zrzut ekranu przedstawiający Visual Studio. Zostanie wyświetlone okno projektu dla nowego projektu. Kod przykładowej funkcji jest wyświetlany w pliku o nazwie Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Pisanie funkcji z wyzwalaczem Event Grid wyzwalacza

Funkcję można napisać, dodając zestaw SDK do aplikacji funkcji. Aplikacja funkcji współdziała z Azure Digital Twins pomocą zestawu SDK Azure Digital Twins [dla platformy .NET (C#).](/dotnet/api/overview/azure/digitaltwins/client) 

Aby użyć zestawu SDK, w projekcie należy uwzględnić następujące pakiety. Zainstaluj pakiety przy użyciu menedżera Visual Studio NuGet. Możesz też dodać pakiety przy użyciu `dotnet` polecenia w narzędziu wiersza polecenia.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Następnie w Visual Studio Eksplorator rozwiązań otwórz plik _Function1.cs,_ który zawiera przykładowy kod. Dodaj następujące `using` instrukcje dla pakietów.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Dodawanie kodu uwierzytelniania do funkcji

Teraz zadeklaruj zmienne na poziomie klasy i dodaj kod uwierzytelniania, który umożliwi funkcji dostęp do Azure Digital Twins. Dodaj zmienne i kod do funkcji w pliku _Function1.cs._

* **Kod odczytu adresu URL Azure Digital Twins jako zmiennej środowiskowej.** Dobrym rozwiązaniem jest odczytanie adresu URL usługi ze zmiennej środowiskowej zamiast kodowania go w funkcji. Wartość tej zmiennej środowiskowej ustawisz w [dalszej części tego artykułu.](#set-up-security-access-for-the-function-app) Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [Zarządzanie aplikacją funkcji](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Zmienna statyczna do przechowywania wystąpienia HttpClient.** Tworzenie aplikacji HttpClient jest stosunkowo kosztowne, dlatego chcemy uniknąć jej tworzenia dla każdego wywołania funkcji.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Poświadczenia tożsamości zarządzanej.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Zmienna lokalna _DigitalTwinsClient._** Dodaj zmienną wewnątrz funkcji w celu przechowywania Azure Digital Twins klienta. Nie *należy oznaczać* tej zmiennej jako statycznej wewnątrz klasy.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Sprawdzanie wartości null dla _adtInstanceUrl_.** Dodaj sprawdzanie wartości null, a następnie opakuj logikę funkcji w blok try/catch, aby przechwyć wszelkie wyjątki.

Po tych zmianach kod funkcji będzie wyglądać podobnie do poniższego przykładu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Teraz, gdy aplikacja została napisana, możesz opublikować ją na platformie Azure.

## <a name="publish-the-function-app-to-azure"></a>Publikowanie aplikacji funkcji na platformie Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>Weryfikowanie publikacji funkcji

1. Zaloguj się przy użyciu swoich poświadczeń w [Azure Portal](https://portal.azure.com/).
2. W polu wyszukiwania w górnej części okna wyszukaj nazwę aplikacji funkcji, a następnie wybierz ją.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. W polu wyszukiwania wprowadź nazwę aplikacji funkcji." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Na stronie **Aplikacji funkcji,** która zostanie otwarta, w menu po lewej stronie wybierz pozycję **Funkcje.** Jeśli funkcja została pomyślnie opublikowana, jej nazwa zostanie wyświetlona na liście.

    > [!Note] 
    > Może być konieczne odczekenie kilku minut lub odświeżenie strony kilka razy, zanim funkcja pojawi się na liście opublikowanych funkcji.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Wyświetlanie opublikowanych funkcji w Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Aby uzyskać Azure Digital Twins, aplikacja funkcji wymaga tożsamości zarządzanej przez system z uprawnieniami dostępu do Azure Digital Twins wystąpienia. Skonfigurujesz to w następnej.

## <a name="set-up-security-access-for-the-function-app"></a>Konfigurowanie dostępu zabezpieczeń dla aplikacji funkcji

Dostęp do zabezpieczeń dla aplikacji funkcji można skonfigurować przy użyciu interfejsu wiersza polecenia platformy Azure lub interfejsu Azure Portal. Postępuj zgodnie z instrukcjami dla preferowanej opcji.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Uruchom te polecenia [w](https://shell.azure.com) Azure Cloud Shell lub lokalnej instalacji [interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
Możesz użyć tożsamości zarządzanej przez system aplikacji funkcji, aby nadać jej rolę **właściciela** Azure Digital Twins danych dla twojego Azure Digital Twins wystąpienia. Rola zapewnia aplikacji funkcji uprawnienia w wystąpieniu do wykonywania działań płaszczyzny danych. Następnie udostępnij adres URL wystąpienia funkcji, ustawiając zmienną środowiskową.

### <a name="assign-an-access-role"></a>Przypisywanie roli dostępu

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Szkielet funkcji we wcześniejszych przykładach wymaga, aby do niego został przekazany token bearer. Jeśli token bearer nie zostanie przekazany, aplikacja funkcji nie może uwierzytelnić się za pomocą Azure Digital Twins. 

Aby upewnić się, że token okaziciela został przekazany, skonfiguruj uprawnienia tożsamości zarządzanych, aby aplikacja funkcji mieć dostęp do Azure Digital Twins. [](../active-directory/managed-identities-azure-resources/overview.md) Te uprawnienia można skonfigurować tylko raz dla każdej aplikacji funkcji.


1. Użyj następującego polecenia, aby wyświetlić szczegóły tożsamości zarządzanej przez system dla funkcji. Zanotuj `principalId` pole w danych wyjściowych.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Jeśli wynik jest pusty zamiast wyświetlania szczegółów tożsamości, utwórz nową tożsamość zarządzaną przez system dla funkcji za pomocą tego polecenia:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > W danych wyjściowych są wyświetlane szczegóły tożsamości, w tym `principalId` wartość wymagana w następnym kroku. 

1. Użyj wartości w poniższym poleceniu, aby przypisać tożsamość aplikacji funkcji do roli właściciela Azure Digital Twins danych dla `principalId` Azure Digital Twins wystąpienia. 

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Konfigurowanie ustawień aplikacji

Udostępnij adres URL wystąpienia funkcji, ustawiając dla niego zmienną środowiskową. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [Zarządzanie aplikacją funkcji](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> Adres URL Azure Digital Twins jest made przez dodanie https:// *na* początku nazwy hosta wystąpienia. Aby wyświetlić nazwę hosta wraz ze wszystkimi właściwościami wystąpienia, `az dt show --dt-name <your-Azure-Digital-Twins-instance>` uruchom .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Wykonaj następujące kroki w Azure Portal [.](https://portal.azure.com/)

### <a name="assign-an-access-role"></a>Przypisywanie roli dostępu

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Tożsamość zarządzana przypisana przez system umożliwia zasobom platformy Azure uwierzytelnianie w usługach w chmurze (na przykład Azure Key Vault) bez przechowywania poświadczeń w kodzie. Po włączeniu tożsamości zarządzanej przypisanej przez system wszystkie niezbędne uprawnienia można przyznać za pośrednictwem kontroli dostępu opartej na rolach platformy Azure. 

Cykl życia tożsamości zarządzanej tego typu jest powiązany z cyklem życia tego zasobu. Ponadto każdy zasób może mieć tylko jedną tożsamość zarządzaną przypisaną przez system.

1. W [Azure Portal](https://portal.azure.com/)wyszukaj aplikację funkcji, wpisując jej nazwę w polu wyszukiwania. Wybierz aplikację z wyników. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Nazwa aplikacji funkcji znajduje się na pasku wyszukiwania portalu, a wynik wyszukiwania jest wyróżniony.":::

1. Na stronie aplikacji funkcji w menu po lewej stronie wybierz pozycję __Tożsamość,__ aby pracować z tożsamością zarządzaną dla funkcji. Na stronie __Przypisana przez system__ sprawdź, czy dla ustawienia __Stan__ ustawiono wartość **Wł.**. Jeśli tak nie jest, ustaw ją teraz, a następnie **zapisz** zmianę.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Na stronie Tożsamość dla aplikacji funkcji opcja Stan jest ustawiona na wartość Wł." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Wybierz __pozycję Przypisania ról platformy Azure.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Na stronie Tożsamość funkcji platformy Azure w obszarze Uprawnienia wyróżniony jest przycisk Przypisania ról platformy Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Wybierz __pozycję + Dodaj przypisanie roli (wersja zapoznawcza).__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Na stronie Przypisania ról platformy Azure wyróżniony jest przycisk Dodaj przypisanie roli (wersja zapoznawcza)." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Na stronie __Dodawanie przypisania roli (wersja zapoznawcza)__ wybierz następujące wartości:

    * **Zakres:** _grupa zasobów_
    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** wybierz grupę zasobów.
    * **Rola:** _Azure Digital Twins danych_

    Zapisz szczegóły, wybierając pozycję __Zapisz.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, w którym pokazano, jak dodać nowe przypisanie roli. W oknie dialogowym są wyświetlane pola Zakres, Subskrypcja, Grupa zasobów i Rola.":::

### <a name="configure-application-settings"></a>Konfigurowanie ustawień aplikacji

Aby adres URL wystąpienia Azure Digital Twins był dostępny dla funkcji, możesz ustawić zmienną środowiskową. Ustawienia aplikacji są udostępniane jako zmienne środowiskowe, aby umożliwić dostęp do Azure Digital Twins wystąpienia. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [Zarządzanie aplikacją funkcji](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Aby ustawić zmienną środowiskową przy użyciu adresu URL wystąpienia, najpierw znajdź nazwę hosta wystąpienia: 

1. Wyszukaj wystąpienie w [Azure Portal](https://portal.azure.com). 
1. W menu po lewej stronie wybierz pozycję __Przegląd.__ 
1. Skopiuj __wartość Nazwa__ hosta.

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Na stronie Przegląd wystąpienia wartość nazwy hosta jest wyróżniona.":::

Teraz możesz utworzyć ustawienie aplikacji:

1. Na pasku wyszukiwania portalu wyszukaj aplikację funkcji, a następnie wybierz ją z wyników.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Nazwa aplikacji funkcji jest przeszukiwana na pasku wyszukiwania portalu. Wynik wyszukiwania zostanie wyróżniony.":::

1. Po lewej stronie wybierz pozycję __Konfiguracja.__ Następnie na karcie __Ustawienia aplikacji__ wybierz pozycję + Nowe __ustawienie aplikacji.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Na karcie Konfiguracja aplikacji funkcji wyróżniony jest przycisk tworzenia ustawienia Nowa aplikacja.":::

1. W otwieranym oknie użyj skopiowanej wartości nazwy hosta, aby utworzyć ustawienie aplikacji.
    * **Nazwa:** ADT_SERVICE_URL
    * **Wartość:** https://{twoja-azure-digital-twins-host-name}
    
    Wybierz __przycisk OK,__ aby utworzyć ustawienie aplikacji.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Na stronie Dodawanie/edytowanie ustawień aplikacji wypełniane są pola Nazwa i Wartość. Przycisk O K został wyróżniony.":::

1. Po utworzeniu ustawienia powinno ono zostać wyświetlone na __karcie Ustawienia__ aplikacji. Sprawdź, **ADT_SERVICE_URL** na liście. Następnie zapisz nowe ustawienie aplikacji, wybierając pozycję __Zapisz.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Na karcie ustawień aplikacji wyróżnione zostanie nowe ustawienie A D T SERVICE U R L. Przycisk Zapisz również został wyróżniony.":::

1. Wszelkie zmiany ustawień aplikacji wymagają ponownego  uruchomienia aplikacji, dlatego po wyświetleniu monitu wybierz pozycję Kontynuuj, aby ponownie uruchomić aplikację.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Zrzut ekranu przedstawiający Azure Portal. Należy pamiętać, że wszelkie zmiany ustawień aplikacji spowoduje ponowne uruchomienie aplikacji. Przycisk Kontynuuj został wyróżniony.":::

---

## <a name="next-steps"></a>Następne kroki

W tym artykule skonfigurujemy aplikację funkcji na platformie Azure do użycia z Azure Digital Twins. Następnie zobacz, jak utworzyć podstawową funkcję w celu IoT Hub [danych do Azure Digital Twins](how-to-ingest-iot-hub-data.md).
