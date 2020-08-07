---
title: W jaki sposób jest stosowany rabat za wcześniejszy zakup usługi Azure Databricks
description: Dowiedz się, w jaki sposób rabat za wcześniejszy zakup usługi Azure Databricks zostanie zastosowany do Twojego użycia. Tych usług Databricks można używać w dowolnym czasie w okresie zakupu.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 380c4c7fdcccf45d83adaf355c2cc12da0b327fc
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460324"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>W jaki sposób jest stosowany rabat za wcześniejszy zakup usługi Azure Databricks

Możesz korzystać z zakupionych wcześniej jednostek zatwierdzeń usługi Azure Databricks (DBCU) w dowolnym momencie w okresie zakupu. Każde użycie usługi Azure Databricks powoduje automatyczne odjęcie wcześniej zakupionych jednostek DBCU.

W odróżnieniu od maszyn wirtualnych wcześniej zakupione jednostki nie wygasają co godzinę. Można z nich skorzystać w dowolnym momencie w okresie zakupu. Nie trzeba ponownie wdrażać ani przypisywać wcześniej zakupionego planu do obszarów roboczych usługi Azure Databricks na potrzeby użycia, aby uzyskać rabaty za wcześniejszy zakup.

Rabat za wcześniejszy zakup dotyczy tylko użycia jednostek usługi Azure Databricks (DBU). Pozostałe opłaty, na przykład za obliczenia, magazyn i sieć, są naliczane osobno.

## <a name="pre-purchase-discount-application"></a>Stosowanie rabatu za wcześniejszy zakup

Wcześniejszy zakup w usłudze Databricks jest stosowany do wszystkich obciążeń i warstw usługi Databricks. Można traktować tę opcję jako pulę opłaconych z góry jednostek zatwierdzeń usługi Databricks. Użycie jest odejmowane od tej puli niezależnie od obciążenia i warstwy. Użycie jest odejmowane zgodnie z poniższym współczynnikiem:

| **Obciążenie** | **Współczynnik naliczania jednostek DBU — warstwa Standardowa** | **Współczynnik naliczania jednostek DBU — warstwa Premium** |
| --- | --- | --- |
| Analiza danych | 0,4 | 0,55 |
| Inżynieria danych | 0,15 | 0,30 |
| Inżynieria danych (lekka) | 0,07 | 0,22 |

Na przykład w przypadku użycia obciążenia Analiza danych w warstwie Standardowa od zakupionych wcześniej jednostek zatwierdzeń usługi Databricks odejmuje się 0,4 jednostki. W przypadku użycia obciążenia Inżynieria danych (lekka) w warstwie Standardowa od zakupionych wcześniej jednostek zatwierdzeń usługi Databricks odejmuje się 0,07 jednostki.

## <a name="determine-plan-use"></a>Określanie użycia planu

Aby określić użycie planu jednostek DBCU, w witrynie Azure Portal przejdź do obszaru **Rezerwacje** i kliknij zakupiony plan usługi Databricks. Zostanie wyświetlone dotychczasowe użycie i liczba pozostałych jednostek. Aby uzyskać więcej informacji na temat określania użycia wystąpień zarezerwowanych, zobacz artykuł [Wyświetlanie użycia wystąpień zarezerwowanych](reservation-apis.md#see-reservation-usage).

## <a name="how-discount-application-shows-in-usage-data"></a>Jak zastosowanie rabatu jest wyświetlane w danych dotyczących użycia

Jeśli do Twojego użycia usługi Databricks jest stosowany rabat za wcześniejszy zakup, w danych dotyczących użycia są wyświetlane zerowe opłaty za użycie na żądanie. Aby uzyskać więcej informacji na temat kosztów i użycia wystąpień zarezerwowanych, zobacz [Pobieranie kosztów i użycia wystąpień zarezerwowanych w ramach umowy Enterprise Agreement](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz temat [Manage Azure Reservations](manage-reserved-vm-instance.md) (Zarządzanie wystąpieniami zarezerwowanymi na platformie Azure).
- Aby dowiedzieć się więcej na temat oszczędności związanych z wcześniejszym zakupem usługi Azure Databricks, zobacz [Optymalizowanie kosztów usługi Azure Databricks dzięki wcześniejszemu zakupowi](prepay-databricks-reserved-capacity.md).
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
  - [Zarządzanie rezerwacjami na platformie Azure](manage-reserved-vm-instance.md)
  - [Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem](understand-reserved-instance-usage.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
