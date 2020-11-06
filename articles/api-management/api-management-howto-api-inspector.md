---
title: Samouczek — debugowanie interfejsów API na platformie Azure API Management przy użyciu śledzenia żądań
description: Wykonaj kroki tego samouczka, aby włączyć śledzenie i inspekcję kroków przetwarzania żądań w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: e9a101de408b506fb5375b5f16c1deff4f67532d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422005"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Samouczek: debugowanie interfejsów API za pomocą funkcji śledzenia żądań

W tym samouczku opisano sposób przeprowadzania inspekcji (Trace) przetwarzania żądań w usłudze Azure API Management, aby ułatwić debugowanie i rozwiązywanie problemów z interfejsem API. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie przykładowego wywołania
> * Przegląd kroków przetwarzania żądań

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="Inspektor interfejsu API":::

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Wykonaj następujące kroki: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Weryfikuj ustawienie ustawienia śledzenia 

Ustawienie **Zezwalaj na śledzenie** dla subskrypcji używanej przez interfejs API musi być włączone. Jeśli używasz wbudowanej subskrypcji All-Access, jest ona domyślnie włączona. Aby sprawdzić w portalu, przejdź do wystąpienia API Management i wybierz pozycję **subskrypcje**.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Zezwalaj na śledzenie subskrypcji":::

## <a name="trace-a-call"></a>Śledzenie wywołania

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do wystąpienia API Management.
1. Wybierz pozycję **Interfejsy API**.
1. Wybierz pozycję  **pokazowy interfejs API konferencji** z listy interfejsów API.
1. Wybierz kartę **Test**.
1. Wybierz operację **GetSpeakers**.
1. Upewnij się, że w nagłówku żądania HTTP znajduje się **wartość OCP-admin-Trace: true** i prawidłowa wartość dla parametru **OCP-admin-Subscription-Key**. Jeśli tak nie jest, wybierz pozycję **+ Dodaj nagłówek** , aby dodać nagłówek.
1. Wybierz pozycję **Wyślij** , aby wykonać wywołanie interfejsu API.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Konfigurowanie śledzenia interfejsu API":::

> [!TIP]
> Jeśli wartość **OCP-APIM-Subscription-Key** nie zostanie automatycznie wypełniona w żądaniu HTTP, możesz ją pobrać w portalu. Wybierz pozycję **subskrypcje** i otwórz menu kontekstowe ( **...** ) dla subskrypcji. Wybierz pozycję **Pokaż/Ukryj klucze**. W razie konieczności można także ponownie wygenerować klucze. Następnie Dodaj klucz do nagłówka.

## <a name="review-trace-information"></a>Przejrzyj informacje o śledzeniu

1. Po zakończeniu wywołania przejdź do karty **śledzenie** w **odpowiedzi HTTP**.
1. Wybierz dowolne z poniższych linków, aby przejść do szczegółów szczegółowych informacji śledzenia: **przychodzące** , **zaplecze** , **wychodzące**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Przegląd śledzenia odpowiedzi":::

    * **Ruch przychodzący** — wyświetla oryginalne żądanie API Management otrzymane od wywołującego i zasady zastosowane do żądania. Na przykład jeśli dodano zasady w [samouczku: Przekształć i Chroń swój interfejs API](transform-api.md), zostaną one wyświetlone w tym miejscu.

    * **Zaplecze** — pokazuje żądania API Management wysyłane do zaplecza interfejsu API i odebrane odpowiedzi.

    * **Wychodzące** — pokazuje zasady zastosowane do odpowiedzi przed wysłaniem ich do obiektu wywołującego.

    > [!TIP]
    > Przy każdym kroku widać również czas, jaki upłynął od odebrania żądania przez usługę API Management.

1. Na karcie **komunikat** nagłówek **OCP-APIM-Trace-Location** pokazuje lokalizację danych śledzenia przechowywanych w usłudze Azure Blob Storage. W razie potrzeby przejdź do tej lokalizacji, aby pobrać śledzenie.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Lokalizacja śledzenia w usłudze Azure Storage":::
## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Śledzenie przykładowego wywołania
> * Przegląd kroków przetwarzania żądań

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Korzystanie z poprawek](api-management-get-started-revise-api.md)
