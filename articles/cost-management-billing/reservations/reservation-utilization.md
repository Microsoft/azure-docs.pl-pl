---
title: Wyświetlanie wykorzystania rezerwacji platformy Azure
description: Dowiedz się, jak uzyskać szczegóły i wykorzystanie rezerwacji.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568948"
---
# <a name="view-reservation-utilization-after-purchase"></a>Wyświetlanie użycia rezerwacji po zakupie

Możesz wyświetlić procent wykorzystania rezerwacji i zasoby, które użyły rezerwacji w Azure Portal i w Cost Management Power BI aplikacji.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Wyświetlanie wykorzystania w witrynie Azure Portal dostępem RBAC platformy Azure

Aby wyświetlić wykorzystanie rezerwacji, musisz mieć dostęp RBAC platformy Azure do rezerwacji lub mieć podwyższony poziom dostępu, aby zarządzać wszystkimi subskrypcjami i grupami zarządzania platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do pozycji [Rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. Lista zawiera wszystkie rezerwacje, w których masz rolę właściciela lub czytelnika. Dla każdej rezerwacji pokazany jest ostatni znany procent użycia.
1. Wybierz pozycję procentu wykorzystania, aby wyświetlić historię wykorzystania i szczegóły. Poniższy klip wideo przedstawia przykład.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Wyświetlanie wykorzystania jako administrator rozliczeń

Administrator Enterprise Agreement (EA) lub administrator rozliczeń Umowa z Klientem Microsoft (MCA) może wyświetlić wykorzystanie z **Cost Management + Billing**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Cost Management + Billing**  >  **Rezerwacje.**
1. Wybierz pozycję procentu wykorzystania, aby wyświetlić historię wykorzystania i szczegóły.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Uzyskiwanie danych rezerwacji i wykorzystania przy użyciu interfejsów API, programu PowerShell i interfejsu wiersza polecenia

Wykorzystanie rezerwacji można [uzyskać przy użyciu](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) interfejsu API użycia wystąpienia zarezerwowanego.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Wyświetlanie rezerwacji i wykorzystania w usłudze Power BI

Istnieją dwie opcje dla Power BI użytkowników:

- Azure Cost Management dla Power BI Desktop — dane daty zakupu rezerwacji i wykorzystania są dostępne w [łączniku](/power-bi/desktop-connect-azure-cost-management)Azure Cost Management dla Power BI Desktop . Utwórz raporty za pomocą łącznika.
- Azure Cost Management Power BI App — wstępnie [utworzone raporty Azure Cost Management Power BI aplikacji,](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) które można dalej dostosowywać.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md).
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md).
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md).
- [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations).
