---
title: Utwórz magazyn wiedzy w Azure Portal
titleSuffix: Azure Cognitive Search
description: Za pomocą Kreatora importu danych można utworzyć magazyn wiedzy używany do utrwalania wzbogaconej zawartości. Nawiąż połączenie z magazynem wiedzy na potrzeby analizy z innych aplikacji lub Wyślij ulepszoną zawartość do procesów podrzędnych.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/17/2020
ms.openlocfilehash: 3225013f09abd326c619b67caf77918889a64859
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741811"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Szybki Start: Tworzenie sklepu z bazami danych Azure Wyszukiwanie poznawcze w Azure Portal

Magazyn wiedzy to funkcja Wyszukiwanie poznawcze platformy Azure, która utrzymuje dane wyjściowe potoku przetwarzania zawartości dla kolejnych analiz lub przetwarzania podrzędnego. 

Potok akceptuje niestrukturalną zawartość tekstu i obrazu, stosuje AI obsługiwane przez Cognitive Services (na przykład OCR i przetwarzanie języka naturalnego), a także wyprowadza nowe struktury i informacje, które jeszcze nie istniały. Jednym z artefaktów fizycznych utworzonych w potoku jest [Magazyn wiedzy](knowledge-store-concept-intro.md), do którego można uzyskać dostęp za pomocą narzędzi, aby analizować i eksplorować zawartość.

W tym przewodniku szybki start utworzysz usługi i dane w chmurze platformy Azure w celu utworzenia sklepu z bazami informacji. Gdy wszystko będzie na miejscu, uruchom kreatora **importowania danych** w portalu, aby ściągnąć wszystkie te elementy. Wynik końcowy to oryginalna zawartość tekstowa oraz zawartość wygenerowana przez AI, którą można wyświetlić w portalu ([Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md)).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ Konto usługi Azure Storage z [magazynem obiektów BLOB](../storage/blobs/index.yml).

> [!NOTE]
> Ten przewodnik Szybki Start używa również [usługi Azure Cognitive Services dla systemu](https://azure.microsoft.com/services/cognitive-services/) AI. Ze względu na to, że obciążenie jest małe, Cognitive Services jest wybierana w tle, aby można było bezpłatnie przetwarzać do 20 transakcji. Oznacza to, że można wykonać to ćwiczenie bez konieczności tworzenia dodatkowego zasobu Cognitive Services.

## <a name="set-up-your-data"></a>Skonfiguruj dane

W poniższych krokach skonfiguruj kontener obiektów BLOB w usłudze Azure Storage do przechowywania plików zawartości heterogenicznej.

1. [Pobierz HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Te dane to dane z przeglądu hotelu zapisane w pliku CSV (pochodzące z Kaggle.com) i zawierają 19 opinii klientów na temat pojedynczego hotelu. 

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) lub [Znajdź istniejące konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) w ramach bieżącej subskrypcji. Będziesz używać usługi Azure Storage do importowania nieprzetworzonej zawartości oraz magazynu wiedzy, który jest wynikiem końcowym.

   + Wybierz typ konta **StorageV2 (ogólnego przeznaczenia w wersji 2)** .

1. Otwórz strony usługi BLOB Services i Utwórz kontener o nazwie *"przeglądy hotelowe"*.

1. Kliknij pozycję **Przekaż**.

    ![Przekaż dane](media/knowledge-store-create-portal/upload-command-bar.png "Przekaż przeglądy hotelu")

1. Wybierz plik **HotelReviews-Free.csv** pobrany w pierwszym kroku.

    ![Tworzenie kontenera obiektów blob platformy Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Tworzenie kontenera obiektów blob platformy Azure")

1. Przed zamknięciem stron magazynu obiektów BLOB Użyj linku w okienku nawigacji po lewej stronie, aby otworzyć stronę **klawisze dostępu** . Pobierz parametry połączenia, aby pobrać dane z magazynu obiektów BLOB. Parametry połączenia wyglądają podobnie do poniższego przykładu: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Teraz można przystąpić do przenoszenia kreatora **importu danych** .

## <a name="run-the-import-data-wizard"></a>Uruchom Kreatora importowania danych

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

1. [Znajdź usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) i na stronie Przegląd kliknij pozycję **Importuj dane** na pasku poleceń, aby utworzyć magazyn wiedzy w czterech krokach.

   ![Polecenie importu danych](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

1. W obszarze **Nawiązywanie połączenia z danymi** wybierz opcję **Magazyn obiektów blob Azure**, a następnie wybierz konto i utworzony kontener. 

1. Wpisz **nazwę** `hotel-reviews-ds` .

1. W obszarze **tryb analizy** wybierz pozycję **Tekst rozdzielany**, a następnie zaznacz pole wyboru **pierwszy wiersz zawiera nagłówek** . Upewnij się, że **znak ogranicznika** jest przecinkiem (,).

1. W polu **Parametry połączenia** wklej parametry połączenia skopiowane ze strony **klucze dostępu** w usłudze Azure Storage.

1. W polu **kontenery** wprowadź nazwę kontenera obiektów BLOB przechowującego dane.

    Strona powinna wyglądać podobnie do poniższego zrzutu ekranu.

    ![Tworzenie obiektu źródła danych](media/knowledge-store-create-portal/hotel-reviews-ds.png "Tworzenie obiektu źródła danych")

1. Przejdź do następnej strony.

### <a name="step-2-add-cognitive-skills"></a>Krok 2. Dodawanie umiejętności poznawczych

W tym kroku kreatora utworzysz zestawu umiejętności z wzbogacaniem umiejętności poznawczych. Dane źródłowe składają się z przeglądów klienta w kilku językach. Umiejętności, które są istotne dla tego zestawu danych, obejmują wyodrębnianie kluczowych fraz i wykrywanie tonacji oraz tłumaczenie tekstu. W późniejszym kroku te wzbogacania będą "zorganizowane" w sklepie z bazami danych jako tabele platformy Azure.

1. Rozwiń węzeł **Attach Cognitive Services**. Wartość **bezpłatna (ograniczone wzbogacania)** jest domyślnie zaznaczona. Możesz użyć tego zasobu, ponieważ liczba rekordów w HotelReviews-Free.csv to 19, a ten bezpłatny zasób umożliwia maksymalnie 20 transakcji dziennie.

1. Rozwiń pozycję **Dodaj wzbogacania**.

1. W obszarze **Nazwa zestawu umiejętności** wprowadź `hotel-reviews-ss` .

1. W **polu Źródło danych** wybierz pozycję **reviews_text**.

1. Aby uzyskać **poziom szczegółowości wzbogacenia**, wybierz pozycję **strony (fragmenty: 5000 znaków).**

1. Wybierz następujące umiejętności poznawcze:
    + **Wyodrębnij kluczowe frazy**
    + **Tłumaczenie tekstu**
    + **Wykrywanie tonacji**

      ![Tworzenie zestawu umiejętności](media/knowledge-store-create-portal/hotel-reviews-ss.png "Tworzenie zestawu umiejętności")

1. Rozwiń pozycję **Zapisz wzbogacanie do sklepu merytorycznego**.

1. Wybierz następujące **projekcje tabeli platformy Azure**:
    + **Dokumenty**
    + **Strony**
    + **Kluczowe frazy**

1. Wprowadź **Parametry połączenia konta magazynu** zapisane w poprzednim kroku.

    ![Konfigurowanie sklepu merytorycznego](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurowanie sklepu merytorycznego")

1. Opcjonalnie Pobierz szablon Power BI. Gdy uzyskujesz dostęp do szablonu za pomocą kreatora, plik Local. Pbit jest dostosowywany do odzwierciedlenia kształtu danych.

1. Przejdź do następnej strony.

### <a name="step-3-configure-the-index"></a>Krok 3. Konfigurowanie indeksu

W tym kroku kreatora skonfigurujesz indeks opcjonalnych zapytań wyszukiwania pełnotekstowego. Kreator posłuży do próbkowania źródła danych w celu wywnioskowania pól i typów danych. Musisz tylko wybrać atrybuty żądanego zachowania. Na przykład atrybut możliwy do **pobierania** zezwoli usłudze wyszukiwania na zwrócenie wartości pola, podczas gdy **przeszukiwanie** spowoduje włączenie wyszukiwania pełnotekstowego w polu.

1. W obszarze **Nazwa indeksu** wprowadź `hotel-reviews-idx` .

1. W przypadku atrybutów Zaakceptuj wybór **domyślny: pobieranie** i **Wyszukiwanie** nowych pól tworzonych przez potok.

    Indeks powinien wyglądać podobnie do poniższej ilustracji. Ponieważ lista jest długa, nie wszystkie pola są widoczne na obrazie.

    ![Skonfiguruj indeks](media/knowledge-store-create-portal/hotel-reviews-idx.png "Skonfiguruj indeks")

1. Przejdź do następnej strony.

### <a name="step-4-configure-the-indexer"></a>Krok 4. Konfigurowanie indeksatora

W tym kroku kreatora skonfigurujesz indeksator, który będzie ściągał źródło danych, zestawu umiejętności i indeks zdefiniowany w poprzednich krokach kreatora.

1. W obszarze **Nazwa** wprowadź `hotel-reviews-idxr` .

1. W polu **harmonogram** Zachowaj wartość domyślną **jeden raz**.

1. Kliknij przycisk **Prześlij** , aby uruchomić indeksator. W tym kroku jest wyodrębnianie danych, indeksowanie i stosowanie umiejętności poznawczych.

## <a name="monitor-status"></a>Monitorowanie stanu

Indeksowanie umiejętności poznawcze trwa dłużej niż typowe indeksowanie tekstowe. Kreator powinien otworzyć listę Indeksator na stronie przeglądu, co pozwala na śledzenie postępu. W przypadku samodzielnej nawigacji przejdź do strony Przegląd, a następnie kliknij przycisk **Indeksatory**.

W Azure Portal można także monitorować dziennik aktywności powiadomień dla łącza stanu **powiadomień wyszukiwanie poznawcze platformy Azure** . Wykonanie może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dane zostały wzbogacone przy użyciu Cognitive Services i zostały przedstawione wyniki w sklepie z bazami danych, można użyć Eksplorator usługi Storage lub Power BI do eksplorowania wzbogaconego zestawu.

Możesz wyświetlić zawartość w Eksplorator usługi Storage lub zawęzić krok z Power BI, aby uzyskać wgląd w dane za pomocą wizualizacji.

> [!div class="nextstepaction"]
> [Wyświetl z Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md) 
>  [Połącz z Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Jeśli chcesz powtórzyć to ćwiczenie lub spróbować użyć innego instruktażu do wzbogacania, Usuń usługę *Hotel-Recenzje-idxr* indeksator. Usunięcie indeksatora resetuje bezpłatny dzienny licznik transakcji do zera na potrzeby przetwarzania Cognitive Services.
