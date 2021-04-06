---
title: Samouczek — makieta odpowiedzi interfejsu API w API Management-Azure Portal | Microsoft Docs
description: W tym samouczku użyjesz API Management, aby ustawić zasady w interfejsie API, tak aby zwracała replikę, jeśli zaplecze nie będzie dostępne do wysyłania rzeczywistych odpowiedzi.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 75727d139242e1b537505d2ed907ae20fc5479f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100547272"
---
# <a name="tutorial-mock-api-responses"></a>Samouczek: makieta odpowiedzi interfejsu API

Interfejsy API zaplecza można importować do interfejsu API API Management (APIM) lub tworzyć i zarządzać nimi ręcznie. Kroki opisane w tym samouczku pokazują, jak za pomocą programu APIM utworzyć pusty interfejs API i zarządzać nim ręcznie, a następnie ustawić zasady w interfejsie API, aby zwracała zastosowana odpowiedź. Ta metoda pozwala deweloperom na kontynuowanie implementowania i testowania wystąpienia usługi APIM nawet wtedy, gdy nie ma dostępnego zaplecza wysyłającego prawdziwe odpowiedzi. 

Możliwość makiety odpowiedzi może być przydatna w wielu scenariuszach:

+ Kiedy najpierw projektowana jest fasada interfejsu API, a implementacja zaplecza powstaje później. Lub kiedy zaplecze jest opracowywane równolegle.
+ Gdy zaplecze tymczasowo nie działa lub nie można go przeskalować.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie testowego interfejsu API 
> * Dodawanie operacji do testowego interfejsu API
> * Włączanie pozorowania odpowiedzi
> * Testowanie pozorowanego interfejsu API


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Makieta odpowiedzi interfejsu API":::

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Zapoznaj się z [koncepcją zasad w usłudze Azure API Management](api-management-howto-policies.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Tworzenie testowego interfejsu API 

Kroki opisane w tej sekcji pokazują, jak utworzyć pusty interfejs API bez zaplecza. 


1. Zaloguj się do Azure Portal i przejdź do wystąpienia API Management.
1. Wybierz **interfejsy API**  >  **+ Dodaj**  >  **pusty interfejs** API interfejsu API.
1. W oknie **Tworzenie pustego interfejsu API** wybierz pozycję **pełna**.
1. Wprowadź *testowy interfejs API* , aby **wyświetlić nazwę wyświetlaną**.
1. Wybierz pozycję **nieograniczone** dla **produktów**.
1. Upewnij **się, że wybrano** w **bramach**.
1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Tworzenie pustego interfejsu API":::

## <a name="add-an-operation-to-the-test-api"></a>Dodawanie operacji do testowego interfejsu API

Interfejs API uwidacznia jedną lub więcej operacji. W tej sekcji Dodaj operację do utworzonego pustego interfejsu API. Wywołanie operacji po wykonaniu kroków w tej sekcji powoduje błąd. Po wykonaniu kroków opisanych w dalszej części sekcji " [włączenie makiety odpowiedzi](#enable-response-mocking) " nie zostaną wyświetlone żadne błędy.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz interfejs API utworzony w poprzednim kroku.
1. Wybierz opcję **+ Dodaj operację**.
1. W oknie **frontonu** wprowadź następujące wartości.

     | Ustawienie             | Wartość                             | Opis                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nazwa wyświetlana**    | *Wywołanie testowe*                       | Nazwa wyświetlana w [portalu dla deweloperów](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **Adres URL** (czasownik HTTP) | GET                               | Wybierz jedno ze wstępnie zdefiniowanych czasowników HTTP.                                                                                                                                         |
    | **Adres URL**             | */Test*                           | Ścieżka adresu URL dla interfejsu API.                                                                                                                                                                       |
    | **Opis**     |                                   |  Opcjonalny opis operacji służący do udostępniania dokumentacji w portalu dla deweloperów dla deweloperów korzystających z tego interfejsu API.                                                    |
    
1. Wybierz kartę **odpowiedzi** znajdującą się w polach adres URL, nazwa wyświetlana i opis. Wprowadź ustawienia na tej karcie, aby zdefiniować kody stanu odpowiedzi, typy zawartości, przykłady i schematy.
1. Wybierz pozycję **+ Dodaj odpowiedź** i wybierz pozycję **200 OK** z listy.
1. Pod nagłówkiem **Reprezentacje** po prawej stronie wybierz pozycję **+ Dodaj reprezentację**.
1. Wprowadź ciąg *Application/JSON* w polu wyszukiwania i wybierz typ zawartości **Application/JSON** .
1. W polu tekstowym **Przykład** wprowadź tekst `{ "sampleField" : "test" }`.
1. Wybierz pozycję **Zapisz**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Dodaj operację interfejsu API" border="false":::

Chociaż nie jest to wymagane w tym przykładzie, można skonfigurować dodatkowe ustawienia operacji interfejsu API na innych kartach, w tym:


|Tab      |Opis  |
|---------|---------|
|**Zapytanie**     |  Dodaj parametry zapytania. Oprócz podawania nazwy i opisu można podać wartości, które są przypisane do parametru zapytania. Jedna z wartości może być oznaczona jako domyślna (opcjonalnie).        |
|**Żądanie**     |  Zdefiniuj typy zawartości żądania, przykłady i schematy.       |

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Aby dodać operację do testowego interfejsu API, uruchom polecenie [AZ APIM API Operation Create](/cli/azure/apim/api/operation#az_apim_api_operation_create) :

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Uruchom polecenie [AZ APIM API list](/cli/azure/apim/api/operation#az_apim_api_operation_list) , aby wyświetlić wszystkie operacje dotyczące interfejsu API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Aby usunąć operację, użyj polecenia [AZ APIM API Operation Delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) . Pobierz identyfikator operacji z poprzedniego polecenia.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Tę operację należy zachować w dalszej części tego artykułu.

---

## <a name="enable-response-mocking"></a>Włączanie pozorowania odpowiedzi

1. Wybierz interfejs API utworzony w [ramach tworzenia testowego interfejsu API](#create-a-test-api).
1. Wybierz dodaną operację testową.
1. W oknie po prawej stronie upewnij się, że jest zaznaczona karta **projektowanie** .
1. W oknie **Przetwarzanie przychodzące** wybierz pozycję **+ Dodaj zasady**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Dodawanie zasad przetwarzania" border="false":::

1. Wybierz pozycję **makieta odpowiedzi**  z galerii.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Kafelek zasad pozorowania odpowiedzi" border="false":::

1. W polu tekstowym **Odpowiedź usługi API Management** wpisz tekst **200 OK, application/json**. Taki wybór oznacza, że Twój interfejs API powinien zwrócić przykład odpowiedzi zdefiniowany w poprzedniej sekcji.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Ustaw odpowiedź na imitację":::

1. Wybierz pozycję **Zapisz**.

    > [!TIP]
    > Żółty pasek z **makietą tekstu jest włączony** dla interfejsu API wskazuje, że odpowiedzi zwrócone przez API Management są imitacj przez [zasady imitacji](api-management-advanced-policies.md#mock-response) i nie są tworzone przez zaplecze.

## <a name="test-the-mocked-api"></a>Testowanie pozorowanego interfejsu API

1. Wybierz interfejs API utworzony w [ramach tworzenia testowego interfejsu API](#create-a-test-api).
1. Wybierz kartę **Test**.
1. Upewnij się, że wybrany jest interfejs API **Wywołanie testowe**. Wybierz pozycję **Wyślij**, aby wykonać wywołanie testowe.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testowanie pozorowanego interfejsu API":::

1. W obszarze **Odpowiedź HTTP** zostanie wyświetlony kod JSON podany jako przykład w pierwszej sekcji tego samouczka.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Makieta odpowiedzi HTTP":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie testowego interfejsu API
> * Dodawanie operacji do testowego interfejsu API
> * Włączanie pozorowania odpowiedzi
> * Testowanie pozorowanego interfejsu API

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
