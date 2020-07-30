---
title: Wyświetlanie transakcji zakupu i zwrotów za rezerwacje platformy Azure
description: Dowiedz się, jak wyświetlać transakcje zakupu i zwroty za rezerwacje platformy Azure.
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 988dd057834f6eb19dfd75a868c2893aefb61435
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290756"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Wyświetlanie transakcji zakupu i zwrotów za rezerwacje

Transakcje zakupu i zwroty za rezerwacje można wyświetlać na kilka różnych sposobów. Można użyć witryny Azure Portal, usługi Power BI lub interfejsów API REST.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Wyświetlanie transakcji dotyczących rezerwacji w witrynie Azure Portal

Administrator rozliczeń rejestracji Enterprise lub umowy z Klientem Microsoft może wyświetlać transakcje dotyczące rezerwacji w obszarze Zarządzanie kosztami i rozliczenia.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
1. Wybierz pozycję **Transakcje rezerwacji**.
1. Aby przefiltrować wyniki, wybierz pozycję **Przedział czasu**, **Typ** lub **Opis**.
1. Wybierz przycisk **Zastosuj**.

[![Zrzut ekranu przedstawiający transakcje dotyczące rezerwacji w witrynie Azure Portal](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Wyświetlanie transakcji dotyczących rezerwacji w usłudze Power BI

Administrator rozliczeń rejestracji Enterprise lub umowy z Klientem Microsoft może wyświetlać transakcje dotyczące rezerwacji w aplikacji Cost Management dla usługi Power BI.

1. Pobierz [aplikację Cost Management dla usługi Power BI](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Przejdź do raportu Zakupy wystąpień zarezerwowanych.

[![Przykład przedstawiający transakcje dotyczące rezerwacji](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Aby uzyskać więcej informacji, zobacz [Analizowanie kosztów za pomocą aplikacji Azure Cost Management usługi Power BI dla umów Enterprise Agreement (EA)](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

## <a name="use-apis-to-get-reservation-transactions"></a>Pobieranie transakcji dotyczących rezerwacji przy użyciu interfejsów API

Użytkownicy z umową Enterprise Agreement lub umową z Klientem Microsoft mogą pobierać dane transakcji dotyczących rezerwacji za pomocą [Interfejsu API listy transakcji dotyczących rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz temat [Manage Azure Reservations](manage-reserved-vm-instance.md) (Zarządzanie wystąpieniami zarezerwowanymi na platformie Azure).
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
  - [Zarządzanie rejestracjami platformy Azure](manage-reserved-vm-instance.md)
