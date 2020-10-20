---
title: Rozwiązywanie problemów z rozliczeniami w ramach programu Azure CSP przy użyciu tabel przestawnych pliku użycia
description: Ten artykuł zawiera informacje pomocne przy rozwiązywaniu problemów z rozliczeniami w ramach programu Azure Cloud Solution Provider (CSP) przy użyciu tabel przestawnych utworzonych na podstawie plików użycia w formacie CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026875"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Rozwiązywanie problemów z rozliczeniami w ramach programu CSP przy użyciu tabel przestawnych pliku użycia

Ten artykuł zawiera informacje pomocne przy rozwiązywaniu problemów z rozliczeniami w ramach programu Cloud Solution Provider (CSP) przy użyciu tabel przestawnych utworzonych na podstawie plików uzgodnień (użycia) z Centrum partnerskiego. Pliki użycia platformy Azure zawierają wszystkie informacje dotyczące użycia i zużycia na platformie Azure. Informacje zawarte w pliku mogą pomóc w zrozumieniu następujących kwestii:

- Używanie i stosowanie rezerwacji platformy Azure
- Uzgadnianie informacji z usługi Azure Cost Management z fakturą rozliczeniową
- Rozwiązywanie problemów ze wzrostem kosztów
- Obliczanie kwoty zwrotu dla umowy dotyczącej poziomu usług

Korzystając z informacji z plików użycia, można lepiej zrozumieć problemy dotyczące użycia i je zdiagnozować. Pliki użycia są generowane w formacie danych rozdzielanych przecinkami (CSV). Ponieważ pliki użycia mogą być dużymi plikami CSV, łatwiej nimi manipulować i wyświetlać je w postaci tabel przestawnych w aplikacji do obsługi arkusza kalkulacyjnego, takiej jak Excel. Przykłady w tym artykule są przedstawiane w programie Excel, ale możesz użyć dowolnej aplikacji do obsługi arkusza kalkulacyjnego.

Pliki uzgodnień mogą pobierać tylko administratorzy rozliczeń i administratorzy globalni. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie odczytywania wierszy pozycji w plikach uzgodnień w Centrum partnerskim](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Pobieranie i formatowanie danych

Ponieważ pliki użycia platformy Azure są w formacie CSV, należy przygotować dane do użycia w programie Excel. Wykonaj następujące kroki, aby sformatować dane jako tabelę.

1. Pobierz plik użycia, postępując według instrukcji opisanych w artykule [Znajdowanie rachunku](/partner-center/read-your-bill#find-your-bill).
1. Otwórz plik w programie Excel.
1. Niesformatowane dane są podobne do tych z poniższego przykładu.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Wybierz pierwsze pole w tabeli. **PartnerID**.
1. Naciśnij klawisze Ctrl + Shift + Strzałka w dół, a następnie Ctrl + Shift + Strzałka w prawo, aby zaznaczyć wszystkie informacje w tabeli.
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela**. W polu Tworzenie tabeli wybierz pozycję **Moja tabela ma nagłówki**, a następnie wybierz przycisk **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" :::
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela przestawna**, a następnie wybierz przycisk **OK**. Spowoduje to utworzenie nowego arkusza w pliku i przejście do obszaru tabeli przestawnej po prawej stronie arkusza.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

W obszarze Pola tabeli przestawnej można korzystać z metody przeciągnij i upuść. Przejdź do następnej sekcji, aby utworzyć tabelę przestawną.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Tworzenie tabeli przestawnej w celu wyświetlenia kosztów platformy Azure według zasobów

W tej sekcji utworzysz tabelę przestawną, za pomocą której będzie można rozwiązywać problemy z ogólnym użyciem platformy Azure. Przykładowa tabela może ułatwić zbadanie, która usługa zużywa najwięcej zasobów. Możesz też wyświetlić zasoby, które generują najwięcej kosztów, oraz prześledzić sposób naliczania opłat za usługę.

1. W obszarze Pola tabeli przestawnej przeciągnij pola **Service Name** i **Resource** do obszaru **Wiersze**. Umieść pole **Resource** poniżej pola **Service Name**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Następnie umieść pole **Post-Tax Total** w obszarze **Wartości**. Zamiast tej kolumny można również użyć kolumny Consumed Quantity, aby uzyskać informacje o jednostkach zużycia i transakcjach. Mogą to być na przykład gigabajty lub godziny. Lub liczba transakcji zamiast kosztów w różnych walutach, takich jak USD, EUR i INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Masz teraz pulpit nawigacyjny do uogólnionego badania zużycia. Aby uzyskać konkretną usługę, można użyć filtru i różnych opcji filtrowania w tabeli przestawnej.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Aby odfiltrować drugi poziom w tabeli przestawnej, na przykład zasób, wybierz element drugiego poziomu w tabeli.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Aby zastosować dodatkowe filtry, można dodać pola **SubscriptionID** oraz **Customer Company Name** do obszaru **Filtry** i wybrać żądany zakres.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Tworzenie tabeli przestawnej w celu wyświetlenia użycia platformy Azure według daty

W tej sekcji utworzysz tabelę przestawną, za pomocą której będzie można rozwiązywać problemy z ogólnym użyciem platformy Azure według zużytej ilości i daty. Warto zidentyfikować najwyższe koszty według daty i usługi. Możesz też wyświetlić zasoby, które generują najwięcej kosztów, oraz prześledzić sposób naliczania opłat za usługę.

Plik uzgodnień zawiera dwie tabele. Jedna z nich znajduje się u góry dokumentu (tabela główna), a druga w dolnej części dokumentu. Druga tabela zawiera podobne informacje, ale nie obejmuje szczegółowych informacji dotyczących cen ani kosztów. Zawiera jednak datę użycia i zużytą ilość.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Aby utworzyć tabelę programu Excel z informacjami pochodzącymi z dolnej części pliku uzgodnień, wykonaj kroki opisane w sekcji [Pobieranie i formatowanie danych](#get-the-data-and-format-it).
1. Gdy tabela będzie gotowa i będziesz mieć arkusz tabeli przestawnej, wykonaj czynności opisane w sekcji create-pivot-table-to-view-azure-costs-by-resources, aby przygotować pulpit nawigacyjny. Zamiast korzystać z pola Post-Tax total, umieść pole **Consumed quantity** w obszarze **Wartości**.
1. Dodaj kolumnę **Usage Date** do sekcji kolumn. Tabela przestawna powinna wyglądać podobnie jak na poniższym przykładzie.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Masz teraz pulpit nawigacyjny umożliwiający prześledzenie użycia według daty. Poszczególne miesiące można rozwijać, wybierając symbol **+** .

Na pulpicie nawigacyjnym jest wyświetlana zużyta ilość w jednostkach, takich jak gigabajty, godziny i transfery.

Aby wyświetlić cenę w poszczególnych dniach, można dodać pole **Resource GUID** do obszaru **Wiersze**. W górnej tabeli dodaj cenę jednostkową (**ListPrice**) dla zasobu. Pomnóż wartości z pola **ListPrice** przez wartości z pola **Consumed quantity**, aby obliczyć opłaty przed opodatkowaniem. Kwoty powinny być zgodne.

Niektóre zasoby (usługi) mają skalowane ceny według zużytej ilości. Na przykład niektóre zasoby mają wyższą cenę za zużyte pierwsze 100 GB i niższą cenę za kolejne zużyte gigabajty. Pamiętaj o skalowanych cenach, jeśli ręcznie obliczasz koszty.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Tworzenie tabeli przestawnej w celu wyświetlenia kosztu konkretnego zasobu

Pojedynczy zasób może spowodować naliczenie różnych opłat za różne usługi. Na przykład maszyna wirtualna może spowodować naliczenie opłat za zasoby obliczeniowe, licencjonowanie systemu operacyjnego, przepustowość (transfery danych), użycie wystąpienia zarezerwowanego i magazyn dla migawek. Jeśli chcesz poznać ogólne użycie określonych zasobów, w poniższych krokach opisano proces tworzenia pulpitu nawigacyjnego umożliwiającego wyświetlenie ogólnego użycia za pomocą plików użycia.

Pliki uzgodnień nie zawierają szczegółów dotyczących zasobów. Dlatego należy użyć zagregowanego pliku użycia. Skontaktuj się z [pomocą techniczną ds. rozliczeń platformy Azure](https://go.microsoft.com/fwlink/?linkid=2083458), aby uzyskać zagregowany plik użycia dla Twojej subskrypcji. Zagregowane pliki są generowane na poziomie subskrypcji. Niesformatowane dane są podobne do tych z poniższego przykładu.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

Plik zawiera następujące kolumny.

- **UsageStart** i **UsageEnd** — data dla każdego elementu wiersza (poszczególne jednostki użycia). Mogą to być na przykład poszczególne dni.
- **MeteredResourceID** — wartości z tej kolumny odpowiadają identyfikatorowi miernika na platformie Azure.
- **Properties** — zawiera identyfikator wystąpienia (nazwę zasobu) i inne szczegóły, na przykład lokalizację.
- **Quantity** — zużyta ilość w pliku uzgodnień.

1. Wybierz pierwsze pole w tabeli. **PartnerID**.  
1. Naciśnij klawisze Ctrl + Shift + Strzałka w dół, a następnie Ctrl + Shift + Strzałka w prawo, aby zaznaczyć wszystkie informacje w tabeli.
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela**. W polu Tworzenie tabeli wybierz pozycję **Moja tabela ma nagłówki**, a następnie wybierz przycisk **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" :::
1. W górnym menu wybierz pozycję **Wstawianie** > **Tabela przestawna**, a następnie wybierz przycisk **OK**. Spowoduje to utworzenie nowego arkusza w pliku i przejście do obszaru tabeli przestawnej po prawej stronie arkusza.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Następnie dodaj pole **MeteredResourceID** do obszaru **Wiersze**, a pole **Quantity** do obszaru **Wartości**. Wyniki przedstawiają ogólne informacje o użyciu. Aby uzyskać dodatkowe informacje, umieść pole **UsageEndDateTime** w obszarze **Kolumny**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Przykład przedstawiający niesformatowane dane w programie Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Aby wyświetlić ogólny raport, dodaj pole **Properties** do obszaru **Wiersze** pod pozycją **MeteredResourceID**. Pulpit nawigacyjny do śledzenia użycia jest już gotowy.
1. Aby filtrować według określonego zasobu, dodaj pole **Properties** do obszaru **Filtry** i wybierz odpowiednie użycie. Aby znaleźć nazwę zasobu, możesz użyć funkcji wyszukiwania.
    Aby wyświetlić koszt zasobu, znajdź łączną zużytą ilość i pomnóż tę wartość przez cenę. Cena jest określona dla każdego identyfikatora GUID zasobu (MeteredResourceID). Jeśli zasób korzysta z kilku identyfikatorów MeteredResourceID, należy zanotować łączną wartość dla każdego identyfikatora.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cost Management dla partnerów](../costs/get-started-partners.md).