---
title: Translator 2.0
titleSuffix: Azure Cognitive Services
description: Dokumentacja dotycząca usługi Translator w wersji 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: fd0dbe5912b7c4df3c666c648dbf9a92d5398cf1
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369515"
---
# <a name="translator-v20"></a>Translator 2.0

> [!IMPORTANT]
> Ta wersja usługi Translator jest przestarzała. [Wyświetl dokumentację usługi Translator w wersji 3](v3-0-reference.md).

Program w wersji 2 translatora można bezproblemowo zintegrować z aplikacjami, witrynami sieci Web, narzędziami lub innymi rozwiązaniami w celu zapewnienia obsługi środowiska użytkownika w wielu językach. Można jej używać na dowolnej platformie sprzętowej i dowolnym systemie operacyjnym w celu przeprowadzenia tłumaczenia języka i innych zadań związanych z językiem, takich jak wykrywanie języka tekstu i zamiana tekstu na mowę, zgodnie ze standardami branżowymi. Aby uzyskać więcej informacji, zobacz [translator](../translator-info-overview.md).

## <a name="getting-started"></a>Wprowadzenie
Aby uzyskać dostęp do translatora, musisz [zarejestrować się w usłudze Microsoft Azure](../translator-how-to-signup.md).

## <a name="authentication"></a>Authentication 
Wszystkie wywołania translatora wymagają klucza subskrypcji do uwierzytelnienia. Interfejs API obsługuje trzy metody uwierzytelniania:

- Token dostępu. Użyj klucza subskrypcji, aby utworzyć token dostępu, wysyłając żądanie POST do usługi uwierzytelniania. Szczegółowe informacje znajdują się w dokumentacji usługi tokenu. Przekaż token dostępu do usługi translatora przy użyciu `Authorization` nagłówka lub `access_token` parametru zapytania. Token dostępu jest ważny przez 10 minut. Uzyskaj nowy token dostępu co 10 minut i Kontynuuj używanie tego samego tokenu dostępu dla powtarzanych żądań w ciągu 10 minut.
- Klucz subskrypcji jest używany bezpośrednio. Przekaż swój klucz subskrypcji jako wartość w `Ocp-Apim-Subscription-Key` nagłówku dołączonym do Twojego żądania do translatora. Jeśli używasz klucza subskrypcji bezpośrednio, nie musisz wywoływać usługi uwierzytelniania tokenów, aby utworzyć token dostępu.
- [Wielousługowa subskrypcja usługi Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ta metoda umożliwia użycie jednego klucza tajnego do uwierzytelniania żądań dla wielu usług.
W przypadku korzystania z wielousługowego klucza tajnego należy dołączyć dwa nagłówki uwierzytelniania do żądania. Pierwszy nagłówek przekazuje klucz tajny. Drugi nagłówek określa region skojarzony z subskrypcją:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Region jest wymagany w przypadku subskrypcji wielousługowego interfejsu API tekstu. Wybrany region jest jedynym regionem, którego można użyć do tłumaczenia tekstu przy użyciu klucza subskrypcji wielousługowej. Musi to być ten sam region, który został wybrany podczas rejestrowania się w ramach subskrypcji wielousługowej na Azure Portal.

Dostępne regiony to,,,,,,,,,,, `australiaeast` `brazilsouth` ,, `canadacentral` `centralindia` ,, `centraluseuap` `eastasia` `eastus` `eastus2` `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` i `westus2` .

Klucz subskrypcji i token dostępu są tajne, które powinny być ukryte przed wyświetleniem.

## <a name="profanity-handling"></a>Obsługa niewulgarności
Zwykle usługa translatora zachowa nieobecność, która jest obecna w źródle. Stopień niewulgarności i kontekst, który sprawia, że wyrazy nie różnią się w zależności od kultury. Dzięki temu można zwiększyć lub zmniejszyć stopień niewulgarności w języku docelowym.

Jeśli chcesz zapobiec niezbyt wulgarności w tłumaczeniu, nawet jeśli znajduje się w tekście źródłowym, możesz użyć opcji filtrowania niewymagającej niepotrzebnej metody, która go obsługuje. Opcja pozwala określić, czy ma być wyświetlana wartość wulgarne lub oznaczone za pomocą odpowiednich tagów, czy też chcesz zezwolić na nierówność w miejscu docelowym. Akceptowane wartości `ProfanityAction` to `NoAction` (domyślne), `Marked` i `Deleted` .


|ProfanityAction    |Akcja |Przykładowe źródło (japoński)  |Przykładowe tłumaczenie (angielski)  |
|:--|:--|:--|:--|
|NoAction   |Domyślne. Analogicznie jak ustawienie opcji nie jest możliwe. Niedostępność zostanie przekazana z lokalizacji źródłowej do docelowej.        |彼はジャッカスです.     |Jest to Jackass.   |
|Oznacz     |Słowa wulgarne będą otoczone tagami XML \<profanity> i \</profanity> .       |彼はジャッカスです. |Jest to \<profanity> Jackass \</profanity> .  |
|Usunięte    |Wyrazy wulgarne zostaną usunięte z danych wyjściowych bez zastępowania.     |彼はジャッカスです. |Jest to.   |

    
## <a name="excluding-content-from-translation"></a>Wykluczanie zawartości z tłumaczenia
W przypadku tłumaczenia zawartości za pomocą tagów, takich jak HTML ( `contentType=text/html` ), czasami warto wykluczyć określoną zawartość z tłumaczenia. Możesz użyć atrybutu, `class=notranslate` Aby określić zawartość, która powinna pozostać w oryginalnym języku. W poniższym przykładzie zawartość pierwszego `div` elementu nie zostanie przetłumaczona, ale zawartość drugiego `div` elementu zostanie przetłumaczona.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>Pobierz przełącznik/translate

### <a name="implementation-notes"></a>Uwagi o implementacji
Tłumaczy ciąg tekstowy z jednego języka na inny.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/Translate` .

**Wartość zwracana:** Ciąg, który reprezentuje przetłumaczony tekst.

Jeśli wcześniej użyto `AddTranslation` lub `AddTranslationArray` do wprowadzenia tłumaczenia z klasyfikacją 5 lub wyższą dla tego samego zdania źródła, `Translate` zwraca tylko górny wybór, który jest dostępny dla systemu. "Te same zdania źródłowe" oznaczają dokładnie te same (100%), z wyjątkiem wielkości liter, białych znaków, wartości tagów i interpunkcji na końcu zdania. Jeśli klasyfikacja nie jest przechowywana w klasyfikacji 5 lub wyższej, zwracany wynik będzie automatycznym tłumaczeniem według usługi Microsoft Translator.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: Application/XML

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis    |Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID  |ciągiem    |Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|tekst|ciągiem   |Wymagane. Ciąg, który reprezentuje tekst do przetłumaczenia. Tekst nie może zawierać więcej niż 10 000 znaków.|query|ciąg|
|wniosek|ciągiem   |Opcjonalny. Ciąg, który reprezentuje kod języka przetłumaczonego tekstu. Na przykład EN dla języka angielskiego.|query|ciąg|
|na wartość|ciągiem |Wymagane. Ciąg, który reprezentuje kod języka, do którego zostanie przetłumaczony tekst.|query|ciąg|
|contentType|ciągiem    |Opcjonalny. Format przetłumaczonego tekstu. Obsługiwane formaty to `text/plain` (domyślne) i  `text/html` . Wszystkie elementy HTML muszą być poprawnie sformułowane i kompletne.|query|ciąg|
|category|ciągiem   |Opcjonalny. Ciąg, który zawiera kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.|query|ciąg|
|Autoryzacja|ciągiem  |Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem  |Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|


### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="post-translatearray"></a>Opublikuj/TranslateArray

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera tłumaczenia dla wielu tekstów źródłowych.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray` .

Oto format treści żądania:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Te elementy znajdują się w `TranslateArrayRequest` :


* `AppId`: Wymagany. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `AppId` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .
* `From`Obowiązkowe. Ciąg, który reprezentuje kod języka przetłumaczonego tekstu. Jeśli to pole pozostanie puste, odpowiedź będzie obejmować wynik automatycznego wykrywania języka.
* `Options`Obowiązkowe. `Options`Obiekt, który zawiera następujące wartości. Są one opcjonalne i domyślne dla najczęściej używanych ustawień. Określone elementy muszą być wymienione w porządku alfabetycznym.
    - `Category`: Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.
    - `ContentType`: Format przetłumaczonego tekstu. Obsługiwane formaty to `text/plain` (domyślne), `text/xml` i `text/html` . Wszystkie elementy HTML muszą być poprawnie sformułowane i kompletne.
    - `ProfanityAction`: Określa, jak są obsługiwane wulgarne, jak wyjaśniono wcześniej. Akceptowane wartości to `NoAction` (domyślne), `Marked` i `Deleted` .
    - `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
    - `Uri`: Filtruj wyniki według tego identyfikatora URI. Wartość domyślna: `all`.
    - `User`: Filtruj wyniki według tego użytkownika. Wartość domyślna: `all`.
* `Texts`: Wymagany. Tablica zawierająca tekst do tłumaczenia. Wszystkie ciągi muszą być w tym samym języku. Suma całego tekstu, który ma zostać przetłumaczony, nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy to 2 000.
* `To`: Wymagany. Ciąg, który reprezentuje kod języka, do którego zostanie przetłumaczony tekst.

Można pominąć opcjonalne elementy. Elementy, które są bezpośrednimi elementami podrzędnymi, `TranslateArrayRequest` muszą być wymienione w porządku alfabetycznym.

`TranslateArray`Metoda akceptuje `application/xml` lub `text/xml` dla `Content-Type` .

**Wartość zwracana:** `TranslateArrayResponse` Tablica. Każdy z `TranslateArrayResponse` nich ma następujące elementy:

* `Error`: Wskazuje błąd, jeśli wystąpi. W przeciwnym razie ustaw wartość null.
* `OriginalSentenceLengths`: Tablica liczb całkowitych, która wskazuje długość każdego zdania w tekście źródłowym. Długość tablicy wskazuje liczbę zdań.
* `TranslatedText`: Przetłumaczony tekst.
* `TranslatedSentenceLengths`: Tablica liczb całkowitych, która wskazuje długość każdego zdania w przetłumaczonym tekście. Długość tablicy wskazuje liczbę zdań.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Zwraca tę samą zawartość co żądanie.

Oto format treści odpowiedzi:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Pomyślna odpowiedź obejmuje tablicę tablic `TranslateArrayResponse` w formacie opisanym wcześniej.

ciąg

Typ zawartości odpowiedzi: Application/XML

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|ciągiem  |Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem|Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP   |Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd. Typowe błędy obejmują: <ul><li>Element tablicy nie może być pusty.</li><li>Nieprawidłowa Kategoria.</li><li>Język jest nieprawidłowy.</li><li>Do języka jest nieprawidłowy.</li><li>Żądanie zawiera zbyt wiele elementów.</li><li>Język od nie jest obsługiwany.</li><li>Język do nie jest obsługiwany.</li><li>Żądanie tłumaczenia ma za dużo danych.</li><li>Format HTML jest niepoprawny.</li><li>W żądaniu tłumaczenia przekazano zbyt wiele ciągów.</li></ul>|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="post-getlanguagenames"></a>Opublikuj/GetLanguageNames

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera przyjazne nazwy języków przewidzianych jako parametr `languageCodes` zlokalizowany w pożądanym `locale` języku.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames` .

Treść żądania zawiera tablicę ciągów, która reprezentuje kody języka ISO 639-1, dla których mają zostać pobrane przyjazne nazwy. Oto przykład:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Wartość zwracana:** Tablica ciągów zawierająca nazwy języków obsługiwane przez usługę translatora zlokalizowanych w żądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągów zawierająca nazwy języków obsługiwane przez usługę translatora zlokalizowanych w żądanym języku.

ciąg

Typ zawartości odpowiedzi: Application/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem|Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|locale|ciągiem |Wymagane. Ciąg, który reprezentuje jeden z następujących, używany do lokalizowania nazw języka: <ul><li>Kombinacja kodu ISO 639 2 litery małymi literami związanymi z językiem i symbolem wielokulturowym ISO 3166 2. <li>Kod kulturowy z małymi literami ISO 639.|query|ciąg|
|Autoryzacja|ciągiem  |Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem  |Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="get-getlanguagesfortranslate"></a>Pobierz/GetLanguagesForTranslate

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera listę kodów języka reprezentujących języki obsługiwane przez usługę tłumaczenia.  `Translate` i `TranslateArray` można przetłumaczyć między dowolne dwa z tych języków.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate` .

**Wartość zwracana:** Tablica ciągów zawierająca kody języków obsługiwane przez usługę translatora.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągów zawierająca kody języków obsługiwane przez usługę translatora.

ciąg

Typ zawartości odpowiedzi: Application/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem|Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|Autoryzacja|ciągiem  |Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem|Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="get-getlanguagesforspeak"></a>Pobierz/GetLanguagesForSpeak

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera Języki dostępne dla syntezy mowy.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak` .

**Wartość zwracana:** Tablica ciągów zawierająca kody języków, które są obsługiwane przez funkcję synteza mowy przez usługę translatora.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica ciągów zawierająca kody języków, które są obsługiwane przez funkcję synteza mowy przez usługę translatora.

ciąg

Typ zawartości odpowiedzi: Application/XML

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem|Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|Autoryzacja|ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem|Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|
 
### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401|Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="get-speak"></a>Pobierz/Speak

### <a name="implementation-notes"></a>Uwagi o implementacji
Zwraca strumień WAV lub MP3 zadanego tekstu, który jest wymawiany w pożądanym języku.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/Speak` .

**Wartość zwracana:** Strumień WAV lub MP3 zadanego tekstu, który jest wymawiany w pożądanym języku.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

binarny

Typ zawartości odpowiedzi: Application/XML

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem|Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|tekst|ciągiem   |Wymagane. Ciąg zawierający co najmniej jedno zdanie, które ma być wymawiane dla strumienia w określonym języku. Długość tekstu nie może przekraczać 2 000 znaków.|query|ciąg|
|language|ciągiem   |Wymagane. Ciąg, który reprezentuje kod języka obsługiwanego w języku, w którym należy mówić do tekstu. Kod musi być jednym z kodów zwracanych przez metodę `GetLanguagesForSpeak` .|query|ciąg|
|format|ciągiem|Opcjonalny. Ciąg określający identyfikator typu zawartości. Obecnie  `audio/wav` i `audio/mp3` są dostępne. Wartość domyślna to `audio/wav`.|query|ciąg|
|opcje|ciągiem    |Opcjonalny. Ciąg określający właściwości wykorzystanej mowy:<ul><li>`MaxQuality` i `MinSize` określ jakość sygnału audio. `MaxQuality` zapewnia najwyższą jakość. `MinSize` zapewnia najmniejszy rozmiar pliku. Wartość domyślna to  `MinSize` .</li><li>`female` i `male` Określ żądany Płeć głosu. Wartość domyślna to `female`. Użyj pionowego słupka ( <code>\|</code> ), aby dołączyć wiele opcji. Przykład: `MaxQuality|Male`.</li></li></ul>  |query|ciąg|
|Autoryzacja|ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem  |Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="get-detect"></a>Pobierz/Detect

### <a name="implementation-notes"></a>Uwagi o implementacji
Identyfikuje język sekcji tekstu.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/Detect` .

**Wartość zwracana:** Ciąg zawierający kod języka dwuznakowego dla tekstu.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: Application/XML

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem  |Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|tekst|ciągiem|Wymagane. Ciąg zawierający tekst, którego język ma być zidentyfikowany. Długość tekstu nie może przekraczać 10 000 znaków.|query|  ciąg|
|Autoryzacja|ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key  |ciągiem    |Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|


## <a name="post-detectarray"></a>Opublikuj/DetectArray

### <a name="implementation-notes"></a>Uwagi o implementacji

Identyfikuje Języki w tablicy ciągów. Niezależnie wykrywa język każdego pojedynczego elementu tablicy i zwraca wynik dla każdego wiersza tablicy.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/DetectArray` .

Oto format treści żądania:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Tekst nie może zawierać więcej niż 10 000 znaków.

**Wartość zwracana:** Tablica ciągów zawierająca dwuznakowy kod języka dla każdego wiersza w tablicy wejściowej.

Oto format treści odpowiedzi:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
`DetectArray` zakończył się pomyślnie. Zwraca tablicę ciągów, która zawiera dwuznakowy kod języka dla każdego wiersza tablicy wejściowej.

ciąg

Typ zawartości odpowiedzi: Application/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem|Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|Autoryzacja|ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste.  Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem|Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="get-addtranslation"></a>Pobierz/AddTranslation

### <a name="implementation-notes"></a>Uwagi o implementacji

> [!IMPORTANT]
> **Uwaga dotycząca wycofania:** Po 31 stycznia 2018 ta metoda nie akceptuje nowych przesłanych zdań. Zostanie wyświetlony komunikat o błędzie. Zapoznaj się z informacjami o zmianach w programie do współpracy z platformą Translation (COLLABORATIVE).

Dodaje tłumaczenie do pamięci translacji.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation` .

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: aplikacja: XML
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych   |
|:--|:--|:--|:--|:--|
|AppID|ciągiem|Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|originalText|ciągiem|Wymagane. Ciąg, który zawiera tekst do przetłumaczenia. Maksymalna długość ciągu to 1 000 znaków.|query|ciąg|
|translatedText|ciągiem |Wymagane. Ciąg, który zawiera tekst przetłumaczony na język docelowy. Maksymalna długość ciągu to 2 000 znaków.|query|ciąg|
|wniosek|ciągiem   |Wymagane. Ciąg, który reprezentuje kod języka oryginalnego języka tekstu. Na przykład EN dla języka angielskiego i Niemcy dla języka niemieckiego.|query|ciąg|
|na wartość|ciągiem|Wymagane. Ciąg, który reprezentuje kod języka w celu przetłumaczenia tekstu na.|query|ciąg|
|rating|ciągiem |Opcjonalny. Liczba całkowita reprezentująca ocenę jakości dla ciągu. Wartość należy do zakresu od-10 do 10. Wartość domyślna to 1.|query|liczba całkowita|
|contentType|ciągiem    |Opcjonalny. Format przetłumaczonego tekstu. Obsługiwane formaty to `text/plain` i `text/html` . Wszystkie elementy HTML muszą być poprawnie sformułowane i kompletne.    |query|ciąg|
|category|ciągiem|Opcjonalny. Ciąg, który zawiera kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.|query|ciąg|
|użytkownik|ciągiem|Wymagane. Ciąg, który służy do śledzenia nadawcy przesłania.|query|ciąg|
|adresu|ciągiem|Opcjonalny. Ciąg, który zawiera lokalizację zawartości tłumaczenia.|query|ciąg|
|Autoryzacja|ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste.  Token autoryzacji:  `"Bearer" + " " + "access_token"` .  |header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem|Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|410|`AddTranslation` nie jest już obsługiwane.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="post-addtranslationarray"></a>Opublikuj/AddTranslationArray

### <a name="implementation-notes"></a>Uwagi o implementacji

> [!IMPORTANT]
> **Uwaga dotycząca wycofania:** Po 31 stycznia 2018 ta metoda nie akceptuje nowych przesłanych zdań. Zostanie wyświetlony komunikat o błędzie. Zapoznaj się z informacjami o zmianach w programie do współpracy z platformą Translation (COLLABORATIVE).

Dodaje tablicę tłumaczeń do pamięci translacji. Ta metoda jest wersją tablicową `AddTranslation` .

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray` .

Oto format treści żądania:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Te elementy znajdują się w `AddtranslationsRequest` :

* `AppId`: Wymagany. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `AppId` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .
* `From`: Wymagany. Ciąg, który zawiera kod języka języka źródłowego. Musi być jednym z języków zwracanych przez `GetLanguagesForTranslate` metodę.
* `To`: Wymagany. Ciąg, który zawiera kod języka dla języka docelowego. Musi być jednym z języków zwracanych przez `GetLanguagesForTranslate` metodę.
* `Translations`: Wymagany. Tablica tłumaczeń do dodania do pamięci translacji. Każde tłumaczenie musi zawierać `OriginalText` , `TranslatedText` , i `Rating` . Maksymalny rozmiar każdego z `OriginalText` i `TranslatedText` 1 000 znaków. Suma wszystkich `OriginalText` i `TranslatedText` elementów nie może być dłuższa niż 10 000 znaków. Maksymalna liczba elementów tablicy to 100.
* `Options`: Wymagany. Zestaw opcji, w tym `Category` ,, `ContentType` `Uri` , i `User` . Ciąg `User` jest wymagany. `Category`, `ContentType` , i `Uri` są opcjonalne. Określone elementy muszą być wymienione w porządku alfabetycznym.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
`AddTranslationArray` Metoda powiodła się. 

Po 31 stycznia 2018, zgłoszenia zdań nie będą akceptowane. Usługa odpowie z kodem błędu 410.

ciąg

Typ zawartości odpowiedzi: Application/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja|ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste.  Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem|Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|410    |`AddTranslation` nie jest już obsługiwane.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="get-breaksentences"></a>Pobierz/BreakSentences

### <a name="implementation-notes"></a>Uwagi o implementacji
Dzieli sekcję tekstu na zdania i zwraca tablicę zawierającą długości każdego zdania.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences` .

**Wartość zwracana:** Tablica liczb całkowitych, która reprezentuje długości zdań. Długość tablicy reprezentuje liczbę zdań. Wartości reprezentują długość każdego zdania.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
Tablica liczb całkowitych, która reprezentuje długości zdań. Długość tablicy reprezentuje liczbę zdań. Wartości reprezentują długość każdego zdania.

liczba całkowita

Typ zawartości odpowiedzi: Application/XML

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem  |Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query| ciąg|
|tekst|ciągiem   |Wymagane. Ciąg, który reprezentuje tekst do podziału na zdania. Maksymalny rozmiar tekstu to 10 000 znaków.|query|ciąg|
|language   |ciągiem    |Wymagane. Ciąg, który reprezentuje kod języka tekstu wejściowego.|query|ciąg|
|Autoryzacja|ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .   |header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem|Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400|Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401|Nieprawidłowe poświadczenia.|
|500|Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="post-gettranslations"></a>Opublikuj/GetTranslations

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera tablicę tłumaczeń dla danej pary językowej ze sklepu i aparatu MT. `GetTranslations` różni się od `Translate` , że zwraca wszystkie dostępne tłumaczenia.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations` .

Treść żądania zawiera opcjonalny `TranslationOptions` obiekt, który ma następujący format:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions`Obiekt zawiera wartości z poniższej listy. Są one opcjonalne i domyślne dla najczęściej używanych ustawień. Określone elementy muszą być wymienione w porządku alfabetycznym.

* `Category`: Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.
* `ContentType`: Jedyną obsługiwaną opcją i wartością domyślną jest `text/plain` .
* `IncludeMultipleMTAlternatives`: Wartość logiczna określająca, czy w aparacie MT należy zwrócić więcej niż jeden alternatywę. Prawidłowe wartości to `true` i `false` (z uwzględnieniem wielkości liter). Wartość domyślna to `false` , która zwraca tylko jedną alternatywę. Ustawienie flagi `true` umożliwia tworzenie sztucznych alternatyw, w pełni zintegrowanych z platformą translacji współpracy (Collaborative). Funkcja umożliwia zwracanie alternatyw dla zdań, które nie mają żadnych tłumaczeń w COLLABORATIVE przez dodanie sztucznych alternatyw z listy *n* -najlepszych dla dekodera.
    - Klasyfikowani. Klasyfikacje są stosowane w następujący sposób: 
         - Najlepsze automatyczne tłumaczenie ma klasyfikację 5.
       - Alternatywy od COLLABORATIVE odzwierciedlają urząd recenzenta. Należą one do zakresu od-10 do + 10.
       - Alternatywy dla tłumaczenia generowanego automatycznie ( *n* -Najlepsza) mają klasyfikację 0 i dopasowanie stopnia 100.
    - Liczba wariantów. Liczba zwróconych alternatyw może być tak duża jak wartość określona w `maxTranslations` , ale może być niższa.
    - Pary językowe. Ta funkcja jest niedostępna w przypadku tłumaczenia między chiński uproszczony i chiński tradycyjny, w dowolnym kierunku. Jest ona dostępna dla wszystkich innych par językowych obsługiwanych przez usługi Microsoft Translator.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
* `Uri`: Filtruj wyniki według tego identyfikatora URI. Jeśli wartość nie jest ustawiona, wartością domyślną jest `all` .
* `User`: Filtruj wyniki według tego użytkownika. Jeśli wartość nie jest ustawiona, wartością domyślną jest `all` .

Żądanie `Content-Type` powinno być `text/xml` .

**Wartość zwracana:** Oto format odpowiedzi:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Ta odpowiedź zawiera `GetTranslationsResponse` element, który zawiera następujące wartości:

* `Translations`: Tablica znalezionych dopasowań przechowywanych w `TranslationMatch` obiektach (opisana w poniższej sekcji). Tłumaczenia mogą zawierać nieznaczne odmiany oryginalnego tekstu (dopasowanie rozmyte). Tłumaczenia zostaną posortowane: 100% dopasowuje pierwsze, rozmyte dopasowuje dalej.
* `From`: Jeśli metoda nie określa `From` języka, ta wartość będzie pochodzić z automatycznego wykrywania języka. W przeciwnym razie będzie to określony `From` język.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Zawiera wartość podaną w `TranslateOptions` parametrze.

`TranslationMatch`Obiekt składa się z następujących wartości:

* `Error`: Kod błędu, jeśli wystąpi błąd dla określonego ciągu wejściowego. W przeciwnym razie to pole jest puste.
* `MatchDegree`: Wskazuje, jak blisko tekst wejściowy jest zgodny z oryginalnym tekstem znalezionym w sklepie. System dopasowuje zdania wejściowe do magazynu, w tym niedokładne dopasowania. Wartość zwracana jest z zakresu od 0 do 100, gdzie 0 oznacza brak podobieństwa, a 100 jest dokładnym dopasowaniem z uwzględnieniem wielkości liter.
* `MatchedOriginalText`: Oryginalny tekst, który został dopasowany do tego wyniku. Ta wartość jest zwracana tylko wtedy, gdy dopasowany oryginalny tekst różni się od tekstu wejściowego. Służy do zwrócenia tekstu źródłowego dopasowania rozmytego. Ta wartość nie jest zwracana dla wyników usługi Microsoft Translator.
* `Rating`: Wskazuje urząd osoby odpowiedzialnej za podjęcie decyzji dotyczącej jakości. Wyniki tłumaczenia maszynowego mają klasyfikację 5. Anonimowo udostępniane tłumaczenia zwykle mają klasyfikację od 1 do 4. Tłumaczenia przekazane autorytatywnie zazwyczaj mają klasyfikację od 6 do 10.
* `Count`: Liczba wybranych tłumaczeń z tą klasyfikacją. Wartość jest równa 0 dla automatycznie przetłumaczonej odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.

### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)
`GetTranslationsResponse`Obiekt w formacie opisanym wcześniej.

ciąg

Typ zawartości odpowiedzi: Application/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|AppID|ciągiem|Wymagane. Jeśli `Authorization` `Ocp-Apim-Subscription-Key` jest używany nagłówek lub, pozostaw `appid` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .|query|ciąg|
|tekst|ciągiem|Wymagane. Ciąg, który reprezentuje tekst do przetłumaczenia. Maksymalny rozmiar tekstu to 10 000 znaków.|query|ciąg|
|wniosek|ciągiem|Wymagane. Ciąg, który reprezentuje kod języka przetłumaczonego tekstu.|query|ciąg|
|na wartość |ciągiem    |Wymagane. Ciąg, który reprezentuje kod języka w celu przetłumaczenia tekstu na.|query|ciąg|
|maxTranslations|ciągiem|Wymagane. Liczba całkowita, która reprezentuje maksymalną liczbę tłumaczeń do zwrócenia.|query|liczba całkowita|
|Autoryzacja| ciągiem|Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste. Token autoryzacji:  `"Bearer" + " " + "access_token"` .|ciąg|  header|
|Ocp-Apim-Subscription-Key|ciągiem  |Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi `X-MS-Trans-Info` .|
|503|Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="post-gettranslationsarray"></a>Opublikuj/GetTranslationsArray

### <a name="implementation-notes"></a>Uwagi o implementacji
Pobiera wiele kandydatów tłumaczenia dla wielu tekstów źródłowych.

Identyfikator URI żądania to `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray` .

Oto format treści żądania:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` obejmuje następujące elementy:

* `AppId`: Wymagany. Jeśli `Authorization` nagłówek jest używany, pozostaw `AppId` pole puste. W przeciwnym razie Uwzględnij ciąg, który zawiera `"Bearer" + " " + "access_token"` .
* `From`: Wymagany. Ciąg, który reprezentuje kod języka przetłumaczonego tekstu.
* `MaxTranslations`: Wymagany. Liczba całkowita, która reprezentuje maksymalną liczbę tłumaczeń do zwrócenia.
* `Options`Obowiązkowe. `Options`Obiekt, który zawiera następujące wartości. Są one opcjonalne i domyślne dla najczęściej używanych ustawień. Określone elementy muszą być wymienione w porządku alfabetycznym.
    - `Category`: Ciąg zawierający kategorię (domenę) tłumaczenia. Wartość domyślna to `general`.
    - `ContentType`: Jedyną obsługiwaną opcją i wartością domyślną jest `text/plain` .
    - `IncludeMultipleMTAlternatives`: Wartość logiczna określająca, czy w aparacie MT należy zwrócić więcej niż jeden alternatywę. Prawidłowe wartości to `true` i `false` (z uwzględnieniem wielkości liter). Wartość domyślna to `false` , która zwraca tylko jedną alternatywę. Ustawienie flagi `true` umożliwia generowanie sztucznych alternatyw w tłumaczeniu, w pełni zintegrowanych z platformą tłumaczenia współpracy (Collaborative). Funkcja włącza alternatywy dla zdań, które nie mają alternatywy w COLLABORATIVE przez dodanie sztucznych alternatyw z listy *n* -najlepszych dla dekodera.
        - Klasyfikacje, które są stosowane do klasyfikacji:
          - Najlepsze automatyczne tłumaczenie ma klasyfikację 5.
          - Alternatywy od COLLABORATIVE odzwierciedlają urząd recenzenta. Należą one do zakresu od-10 do + 10.
          - Alternatywy dla tłumaczenia generowanego automatycznie ( *n* -Najlepsza) mają klasyfikację 0 i dopasowanie stopnia 100.
        - Liczba wariantów. Liczba zwróconych alternatyw może być tak duża jak wartość określona w `maxTranslations` , ale może być niższa.
        - Pary językowe. Ta funkcja jest niedostępna w przypadku tłumaczenia między chiński uproszczony i chiński tradycyjny, w dowolnym kierunku. Jest ona dostępna dla wszystkich innych par językowych obsługiwanych przez usługi Microsoft Translator.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Ta sama zawartość zostanie zwrócona w odpowiedzi.
* `Uri`: Filtruj wyniki według tego identyfikatora URI. Jeśli wartość nie jest ustawiona, wartością domyślną jest `all` .
* `User`: Filtruj wyniki według tego użytkownika. Jeśli wartość nie jest ustawiona, wartością domyślną jest `all` .
* `Texts`: Wymagany. Tablica zawierająca tekst do tłumaczenia. Wszystkie ciągi muszą być w tym samym języku. Suma całego tekstu, który ma zostać przetłumaczony, nie może przekraczać 10 000 znaków. Maksymalna liczba elementów tablicy wynosi 10.
* `To`: Wymagany. Ciąg, który reprezentuje kod języka w celu przetłumaczenia tekstu na.

Można pominąć opcjonalne elementy. Elementy, które są bezpośrednimi elementami podrzędnymi, `GetTranslationsArrayRequest` muszą być wymienione w porządku alfabetycznym.

Żądanie `Content-Type` powinno być `text/xml` .

**Wartość zwracana:** Oto format odpowiedzi:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Każdy `GetTranslationsResponse` element zawiera następujące wartości:

* `Translations`: Tablica znalezionych dopasowań przechowywanych w `TranslationMatch` obiektach (opisana w poniższej sekcji). Tłumaczenia mogą zawierać nieznaczne odmiany oryginalnego tekstu (dopasowanie rozmyte). Tłumaczenia zostaną posortowane: 100% dopasowuje pierwsze, rozmyte dopasowuje dalej.
* `From`: Jeśli metoda nie określa `From` języka, ta wartość będzie pochodzić z automatycznego wykrywania języka. W przeciwnym razie będzie to określony `From` język.
* `State`: Stan użytkownika, aby pomóc skorelować żądanie i odpowiedź. Zawiera wartość podaną w `TranslateOptions` parametrze.

`TranslationMatch`Obiekt zawiera następujące wartości:
* `Error`: Kod błędu, jeśli wystąpi błąd dla określonego ciągu wejściowego. W przeciwnym razie to pole jest puste.
* `MatchDegree`: Wskazuje, jak blisko tekst wejściowy jest zgodny z oryginalnym tekstem znalezionym w sklepie. System dopasowuje zdania wejściowe do magazynu, w tym niedokładne dopasowania. Wartość zwracana jest z zakresu od 0 do 100, gdzie 0 oznacza brak podobieństwa, a 100 jest dokładnym dopasowaniem z uwzględnieniem wielkości liter.
* `MatchedOriginalText`: Oryginalny tekst, który został dopasowany do tego wyniku. Ta wartość jest zwracana tylko wtedy, gdy dopasowany oryginalny tekst różni się od tekstu wejściowego. Służy do zwrócenia tekstu źródłowego dopasowania rozmytego. Ta wartość nie jest zwracana dla wyników usługi Microsoft Translator.
* `Rating`: Wskazuje urząd osoby odpowiedzialnej za podjęcie decyzji dotyczącej jakości. Wyniki tłumaczenia maszynowego mają klasyfikację 5. Anonimowo udostępniane tłumaczenia zwykle mają klasyfikację od 1 do 4. Przekazane autorytatywne ogólnie tłumaczenia mają klasyfikację od 6 do 10.
* `Count`: Liczba wybranych tłumaczeń z tą klasyfikacją. Wartość jest równa 0 dla automatycznie przetłumaczonej odpowiedzi.
* `TranslatedText`: Przetłumaczony tekst.


### <a name="response-class-status-200"></a>Klasa odpowiedzi (stan 200)

ciąg

Typ zawartości odpowiedzi: Application/XML
 
### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:--|:--|:--|:--|:--|
|Autoryzacja  |ciągiem    |Wymagane, jeśli oba `appid` pola i `Ocp-Apim-Subscription-Key` nagłówki są puste.  Token autoryzacji:  `"Bearer" + " " + "access_token"` .|header|ciąg|
|Ocp-Apim-Subscription-Key|ciągiem  |Wymagane, jeśli oba `appid` pola i `Authorization` nagłówki są puste.|header|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|
|:--|:--|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe i szczegółową odpowiedź na błąd.|
|401    |Nieprawidłowe poświadczenia.|
|500    |Błąd serwera. Jeśli błąd będzie się powtarzać, daj nam znać. Podaj przybliżoną datę, & godzinę żądania oraz IDENTYFIKATORem żądania zawartym w nagłówku odpowiedzi  `X-MS-Trans-Info` .|
|503    |Usługa jest tymczasowo niedostępna. Spróbuj ponownie, aby poinformować nas o tym, czy błąd będzie nadal występował.|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie do usługi Translator v3](../migrate-to-v3.md)