---
title: Przedpłata za obliczenia z zarezerwowaną pojemnością — Azure Database for MySQL
description: Przedpłata za Azure Database for MySQL zasoby obliczeniowe z zarezerwowaną pojemnością
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 86933b324d7216a6097102cf237d0402d9f63234
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90882684"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Przedpłata za Azure Database for MySQL zasoby obliczeniowe z zarezerwowaną pojemnością

Azure Database for MySQL teraz pomaga zaoszczędzić pieniądze dzięki wypłaceniu zasobów obliczeniowych w porównaniu z cenami płatności zgodnie z rzeczywistym użyciem. Dzięki Azure Database for MySQL zarezerwowanej pojemności nastąpi zobowiązanie z góry na serwer MySQL przez okres jednego lub trzech lat w celu uzyskania znacznego rabatu w kosztach obliczeniowych. Aby kupić Azure Database for MySQL zarezerwowaną pojemność, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin. </br>

Nie trzeba przypisywać rezerwacji do określonych serwerów Azure Database for MySQL. Już uruchomione Azure Database for MySQL lub te, które są nowo wdrożone, będą automatycznie korzystały z zarezerwowanych cen. Zakup rezerwacji polega na przedpłaceniu za koszt obliczeń przez okres jeden lub trzy lata. Po zakupieniu rezerwacji opłaty za usługę Azure Database for MySQL nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Rezerwacja nie obejmuje opłat za oprogramowanie, sieci lub magazyn związany z serwerem bazy danych MySQL. Na koniec okresu rezerwacji Pomoc dotycząca rozliczeń wygaśnie, a Azure Database for MySQL są rozliczane według ceny płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zobacz [Azure Database for MySQL zarezerwowanej pojemności](https://azure.microsoft.com/pricing/details/mysql/). </br>

W [Azure Portal](https://portal.azure.com/)można kupić Azure Database for MySQL zarezerwowaną pojemność. Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/monthly-payments-reservations.md). Aby kupić zarezerwowaną pojemność:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić Azure Database for MySQL zarezerwowaną pojemność. </br>

Szczegółowe informacje na temat tego, w jaki sposób Klienci korporacyjni i klienci korzystający z płatnej zgodnie z rzeczywistym [użyciem są](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) obciążani opłatami za zakup rezerwacji [.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-database-size-before-purchase"></a>Określ właściwy rozmiar bazy danych przed zakupem

Rozmiar rezerwacji powinien opierać się na łącznej ilości obliczeń używanych przez istniejący lub wkrótce wdrożony serwer w określonym regionie oraz przy użyciu tej samej warstwy wydajności i generowania sprzętu.</br>

Załóżmy na przykład, że korzystasz z jednej bazy danych 5 rdzeń – 32 rdzeń wirtualny MySQL i dwóch zoptymalizowanych pod kątem pamięci, 5 rdzeń — 16 rdzeń wirtualny MySQL. Ponadto Załóżmy, że planujesz wdrożenie w następnym miesiącu, dodatkowym celu ogólnego przeznaczenia, 5 rdzeń – 32 rdzeń wirtualny bazy danych i jednej pamięci zoptymalizowanej jako serwer bazy danych 5 rdzeń-16 rdzeń wirtualny. Załóżmy, że wiesz, że te zasoby będą potrzebne przez co najmniej 1 rok. W takim przypadku należy zakupić 64 (2x32) rdzeni wirtualnych, 1-letnią rezerwację dla jednej bazy danych ogólnego przeznaczenia-5 rdzeń i 48 (2x16 + 16) rdzeń wirtualny 1 roku dla jednolitej pamięci bazy danych zoptymalizowanej-5 rdzeń


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Kupowanie Azure Database for MySQL zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz pozycję **Dodaj** , a następnie w okienku rezerwacje zakupu wybierz pozycję **Azure Database for MySQL** , aby zakupić nową rezerwację dla baz danych MySQL.
4. Wypełnij pola wymagane. Istniejące lub nowe bazy danych zgodne z wybranymi atrybutami kwalifikują się do uzyskania rabatu zarezerwowanej pojemności. Rzeczywista liczba serwerów Azure Database for MySQL, które pobierają rabat, zależą od wybranego zakresu i ilości.


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="Przegląd cen zarezerwowanych":::


W poniższej tabeli opisano wymagane pola.

| Pole | Opis |
| :------------ | :------- |
| Subskrypcja   | Subskrypcja używana do płacenia za Azure Database for MySQL zarezerwowaną rezerwację zdolności produkcyjnych. W ramach metody płatności w ramach subskrypcji jest naliczana opłata z tytułu kosztów ponoszonych z góry Azure Database for MySQL zastrzeżonej rezerwacji. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.
| Zakres | Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: </br></br> **Shared**Rdzeń wirtualny rabat rezerwacji jest stosowany do serwerów Azure Database for MySQL uruchomionych w ramach dowolnych subskrypcji w kontekście rozliczeń. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.</br></br> **Pojedyncza subskrypcja**— rabat rezerwacji rdzeń wirtualny jest stosowany do serwerów Azure Database for MySQL w tej subskrypcji. </br></br> **Pojedynczej grupy zasobów**, Rabat rezerwacji jest stosowany do Azure Database for MySQL serwerów w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.
| Region | Region świadczenia usługi Azure objęty Azure Database for MySQL rezerwacji zarezerwowanej pojemności.
| Typ wdrożenia | Typ zasobu Azure Database for MySQL, dla którego chcesz kupić rezerwację.
| Warstwa wydajności | Warstwa usług dla serwerów Azure Database for MySQL.
| Okres | Jeden rok
| Liczba | Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji Azure Database for MySQL zarezerwowanej. Ilość to liczba rdzeni wirtualnych w wybranym regionie i warstwie wydajności platformy Azure, które są zarezerwowane i pobierają rabat rozliczeń. Na przykład w przypadku uruchamiania programu lub planowania uruchamiania serwerów Azure Database for MySQL z łączną pojemnością obliczeniową 5 rdzeń 16 rdzeni wirtualnych w regionie Wschodnie stany USA należy określić liczbę jako 16, aby zmaksymalizować korzyść dla wszystkich serwerów.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie w górę i w dół w warstwach i regionach wydajności bez utraty korzyści zarezerwowanej pojemności. 

## <a name="need-help--contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby serwerów Azure Database for MySQL, które pasują do Azure Database for MySQL zakres rezerwacji zarezerwowanej pojemności i atrybuty. Zakres zarezerwowanej pojemności usługi Azure Database for MySQL można zaktualizować za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API. </br></br>
Aby dowiedzieć się, jak zarządzać zarezerwowaną Azure Database for MySQL pojemności, zobacz Zarządzanie Azure Database for MySQL zarezerwowaną pojemnością.

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

* [Co to są Azure Reservations](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Zarządzanie usługą Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Informacje na temat rabatu na rezerwacje platformy Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim](https://docs.microsoft.com/partner-center/azure-reservations)

