---
title: Co nowego w usłudze Rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Poznaj najnowsze zmiany w interfejsie API Rozpoznawanie formularzy API.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: e952d481daf53b1806dc3cfbb658c8c0c21f6984
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516301"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-form-recognizer"></a>Co nowego w usłudze Rozpoznawanie formularzy?

Usługa Rozpoznawanie formularzy jest aktualizowana na bieżąco. Skorzystaj z tego artykułu, aby być na bieżąco z ulepszeniami funkcji, poprawkami i aktualizacjami dokumentacji.

## <a name="april-2021"></a>Kwiecień 2021 r.
<!-- markdownlint-disable MD029 -->

### <a name="sdk-updates-api--version-21-preview3"></a>Aktualizacje zestawu SDK (interfejs API w wersji 2.1-preview.3)

### <a name="c-version-310-beta4"></a>**C# w wersji 3.1.0-beta.4**

* **Nowe metody analizowania danych z dokumentów tożsamości:**

   **[StartRecognizeIdDocumentsFromUriAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   **[StartRecognizeIdDocumentsAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   Aby uzyskać listę wartości pól, _zobacz_ [Pola wyodrębnione](concept-identification-cards.md#fields-extracted) w naszej Rozpoznawanie formularzy dokumentacji.

* Rozszerzono zestaw języków dokumentów, które można dostarczać do **[metody StartRecognizeContent.](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)**

* **Nowa właściwość `Pages` obsługiwana przez następujące klasy:**

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   Właściwość umożliwia wybranie poszczególnych stron lub zakresu stron dla `Pages` wielostronicowych dokumentów PDF i TIFF. W przypadku poszczególnych stron wprowadź numer strony, na przykład `3` . Dla zakresu stron (takich jak strona 2 i strony 5–7) wprowadź liczby wiekowe p i zakresy rozdzielone przecinkami: `2, 5-7` .    

* **Nowa właściwość `ReadingOrder` obsługiwana dla następującej klasy:**

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  Właściwość jest opcjonalnym parametrem, który pozwala określić, który algorytm kolejności odczytu — lub — powinien być stosowany w celu uporządkowania `ReadingOrder` `basic` `natural` wyodrębniania elementów tekstowych. Jeśli nie zostanie określony, wartość domyślna to `basic` .

#### <a name="breaking-changes"></a>Fundamentalne zmiany

* Domyślnie klient korzysta z najnowszej obsługiwanej wersji usługi, która jest obecnie **w wersji 2.1-preview.3.**

* **[Metoda StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** zgłasza teraz `RequestFailedException()` wyjątek, gdy zostanie przekazany nieprawidłowy plik.

### <a name="java-version-310-beta3"></a>**Java w wersji 3.1.0-beta.3**

* **Nowe metody analizowania danych z dokumentów tożsamości:**

  **[beginRecognizeIdDocumentsFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocumentsfromurl?view=azure-java-preview&preserve-view=true)**

  **[beginRecognizeIdDocuments](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocuments?view=azure-java-preview&preserve-view=true)**

   Aby uzyskać listę wartości pól, _zobacz_ [Pola wyodrębnione](concept-identification-cards.md#fields-extracted) w naszej Rozpoznawanie formularzy dokumentacji.

* **Obsługa pliku obrazu mapy bitowej (bmp) dla formularzy niestandardowych i metod trenowania w `FormContentType` wylicie**:

* `image/bmp`

* **Nowa właściwość `Pages` obsługiwana przez następujące klasy:**

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  Właściwość `Pages` umożliwia wybranie poszczególnych stron lub zakresów stron dla wielostronicowych dokumentów PDF i TIFF. W przypadku poszczególnych stron wprowadź numer strony, na przykład `3` . W przypadku zakresu stron (takich jak strona 2 i strony 5–7) wprowadź numery stron i zakresy rozdzielone przecinkami: `2, 5-7` .

* **Obsługa pliku obrazu mapy bitowej (bmp) dla formularzy niestandardowych i metod trenowania w polach [FormContentType:](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)**

  `image/bmp`

* **Nowy argument słowa `ReadingOrder` kluczowego obsługiwany dla następujących metod:**

* **[beginRecognizeContent](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?view=azure-java-preview&preserve-view=true)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   Argument słowa kluczowego jest opcjonalnym parametrem, który pozwala określić, który algorytm kolejności odczytu — lub — powinien zostać zastosowany w celu uporządkowania `ReadingOrder` `basic` `natural` wyodrębniania elementów tekstowych. Jeśli nie zostanie określona, wartość domyślna to `basic` .

* Domyślnie klient korzysta z najnowszej obsługiwanej wersji usługi, która obecnie to **2.1-preview.3.**

### <a name="javascript-version-310-beta3"></a>**JavaScript w wersji 3.1.0-beta.3**

* **Nowe metody analizowania danych z dokumentów tożsamości:**

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    Aby uzyskać listę wartości pól, _zobacz_ [Pola wyodrębnione](concept-identification-cards.md#fields-extracted) w naszej Rozpoznawanie formularzy dokumentacji.

* **Nowe wartości pól dodane do interfejsu FieldValue:**

    `gender`— możliwe wartości to `M` `F` lub `X` .</br>
   `country`— możliwe wartości są zgodne z trzyliterowym ciągiem kodu kraju w standardach [ISO alpha-3.](https://www.iso.org/obp/ui/#search)

* **Nowa opcja `pages` obsługiwana przez wszystkie metody rozpoznawania formularzy (formularze niestandardowe i wszystkie wstępnie utworzone modele). Argument umożliwia wybranie poszczególnych stron lub zakresu stron dla wielostronicowych dokumentów PDF i TIFF. W przypadku poszczególnych stron wprowadź numer strony, na przykład `3` . W przypadku zakresu stron (takich jak strona 2 i strony 5–7) wprowadź numery stron i zakresy rozdzielone przecinkami: `2, 5-7` .

* Dodano obsługę typu **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/readingorder?view=azure-node-preview&preserve-view=true)** do metod rozpoznawania zawartości. Ta opcja umożliwia kontrolowanie algorytmu używanego przez usługę do określania kolejności rozpoznanych wierszy tekstu. Można określić, który algorytm kolejności odczytu — lub — ma być stosowany w celu `basic` `natural` uporządkowania wyodrębniania elementów tekstowych. Jeśli nie zostanie określony, wartość domyślna to `basic` .

* Podziel **[typ FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** na kilka różnych interfejsów. Nie powinno to powodować żadnych problemów ze zgodnością interfejsu API z wyjątkiem niektórych przypadków brzegowych (niezdefiniowany typ wartości).

* Migracja do punktu końcowego usługi **Rozpoznawanie formularzy 2.1-preview.3** dla wszystkich wywołań interfejsu API REST.

### <a name="python-version--310b4"></a>**Python w wersji 3.1.0b4**

* **Nowe metody analizowania danych z dokumentów tożsamości:**

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  Aby uzyskać listę wartości pól, _zobacz_ [Pola wyodrębnione](concept-identification-cards.md#fields-extracted) w naszej Rozpoznawanie formularzy dokumentacji.

* **Nowe wartości pól dodane do [wyliczeni FieldValueType:](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true)**

   gender — możliwe wartości to `M` `F` lub `X` .

  country — możliwe wartości są [zgodne ze standardem ISO alpha-3 Country Codes](https://www.iso.org/obp/ui/#search).

* **Obsługa pliku obrazu mapy bitowej (bmp) dla formularzy niestandardowych i metod trenowania w [wylicie FormContentType:](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true)**

    image/bmp

* **Nowy argument słowa `pages` kluczowego obsługiwany przez następujące metody:**

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Argument słowa kluczowego umożliwia wybranie poszczególnych stron lub zakresu stron dla `pages` wielostronicowych dokumentów PDF i TIFF. W przypadku poszczególnych stron wprowadź numer strony, na przykład `3` . W przypadku zakresu stron (takich jak strona 2 i strony 5–7) wprowadź numery stron i zakresy rozdzielone przecinkami: `2, 5-7` .

* **Nowy argument słowa `readingOrder` kluczowego obsługiwany dla następujących metod:**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Argument słowa kluczowego jest opcjonalnym parametrem, który pozwala określić, który algorytm kolejności odczytu — lub — powinien zostać zastosowany w celu uporządkowania `readingOrder` `basic` `natural` wyodrębniania elementów tekstowych. Jeśli nie zostanie określona, wartość domyślna to `basic` .

## <a name="march-2021"></a>Marzec 2021 r.

**Rozpoznawanie formularzy dostępna jest publiczna wersja zapoznawcza 3 w wersji 2.1.** Została wydana wersja 2.1-preview.3, w tym następujące funkcje:

* **Nowy wstępnie utworzony model identyfikatorów** Nowy wstępnie utworzony model identyfikatorów umożliwia klientom korzystanie z identyfikatorów i zwracanie ustrukturyzowanych danych w celu zautomatyzowania przetwarzania. Łączy ona nasze zaawansowane funkcje optycznego rozpoznawania znaków (OCR) z modelami do rozumienia identyfikatorów w celu wyodrębniania kluczowych informacji z paszportów i licencji na kierowcy w Stanach Zjednoczonych, takich jak nazwa, data urodzenia, data wydania, data wygaśnięcia i inne.

  [Dowiedz się więcej o wstępnie utworzonym modelu identyfikatorów](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="przykładowy paszport" lightbox="./media/id-canada-passport-example.png":::

* **Wyodrębnianie elementów wiersza dla wstępnie utworzonego modelu** faktur — wstępnie utworzony model faktur obsługuje teraz wyodrębnianie elementów wiersza; Teraz wyodrębnia pełne elementy i ich części — opis, ilość, ilość, identyfikator produktu, datę i inne. Za pomocą prostego wywołania interfejsu API/zestawu SDK możesz wyodrębnić przydatne dane z faktur — tekst, tabelę, pary klucz-wartość i elementy wiersza.

   [Dowiedz się więcej o wstępnie utworzonym modelu faktur](concept-invoices.md)

* **Nadzorowane** etykietowanie i trenowanie tabel, etykietowanie pustych wartości — [](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)oprócz najnowocześniejszej funkcji automatycznego wyodrębniania tabel w uczeniu głębokim firmy Rozpoznawanie formularzy umożliwia teraz klientom etykietowanie i trenowanie tabel. Ta nowa wersja obejmuje możliwość etykietowania i trenowania elementów wiersza/tabel (dynamicznych i stałych) oraz trenowania modelu niestandardowego w celu wyodrębniania par klucz-wartość i elementów wiersza. Gdy model zostanie wytrenowany, wyodrębni elementy wiersza w ramach danych wyjściowych JSON w sekcji documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Etykietowanie tabel" lightbox="./media/table-labeling.png":::

    Oprócz etykietowania tabel można teraz oznaczać puste wartości i regiony. Jeśli niektóre dokumenty w zestawie treningowym nie mają wartości dla niektórych pól, możesz je oznaczać etykietami, aby model wiedział, jak prawidłowo wyodrębniać wartości z przeanalizowanych dokumentów.

* **Obsługa 66 nowych języków** — interfejs API Rozpoznawanie formularzy i modele niestandardowe obsługują teraz 73 języki.

  [Dowiedz się więcej Rozpoznawanie formularzy o pomocy technicznej języka firmy](language-support.md)

* **Naturalna** kolejność czytania, klasyfikacja pisma ręcznego i wybór strony — dzięki tej aktualizacji możesz wybrać sposób uzyskania danych wyjściowych wiersza tekstu w naturalnej kolejności odczytywania zamiast domyślnej kolejności od lewej do prawej i od góry do dołu. Użyj nowego parametru zapytania readingOrder i ustaw go na wartość "natural" dla bardziej przyjaznych dla człowieka danych wyjściowych kolejności odczytu. Ponadto w przypadku języków łacińskich Rozpoznawanie formularzy klasyfikuje wiersze tekstu jako styl odręczny i daje wynik ufności.

* **Ulepszenia jakości wstępnie utworzonego modelu paragonu** Ta aktualizacja zawiera wiele ulepszeń jakości dla wstępnie utworzonego modelu paragonu, szczególnie w przypadku wyodrębniania elementów wiersza.

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="new-features"></a>Nowe funkcje

**Rozpoznawanie formularzy publiczna wersja zapoznawcza 2.1 jest teraz dostępna.** Została wydana wersja 2.1-preview.2, w tym następujące funkcje:

- **Nowy wstępnie utworzony model** faktur — nowy wstępnie utworzony model faktur umożliwia klientom korzystanie z faktur w różnych formatach i zwracanie ustrukturyzowanych danych w celu zautomatyzowania przetwarzania faktur. Łączy ona nasze zaawansowane możliwości optycznego rozpoznawania znaków (OCR) z modelami uczenia głębokiego do wyodrębniania kluczowych informacji z faktur w języku angielskim. Wyodrębnia ona tekst klucza, tabele i informacje, takie jak klient, dostawca, identyfikator faktury, data płatności faktury, suma, kwota należna, kwota podatku, wysyłka i rachunek.

  > [Dowiedz się więcej o wstępnie utworzonym modelu faktur](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="przykład faktury" lightbox="./media/invoice-example.jpg":::

- **Ulepszone wyodrębnianie** tabel — Rozpoznawanie formularzy teraz ulepszone wyodrębnianie tabel, które łączy nasze zaawansowane możliwości optycznego rozpoznawania znaków (OCR) z modelem wyodrębniania tabel uczenia głębokiego. Rozpoznawanie formularzy wyodrębniać dane z tabel, w tym złożone tabele ze scalaymi kolumnami, wierszami, bez obramowań i nie tylko.

  :::image type="content" source="./media/tables-example.jpg" alt-text="przykład tabel" lightbox="./media/tables-example.jpg":::


  > [Dowiedz się więcej na temat wyodrębniania układu](concept-layout.md)

- **Aktualizacja biblioteki klienta** — [](quickstarts/client-library.md) najnowsze wersje bibliotek klienckich dla platform .NET, Python, Java i JavaScript obsługują interfejs API Rozpoznawanie formularzy 2.1.
- **Obsługiwany jest nowy język: japoński** — obecnie obsługiwane są następujące nowe języki: dla `AnalyzeLayout` i : japoński ( `AnalyzeCustomForm` `ja` ). [Obsługa języków](language-support.md)
- Oznaczanie stylu linii tekstu **(odręczne/inne) (tylko** języki łaciński) — Rozpoznawanie formularzy teraz dane wyjściowe obiektu klasyfikowania, czy każdy wiersz tekstu jest stylu odręcznego, wraz ze wskaźnikiem `appearance` ufności. Ta funkcja jest obsługiwana tylko w językach łacińskich.
- **Ulepszenia jakości** — ulepszenia wyodrębniania, w tym ulepszenia wyodrębniania z jedną cyfrą.
- Nowa funkcja wypróbuj w narzędziu do przykładów i etykietowania usługi **Rozpoznawanie formularzy** — możliwość wypróbowania wstępnie utworzonych modeli faktur, paragonów i wizytówki oraz interfejsu API układu przy użyciu narzędzia do etykietowania przykładów Rozpoznawanie formularzy. Zobacz, jak dane zostaną wyodrębnione bez pisania kodu.

  > [Wypróbuj przykładowe Rozpoznawanie formularzy narzędziu](https://fott-preview.azurewebsites.net/)

  ![Przykład DLAT](./media/ui-preview.jpg)

- **Sprzężenie zwrotne** — podczas analizowania plików za pomocą przykładowego narzędzia do etykietowania można teraz również dodać je do zestawu treningowego i w razie potrzeby dostosować etykiety oraz w razie potrzeby trenować w celu ulepszenia modelu.
- **Automatyczne etykietowanie dokumentów** — automatycznie oznacza dodatkowe dokumenty na podstawie poprzednich dokumentów z etykietami w projekcie.

## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="new-features"></a>Nowe funkcje

**Rozpoznawanie formularzy publiczna wersja zapoznawcza 2.1 jest teraz dostępna.** Została wydana wersja 2.1-preview.1, w tym następujące funkcje:


- **Dokumentacja interfejsu API REST jest dostępna** — zobacz informacje w wersji [2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- Obsługiwane są nowe języki Oprócz [](language-support.md) języka angielskiego obsługiwane są teraz następujące języki: dla `Layout` i : angielski `Train Custom Model` `en` (), chiński (uproszczony) ( `zh-Hans` ), holenderski `nl` (), francuski `fr` (), niemiecki (), włoski `de` (), portugalski `it` `pt` () i hiszpański `es` ().
- **Wykrywanie znaczników wyboru/zaznaczenia** — Rozpoznawanie formularzy wykrywania i wyodrębniania znaczników wyboru, takich jak pola wyboru i przyciski radiowe. Znaki wyboru są wyodrębnione w programie i można teraz również oznaczać etykietami i trenować w trenowanie za pomocą etykiet w celu wyodrębnienia `Layout` `Train Custom Model`  -   par klucz-wartość dla znaczników wyboru.
- **Tworzenie modelu —** umożliwia tworzenie i wywoływanie wielu modeli z jednym identyfikatorem modelu. Gdy przesyłasz dokument do przeanalizowania za pomocą złożonego identyfikatora modelu, najpierw jest wykonywany krok klasyfikacji w celu przekierowywał go do prawidłowego modelu niestandardowego. Tworzenie modelu jest dostępne dla `Train Custom Model`  -  _trenowania z etykietami_.
- **Nazwa modelu** — dodaj przyjazną nazwę do modeli niestandardowych, aby ułatwić zarządzanie i śledzenie.
- **[Nowy wbudowany model wizytówek](concept-business-cards.md)** do wyodrębniania typowych pól w języku angielskim, wizytówek w języku angielskim.
- **[Nowe opcje lokalne dla wstępnie](concept-receipts.md)** sbudowaną paragonów oprócz en-us, obsługa jest teraz dostępna dla EN-AU, EN-CA, EN-GB, EN-IN
- **Ulepszenia jakości dla** `Layout` , `Train Custom Model`  -  _Trenuj bez etykiet i_ _Trenuj za pomocą etykiet_.

**Wersja 2.0** obejmuje następującą aktualizację:

- Biblioteki [klienckie dla](quickstarts/client-library.md) platform NET, Python, Java i JavaScript zostały ogólnie dostępne.

**Nowe przykłady** są dostępne w witrynie GitHub.

- Podręcznik [Knowledge Extraction Recipes — Forms](https://github.com/microsoft/knowledge-extraction-recipes-forms) (Przepisy wyodrębniania wiedzy — formularze) zbiera najlepsze rozwiązania z rzeczywistych Rozpoznawanie formularzy zaangażowania klientów i udostępnia użyteczne przykłady kodu, listy kontrolne i przykładowe potoki używane podczas opracowywania tych projektów.
- Przykładowe [narzędzie do etykietowania](https://github.com/microsoft/OCR-Form-Tools) zostało zaktualizowane w celu obsługi nowej funkcji w wersji 2.1. Zobacz ten [przewodnik Szybki start,](quickstarts/label-tool.md) aby rozpocząć pracę z narzędziem.
- Przykład [inteligentnego kiosku](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Rozpoznawanie formularzy sposób integracji i szkolenia `Analyze Receipt` bez `Train Custom Model`  -  _etykiet._

## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="new-features"></a>Nowe funkcje
<!-- markdownlint-disable MD004 -->
* **Dostępna dokumentacja w wersji 2.0** — zobacz informacje dotyczące interfejsu API w wersji [2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) i zaktualizowane zestawy SDK dla [platform .NET,](/dotnet/api/overview/azure/ai.formrecognizer-readme) [Python,](/python/api/overview/azure/) [Java](/java/api/overview/azure/ai-formrecognizer-readme)i [JavaScript.](/javascript/api/overview/azure/)
* **Ulepszenia tabel i** ulepszenia wyodrębniania — obejmują ulepszenia dokładności i ulepszenia wyodrębniania tabel, w szczególności możliwość uczenia nagłówków i struktur tabel w niestandardowym trenie bez _etykiet._

* **Obsługa walut —** wykrywanie i wyodrębnianie globalnych symboli walut.
* **Azure Gov** — Rozpoznawanie formularzy jest teraz również dostępna w usłudze Azure Gov.
* **Ulepszone funkcje zabezpieczeń:**
  * **Bring your own key** — Rozpoznawanie formularzy automatycznie szyfruje dane po utrwalenia w chmurze w celu ich ochrony i spełnienia wymagań organizacji dotyczących zabezpieczeń i zgodności. Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Teraz możesz również zarządzać subskrypcją przy użyciu własnych kluczy szyfrowania. Klucze zarządzane przez klienta, znane również jako bring [your own key (BYOK),](./encrypt-data-at-rest.md)oferują większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.
  * **Prywatne punkty końcowe** — umożliwiają w sieci wirtualnej bezpieczny dostęp do danych [za pośrednictwem Private Link.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="new-features"></a>Nowe funkcje

* **Interfejs API CopyModel dodany do zestawów SDK klienta** — możesz teraz używać zestawów SDK klienta do kopiowania modeli z jednej subskrypcji do innej. Aby uzyskać ogólne informacje na temat tej funkcji, zobacz Back [up and recover models (Modele](./disaster-recovery.md) kopii zapasowej i odzyskiwania).
* **Azure Active Directory integracji —** teraz możesz używać poświadczeń usługi Azure AD do uwierzytelniania obiektów Rozpoznawanie formularzy klienta w zestawach SDK.
* **Zmiany specyficzne dla zestawu SDK** — ta zmiana obejmuje zarówno drobne dodatki do funkcji, jak i zmiany istotne. Zobacz listy zmian zestawu SDK, aby uzyskać więcej informacji.
  * [C# SDK Preview 3 changelog (Zestaw SDK języka C# w wersji zapoznawczej 3)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 changelog (Zestaw SDK języka Python w wersji zapoznawczej 3)](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 changelog (Zestaw Sdk Java w wersji zapoznawczej 3)](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK Preview 3 changelog](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="new-features"></a>Nowe funkcje

* Obsługa zestawu SDK dla Rozpoznawanie formularzy API w wersji **2.0** w publicznej wersji zapoznawczej — w tym miesiącu rozszerzyliśmy obsługę naszej usługi o zestaw SDK w wersji zapoznawczej dla wersji Rozpoznawanie formularzy 2.0 (wersja zapoznawcza). Użyj poniższych linków, aby rozpocząć pracę z używanym językiem:
  * [Zestaw SDK platformy .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Zestaw SDK Java](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Zestaw SDK dla języka Python](/python/api/overview/azure/ai-formrecognizer-readme)
  * [Zestaw SDK dla języka JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Nowy zestaw SDK obsługuje wszystkie funkcje interfejsu API REST w wersji 2.0 dla Rozpoznawanie formularzy. Na przykład można wyttrenować model z etykietami lub bez niego oraz wyodrębnić tekst, pary klucz-wartość i tabele z formularzy, wyodrębnić dane z paragonów przy użyciu wstępnie sbudowaną usługę paragonów oraz wyodrębnić tekst i tabele za pomocą usługi układu z dokumentów. Swoją opinię na temat zestawów SDK możesz udostępnić za pomocą formularza [opinii o zestawie SDK.](https://aka.ms/FR_SDK_v1_feedback)

* **Kopiowanie modelu niestandardowego** Teraz możesz kopiować modele między regionami i subskrypcjami przy użyciu nowej funkcji Kopiowania modelu niestandardowego. Przed wywołaniem interfejsu API kopiowania modelu niestandardowego należy najpierw uzyskać autoryzację kopiowania do zasobu docelowego, wywołując operację autoryzacji kopiowania względem docelowego punktu końcowego zasobu.

  * [Generowanie autoryzacji kopiowania](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [Kopiowanie modelu niestandardowego](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>Ulepszenia zabezpieczeń

* Customer-Managed klucze są teraz dostępne dla formRecognizer. Aby uzyskać więcej informacji, zobacz Szyfrowanie danych [w spoczynku dla Rozpoznawanie formularzy](./encrypt-data-at-rest.md).
* Użyj tożsamości zarządzanych, aby uzyskać dostęp do zasobów platformy Azure za pomocą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Autoryzowanie dostępu do tożsamości zarządzanych.](../authentication.md#authorize-access-to-managed-identities)

## <a name="march-2020"></a>Marzec 2020 r.

### <a name="new-features"></a>Nowe funkcje

* **Typy wartości do etykietowania** Teraz możesz określić typy wartości, które są oznaczane etykietami, za pomocą Rozpoznawanie formularzy przykładowego narzędzia do etykietowania. Obecnie obsługiwane są następujące typy i wariacje wartości:
  * `string`
    * wartość domyślna, `no-whitespaces` , `alphanumeric`
  * `number`
    * Domyślny `currency`
  * `date`
    * wartość `dmy` domyślna, , `mdy` , `ymd`
  * `time`
  * `integer`

  Zobacz Przewodnik [po przykładowym narzędziu do etykietowania,](./quickstarts/label-tool.md#specify-tag-value-types) aby dowiedzieć się, jak korzystać z tej funkcji.


* **Wizualizacja tabeli** Przykładowe narzędzie do etykietowania wyświetla teraz tabele, które zostały rozpoznane w dokumencie. Ta funkcja umożliwia wyświetlanie tabel, które zostały rozpoznane i wyodrębnione z dokumentu, przed etykietowaniem i analizowaniem. Tę funkcję można włączyć/wyłączyć przy użyciu opcji warstwy.

  Na poniższej ilustracji przedstawiono przykład sposobu, w jaki tabele są rozpoznawane i wyodrębnione:

  > [!div class="mx-imgBorder"]
  > ![Wizualizacja tabeli przy użyciu przykładowego narzędzia do etykietowania](./media/whats-new/table-viz.png)

    Wyodrębnione tabele są dostępne w danych wyjściowych JSON w obszarze `"pageResults"` .

  > [!IMPORTANT]
  > Tabele etykietowania nie są obsługiwane. Jeśli tabele nie są rozpoznawane i ekstradowane automatycznie, można je oznaczać tylko jako pary klucz/wartość. W przypadku oznaczania tabel jako par klucz/wartość należy oznaczać każdą komórkę jako unikatową wartość.

### <a name="extraction-enhancements"></a>Ulepszenia wyodrębniania

Ta wersja zawiera ulepszenia wyodrębniania i ulepszenia dokładności, w szczególności możliwość etykietowania i wyodrębniania wielu par klucz/wartość w tym samym wierszu tekstu.

### <a name="sample-labeling-tool-is-now-open-source"></a>Przykładowe narzędzie do etykietowania jest teraz typu open source

Przykładowe Rozpoznawanie formularzy etykietowania jest teraz dostępne jako projekt open source. Możesz zintegrować ją z rozwiązaniami i wprowadzać zmiany specyficzne dla klientów, aby spełniały Twoje potrzeby.

Aby uzyskać więcej informacji na temat Rozpoznawanie formularzy przykładowego narzędzia do etykietowania, zapoznaj się z dokumentacją dostępną w witrynie [GitHub.](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)

### <a name="tls-12-enforcement"></a>Wymuszanie protokołu TLS 1.2

Protokół TLS 1.2 jest teraz wymuszany dla wszystkich żądań HTTP do tej usługi. Aby uzyskać więcej informacji, [zobacz Azure Cognitive Services zabezpieczeń](../cognitive-services-security.md).

## <a name="january-2020"></a>Styczeń 2020 r.

W tej wersji wprowadzono Rozpoznawanie formularzy 2.0 (wersja zapoznawcza). W poniższych sekcjach znajdziesz więcej informacji o nowych funkcjach, ulepszeniach i zmianach.

### <a name="new-features"></a>Nowe funkcje

* **Model niestandardowy**
  * **Trenuj przy użyciu etykiet** Teraz możesz trenować model niestandardowy przy użyciu danych ręcznie oznaczonych etykietami. Ta metoda skutkuje modelami o lepszej jakości i może tworzyć modele, które działają ze złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.
  * **Asynchroniczny interfejs API** Asynchronicznych wywołań interfejsu API można używać do trenowania i analizowania dużych zestawów danych i plików.
  * **Obsługa plików TIFF** Teraz możesz trenować przy użyciu dokumentów TIFF i wyodrębniać z tych dokumentów.
  * **Ulepszenia dokładności wyodrębniania**

* **Wstępnie utworzony model paragonu**
  * **Kwoty porad** Teraz możesz wyodrębnić kwoty porad i inne wartości odręczne.
  * **Wyodrębnianie elementów wiersza** Wartości elementów wiersza można wyodrębnić z paragonów.
  * **Wartości ufności** Możesz wyświetlić pewność modelu dla każdej wyodrębnionej wartości.
  * **Ulepszenia dokładności wyodrębniania**

* **Wyodrębnianie układu** Teraz możesz wyodrębnić dane tekstowe i dane tabeli z formularzy przy użyciu interfejsu API układu.

### <a name="custom-model-api-changes"></a>Zmiany interfejsu API modelu niestandardowego

Nazwy wszystkich interfejsów API do trenowania i używania modeli niestandardowych zostały zmienione, a niektóre metody synchroniczne są teraz asynchroniczne. Poniżej przedstawiono istotne zmiany:

* Proces trenowania modelu jest teraz asynchroniczny. Trenuj za pomocą wywołania interfejsu API **/custom/models.** To wywołanie zwraca identyfikator operacji, który można przekazać do elementu **custom/models/{modelID},** aby zwrócić wyniki trenowania.
* Wyodrębnianie klucza/wartości jest teraz inicjowane przez wywołanie interfejsu API **/custom/models/{modelID}/analyze.** To wywołanie zwraca identyfikator operacji, który można przekazać do elementu **custom/models/{modelID}/analyzeResults/{resultID},** aby zwrócić wyniki wyodrębniania.
* Identyfikatory operacji trenowania znajdują się teraz w nagłówku **Location** odpowiedzi HTTP, a nie **nagłówku Operation-Location.**

### <a name="receipt-api-changes"></a>Zmiany interfejsu API paragonu

Nazwy interfejsów API do odczytywania paragonów sprzedaży zostały zmienione.

* Wyodrębnianie danych paragonu jest teraz inicjowane przez wywołanie interfejsu API **/prebuilt/receipt/analyze.** To wywołanie zwraca identyfikator operacji, który można przekazać do elementu **/prebuilt/receipt/analyzeResults/{resultID},** aby zwrócić wyniki wyodrębniania.

### <a name="output-format-changes"></a>Zmiany formatu danych wyjściowych

Odpowiedzi JSON dla wszystkich wywołań interfejsu API mają nowe formaty. Niektóre klucze i wartości zostały dodane, usunięte lub zmienione. Zobacz przewodniki Szybki start, aby uzyskać przykłady bieżących formatów JSON.

## <a name="next-steps"></a>Następne kroki

Ukończ przewodnik [Szybki start,](quickstarts/client-library.md) aby rozpocząć pisanie aplikacji do przetwarzania formularzy z Rozpoznawanie formularzy w języku programowania.

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](./overview.md)