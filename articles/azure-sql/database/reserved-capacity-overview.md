---
title: Zapisz koszty obliczeń z zarezerwowaną pojemnością
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, jak kupić Azure SQL Database i zarezerwowaną pojemność wystąpienia zarządzanego SQL, aby zaoszczędzić na kosztach obliczeniowych.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: c02368b5713c0743cdca764275f76dda0e0926d2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119095"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Oszczędność kosztów zasobów przy użyciu zarezerwowanej pojemności — Azure SQL Database & wystąpienia zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Oszczędność pieniędzy dzięki Azure SQL Database i wystąpieniu zarządzanym SQL przez zatwierdzenie do rezerwacji zasobów obliczeniowych w porównaniu z cenami z płatność zgodnie z rzeczywistym użyciem. W przypadku zarezerwowanej pojemności użytkownik zobowiązuje się do użycia wystąpienia zarządzanego SQL Database i/lub SQL przez okres jednego lub trzech lat w celu uzyskania znacznego rabatu w kosztach obliczeniowych. Aby zakupić zastrzeżoną pojemność, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin.

Nie trzeba przypisywać rezerwacji do określonego SQL Database lub wystąpienia zarządzanego SQL. Takie korzyści są zgodne z istniejącymi wdrożeniami, które są już uruchomione lub które są nowo wdrożone. Zakup rezerwacji polega na przekazaniu użycia na koszty obliczeń przez okres jeden lub trzy lata. Po zakupieniu rezerwacji opłaty za obliczenia, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Rezerwacja nie obejmuje opłat za oprogramowanie, Sieć ani opłaty za magazyn, które są skojarzone z usługą. Na koniec okresu rezerwacji korzyści z rozliczeń wygaśnie, a SQL Database lub wystąpienie zarządzane SQL są rozliczane według ceny płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zobacz [ofertę pojemności zarezerwowanej](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Możesz kupić zastrzeżoną pojemność w [Azure Portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../../cost-management-billing/reservations/prepare-buy-reservation.md). Aby kupić pojemność zarezerwowaną:

- Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji. zarezerwowana pojemność.

Szczegółowe informacje na temat tego, w jaki sposób Klienci korporacyjni i klienci korzystający z płatnej zgodnie z rzeczywistym [użyciem są](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) obciążani opłatami za zakup rezerwacji [.](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-correct-size-before-purchase"></a>Określ prawidłowy rozmiar przed zakupem

Rozmiar rezerwacji powinien opierać się na łącznej ilości obliczeń używanych przez istniejące lub wkrótce wdrożone SQL Database lub wystąpienie zarządzane SQL w określonym regionie oraz przy użyciu tej samej warstwy wydajności i generowania sprzętu.

Załóżmy na przykład, że korzystasz z jednego ogólnego celu, 5 rdzeń – 16 rdzeń wirtualny elastycznej puli i dwóch baz danych o znaczeniu krytycznym dla firmy 5 rdzeń — 4 rdzeń wirtualny. Ponadto Załóżmy, że planujesz wdrożenie w następnym miesiącu, w ramach dodatkowego celu ogólnego przeznaczenia, 5 rdzeń – 16 rdzeń wirtualny elastyczną pulę, i jedną z 32 nich. Załóżmy również, że wiesz, że te zasoby będą potrzebne przez co najmniej 1 rok. W takim przypadku należy zakupić 32 (2x16) rdzeni wirtualnych, 1-letnią rezerwację dla pojedynczej bazy danych i elastycznego ogólnego przeznaczenia — 5 rdzeń i 40 (2x4 + 32) rdzeń wirtualny 1 roku dla pojedynczej bazy danych/elastycznej puli firmy krytyczne-5 rdzeń.

## <a name="buy-reserved-capacity"></a>Kupowanie pojemności zarezerwowanej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz pozycję **Dodaj** , a następnie w okienku **rezerwacje zakupu** wybierz pozycję **SQL Database** , aby zakupić nową rezerwację dla SQL Database.
4. Wypełnij pola wymagane. Istniejące bazy danych SQL (pojedyncze lub w puli) lub wystąpienia zarządzane SQL zgodne z wybranymi atrybutami kwalifikują się do uzyskania rabatu zarezerwowanej pojemności. Rzeczywista liczba baz danych SQL lub wystąpień zarządzanych przez program SQL, które pobiera rabat, zależą od wybranego zakresu i ilości.
    ![Zrzut ekranu przed przesłaniem zarezerwowanej pojemności](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

W poniższej tabeli opisano wymagane pola.

| Pole      | Opis|
|------------|--------------|
|Subskrypcja|Subskrypcja używana do regulowania rezerwacji zdolności produkcyjnych. W przypadku metody płatności w ramach subskrypcji jest naliczana opłata za pozostały koszt. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.|
|Zakres       |Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: <br/><br/>**Udostępniony**, Rabat rezerwacji rdzeń wirtualny jest stosowany do wystąpienia zarządzanego SQL Database lub SQL uruchomionego w dowolnej subskrypcji w kontekście rozliczania. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.<br/><br/>**Pojedyncza subskrypcja**— rabat zastrzeżeń rdzeń wirtualny jest stosowany do baz danych SQL lub wystąpień zarządzanych SQL w tej subskrypcji. <br/><br/>**Pojedynczej grupy zasobów**, Rabat rezerwacji jest stosowany do baz danych SQL lub wystąpień zarządzanych przez program SQL w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.|
|Region      |Region świadczenia usługi Azure objęty rezerwacją pojemności.|
|Typ wdrożenia|Typ zasobu SQL, dla którego chcesz kupić rezerwację.|
|Warstwa wydajności|Warstwa usługi dla baz danych SQL lub wystąpień zarządzanych przez program SQL Server. |
|Termin        |Jeden rok lub trzy lata.|
|Liczba    |Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji pojemności. Ilość to liczba rdzeni wirtualnych w wybranym regionie i warstwie wydajności platformy Azure, które są zarezerwowane i pobierają rabat rozliczeń. Na przykład w przypadku uruchamiania lub planowania uruchamiania wielu baz danych SQL o całkowitej pojemności obliczeniowej 5 rdzeń 16 rdzeni wirtualnych w regionie Wschodnie stany USA należy określić liczbę jako 16, aby zmaksymalizować korzyść dla wszystkich baz danych. |

1. Sprawdź koszt rezerwacji zdolności produkcyjnych w sekcji **koszty** .
1. Wybierz pozycję **Kup**.
1. Wybierz pozycję **Wyświetl to zastrzeżenie** , aby zobaczyć stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie w górę i w dół w warstwach i regionach wydajności bez utraty korzyści zarezerwowanej pojemności. Zarezerwowana pojemność zapewnia również elastyczność tymczasowego przenoszenia aktywnych baz danych SQL do i z elastycznych pul (w ramach tego samego regionu i warstwy wydajności) w ramach normalnych operacji, bez utraty korzyści zarezerwowanej pojemności. Przez utrzymywanie niezastosowanych buforów w rezerwacji można efektywnie zarządzać wzrostami wydajności bez przekraczania budżetu.

## <a name="limitation"></a>Ograniczenia

Nie można zarezerwować baz danych opartych na serwerach lub DTU (w warstwach Podstawowa, standardowa lub Premium) w SQL Database.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby baz danych SQL lub wystąpień zarządzanych przez program SQL, które pasują do zakresu rezerwacji i atrybutów. Zakres rezerwacji pojemności można zaktualizować za pomocą [Azure Portal](https://portal.azure.com), PowerShell, interfejsu wiersza polecenia lub interfejsu API.

Aby dowiedzieć się, jak zarządzać rezerwacją pojemności, zobacz [Zarządzanie zarezerwowaną pojemnością](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to jest Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Zarządzanie usługą Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Informacje na temat rabatu na rezerwacje platformy Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)
