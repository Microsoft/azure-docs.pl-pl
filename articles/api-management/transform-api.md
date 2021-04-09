---
title: Samouczek — przekształcanie i ochrona interfejsu API na platformie Azure API Management | Microsoft Docs
description: W tym samouczku dowiesz się, jak chronić interfejs API w API Management z użyciem zasad przekształcania i ograniczania przepustowości (ograniczania szybkości).
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010235"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Samouczek: przekształcanie i ochrona interfejsu API

W tym samouczku pokazano, jak przekształcić interfejs API, aby nie ujawniał informacji o zapleczu prywatnym. Na przykład możesz chcieć ukryć informacje o stosie technologii uruchomionym w zapleczu. Możesz również ukryć oryginalne adresy URL, które są wyświetlane w treści odpowiedzi HTTP interfejsu API, a zamiast tego przekierować je do bramy APIM.

W tym samouczku pokazano również, jak łatwo dodać ochronę dla interfejsu API zaplecza, konfigurując Limit szybkości za pomocą usługi Azure API Management. Na przykład możesz chcieć ograniczyć szybkość wywołań interfejsu API, aby interfejs API nie był używany przez deweloperów. Aby uzyskać więcej informacji, zobacz [zasady API Management](api-management-policies.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> -   Przekształcanie interfejsu API w celu usuwania nagłówków odpowiedzi
> -   Zamiana oryginalnych adresów URL w treści odpowiedzi interfejsu API w adresy URL bramy APIM
> -   Ochrona interfejsu API przez dodanie zasad limitu szybkości (ograniczanie przepustowości)
> -   Testowanie przekształceń

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Zasady w portalu":::

## <a name="prerequisites"></a>Wymagania wstępne

-   Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
-   Zapoznaj się z [koncepcją zasad w usłudze Azure API Management](api-management-howto-policies.md).
-   Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
-   Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Przekształcanie interfejsu API w celu usuwania nagłówków odpowiedzi

W tej sekcji pokazano, jak ukryć nagłówki HTTP, które nie mają być wyświetlane użytkownikom. Ten przykład pokazuje, jak usunąć następujące nagłówki w odpowiedzi HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testowanie oryginalnej odpowiedzi

Aby zobaczyć oryginalną odpowiedź:

1. W wystąpieniu usługi API Management wybierz pozycję **interfejsy API**.
1. Wybierz pozycję **pokazowy interfejs API konferencji** z listy interfejsów API.
1. Wybierz kartę **test** w górnej części ekranu.
1. Wybierz operację **getgłośników** i wybierz pozycję **Wyślij**.

Oryginalna odpowiedź powinna wyglądać podobnie do następującej:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Oryginalna odpowiedź interfejsu API":::

Jak widać, odpowiedź obejmuje nagłówki **x-ASPNET-Version** i **X-d-by** .

### <a name="set-the-transformation-policy"></a>Ustawianie zasad przekształcania

1. Wybierz pozycję **demonstracyjny interfejs API**  >  **projektowania**  >  **wszystkie operacje**.
4. W sekcji **Przetwarzanie wychodzące** wybierz ikonę Edytor kodu ( **</>** ).

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Przejdź do zasad wychodzących" border="false":::

1. Umieść kursor wewnątrz elementu **&lt; wychodzącego &gt;** i wybierz pozycję **Pokaż fragmenty kodu** w prawym górnym rogu.
1. W oknie po prawej stronie w obszarze **zasady przekształcania** wybierz dwa razy pozycję **Ustaw nagłówek HTTP** (aby wstawić dwa fragmenty kodu zasad).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Ustawianie zasad nagłówka HTTP":::

1. Zmodyfikuj swój **\<outbound>** kod, aby wyglądać następująco:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Ustaw nagłówek HTTP":::

1. Wybierz pozycję **Zapisz**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Zamiana oryginalnych adresów URL w treści odpowiedzi interfejsu API w adresy URL bramy APIM

W tej sekcji przedstawiono sposób ukrywania oryginalnych adresów URL, które są wyświetlane w treści odpowiedzi HTTP interfejsu API, a zamiast tego przekierowywać je do bramy APIM.

### <a name="test-the-original-response"></a>Testowanie oryginalnej odpowiedzi

Aby zobaczyć oryginalną odpowiedź:

1. Wybierz **test interfejsu API konferencji demonstracyjnej**  >  .
1. Wybierz operację **getgłośników** i wybierz pozycję **Wyślij**.

    Jak widać, odpowiedź zawiera oryginalne adresy URL zaplecza:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Oryginalne adresy URL w odpowiedzi":::


### <a name="set-the-transformation-policy"></a>Ustawianie zasad przekształcania

1.  Wybierz pozycję **Demonstracja interfejs API konferencji**  >  **wszystkie operacje**  >  **projektowe**.
1.  W sekcji **Przetwarzanie wychodzące** wybierz ikonę Edytor kodu ( **</>** ).
1.  Umieść kursor wewnątrz elementu **&lt; wychodzącego &gt;** i wybierz pozycję **Pokaż fragmenty kodu** w prawym górnym rogu.
1.  W oknie po prawej stronie w obszarze **zasady przekształcania** wybierz pozycję **maska adresów URL w obszarze zawartość**. 
1.  Wybierz pozycję **Zapisz**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Ochrona interfejsu API poprzez dodanie zasad limitu szybkości (ograniczanie przepustowości)

W tej sekcji przedstawiono sposób dodawania zabezpieczeń do interfejsu API zaplecza poprzez konfigurowanie limitów szybkości. Można na przykład ograniczyć szybkość wywołań interfejsu API, tak aby interfejs API nie był używany przez deweloperów. W tym przykładzie limit jest ustawiony na 3 wywołań na 15 sekund dla każdego identyfikatora subskrypcji. Po 15 sekundach deweloper może ponowić próbę wywołania interfejsu API.

1.  Wybierz pozycję **Demonstracja interfejs API konferencji**  >  **wszystkie operacje**  >  **projektowe**.
1.  W sekcji **Przetwarzanie przychodzące** wybierz ikonę Edytor kodu ( **</>** ).
1.  Umieść kursor wewnątrz elementu **&lt; przychodzącego &gt;** i wybierz pozycję **Pokaż fragmenty kodu** w prawym górnym rogu.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Ustawianie zasad danych przychodzących" border="false":::

1.  W oknie po prawej stronie w obszarze **zasady ograniczeń dostępu** wybierz pozycję **+ Ogranicz częstotliwość wywołań na klucz**.
1.  Zmodyfikuj kod **Rate-limit-by-Key** (w **\<inbound\>** elemencie) do następującego kodu:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testowanie przekształceń

W tym momencie, Jeśli zobaczysz kod w edytorze kodu, Twoje zasady wyglądają następująco:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

Pozostała część tej sekcji testuje przekształcenia zasad ustawione w tym artykule.

### <a name="test-the-stripped-response-headers"></a>Testowanie usuniętych nagłówków odpowiedzi

1. Wybierz **test interfejsu API konferencji demonstracyjnej**  >  .
1. Wybierz operację **getgłośników** i wybierz pozycję **Wyślij**.

    Jak widać, nagłówki zostały usunięte:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Usunięte nagłówki odpowiedzi":::

### <a name="test-the-replaced-url"></a>Testowanie zamienionego adresu URL

1. Wybierz **test interfejsu API konferencji demonstracyjnej**  >  .
1. Wybierz operację **getgłośników** i wybierz pozycję **Wyślij**.

    Jak widać, adres URL został zastąpiony.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Zastąpiony adres URL":::

### <a name="test-the-rate-limit-throttling"></a>Testowanie limitu szybkości (ograniczania przepustowości)

1. Wybierz **test interfejsu API konferencji demonstracyjnej**  >  .
1. Wybierz operację **GetSpeakers**. Wybierz pozycję **Wyślij** trzy razy w wierszu.

    Po wysłaniu żądania trzy razy otrzymujesz **429 zbyt wiele** odpowiedzi na żądania.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Zbyt wiele żądań":::

1. Zaczekaj 15 sekund lub, a następnie wybierz pozycję **Wyślij** ponownie. Teraz interfejs powinien zwrócić odpowiedź **200 OK**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> -   Przekształcanie interfejsu API w celu usuwania nagłówków odpowiedzi
> -   Zamiana oryginalnych adresów URL w treści odpowiedzi interfejsu API w adresy URL bramy APIM
> -   Ochrona interfejsu API poprzez dodanie zasad limitu szybkości (ograniczanie przepustowości)
> -   Testowanie przekształceń

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Monitorowanie interfejsu API](api-management-howto-use-azure-monitor.md)
