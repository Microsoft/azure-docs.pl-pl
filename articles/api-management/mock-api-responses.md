---
title: Samouczek — pozoruj odpowiedzi interfejsu API w API Management — Azure Portal | Microsoft Docs
description: W tym samouczku użyjemy API Management do ustawienia zasad dla interfejsu API, aby zwracał on pozorowane odpowiedzi, jeśli zaplecza nie są dostępne do wysyłania rzeczywistych odpowiedzi.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 6214594f825908388762ac35cc8c8722ff9135f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874355"
---
# <a name="tutorial-mock-api-responses"></a>Samouczek: pozoruj odpowiedzi interfejsu API

Interfejsy API zaplecza można importować do interfejsu API API Management (APIM) lub tworzyć i zarządzać nimi ręcznie. Kroki opisane w tym samouczku pokazują, jak za pomocą usługi APIM utworzyć pusty interfejs API i zarządzać nim ręcznie, a następnie ustawić zasady dla interfejsu API, aby zwracał pozorowane odpowiedzi. Ta metoda pozwala deweloperom na kontynuowanie implementowania i testowania wystąpienia usługi APIM nawet wtedy, gdy nie ma dostępnego zaplecza wysyłającego prawdziwe odpowiedzi. 

Możliwość pozorowania odpowiedzi może być przydatna w wielu scenariuszach:

+ Kiedy najpierw projektowana jest fasada interfejsu API, a implementacja zaplecza powstaje później. Lub kiedy zaplecze jest opracowywane równolegle.
+ Gdy zaplecze tymczasowo nie działa lub nie można go przeskalować.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie testowego interfejsu API 
> * Dodawanie operacji do testowego interfejsu API
> * Włączanie pozorowania odpowiedzi
> * Testowanie pozorowanego interfejsu API


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Pozorowana odpowiedź interfejsu API":::

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Zapoznaj się z [koncepcją zasad w usłudze Azure API Management](api-management-howto-policies.md).
+ Wykonaj czynności z następującego przewodnika Szybki start: [tworzenie wystąpienia API Management Azure.](get-started-create-service-instance.md)

## <a name="create-a-test-api"></a>Tworzenie testowego interfejsu API 

Kroki opisane w tej sekcji pokazują, jak utworzyć pusty interfejs API bez zaplecza. 


1. Zaloguj się do Azure Portal i przejdź do swojego API Management wystąpienia.
1. Wybierz pozycję **Interfejsy API**  >  **+ Dodaj pusty interfejs**  >  **API.**
1. W **oknie Tworzenie pustego interfejsu API** wybierz pozycję **Pełny.**
1. Wprowadź *testowy interfejs API* w **polach Nazwa wyświetlana.**
1. Wybierz **pozycję Nieograniczone** dla opcji **Produkty.**
1. Upewnij **się, że w** opcjach Bramy wybrano opcję **Zarządzane.**
1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Tworzenie pustego interfejsu API":::

## <a name="add-an-operation-to-the-test-api"></a>Dodawanie operacji do testowego interfejsu API

Interfejs API uwidacznia jedną lub więcej operacji. W tej sekcji dodaj operację do utworzonego pustego interfejsu API. Wywołanie operacji po wykonaniu kroków w tej sekcji powoduje błąd. Po ukończeniu kroków w dalszej części sekcji Włączanie [pozorowania](#enable-response-mocking) odpowiedzi nie zostaną wystąpiły żadne błędy.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz interfejs API utworzony w poprzednim kroku.
1. Wybierz opcję **+ Dodaj operację**.
1. W **oknie Frontend** wprowadź następujące wartości.

     | Ustawienie             | Wartość                             | Opis                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nazwa wyświetlana**    | *Wywołanie testowe*                       | Nazwa wyświetlana w portalu [dla deweloperów](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **Adres URL** (czasownik HTTP) | GET                               | Wybierz jeden ze wstępnie zdefiniowanych zleceń HTTP.                                                                                                                                         |
    | **Adres URL**             | */test*                           | Ścieżka adresu URL dla interfejsu API.                                                                                                                                                                       |
    | **Opis**     |                                   |  Opcjonalny opis operacji używany do zapewnienia dokumentacji w portalu dla deweloperów deweloperom korzystającym z tego interfejsu API.                                                    |
    
1. Wybierz **kartę Odpowiedzi** znajdującą się w polach Adres URL, Nazwa wyświetlana i Opis. Wprowadź ustawienia na tej karcie, aby zdefiniować kody stanu odpowiedzi, typy zawartości, przykłady i schematy.
1. Wybierz **pozycję + Dodaj odpowiedź** i wybierz pozycję **200 OK** z listy.
1. Pod nagłówkiem **Reprezentacje** po prawej stronie wybierz pozycję **+ Dodaj reprezentację**.
1. Wprowadź *wartość application/json* w polu wyszukiwania i wybierz typ zawartości **application/json.**
1. W polu tekstowym **Przykład** wprowadź tekst `{ "sampleField" : "test" }`.
1. Wybierz pozycję **Zapisz**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Operacja dodawania interfejsu API" border="false":::

Chociaż nie jest to wymagane w tym przykładzie, dodatkowe ustawienia operacji interfejsu API można skonfigurować na innych kartach, w tym:


|Tab      |Opis  |
|---------|---------|
|**Zapytanie**     |  Dodaj parametry zapytania. Oprócz podania nazwy i opisu można podać wartości przypisane do parametru zapytania. Jedna z wartości może być oznaczona jako domyślna (opcjonalnie).        |
|**Żądanie**     |  Definiowanie typów zawartości żądań, przykładów i schematów.       |

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Aby dodać operację do testowego interfejsu API, uruchom [polecenie az apim api operation create:](/cli/azure/apim/api/operation#az_apim_api_operation_create)

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Uruchom polecenie [az apim api operation list,](/cli/azure/apim/api/operation#az_apim_api_operation_list) aby wyświetlić wszystkie operacje dla interfejsu API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Aby usunąć operację, użyj [polecenia az apim api operation delete.](/cli/azure/apim/api/operation#az_apim_api_operation_delete) Pobierz identyfikator operacji z poprzedniego polecenia.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Zachowaj tę operację do użycia w pozostałej części tego artykułu.

---

## <a name="enable-response-mocking"></a>Włączanie pozorowania odpowiedzi

1. Wybierz interfejs API utworzony w teście [Tworzenie testowego interfejsu API.](#create-a-test-api)
1. Wybierz dodaną operację testową.
1. W oknie po prawej stronie upewnij się, że **wybrano kartę** Projekt.
1. W **oknie Przychodzące przetwarzanie** wybierz pozycję **+ Dodaj zasady.**

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Dodawanie zasad przetwarzania" border="false":::

1. Wybierz **pozycję Pozoruj odpowiedzi**  z galerii.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Kafelek zasad pozorowania odpowiedzi" border="false":::

1. W polu tekstowym **Odpowiedź usługi API Management** wpisz tekst **200 OK, application/json**. Taki wybór oznacza, że Twój interfejs API powinien zwrócić przykład odpowiedzi zdefiniowany w poprzedniej sekcji.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Ustawianie odpowiedzi pozorowania":::

1. Wybierz pozycję **Zapisz**.

    > [!TIP]
    > Żółty pasek z  tekstem Pozorowanie jest włączony dla interfejsu API wskazuje, że odpowiedzi [](api-management-advanced-policies.md#mock-response) zwrócone z usługi API Management są pozorowane przez zasady pozorowania i nie są produkowane przez zaplecza.

## <a name="test-the-mocked-api"></a>Testowanie pozorowanego interfejsu API

1. Wybierz interfejs API utworzony w teście [Create a test API (Tworzenie testowego interfejsu API).](#create-a-test-api)
1. Wybierz kartę **Test**.
1. Upewnij się, że wybrany jest interfejs API **Wywołanie testowe**. Wybierz pozycję **Wyślij**, aby wykonać wywołanie testowe.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testowanie pozorowanego interfejsu API":::

1. W obszarze **Odpowiedź HTTP** zostanie wyświetlony kod JSON podany jako przykład w pierwszej sekcji tego samouczka.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Pozoruj odpowiedź HTTP":::

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
