---
title: Wykonywanie żądań za pomocą narzędzia Postman
titleSuffix: Azure Digital Twins
description: Informacje o konfigurowaniu i używaniu programu Poster do testowania interfejsów API Digital bliźniaczych reprezentacji platformy Azure.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783816"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Jak wysyłać żądania do interfejsów API Digital bliźniaczych reprezentacji na platformie Azure przy użyciu programu Poster

[Poster](https://www.getpostman.com/) to narzędzie do testowania REST, które udostępnia kluczowe funkcje żądania HTTP w interfejsie GUI pulpitu i wtyczki. Można jej używać do wysyłania żądań HTTP i przesyłania ich do [interfejsów API REST usługi Azure Digital bliźniaczych reprezentacji](how-to-use-apis-sdks.md).

W tym artykule opisano sposób konfigurowania [klienta REST programu post](https://www.getpostman.com/) w celu współdziałania z interfejsami API Digital bliźniaczych reprezentacji platformy Azure, wykonując następujące czynności:

1. Użyj interfejsu wiersza polecenia platformy Azure, aby [**uzyskać token okaziciela**](#get-bearer-token) , który będzie używany do podejmowania żądań interfejsu API w programie Poster.
1. Skonfiguruj [**kolekcję ogłoszeń**](#about-postman-collections) i Skonfiguruj klienta REST programu post, aby używał tokenu okaziciela do uwierzytelniania. Podczas konfigurowania kolekcji można wybrać jedną z następujących opcji:
    1. [**Zaimportuj**](#import-collection-of-azure-digital-twins-apis) wstępnie zbudowaną kolekcję żądań interfejsu API Digital bliźniaczych reprezentacji platformy Azure.
    1. [**Utwórz**](#create-your-own-collection) własną kolekcję od podstaw.
1. [**Dodaj żądania**](#add-an-individual-request) do skonfigurowanej kolekcji i wyślij je do interfejsów API Digital bliźniaczych reprezentacji na platformie Azure.

Usługa Azure Digital bliźniaczych reprezentacji ma dwa zestawy interfejsów API, z którymi można korzystać: **płaszczyzny danych** i **płaszczyzny kontroli**. Aby uzyskać więcej informacji o różnicach między tymi zestawami interfejsów API, zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md). Ten artykuł zawiera informacje dotyczące obu zestawów interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby kontynuować korzystanie z programu Poster w celu uzyskania dostępu do interfejsów API Digital bliźniaczych reprezentacji platformy Azure, musisz skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji i pobrać program. Pozostała część tej sekcji przeprowadzi Cię przez te kroki.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Pobierz notkę

Następnie Pobierz wersję Desktop klienta programu Poster. Przejdź do [*www.getpostman.com/apps*](https://www.getpostman.com/apps) i postępuj zgodnie z monitami, aby pobrać aplikację.

## <a name="get-bearer-token"></a>Pobierz token okaziciela

Teraz, po skonfigurowaniu programu Poster i wystąpienia usługi Azure Digital bliźniaczych reprezentacji, należy uzyskać token okaziciela, który może być używany przez program do autoryzacji w przypadku interfejsów API Digital bliźniaczych reprezentacji platformy Azure.

Istnieje kilka możliwych sposobów uzyskania tego tokenu. W tym artykule jest używany [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) do logowania się do konta platformy Azure i uzyskiwania tokenu w taki sposób.

Jeśli masz [lokalnie zainstalowany](/cli/azure/install-azure-cli)interfejs wiersza polecenia platformy Azure, możesz uruchomić wiersz poleceń na komputerze, aby uruchomić następujące polecenia.
W przeciwnym razie możesz otworzyć okno [Azure Cloud Shell](https://shell.azure.com) w przeglądarce i uruchomić polecenia w tym miejscu.

1. Najpierw upewnij się, że logujesz się do platformy Azure przy użyciu odpowiednich poświadczeń, uruchamiając następujące polecenie:

    ```azurecli-interactive
    az login
    ```

2. Następnie użyj polecenia [AZ Account Get-Access-token](/cli/azure/account#az_account_get_access_token) , aby uzyskać token okaziciela z dostępem do usługi Azure Digital bliźniaczych reprezentacji. W tym poleceniu zostanie przekazany identyfikator zasobu dla punktu końcowego usługi Azure Digital bliźniaczych reprezentacji, aby uzyskać token dostępu, który może uzyskiwać dostęp do zasobów usługi Azure Digital bliźniaczych reprezentacji. 

    Wymagany kontekst dla tokenu zależy od używanego zestawu interfejsów API, dlatego należy użyć poniższych kart, aby wybrać między [płaszczyzną danych](how-to-use-apis-sdks.md#overview-data-plane-apis) i interfejsami API [płaszczyzny kontroli](how-to-use-apis-sdks.md#overview-control-plane-apis) .

    # <a name="data-plane"></a>[Płaszczyzna danych](#tab/data-plane)
    
    Aby uzyskać token do użycia z interfejsami API **płaszczyzny danych** , należy użyć następującej wartości statycznej kontekstu tokena: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Jest to identyfikator zasobu dla punktu końcowego usługi Digital bliźniaczych reprezentacji.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Płaszczyzna sterowania](#tab/control-plane)
    
    Aby uzyskać token do użycia z interfejsami API **płaszczyzny kontroli** , użyj następującej wartości kontekstu tokena: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. Skopiuj wartość `accessToken` w wyniku i Zapisz ją do użycia w następnej sekcji. Jest to **wartość tokenu** , którą podajesz do opublikowania żądania.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Zrzut ekranu konsoli zawierającej wynik polecenia AZ Account Get-Access-token. Pole accessToken i jego przykładowa wartość są wyróżnione.":::

>[!TIP]
>Token jest ważny przez co najmniej pięć minut i maksymalnie 60 minut. Jeśli upłynął limit czasu dla bieżącego tokenu, możesz powtórzyć kroki opisane w tej sekcji, aby uzyskać nowy.

Następnie skonfigurujesz program bliźniaczych reprezentacji do używania tego tokenu do żądania interfejsu API do usługi Azure Digital.

## <a name="about-postman-collections"></a>Informacje o kolekcjach ogłoszeń

Żądania w programie Poster są zapisywane w **kolekcjach** (grupy żądań). Podczas tworzenia kolekcji w celu grupowania żądań można zastosować typowe ustawienia do wielu żądań jednocześnie. Może to znacznie uprościć autoryzację, jeśli planujesz utworzyć więcej niż jedno żądanie w odniesieniu do interfejsów API Digital bliźniaczych reprezentacji na platformie Azure, ponieważ wystarczy skonfigurować te szczegóły tylko raz dla całej kolekcji.

Podczas pracy z usługą Azure Digital bliźniaczych reprezentacji można rozpocząć od zaimportowania [wstępnie skompilowanej kolekcji wszystkich żądań usługi Azure Digital bliźniaczych reprezentacji](#import-collection-of-azure-digital-twins-apis). Możesz to zrobić, jeśli szukasz interfejsów API i chcesz szybko skonfigurować projekt z przykładami żądania.

Alternatywnie można również zacząć od podstaw, [tworząc własną pustą kolekcję](#create-your-own-collection) i wypełniając ją indywidualnymi żądaniami, które wywołują tylko potrzebne interfejsy API. 

W poniższych sekcjach opisano oba te procesy. Pozostała część artykułu jest umieszczona w lokalnej aplikacji do publikowania, dlatego możesz teraz otworzyć aplikację Poster na swoim komputerze.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importowanie kolekcji interfejsów API Digital bliźniaczych reprezentacji platformy Azure

Szybkim sposobem na rozpoczęcie pracy z usługą Azure Digital bliźniaczych reprezentacji w programie Poster jest zaimportowanie wstępnie skompilowanej kolekcji dla interfejsów API Digital bliźniaczych reprezentacji platformy Azure.

### <a name="download-the-collection-file"></a>Pobieranie pliku kolekcji

Pierwszym krokiem importowania zestawu interfejsów API jest pobranie kolekcji. Wybierz poniższą kartę w celu wybrania płaszczyzny danych lub płaszczyzny kontroli, aby wyświetlić wstępnie skompilowane opcje kolekcji.

# <a name="data-plane"></a>[Płaszczyzna danych](#tab/data-plane)

Obecnie dostępne są dwie kolekcje bliźniaczych reprezentacjiych danych cyfrowych platformy Azure do wyboru:
* [**Kolekcja kolekcji Digital bliźniaczych reprezentacji na platformie Azure**](https://github.com/microsoft/azure-digital-twins-postman-samples): Ta kolekcja zawiera proste środowisko uruchamiania dla usługi Azure Digital bliźniaczych reprezentacji w programie Poster. Żądania obejmują przykładowe dane, dlatego można je uruchomić z minimalnymi potrzebami edycji. Wybierz tę kolekcję, jeśli chcesz, aby digestible zestaw żądań interfejsu API zawierający przykładowe informacje.
    - Aby znaleźć kolekcję, przejdź do linku repozytorium i Otwórz plik o nazwie *postman_collection.json*.
* **[Azure Digital bliźniaczych reprezentacji Data](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**— struktura Swagger: to repozytorium zawiera kompletny plik Swagger dla zestawu interfejsu API Digital bliźniaczych reprezentacji platformy Azure, który można pobrać i zaimportować do programu Poster jako kolekcja. Zapewni to kompleksowy zestaw wszystkich żądań interfejsu API, ale z pustymi treściami danych, a nie przykładowymi danymi. Wybierz tę kolekcję, jeśli chcesz mieć dostęp do każdego wywołania interfejsu API i Wypełnij wszystkie dane samodzielnie.
    - Aby znaleźć kolekcję, przejdź do linku repozytorium i wybierz folder dla najnowszej wersji specyfikacji. W tym miejscu Otwórz plik o nazwie *digitaltwins.json*.

# <a name="control-plane"></a>[Płaszczyzna sterowania](#tab/control-plane)

Kolekcja jest obecnie dostępna dla płaszczyzny kontroli to struktura [**Swagger programu Azure Digital bliźniaczych reprezentacji Control**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). To repozytorium zawiera kompletny plik Swagger dla zestawu interfejsu API Digital bliźniaczych reprezentacji platformy Azure, który można pobrać i zaimportować do programu Poster jako kolekcja. Zapewni to kompleksowy zestaw każdego żądania interfejsu API.

Aby znaleźć kolekcję, przejdź do linku repozytorium i wybierz folder dla najnowszej wersji specyfikacji. W tym miejscu Otwórz plik o nazwie *digitaltwins.json*.

---

Poniżej przedstawiono sposób pobierania wybranej kolekcji na maszynę, aby można było zaimportować ją do programu Poster.
1. Skorzystaj z powyższych linków, aby otworzyć plik kolekcji w usłudze GitHub w przeglądarce.
1. Wybierz przycisk **nieprzetworzony** , aby otworzyć nieprzetworzony tekst pliku.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Zrzut ekranu przedstawiający płaszczyznę danych digitaltwins.jsw pliku w serwisie GitHub. Istnieje wyróżnienie wokół pierwotnego przycisku." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Skopiuj tekst z okna i wklej go do nowego pliku na komputerze.
1. Zapisz plik z rozszerzeniem *JSON* (nazwa pliku może być dowolnego rodzaju, o ile można go zapamiętać, aby znaleźć plik później).

### <a name="import-the-collection"></a>Importowanie kolekcji

Następnie zaimportuj kolekcję do programu Poster.

1. W oknie głównym programu Poster wybierz przycisk **Importuj** .
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Zrzut ekranu nowo otwartego okna programu Poster. Przycisk &quot;Importuj&quot; jest wyróżniony." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. W poniższym oknie importowania wybierz pozycję **Przekaż pliki** i przejdź do pliku kolekcji na utworzonej wcześniej maszynie. Wybierz pozycję Otwórz.
1. Wybierz przycisk **Importuj** , aby potwierdzić.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Zrzut ekranu przedstawiający okno &quot;Import&quot; programu post, pokazujący plik do zaimportowania jako kolekcja i przycisk Importuj.":::

Nowo zaimportowana kolekcja może być teraz widoczna z głównego widoku programu Poster na karcie kolekcje.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Zrzut ekranu głównego okna programu Poster. Nowo zaimportowana kolekcja zostanie wyróżniona na karcie kolekcje." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Następnie przejdź do następnej sekcji, aby dodać token okaziciela do kolekcji w celu autoryzacji i nawiązać połączenie z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

### <a name="configure-authorization"></a>Konfigurowanie autoryzacji

Następnie edytuj utworzoną kolekcję, aby skonfigurować szczegóły dostępu. Zaznacz utworzoną kolekcję i wybierz ikonę **Wyświetl więcej akcji** , aby ściągnąć menu. Wybierz pozycję **Edytuj**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Zrzut ekranu przedstawiający wpis. Ikona &quot;Wyświetl więcej akcji&quot; dla zaimportowanej kolekcji zostanie wyróżniona, a w opcjach jest wyróżniona wartość &quot;Edytuj&quot;." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Wykonaj następujące kroki, aby dodać token okaziciela do kolekcji w celu autoryzacji. W tym miejscu zostanie użyta **wartość tokenu** zebraną w sekcji [pobieranie tokenu okaziciela](#get-bearer-token) , aby można było jej używać dla wszystkich żądań interfejsu API w kolekcji.

1. Upewnij się, że korzystasz z karty **autoryzacja** w oknie dialogowym edytowania kolekcji. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji zaimportowanej kolekcji w programie Poster z kartami &quot;Autoryzacja&quot;." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Ustaw typ **uwierzytelniania OAuth 2,0**, wklej token dostępu do pola token dostępu i wybierz pozycję **Zapisz**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edytowania zaimportowanej kolekcji na karcie &quot;Autoryzacja&quot;. Typ to &quot;OAuth 2,0&quot;, a pole tokenu dostępu jest wyróżnione." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Dodatkowa konfiguracja

# <a name="data-plane"></a>[Płaszczyzna danych](#tab/data-plane)

Jeśli tworzysz kolekcję [płaszczyzny danych](how-to-use-apis-sdks.md#overview-data-plane-apis) , Pomóż kolekcji łatwo połączyć się z zasobami usługi Azure Digital bliźniaczych reprezentacji, ustawiając pewne **zmienne** dostarczone z kolekcjami. Gdy wiele żądań w kolekcji wymaga tej samej wartości (takiej jak nazwa hosta wystąpienia usługi bliźniaczych reprezentacji Digital), można przechowywać wartość w zmiennej, która jest stosowana do każdego żądania w kolekcji. Obie kolekcje do pobrania dla usługi Azure Digital bliźniaczych reprezentacji są dołączone do wstępnie utworzonych zmiennych, które można ustawić na poziomie kolekcji.

1. W oknie dialogowym edytowania kolekcji przejdź do karty **zmienne** .

1. Użyj **nazwy hosta** wystąpienia z sekcji [*wymagania wstępne*](#prerequisites) , aby ustawić bieżące pole wartości dla odpowiedniej zmiennej. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji zaimportowanej kolekcji w programie Poster z kartami &quot;zmienne&quot;. Pole &quot;Bieżąca wartość&quot; jest wyróżnione." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Jeśli kolekcja zawiera dodatkowe zmienne, Wypełnij i Zapisz te wartości.

Po zakończeniu pracy z powyższymi krokami skończysz Konfigurowanie kolekcji. Jeśli chcesz, możesz zamknąć kartę Edycja dla kolekcji.

# <a name="control-plane"></a>[Płaszczyzna sterowania](#tab/control-plane)

Jeśli tworzysz kolekcję [płaszczyzny kontroli](how-to-use-apis-sdks.md#overview-control-plane-apis) , wszystko gotowe do skonfigurowania kolekcji. Jeśli chcesz, możesz zamknąć kartę Edycja dla kolekcji, a następnie przejdź do następnej sekcji.

--- 

### <a name="explore-requests"></a>Eksploruj żądania

Następnie przejrzyj żądania w kolekcji interfejsów API Digital bliźniaczych reprezentacji platformy Azure. Można rozwinąć kolekcję, aby wyświetlić wstępnie utworzone żądania (posortowane według kategorii operacji). 

Różne żądania wymagają różnych informacji o wystąpieniu i jego danych. Aby wyświetlić wszystkie informacje wymagane do uzyskania określonego żądania, zapoznaj się ze szczegółami żądania w [dokumentacji interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji](/rest/api/azure-digitaltwins/).

Szczegóły żądania można edytować w kolekcji programu Poster, wykonując następujące kroki:

1. Wybierz ją z listy, aby pobrać jej szczegóły do edycji. 

1. Wypełnij wartości dla zmiennych wymienionych na karcie **params** w obszarze **zmienne ścieżki**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Zrzut ekranu przedstawiający wpis. Kolekcja jest rozwinięta, aby pokazać żądanie. Sekcja &quot;zmienne ścieżki&quot; została wyróżniona w szczegółach żądania." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Podaj wymagane **nagłówki** lub szczegóły **treści** na odpowiednich kartach.

Po udostępnieniu wszystkich wymaganych szczegółów możesz uruchomić żądanie za pomocą przycisku **Wyślij** .

Możesz również dodać własne żądania do kolekcji, korzystając z procesu opisanego w sekcji [*Dodaj poszczególne żądania*](#add-an-individual-request) poniżej.

## <a name="create-your-own-collection"></a>Tworzenie własnej kolekcji

Zamiast importować istniejącą kolekcję wszystkich interfejsów API Digital bliźniaczych reprezentacji platformy Azure, możesz również utworzyć własną kolekcję od podstaw. Następnie można wypełnić je indywidualnymi żądaniami przy użyciu [dokumentacji interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Tworzenie kolekcji Postman

1. Aby utworzyć kolekcję, wybierz przycisk **Nowy** w głównym oknie programu Poster.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Zrzut ekranu głównego okna programu Poster. Przycisk &quot;nowy&quot; został wyróżniony." lightbox="media/how-to-use-postman/postman-new.png":::

    Wybierz typ **kolekcji**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe &quot;Tworzenie nowego&quot; w programie Poster. Opcja &quot;Kolekcja&quot; została wyróżniona.":::

1. Spowoduje to otwarcie karty w celu wypełnienia szczegółów nowej kolekcji. Wybierz ikonę edycji obok nazwy domyślnej kolekcji (**Nowa kolekcja**), aby zamienić ją na własny wybór nazwy. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji nowej kolekcji w programie Poster. Ikona edycji obok nazwy &quot;Nowa kolekcja&quot; jest wyróżniona." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Następnie przejdź do następnej sekcji, aby dodać token okaziciela do kolekcji w celu autoryzacji.

### <a name="configure-authorization"></a>Konfigurowanie autoryzacji

Wykonaj następujące kroki, aby dodać token okaziciela do kolekcji w celu autoryzacji. W tym miejscu zostanie użyta **wartość tokenu** zebraną w sekcji [pobieranie tokenu okaziciela](#get-bearer-token) , aby można było jej używać dla wszystkich żądań interfejsu API w kolekcji.

1. W oknie dialogowym edytowania nowej kolekcji przejdź do karty **autoryzacja** .

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edycji nowej kolekcji w programie Poster z kartami &quot;Autoryzacja&quot;." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Ustaw typ **uwierzytelniania OAuth 2,0**, wklej token dostępu do pola token dostępu i wybierz pozycję **Zapisz**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe edytowania dla nowej kolekcji na karcie &quot;Autoryzacja&quot;. Typ to &quot;OAuth 2,0&quot;, a pole tokenu dostępu jest wyróżnione." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Po zakończeniu pracy z powyższymi krokami skończysz Konfigurowanie kolekcji. Jeśli chcesz, możesz zamknąć kartę Edycja dla nowej kolekcji.

Nowa kolekcja może być widoczna z głównego widoku programu Poster na karcie kolekcje.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Zrzut ekranu głównego okna programu Poster. Nowo utworzona kolekcja niestandardowa zostanie wyróżniona na karcie kolekcje." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Dodaj pojedyncze żądanie

Teraz, gdy kolekcja została skonfigurowana, możesz dodać własne żądania do interfejsów API cyfrowych przędzy na platformie Azure.

1. Aby utworzyć żądanie, ponownie Użyj przycisku **Nowy** .

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Zrzut ekranu głównego okna programu Poster. Przycisk &quot;nowy&quot; został wyróżniony." lightbox="media/how-to-use-postman/postman-new.png":::

    Wybierz typ **żądania**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe &quot;Tworzenie nowego&quot; w programie Poster. Opcja &quot;Request&quot; została wyróżniona.":::

1. Ta akcja powoduje otwarcie okna Zapisz żądanie, w którym można wprowadzić nazwę żądania, nadać mu opcjonalny opis i wybrać kolekcję, do której należy. Wprowadź szczegóły i Zapisz żądanie w utworzonej wcześniej kolekcji.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Zrzut ekranu przedstawiający okno &quot;Zapisz żądanie&quot; w ogłoszeniu zawierającym opisane pola. Zostanie wyróżniony przycisk &quot;Zapisz na potrzeby kolekcji Digital bliźniaczych reprezentacjis&quot;.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Teraz możesz wyświetlić swoje żądanie w ramach kolekcji i wybrać je, aby uzyskać szczegółowe informacje, które można edytować.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Zrzut ekranu przedstawiający wpis. Kolekcja Digital bliźniaczych reprezentacji na platformie Azure jest rozwinięta, aby wyświetlić szczegóły żądania." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Ustaw szczegóły żądania

Aby wysłać żądanie post do jednego z interfejsów API Digital bliźniaczych reprezentacji platformy Azure, potrzebny będzie adres URL interfejsu API i informacje o wymaganych szczegółach. Te informacje można znaleźć w [dokumentacji referencyjnej interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji](/rest/api/azure-digitaltwins/).

Aby kontynuować przykładowe zapytanie, w tym artykule zostanie użyty interfejs API zapytań (wraz z [dokumentacją referencyjną](/rest/api/digital-twins/dataplane/query/querytwins)) do wykonywania zapytań dotyczących wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu.

1. Pobierz adres URL żądania i wpisz z dokumentacji referencyjnej. W przypadku interfejsu API zapytań jest to obecnie *wpis `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. W programie Poster Ustaw typ żądania i wprowadź adres URL żądania, wypełniając symbole zastępcze w adresie URL zgodnie z wymaganiami. W tym miejscu zostanie użyta **Nazwa hosta** wystąpienia z sekcji [*wymagania wstępne*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Zrzut ekranu przedstawiający szczegóły nowego żądania w programie Poster. Adres URL zapytania z dokumentacji referencyjnej został wypełniony w polu adres URL żądania." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Sprawdź, czy parametry podane w żądaniu na karcie **params** są zgodne z tymi opisanymi w dokumentacji referencyjnej. W przypadku tego żądania w programie Poster `api-version` parametr został automatycznie wypełniony, gdy adres URL żądania został wprowadzony w poprzednim kroku. W przypadku interfejsu API zapytania jest to jedyny wymagany parametr, więc ten krok jest wykonywany.
1. Na karcie **autoryzacja** Ustaw typ, aby **dziedziczyć uwierzytelnianie z elementu nadrzędnego**. Oznacza to, że to żądanie będzie używać autoryzacji skonfigurowanej wcześniej dla całej kolekcji.
1. Sprawdź, czy nagłówki pokazywane dla żądania na karcie **nagłówki** są zgodne z tymi opisanymi w dokumentacji referencyjnej. Dla tego żądania kilka nagłówków zostało wypełnionych automatycznie. W przypadku interfejsu API zapytań żadna z opcji nagłówka nie jest wymagana, więc ten krok jest wykonywany.
1. Sprawdź, czy treść pokazana dla żądania na karcie **treść** jest zgodna z wymaganiami opisanymi w dokumentacji referencyjnej. W przypadku interfejsu API zapytań do dostarczania tekstu zapytania jest wymagana treść JSON. Poniżej znajduje się Przykładowa treść tego żądania, która wykonuje zapytania dla wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Zrzut ekranu przedstawiający szczegóły nowego żądania w programie Poster na karcie treść. Zawiera on niesformatowaną treść JSON z zapytaniem &quot;SELECT * FROM DIGITALTWINS&quot;." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Aby uzyskać więcej informacji na temat tworzenia zapytań bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).

1. Zapoznaj się z dokumentacją referencyjną dla innych pól, które mogą być wymagane w przypadku danego typu żądania. W przypadku interfejsu API zapytań wszystkie wymagania zostały teraz spełnione w żądaniu post, więc ten krok jest wykonywany.
1. Użyj przycisku **Wyślij** , aby wysłać zakończone żądanie.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Zrzut ekranu przedstawiający wpis pokazujący szczegóły nowego żądania. Przycisk Wyślij jest wyróżniony." lightbox="media/how-to-use-postman/postman-request-send.png":::

Po wysłaniu żądania szczegóły odpowiedzi pojawią się w oknie Poster poniżej żądania. Można wyświetlić kod stanu odpowiedzi i treść dowolnego tekstu.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Zrzut ekranu przedstawiający wysłane żądanie w programie Poster. Poniżej szczegółów żądania jest wyświetlana odpowiedź. Stan to 200 OK, a treść zawiera wyniki zapytania." lightbox="media/how-to-use-postman/postman-request-response.png":::

Możesz również porównać odpowiedź z oczekiwanymi danymi odpowiedzi podanymi w dokumentacji referencyjnej, aby sprawdzić wynik lub dowiedzieć się więcej o ewentualnych błędach.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat cyfrowych interfejsów API bliźniaczych reprezentacji, Przeczytaj [*instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji platformy Azure*](how-to-use-apis-sdks.md)lub zapoznaj się z [dokumentacją referencyjną interfejsów API REST](/rest/api/azure-digitaltwins/).