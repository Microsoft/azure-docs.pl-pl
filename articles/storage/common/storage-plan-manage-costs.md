---
title: Planowanie i zarządzanie kosztami usługi Azure Storage
description: Dowiedz się, jak planować i zarządzać kosztami usługi Azure Storage za pomocą analizy kosztów w Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304526"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planowanie i zarządzanie kosztami usługi Azure Storage

W tym artykule opisano sposób planowania i zarządzania kosztami usługi Azure Storage. Najpierw należy skorzystać z kalkulatora cen platformy Azure, aby ułatwić planowanie kosztów magazynowania przed dodaniem zasobów. Po rozpoczęciu korzystania z zasobów usługi Azure Storage Użyj funkcji zarządzania kosztami w celu ustawienia budżetów i monitorowania kosztów. Możesz również przejrzeć przewidywane koszty i monitorować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie.

Należy pamiętać, że koszty usługi Azure Storage to tylko część miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami usługi Azure Storage, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm. Po zapoznaniu się z zarządzaniem kosztami usługi Azure Storage możesz zastosować podobne metody, aby zarządzać kosztami wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Oszacuj koszty przed utworzeniem konta usługi Azure Storage

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed utworzeniem i rozpoczęciem transferu danych do konta usługi Azure Storage.

1. Na stronie [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz kafelek **konta magazynu** .

2. Przewiń w dół stronę i Znajdź sekcję **konta magazynu** oszacowania.

3. Wybierz opcje z listy rozwijanej. 

   Gdy modyfikujesz wartość tych list rozwijanych, oszacowanie kosztów zostanie zmienione. To oszacowanie pojawia się w górnym rogu, a także u dołu oszacowania. 
    
   ![Monitorowanie kosztów za pomocą okienka analiza kosztów](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   W miarę zmieniania wartości listy rozwijanej **Typ** są również wyświetlane inne opcje, które pojawiają się w tym arkuszu. Skorzystaj z linków w sekcji **więcej informacji** , aby dowiedzieć się więcej na temat tego, co oznacza każda opcja i jak te opcje wpływają na cenę operacji związanych z magazynem. 

4. Zmodyfikuj pozostałe opcje, aby zobaczyć ich wpływ na oszacowanie.

## <a name="use-budgets-and-cost-alerts"></a>Używanie alertów dotyczących budżetu i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) oraz alerty, które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. Mogą jednak mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usług platformy Azure, takimi jak koszt usługi Azure Storage, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów platformy Azure w usłudze Azure Storage, naliczane są koszty. Koszty jednostkowe użycia zasobów różnią się w zależności od interwału czasu (sekundy, minuty, godziny i dni) lub według użycia jednostek (bajty, megabajty itd.). Koszty są naliczane zaraz po rozpoczęciu korzystania z usługi Azure Storage. Koszty można zobaczyć w okienku [Analiza kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md) w Azure Portal.

Korzystając z analizy kosztów, można wyświetlać koszty usługi Azure Storage w wykresach i tabelach dla różnych interwałów czasu. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków i sprawdzaniu, gdzie mogły wystąpić nadwyżki. Jeśli utworzono budżety, możesz również łatwo zobaczyć miejsce ich przekroczenia.

Aby wyświetlić koszty usługi Azure Storage w analizie kosztów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Otwórz okno **Cost Management + rozliczanie** , wybierz pozycję **Zarządzanie kosztami** z menu, a następnie wybierz pozycję **Analiza kosztów**. Następnie można zmienić zakres dla określonej subskrypcji z listy rozwijanej **zakres** .

   ![Monitorowanie kosztów za pomocą okienka analiza kosztów](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Aby wyświetlić tylko koszty usługi Azure Storage, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz pozycję **Magazyn** z listy. 

   Oto przykład przedstawiający koszty tylko usługi Azure Storage:

   ![Monitorowanie kosztów magazynu za pomocą okienka analiza kosztów](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Są również wyświetlane koszty według regionów świadczenia usługi Azure i grup zasobów.  

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat sposobu działania cen w usłudze Azure Storage:

- [Cennik usługi Azure Storage — Omówienie](https://azure.microsoft.com/pricing/details/storage/)
- [Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej](../blobs/storage-blob-reserved-capacity.md)
