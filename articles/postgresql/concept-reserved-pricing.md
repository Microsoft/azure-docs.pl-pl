---
title: Zarezerwowane ceny obliczeniowe — Azure Database for PostgreSQL — pojedynczy serwer
description: Przedpłata za Azure Database for PostgreSQL zasoby obliczeniowe z zarezerwowaną pojemnością
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: dcc6cc40c936876c856326e330b67ef465f90cde
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240447"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Przedpłata za zasoby obliczeniowe dla Azure Database for PostgreSQL pojedynczego serwera z zarezerwowaną pojemnością

Azure Database for PostgreSQL teraz pomaga zaoszczędzić pieniądze dzięki wypłaceniu zasobów obliczeniowych w porównaniu z cenami płatności zgodnie z rzeczywistym użyciem. Dzięki Azure Database for PostgreSQL zarezerwowanej pojemności nastąpi zobowiązanie z góry na serwerze PostgreSQL przez okres jednego lub trzech lat w celu uzyskania znacznego rabatu w kosztach obliczeniowych. Aby kupić Azure Database for PostgreSQL zarezerwowaną pojemność, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin. </br>

Nie musisz przypisywać rezerwacji do określonych serwerów Azure Database for PostgreSQL. Już uruchomione Azure Database for PostgreSQL (lub nowo wdrożone) będą automatycznie korzystać z korzyści z zarezerwowanych cen. Zakup rezerwacji polega na przedpłaceniu za koszt obliczeń przez okres jeden lub trzy lata. Gdy tylko kupisz rezerwację, opłaty za usługę Azure Database for PostgreSQL, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Rezerwacja nie obejmuje opłat za oprogramowanie, sieci lub magazyn związany z serwerami baz danych PostgreSQL. Na koniec okresu rezerwacji Pomoc dotycząca rozliczeń wygaśnie, a Azure Database for PostgreSQL są rozliczane według ceny płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zobacz [Azure Database for PostgreSQL zarezerwowanej pojemności](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Ceny zarezerwowanej pojemności są dostępne dla Azure Database for PostgreSQL zarówno na [jednym serwerze](./overview.md#azure-database-for-postgresql---single-server) , jak i w [Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) . Aby uzyskać informacje o cenach w modelu RI (Citus), zobacz [Tę stronę](concepts-hyperscale-reserved-pricing.md).

W [Azure Portal](https://portal.azure.com/)można kupić Azure Database for PostgreSQL zarezerwowaną pojemność. Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/prepare-buy-reservation.md). Aby kupić zarezerwowaną pojemność:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić Azure Database for PostgreSQL zarezerwowaną pojemność. </br>

Szczegółowe informacje na temat tego, w jaki sposób Klienci korporacyjni i klienci korzystający z płatnej zgodnie z rzeczywistym [użyciem są](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) obciążani opłatami za zakup rezerwacji [.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)


## <a name="determine-the-right-server-size-before-purchase"></a>Określ właściwy rozmiar serwera przed zakupem

Rozmiar rezerwacji powinien opierać się na łącznej ilości obliczeń używanych przez istniejące lub wkrótce wdrożone serwery w określonym regionie oraz przy użyciu tej samej warstwy wydajności i generowania sprzętu.</br>

Załóżmy na przykład, że korzystasz z jednej bazy danych ogólnego przeznaczenia 5 rdzeń – 32 rdzeń wirtualny PostgreSQL i dwóch zoptymalizowanych pod kątem pamięci 5 rdzeń-16 rdzeń wirtualny PostgreSQL. Ponadto Załóżmy, że planujesz wdrożenie w następnym miesiącu i dodatkowym głównym przeznaczeniu 5 rdzeń – 32 rdzeń wirtualny Database Server oraz jednym z zoptymalizowanych pod kątem pamięci 5 rdzeń serwera bazy danych rdzeń wirtualny. Załóżmy, że wiesz, że te zasoby będą potrzebne przez co najmniej jeden rok. W takim przypadku należy zakupić 64 (2x32) rdzeni wirtualnych, jednoletnią rezerwację dla jednej bazy danych ogólnego przeznaczenia-5 rdzeń i 48 (2x16 + 16) rdzeń wirtualny jednoletnią rezerwację dla zoptymalizowanej pod kątem pamięci pojedynczej bazy danych — 5 rdzeń


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Kupowanie Azure Database for PostgreSQL zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje** .
3. Wybierz pozycję **Dodaj** , a następnie w okienku rezerwacje zakupu wybierz pozycję **Azure Database for PostgreSQL** , aby zakupić nową rezerwację dla baz danych PostgreSQL.
4. Wypełnij pola wymagane. Istniejące lub nowe bazy danych zgodne z wybranymi atrybutami kwalifikują się do uzyskania rabatu zarezerwowanej pojemności. Rzeczywista liczba serwerów Azure Database for PostgreSQL, które pobierają rabat, zależą od wybranego zakresu i ilości.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Przegląd cen zarezerwowanych":::


W poniższej tabeli opisano wymagane pola.

| Pole | Opis |
| :------------ | :------- |
| Subskrypcja   | Subskrypcja używana do płacenia za Azure Database for PostgreSQL zarezerwowaną rezerwację zdolności produkcyjnych. W ramach metody płatności w ramach subskrypcji jest naliczana opłata z tytułu kosztów ponoszonych z góry Azure Database for PostgreSQL zastrzeżonej rezerwacji. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.
| Zakres | Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: </br></br> **Shared** Rdzeń wirtualny rabat rezerwacji jest stosowany do serwerów Azure Database for PostgreSQL uruchomionych w ramach dowolnych subskrypcji w kontekście rozliczeń. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.</br></br> **Pojedyncza subskrypcja** — rabat rezerwacji rdzeń wirtualny jest stosowany do serwerów Azure Database for PostgreSQL w tej subskrypcji. </br></br> **Pojedynczej grupy zasobów** , Rabat rezerwacji jest stosowany do Azure Database for PostgreSQL serwerów w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.
| Region | Region świadczenia usługi Azure objęty Azure Database for PostgreSQL rezerwacji zarezerwowanej pojemności.
| Typ wdrożenia | Typ zasobu Azure Database for PostgreSQL, dla którego chcesz kupić rezerwację.
| Warstwa wydajności | Warstwa usług dla serwerów Azure Database for PostgreSQL.
| Termin | Jeden rok
| Liczba | Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji Azure Database for PostgreSQL zarezerwowanej. Ilość to liczba rdzeni wirtualnych w wybranym regionie i warstwie wydajności platformy Azure, które są zarezerwowane i pobierają rabat rozliczeń. Na przykład w przypadku uruchamiania programu lub planowania uruchamiania serwerów Azure Database for PostgreSQL z łączną pojemnością obliczeniową 5 rdzeń 16 rdzeni wirtualnych w regionie Wschodnie stany USA należy określić liczbę jako 16, aby zmaksymalizować korzyść dla wszystkich serwerów.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie w górę i w dół w warstwach i regionach wydajności bez utraty korzyści zarezerwowanej pojemności. 

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby serwerów Azure Database for PostgreSQL, które pasują do Azure Database for PostgreSQL zakres rezerwacji zarezerwowanej pojemności i atrybuty. Możesz zaktualizować zakres rezerwacji zarezerwowanej pojemności usługi Azure Database for PostgreSQL za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

* [Co to są Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Zarządzanie usługą Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Informacje na temat rabatu na rezerwacje platformy Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim](/partner-center/azure-reservations)