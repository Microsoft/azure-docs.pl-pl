---
title: Koszty rezerwacji platformy Azure — obciążenie zwrotne
description: Dowiedz się, jak wyświetlać koszty rezerwacji platformy Azure na potrzeby obciążenia zwrotnego.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 4059318e6b8052f3b0221c87e8a357cfc8679e44
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532760"
---
# <a name="charge-back-azure-reservation-costs"></a>Koszty rezerwacji platformy Azure — obciążenie zwrotne

Osoby pełniące rolę czytelnika rozliczeń dla umów Enterprise i umów z Klientem Microsoft mogą przeglądać dane o zamortyzowanych kosztach rezerwacji. Na podstawie tych danych o kosztach mogą wykonać obciążenie zwrotne wartości pieniężnej za subskrypcję, grupę zasobów, zasób lub tag na rzecz partnerów. W danych zamortyzowanych rzeczywista cena to obliczony proporcjonalnie godzinowy koszt rezerwacji. Jest to całkowity koszt użycia rezerwacji przez zasób w danym dniu.

Użytkownicy mający subskrypcję indywidualną mogą znaleźć dane o kosztach zamortyzowanych w pliku użycia. Jeśli zasób jest objęty rabatem za rezerwację, szczegóły rezerwacji będą widoczne w sekcji *AdditionalInfo* (Informacje dodatkowe) w pliku użycia. Aby uzyskać więcej informacji, zobacz [Pobieranie danych użycia z witryny Azure Portal](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Wyświetlanie danych użycia rezerwacji w celu pokazania kosztów i obciążenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do obszaru **Zarządzanie kosztami i rozliczenia** 
3. Wybierz **pozycję Analiza kosztów z** nawigacji po lewej stronie 
4. W obszarze **Koszt rzeczywisty** wybierz metrykę **Koszt zamortyzowany**.
5. Aby sprawdzić, które zasoby były używane z rezerwacją, zastosuj filtr **Rezerwacja** i wybierz rezerwacje.
6. Ustaw **Poziom szczegółowości** na **Miesięczny** lub **Dzienny**.
7. Ustaw typ wykresu **Tabela**.
8. Ustaw opcję **Grupuj według** na **Zasób**.

[![Przykład przedstawiający koszty zasobów rezerwacji, których można użyć do wykonania obciążenia zwrotnego](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Oto wideo przedstawiające sposób wyświetlania kosztów użycia rezerwacji na poziomie subskrypcji, grupy zasobów i zasobów w Azure Portal.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Pobierz dane do pokazania i załaduj ponownie
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do obszaru **Zarządzanie kosztami i rozliczenia** 
3. Wybieranie opcji **Eksportuj w** lewym okienku nawigacji 
4. Kliknij **przycisk** Dodaj
5. Wybierz pozycję Koszt zamortyzowany jako przycisk metryki i skonfiguruj eksport

Cena efektywna za użycie, które otrzymuje rabat na rezerwację, to proporcjonalny koszt rezerwacji (a nie zero). Pomaga to poznać wartość pieniężną użycia rezerwacji w ramach subskrypcji, grupy zasobów lub zasobu, a także może pomóc w obciążeniu wewnątrz firmy za użycie rezerwacji. Zestaw danych ma także nieużywane godziny rezerwacji. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Pobieranie danych użycia platformy Azure i rezerwacji przy użyciu interfejsu API

Można pobrać dane przy użyciu interfejsu API lub pobrać je z witryny Azure Portal.

Można wywołać [interfejs API szczegółów użycia](/rest/api/consumption/usagedetails/list), aby pobrać nowe dane. Aby uzyskać szczegółowe informacje na temat terminologii, zobacz [warunki użytkowania](../understand/understand-usage.md).

Oto przykład wywołania interfejsu API szczegółów użycia:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Aby uzyskać więcej informacji na temat parametrów {enrollmentId} i {billingPeriodId}, zobacz artykuł dotyczący interfejsu API [Usage Details – List](/rest/api/consumption/usagedetails/list) (Szczegóły użycia — lista).

Informacje przedstawione w poniższej tabeli dotyczące metryki i filtru mogą pomóc w rozwiązywaniu typowych problemów z rezerwacją.

| **Typ danych interfejsu API** | Akcja wywołania interfejsu API |
| --- | --- |
| **Wszystkie opłaty (użycie i zakupy)** | Zastąp parametr {metric} ciągiem ActualCost |
| **Użycie, któremu przyznano rabat rezerwacji** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} ciągiem properties/reservationId%20ne%20 |
| **Użycie, któremu nie przyznano rabatu rezerwacji** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} poleceniem properties/reservationId%20eq%20 |
| **Opłaty zamortyzowane (użycie i zakupy)** | Zastąp parametr {metric} ciągiem AmortizedCost |
| **Nieużywany raport rezerwacji** | Zastąp parametr {metric} ciągiem AmortizedCost<br><br>Zastąp parametr {filter} poleceniem properties/ChargeType%20eq%20'UnusedReservation' |
| **Zakupy rezerwacji** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} poleceniem properties/ChargeType%20eq%20'Purchase'  |
| **Zwroty** | Zastąp parametr {metric} ciągiem ActualCost<br><br>Zastąp parametr {filter} poleceniem properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Pobierz plik CSV użycia z nowymi danymi

Jeśli jesteś administratorem EA, możesz pobrać plik CSV zawierający nowe dane użycia z Azure Portal. Te dane nie są dostępne w portalu EA (ea.azure.com) — aby wyświetlić nowe dane, musisz pobrać plik użycia z witryny Azure Portal (portal.azure.com).

W witrynie Azure Portal przejdź do obszaru [Zarządzanie kosztami i rozliczenia](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Wybierz konto rozliczeniowe.
2. Kliknij pozycję **Użycie i opłaty**.
3. Kliknij pozycję **Pobierz**.  
![Przykład pokazujący, gdzie pobrać plik CSV z danymi użycia w witrynie Azure Portal](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. W **szczegółach użycia** wybierz pozycję **Dane zamortyzowanego użycia.**

Pobierane przez Ciebie pliki CSV zawierają koszty rzeczywiste i zamortyzowane.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat danych użycia rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Enterprise Agreement i Umowa z Klientem Microsoft koszty i użycie rezerwacji](understand-reserved-instance-usage-ea.md)
 
