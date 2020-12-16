---
title: Co nowego w usłudze Rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Zapoznaj się z najnowszymi zmianami w interfejsie API aparatu rozpoznawania formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: 3f71cef19d25a7f987af4147ae0a889280e49bd1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563164"
---
# <a name="whats-new-in-form-recognizer"></a>Co nowego w usłudze Rozpoznawanie formularzy?

Usługa aparat rozpoznawania formularzy jest regularnie aktualizowana. Skorzystaj z tego artykułu, aby uzyskać aktualne informacje dotyczące ulepszeń, poprawek i aktualizacji dokumentacji.

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="new-features"></a>Nowe funkcje

**Aparat rozpoznawania w formacie 2.0 2.1 (publiczna wersja zapoznawcza 2) jest teraz dostępny.** Wersja 2.1 — wersja zapoznawcza. 2 została wydana, łącznie z następującymi funkcjami: 

- **Nowy model prekompilowanej faktury** — nowy model prekompilowanej faktury umożliwia klientom korzystanie z faktur w różnych formatach i zwracanie danych strukturalnych w celu zautomatyzowania przetwarzania faktur. Łączy nasze zaawansowane funkcje rozpoznawania znaków optycznych (OCR) z fakturą zrozumienie modeli uczenia głębokiego, aby wyodrębnić informacje o kluczu z faktur w języku angielskim. Wyodrębnia tekst, tabele i informacje, takie jak klient, dostawca, Identyfikator faktury, Data płatności faktury, suma, kwota należna, kwota podatku, Wysyłka do, rachunek do i inne.

  > [Dowiedz się więcej na temat prekompilowanego modelu faktury](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="przykład faktury" lightbox="./media/invoice-example.jpg":::

- **Ulepszone wyodrębnianie tabel** — aparat rozpoznawania formularzy udostępnia teraz rozszerzoną funkcję wyodrębniania tabel, która łączy nasze zaawansowane możliwości rozpoznawania znaków optycznych (OCR) z modelem wyodrębniania tabel uczenia głębokiego. Aparat rozpoznawania formularzy może wyodrębnić dane z tabel, w tym złożone tabele ze scalonymi kolumnami, wierszami, bez obramowania i nie tylko. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="przykład tabel" lightbox="./media/tables-example.jpg":::

 
  > [Dowiedz się więcej o wyodrębnianiu układu](concept-layout.md)

- **Aktualizacja biblioteki klienta** — najnowsze wersje [bibliotek klienckich](quickstarts/client-library.md) dla platform .NET, Python, Java i JavaScript obsługują interfejs API rozpoznawania formularzy 2,1.
- **Obsługiwany nowy język: japoński** — teraz obsługiwane są następujące nowe języki: dla `AnalyzeLayout` i `AnalyzeCustomForm` : japoński ( `ja` ). [Obsługa języków](language-support.md)
- **Oznaczenie stylu linii tekstowej (pismo odręczne/inne) (tylko języki łacińskie)** — aparat rozpoznawania formularzy teraz wyprowadza `appearance` obiekt sklasyfikowany niezależnie od tego, czy każdy wiersz tekstu jest stylem odręcznym, czy nie, wraz z oceną ufności. Ta funkcja jest obsługiwana tylko dla języków łacińskich.
- **Udoskonalenia dotyczące jakości** — ulepszenia wyodrębniania, w tym ulepszenia wyodrębniania z jednej cyfry.
- **Nowa funkcja try-The-out w narzędziu do rozpoznawania i etykietowania** aplikacji, która umożliwia wypróbowanie wstępnie skompilowanych modeli faktur, paragonów i kart służbowych oraz interfejsu API układu przy użyciu narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy. Zobacz, w jaki sposób dane będą wyodrębniane bez pisania kodu.

  > [Wypróbuj narzędzie przykładowe aparat rozpoznawania formularzy](https://fott-preview.azurewebsites.net/)

  ![Przykład FOTT](./media/ui-preview.jpg)
  
- **Pętla opinii** — podczas analizowania plików za pomocą przykładowego narzędzia do etykietowania można teraz również dodać je do zestawu szkoleniowego i dostosować etykiety w razie potrzeby i pouczenie się, aby poprawić model.
- **Automatyczne etykietowanie dokumentów** — automatyczne etykietowanie dodatkowych dokumentów w oparciu o poprzednie dokumenty etykietowane w projekcie.

## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="new-features"></a>Nowe funkcje

**Dostępna jest publiczna wersja zapoznawcza aparatu rozpoznawania w wersji 2.1.** Wersja 2.1 — wersja zapoznawcza. 1 została wydana, w tym następujące funkcje: 


- **Dokumentacja interfejsu API REST jest dostępna** — zapoznaj się z dokumentacją [v 2.1 — wersja zapoznawcza 1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **Oprócz języka angielskiego obsługiwane są nowe języki**, które są obecnie obsługiwane: dla [języków](language-support.md) `Layout` i `Train Custom Model` : angielski ( `en` ), chiński (uproszczony) ( `zh-Hans` ), holenderski ( `nl` ), francuski ( `fr` ), niemiecki (), `de` włoski ( `it` ), portugalski () `pt` i hiszpański ( `es` ).
- **Wykrywanie znaczników wyboru/zaznaczenia** — aparat rozpoznawania formularzy obsługuje wykrywanie i wyodrębnianie znaczników wyboru, takich jak pola wyboru i przyciski radiowe. Znaczniki wyboru są wyodrębniane w `Layout` , a teraz można również etykietować i nauczyć się w `Train Custom Model`  -  _pouczeniu z etykietami_ , aby wyodrębnić pary kluczy wartości dla znaczników wyboru. 
- **Redagowanie modelu** — umożliwia tworzenie i wywoływanie wielu modeli przy użyciu jednego identyfikatora modelu. Gdy dokument zostanie przeanalizowany w celu przeanalizowania z IDENTYFIKATORem modelu złożonego, etap klasyfikacji jest najpierw wykonywany, aby skierować go do poprawnego modelu niestandardowego. Tworzenie modelu jest dostępne dla `Train Custom Model`  -  _uczenia się z etykietami_.
- **Nazwa modelu** — Dodaj przyjazną nazwę do modeli niestandardowych, aby ułatwić zarządzanie i śledzenie.
- **[Nowy wstępnie utworzony model dla kart służbowych](concept-business-cards.md)** służący do wyodrębniania typowych pól w języku angielskim, językowym kartą biznesową.
- **[Nowe ustawienia regionalne dla wstępnie utworzonych przyjęć](concept-receipts.md)** oprócz en-us, pomoc techniczna jest teraz dostępna dla en-AU, en-CA, en-GB, EN-in
- **Udoskonalenia jakości** `Layout` , `Train Custom Model`  -  _uczenie bez etykiet_ i _uczenie się z etykietami_.


**wersja 2.0** obejmuje następujące aktualizacje:

- [Biblioteki klienckie](quickstarts/client-library.md) dla języków NET, Python, Java i JavaScript zostały ogólnie dostępne. 


**Nowe przykłady** są dostępne w serwisie GitHub. 
- [Przepisy dotyczące ekstrakcji merytorycznej — formularze element PlayBook](https://github.com/microsoft/knowledge-extraction-recipes-forms) zbierają najlepsze rozwiązania z zakresu rzeczywistego zaangażowania użytkowników aparatu rozpoznawania i udostępniają przykłady kodu, listy kontrolne i potoki przykładowe używane podczas tworzenia projektów. 
- [Narzędzie przykładowe etykietowanie](https://github.com/microsoft/OCR-Form-Tools) zostało zaktualizowane w celu obsługi nowych funkcji w wersji 2.1. Ten [Przewodnik Szybki Start](quickstarts/label-tool.md) zawiera wprowadzenie do narzędzia. 
- Przykładowy aparat rozpoznawania w postaci [inteligentnego kiosku](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) pokazuje, jak zintegrować `Analyze Receipt` i `Train Custom Model`  -  _uczenie bez etykiet_.



## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="new-features"></a>Nowe funkcje

* **wersja 2.0 dostępna** — informacje o [interfejsie API 2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) oraz zaktualizowanych zestawach SDK dla [platformy .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet), [Python](/python/api/overview/azure/?view=azure-python), [Java](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)i [JavaScript](/javascript/api/overview/azure/?view=azure-node-latest).
* Udoskonalenia **tabel i ulepszenia wyodrębniania** — w tym ulepszenia dokładności i wyodrębniania tabel, w tym możliwość poznania nagłówków i struktur tabel w _niestandardowym pouczeniu bez etykiet_. 

* **Obsługa waluty** — wykrywanie i wyodrębnianie symboli walut globalnych.
* **Usługa Azure gov** — aparat rozpoznawania formularzy jest teraz również dostępny na platformie Azure gov.
* **Ulepszone funkcje zabezpieczeń**: 
   * Usługa aparat rozpoznawania **własnych** formularzy automatycznie szyfruje dane po utrwaleniu ich w chmurze w celu zapewnienia jej ochrony i zapewnienia zgodności ze swoimi zobowiązaniami dotyczącymi zabezpieczeń i bezpieczeństwa. Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Teraz możesz również zarządzać subskrypcją przy użyciu własnych kluczy szyfrowania. [Klucze zarządzane przez klienta, znane także jako dające własny klucz (BYOK)](./form-recognizer-encryption-of-data-at-rest.md), zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.  
   * **Prywatne punkty końcowe** — umożliwia korzystanie z sieci wirtualnej (VNET) w celu [bezpiecznego dostępu do danych za pośrednictwem prywatnego linku.](../../private-link/private-link-overview.md)


## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="new-features"></a>Nowe funkcje
* **Interfejs API CopyModel został dodany do zestawów SDK klienta** — teraz można używać zestawów SDK klienta do kopiowania modeli z jednej subskrypcji do innej. Zobacz [Tworzenie kopii zapasowych i odzyskiwanie modeli,](./disaster-recovery.md) Aby uzyskać ogólne informacje dotyczące tej funkcji.
* **Integracja Azure Active Directory** — teraz można używać poświadczeń usługi Azure AD do uwierzytelniania obiektów klienta aparatu rozpoznawania formularzy w zestawach SDK.
* **Zmiany specyficzne dla zestawu SDK** — dotyczy to zarówno dodatkowych, jak i drobnych zmian funkcji. Aby uzyskać więcej informacji, zobacz dziennik zmian zestawu SDK.
  * [Podgląd zestawu C# SDK 3 — dziennik zmian](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Dziennik zmian zestawu SDK języka Python w wersji 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Podgląd zestawu Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Kod dziennika zmian zestawu JavaScript SDK Preview 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="new-features"></a>Nowe funkcje
* **Obsługa zestawu SDK dla interfejsu API w wersji 1.0 2.0 — publiczna wersja zapoznawcza** — w tym miesiącu rozszerzono obsługę usługi w celu uwzględnienia zestawu SDK wersji zapoznawczej dla aparatu rozpoznawania wersji 2.0 (wersja zapoznawcza). Skorzystaj z poniższych linków, aby rozpocząć pracę z wybranym językiem: 
   * [Zestaw SDK platformy .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet)
   * [Zestaw SDK Java](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)
   * [Zestaw SDK dla języka Python](/python/api/overview/azure/ai-formrecognizer-readme?view=azure-python-preview)
   * [Zestaw SDK dla języka JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-preview)

  Nowy zestaw SDK obsługuje wszystkie funkcje interfejsu API REST programu v 2.0 na potrzeby aparatu rozpoznawania formularzy. Na przykład można przeprowadzić uczenie modelu z etykietami lub bez nich oraz Wyodrębnianie tekstu, par klucz-wartość i tabel z formularzy, wyodrębnianie danych z przyjęć przy użyciu wstępnie utworzonych usług przyjęć oraz Wyodrębnianie tekstu i tabel z użyciem usługi układu z dokumentów. Swoją opinię na temat zestawów SDK możesz udostępnić za pomocą [formularza opinii zestawu SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Kopiuj model niestandardowy** Teraz można kopiować modele między regionami i subskrypcjami przy użyciu nowej funkcji kopiowania modelu niestandardowego. Przed wywołaniem interfejsu API kopiowania niestandardowych modeli należy najpierw uzyskać autoryzację do skopiowania do zasobu docelowego, wywołując operację kopiowania autoryzacji do docelowego punktu końcowego zasobu.
   * [Generuj autoryzację kopiowania](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) INTERFEJS API REST
   * [Kopiowanie modelu niestandardowego](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) INTERFEJS API REST 

### <a name="security-improvements"></a>Ulepszenia zabezpieczeń

* Klucze Customer-Managed są teraz dostępne dla FormRecognizer. Aby uzyskać więcej informacji, zobacz [szyfrowanie danych w usłudze REST dla aparatu rozpoznawania formularzy](./form-recognizer-encryption-of-data-at-rest.md).
* Korzystaj z tożsamości zarządzanych, aby uzyskiwać dostęp do zasobów platformy Azure za pomocą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do zarządzanych tożsamości](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Marzec 2020 r. 

### <a name="new-features"></a>Nowe funkcje

* **Typy wartości dla etykietowania** Teraz możesz określić typy wartości, które są oznaczone etykietami, za pomocą narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy. Następujące typy wartości i różnice są obecnie obsługiwane:
  * `string`
    * domyślne, `no-whitespaces` , `alphanumeric`
  * `number`
    * wartooć `currency`
  * `date` 
    * domyślne, `dmy` , `mdy` , `ymd`
  * `time`
  * `integer`

  Aby dowiedzieć się, jak korzystać z tej funkcji, zobacz Przewodnik po [przykładowym narzędziu do etykietowania](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Wizualizacja tabeli** Narzędzie przykładowe etykietowanie wyświetla teraz tabele, które zostały rozpoznane w dokumencie. Ta funkcja umożliwia wyświetlenie tabel, które zostały rozpoznane i wyodrębnione z dokumentu przed oznaczeniem i analizą. Tę funkcję można włączyć/wyłączyć przy użyciu opcji warstwy.

  Na poniższej ilustracji przedstawiono przykład sposobu rozpoznawania i wyodrębniania tabel:

  > [!div class="mx-imgBorder"]
  > ![Wizualizacja tabeli przy użyciu narzędzia do etykietowania przykładowego](./media/whats-new/formre-table-viz.png)

    Wyodrębnione tabele są dostępne w danych wyjściowych JSON poniżej `"pageResults"` .

  > [!IMPORTANT]
  > Tabele etykiet nie są obsługiwane. Jeśli tabele nie są rozpoznawane i extrated automatycznie, można je oznaczyć tylko jako pary klucz/wartość. Podczas etykietowania tabel jako par klucz/wartość, Oznacz każdą komórkę jako wartość unikatową.

### <a name="extraction-enhancements"></a>Ulepszenia wyodrębniania

Ta wersja obejmuje ulepszenia wyodrębniania i ulepszenia dokładności, w oddzielnym zakresie, możliwość etykietowania i wyodrębniania wielu par klucz/wartość w tym samym wierszu tekstu. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Przykładowe narzędzie do etykietowania jest teraz otwarte — źródło

Narzędzie do etykietowania przykładowego aparatu rozpoznawania formularzy jest teraz dostępne jako projekt Open-Source. Możesz zintegrować je w swoich rozwiązaniach i wprowadzić zmiany specyficzne dla klienta, aby zaspokoić Twoje potrzeby.

Aby uzyskać więcej informacji na temat narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy, zapoznaj się z dokumentacją dostępną w witrynie [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Wymuszanie protokołu TLS 1.2

Protokół TLS 1,2 jest teraz wymuszany dla wszystkich żądań HTTP do tej usługi. Aby uzyskać więcej informacji, zobacz [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>Styczeń 2020 r.

W tej wersji wprowadzono aparat rozpoznawania formularzy 2,0 (wersja zapoznawcza). W poniższych sekcjach znajdziesz więcej informacji na temat nowych funkcji, ulepszeń i zmian. 

### <a name="new-features"></a>Nowe funkcje

* **Model niestandardowy**
  * **Uczenie z etykietami** Teraz można nauczyć model niestandardowy z ręcznie oznaczonymi danymi. Ta metoda skutkuje lepszymi modelami i może generować modele, które współpracują z złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.
  * **Asynchroniczny interfejs API** Za pomocą asynchronicznych wywołań interfejsu API można uczenie się i analizować duże zestawy danych i pliki.
  * **Obsługa plików TIFF** Teraz można nauczyć się i wyodrębnić dane z dokumentów TIFF.
  * **Ulepszenia dokładności wyodrębniania**

* **Wstępnie utworzony model przyjęcia**
  * **Kwoty Porada** Teraz można wyodrębnić kwoty etykiet i inne odręczne wartości.
  * **Wyodrębnianie elementu wiersza** Można wyodrębnić wartości elementów wiersza z przyjęć.
  * **Wartości zaufania** Można wyświetlić zaufanie modelu dla każdej wyodrębnionej wartości.
  * **Ulepszenia dokładności wyodrębniania**

* **Wyodrębnianie układu** Teraz można używać interfejsu API układu do wyodrębniania danych tekstowych i danych tabeli z formularzy.

### <a name="custom-model-api-changes"></a>Zmiany modelu niestandardowego interfejsu API

Wszystkie interfejsy API do szkolenia i korzystania z modeli niestandardowych zostały zmienione, a niektóre metody synchroniczne są teraz asynchroniczne. Poniżej przedstawiono istotne zmiany:

* Proces uczenia modelu jest teraz asynchroniczny. Możesz inicjować szkolenia za pomocą wywołania interfejsu API **/Custom/models** . To wywołanie zwraca identyfikator operacji, który można przekazać do **niestandardowych/modeli/{modelID}** w celu zwrócenia wyników szkoleniowych.
* Wyodrębnianie klucza/wartości jest teraz inicjowane przez wywołanie interfejsu API **/Custom/models/{modelID}/analyze** . To wywołanie zwraca identyfikator operacji, który można przekazać do **niestandardowych/modeli/{modelID}/analyzeResults/{identyfikatora resultid}** , aby zwrócić wyniki wyodrębniania.
* Identyfikatory operacji dla operacji pouczenia znajdują się teraz w nagłówku **lokalizacji** odpowiedzi HTTP, a nie w nagłówku **lokalizacji operacji** .

### <a name="receipt-api-changes"></a>Zmiany interfejsu API paragonów

Nazwy interfejsów API do odczytywania przyjęć sprzedaży zostały zmienione.

* Wyodrębnianie danych przyjęcia jest teraz inicjowane przez wywołanie interfejsu API **/prebuilt/receipt/analyze** . To wywołanie zwraca identyfikator operacji, który można przekazać do **/prebuilt/receipt/analyzeResults/{resultID}** w celu zwrócenia wyników wyodrębniania.

### <a name="output-format-changes"></a>Zmiany formatu danych wyjściowych

Odpowiedzi JSON dla wszystkich wywołań interfejsu API mają nowe formaty. Niektóre klucze i wartości zostały dodane, usunięte lub zmieniono ich nazwy. Przykłady bieżących formatów JSON można znaleźć w przewodnikach Szybki Start.

## <a name="next-steps"></a>Następne kroki

Ukończ [Przewodnik Szybki Start dla biblioteki klienta](quickstarts/client-library.md) , aby rozpocząć pisanie aplikacji przetwarzania formularzy przy użyciu aparatu rozpoznawania formularzy w wybranym języku.

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](./overview.md)
