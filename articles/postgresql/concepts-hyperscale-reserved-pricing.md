---
title: Ceny zarezerwowanych obliczeń — Azure Database for PostgreSQL-Citus
description: Przedpłata za zasoby obliczeniowe Azure Database for PostgreSQL (Citus) z zarezerwowaną pojemnością.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600723"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Przedpłata za zasoby obliczeniowe Azure Database for PostgreSQL (Citus) z zarezerwowaną pojemnością

Azure Database for PostgreSQL — funkcja Citus) teraz pomaga zaoszczędzić pieniądze dzięki wykorzystaniu zasobów obliczeniowych w porównaniu z cenami płatności zgodnie z rzeczywistym użyciem. Dzięki zarezerwowanej pojemności (Citus) można wprowadzić z góry zobowiązanie dla grupy serwerów z przedziałem (Citus), aby uzyskać znaczny rabat na koszty obliczeń. Aby kupić zastrzeżoną pojemność (Citus), musisz określić region platformy Azure, termin rezerwacji i częstotliwość rozliczeń.

> [!IMPORTANT]
> W tym artykule zarezerwowano pojemność Azure Database for PostgreSQL — funkcja Citus. Aby uzyskać informacje o pojemności zarezerwowanej dla Azure Database for PostgreSQL — jeden serwer, zobacz [przedpłata za Azure Database for PostgreSQL — zasoby obliczeniowe pojedynczego serwera o pojemności zarezerwowanej](./concept-reserved-pricing.md).

Nie musisz przypisywać rezerwacji do określonych grup serwerów Citus. W przypadku grupy serwerów z uruchomioną funkcją Moje skalowanie (Citus) lub nowo wdrożonych automatycznie uzyskuje się korzyści wynikające z zarezerwowanych cen. Zakup rezerwacji polega na zapłaceniu za koszt obliczeń przez jeden rok lub trzy lata. Gdy tylko kupisz rezerwację, opłaty za obliczenia ze skalowaniem (Citus), które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. 

Rezerwacja nie obejmuje opłat za oprogramowanie, Sieć ani magazyn związany z grupami serwerów Citus. Na koniec okresu rezerwacji, wygaśnięcie korzyści z rozliczeń i grup serwerów w ramach funkcji Citus (z płatnością zgodnie z rzeczywistym użyciem). Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zapoznaj się z [zastrzeżoną pojemnością Azure Database for PostgreSQL — Citus](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

W [Azure Portal](https://portal.azure.com/)można zakupić zarezerwowaną pojemność (Citus). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/prepare-buy-reservation.md). Aby kupić zarezerwowaną pojemność:

* Musisz być w roli właściciela dla co najmniej jednej Enterprise Agreement (EA) lub pojedynczej subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.
* W przypadku subskrypcji Enterprise Agreement w [portalu EA](https://ea.azure.com/)należy włączyć opcję **Dodawanie wystąpień zarezerwowanych** . Jeśli to ustawienie jest wyłączone, musisz być administratorem Enterprise Agreement w ramach subskrypcji.
* W przypadku programu w programie Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić zastrzeżoną pojemność (Citus).

Aby uzyskać informacje na temat sposobu, w jaki klienci Enterprise Agreement i klienci z opłatą zgodnie z rzeczywistym użyciem są obciążani zakupami rezerwacji, zobacz:
- [Informacje na temat użycia usługi Azure Reservation na potrzeby rejestracji Enterprise Agreement](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Informacje na temat użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>Określ właściwy rozmiar grupy serwerów przed zakupem

Rozmiar rezerwacji zależy od łącznej ilości obliczeń używanych przez istniejący lub wkrótce wdrożony koordynator i węzły procesu roboczego w ramach grup serwerów Citus () w określonym regionie.

Załóżmy na przykład, że jest uruchamiana Grupa serwerów z jednym skalem (Citus) z 16 koordynatorem rdzeń wirtualny i węzłami procesu roboczego 3 8 rdzeń wirtualny. Załóżmy również, że planujesz wdrożenie w następnym miesiącu grupy serwerów dodatkowej Citus z rdzeń wirtualny koordynatorem 32 i rdzeń wirtualny węzłami roboczymi 2 4. Załóżmy również, że te zasoby są potrzebne przez co najmniej jeden rok.

W takim przypadku należy zakupić jednoletnią rezerwację dla:

* Łączna liczba 16 rdzeni wirtualnych + 32 rdzeni wirtualnych = 48 rdzeni wirtualnych dla węzłów koordynatora
* Łączna liczba 3 węzłów x 8 rdzeni wirtualnych + 2 węzły x 4 rdzeni wirtualnych = 24 + 8 = 32 rdzeni wirtualnych dla węzłów procesu roboczego

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Kupowanie Azure Database for PostgreSQL zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
1. Wybierz pozycję **Dodaj**. W okienku **rezerwacje zakupu** wybierz pozycję **Azure Database for PostgreSQL** , aby zakupić nową rezerwację dla baz danych PostgreSQL.
1. Wybierz typ **obliczeń Citus (skala)** do zakupu, a następnie kliknij pozycję **Wybierz**.
1. Przejrzyj ilość wybranego typu obliczenia na karcie **produkty** .
1. Przejdź do karty **kupowanie i przeglądanie** , aby zakończyć kupowanie.

W poniższej tabeli opisano wymagane pola.

| Pole        | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subskrypcja | Subskrypcja używana do płacenia za Azure Database for PostgreSQL zarezerwowaną rezerwację zdolności produkcyjnych. W ramach metody płatności w ramach subskrypcji jest naliczana opłata z tytułu kosztów ponoszonych z góry Azure Database for PostgreSQL zastrzeżonej rezerwacji. Typ subskrypcji musi być Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cenami z rabatem zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise Agreement opłaty są naliczane od przedpłaty za subskrypcję platformy Azure (wcześniej nazywanej zobowiązaniem pieniężnym) lub za użycie. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.                                                                                  |
| Zakres        | Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji **udostępnione** w ramach kontekstu rozliczania zostanie zastosowany rabat zastrzeżeń rdzeń wirtualny (Citus). W przypadku klientów Enterprise Agreement zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z płatność zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z płatność zgodnie z rzeczywistym uwzględnieniem utworzone przez administratora konta. W przypadku wybrania opcji **pojedyncze subskrypcje** w ramach tej subskrypcji zostanie zastosowany rabat rezerwacji rdzeń wirtualny (Citus). W przypadku wybrania opcji **pojedyncze grupy zasobów** rabat rezerwacji będzie stosowany do grup serwerów z Citusą w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach danej subskrypcji. |
| Region (Region)       | Region świadczenia usługi Azure objęty rezerwacją zarezerwowanej pojemności Azure Database for PostgreSQL (Citus).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Okres         | Jeden rok lub trzy lata.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Liczba     | Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji zarezerwowanej pojemności Citus. W szczególności liczba elementów będących koordynatorem lub węzłem procesu roboczego rdzeni wirtualnych w wybranym regionie świadczenia usługi Azure, które są zarezerwowane, i które uzyskają rabat rozliczeń. Na przykład, jeśli uruchamiasz (lub planujesz uruchomić) grupy serwerów z funkcją "Citus () z łączną pojemnością obliczeniową 64 węzła rdzeni wirtualnych i 32 węzeł procesu roboczego rdzeni wirtualnych w regionie Wschodnie stany USA, określ ilość odpowiednio 64 i 32 dla koordynatora i węzłów procesu roboczego, aby zmaksymalizować korzyść dla wszystkich serwerów.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz samoobsługowe [weksle i zwroty dla rezerwacji platformy Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie koordynatora w górę i w dół oraz węzłów procesu roboczego w regionie, bez utraty korzyści związanych z pojemnością.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby grup serwerów w ramach funkcji Citus, które pasują do Azure Database for PostgreSQL zarezerwowanego zakresu rezerwacji i atrybutów. Zakres rezerwacji zarezerwowanej pojemności Azure Database for PostgreSQL (Citus) można zaktualizować za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API.

Więcej szczegółów na temat rezerwacji platformy Azure można znaleźć w następujących artykułach:

* [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Zarządzanie rezerwacjami platformy Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Informacje na temat rabatu rezerwacji platformy Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Informacje na temat użycia rezerwacji Enterprise Agreement rejestracji](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Rezerwacje platformy Azure w programie partnerskim dostawca rozwiązań w chmurze](/partner-center/azure-reservations)