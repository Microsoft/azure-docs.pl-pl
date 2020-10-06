---
title: Samouczek — eksportowanie danych z usługi Azure Cost Management i zarządzanie nimi
description: W tym artykule pokazano, jak eksportować dane usługi Azure Cost Management i zarządzać nimi, aby można było używać ich w systemach zewnętrznych.
author: bandersmsft
ms.author: banders
ms.date: 08/05/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 6ef5a457bac7b384dc1b4349b1782a752c41ea26
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447607"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Samouczek: Eksportowanie danych i zarządzanie nimi

Jeśli znasz samouczek Analiza kosztów, już wiesz, jak ręcznie pobierać dane usługi Cost Management. Jednak możesz utworzyć cykliczne zadanie, które będzie automatycznie eksportowało dane usługi Cost Management do magazynu platformy Azure — codziennie, co tydzień lub co miesiąc. Dane są eksportowane w formacie CSV i zawierają wszystkie informacje zebrane przez usługę Cost Management. Wyeksportowanych danych znajdujących się w magazynie platformy Azure możesz następnie używać z systemami zewnętrznymi, łącząc je z własnymi danymi niestandardowymi. Możesz również użyć ich w systemie zewnętrznym, takim jak pulpit nawigacyjny lub inny system finansowy.

Obejrzyj wideo [Jak zaplanować eksporty do magazynu za pomocą usługi Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) z informacjami o tworzeniu zaplanowanego eksportu danych kosztów platformy Azure do usługi Azure Storage. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Przykłady w tym samouczku przeprowadzą Cię przez proces eksportowania danych zarządzania kosztami i sprawdzania, czy dane zostały pomyślnie wyeksportowane.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

## <a name="prerequisites"></a>Wymagania wstępne
Eksportowanie danych jest dostępne dla różnych typów kont platformy Azure, w tym dla klientów z umową [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) i [umową klienta firmy Microsoft](get-started-partners.md). Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Na potrzeby eksportu danych przez użytkownika i grupę w ramach subskrypcji są obsługiwane następujące uprawnienia, czyli zakresy, platformy Azure. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać zaplanowane eksporty dla subskrypcji.
- Współautor — może tworzyć, modyfikować i usuwać zaplanowane przez siebie eksporty. Może modyfikować nazwy zaplanowanych eksportów utworzonych przez innych użytkowników.
- Czytelnik — może planować eksporty, do których ma uprawnienia.

W przypadku kont usługi Azure Storage:
- Niezależnie od uprawnień dotyczących eksportu, do zmiany skonfigurowanego konta magazynu wymagane są uprawnienia zapisu.
- Twoje konto magazynu platformy Azure musi być skonfigurowane jako magazyn obiektów blob lub magazyn plików.

Jeśli masz nową subskrypcję, nie możesz od razu korzystać z funkcji usługi Cost Management. Aby można było korzystać ze wszystkich funkcji usługi Cost Management, może upłynąć do 48 godzin.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Tworzenie codziennego eksportu

Aby utworzyć lub wyświetlić eksport danych bądź zaplanować eksport, otwórz żądany zakres w witrynie Azure Portal i wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do obszaru **Subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję **Analiza kosztów** w menu. W górnej części strony Analiza kosztów wybierz pozycję **Ustawienia**, a następnie **Eksporty**.

> [!NOTE]
> - Eksporty można tworzyć nie tylko dla subskrypcji, ale również dla grup zasobów, grup zarządzania, działów i rejestracji. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).
>- Po zalogowaniu się jako partner w zakresie konta rozliczeniowego lub dzierżawy klienta możesz wyeksportować dane do konta usługi Azure Storage połączonego z Twoim partnerskim kontem magazynu. Musisz jednak mieć aktywną subskrypcję w dzierżawie dostawców rozwiązań w chmurze.

1. Wybierz pozycję **Dodaj** i wpisz nazwę eksportu. 
1. W obszarze **Metryka** zaznacz pozycję:
    - **Koszt rzeczywisty (użycie i zakupy)** — wybierz tę opcję, aby wyeksportować standardowe użycie i zakupy
    - **Koszt zamortyzowany (użycie i zakupy)** — wybierz tę opcję, aby wyeksportować zamortyzowane koszty zakupów, takich jak rezerwacje platformy Azure
1. W obszarze **Typ eksportu** zaznacz pozycję:
    - **Codzienny eksport kosztów od początku miesiąca** — codziennie udostępnia nowy plik eksportu dotyczący kosztów od początku miesiąca. Najnowsze dane są agregowane z poprzednich codziennych eksportów.
    - **Tygodniowy eksport kosztów z ostatnich 7 dni** — umożliwia utworzenie tygodniowego eksportu kosztów z ostatnich siedmiu dni począwszy od wybranej daty rozpoczęcia eksportu.  
    - **Miesięczny eksport kosztów z ostatniego miesiąca** — umożliwia eksportowanie kosztów z ostatniego miesiąca w celu porównania ich z eksportem tworzonym w bieżącym miesiącu. Eksport jest uruchamiany zgodnie z harmonogramem w piątym dniu każdego nowego miesiąca i zawiera koszty z poprzednich miesięcy.  
    - **Eksport jednorazowy**— umożliwia wybranie zakresu dat dla danych historycznych eksportowanych do usługi Azure Blob Storage. Możesz wyeksportować koszty historyczne obejmujące maksymalnie 90 dni począwszy od wskazanego dnia. Eksport jest uruchamiany natychmiast, a na koncie magazynu jest dostępny w ciągu dwóch godzin.  
        W zależności od typu eksportu możesz wybrać datę rozpoczęcia lub przedział czasowy, wskazując datę początkową (**Od**) i datę końcową (**Do**).
1. Podaj subskrypcję zawierającą konto usługi Azure Storage, a następnie wybierz grupę zasobów lub utwórz nową. 
1. Wybierz nazwę konta magazynu lub utwórz nowe konto. 
1. Wybierz lokalizację (region świadczenia platformy Azure).
1. Określ ścieżkę kontenera i katalogu do zapisania wyeksportowanego pliku. 
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Przykład nowego eksportu" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Przejrzyj szczegóły eksportu, a następnie wybierz przycisk **Utwórz**.

Nowy eksport pojawi się na liście eksportów. Domyślnie nowe zadania eksportu są włączone. Jeśli chcesz wyłączyć lub usunąć zaplanowany eksport, wybierz dowolny element na liście, a następnie wybierz pozycję **Wyłącz** lub **Usuń**.

Początkowo uruchomienie eksportu może zająć od 12 do 24 godzin. Jednak wyświetlenie danych w wyeksportowanych plikach może potrwać dłużej.

### <a name="export-schedule"></a>Eksportowanie harmonogramu

Na zaplanowane eksporty wpływa czas (godzina i dzień tygodnia) początkowego utworzenia eksportu. Po utworzeniu zaplanowanego eksportu eksport będzie uruchamiany z taką samą częstotliwością dla każdego kolejnego wystąpienia eksportu. Na przykład w przypadku ustawienia codziennego eksportu kosztów od początku miesiąca z częstotliwością raz na dzień, eksport będzie uruchamiany codziennie. Podobnie w przypadku eksportu tygodniowego eksport będzie uruchamiany co tydzień tego samego dnia zgodnie z harmonogramem. Dokładny czas dostarczania eksportu nie jest gwarantowany, a wyeksportowane dane będą dostępne w ciągu czterech godzin od uruchomienia.

Każdy eksport tworzy nowy plik, więc starsze eksporty nie są zastępowane.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Tworzenie eksportu dla wielu subskrypcji

Jeśli masz umowę Enterprise Agreement, możesz użyć grupy zarządzania do agregowania informacji o kosztach subskrypcji w jednym kontenerze. Następnie można wyeksportować dane dotyczące zarządzania kosztami dla grupy zarządzania.

Eksporty dla grup zarządzania innych typów subskrypcji nie są obsługiwane.

1. Jeśli grupa zarządzania nie została jeszcze utworzona, utwórz ją i przypisz do niej subskrypcje.
1. W analizie kosztów ustaw zakres grupy zarządzania i wybierz opcję **Wybierz tę grupę zarządzania**.  
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Przykład nowego eksportu" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Utwórz eksport w zakresie, aby uzyskać dane dotyczące zarządzania kosztami dla subskrypcji w grupie zarządzania.  
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Przykład nowego eksportu":::

## <a name="verify-that-data-is-collected"></a>Sprawdzanie, czy dane są zbierane

Używając Eksploratora usługi Azure Storage, możesz łatwo sprawdzić, czy dane usługi Cost Management są zbierane, i otworzyć wyeksportowany plik CSV.

Na liście eksportów wybierz nazwę konta magazynu. Na stronie konta magazynu wybierz pozycję Otwórz w Eksploratorze. Jeśli zostanie wyświetlone okno dialogowe potwierdzenia, wybierz przycisk **Tak**, aby otworzyć plik w Eksploratorze usługi Azure Storage.

![Strona konta magazynu z przykładowymi informacjami i linkiem Otwórz w programie Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

W Eksploratorze usługi Storage przejdź do kontenera, który chcesz otworzyć, a następnie wybierz folder odpowiadający bieżącemu miesiącowi. Zostanie wyświetlona lista plików CSV. Wybierz jeden z nich, a następnie wybierz przycisk **Otwórz**.

![Przykładowe informacje wyświetlane w Eksploratorze usługi Storage](./media/tutorial-export-acm-data/storage-explorer.png)

Plik zostanie otwarty za pomocą programu lub aplikacji, która została skonfigurowana do otwierania plików z rozszerzeniem CSV. Oto przykład w programie Excel.

![Przykładowe wyeksportowane dane w formacie CSV wyświetlane w programie Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Pobieranie wyeksportowanego pliku danych CSV

Możesz również pobrać wyeksportowany plik CSV w witrynie Azure Portal. Poniżej wyjaśniono, jak znaleźć go z poziomu analizy kosztów.

1. W obszarze analizy kosztów wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Eksporty**.
1. Z listy eksportów wybierz konto magazynu do wyeksportowania.
1. Na koncie magazynu kliknij pozycję **Kontenery**.
1. Z listy kontenerów wybierz kontener.
1. Przejdź do katalogów i obiektów blob magazynu z żądanego dnia.
1. Wybierz plik CSV, a następnie wybierz pozycję **Pobierz**.

[![Przykład pobierania eksportu](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Sprawdzanie historii uruchamiania eksportu  

Aby wyświetlić historię uruchamiania zaplanowanego eksportu, wybierz eksport indywidualny na stronie listy z eksportami. Strona listy z eksportami umożliwia również szybkie wyświetlenie czasu uruchomienia poprzednich eksportów oraz zaplanowanych eksportów. Oto przykładowa historia uruchamiania.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Przykład nowego eksportu":::

Wybierz eksport, aby wyświetlić jego historię uruchamiania.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Przykład nowego eksportu":::

## <a name="access-exported-data-from-other-systems"></a>Uzyskiwanie dostępu do wyeksportowanych danych z poziomu innych systemów

Jednym z celów eksportowania danych usługi Cost Management jest uzyskiwanie dostępu do danych z poziomu systemów zewnętrznych. Możesz na przykład użyć systemu pulpitu nawigacyjnego lub innego systemu finansowego. Takie systemy są bardzo zróżnicowane, dlatego pokazanie przykładu byłoby niepraktyczne.  Jednak możesz zacząć od sposobu uzyskiwania dostępu do danych z poziomu Twoich aplikacji w artykule [Wprowadzenie do usługi Azure Storage](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie codziennego eksportu
> * Sprawdzanie, czy dane są zbierane

Przejdź do następnego samouczka, aby przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane.

> [!div class="nextstepaction"]
> [Zapoznawanie się z zaleceniami dotyczącymi optymalizacji i ich wdrażanie](tutorial-acm-opt-recommendations.md)
