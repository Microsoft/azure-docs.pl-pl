---
title: Tworzenie funkcji integrującej się z usługą Azure Logic Apps
description: Utwórz funkcję integrującą się z usługą Azure Logic Apps i usługami Azure Cognitive Services, aby kategoryzować tonację w tweetach i wysyłać powiadomienia, gdy poziom nastrojów (tonacji) będzie niski.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: feb6b36f8e5e7bbec83d8882552484f68abfd56d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537756"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Tworzenie funkcji integrującej się z usługą Azure Logic Apps

Usługa Azure Functions integruje się z usługą Azure Logic Apps w Projektancie aplikacji usługi Logic Apps. Ta integracja umożliwia używanie mocy obliczeniowej usługi Functions w aranżacjach z innymi usługami platformy Azure oraz innych dostawców. 

Ten samouczek pokazuje, w jaki sposób używać Azure Functions z Logic Apps i Cognitive Services na platformie Azure do uruchamiania analizy tonacji z wpisów w serwisie Twitter. Funkcja wyzwalacza HTTP klasyfikuje tweety jako zielone, żółte lub czerwone na podstawie wyniku tonacji. W razie wykrycia niskiego poziomu tonacji wysyłana jest wiadomość e-mail. 

![ilustracja: pierwsze dwa kroki aplikacji w Projektancie aplikacji usługi Logic Apps](media/functions-twitter-email/00-logic-app-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu interfejsu API usług Cognitive Services.
> * Tworzenie funkcji kategoryzującej tonację w tweetach.
> * Tworzenie aplikacji logiki łączącej się z usługą Twitter.
> * Dodawanie wykrywania tonacji do aplikacji logiki. 
> * Łączenie aplikacji logiki z funkcją.
> * Wysyłanie wiadomości e-mail na podstawie odpowiedzi z funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ Aktywne konto w usłudze [Twitter](https://twitter.com/). 
+ Konto usługi [Outlook.com](https://outlook.com/) (do wysyłania powiadomień).

> [!NOTE]
> Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć łącznika usługi Gmail z tylko określonymi aplikacjami i usługami zatwierdzonymi przez firmę Google. Możesz też [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania w łączniku usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

+ Do wykonania czynności przedstawionych w tym artykule są wymagane zasoby utworzone w temacie [Tworzenie pierwszej funkcji w witrynie Azure Portal](functions-create-first-azure-function.md).
Jeśli jeszcze tego nie zrobiono, wykonaj teraz te kroki, aby utworzyć aplikację funkcji.

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

Interfejsy API usług Cognitive Services są dostępne na platformie Azure jako pojedyncze zasoby. Użyj interfejsu API analizy tekstu do wykrywania tonacji monitorowanych tweetów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Kliknij pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.

3. Kliknij pozycję **AI + Machine Learning**  >  **Analiza tekstu**. Następnie użyj ustawień określonych w tabeli, aby utworzyć zasób.

    ![Tworzenie strony zasobu usług Cognitive](media/functions-twitter-email/01-create-text-analytics.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | --- |
    | **Nazwa** | MyCognitiveServicesAccnt | Wybierz unikatową nazwę konta. |
    | **Lokalizacja** | Zachodnie stany USA | Użyj najbliższej lokalizacji. |
    | **Warstwa cenowa** | F0 | Rozpocznij od najniższej warstwy. Gdy wyczerpią się wywołania, przeskaluj do wyższego poziomu.|
    | **Grupa zasobów** | myResourceGroup | Użyj tej samej grupy zasobów dla wszystkich usług w tym samouczku.|

4. Kliknij przycisk **Utwórz**, aby utworzyć zasób. 

5. Kliknij pozycję **Przegląd** i skopiuj wartość **punktu końcowego** do edytora tekstu. Ta wartość jest używana podczas tworzenia połączenia z interfejsem API usług Cognitive Services.

    ![Ustawienia usług Cognitive Services](media/functions-twitter-email/02-cognitive-services.png)

6. W kolumnie nawigacji po lewej stronie kliknij pozycję **Klucze**, a następnie skopiuj wartość pozycji **Klucz 1** i zapisz ją w edytorze tekstów. Ten klucz umożliwia łączenie aplikacji logiki z interfejsem API usług Cognitive Services. 
 
    ![Klucze usług Cognitive Services](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Tworzenie aplikacji funkcji

Azure Functions zapewnia doskonały sposób odciążania zadań przetwarzania w przepływie pracy aplikacji logiki. Ten samouczek używa funkcji wyzwalacza HTTP do przetwarzania wyników tonacji tweetów z Cognitive Services i zwracania wartości kategorii.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-trigger-function"></a>Tworzenie funkcji wyzwalacza HTTP  

1. W menu po lewej stronie okna **funkcje** wybierz pozycję **funkcje**, a następnie wybierz pozycję **Dodaj** z górnego menu.

2. W oknie **Nowa funkcja** wybierz pozycję **wyzwalacz http**.

    ![Wybierz funkcję wyzwalacza HTTP](./media/functions-twitter-email/06-function-http-trigger.png)

3. Na stronie **Nowa funkcja** wybierz pozycję **Utwórz funkcję**.

4. W nowej funkcji wyzwalacza HTTP wybierz pozycję **Kod + test** z menu po lewej stronie, Zastąp zawartość `run.csx` pliku następującym kodem, a następnie wybierz pozycję **Zapisz**:

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
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```

    Ten kod funkcji zwraca kategorię koloru na podstawie wyniku tonacji otrzymanego w żądaniu. 

5. Aby przetestować funkcję, wybierz pozycję **Testuj** z górnego menu. Na karcie **dane wejściowe** wprowadź wartość `0.2` w polu **treść**, a następnie wybierz pozycję **Uruchom**. Wartość **czerwona** jest zwracana w **treści odpowiedzi HTTP** na karcie **dane wyjściowe** . 

    :::image type="content" source="./media/functions-twitter-email/07-function-test.png" alt-text="Definiowanie ustawień serwera proxy":::

Została utworzona funkcja kategoryzująca wyniki tonacji. Następnie należy utworzyć aplikację logiki, która zintegruje funkcję z usługą Twitter i interfejsem API usług Cognitive Services. 

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki   

1. W Azure Portal kliknij przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu Azure Portal.

2. Kliknij **Web**pozycję  >  **aplikacja logiki**sieci Web.
 
3. Następnie wpisz wartość **Nazwa**, taką jak `TweetSentiment`, i użyj ustawień określonych w tabeli.

    ![Tworzenie aplikacji logiki w witrynie Azure Portal](./media/functions-twitter-email/08-logic-app-create.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | ----------------- | ------------ | ------------- |
    | **Nazwa** | TweetSentiment | Wybierz odpowiednią nazwę dla aplikacji. |
    | **Grupa zasobów** | myResourceGroup | Wybierz tę samą istniejącą grupę zasobów co wcześniej. |
    | **Lokalizacja** | East US | Wybierz bliską lokalizację. |    

4. Po wprowadzeniu odpowiednich wartości ustawień kliknij pozycję **Utwórz** w celu utworzenia aplikacji logiki. 

5. Po utworzeniu aplikacji kliknij nową aplikację logiki przypiętą do pulpitu nawigacyjnego. Następnie w Projektancie aplikacji usługi Logic Apps przewiń w dół i kliknij szablon **Pusta aplikacja logiki**. 

    ![Szablon pustej aplikacji usługi Logic Apps](media/functions-twitter-email/09-logic-app-create-blank.png)

Teraz za pomocą Projektanta aplikacji usługi Logic Apps możesz dodać do aplikacji usługi i wyzwalacze.

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter

Najpierw utwórz połączenie z kontem w usłudze Twitter. Aplikacja logiki sonduje pod kątem tweetów, które wyzwalają uruchomienie aplikacji.

1. W projektancie kliknij usługę **Twitter**, a następnie kliknij wyzwalacz **Po wysłaniu nowego tweetu**. Zaloguj się na koncie w usłudze Twitter i zezwól aplikacji logiki na korzystanie z tego konta.

2. Użyj ustawień wyzwalacza usługi Twitter określonych w tabeli. 

    ![Ustawienia łącznika usługi Twitter](media/functions-twitter-email/10-tweet-settings.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | ----------------- | ------------ | ------------- |
    | **Wyszukiwany tekst** | #Azure | Użyj hasztagu dostatecznie popularnego, aby wygenerować nowe tweety w wybranym interwale. Jeśli używasz warstwy bezpłatnej, a hasztag jest zbyt popularny, możesz szybko zużyć przydział transakcji w interfejsie API usług Cognitive Services. |
    | **Interwał** | 15 | Czas między żądaniami usługi Twitter w jednostkach częstotliwości. |
    | **Częstotliwość** | Minuta | Jednostka częstotliwości używana do sondowania usługi Twitter.  |

3.  Kliknij przycisk **Zapisz** w celu połączenia się z kontem usługi Twitter. 

Teraz aplikacja jest połączona z usługą Twitter. Następnie należy połączyć się z analizą tekstu w celu wykrywania tonacji zebranych tweetów.

## <a name="add-sentiment-detection"></a>Dodawanie wykrywania tonacji

1. Kliknij pozycję **Nowy krok**, a następnie pozycję **Dodaj akcję**.

2. W obszarze **Wybierz akcję** wpisz **Analiza tekstu**, a następnie kliknij akcję **Wykryj tonację**.
    
    ![Zrzut ekranu przedstawiający sekcję "Wybieranie akcji" z "analiza tekstu" w polu wyszukiwania oraz wybraną akcję "Wykryj tonacji". ](media/functions-twitter-email/11-detect-sentiment.png)

3. Wpisz nazwę połączenia, taką jak `MyCognitiveServicesConnection`, wklej klucz interfejsu API usług Cognitive Services i punkt końcowy usług Cognitive Services zapisane w edytorze tekstów, a następnie kliknij pozycję **Utwórz**.

    ![Nowy krok, a następnie pozycja Dodaj akcję](media/functions-twitter-email/12-connection-settings.png)

4. Następnie w polu tekstowym wprowadź **tekst tweetu** , a następnie kliknij przycisk **nowy krok**.

    ![Definiowanie tekstu do przeanalizowania](media/functions-twitter-email/13-analyze-tweet-text.png)

Skonfigurowano już wykrywanie tonacji, więc można dodać połączenie z funkcją wykorzystującą dane wyjściowe wyniku tonacji.

## <a name="connect-sentiment-output-to-your-function"></a>Łączenie danych wyjściowych tonacji z funkcją

1. W projektancie Logic Apps kliknij pozycję **nowy krok**  >  **Dodaj akcję**, odfiltruj **Azure Functions** a następnie kliknij pozycję **Wybierz funkcję platformy Azure**.

    ![Wykrywanie tonacji](media/functions-twitter-email/14-azure-functions.png)
  
4. Wybierz aplikację funkcji, która została utworzona wcześniej.

    ![Zrzut ekranu pokazujący sekcję "Wybieranie akcji" z wybraną aplikacją funkcji.](media/functions-twitter-email/15-select-function.png)

5. Wybierz funkcję, która została utworzona na potrzeby tego samouczka.

    ![Wybieranie funkcji](media/functions-twitter-email/16-select-function.png)

4. W polu **Treść żądania** kliknij pozycję **Score** (Wynik), a następnie pozycję **Zapisz**.

    ![Wynik](media/functions-twitter-email/17-function-input-score.png)

Teraz funkcja jest wyzwalana po wysłaniu wyniku tonacji z aplikacji logiki. Funkcja zwraca aplikacji logiki kategorię kodowaną kolorem. Następnie zostanie dodane powiadomienie e-mail wysyłane w razie zwrócenia przez funkcję wartości tonacji **RED** (Czerwony). 

## <a name="add-email-notifications"></a>Dodawanie powiadomień w wiadomościach e-mail

Ostatnia część przepływu pracy polega na wyzwoleniu wiadomości e-mail, gdy wynik tonacji skategoryzowano jako _RED_ (Czerwony). W tym artykule jest stosowany łącznik Outlook.com. Wykonując podobne kroki, można użyć łącznika usługi Gmail lub programu Outlook w usłudze Office 365.   

1. W projektancie Logic Apps kliknij pozycję **nowy krok**  >  **Dodaj warunek**. 

    ![Dodaj warunek do aplikacji logiki.](media/functions-twitter-email/18-add-condition.png)

2. Kliknij pozycję **Wybierz wartość**, a następnie pozycję **Treść**. Wybierz pozycję **jest równe**, kliknij pozycję **Wybierz wartość** i wpisz `RED`, a następnie kliknij przycisk **Zapisz**. 

    ![Wybierz akcję dla warunku.](media/functions-twitter-email/19-condition-settings.png)    

3. W obszarze **Jeśli prawda** kliknij pozycję **Dodaj akcję**, wyszukaj `outlook.com`, kliknij pozycję **Wyślij wiadomość e-mail** i zaloguj się na koncie w usłudze Outlook.com.

    ![Zrzut ekranu przedstawiający sekcję "Jeśli prawda" z "outlook.com" wprowadzoną w polu wyszukiwania i wybraną akcję "Wyślij wiadomość e-mail".](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Jeśli nie masz konta usługi Outlook.com, możesz wybrać inny łącznik, taki jak usługi Gmail lub programu Outlook w usłudze Office 365.

4. W akcji **Wyślij wiadomość e-mail** użyj ustawień poczty e-mail określonych w tabeli. 

    ![Skonfiguruj wiadomość e-mail do wysłania oraz akcję jej wysyłania.](media/functions-twitter-email/21-configure-email.png)
    
| Ustawienie      |  Sugerowana wartość   | Opis  |
| ----------------- | ------------ | ------------- |
| **Działanie** | Wpisz adres e-mail | Adres e-mail, na który będą wysyłane powiadomienia. |
| **Temat** | Wykryto negatywną tonację tweetów  | Wiersz tematu powiadomienia w wiadomości e-mail.  |
| **Treść** | Tekst tweetu, lokalizacja | Kliknij parametry **Tekst tweetu** i **Lokalizacja**. |

1. Kliknij pozycję **Zapisz**.

Przepływ pracy jest gotowy, można więc włączyć aplikację logiki i przyjrzeć się funkcji w działaniu.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

1. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **Uruchom** w celu uruchomienia aplikacji.

2. W lewej kolumnie kliknij pozycję **Przegląd**, aby zobaczyć stan aplikacji logiki. 
 
    ![Stan wykonania aplikacji logiki](media/functions-twitter-email/22-execution-history.png)

3. Opcjonalnie: kliknij jeden z przebiegów, aby wyświetlić szczegóły wykonania.

4. Przejdź do funkcji, przejrzyj dzienniki i potwierdź, że wartości tonacji zostały odebrane i przetworzone.
 
    ![Wyświetlanie dzienników funkcji](media/functions-twitter-email/sent.png)

5. Po wykryciu potencjalnie negatywnej tonacji otrzymasz wiadomość e-mail. Jeśli wiadomość e-mail nie nadeszła, możesz tak zmienić kod funkcji, aby wartość RED (Czerwony) była zwracana zawsze:

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    Po zweryfikowaniu powiadomień e-mail przywróć pierwotną postać kodu:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > Po ukończeniu tego samouczka najlepiej jest wyłączyć aplikację logiki. Wyłączenie aplikacji pozwala uniknąć naliczania opłat za wykonania i zużywania transakcji w interfejsie API usług Cognitive Services.

Teraz wiesz, jak łatwo zintegrować funkcje z przepływem pracy Logic Apps.

## <a name="disable-the-logic-app"></a>Wyłączanie aplikacji logiki

Aby wyłączyć aplikację logiki, kliknij pozycję **Przegląd**, a następnie pozycję **Wyłącz** w górnej części ekranu. Wyłączenie aplikacji spowoduje jej zatrzymanie oraz przerwanie naliczania opłat bez usuwania aplikacji.

![Dzienniki funkcji](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu interfejsu API usług Cognitive Services.
> * Tworzenie funkcji kategoryzującej tonację w tweetach.
> * Tworzenie aplikacji logiki łączącej się z usługą Twitter.
> * Dodawanie wykrywania tonacji do aplikacji logiki. 
> * Łączenie aplikacji logiki z funkcją.
> * Wysyłanie wiadomości e-mail na podstawie odpowiedzi z funkcji.

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć dla funkcji bezserwerowy interfejs API.

> [!div class="nextstepaction"] 
> [Tworzenie bezserwerowego interfejsu API za pomocą usługi Azure Functions](functions-create-serverless-api.md)

Aby dowiedzieć się więcej na temat usługi Logic Apps, zobacz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).
