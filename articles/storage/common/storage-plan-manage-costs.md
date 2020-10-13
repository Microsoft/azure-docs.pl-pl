---
title: Planowanie i zarządzanie kosztami usługi Azure Blob Storage
description: Dowiedz się, jak planować i zarządzać kosztami usługi Azure Blob Storage za pomocą analizy kosztów w Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 75b464c140bfda6c3f3559d3bfdbe1e6bc2e7f24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760740"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Planowanie i zarządzanie kosztami usługi Azure Blob Storage

Ten artykuł ułatwia planowanie i zarządzanie kosztami usługi Azure Blob Storage. Najpierw Oszacuj koszty przy użyciu kalkulatora cen platformy Azure. Po utworzeniu konta magazynu Zoptymalizuj konto, aby płacisz tylko za to, czego potrzebujesz. Funkcje zarządzania kosztami umożliwiają ustawianie budżetów i monitorowanie kosztów. Możesz również sprawdzić przewidywane koszty i monitorować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie.

Należy pamiętać, że koszty usługi BLOB Storage są jedynie częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak oszacować koszty magazynu obiektów blob i zarządzać nimi, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm. Po zapoznaniu się z zarządzaniem kosztami usługi BLOB Storage możesz zastosować podobne metody, aby zarządzać kosztami wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="estimate-costs"></a>Szacowanie kosztów

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed utworzeniem i rozpoczęciem transferu danych do konta usługi Azure Storage.

1. Na stronie [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz kafelek **konta magazynu** .

2. Przewiń w dół stronę i Znajdź sekcję **konta magazynu** oszacowania.

3. Wybierz opcje z listy rozwijanej. 

   Gdy modyfikujesz wartość tych list rozwijanych, oszacowanie kosztów zostanie zmienione. To oszacowanie pojawia się w górnym rogu, a także u dołu oszacowania. 
    
   ![Monitorowanie kosztów za pomocą okienka analiza kosztów](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   W miarę zmieniania wartości listy rozwijanej **Typ** są również wyświetlane inne opcje, które pojawiają się w tym arkuszu. Skorzystaj z linków w sekcji **więcej informacji** , aby dowiedzieć się więcej na temat tego, co oznacza każda opcja i jak te opcje wpływają na cenę operacji związanych z magazynem. 

4. Zmodyfikuj pozostałe opcje, aby zobaczyć ich wpływ na oszacowanie.

## <a name="optimize-costs"></a>Optymalizowanie kosztów

Rozważ użycie tych opcji, aby obniżyć koszty. 

- Rezerwacja pojemności magazynu

- Organizowanie danych w warstwy dostępu

- Automatycznie przenoś dane między warstwami dostępu

Ta sekcja zawiera więcej szczegółów. 

#### <a name="reserve-storage-capacity"></a>Rezerwacja pojemności magazynu

Możesz zaoszczędzić oszczędności związane z magazynowaniem danych obiektów BLOB w usłudze Azure Storage. Zarezerwowana pojemność usługi Azure Storage oferuje rabat na wydajność blokowych obiektów blob oraz Azure Data Lake Storage Gen2 danych na kontach magazynu w warstwie Standardowa po zatwierdzeniu do rezerwacji przez jeden rok lub trzy lata. Rezerwacja zapewnia stałą ilość miejsca w magazynie w przypadku rezerwacji. Zarezerwowana pojemność usługi Azure Storage może znacząco obniżyć koszty pojemności dla blokowych obiektów blob i danych Azure Data Lake Storage Gen2. 

Aby dowiedzieć się więcej, zobacz [Optymalizacja kosztów magazynu obiektów BLOB przy użyciu zarezerwowanej pojemności](https://docs.microsoft.com/azure/storage/blobs/storage-blob-reserved-capacity).

#### <a name="organize-data-into-access-tiers"></a>Organizowanie danych w warstwy dostępu

Możesz obniżyć koszty, umieszczając dane obiektów BLOB w najbardziej ekonomicznych warstwach dostępu. Wybieraj spośród trzech warstw, które zaprojektowano w celu optymalizacji kosztów związanych z użyciem danych. Na przykład warstwa *gorąca* ma wyższy koszt magazynowania, ale obniża koszty dostępu. W związku z tym, jeśli planujesz uzyskać dostęp do danych często, warstwa gorąca może być najbardziej wydajnym wyborem. Jeśli planujesz uzyskiwać dostęp do danych rzadziej, warstwa *zimna* lub *archiwum* może być najbardziej sensowna, ponieważ zwiększa koszt uzyskiwania dostępu do danych przy jednoczesnym zmniejszeniu kosztów przechowywania danych.    

Aby dowiedzieć się więcej, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal).

#### <a name="automatically-move-data-between-access-tiers"></a>Automatycznie przenoś dane między warstwami dostępu

Zasady zarządzania cyklem życia umożliwiają okresowe przenoszenie danych między warstwami w celu zaoszczędzenia najbardziej pieniędzy. Te zasady mogą przenosić dane do programu przy użyciu określonych reguł. Można na przykład utworzyć regułę, która przenosi obiekty blob do warstwy archiwum, jeśli ten obiekt BLOB nie został zmodyfikowany w ciągu 90 dni. Tworząc zasady dostosowujące warstwę dostępu do danych, możesz zaprojektować najtańsze opcje magazynu dla Twoich potrzeb.

Aby dowiedzieć się więcej, zobacz [Zarządzanie cyklem życia usługi Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) oraz alerty, które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. Mogą jednak mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usług platformy Azure, takimi jak koszt usługi Azure Storage, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów platformy Azure w usłudze Azure Storage, naliczane są koszty. Koszty jednostkowe użycia zasobów różnią się w zależności od interwału czasu (sekundy, minuty, godziny i dni) lub według użycia jednostek (bajty, megabajty itd.). Koszty są naliczane zaraz po rozpoczęciu korzystania z usługi Azure Storage. Koszty można zobaczyć w okienku [Analiza kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md) w Azure Portal.

Korzystając z analizy kosztów, można wyświetlać koszty usługi Azure Storage w wykresach i tabelach dla różnych interwałów czasu. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków i sprawdzaniu, gdzie mogły wystąpić nadwyżki. Jeśli utworzono budżety, możesz również łatwo zobaczyć miejsce ich przekroczenia.

>[!NOTE]
> Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../../cost-management-billing/costs/assign-access-acm-data.md).

Aby wyświetlić koszty usługi Azure Storage w analizie kosztów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Otwórz okno **Cost Management + rozliczanie** , wybierz pozycję **Zarządzanie kosztami** z menu, a następnie wybierz pozycję **Analiza kosztów**. Następnie można zmienić zakres dla określonej subskrypcji z listy rozwijanej **zakres** .

   ![Monitorowanie kosztów za pomocą okienka analiza kosztów](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Aby wyświetlić tylko koszty usługi Azure Storage, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz pozycję **Magazyn** z listy. 

   Oto przykład przedstawiający koszty tylko usługi Azure Storage:

   ![Monitorowanie kosztów magazynu za pomocą okienka analiza kosztów](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Są również wyświetlane koszty według regionów świadczenia usługi Azure i grup zasobów. Można również dodać inne filtry (na przykład: filtr, aby wyświetlić koszty dla określonych kont magazynu).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat sposobu działania cen w usłudze Azure Storage:

- [Cennik usługi Azure Storage — Omówienie](https://azure.microsoft.com/pricing/details/storage/)
- [Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej](../blobs/storage-blob-reserved-capacity.md)
