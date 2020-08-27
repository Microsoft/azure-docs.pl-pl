---
title: Zarządzanie budżetami platformy Cloudyn na platformie Azure
description: Ten artykuł pomoże Ci szybko utworzyć budżety i rozpocząć zarządzanie nimi na platformie Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: b7a26e7d94bbfea77bfc8121923377a086aef037
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690871"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Zarządzanie budżetami platformy Azure za pomocą platformy Cloudyn

Konfigurowanie budżetów i alertów na podstawie budżetu pomaga usprawnić zarządzanie chmurą i podnieść odpowiedzialność. Ten artykuł pomoże Ci szybko utworzyć budżety i rozpocząć zarządzanie nimi na platformie Cloudyn.

Jeśli masz konto Enterprise lub MSP, możesz użyć hierarchicznej struktury kosztów, aby przypisać miesięczne limity budżetu do różnych jednostek biznesowych, działów lub innych jednostek kosztów. Jeśli masz konto Premium, możesz użyć funkcji zarządzania budżetem, która zostanie następnie zastosowana do wszystkich wydatków w chmurze. Wszystkie budżety są przypisywane ręcznie.

Na podstawie przypisanych budżetów można ustawić alerty progowe w oparciu o wartość procentową używanego budżetu i zdefiniować ważność poszczególnych wartości progowych.

Raporty budżetu przedstawiają przypisany budżet. Użytkownicy mogą zobaczyć, kiedy ich wydatki przekraczają użycie w czasie lub są od niego niższe albo równe. Wybierając pozycję **Pokaż/Ukryj pola** u góry raportu budżetu, możesz wyświetlić koszty, budżet, koszty skumulowane i łączny budżet.

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji na temat budżetów w usłudze Cost Management, zobacz [Tworzenie budżetów i zarządzanie nimi](../costs/tutorial-acm-create-budgets.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="create-budgets"></a>Tworzenie budżetów

Podczas tworzenia budżetu należy ustawić go dla roku obrachunkowego i zastosować do określonej jednostki.

Aby utworzyć budżet i przypisać go do jednostki:

1. Przejdź do pozycji **Costs (Koszty)** &gt; **Cost Management (Zarządzanie kosztami)** &gt; **Budget (Budżet)** .
2. Na stronie Budget Management (Zarządzanie budżetem) w obszarze **Entities** (Jednostki) wybierz jednostkę, w której chcesz utworzyć budżet.
3. W ramach roku budżetowego wybierz rok, w którym chcesz utworzyć budżet.
4. Dla każdego miesiąca ustaw wartość budżetu. Gdy wszystko będzie gotowe, kliknij przycisk **Save** (Zapisz).
W tym przykładzie miesięczny budżet dla czerwca 2018 roku jest ustawiony na 135 000 USD. Łączny budżet dla roku to 1 615 000,00 USD.
![Tworzenie strony budżetu, na której ustawiany jest budżet dla każdego miesiąca](./media/manage-budgets/set-budget.png)


Aby zaimportować plik dla budżetu rocznego:

1. W obszarze **Actions** (Akcje) wybierz pozycję **Export** (Eksportuj), aby pobrać pusty szablon CSV, którego można użyć jako podstawy budżetu.
2. Wypełnij plik CSV pozycjami budżetu i zapisz go lokalnie.
3. W obszarze **Actions** (Akcje) wybierz pozycję **Import** (Importuj).
4. Wybierz zapisany plik, a następnie kliknij pozycję **OK**.

Aby wyeksportować ukończony budżet jako plik CSV, w obszarze **Actions** (Akcje) wybierz pozycję **Export** (Eksportuj), aby pobrać plik.

## <a name="view-budget-in-reports"></a>Wyświetlanie budżetu w raportach

Po zakończeniu budżet jest pokazywany w większości raportów Koszty w obszarze **Costs (Koszty)** &gt; **Cost Analysis (Analiza kosztów)** oraz w raporcie Koszt a budżet w czasie. Raporty możesz również zaplanować na podstawie progów budżetu przy użyciu pozycji **Actions** (Akcje).

Oto przykład raportu Analiza kosztów. Pokazuje on łączny budżet i koszt według obciążeń i typów użycia od początku roku.

![Przykładowy raport Analiza kosztów z budżetem](./media/manage-budgets/cost-analysis-budget-example.png)

W tym przykładzie zakładamy, że bieżąca data to 22 czerwca. Koszt w czerwcu 2018 roku wyniósł 71 611,28 USD w porównaniu do miesięcznego budżetu wynoszącego 135 000 USD. Koszt jest znacznie niższy niż miesięczny budżet, ponieważ zostało jeszcze osiem dni wydatków do końca miesiąca.

Innym sposobem wyświetlenia raportu jest porównanie kosztu skumulowanego z budżetem. Aby wyświetlić skumulowane koszty, w obszarze **Show/Hide Fields** (Pokaż/Ukryj pola) wybierz pozycję **Accumulated Cost** (Skumulowany koszt) i **Total Budget** (Łączny budżet). Oto przykład przedstawiający skumulowany koszt od początku roku.

![Przykład skumulowanego kosztu i łącznego budżetu pokazywanego w raporcie Koszt a budżet w czasie](./media/manage-budgets/accumulated-budget.png)

W przyszłości koszt skumulowany może przekroczyć budżet. Będzie to lepiej widoczne, jeśli zmienisz widok wykresu na wykres _liniowy_.

![Budżet pokazywany na wykresie liniowym w raporcie Koszt według miesięcy](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Tworzenie alertów budżetowych dla filtru

W poprzednim przykładzie można zobaczyć, że skumulowany koszt zbliża się do budżetu. Możesz tworzyć automatyczne alerty budżetowe, aby otrzymywać powiadomienia, gdy wydatki zbliżą się do budżetu lub przekroczą go. W zasadzie alert to zaplanowany raport z progiem. Metryki progu alertu budżetowego obejmują następujące elementy:

- Pozostały koszt a budżet — w celu określenia progu wartości walutowej
- Wartość procentowa kosztów a budżet — w celu określenia progu wartości procentowej

Spójrzmy na przykład.

W prawym górnym rogu raportu Koszty a budżet w czasie kliknij pozycję **Actions** (Akcje), a następnie wybierz pozycję **Schedule report** (Zaplanuj raport). Na karcie Threshold (Próg) wybierz metrykę progową. Na przykład **Cost percentage vs budget** (Wartość procentowa a budżet). Wybierz typ alertu i wprowadź wartość procentową budżetu. Jeśli chcesz otrzymać powiadomienie tylko raz, wybierz pozycję **Number of consecutive alerts** (Liczba kolejnych alertów) i wpisz wartość _1_. Kliknij pozycję **Zapisz**.

![Tworzenie alertu budżetowego w polu Zapisz lub Zaplanuj ten raport](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli zadania z pierwszego samouczka dotyczącego platformy Cloudyn nie zostały jeszcze wykonane, zapoznaj się z nim w temacie [Review usage and costs (Przeglądanie użycia i kosztów)](tutorial-review-usage.md).
- Dowiedz się więcej o [raportach dostępnych w usłudze Cloudyn](use-reports.md).
