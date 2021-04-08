---
title: 'Samouczek: używanie sesji debugowania do naprawy umiejętności'
titleSuffix: Azure Cognitive Search
description: Sesje debugowania (wersja zapoznawcza) to narzędzie Azure Portal używane do znajdowania, diagnozowania i naprawiania problemów w zestawu umiejętności.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509153"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Samouczek: debugowanie zestawu umiejętności przy użyciu sesji debugowania

Umiejętności koordynuje serię akcji, które analizują lub przekształcają zawartość, gdzie dane wyjściowe jednej umiejętności staną się danymi wejściowymi innego. Gdy dane wejściowe są zależne od danych wyjściowych, błędy w definicjach zestawu umiejętności i skojarzeniach pól mogą spowodować nieodebrane operacje i dane.

**Sesje debugowania** w Azure Portal udostępniają całościową wizualizację zestawu umiejętności. Korzystając z tego narzędzia, możesz przejść do szczegółów określonych kroków, aby łatwo zobaczyć, gdzie może zostać wyświetlona akcja.

W tym artykule opisano, jak używać **sesji debugowania** do znajdowania i naprawienia 1) brakujących danych wejściowych i 2) mapowań pól wyjściowych. Samouczek to wszystko — włącznie. Zawiera dane do indeksowania (dane badań klinicznych), kolekcji programu Poster, która tworzy obiekty, oraz instrukcje dotyczące korzystania z **sesji debugowania** do znajdowania i rozwiązywania problemów w zestawu umiejętności.

> [!Important]
> Sesje debugowania to funkcja w wersji zapoznawczej, która jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy mieć na miejsce następujące wymagania wstępne:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ Konto usługi Azure Storage z [magazynem obiektów BLOB](../storage/blobs/index.yml), służące do hostowania przykładowych danych i utrwalania danych tymczasowych utworzonych podczas sesji debugowania.

+ [Publikowanie aplikacji klasycznych](https://www.getpostman.com/) i [kolekcji programu Poster](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) w celu tworzenia obiektów przy użyciu interfejsów API REST.

+ [Przykładowe dane (próby kliniczne)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Ten przewodnik Szybki Start używa również [usługi Azure Cognitive Services dla systemu](https://azure.microsoft.com/services/cognitive-services/) AI. Ze względu na to, że obciążenie jest małe, Cognitive Services jest wybierana w tle, aby można było bezpłatnie przetwarzać do 20 transakcji. Oznacza to, że można wykonać to ćwiczenie bez konieczności tworzenia dodatkowego zasobu Cognitive Services.

## <a name="set-up-your-data"></a>Skonfiguruj dane

W tej sekcji jest tworzony przykładowy zestaw danych w usłudze Azure Blob Storage, dzięki czemu indeksator i zestawu umiejętności mają zawartość do współpracy.

1. [Pobierz przykładowe dane (badania kliniczne-PDF-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19)składające się z 19 plików.

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) lub [Znajdź istniejące konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Wybierz ten sam region co usługa Azure Wyszukiwanie poznawcze, aby uniknąć naliczania opłat za przepustowość.

   + Wybierz typ konta StorageV2 (ogólnego przeznaczenia w wersji 2).

1. Przejdź do stron usługi Azure Storage w portalu i Utwórz kontener obiektów BLOB. Najlepszym rozwiązaniem jest określenie poziomu dostępu "Private". Nazwij kontener `clinicaltrialdataset` .

1. W kontenerze kliknij pozycję **Przekaż** , aby przekazać pliki przykładowe pobrane i rozpakowane w pierwszym kroku.

1. W portalu Pobierz i Zapisz parametry połączenia dla usługi Azure Storage. Będzie ona potrzebna dla wywołań interfejsu API REST, które mają dane indeksu. Parametry połączenia można uzyskać z **ustawień**  >  **kluczy dostępu** w portalu.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Pobieranie punktu końcowego HTTP i klucza dostępu" border="false":::

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="create-data-source-skillset-index-and-indexer"></a>Tworzenie źródła danych, zestawu umiejętności, indeksu i indeksatora

W tej sekcji, program ogłaszający i poświadczona kolekcja są używane do tworzenia Wyszukiwanie poznawcze źródła danych, indeksu i indeksatora. Jeśli nie znasz programu Poster, zapoznaj się z [tym przewodnikiem Szybki Start](search-get-started-rest.md).

Do wykonania tego zadania będzie potrzebna [Kolekcja ogłoszeń](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) utworzona dla tego samouczka. 

1. Uruchom notkę i zaimportuj kolekcję. W obszarze **pliki**  >  **nowe** wybierz kolekcję do zaimportowania.
1. Po zaimportowaniu kolekcji rozwiń listę akcje (...).
1. Kliknij pozycję **Edytuj**.
1. W obszarze bieżąca wartość wprowadź nazwę `searchService` (na przykład, jeśli punkt końcowy to `https://mydemo.search.windows.net` , nazwa usługi to " `mydemo` ").
1. Wprowadź wartość `apiKey` przy użyciu klucza podstawowego lub pomocniczego usługi wyszukiwania.
1. Wprowadź wartość `storageConnectionString` ze strony klucze na koncie usługi Azure Storage.
1. Wprowadź wartość `containerName` dla kontenera utworzonego na koncie magazynu, a następnie kliknij przycisk **Aktualizuj**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Edytuj zmienne w programie Poster":::

Kolekcja zawiera cztery różne wywołania REST, które są używane do tworzenia obiektów używanych w tym samouczku.

Pierwsze wywołanie tworzy źródło danych. `clinical-trials-ds`. Drugie wywołanie tworzy zestawu umiejętności, `clinical-trials-ss` . Trzecie wywołanie tworzy indeks, `clinical-trials` . Połączenie czwarte i końcowe tworzy indeksator, `clinical-trials-idxr` .

+ Otwórz każde żądanie z kolei, a następnie kliknij przycisk **Wyślij** , aby wysłać każde żądanie do usługi wyszukiwania. 

Po zakończeniu wszystkich wywołań w kolekcji Zamknij program Poster i wróć do Azure Portal.

## <a name="check-results-in-the-portal"></a>Sprawdź wyniki w portalu

Przykładowy kod celowo tworzy indeks debugowania jako przyczynę problemów, które wystąpiły podczas wykonywania zestawu umiejętności. Wystąpił problem z brakującymi danymi. 

1. W Azure Portal na stronie Przegląd usługi wyszukiwania wybierz kartę **indeksy** . 
1. Wybierz `clinical-trials` indeks.
1. Wprowadź ten ciąg zapytania: `$select=metadata_storage_path, organizations, locations&$count=true` Aby zwrócić pola dla określonych dokumentów (identyfikowanych przez `metadata_storage_path` pole unikatowe).
1. Kliknij przycisk **Wyszukaj** , aby uruchomić zapytanie, zwracając wszystkie 19 dokumentów, wyświetlając puste wartości dla "organizacje" i "lokalizacje".

Pola te powinny zostać wypełnione przez [umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md)zestawu umiejętności, używane do znajdowania organizacji i lokalizacji w dowolnym miejscu w zawartości obiektu BLOB. W następnym ćwiczeniu sesja debugowania zostanie użyta do określenia, co poszło źle.

Innym sposobem badania błędów i ostrzeżeń jest użycie Azure Portal.

1. Otwórz kartę **indeksatory** i wybierz pozycję `clinical-trials-idxr` .
1. Zwróć uwagę, że gdy zadanie indeksatora zakończyło się pomyślnie, wystąpiły 57 ostrzeżeń.
1. Kliknij pozycję **sukces** , aby wyświetlić ostrzeżenia (jeśli wystąpiły błędy w większości błędów), link szczegółowy nie powiedzie **się**. Zobaczysz długą listę wszystkich ostrzeżeń wyemitowanych przez indeksator.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="Wyświetl ostrzeżenia":::

## <a name="start-your-debug-session"></a>Rozpocznij sesję debugowania

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Rozpocznij nową sesję debugowania":::

1. Na stronie Przegląd wyszukiwania kliknij kartę **sesje debugowania** .
1. Wybierz pozycję **+ Nowa sesja debugowania**.
1. Nadaj nazwę sesji. 
1. Połącz sesję z kontem magazynu. 
1. W szablonie indeksatora Podaj nazwę indeksatora. Indeksator zawiera odwołania do źródła danych, zestawu umiejętności i index.
1. Zaakceptuj wybór dokumentu domyślnego dla pierwszego dokumentu w kolekcji. 
1. **Zapisz** sesję. Zapisanie sesji spowoduje uruchomienie potoku wzbogacenia AI zgodnie z definicją zestawu umiejętności.

> [!Important]
> Sesja debugowania działa tylko z jednym dokumentem. Możesz wybrać dokument do debugowania lub po prostu użyć pierwszego z nich.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Po zainicjowaniu sesji debugowania jest ona domyślnie ustawiana na kartę **wzbogacanie AI** , wyróżnianie **grafu umiejętności**. Wykres umiejętności zawiera wizualną hierarchię zestawu umiejętności i jej kolejność wykonywania sekwencyjnie i równolegle.

## <a name="find-issues-with-the-skillset"></a>Znajdź problemy z zestawu umiejętności

Wszystkie problemy zgłaszane przez indeksatora można znaleźć na karcie sąsiadujące **Błędy/ostrzeżenia** . 

Zauważ, że karta **Błędy/ostrzeżenia** dostarczy znacznie mniejszej listy niż ta, która jest wyświetlana wcześniej, ponieważ ta lista zawiera tylko szczegóły błędów dla pojedynczego dokumentu. Podobnie jak lista wyświetlana przez indeksator, można kliknąć komunikat ostrzegawczy i wyświetlić szczegóły tego ostrzeżenia.

Wybierz pozycje **Błędy/ostrzeżenia** , aby przejrzeć powiadomienia. Powinna zostać wyświetlona trzy:

   + "Nie można zmapować pola danych wyjściowych" Locations "na indeks wyszukiwania. Sprawdź Właściwość "outputFieldMappings" indeksatora.
Brak wartości "/Document/merged_content/Locations". "

   + "Nie można zmapować pola danych wyjściowych" Organizations "na indeks wyszukiwania. Sprawdź Właściwość "outputFieldMappings" indeksatora.
Brak wartości "/Document/merged_content/Organizations". "

   + "Umiejętność została wykonana, ale może mieć nieoczekiwane wyniki, ponieważ co najmniej jedno dane wejściowe kwalifikacji były nieprawidłowe.
Brak opcjonalnego danych wejściowych umiejętności. Name: "languageCode", source: "/document/languageCode". Problemy z analizowaniem języka wyrażeń: brak wartości "/document/languageCode". "

   Wiele umiejętności ma parametr "languageCode". Sprawdzając operację, można zobaczyć, że w programie nie ma tego danych wejściowych kodu języka `Enrichment.NerSkillV2.#1` , który jest tą samą umiejętnością rozpoznawania jednostek, która ma problemy z danymi wyjściowymi "Locations" i "Organizations". 

Ponieważ wszystkie trzy powiadomienia dotyczą tej umiejętności, następnym krokiem jest debugowanie tej umiejętności. Jeśli to możliwe, najpierw Rozpocznij od rozwiązywania problemów wejściowych przed przejściem do outputFieldMapping problemów.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Rozpoczęto nową sesję debugowania":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Popraw brakującą wartość wejściową kwalifikacji

Na karcie **Błędy/ostrzeżenia** występuje błąd dla operacji oznaczonej etykietą `Enrichment.NerSkillV2.#1` . Szczegóły tego błędu wyjaśniają, że występuje problem z wartością wejściową "/document/languageCode". 

1. Wróć do karty **wzbogacania AI** .
1. Kliknij **Wykres umiejętności**.
1. Kliknij umiejętność z etykietą **#1** , aby wyświetlić jej szczegóły w okienku po prawej stronie.
1. Znajdź dane wejściowe dla "languageCode".
1. Wybierz **</>** symbol na początku wiersza i Otwórz ewaluatora wyrażeń.
1. Kliknij przycisk **Oceń** , aby potwierdzić, że to wyrażenie powoduje wystąpienie błędu. Upewnij się, że właściwość "languageCode" nie jest prawidłowym danymi wejściowymi.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Ewaluator wyrażeń":::

Istnieją dwa sposoby badania tego błędu w sesji. Pierwszy polega na tym, że dane wejściowe pochodzą z-jakie umiejętności w hierarchii powinny zostać wygenerowane? Na karcie wykonania w okienku Szczegóły umiejętności powinna zostać wyświetlona wartość źródła danych wejściowych. Jeśli nie ma źródła, oznacza to błąd mapowania pola.

1. Kliknij kartę **wykonania** .
1. Spójrz na dane wejściowe i Znajdź "languageCode". Brak źródła dla tego danych wejściowych wymienionych. 
1. Przełącz okienko po lewej stronie, aby wyświetlić ulepszoną strukturę danych. Nie ma zmapowanej ścieżki odpowiadającej elementowi "languageCode".

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Ulepszona struktura danych":::

Istnieje zmapowana ścieżka dla "języka". W związku z tym w ustawieniach umiejętności występuje literówka. Aby naprawić to wyrażenie w #1ej, należy zaktualizować wyrażenie "/Document/Language".

1. Otwórz ewaluatora wyrażeń **</>** dla ścieżki "Language" (język).
1. Skopiuj wyrażenie. Zamknij okno.
1. Przejdź do ustawień umiejętności #1 i Otwórz ewaluatora wyrażeń **</>** dla danych wejściowych "languageCode".
1. Wklej nową wartość "/Document/Language" w polu wyrażenie i kliknij pozycję **Oceń**.
1. Powinien on zawierać poprawne dane wejściowe "pl". Kliknij przycisk Zastosuj, aby zaktualizować wyrażenie.
1. Kliknij przycisk **Zapisz** w prawym okienku szczegółów umiejętności.
1. W menu okna sesji kliknij polecenie **Uruchom** . Spowoduje to uruchomienie innego wykonywania zestawu umiejętności przy użyciu dokumentu. 

Po zakończeniu wykonywania sesji debugowania kliknij kartę błędy/ostrzeżenia, aby zobaczyć, że wystąpił błąd z etykietą "wzbogacanie. NerSkillV2. #1". Jednak nadal istnieją dwa ostrzeżenia, które usługa nie może zmapować pól wyjściowych dla organizacji i lokalizacji do indeksu wyszukiwania. Brak wartości: "/Document/merged_content/Organizations" i "/Document/merged_content/Locations".

## <a name="fix-missing-skill-output-values"></a>Popraw brakujące wartości wyjściowe kwalifikacji

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Błędy i ostrzeżenia":::

Brak wartości wyjściowych z umiejętności. Aby zidentyfikować umiejętność z błędem, przejdź do wzbogaconej struktury danych, Znajdź nazwę wartości i sprawdź jej pierwotne źródło. W przypadku brakujących wartości organizacji i lokalizacji są one wyprowadzane z #1 umiejętności. Otwarcie <ewaluatora wyrażeń/> dla każdej ścieżki spowoduje wyświetlenie odpowiednio wyrażeń wymienionych jako "/Document/Content/Organizations" i "/Document/Content/Locations".

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Jednostka organizacji ewaluatora wyrażeń":::

Dane wyjściowe dla tych jednostek są puste i nie powinny być puste. Jakie są dane wejściowe tego wyniku?

1. Przejdź do **wykresu umiejętności** i wybierz pozycję kwalifikacje #1.
1. Wybierz kartę **wykonania** w prawym okienku szczegółów umiejętności.
1. Otwórz ewaluatora wyrażeń **</>** dla danych wejściowych "text".

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Dane wejściowe dotyczące kwalifikacji tekstu":::

Wyświetlany wynik dla tego danych wejściowych nie wygląda jak tekst wejściowy. Wygląda podobnie do obrazu, który jest ujęty w nowe wiersze. Brak tekstu oznacza, że nie można zidentyfikować żadnych jednostek. Spojrzenie na hierarchię zestawu umiejętności wyświetla zawartość, która jest najpierw przetwarzana przez umiejętność #6 (OCR), a następnie przenoszona do umiejętności #5 (merge). 

1. Wybierz umiejętność #5 (merge) na **grafie umiejętności**.
1. Wybierz kartę **wykonania** w prawym okienku szczegółów umiejętności i Otwórz ewaluatora wyrażeń **</>** dla danych wyjściowych "mergedText".

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Dane wyjściowe dla umiejętności scalania":::

W tym miejscu tekst jest sparowany z obrazem. Spojrzenie na wyrażenie "/Document/merged_content" powoduje błąd w ścieżkach "Organizations" i "Locations" dla #1 umiejętności. Zamiast używać elementu "/Document/Content", powinien on używać elementu "/Document/merged_content" dla danych wejściowych "text".

1. Skopiuj wyrażenie dla danych wyjściowych "mergedText" i Zamknij okno ewaluatora wyrażeń.
1. Wybierz pozycję umiejętność #1 na **grafie umiejętności**.
1. Wybierz kartę **Ustawienia umiejętności** w prawym okienku szczegółów umiejętności.
1. Otwórz ewaluatora wyrażeń **</>** dla danych wejściowych "text".
1. Wklej nowe wyrażenie do pola. Kliknij pozycję **Oceń**.
1. Należy wyświetlić poprawne dane wejściowe z dodanym tekstem. Kliknij przycisk **Zastosuj** , aby zaktualizować ustawienia umiejętności.
1. Kliknij przycisk **Zapisz** w prawym okienku szczegółów umiejętności.
1. W menu okna sesje kliknij polecenie **Uruchom** . Spowoduje to uruchomienie innego wykonywania zestawu umiejętności przy użyciu dokumentu.

Po zakończeniu działania indeksatora te błędy nadal są dostępne. Wróć do umiejętności #1 i zbadaj. Dane wejściowe dotyczące umiejętności zostały poprawione na "merged_content" z "Content". Jakie są dane wyjściowe tych jednostek w ramach umiejętności?

1. Wybierz kartę **wzbogacanie AI** .
1. Wybierz **Wykres umiejętności** i kliknij #1 umiejętności.
1. Przejdź do **ustawień umiejętności** , aby znaleźć "dane wyjściowe".
1. Otwórz ewaluatora wyrażeń **</>** dla jednostki "organizacje".

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Dane wyjściowe dla jednostki organizacji":::

Obliczenie wyniku wyrażenia daje prawidłowy wynik. Umiejętność działania umożliwia zidentyfikowanie poprawnej wartości dla jednostki "organizacje". Jednak mapowanie danych wyjściowych w ścieżce jednostki nadal zgłasza błąd. W porównaniu ze ścieżką wyjściową w polu umiejętność do ścieżki wyjściowej błędu, umiejętność nadrzędna danych wyjściowych, organizacji i lokalizacji w węźle/Document/Content. Chociaż mapowanie pola wyjściowego oczekuje, że wyniki mają być nadrzędne w węźle merged_content/Document/. W poprzednim kroku dane wejściowe zmieniły się z "/Document/Content" na "/Document/merged_content". Kontekst w ustawieniach umiejętności należy zmienić, aby upewnić się, że dane wyjściowe są generowane z odpowiednim kontekstem.

1. Wybierz kartę **wzbogacanie AI** .
1. Wybierz **Wykres umiejętności** i kliknij #1 umiejętności.
1. Przejdź do **ustawień umiejętności** , aby znaleźć "kontekst".
1. Kliknij dwukrotnie ustawienie "context" i edytuj je, aby odczytać "/Document/merged_content".
1. Kliknij przycisk **Zapisz** w prawym okienku szczegółów umiejętności.
1. W menu okna sesje kliknij polecenie **Uruchom** . Spowoduje to uruchomienie innego wykonywania zestawu umiejętności przy użyciu dokumentu.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Korekta kontekstu w ustawieniu umiejętności":::

Wszystkie błędy zostały rozwiązane.

## <a name="commit-changes-to-the-skillset"></a>Zatwierdź zmiany w zestawu umiejętności

Po zainicjowaniu sesji debugowania usługa wyszukiwania utworzyła kopię zestawu umiejętności. Zostało to zrobione w celu ochrony oryginalnego zestawu umiejętności w usłudze wyszukiwania. Po zakończeniu debugowania zestawu umiejętności można zatwierdzić poprawki (zastąpić oryginalny zestawu umiejętności). 

Alternatywnie, jeśli nie jesteś gotowy do zatwierdzania zmian, możesz zapisać sesję debugowania i otworzyć ją ponownie później.

1. Kliknij pozycję **Zatwierdź zmiany** w głównym menu sesji debugowania.
1. Kliknij przycisk **OK** , aby potwierdzić, że chcesz zaktualizować zestawu umiejętności.
1. Zamknij sesję debugowania i wybierz kartę **indeksatory** .
1. Otwórz "badania kliniczne-idxr".
1. Kliknij przycisk **Resetuj**.
1. Kliknij przycisk **Uruchom**. Kliknij przycisk **OK** , aby potwierdzić.

Gdy indeksator zakończył działanie, powinien istnieć zielony znacznik wyboru, a słowo "powodzenie" obok sygnatury czasowej dla ostatniego uruchomienia na karcie **historia wykonywania** . Aby upewnić się, że zmiany zostały zastosowane:

1. Na stronie Przegląd wyszukiwania wybierz kartę **indeks** .
1. Otwórz indeks "badania kliniczne", a następnie na karcie Eksplorator wyszukiwania wprowadź następujący ciąg zapytania: `$select=metadata_storage_path, organizations, locations&$count=true` Aby zwrócić pola dla określonych dokumentów (identyfikowanych przez pole unikatowe `metadata_storage_path` ).
1. Kliknij przycisk **Wyszukaj**.

Wyniki powinny wskazywać, że organizacje i lokalizacje są teraz wypełniane oczekiwanymi wartościami.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Ten samouczek dotykał różnych aspektów definicji i przetwarzania zestawu umiejętności. Aby dowiedzieć się więcej na temat pojęć i przepływów pracy, zapoznaj się z następującymi artykułami:

+ [Jak mapować pola danych wyjściowych zestawu umiejętności do pól w indeksie wyszukiwania](cognitive-search-output-field-mapping.md)

+ [Umiejętności na platformie Azure Wyszukiwanie poznawcze](cognitive-search-working-with-skillsets.md)

+ [Jak skonfigurować buforowanie na potrzeby wzbogacania przyrostowego](cognitive-search-incremental-indexing-conceptual.md)