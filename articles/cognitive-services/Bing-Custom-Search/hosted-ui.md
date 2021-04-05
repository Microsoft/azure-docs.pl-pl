---
title: Konfigurowanie hostowanego interfejsu użytkownika dla wyszukiwanie niestandardowe Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ten artykuł służy do konfigurowania i integrowania hostowanego interfejsu użytkownika wyszukiwanie niestandardowe Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca3f9da681c60608b5b196b17191f0b4d549f305
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338371"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurowanie środowiska hostowanego interfejsu użytkownika

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Wyszukiwanie niestandardowe Bing udostępnia hostowany interfejs użytkownika, który można łatwo zintegrować ze stronami sieci Web i aplikacjami internetowymi jako fragment kodu JavaScript. Za pomocą portalu wyszukiwanie niestandardowe Bing można skonfigurować opcje układu, koloru i wyszukiwania interfejsu użytkownika.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurowanie niestandardowego interfejsu użytkownika hostowanego

Aby skonfigurować hostowany interfejs użytkownika dla aplikacji sieci Web, wykonaj następujące kroki. Po wprowadzeniu zmian okienko po prawej stronie udostępni Podgląd interfejsu użytkownika. Wyświetlane wyniki wyszukiwania nie są rzeczywistymi wynikami dla danego wystąpienia.

1. Zaloguj się do [portalu](https://customsearch.ai)wyszukiwanie niestandardowe Bing.  
  
2. Wybierz wystąpienie wyszukiwanie niestandardowe Bing.

3. Kliknij kartę **Hosted UI** (Hostowany interfejs użytkownika).  
  
4. Wybierz układ.

    - Pasek wyszukiwania i wyniki (domyślnie): Wyświetla pole wyszukiwania z wynikami wyszukiwania poniżej.
    - Tylko wyniki: wyświetla tylko wyniki wyszukiwania, bez pola wyszukiwania. W przypadku korzystania z tego układu należy podać zapytanie wyszukiwania ( `&q=<query string>` ). Dodaj parametr zapytania do adresu URL żądania w fragmencie kodu JavaScript lub linku do punktu końcowego HTML.
    - Wyskakujące okienko: zawiera pole wyszukiwania i wyświetla wyniki wyszukiwania w przesuwanej nakładce.

5. Wybierz motyw kolorów. Kolory można dostosować w celu dopasowania do aplikacji przez kliknięcie przycisku **Dostosuj motyw**. Aby zmienić kolor, wprowadź wartość SZESNASTKOWą RGB koloru (na przykład `#366eb8` ) lub kliknij Podgląd koloru.

   Zmiany można wyświetlić w podglądzie po prawej stronie portalu. Kliknięcie przycisku **Resetuj do ustawień domyślnych** spowoduje przywrócenie domyślnych kolorów dla wybranego motywu.

   > [!NOTE]
   > Weź pod uwagę ułatwienia dostępu, wybierając kolory.

6. W obszarze **dodatkowe konfiguracje** podaj wartości odpowiednie dla aplikacji. Te ustawienia są opcjonalne. Aby zobaczyć efekt zastosowania lub usunięcia tych elementów, zobacz okienko podglądu po prawej stronie. Dostępne opcje konfiguracji:  

7. Wprowadź klucz subskrypcji wyszukiwania lub wybierz jeden z listy rozwijanej. Lista rozwijana zawiera klucze z subskrypcji konta platformy Azure. Zobacz [Cognitive Services konta interfejsu API](../cognitive-services-apis-create-account.md).  

8. Jeśli włączono automatyczne sugerowanie, wprowadź klucz subskrypcji autosugeruj lub wybierz jeden z listy rozwijanej. Lista rozwijana zawiera klucze z subskrypcji konta platformy Azure. Niestandardowe automatyczne sugerowanie wymaga określonej warstwy subskrypcji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Korzystanie z niestandardowego interfejsu użytkownika

Aby korzystać z hostowanego interfejsu użytkownika: 

- Dołącz skrypt do strony sieci Web  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Lub użyj następującego adresu URL w przeglądarce sieci Web.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > W razie potrzeby dodaj następujące parametry zapytania do adresu URL. Informacje o tych parametrach znajdują się w temacie [interfejs API wyszukiwania niestandardowego](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) Reference.
  >
  > - q
  > - mkt
  > - bezpieczne wyszukiwanie
  > - setlang

  > [!IMPORTANT]
  > Na stronie nie można wyświetlić zasad zachowania poufności informacji ani innych uwag i warunków. Przydatność do użycia może się różnić.  

Aby uzyskać dodatkowe informacje, w tym identyfikator konfiguracji niestandardowej, przejdź do obszaru **punkty końcowe** na karcie **produkcja** .

## <a name="configuration-options"></a>Opcje konfiguracji

Można skonfigurować zachowanie hostowanego interfejsu użytkownika, klikając **dodatkowe konfiguracje** i dostarczając wartości. Te ustawienia są opcjonalne. Aby zobaczyć efekt zastosowania lub usunięcia tych elementów, zobacz okienko podglądu po prawej stronie. 

### <a name="web-search-configurations"></a>Konfiguracje wyszukiwania w sieci Web

- Wyniki sieci Web włączone: określa, czy jest włączone wyszukiwanie w sieci Web (w górnej części strony zostanie wyświetlona karta Sieć Web).
- Włącz automatyczne sugerowanie: określa, czy jest włączone niestandardowe automatyczne sugerowanie (zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) dla dodatkowych kosztów).
- Wyniki sieci Web na stronie: liczba wyników wyszukiwania w sieci Web do wyświetlenia w danym momencie (wartość maksymalna to 50 wyników na stronie).
- Podpis obrazu: określa, czy obrazy są wyświetlane z wynikami wyszukiwania.

Po kliknięciu przycisku **Pokaż konfiguracje zaawansowane** wyświetlane są następujące konfiguracje:

- Wyróżnij wyrazy: określa, czy wyniki wyszukiwania są wyświetlane pogrubioną czcionką.
- Obiekt docelowy linku: określa, czy strona sieci Web otwiera się w nowej karcie przeglądarki (pustej) czy na tej samej karcie przeglądarki (samodzielnej), gdy użytkownik kliknie wynik wyszukiwania.

### <a name="image-search-configurations"></a>Konfiguracje wyszukiwania obrazów

- Wyniki obrazu włączone: określa, czy wyszukiwanie obrazów jest włączone (karta obrazy w górnej części strony).
- Wyniki obrazu na stronę: liczba wyników wyszukiwania obrazów do wyświetlenia w danym momencie (wartość maksymalna to 150 wyników na stronie).

Po kliknięciu przycisku **Pokaż konfiguracje zaawansowane** zostanie wyświetlona następująca konfiguracja.  
  
- Włącz filtry: dodaje filtry, których użytkownik może użyć do filtrowania obrazów zwracanych przez usługę Bing. Na przykład użytkownik może filtrować wyniki tylko dla animowanych plików GIF.

### <a name="video-search-configurations"></a>Konfiguracje wyszukiwania wideo

- Wyniki wideo włączone: określa, czy wyszukiwanie wideo jest włączone (karta wideo znajduje się u góry strony).
- Wyniki wideo na stronę: liczba wyników wyszukiwania wideo do wyświetlenia w danym momencie (maksymalna liczba wyników na stronę to 150).

Po kliknięciu przycisku **Pokaż konfiguracje zaawansowane** zostanie wyświetlona następująca konfiguracja.  
  
- Włącz filtry: dodaje filtry, których użytkownik może używać do filtrowania filmów wideo zwracanych przez usługę Bing. Na przykład użytkownik może filtrować wyniki filmów wideo z określoną rozdzielczością lub klipami wideo odnalezionymi w ciągu ostatnich 24 godzin.

### <a name="miscellaneous-configurations"></a>Różne konfiguracje

- Tytuł strony: tekst wyświetlany w obszarze tytułu strony wyników wyszukiwania (nie dla układu wyskakującego).
- Motyw paska narzędzi: Określa kolor tła obszaru tytułu strony wyników wyszukiwania.

Po kliknięciu przycisku **Pokaż konfiguracje zaawansowane** wyświetlane są następujące konfiguracje.  

|Column1  |Kolumna2  |
|---------|---------|
|Symbol zastępczy tekstu pola wyszukiwania   | Tekst wyświetlany w polu wyszukiwania przed wejściem.        |
|Adres URL linku tytułu    |Element docelowy linku tytułu.         |
|Adres URL logo     | Obraz wyświetlany obok tytułu.         |
|Favicon    | Ikona wyświetlana na pasku tytułu przeglądarki.          |

Następujące konfiguracje mają zastosowanie tylko wtedy, gdy korzystasz z hostowanego interfejsu użytkownika za pomocą punktu końcowego HTML (nie mają zastosowania, jeśli używasz fragmentu kodu JavaScript).

- Tytuł strony
- Motyw paska narzędzi
- Adres URL linku tytułu
- Adres URL logo
- Adres URL Faviicon  

## <a name="next-steps"></a>Następne kroki

- [Wyróżnianie tekstu za pomocą znaczników dekoracji](../bing-web-search/hit-highlighting.md)
- [Dzielenie na strony wyników wyszukiwania stron internetowych](../bing-web-search/paging-search-results.md)