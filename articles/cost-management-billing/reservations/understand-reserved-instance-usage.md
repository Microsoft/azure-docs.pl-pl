---
title: Użycie rezerwacji platformy Azure dla pojedynczej subskrypcji
description: Dowiedz się, jak odczytywać dane dotyczące użycia, aby zrozumieć, w jaki sposób zastosowana została rezerwacja platformy Azure dla indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 2b00c90abce915ae6fd645cbcce3536870609558
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447877"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Informacje na temat użycia rezerwacji platformy Azure dla indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem

Użyj pozycji ReservationId ze [strony Rezerwacja](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) oraz pliku użycia z [portalu Konta platformy Azure](https://account.azure.com), aby obliczyć użycie rezerwacji.

Jeśli jesteś klientem z umową Enterprise Agreement, zobacz [Informacje na temat użycia rezerwacji w przypadku rejestracji Enterprise.](understand-reserved-instance-usage-ea.md)

W tym artykule przyjęto założenie, że rezerwacja jest stosowana do pojedynczej subskrypcji. Jeśli rezerwacja jest stosowana do więcej niż jednej subskrypcji, korzyść z rezerwacji może obejmować wiele plików CSV.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Użycie zarezerwowanych wystąpień maszyn wirtualnych

W poniższych sekcjach założono, że uruchomiono maszynę wirtualną Standardowa_DS1_v2 z systemem Windows w regionie Wschodnie stany USA, a informacje dotyczące zarezerwowanego wystąpienia maszyny wirtualnej wyglądają jak w poniższej tabeli:

| Pole | Wartość |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Liczba |1|
|SKU | Standardowa_DS1_v2|
|Region | eastus |

Część sprzętowa maszyny wirtualnej jest uwzględniona, ponieważ wdrożona maszyna wirtualna jest zgodna z atrybutami rezerwacji. Aby zobaczyć, jakie oprogramowanie systemu Windows nie jest objęte zarezerwowanym wystąpieniem maszyny wirtualnej, zobacz [Koszty oprogramowania systemu Windows zarezerwowanych wystąpień maszyn wirtualnych platformy Azure](reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Sekcja zestawienia w pliku CSV dla maszyn wirtualnych

Ta sekcja pliku CSV pokazuje łączne użycie rezerwacji. W polu **Podkategoria miernika** zastosuj filtr zawierający wartość **„Reservation-”** . Zostaną wyświetlone informacje podobne do tych na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający odfiltrowane szczegółowe informacje o użyciu rezerwacji i opłaty](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Wiersz **Reservation-Base VM** zawiera łączną liczbę godzin, które są objęte rezerwacją. Ten wiersz zawiera wartość 0,00 USD, ponieważ rezerwacja obejmuje go. Wiersz **Reservation-Windows Svr (1 Core)** obejmuje koszty oprogramowania systemu Windows.

### <a name="daily-usage-section-of-csv-file"></a>Sekcja dziennego użycia w pliku CSV

Filtruj według pozycji **Dodatkowe informacje** i wpisz tekst **Identyfikator rezerwacji**. Poniższy zrzut ekranu przedstawia pola powiązane z rezerwacją.

![Zrzut ekranu przedstawiający szczegółowe informacje dotyczące dziennego użycia i opłaty](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. Pozycja **ReservationId** w polu **Dodatkowe informacje** to rezerwacja zastosowana do maszyny wirtualnej.
2. Pozycja **ConsumptionMeter** jest identyfikatorem miernika dla maszyny wirtualnej.
3. Wiersz **podkategorii miernika** **Reservation-Base VM** reprezentuje koszt 0 USD w sekcji zestawienia. Koszt uruchomienia tej maszyny wirtualnej jest już opłacony przez rezerwację.
4. **Identyfikator miernika** jest identyfikatorem miernika rezerwacji. Koszt tego miernika wynosi 0 USD. Ten identyfikator miernika pojawia się dla każdej maszyny wirtualnej, która jest uprawniona do rabatu na rezerwację.
5. Maszyna wirtualna Standardowa_DS1_v2 ma jeden procesor wirtualny i jest wdrażana bez korzyści użycia hybrydowego platformy Azure. W związku z tym miernik ten obejmuje dodatkową opłatę za oprogramowanie systemu Windows. Aby znaleźć miernik odpowiadający maszynie wirtualnej serii D z 1 rdzeniem, zobacz [Koszty oprogramowywania systemu Windows zarezerwowanych wystąpień maszyn wirtualnych platformy Azure](reserved-instance-windows-software-costs.md). Jeśli masz korzyść użycia hybrydowego platformy Azure, ta dodatkowa opłata nie zostanie zastosowana.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>Użycie dla rezerwacji usług SQL Database i Cosmos DB

W poniższych sekcjach wykorzystano usługę Azure SQL Database jako przykładu do opisania raportu użycia. Możesz użyć tych samych kroków, aby uzyskać użycie dla usługi Azure Cosmos DB.

Załóżmy, że uruchomiono usługę SQL Database Gen 4 w regionie Wschodnie stany USA, a informacje dotyczące rezerwacji wyglądają jak w poniższej tabeli:

| Pole | Wartość |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Liczba |2|
|Product (Produkt)| SQL Database Gen 4 (2 rdzenie)|
|Region | eastus |

### <a name="statement-section-of-csv-file"></a>Sekcja zestawienia w pliku CSV

Filtruj według nazwy miernika **Użycie wystąpień zarezerwowanych** i wybierz wymaganą **kategorię miernika** — Azure SQL Database lub Azure Cosmos DB. Zostaną wyświetlone informacje podobne do tych na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawia wpis kategorii miernika.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

Wiersz **Reserved Instance Usage** (Użycie wystąpień zarezerwowanych) zawiera łączną liczbę godzin podstawowych objętych rezerwacją. Stawka dla tego wiersza wynosi 0 USD, ponieważ koszt został pokryty przez rezerwację.

### <a name="detail-section-of-csv-file"></a>Sekcja szczegółów w pliku CSV

Filtruj według pozycji **Dodatkowe informacje** i wpisz tekst **Identyfikator rezerwacji**. Poniższy zrzut ekranu przedstawia pola powiązane z rezerwacją pojemności zarezerwowanej usługi SQL Database.

![Zrzut ekranu przedstawia szczegóły pliku CSV dotyczące pojemności zarezerwowanej.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. Pozycja **ReservationId** w polu **Dodatkowe informacje** to rezerwacja pojemności zarezerwowanej usługi SQL Database stosowana do zasobów bazy danych SQL.
2. **ConsumptionMeter** jest identyfikatorem miernika dla zasobów usługi SQL Database.
3. **Identyfikator miernika** to miernik rezerwacji. Koszt tego miernika wynosi 0 USD. Wszystkie zasoby usługi SQL Database, które kwalifikują się do zastosowania rabatu na rezerwację, pokazują ten identyfikator miernika w pliku CSV.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../../azure-sql/database/reserved-capacity-overview.md)
- [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
- [Jak jest stosowany rabat na rezerwacje](../manage/understand-vm-reservation-charges.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
- [Windows software costs not included with Reservations (Koszty oprogramowania systemu Windows nieuwzględniane w przypadku rezerwacji)](reserved-instance-windows-software-costs.md)
