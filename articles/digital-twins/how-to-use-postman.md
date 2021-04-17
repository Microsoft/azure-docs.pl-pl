---
title: Wykonywanie żądań za pomocą narzędzia Postman
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak skonfigurować program Postman i używać go do testowania Azure Digital Twins API.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588007"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Jak wysyłać żądania do interfejsów API Azure Digital Twins Postman

[Postman](https://www.getpostman.com/) to narzędzie do testowania REST, które zapewnia kluczowe funkcje żądań HTTP w graficznym interfejsie użytkownika opartym na wtyczkach i na komputerze stacjonarnym. Za jego pomocą można tworzyć żądania HTTP i przesyłać je do Azure Digital Twins [API REST.](how-to-use-apis-sdks.md)

W tym artykule opisano sposób konfigurowania klienta [REST programu Postman](https://www.getpostman.com/) do interakcji z interfejsami API Azure Digital Twins, w następujący sposób:

1. Użyj interfejsu wiersza polecenia platformy [**Azure, aby uzyskać token bearer,**](#get-bearer-token) który będzie używać do żądania interfejsu API w programie Postman.
1. Skonfiguruj kolekcję [**Postman i**](#about-postman-collections) skonfiguruj klienta REST postman do uwierzytelniania przy użyciu tokenu bearer. Podczas konfigurowania kolekcji można wybrać jedną z tych opcji:
    1. [](#import-collection-of-azure-digital-twins-apis) Zaimportuj wstępnie sbudowaną kolekcję Azure Digital Twins API.
    1. [**Utwórz**](#create-your-own-collection) własną kolekcję od podstaw.
1. [**Dodaj żądania**](#add-an-individual-request) do skonfigurowanej kolekcji i wyślij je do Azure Digital Twins API.

Azure Digital Twins dwa zestawy interfejsów API, z których można pracować: **płaszczyzna danych** i **płaszczyzna sterowania**. Aby uzyskać więcej informacji o różnicach między tymi zestawami interfejsów API, zobacz [*How-to: Use the Azure Digital Twins APIs and SDKs*](how-to-use-apis-sdks.md)(Jak używać interfejsów API Azure Digital Twins i zestawów SDK). Ten artykuł zawiera informacje dotyczące obu zestawów interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby kontynuować używanie narzędzia Postman do uzyskiwania dostępu do interfejsów API Azure Digital Twins, należy skonfigurować wystąpienie Azure Digital Twins i pobrać program Postman. W pozostałej części tej sekcji oswrócą Cię te kroki.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurowanie Azure Digital Twins wystąpienia

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Pobieranie aplikacji Postman

Następnie pobierz wersję komputerową klienta Postman. Przejdź do [*www.getpostman.com/apps*](https://www.getpostman.com/apps) i postępuj zgodnie z monitami, aby pobrać aplikację.

## <a name="get-bearer-token"></a>Uzyskiwanie tokenu okaziciela

Teraz, po skonfigurowaniu programu Postman i wystąpienia usługi Azure Digital Twins, musisz uzyskać token okaziciela, za pomocą których żądania postman mogą autoryzować dane względem interfejsów API Azure Digital Twins.

Istnieje kilka możliwych sposobów uzyskania tego tokenu. W tym artykule użyto interfejsu [wiersza polecenia](/cli/azure/install-azure-cli) platformy Azure do zalogowania się do konta platformy Azure i uzyskania w ten sposób tokenu.

Jeśli masz zainstalowany lokalnie [interfejs](/cli/azure/install-azure-cli)wiersza polecenia platformy Azure, możesz uruchomić wiersz polecenia na swojej maszynie, aby uruchomić następujące polecenia.
W przeciwnym razie możesz otworzyć [okno Azure Cloud Shell](https://shell.azure.com) w przeglądarce i uruchomić w tym miejscu polecenia.

1. Najpierw upewnij się, że zalogowano się na platformie Azure przy użyciu odpowiednich poświadczeń, uruchamiając to polecenie:

    ```azurecli-interactive
    az login
    ```

2. Następnie użyj polecenia [az account get-access-token,](/cli/azure/account#az_account_get_access_token) aby uzyskać token okaziciela z dostępem do Azure Digital Twins tokenu. W tym poleceniu przekażesz identyfikator zasobu dla punktu końcowego usługi Azure Digital Twins, aby uzyskać token dostępu, który może uzyskać dostęp do Azure Digital Twins zasobów. 

    Kontekst wymagany dla tokenu zależy od zestawu interfejsów API, których używasz, [](how-to-use-apis-sdks.md#overview-data-plane-apis) dlatego użyj poniższych kart, aby wybrać między interfejsami API płaszczyzny danych i [płaszczyzny](how-to-use-apis-sdks.md#overview-control-plane-apis) sterowania.

    # <a name="data-plane"></a>[Płaszczyzna danych](#tab/data-plane)
    
    Aby uzyskać token do użycia z interfejsami API **płaszczyzny** danych, użyj następującej wartości statycznej dla kontekstu tokenu: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Jest to identyfikator zasobu dla punktu końcowego Azure Digital Twins końcowego usługi.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Płaszczyzna sterowania](#tab/control-plane)
    
    Aby uzyskać token do użycia z interfejsami API **płaszczyzny** sterowania, użyj następującej wartości dla kontekstu tokenu: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Jeśli musisz uzyskać dostęp do wystąpienia usługi Azure Digital Twins przy użyciu jednostki usługi lub konta użytkownika należącego do innej dzierżawy usługi Azure Active Directory niż wystąpienie, musisz zażądać **tokenu** z dzierżawy "macierzyszej" wystąpienia usługi Azure Digital Twins. Aby uzyskać więcej informacji na temat tego procesu, zobacz [*Jak napisać kod uwierzytelniania aplikacji*](how-to-authenticate-client.md#authenticate-across-tenants).

3. Skopiuj wartość w wyniku i zapisz ją do `accessToken` użycia w następnej sekcji. Jest to wartość **tokenu,** która zostanie dostarczana do aplikacji Postman w celu autoryzowania żądań.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Zrzut ekranu konsoli przedstawiający wynik polecenia az account get-access-token. Pole accessToken i jego przykładowa wartość są wyróżnione.":::

>[!TIP]
>Ten token jest ważny przez co najmniej pięć minut i maksymalnie 60 minut. Jeśli zabraknie ci czasu dla bieżącego tokenu, możesz powtórzyć kroki opisane w tej sekcji, aby uzyskać nowy.

Następnie skonfigurujesz obiekt Postman do używania tego tokenu w celu żądania interfejsu API do Azure Digital Twins.

## <a name="about-postman-collections"></a>Informacje o kolekcjach Postman

Żądania w programie Postman są zapisywane w **kolekcjach** (grupach żądań). Podczas tworzenia kolekcji w celu grupowania żądań można zastosować typowe ustawienia do wielu żądań jednocześnie. Może to znacznie uprościć autoryzację, jeśli zamierzasz utworzyć więcej niż jedno żądanie względem interfejsów API usługi Azure Digital Twins, ponieważ wystarczy skonfigurować te szczegóły tylko raz dla całej kolekcji.

Podczas pracy z Azure Digital Twins możesz rozpocząć pracę, importując wstępnie sbudowaną kolekcję wszystkich żądań [Azure Digital Twins danych.](#import-collection-of-azure-digital-twins-apis) Możesz to zrobić, jeśli eksplorujesz interfejsy API i chcesz szybko skonfigurować projekt z przykładami żądań.

Alternatywnie możesz również zacząć od podstaw, [](#create-your-own-collection) tworząc własną pustą kolekcję i wypełniając ją poszczególnymi żądaniami, które wywołują tylko potrzebne interfejsy API. 

W poniższych sekcjach opisano oba te procesy. Pozostała część artykułu dotyczy lokalnej aplikacji Postman, więc teraz otwórz aplikację Postman na komputerze.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importowanie kolekcji Azure Digital Twins API

Aby szybko rozpocząć pracę z usługą Azure Digital Twins postman, można zaimportować wstępnie sbudowaną kolekcję żądań dla interfejsów API Azure Digital Twins Api.

### <a name="download-the-collection-file"></a>Pobieranie pliku kolekcji

Pierwszym krokiem importowania zestawu interfejsów API jest pobranie kolekcji. Wybierz kartę poniżej, aby wybrać płaszczyznę danych lub płaszczyznę sterowania, aby wyświetlić wstępnie sbudowaną opcję kolekcji.

# <a name="data-plane"></a>[Płaszczyzna danych](#tab/data-plane)

Obecnie dostępne są dwie Azure Digital Twins płaszczyzny danych do wyboru:
* [**Azure Digital Twins Postman Collection:**](https://github.com/microsoft/azure-digital-twins-postman-samples)ta kolekcja zapewnia proste środowisko rozpoczynania pracy Azure Digital Twins w programie Postman. Żądania obejmują przykładowe dane, dzięki czemu można je uruchamiać przy minimalnych wymaganych edycjach. Wybierz tę kolekcję, jeśli chcesz uzyskać czytelny zestaw kluczowych żądań interfejsu API zawierających przykładowe informacje.
    - Aby znaleźć kolekcję, przejdź do linku do repo i otwórz plik o *nazwiepostman_collection.jsna stronie*.
* Azure Digital Twins danych **[Swagger:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)** to repo zawiera kompletny plik programu Swagger dla zestawu interfejsów API programu Azure Digital Twins, który można pobrać i zaimportować do programu Postman jako kolekcję. Zapewni to kompleksowy zestaw wszystkich żądań interfejsu API, ale z pustymi jednostkami danych, a nie przykładami danych. Wybierz tę kolekcję, jeśli chcesz mieć dostęp do każdego wywołania interfejsu API i samodzielnie wypełnij wszystkie dane.
    - Aby znaleźć kolekcję, przejdź do linku do repo i wybierz folder dla najnowszej wersji specyfikacji. W tym miejscu otwórz plik o nazwie *digitaltwins.jsna stronie*.

# <a name="control-plane"></a>[Płaszczyzna sterowania](#tab/control-plane)

Kolekcja obecnie dostępna dla płaszczyzny sterowania to [**Azure Digital Twins swagger płaszczyzny sterowania**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). To repo zawiera kompletny plik programu Swagger dla zestawu Azure Digital Twins API, który można pobrać i zaimportować do programu Postman jako kolekcję. Zapewni to kompleksowy zestaw wszystkich żądań interfejsu API.

Aby znaleźć kolekcję, przejdź do linku do repo i wybierz folder dla najnowszej wersji specyfikacji. W tym miejscu otwórz plik o nazwie *digitaltwins.jsna stronie*.

---

Poniżej opisano sposób pobierania wybranej kolekcji na komputer, aby można było zaimportować ją do aplikacji Postman.
1. Użyj powyższych linków, aby otworzyć plik kolekcji w usłudze GitHub w przeglądarce.
1. Wybierz przycisk **Raw** (Nieprzetworzone), aby otworzyć nieprzetworzny tekst pliku.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Zrzut ekranu przedstawiający płaszczyznę digitaltwins.jsw pliku w usłudze GitHub. Wokół przycisku Raw (Nieprzetworzone) znajduje się wyróżnienie." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Skopiuj tekst z okna i wklej go do nowego pliku na maszynie.
1. Zapisz plik z rozszerzeniem *json* (nazwa pliku może być dowolny, o ile będzie można go zapamiętać, aby później znaleźć plik).

### <a name="import-the-collection"></a>Importowanie kolekcji

Następnie zaimportuj kolekcję do postmanu.

1. W głównym oknie programu Postman wybierz **przycisk Importuj.**
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Zrzut ekranu przedstawiający nowo otwarte okno programu Postman. Przycisk &quot;Importuj&quot; został wyróżniony." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. W oknie Import (Importuj) wybierz pozycję **Upload Files (Przekaż** pliki) i przejdź do pliku kolekcji na utworzonej wcześniej maszynie. Wybierz pozycję Otwórz.
1. Wybierz przycisk **Importuj,** aby potwierdzić.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Zrzut ekranu przedstawiający okno &quot;Importowanie&quot; programu Postman z plikiem do zaimportowania jako kolekcją i przyciskiem Importuj.":::

Nowo zaimportowaną kolekcję można teraz wyświetlić w głównym widoku narzędzia Postman na karcie Kolekcje.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Zrzut ekranu przedstawiający główne okno programu Postman. Nowo zaimportowana kolekcja zostanie wyróżniona na karcie &quot;Kolekcje&quot;." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Następnie przejdź do następnej sekcji, aby dodać token elementów bearer do kolekcji w celu autoryzacji i połączyć go z wystąpieniem usługi Azure Digital Twins.

### <a name="configure-authorization"></a>Konfigurowanie autoryzacji

Następnie edytuj utworzoną kolekcję, aby skonfigurować pewne szczegóły dostępu. Wyróżnij utworzoną kolekcję i wybierz **ikonę Wyświetl więcej akcji,** aby wyświetlić menu. Wybierz pozycję **Edytuj**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Zrzut ekranu przedstawiający postman. Ikona &quot;Wyświetl więcej akcji&quot; dla zaimportowanych kolekcji jest wyróżniona, a opcja &quot;Edytuj&quot; jest wyróżniona w opcjach." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Wykonaj następujące kroki, aby dodać token elementów bearer do kolekcji w celu autoryzacji. W tym miejscu użyjesz wartości **tokenu** zebranej w sekcji Pobieranie [tokenu elementów bearer,](#get-bearer-token) aby użyć jej dla wszystkich żądań interfejsu API w kolekcji.

1. W oknie dialogowym edycji kolekcji upewnij się, że jesteś na **karcie Autoryzacja.** 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edytowania zaimportowanych kolekcji w programie Postman z kartą &quot;Autoryzacja&quot;." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. W polu Typ ustaw **wartość OAuth 2.0,** wklej token dostępu w polu Token dostępu, a następnie wybierz pozycję **Zapisz.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji narzędzia Postman dla zaimportowanych kolekcji na karcie &quot;Authorization&quot; (Autoryzacja). Typ to &quot;OAuth 2.0&quot;, a pole Token dostępu jest wyróżnione." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Dodatkowa konfiguracja

# <a name="data-plane"></a>[Płaszczyzna danych](#tab/data-plane)

W przypadku tworzenia kolekcji płaszczyzny [danych](how-to-use-apis-sdks.md#overview-data-plane-apis) pomóż kolekcji łatwo połączyć się  z zasobami Azure Digital Twins, ustawiając pewne zmienne dostarczone z kolekcjami. Gdy wiele żądań w kolekcji wymaga tej samej wartości (na przykład nazwy hosta wystąpienia Azure Digital Twins), można przechowywać wartość w zmiennej, która ma zastosowanie do każdego żądania w kolekcji. Obie kolekcje do pobrania Azure Digital Twins są ze wstępnie utworzonymi zmiennymi, które można ustawić na poziomie kolekcji.

1. W oknie dialogowym edycji kolekcji przejdź do **karty Zmienne.**

1. Użyj nazwy hosta wystąpienia **z** sekcji [*Wymagania wstępne,*](#prerequisites) aby ustawić pole BIEŻĄCA WARTOŚĆ odpowiedniej zmiennej. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji zaimportowanych kolekcji w programie Postman z kartą &quot;Zmienne&quot;. Pole &quot;BIEŻĄCA WARTOŚĆ&quot; jest wyróżnione." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Jeśli kolekcja zawiera dodatkowe zmienne, wypełnij i zapisz również te wartości.

Po zakończeniu powyższych kroków konfigurowanie kolekcji zakończy się. Jeśli chcesz, możesz zamknąć kartę edycji dla kolekcji.

# <a name="control-plane"></a>[Płaszczyzna sterowania](#tab/control-plane)

Jeśli robisz kolekcję [płaszczyzny](how-to-use-apis-sdks.md#overview-control-plane-apis) sterowania, wszystko, czego potrzebujesz, aby skonfigurować kolekcję. Jeśli chcesz, możesz zamknąć kartę edycji kolekcji i przejść do następnej sekcji.

--- 

### <a name="explore-requests"></a>Eksplorowanie żądań

Następnie zapoznaj się z żądaniami w kolekcji Azure Digital Twins API. Możesz rozwinąć kolekcję, aby wyświetlić wstępnie utworzone żądania (posortowane według kategorii operacji). 

Różne żądania wymagają różnych informacji o wystąpieniu i jego danych. Aby wyświetlić wszystkie informacje wymagane do tworzenia określonego żądania, zapoznaj się ze szczegółami żądania w dokumentacji referencyjnej interfejsu API REST Azure Digital Twins [rest.](/rest/api/azure-digitaltwins/)

Szczegóły żądania w kolekcji Postman można edytować, korzystając z poniższych kroków:

1. Wybierz go z listy, aby ściągnąć jego edytowalne szczegóły. 

1. Wypełnij wartości zmiennych wymienionych na karcie **Params** w obszarze **Zmienne ścieżki.**

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Zrzut ekranu przedstawiający postman. Kolekcja jest rozwinięta w celu pokazania żądania. Sekcja &quot;Zmienne ścieżki&quot; jest wyróżniona w szczegółach żądania." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Podaj wymagane **nagłówki lub** **szczegóły** treści na odpowiednich kartach.

Po podano wszystkie wymagane szczegóły, możesz uruchomić żądanie przy użyciu **przycisku Wyślij.**

Możesz również dodać własne żądania do kolekcji, korzystając z procesu opisanego w sekcji [*Dodawanie pojedynczego*](#add-an-individual-request) żądania poniżej.

## <a name="create-your-own-collection"></a>Tworzenie własnej kolekcji

Zamiast importować istniejącą kolekcję wszystkich Azure Digital Twins API, możesz również utworzyć własną kolekcję od podstaw. Następnie możesz wypełnić je poszczególnymi żądaniami, korzystając z dokumentacji Azure Digital Twins [API REST.](/rest/api/azure-digitaltwins/)

### <a name="create-a-postman-collection"></a>Tworzenie kolekcji Postman

1. Aby utworzyć kolekcję, wybierz **przycisk Nowy** w głównym oknie postman.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Zrzut ekranu przedstawiający główne okno programu Postman. Przycisk &quot;Nowy&quot; został wyróżniony." lightbox="media/how-to-use-postman/postman-new.png":::

    Wybierz typ **kolekcji**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe &quot;Tworzenie nowego&quot; w programie Postman. Opcja &quot;Kolekcja&quot; jest wyróżniona.":::

1. Spowoduje to otwarcie karty służącej do wypełniania szczegółów nowej kolekcji. Wybierz ikonę Edytuj obok domyślnej nazwy kolekcji **(Nowa** kolekcja), aby zastąpić ją własną nazwą. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji nowej kolekcji w programie Postman. Ikona Edytuj obok nazwy &quot;Nowa kolekcja&quot; jest wyróżniona." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Następnie przejdź do następnej sekcji, aby dodać token elementów bearer do kolekcji w celu autoryzacji.

### <a name="configure-authorization"></a>Konfigurowanie autoryzacji

Wykonaj następujące kroki, aby dodać token elementów bearer do kolekcji w celu autoryzacji. W tym miejscu użyjesz wartości **tokenu** zebranej w sekcji Pobieranie [tokenu](#get-bearer-token) okaziciela, aby użyć jej dla wszystkich żądań interfejsu API w kolekcji.

1. W oknie dialogowym edycji nowej kolekcji przejdź do karty **Autoryzacja.**

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji nowej kolekcji w programie Postman z kartą &quot;Autoryzacja&quot;." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. W polu Typ ustaw **wartość OAuth 2.0,** wklej token dostępu w polu Token dostępu, a następnie wybierz pozycję **Zapisz.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji narzędzia Postman dla nowej kolekcji na karcie &quot;Autoryzacja&quot;. Typ to &quot;OAuth 2.0&quot;, a pole Token dostępu jest wyróżnione." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Po zakończeniu powyższych kroków konfigurowanie kolekcji zakończy się. Jeśli chcesz, możesz zamknąć kartę edycji nowej kolekcji.

Nową kolekcję można wyświetlić w głównym widoku narzędzia Postman na karcie Kolekcje.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Zrzut ekranu przedstawiający główne okno programu Postman. Nowo utworzona kolekcja niestandardowa zostanie wyróżniona na karcie &quot;Kolekcje&quot;." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Dodawanie pojedynczego żądania

Po skonfigurowaniu kolekcji możesz dodać własne żądania do interfejsów API usługi Azure Digital Twin.

1. Aby utworzyć żądanie, ponownie użyj **przycisku** Nowy.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Zrzut ekranu przedstawiający główne okno programu Postman. Przycisk &quot;Nowy&quot; został wyróżniony." lightbox="media/how-to-use-postman/postman-new.png":::

    Wybierz typ **żądania**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe &quot;Tworzenie nowego&quot; w programie Postman. Opcja &quot;Żądanie&quot; jest wyróżniona.":::

1. Ta akcja powoduje otwarcie okna ZAPISZ ŻĄDANIE, w którym można wprowadzić nazwę żądania, podać opcjonalny opis i wybrać kolekcję, do której należy. Wypełnij szczegóły i zapisz żądanie w utworzonej wcześniej kolekcji.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Zrzut ekranu przedstawiający okno &quot;Zapisywanie żądania&quot; w programie Postman z opisanymi polami. Przycisk &quot;Zapisz w Azure Digital Twins kolekcji&quot; został wyróżniony.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Teraz możesz wyświetlić żądanie w obszarze kolekcji i wybrać je, aby wyświetlić jego edytowalne szczegóły.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Zrzut ekranu przedstawiający postman. Kolekcja Azure Digital Twins jest rozwinięta w celu pokazania szczegółów żądania." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Ustawianie szczegółów żądania

Aby wysłać żądanie postmana do jednego z Azure Digital Twins API, potrzebny będzie adres URL interfejsu API i informacje o wymaganych szczegółach. Te informacje można znaleźć w dokumentacji referencyjnej Azure Digital Twins [REST API](/rest/api/azure-digitaltwins/).

Aby kontynuować przykładowe zapytanie, w tym artykule użyjemy interfejsu API zapytań (i jego dokumentacji referencyjnej [)](/rest/api/digital-twins/dataplane/query/querytwins)do wykonywania zapytań dla wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu.

1. Pobierz adres URL żądania i wpisz go z dokumentacji referencyjnej. W przypadku interfejsu API zapytań jest to obecnie *POST `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. W programie Postman ustaw typ żądania i wprowadź adres URL żądania, wypełniając symbole zastępcze w adresie URL zgodnie z potrzebami. W tym miejscu użyjemy nazwy **hosta** wystąpienia z sekcji [*Wymagania*](#prerequisites) wstępne.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Zrzut ekranu przedstawiający szczegóły nowego żądania w programie Postman. Adres URL zapytania z dokumentacji referencyjnej został wypełniony w polu adresu URL żądania." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Sprawdź, czy parametry wyświetlane dla żądania na **karcie Parametry** są zgodne z parametrami opisanymi w dokumentacji referencyjnej. W przypadku tego żądania w programie Postman parametr został wypełniony automatycznie po wprowadzeniu adresu URL żądania `api-version` w poprzednim kroku. W przypadku interfejsu API zapytań jest to jedyny wymagany parametr, więc ten krok jest wykonywane.
1. Na karcie **Autoryzacja** ustaw dla ustawienia Typ wartość **Dziedzicz uwierzytelnianie po nadrzędnej .** Oznacza to, że to żądanie będzie używać autoryzacji ustawionej wcześniej dla całej kolekcji.
1. Sprawdź, czy nagłówki wyświetlane dla żądania na karcie **Nagłówki** są zgodne z nagłówkami opisanymi w dokumentacji referencyjnej. W przypadku tego żądania kilka nagłówków zostało wypełnionych automatycznie. W przypadku interfejsu API zapytań żadna z opcji nagłówka nie jest wymagana, więc ten krok jest wykonywane.
1. Sprawdź, czy treść żądania wyświetlana na karcie **Treść** odpowiada potrzebom opisanym w dokumentacji referencyjnej. W przypadku interfejsu API zapytań treść JSON jest wymagana do podania tekstu zapytania. Oto przykładowa treść tego żądania, która wysyła zapytanie do wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Zrzut ekranu przedstawiający szczegóły nowego żądania w programie Postman na karcie Treść. Zawiera nieprzetworzoną treść JSON z zapytaniem &quot;SELECT * FROM DIGITALTWINS&quot;." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Aby uzyskać więcej informacji na temat tworzenia zapytań Azure Digital Twins, zobacz Tworzyć zapytania dotyczące [*grafu bliźniaczych reprezentacji.*](how-to-query-graph.md)

1. Zapoznaj się z dokumentacją referencyjną, aby uzyskać informacje o innych polach, które mogą być wymagane dla twojego typu żądania. W przypadku interfejsu API zapytań wszystkie wymagania zostały teraz spełnione w żądaniu Postman, więc ten krok jest już spełniony.
1. Użyj **przycisku Wyślij,** aby wysłać ukończone żądanie.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Zrzut ekranu przedstawiający program Postman ze szczegółami nowego żądania. Przycisk Wyślij został wyróżniony." lightbox="media/how-to-use-postman/postman-request-send.png":::

Po wysłaniu żądania szczegóły odpowiedzi pojawią się w oknie postman poniżej żądania. Możesz wyświetlić kod stanu odpowiedzi i dowolny tekst treści.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Zrzut ekranu przedstawiający wysłane żądanie w programie Postman. Poniżej szczegółów żądania jest wyświetlana odpowiedź. Stan to 200 OK, a treść zawiera wyniki zapytania." lightbox="media/how-to-use-postman/postman-request-response.png":::

Możesz również porównać odpowiedź z oczekiwanymi danymi odpowiedzi podanymi w dokumentacji referencyjnej, aby zweryfikować wynik lub dowiedzieć się więcej o wszelkich błędach, które wystąpiły.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat interfejsów API Digital Twins, zapoznaj się z tematem [*How-to: Use the Azure Digital Twins APIs and SDKs ( How-to: Korzystanie*](how-to-use-apis-sdks.md)z interfejsów API i zestawów SDK języka Azure Digital Twins) lub zapoznaj się z dokumentacją referencyjną interfejsów [API REST.](/rest/api/azure-digitaltwins/)