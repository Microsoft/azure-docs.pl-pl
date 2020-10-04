---
title: Wywoływanie interfejsu API usługi Text Analytics
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak można wywołać usługę Azure Cognitive Services analiza tekstu interfejsu API REST i programu Poster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: e17f2015ed4428cfd3c1a6c8a7bc4f92854a6b71
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710604"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak wywołać interfejs API REST analiza tekstu

Wywołania **interfejs API analizy tekstu** to wywołania http post/Get, które można sformułować w dowolnym języku. W tym artykule używamy REST i programu [Poster](https://www.postman.com/downloads/) , aby zademonstrować kluczowe pojęcia.

Każde żądanie musi zawierać klucz dostępu i punkt końcowy HTTP. Punkt końcowy określa region wybrany podczas rejestracji, adres URL usługi i zasób używany w żądaniu: `sentiment` , `keyphrases` , `languages` , i `entities` . 

Odzyskanie tego analiza tekstu jest bezstanowe, aby nie było żadnych zasobów danych do zarządzania. Tekst zostanie przekazany, przeanalizowany po odebraniu, a wyniki są zwracane natychmiast do aplikacji wywołującej.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definicja schematu JSON

Dane wejściowe muszą być w formacie JSON w nieprzetworzonym tekście bez struktury. KOD XML nie jest obsługiwany. Schemat jest prosty, składający się z elementów opisanych na poniższej liście. 

Obecnie można przesłać te same dokumenty do wszystkich operacji analiza tekstu: tonacji, frazy kluczowej, wykrywania języka i identyfikacji jednostek. (Schemat jest prawdopodobnie różny dla każdej analizy w przyszłości).

| Element | Prawidłowe wartości | Wymagane? | Użycie |
|---------|--------------|-----------|-------|
|`id` |Typ danych to ciąg, ale w temacie identyfikatory dokumentów w programie mają być liczbami całkowitymi. | Wymagane | System używa identyfikatorów dostarczanych do struktury danych wyjściowych. Kody języka, kluczowe frazy i oceny tonacji są generowane dla każdego identyfikatora w żądaniu.|
|`text` | Nieprzetworzony tekst nieprzebudowany, do 5 120 znaków. | Wymagane | W przypadku wykrywania języka tekst może być wyrażony w dowolnym języku. W przypadku analizy tonacji, wyodrębniania kluczowych fraz i identyfikacji jednostek tekst musi być w [obsługiwanym języku](../text-analytics-supported-languages.md). |
|`language` | 2-znakowy kod [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) dla [obsługiwanego języka](../text-analytics-supported-languages.md) | Różnie | Wymagane do analizy tonacji, wyodrębniania kluczowych fraz i konsolidacji jednostek; opcjonalne do wykrywania języka. Jeśli wyłączysz go, nie ma żadnego błędu, ale analiza zostanie nieprzerwana. Kod języka powinien odpowiadać podanemu podaniu `text` . |

Aby uzyskać więcej informacji na temat limitów, zobacz [Analiza tekstu omówienie > limitów danych](../overview.md#data-limits). 


```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    },
    {
      "language": "en",
      "id": "3",
      "text": "Pike place market is my favorite Seattle attraction."
    }
  ]
}
```


## <a name="set-up-a-request-in-postman"></a>Skonfiguruj żądanie w programie Poster

Usługa akceptuje żądania o rozmiarze do 1 MB. Jeśli używasz programu Poster (lub innego narzędzia testowego interfejsu API sieci Web), Skonfiguruj punkt końcowy do uwzględnienia zasobu, którego chcesz użyć, a następnie podaj klucz dostępu w nagłówku żądania. Każda operacja wymaga dołączenia odpowiedniego zasobu do punktu końcowego. 

1. W programie Poster:

   + Wybierz pozycję **post** jako typ żądania.
   + Wklej w punkcie końcowym skopiowanym ze strony portalu.
   + Dołącz zasób.

   Punkty końcowe zasobów są następujące (region może się różnić):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/entities/recognition/general`

2. Ustaw trzy nagłówki żądania:

   + `Ocp-Apim-Subscription-Key`: Twój klucz dostępu uzyskany z Azure Portal.
   + `Content-Type`: Application/JSON.
   + `Accept`: Application/JSON.

   Twoje żądanie powinno wyglądać podobnie do poniższego zrzutu ekranu, przy założeniu zasobu **/keyPhrases** .

   ![Zażądaj zrzutu ekranu z punktem końcowym i nagłówkami](../media/postman-request-keyphrase-1.png)

4. Kliknij pozycję **treść** , a następnie wybierz pozycję **RAW** dla formatu.

   ![Żądaj zrzutu ekranu z ustawieniami treści](../media/postman-request-body-raw.png)

5. Wklej w niektórych dokumentach JSON w formacie, który jest prawidłowy dla zamierzonej analizy. Aby uzyskać więcej informacji na temat konkretnej analizy, zobacz poniższe tematy:

  + [Wykrywanie języka](text-analytics-how-to-language-detection.md)  
  + [Wyodrębnianie kluczowych fraz](text-analytics-how-to-keyword-extraction.md)  
  + [Analiza tonacji](text-analytics-how-to-sentiment-analysis.md)  
  + [Rozpoznawanie jednostek](text-analytics-how-to-entity-linking.md)  


6. Kliknij przycisk **Wyślij** , aby przesłać żądanie. Zobacz sekcję [limity danych](../overview.md#data-limits) w temacie Omówienie, aby uzyskać informacje dotyczące liczby żądań wysyłanych na minutę i sekundę.

   W programie Poster odpowiedź jest wyświetlana w następnym oknie w dół, jako pojedynczy dokument JSON, z elementem dla każdego identyfikatora dokumentu podanego w żądaniu.

## <a name="see-also"></a>Zobacz też 

 [Przegląd analiza tekstu](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wykrywanie języka](text-analytics-how-to-language-detection.md)
