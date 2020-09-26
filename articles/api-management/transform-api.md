---
title: Przekształcanie i ochrona interfejsu API za pomocą usługi Azure API Management | Microsoft Docs
description: Dowiedz się, jak chronić interfejs API za pomocą zasad przydziałów i dławienia (ograniczania liczby wywołań).
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 07efa1899ab7364615aab9d8b50437092274ae81
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371388"
---
# <a name="transform-and-protect-your-api"></a>Przekształcanie i ochrona interfejsu API

Samouczek przedstawia sposób przekształcania interfejsu API, aby nie ujawniał prywatnych informacji zaplecza. Na przykład możesz ukryć informacje dotyczące stosu technologicznego działającego w zapleczu. Możesz też ukryć oryginalne adresy URL, które są wyświetlane w treści odpowiedzi HTTP interfejsu API, a zamiast tego przekierowywać je do bramy APIM.

W tym samouczku przedstawiono również, jak łatwo można dodawać ochronę do interfejsu API zaplecza dzięki konfigurowaniu liczby wywołań i przydziałów za pomocą usługi Azure API Management. Na przykład możesz ograniczyć liczbę wywołań dla interfejsu API, aby nie był on nadmiernie obciążany przez deweloperów. Aby uzyskać więcej informacji, zobacz [Zasady zarządzania interfejsem API](api-management-policies.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> -   Przekształcanie interfejsu API w celu usuwania nagłówków odpowiedzi
> -   Zamiana oryginalnych adresów URL w treści odpowiedzi interfejsu API w adresy URL bramy APIM
> -   Ochrona interfejsu API poprzez dodanie zasad limitu szybkości (ograniczanie przepustowości)
> -   Testowanie przekształceń

![Zasady](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Wymagania wstępne

-   Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
-   Zapoznaj się z [koncepcją zasad w usłudze Azure API Management](api-management-howto-policies.md).
-   Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
-   Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Przekształcanie interfejsu API w celu usuwania nagłówków odpowiedzi

W tej sekcji przedstawiono sposób ukrywania nagłówków HTTP, które nie powinny być widoczne dla użytkowników. W tym przykładzie następujące nagłówki zostają usunięte w odpowiedzi HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testowanie oryginalnej odpowiedzi

Aby zobaczyć oryginalną odpowiedź:

1. W wystąpieniu usługi APIM wybierz pozycję **Interfejsy API** (w obszarze **API MANAGEMENT**).
2. Kliknij pozycję **Demo Conference API** (Pokazowy interfejs API konferencji) na liście interfejsów API.
3. Kliknij kartę **Test** w górnej części ekranu.
4. Wybierz operację **GetSpeakers**.
5. Naciśnij przycisk **Wyślij** u dołu ekranu.

Oryginalna odpowiedź powinna wyglądać następująco:

![Zasady](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Ustawianie zasad przekształcania

![Ustawianie zasad danych wychodzących](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. Wybierz **wersję demonstracyjną interfejsu API Conference**.
2. W górnej części ekranu wybierz kartę **Projektowanie**.
3. Wybierz opcję **Wszystkie operacje**.
4. W sekcji **Przetwarzanie wychodzące** kliknij **</>** ikonę.
5. Umieść kursor wewnątrz elementu ** &lt; wychodzącego &gt; ** .
6. W okienku po prawej stronie w obszarze **Zasady przekształcania** kliknij dwukrotnie opcję **+ Ustaw nagłówek HTTP** (aby wstawić dwa fragmenty kodu zasad).

   ![Zasady](./media/transform-api/transform-api.png)

7. Zmodyfikuj swój **\<outbound>** kod, aby wyglądać następująco:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   ![Zasady](./media/transform-api/set-policy.png)

8. Kliknij przycisk **Zapisz**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Zamiana oryginalnych adresów URL w treści odpowiedzi interfejsu API w adresy URL bramy APIM

W tej sekcji opisano, jak ukrywać oryginalne adresy URL, które są wyświetlane w treści odpowiedzi HTTP interfejsu API, a zamiast tego przekierowywać je do bramy APIM.

### <a name="test-the-original-response"></a>Testowanie oryginalnej odpowiedzi

Aby zobaczyć oryginalną odpowiedź:

1. Wybierz **wersję demonstracyjną interfejsu API Conference**.
2. Kliknij kartę **Test** w górnej części ekranu.
3. Wybierz operację **GetSpeakers**.
4. Naciśnij przycisk **Wyślij** u dołu ekranu.

    Jak widać, oryginalna odpowiedź wygląda następująco:

    ![Zasady](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Ustawianie zasad przekształcania

1.  Wybierz **wersję demonstracyjną interfejsu API Conference**.
2.  Wybierz opcję **Wszystkie operacje**.
3.  W górnej części ekranu wybierz kartę **Projektowanie**.
4.  W sekcji **Przetwarzanie wychodzące** kliknij **</>** ikonę.
5.  Umieść kursor wewnątrz elementu ** &lt; wychodzącego &gt; ** , a następnie kliknij przycisk **Pokaż fragmenty kodu** w prawym górnym rogu.
6.  W oknie po prawej stronie w obszarze **zasady przekształcania**kliknij pozycję **Maskuj adresy URL w obszarze zawartość**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Ochrona interfejsu API poprzez dodanie zasad limitu szybkości (ograniczanie przepustowości)

W tej sekcji przedstawiono sposób dodawania zabezpieczeń do interfejsu API zaplecza poprzez konfigurowanie limitów szybkości. Na przykład możesz ograniczyć liczbę wywołań dla interfejsu API, aby nie był on nadmiernie obciążany przez deweloperów. W tym przykładzie limit jest ustawiony na 3 wywołań na 15 sekund dla każdego identyfikatora subskrypcji. Po 15 sekundach deweloper może ponowić próbę wywołania interfejsu API.

![Ustawianie zasad danych przychodzących](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  Wybierz **wersję demonstracyjną interfejsu API Conference**.
2.  Wybierz opcję **Wszystkie operacje**.
3.  W górnej części ekranu wybierz kartę **Projektowanie**.
4.  W sekcji **Przetwarzanie danych przychodzących** kliknij ikonę **</>**.
5.  Umieść kursor wewnątrz elementu ** &lt; przychodzącego &gt; ** .
6.  W oknie po prawej stronie w obszarze **Zasady ograniczeń dostępu** kliknij opcję **+ Ogranicz liczbę wywołań na klucz**.
7.  Zmodyfikuj kod **Rate-limit-by-Key** (w **\<inbound\>** elemencie) do następującego kodu:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testowanie przekształceń

Na tym etapie zasady w edytorze kodu powinny wyglądać następująco:

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

1. Wybierz **wersję demonstracyjną interfejsu API Conference**.
2. Wybierz kartę **Test**.
3. Kliknij operację **GetSpeakers**.
4. Kliknij pozycję **Wyślij**.

    Jak widać, nagłówki zostały usunięte:

    ![Zasady](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Testowanie zamienionego adresu URL

1. Wybierz **wersję demonstracyjną interfejsu API Conference**.
2. Wybierz kartę **Test**.
3. Kliknij operację **GetSpeakers**.
4. Kliknij pozycję **Wyślij**.

    Jak widać, adres URL został zastąpiony.

    ![Zasady](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Testowanie limitu szybkości (ograniczania przepustowości)

1. Wybierz **wersję demonstracyjną interfejsu API Conference**.
2. Wybierz kartę **Test**.
3. Kliknij operację **GetSpeakers**.
4. Naciśnij przycisk **Wyślij** trzykrotnie.

    Po trzykrotnym wysłaniu żądania otrzymasz odpowiedź **429 Zbyt wiele żądań**.

5. Poczekaj około 15 sekund i naciśnij przycisk **Wyślij** ponownie. Teraz interfejs powinien zwrócić odpowiedź **200 OK**.

    ![Ograniczanie przepływności](./media/transform-api/test-throttling.png)

## <a name="video"></a>Wideo

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

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
