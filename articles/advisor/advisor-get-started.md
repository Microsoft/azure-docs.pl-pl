---
title: Wprowadzenie do usługi Azure Advisor
description: Rozpocznij pracę z Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85117866"
---
# <a name="get-started-with-azure-advisor"></a>Wprowadzenie do usługi Azure Advisor

Dowiedz się, jak uzyskać dostęp do usługi Advisor za pomocą Azure Portal, uzyskać rekomendacje i zaimplementować zalecenia.

> [!NOTE]
> Azure Advisor automatycznie uruchamiane w tle w celu znalezienia nowo utworzonych zasobów. Udostępnienie zaleceń dotyczących tych zasobów może potrwać do 24 godzin.

## <a name="get-recommendations"></a>Pobierz zalecenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W lewym okienku kliknij pozycję **Advisor**.  Jeśli usługa Advisor nie jest widoczna w okienku po lewej stronie, kliknij pozycję **wszystkie usługi**.  W okienku menu usługi w obszarze **monitorowanie i zarządzanie** kliknij pozycję **Advisor**. Zostanie wyświetlony pulpit nawigacyjny usługi Advisor.

   ![Azure Advisor dostępu przy użyciu Azure Portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Na pulpicie nawigacyjnym usługi Advisor zostanie wyświetlone podsumowanie zaleceń dla wszystkich wybranych subskrypcji.  Możesz wybrać subskrypcje, do których mają być wyświetlane zalecenia, przy użyciu listy rozwijanej filtru subskrypcji.

1. Aby uzyskać zalecenia dotyczące określonej kategorii, kliknij jedną z kart: **niezawodność**, **zabezpieczenia**, **wydajność** lub **koszt**. 

   ![Pulpit nawigacyjny Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Pobierz szczegóły rekomendacji i zaimplementuj rozwiązanie

Możesz wybrać rekomendację w usłudze Advisor, aby wyświetlić dodatkowe szczegóły, takie jak akcje rekomendacji i zasoby, których dotyczy problem, i wdrożyć rozwiązanie do zalecenia.  

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

1. Wybierz kategorię rekomendacji, aby wyświetlić listę zaleceń w tej kategorii, lub wybierz kartę **wszystkie** , aby wyświetlić wszystkie zalecenia.

1. Kliknij zalecenie, które chcesz przejrzeć szczegółowo.

1. Zapoznaj się z informacjami na temat zalecenia i zasobów, których dotyczy zalecenie.

1. Kliknij **zalecaną akcję** , aby zaimplementować zalecenie.

## <a name="filter-recommendations"></a>Zaleceń filtru

Możesz filtrować zalecenia, aby przejść do szczegółów najważniejszych dla Ciebie.  Można filtrować według subskrypcji, typu zasobu lub stanu rekomendacji.  

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

1. Użyj listy rozwijanej na pulpicie nawigacyjnym klasyfikatora, aby filtrować według subskrypcji, typu zasobu lub stanu rekomendacji.

    ![Wyszukiwanie w usłudze Advisor — kryteria filtrowania](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Odłożenie lub Odrzuć rekomendacje

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

1. Przejdź do zalecenia, które chcesz odłożyć lub odrzucić.

1. Kliknij zalecenie.

1. Kliknij przycisk **Odłóż**. 

1. Określ przedział czasu odroczenia lub wybierz pozycję **nigdy nie** należy odrzucić zalecenia.

## <a name="exclude-subscriptions-or-resource-groups"></a>Wyklucz subskrypcje lub grupy zasobów

Mogą istnieć grupy zasobów lub subskrypcje, dla których nie ma potrzeby otrzymywania zaleceń klasyfikatora — na przykład zasobów "test".  Usługę Advisor można skonfigurować tak, aby generować tylko zalecenia dotyczące określonych subskrypcji i grup zasobów.

> [!NOTE]
> Aby dołączyć lub wykluczyć subskrypcję lub grupę zasobów z usługi Advisor, musisz być właścicielem subskrypcji.  Jeśli nie masz wymaganych uprawnień dla subskrypcji lub grupy zasobów, opcja dołączania lub wykluczania jest wyłączona w interfejsie użytkownika.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

1. Na pasku akcji kliknij pozycję **Konfiguruj** .

1. Usuń zaznaczenie wszystkich subskrypcji lub grup zasobów, dla których nie chcesz otrzymywać zaleceń usługi Advisor.

    ![Przykład konfigurowania zasobów usługi Advisor](./media/advisor-get-started/advisor-configure-resources.png)

1. Następnie kliknij przycisk **Apply** (Zastosuj).

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurowanie rekomendacji o niskim użyciu maszyny wirtualnej

Ta procedura służy do konfigurowania średniej reguły użycia procesora CPU dla zalecenia dotyczącego niskiego użycia maszyn wirtualnych.

Usługa Advisor monitoruje użycie maszyn wirtualnych przez 7 dni, a następnie identyfikuje maszyny wirtualne o niskiej przepustowości. Maszyny wirtualne są uznawane za niskie wykorzystanie, jeśli ich użycie procesora CPU wynosi 5% lub mniej, a ich użycie sieci jest mniejsze niż 2% lub jeśli bieżące obciążenie może być dostosowane do mniejszego rozmiaru maszyny wirtualnej.

Jeśli chcesz bardziej agresywnie identyfikować maszyny wirtualne o niskim zużyciu, możesz dostosować średnią regułę użycia procesora CPU dla każdej subskrypcji.  Reguła użycia procesora CPU może być ustawiona na 5%, 10%, 15% lub 20%.

> [!NOTE]
> Aby dostosować średnią regułę użycia procesora CPU do identyfikowania maszyn wirtualnych o niskim zużyciu, musisz być *właścicielem* subskrypcji.  Jeśli nie masz wymaganych uprawnień dla subskrypcji lub grupy zasobów, opcja dołączania lub wykluczania zostanie wyłączona w interfejsie użytkownika. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

1. Na pasku akcji kliknij pozycję **Konfiguruj** .

1. Kliknij kartę **reguły** .

1. Wybierz subskrypcje, dla których chcesz dostosować średnią regułę użycia procesora CPU, a następnie kliknij przycisk **Edytuj**.

1. Wybierz żądaną wartość średniego użycia procesora CPU, a następnie kliknij przycisk **Zastosuj**.

1. Kliknij przycisk **Odśwież zalecenia** , aby zaktualizować istniejące zalecenia, aby użyć nowej zasady średniego użycia procesora CPU. 

   ![Przykład konfigurowania reguł rekomendacji usługi Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Pobierz zalecenia

Program Advisor umożliwia pobranie podsumowania zaleceń.  Zalecenia można pobrać w postaci pliku PDF lub pliku CSV.  Pobranie zaleceń pozwala łatwo udostępniać współpracownikom lub przeprowadzać własne analizy na podstawie danych rekomendacji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

1. Kliknij pozycję **Pobierz jako wolumin CSV** lub **Pobierz jako plik PDF** na pasku akcji.

Opcja pobierania uwzględnia wszystkie filtry, które zostały zastosowane do pulpitu nawigacyjnego usługi Advisor.  W przypadku wybrania opcji pobierania podczas wyświetlania określonej kategorii lub rekomendacji rekomendacji pobrane podsumowanie zawiera informacje dotyczące tej kategorii lub rekomendacji. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Advisor, zobacz:

- [Wprowadzenie do usługi Azure Advisor](advisor-overview.md)
- [Zalecenia dotyczące niezawodności usługi Advisor](advisor-high-availability-recommendations.md)
- [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
- [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
- [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
- [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
