---
title: Tworzenie funkcji integrującej się z usługą Azure Logic Apps
description: Utwórz funkcję zintegrowaną z Azure Logic Apps i Azure Cognitive Services. Wynikowy przepływ pracy kategoryzuje tonacji tweetów, które wysyłają powiadomienia e-mail.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388691"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Samouczek: tworzenie funkcji do integracji z Azure Logic Apps

Usługa Azure Functions integruje się z usługą Azure Logic Apps w Projektancie aplikacji usługi Logic Apps. Ta integracja umożliwia korzystanie z mocy obliczeniowej usługi Functions w aranżacjach z innymi usługami platformy Azure i innych firm.

W tym samouczku pokazano, jak utworzyć przepływ pracy w celu analizowania aktywności w serwisie Twitter. Podczas oceniania tweetów przepływ pracy wysyła powiadomienia po wykryciu pozytywnych tonacji.

Z tego samouczka dowiesz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Tworzenie zasobu interfejsu API usług Cognitive Services.
> * Tworzenie funkcji kategoryzującej tonację w tweetach.
> * Tworzenie aplikacji logiki łączącej się z usługą Twitter.
> * Dodawanie wykrywania tonacji do aplikacji logiki.
> * Łączenie aplikacji logiki z funkcją.
> * Wysyłanie wiadomości e-mail na podstawie odpowiedzi z funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto w usłudze [Twitter](https://twitter.com/).
* Konto usługi [Outlook.com](https://outlook.com/) (do wysyłania powiadomień).

> [!NOTE]
> Jeśli chcesz używać łącznika usługi Gmail, tylko konta biznesowe usługi G-Suite mogą używać tego łącznika bez ograniczeń w aplikacjach logiki. Jeśli masz konto użytkownika usługi Gmail, możesz użyć łącznika Usługi Gmail tylko z konkretnymi aplikacjami i usługami zatwierdzonymi przez Google lub utworzyć aplikację kliency Google do użycia na użytek uwierzytelniania w łączniku [usługi Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) <br><br>Aby uzyskać więcej informacji, zobacz [Zasady zabezpieczeń danych i ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Tworzenie analiza tekstu zasobów

Interfejsy API usług Cognitive Services są dostępne na platformie Azure jako pojedyncze zasoby. Użyj interfejsu API analiza tekstu api do wykrywania tonacji opublikowanych tweetów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.

1. W _obszarze Kategorie_ wybierz pozycję **AI + Machine Learning**

1. W _analiza tekstu_ wybierz pozycję **Utwórz.**

1. Wprowadź następujące wartości na ekranie _Tworzenie analiza tekstu_ aplikacji.

    | Ustawienie | Wartość | Uwagi |
    | ------- | ----- | ------- |
    | Subskrypcja | Nazwa subskrypcji platformy Azure | |
    | Grupa zasobów | Tworzenie nowej grupy zasobów o nazwie **tweet-sentiment-tutorial** | Później usuniesz tę grupę zasobów, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka. |
    | Region (Region) | Wybierz region znajdujący się najbliżej Ciebie | |
    | Nazwa | **TweetSentimentApp** | |
    | Warstwa cenowa | Wybierz **pozycję Bezpłatna F0** | |

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="get-text-analytics-settings"></a>Uzyskiwanie analiza tekstu ustawień

Po utworzeniu analiza tekstu skopiujesz kilka ustawień i odłożysz je do późniejszego użycia.

1. Wybierz **pozycję Klucze i punkt końcowy**.

1. Skopiuj **klucz 1,** klikając ikonę na końcu pola wejściowego.

1. Wklej wartość do edytora tekstów.

1. Skopiuj punkt **końcowy,** klikając ikonę na końcu pola wejściowego.

1. Wklej wartość w edytorze tekstów.

## <a name="create-the-function-app"></a>Tworzenie aplikacji funkcji

1. W górnym polu wyszukiwania wyszukaj i wybierz pozycję **Aplikacja funkcji.**

1. Wybierz przycisk **Utwórz**.

1. Wprowadź następujące wartości.

    | Ustawienie | Sugerowana wartość | Uwagi |
    | ------- | ----- | ------- |
    | Subskrypcja | Nazwa subskrypcji platformy Azure | |
    | Grupa zasobów | **tweet-sentiment-tutorial** | W tym samouczku użyj tej samej nazwy grupy zasobów. |
    | Nazwa aplikacji funkcji | **TweetSentimentAPI** + unikatowy sufiks | Nazwy aplikacji funkcji są globalnie unikatowe. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`. |
    | Publikowanie | **Kod** | |
    | Stos środowiska uruchomieniowego | **.NET** | Podany kod funkcji znajduje się w języku C#. |
    | Wersja | Wybierz numer najnowszej wersji | |
    | Region (Region) | Wybierz region znajdujący się najbliżej Ciebie | |

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

## <a name="create-an-http-triggered-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP  

1. W menu po lewej stronie okna _Funkcje_ wybierz pozycję **Funkcje**.

1. Wybierz **pozycję Dodaj** z górnego menu i wprowadź następujące wartości.

    | Ustawienie | Wartość | Uwagi |
    | ------- | ----- | ------- |
    | Środowisko deweloperskie | **Opracowywanie w portalu** | |
    | Template | **Wyzwalacz HTTP** | |
    | Nowa funkcja | **TweetSentimentFunction** | Jest to nazwa funkcji. |
    | Poziom autoryzacji | **Funkcja** | |

1. Wybierz przycisk **Add** (Dodaj).

1. Wybierz przycisk **Kod i test.**

1. Wklej następujący kod w oknie edytora kodu.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Wynik tonacji jest przekazywany do funkcji , która zwraca nazwę kategorii dla wartości.

1. Wybierz przycisk **Zapisz** na pasku narzędzi, aby zapisać zmiany.

    > [!NOTE]
    > Aby przetestować funkcję, wybierz **pozycję Testuj/Uruchom** z górnego menu. Na karcie _Dane_ wejściowe wprowadź wartość w polu `0.9` _wejściowym_ Treść, a następnie wybierz pozycję **Uruchom**. Sprawdź, czy w polu _zawartości_ odpowiedzi _HTTP_ w sekcji Dane wyjściowe jest zwracana wartość _Dodatnia._

Następnie utwórz aplikację logiki, która integruje się z interfejsem AZURE FUNCTIONS, Twitter i Cognitive Services API.

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. W górnym polu wyszukiwania wyszukaj i wybierz pozycję **Logic Apps**.

1. Wybierz pozycję **Dodaj**.

1. Wybierz **pozycję Zużycie** i wprowadź następujące wartości.

    | Ustawienie | Sugerowana wartość |
    | ------- | --------------- |
    | Subskrypcja | Nazwa subskrypcji platformy Azure |
    | Grupa zasobów | **tweet-sentiment-tutorial** |
    | Nazwa aplikacji logiki | **TweetSentimentApp** |
    | Region | Wybierz region najbliżej Ciebie, najlepiej ten sam region, który został wybrany w poprzednich krokach. |

    Zaakceptuj wartości domyślne dla wszystkich pozostałych ustawień.

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

1. Wybierz przycisk **Pusta aplikacja logiki.**

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Przycisk Pusta aplikacja logiki":::

1. Wybierz przycisk **Zapisz** na pasku narzędzi, aby zapisać postęp.

Teraz możesz użyć projektanta Logic Apps, aby dodać usługi i wyzwalacze do aplikacji.

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter

Utwórz połączenie z usługą Twitter, aby aplikacja umożliwiała sondowanie pod tematem nowych tweetów.

1. Wyszukaj tekst **Twitter** w górnym polu wyszukiwania.

1. Wybierz **ikonę Twitter.**

1. Wybierz wyzwalacz **Po wysłaniu nowego tweetu**.

1. Wprowadź następujące wartości, aby skonfigurować połączenie.

    | Ustawienie |  Wartość |
    | ------- | ---------------- |
    | Nazwa połączenia | **MyTwitterConnection** |
    | Typ uwierzytelniania | **Używanie domyślnej aplikacji udostępnionej** |

1. Wybierz pozycję **Zaloguj się**.

1. Postępuj zgodnie z monitami wyświetlanymi w oknie podręcznym, aby ukończyć logowanie do twittera.

1. Następnie wprowadź następujące wartości w polu _Po opublikowaniu nowego tweetu._

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Wyszukiwany tekst | **#my-twitter-tutorial** |
    | Jak chcesz sprawdzić elementy? | **15** w polu tekstowym i <br> **Minuta** na liście rozwijanej |

1. Wybierz przycisk **Zapisz** na pasku narzędzi, aby zapisać postęp.

Następnie połącz się z analizą tekstu, aby wykryć tonacji zebranych tweetów.

## <a name="add-text-analytics-sentiment-detection"></a>Dodawanie analiza tekstu wykrywania tonacji

1. Wybierz pozycję **Nowy krok**.

1. Wyszukaj **analiza tekstu** w polu wyszukiwania.

1. Wybierz **ikonę analiza tekstu** aplikacji.

1. Wybierz **pozycję Wykryj tonacji** i wprowadź następujące wartości.

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa połączenia | **TextAnalyticsConnection** |
    | Klucz konta | Wklej wcześniej analiza tekstu konta. |
    | Adres URL witryny | Wklej wcześniej analiza tekstu końcowego. |

1. Wybierz przycisk **Utwórz**.

1. Kliknij wewnątrz pola _Dodaj nowy parametr_ i  zaznacz pole wyboru obok dokumentów wyświetlanych w oknie podręcznym.

1. Kliknij wewnątrz pola _tekstowego documents Id -1_ (Identyfikator dokumentów — 1), aby otworzyć okno podręczne zawartości dynamicznej.

1. W polu _wyszukiwania zawartości_ dynamicznej wyszukaj **identyfikator** i kliknij pozycję **Identyfikator tweetu.**

1. Kliknij wewnątrz pola _tekstowego Text -1_ (Tekst — 1) dokumentów, aby otworzyć okno podręczne zawartości dynamicznej.

1. W polu _wyszukiwania zawartości_ dynamicznej wyszukaj tekst **,** a następnie kliknij pozycję **Tekst tweetu.**

1. W obszarze **Wybierz akcję** wpisz **Analiza tekstu**, a następnie kliknij akcję **Wykryj tonację**.

1. Wybierz przycisk **Zapisz** na pasku narzędzi, aby zapisać postęp.

Pole _Wykryj tonacji_ powinno wyglądać jak na poniższym zrzucie ekranu.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Ustawienia wykrywania tonacji":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Łączenie danych wyjściowych tonacji z punktem końcowym funkcji

1. Wybierz pozycję **Nowy krok**.

1. Wyszukaj **Azure Functions** w polu wyszukiwania.

1. Wybierz **ikonę Azure Functions** aplikacji.

1. Wyszukaj nazwę funkcji w polu wyszukiwania. Jeśli powyższe wskazówki są przestrzegane, nazwa funkcji zaczyna się od **TweetSentimentAPI.**

1. Wybierz ikonę funkcji.

1. Wybierz **element TweetSentimentFunction.**

1. Kliknij wewnątrz pola _Treść żądania_ i wybierz element _Wykryj_ ocenę **tonacji** w oknie podręcznym.

1. Wybierz przycisk **Zapisz** na pasku narzędzi, aby zapisać postęp.

## <a name="add-conditional-step"></a>Dodawanie kroku warunkowego

1. Wybierz przycisk **Dodaj akcję.**

1. Kliknij wewnątrz pola _Kontrolka,_ a następnie wyszukaj i wybierz pozycję **Kontrolka** w oknie podręcznym.

1. Wybierz **pozycję Warunek**.

1. Kliknij wewnątrz _pola Wybierz wartość_ i wybierz element Treść _tweetuEntimentFunction_  w oknie podręcznym.

1. Wprowadź **wartość Dodatnia** _w polu Wybierz_ wartość.

1. Wybierz przycisk **Zapisz** na pasku narzędzi, aby zapisać postęp.

## <a name="add-email-notifications"></a>Dodawanie powiadomień w wiadomościach e-mail

1. W polu _Prawda_ wybierz przycisk **Dodaj akcję.**

1. Wyszukaj i wybierz **pozycję Office 365 Outlook** w polu tekstowym.

1. Wyszukaj pozycję **Wyślij i** wybierz pozycję Wyślij **wiadomość e-mail** w polu tekstowym.

1. Wybierz przycisk **Zaloguj.**

1. Postępuj zgodnie z monitami wyświetlanymi w oknie podręcznym, aby zakończyć logowanie do usługi Office 365 Outlook.

1. Wprowadź swój adres e-mail w _polu_ Do.

1. Kliknij wewnątrz pola _Temat_ i kliknij element **Treść** w _obszarze TweetYEntimentFunction._ Jeśli element _Treść_ nie jest wyświetlany na liście, kliknij link **Zobacz więcej,** aby rozwinąć listę opcji.

1. Po pozycji _Treść_ w _temacie_ wprowadź tekst **Tweet z:**.

1. Po _tekście Tweet z:_ kliknij ponownie pole i wybierz pozycję **Nazwa użytkownika** z listy opcji _Po wpisie_ nowego tweetu.

1. Kliknij wewnątrz pola _Treść_ i wybierz pozycję **Tekst tweetu** na liście opcji _Po wpisie nowego_ tweetu. Jeśli pozycja _Tekst tweetu_ nie jest wyświetlana na liście, kliknij link **Zobacz** więcej, aby rozwinąć listę opcji.

1. Wybierz przycisk **Zapisz** na pasku narzędzi, aby zapisać postęp.

Pole e-mail powinno teraz wyglądać jak na tym zrzucie ekranu.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="Powiadomienie e-mail":::

## <a name="run-the-workflow"></a>Uruchamianie przepływu pracy

1. Ze swojego konta w serwisie Twitter wyślij tweet w następującym tekście: **I'm enjoying #my-twitter-tutorial**.

1. Wróć do projektanta Logic Apps i wybierz **przycisk** Uruchom.

1. Sprawdź w wiadomości e-mail, czy nie ma wiadomości z przepływu pracy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić wszystkie usługi i konta platformy Azure utworzone w ramach tego samouczka, usuń grupę zasobów.

1. Wyszukaj grupy **zasobów** w górnym polu wyszukiwania.

1. Wybierz **samouczek tweet-sentiment-tutorial**.

1. Wybierz **pozycję Usuń grupę zasobów**

1. Wprowadź **tekst tweet-sentiment-tutorial** w polu tekstowym.

1. Kliknij przycisk **Usuń**.

Opcjonalnie możesz wrócić do konta w serwisie Twitter i usunąć wszystkie tweety testowe ze źródła danych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bezserwerowego interfejsu API za pomocą usługi Azure Functions](functions-create-serverless-api.md)
