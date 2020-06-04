---
title: Samouczek — eksportowanie danych z usługi Azure Cost Management i zarządzanie nimi
description: W tym artykule pokazano, jak eksportować dane usługi Azure Cost Management i zarządzać nimi, aby można było używać ich w systemach zewnętrznych.
author: bandersmsft
ms.author: banders
ms.date: 05/27/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 90334d29ed2f649854863f9ad86f03811728a945
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142325"
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

Aby utworzyć lub wyświetlić eksport danych bądź zaplanować eksport, otwórz żądany zakres w witrynie Azure Portal i wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do obszaru **Subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję **Analiza kosztów** w menu. W górnej części strony Analiza kosztów wybierz pozycję **Ustawienia** i pozycję **Eksporty**, a następnie wybierz opcję eksportowania.

> [!NOTE]
> - Eksporty można tworzyć nie tylko dla subskrypcji, ale również dla grup zasobów, kont, działów i rejestracji. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).
>- Po zalogowaniu się jako partner w zakresie konta rozliczeniowego lub dzierżawy klienta możesz wyeksportować dane do konta usługi Azure Storage połączonego z Twoim partnerskim kontem magazynu. Musisz jednak mieć aktywną subskrypcję w dzierżawie dostawców rozwiązań w chmurze.

Wybierz pozycję **Dodaj**, wpisz nazwę zadania eksportu, a następnie wybierz opcję **Codzienny eksport kosztów od początku miesiąca**. Wybierz opcję **Dalej**.

[![Przykład nowego eksportu z pokazanym typem eksportu](./media/tutorial-export-acm-data/basics_exports.png)](./media/tutorial-export-acm-data/basics_exports.png#lightbox)

Podaj subskrypcję zawierającą konto usługi Azure Storage, a następnie wybierz konto usługi Storage.  Określ ścieżkę kontenera i katalogu do zapisania wyeksportowanego pliku. Wybierz opcję **Dalej**.

![Przykład nowego eksportu z pokazanymi szczegółami konta magazynu](./media/tutorial-export-acm-data/storage_exports.png)

Przejrzyj szczegóły eksportu, a następnie wybierz przycisk **Utwórz**.

Nowy eksport pojawi się na liście eksportów. Domyślnie nowe zadania eksportu są włączone. Jeśli chcesz wyłączyć lub usunąć zaplanowany eksport, wybierz dowolny element na liście, a następnie wybierz pozycję **Wyłącz** lub **Usuń**.

Początkowo uruchomienie eksportu może zająć od jednej do dwóch godzin. Jednak wyświetlenie danych w wyeksportowanych plikach może potrwać nawet cztery godziny.

### <a name="export-schedule"></a>Harmonogram eksportu

Na zaplanowane eksporty wpływa czas (godzina i dzień tygodnia) początkowego utworzenia eksportu. Po utworzeniu zaplanowanego eksportu eksport będzie uruchamiany z taką samą częstotliwością dla każdego kolejnego wystąpienia eksportu. Na przykład w przypadku ustawienia eksportu od początku miesiąca z częstotliwością raz na dzień, eksport będzie uruchamiany codziennie. Podobnie w przypadku eksportu tygodniowego eksport będzie uruchamiany co tydzień tego samego dnia zgodnie z harmonogramem. Dokładny czas dostarczania eksportu nie jest gwarantowany, a wyeksportowane dane będą dostępne w ciągu czterech godzin od uruchomienia.
Dla każdego eksportu tworzony jest nowy plik, więc starsze eksporty nie są zastępowane.

Istnieją dwa typy opcji eksportu:

**Codzienny eksport kosztów od początku miesiąca** — początkowy eksport jest uruchamiany natychmiast. Kolejne eksporty są uruchamiane następnego dnia o tej samej godzinie, co eksport początkowy. Najnowsze dane są agregowane z poprzednich codziennych eksportów.

**Niestandardowe** — umożliwia zaplanowanie cotygodniowych i comiesięcznych eksportów z opcjami od początku tygodnia i od początku miesiąca. *Początkowy eksport zostanie uruchomiony natychmiast.*

Jeśli masz subskrypcję z płatnością zgodnie z rzeczywistym użyciem, MSDN lub programu Visual Studio, Twój okres rozliczeniowy faktury może nie zgadzać się z miesiącem kalendarzowym. W przypadku tych typów subskrypcji i grup zasobów można utworzyć eksport dopasowany do Twojego okresu faktury lub miesięcy kalendarzowych. Aby utworzyć eksport dopasowany do miesiąca na fakturze, przejdź do obszaru **Niestandardowy**, a następnie wybierz pozycję **Okres rozliczeniowy do dnia bieżącego**.  Aby utworzyć eksport dopasowany do miesiąca kalendarzowego, wybierz pozycję **W ciągu ostatniego miesiąca**.

![Nowy eksport — karta Podstawowe przedstawiająca wybór opcji Niestandardowe, Co tydzień i Od początku tygodnia](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

#### <a name="create-an-export-for-multiple-subscriptions"></a>Tworzenie eksportu dla wielu subskrypcji

Jeśli masz umowę Enterprise Agreement, możesz użyć grupy zarządzania do agregowania informacji o kosztach subskrypcji w jednym kontenerze. Następnie można wyeksportować dane dotyczące zarządzania kosztami dla grupy zarządzania.

Eksporty dla grup zarządzania innych typów subskrypcji nie są obsługiwane.

1. Utwórz grupę zarządzania i przypisz do niej subskrypcje.
1. W obszarze Eksporty wybierz pozycję **Zakres**.
1. Wybierz pozycję **wybierz tę grupę zarządzania**.
1. Utwórz eksport w zakresie, aby uzyskać dane dotyczące zarządzania kosztami dla subskrypcji w grupie zarządzania.

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
