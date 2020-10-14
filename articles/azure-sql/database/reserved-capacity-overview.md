---
title: Zapisz koszty obliczeń z zarezerwowaną pojemnością
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, jak kupić Azure SQL Database i zarezerwowaną pojemność wystąpienia zarządzanego SQL, aby zaoszczędzić na kosztach obliczeniowych.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: c1bedf56896332430c6f4b937aab37764a0c6a43
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058271"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Oszczędność kosztów zasobów przy użyciu zarezerwowanej pojemności — Azure SQL Database & wystąpienia zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Oszczędność pieniędzy dzięki Azure SQL Database i wystąpieniu zarządzanym SQL przez zatwierdzenie do rezerwacji zasobów obliczeniowych w porównaniu z cenami z płatność zgodnie z rzeczywistym użyciem. W przypadku zarezerwowanej pojemności użytkownik zobowiązuje się do użycia wystąpienia zarządzanego SQL Database i/lub SQL przez okres jednego lub trzech lat w celu uzyskania znacznego rabatu w kosztach obliczeniowych. Aby zakupić zastrzeżoną pojemność, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin.

Nie trzeba przypisywać rezerwacji do konkretnej bazy danych lub wystąpienia zarządzanego. Takie korzyści są zgodne z istniejącymi wdrożeniami, które są już uruchomione lub które są nowo wdrożone. Zakup rezerwacji polega na przekazaniu użycia na koszty obliczeń przez okres jeden lub trzy lata. Po zakupieniu rezerwacji opłaty za obliczenia, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. 

Rezerwacja dotyczy podstawowej i rozliczanej repliki obliczeniowej, ale nie obejmuje opłat za oprogramowanie, sieci lub magazyn związany z usługą. Na koniec okresu rezerwacji korzyści z rozliczeń wygaśnie, a baza danych lub wystąpienie zarządzane są rozliczane według ceny płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są automatycznie odnawiane. Aby uzyskać informacje o cenach, zobacz [ofertę pojemności zarezerwowanej](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Możesz kupić zastrzeżoną pojemność w [Azure Portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../../cost-management-billing/reservations/prepare-buy-reservation.md). Aby kupić pojemność zarezerwowaną:

- Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji. Zarezerwowana pojemność.

Aby uzyskać więcej informacji na temat sposobu, w jaki Klienci korporacyjni i klienci korzystający z płatnej zgodnie z rzeczywistym użyciem są obciążani opłatami za zakupy rezerwacji, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) i informacje [dotyczące użycia rezerwacji na platformie Azure dla subskrypcji z opcją płatność za](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)użycie.

> [!NOTE]
> Zakup zarezerwowanej pojemności nie powoduje wstępnego przydzielenia lub zarezerwowania określonych zasobów infrastruktury (maszyn wirtualnych lub węzłów) do użytku.

## <a name="determine-correct-size-before-purchase"></a>Określ prawidłowy rozmiar przed zakupem

Rozmiar rezerwacji powinien opierać się na łącznej liczbie obliczeń używanych przez istniejącą lub natychmiastowo wdrożoną bazę danych lub wystąpienie zarządzane w określonym regionie oraz przy użyciu tej samej warstwy wydajności i generowania sprzętu.

Załóżmy na przykład, że korzystasz z jednego ogólnego celu, 5 rdzeń – 16 rdzeń wirtualny elastycznej puli, a dwie bazy danych o krytycznym znaczeniu dla firmy 5 rdzeń – 4 rdzeń wirtualny. Ponadto Załóżmy, że planujesz wdrożenie w następnym miesiącu, a dodatkowa 5 rdzeń — 16 rdzeń wirtualny elastyczną i jedną z krytycznych 5 rdzeń firmowych — 32 rdzeń wirtualny. Załóżmy również, że wiesz, że te zasoby będą potrzebne przez co najmniej 1 rok. W takim przypadku należy zakupić 32 (2x16) rdzeni wirtualnych 1-letnią rezerwację dla pojedynczej bazy danych/elastycznego ogólnego przeznaczenia-5 rdzeń i 40 (2x4 + 32) rdzeń wirtualny 1-letnią rezerwację dla pojedynczej bazy danych/elastycznej puli o krytycznym znaczeniu dla firmy-5 rdzeń.

## <a name="buy-reserved-capacity"></a>Kupowanie pojemności zarezerwowanej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz pozycję **Dodaj** , a następnie w okienku **rezerwacje zakupu** wybierz pozycję **SQL Database** , aby zakupić nową rezerwację dla SQL Database.
4. Wypełnij pola wymagane. Istniejące bazy danych w SQL Database i wystąpieniu zarządzanym SQL, które pasują do wybranych atrybutów, kwalifikują się do uzyskania rabatu zarezerwowanej pojemności. Rzeczywista liczba baz danych lub wystąpień zarządzanych, które pobierają rabat, zależy od wybranego zakresu i ilości.

    ![Zrzut ekranu przed przesłaniem zarezerwowanej pojemności](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    W poniższej tabeli opisano wymagane pola.
    
    | Pole      | Opis|
    |------------|--------------|
    |Subskrypcja|Subskrypcja używana do regulowania rezerwacji zdolności produkcyjnych. W przypadku metody płatności w ramach subskrypcji jest naliczana opłata za pozostały koszt. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (Numer oferty MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.|
    |Zakres       |Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania pozycji <br/><br/>**Shared**Rdzeń wirtualny rabat rezerwacji jest stosowany do bazy danych lub wystąpienia zarządzanego uruchomionego w dowolnej subskrypcji w kontekście rozliczania. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.<br/><br/>**Pojedyncza subskrypcja**— rabat rezerwacji rdzeń wirtualny jest stosowany do baz danych lub wystąpień zarządzanych w tej subskrypcji. <br/><br/>**Pojedynczej grupy zasobów**, Rabat rezerwacji jest stosowany do wystąpień baz danych lub wystąpień zarządzanych w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.|
    |Region      |Region świadczenia usługi Azure objęty rezerwacją pojemności.|
    |Typ wdrożenia|Typ zasobu SQL, dla którego chcesz kupić rezerwację.|
    |Warstwa wydajności|Warstwa usług dla baz danych lub wystąpień zarządzanych. |
    |Okres        |Jeden rok lub trzy lata.|
    |Liczba    |Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji pojemności. Ilość to liczba rdzeni wirtualnych w wybranym regionie i warstwie wydajności platformy Azure, które są zarezerwowane i pobierają rabat rozliczeń. Na przykład w przypadku uruchamiania lub planowania uruchamiania wielu baz danych o całkowitej pojemności obliczeniowej 5 rdzeń 16 rdzeni wirtualnych w regionie Wschodnie stany USA należy określić liczbę jako 16, aby zmaksymalizować korzyść dla wszystkich baz danych. |

1. Sprawdź koszt rezerwacji zdolności produkcyjnych w sekcji **koszty** .
1. Wybierz pozycję **Kup**.
1. Wybierz pozycję **Wyświetl to zastrzeżenie** , aby zobaczyć stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie w górę i w dół w warstwach i regionach wydajności bez utraty korzyści zarezerwowanej pojemności. Zarezerwowana pojemność zapewnia również elastyczność tymczasowego przenoszenia gorących baz danych do i z elastycznych pul (w ramach tego samego regionu i warstwy wydajności) w ramach normalnych operacji, bez utraty korzyści zarezerwowanej pojemności. Utrzymując niezastosowany bufor w rezerwacji, można efektywnie zarządzać wzrostami wydajności bez przekraczania budżetu.

## <a name="limitation"></a>Ograniczenie

W SQL Database nie można zarezerwować baz danych opartych na jednostkach DTU (podstawowa, standardowa lub Premium).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby baz danych lub wystąpień zarządzanych, które pasują do zakresu rezerwacji i atrybutów. Zakres rezerwacji pojemności można zaktualizować za pomocą [Azure Portal](https://portal.azure.com), programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API.

Aby dowiedzieć się, jak zarządzać rezerwacją pojemności, zobacz [Zarządzanie zarezerwowaną pojemnością](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to jest Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Zarządzanie usługą Azure Reservations](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Informacje na temat rabatu na rezerwacje platformy Azure](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim](https://docs.microsoft.com/partner-center/azure-reservations)
