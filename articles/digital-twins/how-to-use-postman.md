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
ms.openlocfilehash: fe79797aeef2b0c148aece0319d6b340b663b184
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763727"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Jak wysyłać żądania do interfejsów API Digital bliźniaczych reprezentacji na platformie Azure przy użyciu programu Poster

[Poster](https://www.getpostman.com/) to narzędzie do testowania REST, które udostępnia kluczowe funkcje żądania HTTP w interfejsie GUI pulpitu i wtyczki. Można jej używać do wysyłania żądań HTTP i przesyłania ich do [interfejsów API REST usługi Azure Digital bliźniaczych reprezentacji](how-to-use-apis-sdks.md).

W tym artykule opisano sposób konfigurowania [klienta REST programu post](https://www.getpostman.com/) w celu współdziałania z interfejsami API Digital bliźniaczych reprezentacji platformy Azure, wykonując następujące czynności:

1. Użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) , aby uzyskać token okaziciela, który będzie używany do podejmowania żądań interfejsu API w programie Poster.
1. Skonfiguruj kolekcję ogłoszeń i Skonfiguruj klienta REST programu post, aby używał tokenu okaziciela do uwierzytelniania.
1. Za pomocą skonfigurowanego wpisu można utworzyć i wysłać żądanie do interfejsów API Digital bliźniaczych reprezentacji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby kontynuować korzystanie z programu Poster w celu uzyskania dostępu do interfejsów API Digital bliźniaczych reprezentacji platformy Azure, musisz skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji i pobrać program. Pozostała część tej sekcji przeprowadzi Cię przez te kroki.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Pobierz notkę

Następnie Pobierz wersję Desktop klienta programu Poster. Przejdź do [*www.getpostman.com/apps*](https://www.getpostman.com/apps) i postępuj zgodnie z monitami, aby pobrać aplikację.

## <a name="get-bearer-token"></a>Pobierz token okaziciela

Teraz, po skonfigurowaniu programu Poster i wystąpienia usługi Azure Digital bliźniaczych reprezentacji, należy uzyskać token okaziciela, który może być używany przez program do autoryzacji w przypadku interfejsów API Digital bliźniaczych reprezentacji platformy Azure.

Istnieje kilka możliwych sposobów uzyskania tego tokenu. W tym artykule jest używany [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) do logowania się do konta platformy Azure i uzyskiwania tokenu w taki sposób.

Jeśli masz [lokalnie zainstalowany](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)interfejs wiersza polecenia platformy Azure, możesz uruchomić wiersz poleceń na komputerze, aby uruchomić następujące polecenia.
W przeciwnym razie możesz otworzyć okno [Azure Cloud Shell](https://shell.azure.com) w przeglądarce i uruchomić polecenia w tym miejscu.

1. Najpierw upewnij się, że logujesz się do platformy Azure przy użyciu odpowiednich poświadczeń, uruchamiając następujące polecenie:

    ```azurecli-interactive
    az login
    ```

1. Następnie użyj polecenia [AZ Account Get-Access-token](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token) , aby uzyskać token okaziciela z dostępem do usługi Azure Digital bliźniaczych reprezentacji.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Skopiuj wartość `accessToken` w wyniku i Zapisz ją do użycia w następnej sekcji. Jest to **wartość tokenu** , którą podajesz do opublikowania żądania.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Widok okna konsoli lokalnej przedstawiający wynik polecenia AZ Account Get-Access-token. Jedno z pól w wyniku ma nazwę accessToken i jego przykładową wartość — począwszy od EY--jest wyróżniony.":::

>[!TIP]
>Token jest ważny przez co najmniej pięć minut i maksymalnie 60 minut. Jeśli upłynął limit czasu dla bieżącego tokenu, możesz powtórzyć kroki opisane w tej sekcji, aby uzyskać nowy.

## <a name="set-up-postman-collection-and-authorization"></a>Konfigurowanie kolekcji i autoryzacji ogłaszania

Następnie skonfiguruj program do ogłaszania żądań interfejsu API.
Te kroki są wykonywane w lokalnej aplikacji do publikowania, dlatego należy otworzyć aplikację post na swoim komputerze.

### <a name="create-a-postman-collection"></a>Tworzenie kolekcji Postman

Żądania w programie Poster są zapisywane w **kolekcjach** (grupy żądań). Podczas tworzenia kolekcji w celu grupowania żądań można zastosować typowe ustawienia do wielu żądań jednocześnie. Może to znacznie uprościć autoryzację, jeśli planujesz utworzyć więcej niż jedno żądanie w odniesieniu do interfejsów API Digital bliźniaczych reprezentacji na platformie Azure, ponieważ tylko raz skonfigurujesz uwierzytelnianie dla całej kolekcji.

1. Aby utworzyć kolekcję, naciśnij przycisk *+ Nowa kolekcja* .

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Widok nowo otwartego okna programu Poster. Przycisk &quot;Nowa kolekcja&quot; został wyróżniony":::

1. W poniższym oknie *Utwórz nową kolekcję* Podaj **nazwę** i opcjonalny **Opis** kolekcji.

Następnie przejdź do następnej sekcji, aby dodać token okaziciela do kolekcji w celu autoryzacji.

### <a name="add-authorization-token-and-finish-collection"></a>Dodawanie tokenu autoryzacji i kończenie kolekcji

1. W oknie dialogowym *Tworzenie nowej kolekcji* przejdź do karty *autoryzacja* . Jest to miejsce, w którym zostanie umieszczona **wartość tokenu** zebraną w sekcji [pobieranie tokenu okaziciela](#get-bearer-token) , aby można było jej używać dla wszystkich żądań interfejsu API w kolekcji.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="Okno Ogłoś nowej kolekcji, w którym jest wyświetlana karta &quot;Autoryzacja&quot;.":::

1. Ustaw *Typ* _**uwierzytelniania OAuth 2,0**_ i wklej token dostępu do pola *token dostępu* .

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="Okno Ogłoś nowej kolekcji, w którym jest wyświetlana karta &quot;Autoryzacja&quot;. Wybrano typ &quot;OAuth 2,0&quot;, a pole tokenu dostępu, w którym można wkleić wartość tokenu dostępu, jest wyróżnione.":::

1. Po wklejeniu w tokenie okaziciela, kliknij przycisk *Utwórz* , aby zakończyć tworzenie kolekcji.

Nowa kolekcja może być teraz widoczna z głównego widoku Twojego wpisu w obszarze *kolekcje*.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Widok głównego okna programu Poster. Nowo utworzona kolekcja zostanie wyróżniona na karcie kolekcje.":::

## <a name="create-a-request"></a>Utwórz żądanie

Po wykonaniu powyższych kroków można utworzyć żądania do interfejsów API cyfrowych przędzy na platformie Azure.

1. Aby utworzyć żądanie, naciśnij przycisk *+ Nowy* .

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Widok głównego okna programu Poster. Przycisk &quot;nowy&quot; został wyróżniony":::

1. Wybierz pozycję *żądanie*.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Widok opcji, które można wybrać, aby utworzyć coś nowego. Opcja &quot;Request&quot; została wyróżniona":::

1. Ta akcja powoduje otwarcie okna *Zapisz żądanie* , w którym można wprowadzić nazwę żądania, nadać mu opcjonalny opis i wybrać kolekcję, do której należy. Wprowadź szczegóły i Zapisz żądanie w utworzonej wcześniej kolekcji.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Widok okna &quot;Zapisywanie żądania&quot;, w którym można wypełnić podane pola. Zostanie wyróżniony przycisk &quot;Zapisz na potrzeby kolekcji cyfrowych bliźniaczych reprezentacji Azure&quot;":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Teraz możesz wyświetlić swoje żądanie w ramach kolekcji i wybrać je, aby uzyskać szczegółowe informacje, które można edytować.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Widok głównego okna programu Poster. Kolekcja Digital bliźniaczych reprezentacji na platformie Azure jest rozwinięta, a żądanie &quot;Query bliźniaczych reprezentacji&quot; zostało wyróżnione. Szczegóły żądania są wyświetlane na środku strony." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Ustaw szczegóły żądania

Aby wysłać żądanie post do jednego z interfejsów API Digital bliźniaczych reprezentacji platformy Azure, potrzebny będzie adres URL interfejsu API i informacje o wymaganych szczegółach. Te informacje można znaleźć w [dokumentacji referencyjnej interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji](/rest/api/azure-digitaltwins/).

Aby kontynuować przykładowe zapytanie, w tym artykule zostanie użyty interfejs API zapytań (wraz z [dokumentacją referencyjną](/rest/api/digital-twins/dataplane/query/querytwins)) do wykonywania zapytań dotyczących wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu.

1. Pobierz adres URL żądania i wpisz z dokumentacji referencyjnej. W przypadku interfejsu API zapytań jest to obecnie *wpis `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. W programie Poster Ustaw typ żądania i wprowadź adres URL żądania, wypełniając symbole zastępcze w adresie URL zgodnie z wymaganiami. W tym miejscu zostanie użyta **Nazwa hosta** wystąpienia z sekcji [*wymagania wstępne*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Szczegóły nowego żądania, adres URL zapytania z dokumentacji referencyjnej, został wypełniony w polu adres URL żądania." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Sprawdź, czy parametry podane w żądaniu na karcie *params* są zgodne z tymi opisanymi w dokumentacji referencyjnej. W przypadku tego żądania w programie Poster `api-version` parametr został automatycznie wypełniony, gdy adres URL żądania został wprowadzony w poprzednim kroku. W przypadku interfejsu API zapytania jest to jedyny wymagany parametr, więc ten krok jest wykonywany.
1. Na karcie *autoryzacja* Ustaw *Typ* , aby *dziedziczyć uwierzytelnianie z elementu nadrzędnego*. Oznacza to, że to żądanie będzie używać uwierzytelniania skonfigurowanego wcześniej dla całej kolekcji.
1. Sprawdź, czy nagłówki pokazywane dla żądania na karcie *nagłówki* są zgodne z tymi opisanymi w dokumentacji referencyjnej. Dla tego żądania kilka nagłówków zostało wypełnionych automatycznie. W przypadku interfejsu API zapytań żadna z opcji nagłówka nie jest wymagana, więc ten krok jest wykonywany.
1. Sprawdź, czy treść pokazana dla żądania na karcie *treść* jest zgodna z wymaganiami opisanymi w dokumentacji referencyjnej. W przypadku interfejsu API zapytań do dostarczania tekstu zapytania jest wymagana treść JSON. Poniżej znajduje się Przykładowa treść tego żądania, która wykonuje zapytania dla wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Szczegóły nowego żądania są wyświetlane na karcie treść. Zawiera on niesformatowaną treść JSON z zapytaniem &quot;SELECT * FROM DIGITALTWINS&quot;."lightbox="media/how-to-use-postman/postman-request-body.png":::

   Aby uzyskać więcej informacji na temat tworzenia zapytań bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).

1. Zapoznaj się z dokumentacją referencyjną dla innych pól, które mogą być wymagane w przypadku danego typu żądania. W przypadku interfejsu API zapytań wszystkie wymagania zostały teraz spełnione w żądaniu post, więc ten krok jest wykonywany.
1. Użyj przycisku *Wyślij* , aby wysłać zakończone żądanie.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Obok szczegółów nowego żądania zostanie wyróżniony przycisk Wyślij." lightbox="media/how-to-use-postman/postman-request-send.png":::

Po wysłaniu żądania szczegóły odpowiedzi pojawią się w oknie Poster poniżej żądania. Można wyświetlić kod stanu odpowiedzi i treść dowolnego tekstu.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Poniżej szczegółów wysłanego żądania zostaną wyróżnione szczegóły odpowiedzi. Istnieje status 200 OK i treść tekstu opisującego cyfrowy bliźniaczych reprezentacji, który został zwrócony przez zapytanie." lightbox="media/how-to-use-postman/postman-request-response.png":::

Możesz również porównać odpowiedź z oczekiwanymi danymi odpowiedzi podanymi w dokumentacji referencyjnej, aby sprawdzić wynik lub dowiedzieć się więcej o ewentualnych błędach.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat cyfrowych interfejsów API bliźniaczych reprezentacji, Przeczytaj [*instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji platformy Azure*](how-to-use-apis-sdks.md)lub zapoznaj się z [dokumentacją referencyjną interfejsów API REST](/rest/api/azure-digitaltwins/).