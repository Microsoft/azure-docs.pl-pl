---
title: Nawiązywanie połączenia z magazynem wiedzy przy użyciu Power BI
titleSuffix: Azure Cognitive Search
description: Połącz magazyn wiedzy platformy Azure Wyszukiwanie poznawcze z Power BI do analizy i eksploracji.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89289175"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Łączenie ze sklepem wiedzy Power BI

W tym artykule dowiesz się, jak nawiązać połączenie z magazynem wiedzy i poznać go przy użyciu Power Query w aplikacji Power BI Desktop. Możesz szybko zacząć korzystać z szablonów lub utworzyć niestandardowy pulpit nawigacyjny od podstaw. Ten krótki film wideo pokazuje, jak można wzbogacić środowisko pracy z danymi za pomocą usługi Azure Wyszukiwanie poznawcze w połączeniu z Power BI.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ Wykonaj kroki opisane w temacie [Tworzenie sklepu z bazami danych w Azure Portal](knowledge-store-create-portal.md) lub [Utwórz magazyn wiedzy Azure wyszukiwanie poznawcze przy użyciu opcji REST](knowledge-store-create-rest.md) , aby utworzyć przykładowy magazyn wiedzy użyty w tym instruktażu. Potrzebna będzie również nazwa konta usługi Azure Storage, które zostało użyte do utworzenia magazynu wiedzy, wraz z jego kluczem dostępu z poziomu Azure Portal.

+ [Instalowanie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Przykładowy szablon Power BI Azure Portal tylko

Podczas tworzenia [sklepu z bazami danych przy użyciu Azure Portal](knowledge-store-create-portal.md)można pobrać [szablon Power BI](https://github.com/Azure-Samples/cognitive-search-templates) na drugiej stronie kreatora **importu danych** . Ten szablon udostępnia kilka wizualizacji, takich jak WordCloud i Nawigator sieci, dla zawartości tekstowej. 

Kliknij pozycję **Pobierz szablon Power BI** na stronie **Dodaj umiejętności poznawcze** , aby pobrać i pobrać szablon z publicznej lokalizacji w serwisie GitHub. Kreator modyfikuje szablon w celu dopasowania go do kształtu danych, tak jak to zostało przechwycone w projekcjach magazynu wiedzy określonych w kreatorze. Z tego powodu pobrany szablon będzie się różnić przy każdym uruchomieniu kreatora, przy założeniu, że dane wejściowe są różne i wybrane umiejętności.

![Przykładowy szablon Power BI Wyszukiwanie poznawcze platformy Azure](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Przykładowy szablon Power BI")

> [!NOTE]
> Mimo że szablon jest pobierany, gdy Kreator jest w połowie lotu, musisz poczekać, aż magazyn wiedzy zostanie utworzony w usłudze Azure Table Storage, zanim będzie można go używać.

## <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

1. Rozpocznij Power BI Desktop a następnie kliknij pozycję **Pobierz dane**.

1. W oknie **pobieranie danych** wybierz pozycję **Azure**, a następnie wybierz pozycję **Azure Table Storage**.

1. Kliknij przycisk **Połącz**.

1. W polu **nazwa konta lub adres URL** wprowadź nazwę konta usługi Azure Storage (pełny adres URL zostanie utworzony).

1. Jeśli zostanie wyświetlony monit, wprowadź klucz konta magazynu.

1. Wybierz tabele zawierające dane dotyczące przeglądów hotelu utworzone przez poprzednie przewodniki. 

   + Dla przewodnika po portalu nazwy tabel to *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases* i *hotelReviewsSsPages*. 
   
   + W przypadku przewodnika REST nazwy tabel to *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases* i *hotelReviewsSentiment*.

1. Kliknij przycisk **Załaduj**.

1. Na górnej wstążce kliknij przycisk **Edytuj zapytania** , aby otworzyć **Edytor Power Query**.

   ![Otwórz Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Otwórz Power Query")

1. Wybierz pozycję *hotelReviewsSsDocument*, a następnie usuń kolumny *PartitionKey*, *RowKey* i *timestamp* . 
   ![Edytuj tabele](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Edytuj tabele")

1. Kliknij ikonę z przeciwległymi strzałkami w prawym górnym rogu tabeli, aby rozwinąć *zawartość*. Gdy zostanie wyświetlona lista kolumn, zaznacz opcję wszystkie kolumny, a następnie usuń zaznaczenie kolumn, które zaczynają się od "Metadata". Kliknij przycisk **OK** , aby wyświetlić wybrane kolumny.

   ![Rozwiń zawartość](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Rozwiń zawartość")

1. Zmień typ danych dla następujących kolumn, klikając ikonę ABC-123 w lewym górnym rogu kolumny.

   + W obszarze *Content. Latitude* i *Content. Długość geograficzna* wybierz **liczbę dziesiętną**.
   + W obszarze *Content.reviews_date* i *Content.reviews_dateAdded* wybierz pozycję **Data/godzina**.

   ![Zmień typy danych](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Zmień typy danych")

1. Wybierz pozycję *hotelReviewsSsPages*, a następnie powtórz kroki 9 i 10, aby usunąć kolumny i rozwinąć *zawartość*.
1. Zmień typ danych *Content. SentimentScore* na **liczbę dziesiętną**.
1. Wybierz pozycję *hotelReviewsSsKeyPhrases* , a następnie powtórz kroki 9 i 10, aby usunąć kolumny i rozwinąć *zawartość*. Brak modyfikacji typu danych dla tej tabeli.

1. Na pasku poleceń kliknij przycisk **Zamknij i Zastosuj**.

1. Kliknij kafelek model w lewym okienku nawigacji i sprawdź, czy Power BI pokazują relacje między wszystkimi trzema tabelami.

   ![Weryfikuj relacje](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Weryfikuj relacje")

1. Kliknij dwukrotnie każdą relację i upewnij się, że ustawiono **kierunek filtrowania krzyżowego** **.**  Umożliwia to odświeżanie wizualizacji w przypadku zastosowania filtru.

1. Kliknij kafelek raport w okienku nawigacji po lewej stronie, aby eksplorować dane za pomocą wizualizacji. W przypadku pól tekstowych tabele i karty są przydatnymi wizualizacjami. Możesz wybrać pola z każdej z trzech tabel, aby wypełnić tabelę lub kartę. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Czyszczenie

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak eksplorować ten magazyn wiedzy przy użyciu Eksplorator usługi Storage, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Wyświetlanie przy użyciu Eksploratora usługi Storage](knowledge-store-view-storage-explorer.md)