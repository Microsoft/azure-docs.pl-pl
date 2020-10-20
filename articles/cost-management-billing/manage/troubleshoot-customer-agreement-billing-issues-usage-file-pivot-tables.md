---
title: Rozwiązywanie problemów z rozliczeniami w ramach umowy MCA na platformie Azure przy użyciu tabel przestawnych pliku użycia
description: Ten artykuł zawiera informacje pomocne przy rozwiązywaniu problemów z rozliczeniami w ramach Umowy z Klientem Microsoft (MCA) przy użyciu tabel przestawnych utworzonych na podstawie plików użycia w formacie CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 2ca4affaeae7f19cf3c913b5dfcf89a04e5bc628
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026803"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>Rozwiązywanie problemów z rozliczeniami w ramach umowy MCA przy użyciu tabel przestawnych pliku użycia

Ten artykuł zawiera informacje pomocne przy rozwiązywaniu problemów z rozliczeniami w ramach Umowy z Klientem Microsoft (MCA) przy użyciu tabel przestawnych w plikach użycia. Pliki użycia platformy Azure zawierają wszystkie informacje dotyczące użycia i zużycia na platformie Azure. Informacje zawarte w pliku mogą pomóc w zrozumieniu następujących kwestii:

- Używanie i stosowanie rezerwacji platformy Azure
- Uzgadnianie informacji z usługi Azure Cost Management z fakturą rozliczeniową
- Rozwiązywanie problemów ze wzrostem kosztów
- Obliczanie kwoty zwrotu dla umowy dotyczącej poziomu usług

Korzystając z informacji z plików użycia, można lepiej zrozumieć problemy dotyczące użycia i je zdiagnozować. Pliki użycia są generowane w formacie danych rozdzielanych przecinkami (CSV). Ponieważ pliki użycia mogą być dużymi plikami CSV, łatwiej nimi manipulować i wyświetlać je w postaci tabel przestawnych w aplikacji do obsługi arkusza kalkulacyjnego, takiej jak Excel. Przykłady w tym artykule są przedstawiane w programie Excel, ale możesz użyć dowolnej aplikacji do obsługi arkusza kalkulacyjnego.

Tylko właściciele profilów rozliczeniowych, współautorzy, czytelnicy i menedżerowie faktur mogą pobierać pliki użycia. Aby uzyskać więcej informacji, zobacz [Pobieranie zestawienia użycia dla Umowy z Klientem Microsoft](https://docs.microsoft.com/azure/cost-management-billing/manage/download-azure-invoice-daily-usage-date#download-usage-for-your-microsoft-customer-agreement). 

## <a name="get-the-data-and-format-it"></a>Pobieranie i formatowanie danych

Ponieważ pliki użycia platformy Azure są w formacie CSV, należy przygotować dane do użycia w programie Excel. Wykonaj następujące kroki, aby sformatować dane jako tabelę.

1. Pobierz plik użycia, postępując według instrukcji opisanych w artykule [Pobieranie danych użycia w witrynie Azure Portal](https://docs.microsoft.com/azure/cost-management-billing/manage/download-azure-invoice-daily-usage-date#download-usage-in-azure-portal).
1. Otwórz plik w programie Excel.
1. Niesformatowane dane są podobne do tych z poniższego przykładu.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. Wybierz pierwsze pole w tabeli, **invoiceID**.
1. Naciśnij klawisze Ctrl + Shift + Strzałka w dół, a następnie Ctrl + Shift + Strzałka w prawo, aby zaznaczyć wszystkie informacje w tabeli.
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela**. W polu Tworzenie tabeli wybierz pozycję **Moja tabela ma nagłówki**, a następnie wybierz przycisk **OK**.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Przykład przedstawiający niesformatowane dane" :::
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela przestawna**, a następnie wybierz przycisk **OK**. Spowoduje to utworzenie nowego arkusza w pliku i przejście do obszaru tabeli przestawnej po prawej stronie arkusza.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

W obszarze Pola tabeli przestawnej można korzystać z metody przeciągnij i upuść. Przejdź do następnej sekcji, aby utworzyć tabelę przestawną.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Tworzenie tabeli przestawnej w celu wyświetlenia kosztów platformy Azure według zasobów

W tej sekcji utworzysz tabelę przestawną, za pomocą której będzie można rozwiązywać problemy z ogólnym użyciem platformy Azure. Przykładowa tabela może ułatwić zbadanie, która usługa zużywa najwięcej zasobów. Możesz też wyświetlić zasoby, które generują najwięcej kosztów, oraz prześledzić sposób naliczania opłat za usługę.

1. W obszarze Pola tabeli przestawnej przeciągnij pola **Meter Category** i **Product** do sekcji **Wiersze**. Umieść pole **Product** poniżej pola **Meter Category**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Następnie dodaj kolumnę **costInBillingCurrenty** do sekcji **Wartości**. Zamiast tej kolumny można również użyć kolumny **Quantity**, aby uzyskać informacje o jednostkach zużycia i transakcjach. Mogą to być na przykład gigabajty lub godziny. Lub liczba transakcji zamiast kosztów w różnych walutach, takich jak USD, EUR i INR.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Masz teraz pulpit nawigacyjny do uogólnionego badania zużycia. Aby uzyskać konkretną usługę, można użyć filtru i różnych opcji filtrowania w tabeli przestawnej.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Aby odfiltrować drugi poziom w tabeli przestawnej, na przykład zasób, wybierz element drugiego poziomu w tabeli.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Przeciągnij kolumnę **ResourceID** do obszaru **Wiersze** poniżej pola **Product**, aby zobaczyć koszt każdej usługi według zasobu.
1. Dodaj kolumnę **date** do obszaru **Kolumny**, aby zobaczyć dzienne zużycie dla produktu.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Rozwijaj i zwijaj poszczególne miesiące za pomocą symboli **+** przy każdej kolumnie miesiąca.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

Dodanie kolumn **Cost** i **Quantity** w obszarze **Wartości** jest opcjonalne. Spowoduje to utworzenie dwóch kolumn dla każdej sekcji danych poniżej poszczególnych miesięcy i dni, gdy kolumna Date znajduje się w sekcji Kolumny tabeli przestawnej.

Aby zastosować dodatkowe filtry, można dodać pole InvoiceSection, costCenter, SubscriptionID, ResourceGroupName lub Tags do sekcji filtrów i wybrać żądany zakres.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Tworzenie tabeli przestawnej w celu wyświetlenia kosztu konkretnego zasobu

Pojedynczy zasób może spowodować naliczenie różnych opłat za różne usługi. Na przykład maszyna wirtualna może spowodować naliczenie opłat za zasoby obliczeniowe, licencjonowanie systemu operacyjnego, przepustowość (transfery danych), użycie wystąpienia zarezerwowanego i magazyn dla migawek. Jeśli chcesz poznać ogólne użycie określonych zasobów, w poniższych krokach opisano proces tworzenia pulpitu nawigacyjnego umożliwiającego wyświetlenie ogólnego użycia za pomocą plików użycia.

1. W menu po prawej stronie przeciągnij pole **ResourceID** do sekcji **Filtr** w menu tabeli przestawnej.
1. Wybierz zasób, dla którego chcesz wyświetlić koszt. W polu **Wyszukaj** wpisz nazwę szukanego zasobu.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. Dodaj pola **meterCategory** i **Product** do obszaru **Wiersze**. Umieść pole **Product** poniżej pola **meterCategory**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Przykład przedstawiający niesformatowane dane" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Następnie dodaj kolumnę **Extended Cost** do sekcji **Wartości**. Zamiast tej kolumny można również użyć kolumny Consumed Quantity, aby uzyskać informacje o jednostkach zużycia i transakcjach. Mogą to być na przykład gigabajty lub godziny. Lub liczba transakcji zamiast kosztów w różnych walutach, takich jak USD, EUR i INR. Masz teraz pulpit nawigacyjny zawierający wszystkie usługi, z których korzysta zasób.
1. Dodaj kolumnę **Date** do sekcji **Kolumny**. Dzięki niej można zobaczyć dzienne zużycie.
1. Ikony **+** w kolumnach poszczególnych miesięcy umożliwiają rozszerzanie i zwijanie danych.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Przykład przedstawiający niesformatowane dane" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Następne kroki

- [Eksplorowanie i analizowanie kosztów za pomocą analizy kosztów](../costs/quick-acm-cost-analysis.md).