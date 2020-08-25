---
title: Pozorowanie odpowiedzi interfejsu API za pomocą witryny Azure Portal | Microsoft Docs
description: Ten samouczek pokazuje, jak za pomocą usługi API Management (APIM) ustawić zasady dla interfejsu API, tak aby zwracał on pozorowane odpowiedzi. Ta metoda pozwala deweloperom na kontynuowanie implementowania i testowania wystąpienia usługi API Management w sytuacji, gdy nie ma dostępnego zaplecza wysyłającego prawdziwe odpowiedzi.
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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 6841695cca5d3864e6823085520d8e9162e54043
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "70067939"
---
# <a name="mock-api-responses"></a>Pozorowanie odpowiedzi interfejsu API

Interfejsy API mogą zostać zaimportowane do interfejsu API usługi APIM lub utworzone i zarządzane ręcznie. Kroki opisane w tym samouczku pokazują, jak za pomocą usługi APIM utworzyć pusty interfejs API i zarządzać nim ręcznie. Samouczek pokazuje, jak ustawić zasady dla interfejsu API, aby zwracał pozorowane odpowiedzi. Ta metoda pozwala deweloperom na kontynuowanie implementowania i testowania wystąpienia usługi APIM nawet wtedy, gdy nie ma dostępnego zaplecza wysyłającego prawdziwe odpowiedzi. Możliwość pozorowania odpowiedzi może być przydatna w kilku scenariuszach:

+ Kiedy najpierw projektowana jest fasada interfejsu API, a implementacja zaplecza powstaje później. Lub kiedy zaplecze jest opracowywane równolegle.
+ Gdy zaplecze tymczasowo nie działa lub nie można go przeskalować.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie testowego interfejsu API 
> * Dodawanie operacji do testowego interfejsu API
> * Włączanie pozorowania odpowiedzi
> * Testowanie pozorowanego interfejsu API

![Pozorowana odpowiedź operacji](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Zapoznaj się z [koncepcją zasad w usłudze Azure API Management](api-management-howto-policies.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Tworzenie testowego interfejsu API 

Kroki opisane w tej sekcji pokazują, jak utworzyć pusty interfejs API bez zaplecza. W tej sekcji pokazano również, jak dodać operację do interfejsu API. Wywołanie operacji po wykonaniu kroków w tej sekcji powoduje błąd. Błędów nie będzie po ukończeniu kroków opisanych w sekcji „Włączanie pozorowania odpowiedzi”.

![Tworzenie pustego interfejsu API](./media/mock-api-responses/03-MockAPIResponses-01-CreateTestAPI.png)

1. Wybierz pozycję **Interfejsy API** w usłudze **API Management**.
2. Z menu po lewej stronie wybierz pozycję **+ Dodaj interfejs API**.
3. Z listy wybierz pozycję **Pusty interfejs API**.
4. W polu **Nazwa wyświetlana** wprowadź tekst „*Testowy interfejs API*”.
5. W polu **Produkty** wprowadź wartość „*Bez ograniczeń*”.
6. Wybierz przycisk **Utwórz**.

## <a name="add-an-operation-to-the-test-api"></a>Dodawanie operacji do testowego interfejsu API

![Dodawanie operacji do interfejsu API](./media/mock-api-responses/03-MockAPIResponses-02-AddOperation.png)

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Kliknij przycisk **+ Dodaj operację**.

    | Ustawienie             | Wartość                             | Opis                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nazwa wyświetlana**    | *Wywołanie testowe*                       | Nazwa, która jest wyświetlana w **portalu deweloperów**.                                                                                                                                       |
    | **Adres URL** (czasownik HTTP) | GET                               | Możesz wybrać jeden z wstępnie zdefiniowanych czasowników HTTP.                                                                                                                                         |
    | **Adres URL**             | */Test*                           | Ścieżka adresu URL dla interfejsu API.                                                                                                                                                                       |
    | **Opis**     |                                   | Podaj opis operacji, który będzie służyć do zapewnienia dokumentacji dla deweloperów używających tego interfejsu API w **portalu deweloperów**.                                                    |
    | Karta **Zapytanie**       |                                   | Pozwala dodać parametry zapytania. Oprócz podawania nazwy i opisu możesz podać wartości, które mogą być przypisane do tego parametru. Jedna z wartości może być oznaczona jako domyślna (opcjonalnie). |
    | Karta **Żądanie**     |                                   | Pozwala zdefiniować typy zawartości żądania, przykłady i schematy.                                                                                                                                  |
    | Karta **Odpowiedź**    | Zobacz kroki pod tą tabelą. | Zdefiniuj kody stanów odpowiedzi, typy zawartości, przykłady i schematy.                                                                                                                           |

3. Wybierz kartę **Odpowiedź** znajdującą się pod adresem URL, nazwą wyświetlaną i opisem pola.
4. Kliknij pozycję **+ Dodaj odpowiedź**.
5. Wybierz z listy pozycję **200 OK**.
6. Pod nagłówkiem **Reprezentacje** po prawej stronie wybierz pozycję **+ Dodaj reprezentację**.
7. Wprowadź tekst „*application/json*” w polu wyszukiwania i wybierz typ zawartości **application/json**.
8. W polu tekstowym **Przykład** wprowadź tekst `{ "sampleField" : "test" }`.
9. Wybierz przycisk **Utwórz**.

## <a name="enable-response-mocking"></a>Włączanie pozorowania odpowiedzi

![Włączanie pozorowania odpowiedzi](./media/mock-api-responses/03-MockAPIResponses-03-EnableMocking.png)

1. Wybierz interfejs API utworzony w kroku „Tworzenie testowego interfejsu API”.
2. Wybierz dodaną operację testową.
3. W oknie po prawej stronie kliknij kartę **Projekt**.
4. W oknie **Przychodzące przetwarzanie** kliknij pozycję **+ Dodaj zasady**.
5. W galerii wybierz kafelek **Pozorowanie odpowiedzi**.

    ![Kafelek zasad pozorowania odpowiedzi](./media/mock-api-responses/mock-responses-policy-tile.png)

6. W polu tekstowym **Odpowiedź usługi API Management** wpisz tekst **200 OK, application/json**. Taki wybór oznacza, że Twój interfejs API powinien zwrócić przykład odpowiedzi zdefiniowany w poprzedniej sekcji.

    ![Włączanie pozorowania odpowiedzi](./media/mock-api-responses/mock-api-responses-set-mocking.png)

7. Kliknij pozycję **Zapisz**.

## <a name="test-the-mocked-api"></a>Testowanie pozorowanego interfejsu API

![Testowanie pozorowanego interfejsu API](./media/mock-api-responses/03-MockAPIResponses-04-TestMocking.png)

1. Wybierz interfejs API utworzony w kroku „Tworzenie testowego interfejsu API”.
2. Otwórz kartę **Test**.
3. Upewnij się, że wybrany jest interfejs API **Wywołanie testowe**.

    > [!TIP]
    > Żółty pasek z tekstem **Mocking is enabled** (Pozorowanie jest włączone) wskazuje, że odpowiedzi zostały zwrócone z usługi API Management zgodnie z zasadami pozorowania, a nie z rzeczywistego zaplecza.

4. Wybierz pozycję **Wyślij**, aby wykonać wywołanie testowe.
5. W obszarze **Odpowiedź HTTP** zostanie wyświetlony kod JSON podany jako przykład w pierwszej sekcji tego samouczka.

    ![Włączanie pozorowania odpowiedzi](./media/mock-api-responses/mock-api-responses-test-response.png)

## <a name="video"></a>Wideo

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]

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
