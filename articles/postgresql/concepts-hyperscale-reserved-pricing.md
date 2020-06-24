---
title: Ceny zarezerwowanych obliczeń — Azure Database for PostgreSQL-Citus
description: Przedpłata za zasoby obliczeniowe Azure Database for PostgreSQL (Citus) z zarezerwowaną pojemnością
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85218034"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Przedpłata za zasoby obliczeniowe Azure Database for PostgreSQL (Citus) z zarezerwowaną pojemnością

Azure Database for PostgreSQL — funkcja Citus) teraz pomaga zaoszczędzić pieniądze dzięki wykorzystaniu zasobów obliczeniowych w porównaniu z cenami płatności zgodnie z rzeczywistym użyciem. Dzięki zarezerwowanej pojemności (Citus) można wprowadzić z góry zobowiązanie dla grupy serwerów z przedziałem (Citus), aby uzyskać znaczny rabat na koszty obliczeń. Aby kupić zastrzeżoną pojemność (Citus), musisz określić region platformy Azure, termin rezerwacji i częstotliwość rozliczeń.

> [!IMPORTANT]
> Na tej stronie zarezerwowano pojemność Azure Database for PostgreSQL — funkcja Citus. Zapoznaj się z [tą stroną](/azure/postgresql/concept-reserved-pricing) , aby uzyskać informacje na temat pojemności zarezerwowanych dla Azure Database for PostgreSQL — pojedynczy serwer.

Nie musisz przypisywać rezerwacji do określonych grup serwerów Citus. W przypadku grupy serwerów z uruchomioną funkcją Moje skalowanie (Citus) lub nowo wdrożonych jest automatycznie uzyskiwana korzyść z zarezerwowanych cen. Zakup rezerwacji polega na przedpłaceniu za koszt obliczeń przez okres jeden lub trzy lata. Gdy tylko kupisz rezerwację, opłaty za obliczenia ze skalowaniem (Citus), które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Rezerwacja nie obejmuje opłat za oprogramowanie, sieci ani magazyny skojarzone z grupami serwerów Citus. Na koniec okresu rezerwacji, wygaśnięcie korzyści z rozliczeń i grup serwerów w ramach funkcji Citus (z płatnością zgodnie z rzeczywistym użyciem). Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zapoznaj się z [zastrzeżoną pojemnością Azure Database for PostgreSQL — Citus](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

W [Azure Portal](https://portal.azure.com/)można zakupić zarezerwowaną pojemność (Citus). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations). Aby kupić zarezerwowaną pojemność:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić zarezerwowaną pojemność (Citus).

Aby uzyskać szczegółowe informacje na temat sposobu, w jaki Klienci korporacyjni i klienci z opłatą zgodnie z rzeczywistym użyciem są obciążani zakupami rezerwacji, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) i [Opis użycia usługi Azure Reservation dla subskrypcji z opłatą zgodnie z rzeczywistym użyciem](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).

## <a name="determine-the-right-server-group-size-before-purchase"></a>Określ właściwy rozmiar grupy serwerów przed zakupem

Rozmiar rezerwacji powinien opierać się na łącznej ilości obliczeń używanych przez istniejący lub wkrótce wdrożony koordynator i węzły procesu roboczego w ramach grup serwerów Citus (w ramach określonego regionu).

Załóżmy na przykład, że uruchomiono jedną grupę serwerów z systemem Citus, z 16 koordynatorem rdzeń wirtualny i trzema węzłami roboczymi rdzeń wirtualny. Załóżmy również, że planujesz wdrożenie w następnym miesiącu grupy serwerów dodatkowej Citus z rdzeń wirtualny koordynatorem 32 i dwoma węzłami roboczymi rdzeń wirtualny. Załóżmy, że te zasoby będą potrzebne przez co najmniej jeden rok.

W takim przypadku należy zakupić jednoletnią rezerwację dla

* Łączna liczba 16 rdzeni wirtualnych + 32 rdzeni wirtualnych = 48 rdzeni wirtualnych dla węzłów koordynatora
* Łączna liczba trzech węzłów x 8 rdzeni wirtualnych + 2 węzły x 4 rdzeni wirtualnych = 24 + 8 = 32 rdzeni wirtualnych dla węzłów procesu roboczego

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Kupowanie Azure Database for PostgreSQL zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz pozycję **Dodaj** , a następnie w okienku rezerwacje zakupu wybierz pozycję **Azure Database for PostgreSQL** , aby zakupić nową rezerwację dla baz danych PostgreSQL.
4. Wybierz typ obliczeń Citus, aby kupić, a następnie kliknij przycisk **Wybierz** .
5. Sprawdź ilość wybranego typu obliczenia na karcie **produkty** .
4. Aby dokończyć zakup, Skorzystaj z karty **kupowanie i przeglądanie** .

W poniższej tabeli opisano wymagane pola.

| Pole        | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subskrypcja | Subskrypcja używana do płacenia za Azure Database for PostgreSQL zarezerwowaną rezerwację zdolności produkcyjnych. W ramach metody płatności w ramach subskrypcji jest naliczana opłata z tytułu kosztów ponoszonych z góry Azure Database for PostgreSQL zastrzeżonej rezerwacji. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji przedsiębiorstwa opłaty są odejmowane od salda zobowiązania pieniężnego rejestracji lub są naliczane jako nadwyżkowe. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.                                                                                  |
| Zakres        | Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: <br><br> Rdzeń wirtualny rabat rezerwacji jest stosowany do grup **serwerów ze** skalą (Citus) uruchomionych w każdej subskrypcji w kontekście rozliczania. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji.  W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta. <br><br> **Pojedyncza subskrypcja,** rabat zastrzeżeń rdzeń wirtualny jest stosowany do grup serwerów w ramach funkcji do skalowania (Citus) w tej subskrypcji. <br><br> **Pojedynczej grupy zasobów,** rabat rezerwacji jest stosowany do grup serwerów z Citusą w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. |
| Region       | Region świadczenia usługi Azure objęty rezerwacją zarezerwowanej pojemności Azure Database for PostgreSQL (Citus).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termin         | Jeden rok lub trzy lata.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Liczba     | Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji zarezerwowanej pojemności Citus. W szczególności liczba elementów będących koordynatorem lub węzłem procesu roboczego rdzeni wirtualnych w wybranym regionie świadczenia usługi Azure, które są zarezerwowane, i które uzyskają rabat rozliczeń. Na przykład w przypadku uruchamiania programu (lub planowania uruchamiania) grup serwerów z możliwością skalowania (Citus) z łączną pojemnością obliczeniową 64 węzła rdzeni wirtualnych i 32 węzła procesu roboczego rdzeni wirtualnych w regionie Wschodnie stany USA należy określić liczbę jako 64 i 32 dla koordynatora i węzłów procesu roboczego, aby zmaksymalizować korzyść dla wszystkich serwerów.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie koordynatora w górę i w dół oraz węzłów procesu roboczego w regionie, bez utraty korzyści związanych z pojemnością.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby grup serwerów w ramach funkcji Citus, które pasują do Azure Database for PostgreSQL zarezerwowanego zakresu rezerwacji i atrybutów. Zakres zarezerwowanej pojemności usługi Azure Database for PostgreSQL — Citus (rezerwacja) można zaktualizować za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

* [Co to jest Azure Reservations?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Zarządzanie usługą Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Informacje na temat rabatu na rezerwacje platformy Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)
