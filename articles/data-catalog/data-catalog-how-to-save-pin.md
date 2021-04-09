---
title: Zapisywanie wyszukiwań i Przypinanie zasobów danych w Azure Data Catalog
description: Funkcje wyróżniania artykułów w Azure Data Catalog do zapisywania źródeł danych i zasobów danych do późniejszego użycia.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52a8d8fcd0b9b9ee1ff918ead250c88b83f86cb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674839"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Zapisywanie wyszukiwań i Przypinanie zasobów danych w Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Wprowadzenie
Azure Data Catalog zapewnia możliwości odnajdowania źródeł danych. Można szybko wyszukiwać i filtrować wykaz, aby zlokalizować źródła danych i zrozumieć ich zamierzone przeznaczenie, ułatwiając znalezienie odpowiednich danych dla zadania.

Ale co zrobić, jeśli chcesz regularnie korzystać z tych samych danych? Co zrobić, jeśli ty i inni użytkownicy regularnie współwiedzają swoją wiedzę z tymi samymi źródłami danych w katalogu? W takich sytuacjach, koniecznie powtarzające się te same wyszukiwania może być niewydajne. Jest to miejsce, w którym zapisane wyszukiwanie i przypięte zasoby danych mogą pomóc.

## <a name="saved-searches"></a>Zapisane wyszukiwania
Zapisane wyszukiwanie w Data Catalog jest definicją wyszukiwania na użytkownika wielokrotnego użytku. Możesz zdefiniować wyszukiwanie, w tym terminy wyszukiwania, Tagi i inne filtry, a następnie je zapisać. Zapisanej definicji wyszukiwania można ponownie uruchomić później, aby przywrócić wszystkie zasoby danych, które pasują do kryteriów wyszukiwania.

### <a name="create-a-saved-search"></a>Tworzenie zapisanego wyszukiwania
Aby utworzyć zapisane wyszukiwanie, wykonaj następujące czynności:
1. W portalu Azure Data Catalog w **bieżącym oknie wyszukiwania** kliknij pozycję **Zapisz**. 

    ![Link zapisywania bieżących ustawień wyszukiwania](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Wprowadź kryteria wyszukiwania, których chcesz użyć ponownie, a następnie kliknij przycisk **Zapisz**.

    ![Bieżące ustawienia wyszukiwania Zapisano nazwę wyszukiwania](./media/data-catalog-how-to-save-pin/02-name.png)

3. Po wyświetleniu monitu wprowadź nazwę zapisanego wyszukiwania. Wybierz nazwę zrozumiałą i opisującą zasoby danych, które zostaną zwrócone przez wyszukiwanie.

### <a name="manage-saved-searches"></a>Zarządzanie zapisanymi wyszukiwaniami
Po zapisaniu co najmniej jednego wyszukiwania opcja **zapisane wyszukiwania** jest wyświetlana poniżej **bieżącego pola wyszukiwania** . Gdy lista zostanie rozwinięta, wyświetlane są wszystkie zapisane wyszukiwania.

 ![Data Catalog — Lista zapisanych wyszukiwań](./media/data-catalog-how-to-save-pin/03-list.png)

Wykonaj dowolną z następujących czynności:

* Aby wykonać wyszukiwanie, wybierz zapisane wyszukiwanie na liście.

* Aby wyświetlić listę opcji zarządzania dla zapisanego wyszukiwania, kliknij strzałkę w dół obok nazwy wyszukiwania.

    ![Opcje zarządzania zapisanymi wyszukiwaniami](./media/data-catalog-how-to-save-pin/04-managing.png)

* Aby wprowadzić nową nazwę zapisanego wyszukiwania, wybierz pozycję **Zmień nazwę**. Definicja wyszukiwania nie została zmieniona.

* Aby usunąć zapisane wyszukiwanie z listy, wybierz pozycję **Usuń**, a następnie Potwierdź usunięcie.

* Aby oznaczyć zapisane wyszukiwanie jako domyślne wyszukiwania, wybierz opcję **Zapisz jako domyślne**. Jeśli przeszukasz "puste" na stronie głównej Azure Data Catalog, zostanie wykonane domyślne wyszukiwanie. Ponadto wyszukiwanie oznaczone jako domyślne wyszukiwania jest wyświetlane w górnej części listy **zapisane wyszukiwania** .

### <a name="organizational-saved-searches"></a>Zapisane wyszukiwania w organizacji
Wszyscy użytkownicy w organizacji mogą zapisywać wyszukiwania do użycia. Administratorzy Data Catalog mogą także zapisywać wyszukiwania dla wszystkich użytkowników w organizacji. Gdy administratorzy zapisują wyszukiwanie, są one prezentowane z **udziałem w opcji firmy** . Wybranie tej opcji udostępnia zapisane wyszukiwanie dla wszystkich użytkowników w organizacji.

 ![Data Catalog — zapisane wyszukiwania w organizacji](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Przypięte zasoby danych
Dzięki zapisane wyszukiwania można zapisywać i ponownie używać definicji wyszukiwania. Zasoby danych zwracane przez wyszukiwania mogą ulec zmianie w czasie, ponieważ zawartość katalogu zostanie zmieniona. W przypadku przypinania zasobów danych można jawnie zidentyfikować konkretne zasoby danych, aby ułatwić im dostęp bez konieczności wyszukiwania.

Przypinanie zasobów danych jest proste. Aby dodać zasób danych do listy przypiętej, wystarczy kliknąć ikonę **pinezki** . Ikona zostanie wyświetlona w rogu kafelka zasobu w widoku kafelków, a w kolumnie najwyższego poziomu w widoku listy w portalu Azure Data Catalog.

![Data Catalog — ikona numeru PIN elementu dane](./media/data-catalog-how-to-save-pin/05-pinning.png)

Odpięcie zasobu danych jest równie proste. Po prostu kliknij ikonę **Odepnij** , aby przełączyć ustawienie dla wybranego elementu zawartości.

![Data Catalog — ikona odpinania danych](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Sekcja my Assets
Na stronie głównej portalu Data Catalog znajdują się sekcje **My Assets** , w których są wyświetlane zasoby istotne dla bieżącego użytkownika. Ta sekcja zawiera elementy przypięte i zapisane wyszukiwania.

![Sekcja my Assets na stronie głównej](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Podsumowanie
Azure Data Catalog udostępnia funkcje, które ułatwiają odnajdywanie potrzebnych źródeł danych, dzięki czemu Ty i inni członkowie organizacji mogą poświęcać mniej czasu na wyszukiwanie danych i dłuższy czas pracy z działem IT. Zapisane wyszukiwania i przypięte zasoby danych kompilują się na tych podstawowych możliwościach, dzięki czemu użytkownicy mogą łatwo identyfikować źródła danych, z których pracują wielokrotnie.
