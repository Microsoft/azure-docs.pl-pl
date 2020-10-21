---
title: Rozwiązywanie problemów z rozliczeniami w ramach umowy EA platformy Azure przy użyciu tabel przestawnych pliku użycia
description: Ten artykuł zawiera informacje pomocne przy rozwiązywaniu problemów z rozliczeniami w ramach umowy Enterprise Agreement (EA) przy użyciu tabel przestawnych utworzonych na podstawie plików użycia w formacie CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: b32bb979176af8a8a3751db8edf9c129caf1002e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131993"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>Rozwiązywanie problemów z rozliczeniami w ramach umowy EA przy użyciu tabel przestawnych pliku użycia

Ten artykuł zawiera informacje pomocne przy rozwiązywaniu problemów z rozliczeniami w ramach umowy EA przy użyciu tabel przestawnych w plikach użycia. Pliki użycia platformy Azure zawierają wszystkie informacje dotyczące użycia i zużycia na platformie Azure. Informacje zawarte w pliku mogą pomóc w zrozumieniu następujących kwestii:

- Używanie i stosowanie rezerwacji platformy Azure
- Uzgadnianie informacji z usługi Azure Cost Management z fakturą rozliczeniową
- Rozwiązywanie problemów ze wzrostem kosztów
- Obliczanie kwoty zwrotu dla umowy dotyczącej poziomu usług

Korzystając z informacji z plików użycia, można lepiej zrozumieć problemy dotyczące użycia i je zdiagnozować. Pliki użycia są generowane w formacie danych rozdzielanych przecinkami (CSV). Ponieważ pliki użycia mogą być dużymi plikami CSV, łatwiej nimi manipulować i wyświetlać je w postaci tabel przestawnych w aplikacji do obsługi arkusza kalkulacyjnego, takiej jak Excel. Przykłady w tym artykule są przedstawiane w programie Excel, ale możesz użyć dowolnej aplikacji do obsługi arkusza kalkulacyjnego.

Pliki użycia mogą pobierać tylko administratorzy umowy EA, właściciele kont i administratorzy działów.

## <a name="get-the-data-and-format-it"></a>Pobieranie i formatowanie danych

Ponieważ pliki użycia platformy Azure są w formacie CSV, należy przygotować dane do użycia w programie Excel. Wykonaj następujące kroki, aby sformatować dane jako tabelę.

1. Pobierz plik zawierający szczegóły użycia w wersji 2 ze wszystkimi opłatami (za zużycie i zakupy), korzystając z instrukcji z sekcji [Pobieranie zestawienia użycia dla klientów z umową EA](./download-azure-invoice-daily-usage-date.md#download-usage-for-ea-customers).
1. Otwórz plik w programie Excel.
1. Niesformatowane dane są podobne do tych z poniższego przykładu.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. Wybierz pierwsze pole w tabeli zawierające tytuł pierwszej kolumny **BillingAccountID**.
1. Naciśnij klawisze Ctrl + Shift + Strzałka w dół, a następnie Ctrl + Shift + Strzałka w prawo, aby zaznaczyć wszystkie informacje w tabeli.
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela**. W polu Tworzenie tabeli wybierz pozycję **Moja tabela ma nagłówki**, a następnie wybierz przycisk **OK**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" :::
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela przestawna**, a następnie wybierz przycisk **OK**. Spowoduje to utworzenie nowego arkusza w pliku. Po prawej stronie arkusza będzie dostępny obszar tabeli przestawnej.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

W obszarze Pola tabeli przestawnej można korzystać z metody przeciągnij i upuść. Przejdź do następnej sekcji, aby utworzyć tabelę przestawną.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Tworzenie tabeli przestawnej w celu wyświetlenia kosztów platformy Azure według zasobów

W tej sekcji utworzysz tabelę przestawną, za pomocą której będzie można rozwiązywać problemy z ogólnym użyciem platformy Azure. Przykładowa tabela może ułatwić zbadanie, która usługa zużywa najwięcej zasobów. Możesz też wyświetlić zasoby, które generują najwięcej kosztów, oraz prześledzić sposób naliczania opłat za usługę.

1. W obszarze Pola tabeli przestawnej przeciągnij pola **Meter Category** i **Product** do sekcji **Wiersze**. Umieść pole **Product** poniżej pola **Meter Category**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Następnie dodaj kolumnę **Cost** do sekcji **Wartości**. Zamiast tej kolumny można również użyć kolumny Consumed Quantity, aby uzyskać informacje o jednostkach zużycia i transakcjach. Mogą to być na przykład gigabajty lub godziny. Lub liczba transakcji zamiast kosztów w różnych walutach, takich jak USD, EUR i INR.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Masz teraz pulpit nawigacyjny do uogólnionego badania zużycia. Aby uzyskać konkretną usługę, można użyć filtru i różnych opcji filtrowania w tabeli przestawnej.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Aby odfiltrować drugi poziom w tabeli przestawnej, na przykład zasób, wybierz element drugiego poziomu w tabeli.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Przeciągnij kolumnę **ResourceID** do obszaru **Wiersze** poniżej pola **Product**, aby zobaczyć koszt każdej usługi według zasobu. Aby przejrzeć szczegółowe informacje o cenach, wyświetl cenę jednostkową swojej organizacji i wyszukaj **produkt** w pierwszej kolumnie cennika.
1. Dodaj kolumnę **Date** do obszaru **Kolumny**, aby zobaczyć dzienne zużycie dla produktu.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Rozwijaj i zwijaj poszczególne miesiące za pomocą symboli **+** przy każdej kolumnie miesiąca.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    Dodanie kolumn **Cost** i **Quantity** w obszarze **Wartości** jest opcjonalne. Spowoduje to utworzenie dwóch kolumn dla każdej sekcji danych poniżej poszczególnych miesięcy i dni, gdy kolumna Date znajduje się w sekcji Kolumny tabeli przestawnej.
1. Aby zastosować dodatkowe filtry, można dodać kolumnę SubscriptionID, Department, ResourceGroup, Tags lub Cost Center do obszaru **Filtry** i wybrać żądany element.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Tworzenie tabeli przestawnej w celu wyświetlenia kosztu konkretnego zasobu

Pojedynczy zasób może spowodować naliczenie różnych opłat za różne usługi. Na przykład maszyna wirtualna może spowodować naliczenie opłat za zasoby obliczeniowe, licencjonowanie systemu operacyjnego, przepustowość (transfery danych), użycie wystąpienia zarezerwowanego i magazyn dla migawek. Jeśli chcesz poznać ogólne użycie określonych zasobów, w poniższych krokach opisano proces tworzenia pulpitu nawigacyjnego umożliwiającego wyświetlenie ogólnego użycia za pomocą plików użycia.

1. W menu po prawej stronie przeciągnij pole **ResourceID** do sekcji **Filtr** w menu tabeli przestawnej.
1. Wybierz zasób, dla którego chcesz wyświetlić koszt. W polu **Wyszukaj** wpisz nazwę szukanego zasobu.
1. Dodaj pola **Meter Category** i **Product** do sekcji Wiersze. Umieść pole **Product** poniżej pola **Meter Category**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Następnie dodaj kolumnę **Cost** do sekcji **Wartości**. Zamiast tej kolumny można również użyć kolumny Consumed Quantity, aby uzyskać informacje o jednostkach zużycia i transakcjach. Mogą to być na przykład gigabajty lub godziny. Lub liczba transakcji zamiast kosztów w różnych walutach, takich jak USD, EUR i INR. Masz teraz pulpit nawigacyjny zawierający wszystkie usługi, z których korzysta zasób.
1. Dodaj kolumnę **Date** do sekcji **Kolumny**. Dzięki niej można zobaczyć dzienne zużycie.
1. Symbole **+** w kolumnach poszczególnych miesięcy umożliwiają rozszerzanie i zwijanie danych.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Następne kroki

- [Eksplorowanie i analizowanie kosztów za pomocą analizy kosztów](../costs/quick-acm-cost-analysis.md).