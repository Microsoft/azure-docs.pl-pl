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
ms.openlocfilehash: 7898fcf9b149f50f8478576d18cd3c3b984eb937
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85209100"
---
# <a name="whats-new-in-form-recognizer"></a>Co nowego w usłudze Rozpoznawanie formularzy?

Usługa aparat rozpoznawania formularzy jest regularnie aktualizowana. Skorzystaj z tego artykułu, aby uzyskać aktualne informacje dotyczące ulepszeń, poprawek i aktualizacji dokumentacji.

> [!NOTE]
> Przewodniki Szybki Start i wskazówki dla aparatu rozpoznawania formularzy zawsze używają najnowszej wersji interfejsu API, chyba że zostanie to określone.

## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="new-features"></a>Nowe funkcje
* **Interfejs API CopyModel został dodany do zestawów SDK klienta** Teraz można używać zestawów SDK klienta do kopiowania modeli z jednej subskrypcji do innej. Zobacz [Tworzenie kopii zapasowych i odzyskiwanie modeli,](./disaster-recovery.md) Aby uzyskać ogólne informacje dotyczące tej funkcji.
* **Integracja Azure Active Directory** Możesz teraz używać poświadczeń usługi AAD do uwierzytelniania obiektów klienta aparatu rozpoznawania formularzy w zestawach SDK.
* **Zmiany specyficzne dla zestawu SDK** Dotyczy to zarówno dodatkowych, jak i drobnych zmian funkcji. Aby uzyskać więcej informacji, zobacz dziennik zmian zestawu SDK.
  * [Podgląd zestawu C# SDK 3 — dziennik zmian](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Dziennik zmian zestawu SDK języka Python w wersji 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Podgląd zestawu Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Kod dziennika zmian zestawu JavaScript SDK Preview 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="new-features"></a>Nowe funkcje
* **Obsługa zestawu SDK interfejsu API rozpoznawania formularzy w wersji 2.0** W tym miesiącu rozszerzono obsługę usługi w celu uwzględnienia zestawu SDK w wersji zapoznawczej dla aparatu rozpoznawania dla programu format 2.0 (wersja zapoznawcza). Skorzystaj z poniższych linków, aby rozpocząć pracę z wybranym językiem: 
   * [Zestaw SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer)
   * [Zestaw SDK Java](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)
   * [Zestaw SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/formrecognizer)
   * [Zestaw SDK dla języka JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)

  Nowy zestaw SDK obsługuje wszystkie funkcje interfejsu API REST programu v 2.0 na potrzeby aparatu rozpoznawania formularzy. Na przykład można przeprowadzić uczenie modelu z etykietami lub bez nich oraz Wyodrębnianie tekstu, par klucz-wartość i tabel z formularzy, wyodrębnianie danych z przyjęć przy użyciu wstępnie utworzonych usług przyjęć oraz Wyodrębnianie tekstu i tabel z użyciem usługi układu z dokumentów. Swoją opinię na temat zestawów SDK możesz udostępnić za pomocą [formularza opinii zestawu SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Kopiuj model niestandardowy** Teraz można kopiować modele między regionami i subskrypcjami przy użyciu nowej funkcji kopiowania modelu niestandardowego. Przed wywołaniem interfejsu API kopiowania niestandardowych modeli należy najpierw uzyskać autoryzację do skopiowania do zasobu docelowego, wywołując operację kopiowania autoryzacji do docelowego punktu końcowego zasobu.
   * [Generuj autoryzację kopiowania](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/CopyCustomFormModelAuthorization) INTERFEJS API REST
   * [Kopiowanie modelu niestandardowego](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/CopyCustomFormModel) INTERFEJS API REST 

### <a name="security-improvements"></a>Ulepszenia zabezpieczeń

* Klucze zarządzane przez klienta są teraz dostępne dla FormRecognizer. Aby uzyskać więcej informacji, zobacz [szyfrowanie danych w usłudze REST dla aparatu rozpoznawania formularzy](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest).
* Korzystaj z tożsamości zarządzanych, aby uzyskiwać dostęp do zasobów platformy Azure za pomocą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do zarządzanych tożsamości](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Marzec 2020 r. 

### <a name="new-features"></a>Nowe funkcje

* **Typy wartości dla etykietowania** Teraz możesz określić typy wartości, które są oznaczone etykietami, za pomocą narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy. Następujące typy wartości i różnice są obecnie obsługiwane:
  * `string`
    * domyślne, `no-whitespaces` ,`alphanumeric`
  * `number`
    * wartooć`currency`
  * `date` 
    * domyślne, `dmy` , `mdy` ,`ymd`
  * `time`
  * `integer`

  Aby dowiedzieć się, jak korzystać z tej funkcji, zobacz Przewodnik po [przykładowym narzędziu do etykietowania](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Wizualizacja tabeli** Narzędzie przykładowe etykietowanie wyświetla teraz tabele, które zostały rozpoznane w dokumencie. Pozwala to wyświetlić tabele, które zostały rozpoznane i wyodrębnione z dokumentu przed oznaczeniem i analizą. Tę funkcję można włączyć/wyłączyć przy użyciu opcji warstwy.

  Oto przykład sposobu, w jaki tabele są rozpoznawane i wyodrębniane:

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
  * **Uczenie z etykietami** Teraz można nauczyć model niestandardowy z ręcznie oznaczonymi danymi. Prowadzi to do lepszego wykonywania modeli i może generować modele, które współpracują z złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.
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

Ukończ [Przewodnik Szybki Start](quickstarts/curl-train-extract.md) , aby rozpocząć pracę z [interfejsami API aparatu rozpoznawania formularzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm).