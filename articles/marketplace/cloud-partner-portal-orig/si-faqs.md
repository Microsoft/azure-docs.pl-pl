---
title: Często zadawane pytania dotyczące sprzedającego
description: Często zadawane pytania dotyczące funkcji wglądu w dane sprzedawcy w portal Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285388"
---
<a name="seller-insights-faq"></a>Często zadawane pytania dotyczące sprzedającego
===================

Ten artykuł zawiera wskazówki dotyczące typowych procedur użytkownika w ramach programu oraz pytań dotyczących informacji o sprzedawcy.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Znajdź definicje wartości w pobranym pliku transakcji
------------------------------------------------------------------

Definicje wartości metryk w pliku transakcji znajdują się w [definicjach szczegółowych](./si-insights-definitions-v4.md)informacji o sprzedawcy artykułu.


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Zobacz szczegóły klienta transakcji, dla których zostały opłacone
-------------------------------------------------------------

Po pobraniu transakcji z modułu wypłaty Znajdź kolumnę **status wypłaty**i Zastosuj filtr, aby wyświetlić tylko wartość "płatne". Zostaną wyświetlone następujące kolumny zawierające szczegóły klienta: **Nazwa firmy**, **adres E-mail klienta**, **kraj klienta**, **Stan klienta**i **Kod pocztowy klienta**.


<a name="calculate-my-open-accounts-receivable"></a>Oblicz rozliczenia moich otwartych kont
-------------------------------------

Po pobraniu transakcji z modułu wypłaty Znajdź kolumnę **status wypłaty**i Zastosuj filtr, aby wyświetlić tylko wartość "nadchodzące wypłaty" i "nie gotowość do wypłaty". Następnie Sumuj kolumnę z etykietą **Kwota wypłaty (komputer)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Oblicz przychód według okresu użytkowania przez klienta
------------------------------------------

Po pobraniu transakcji z modułu wypłaty Znajdź kolumnę z etykietą **stan transakcji**i przefiltruj wartość "płatne".   Dla każdej transakcji wymienionej w kolumnie **Kwota wypłaty (komputer)** przedstawia kwotę, która została zapłacona.  Aby oszacować okres użycia związany z transakcją, należy użyć **daty opłaty**kolumny, która jest bliskim przybliżeniu ostatniego dnia użycia dla okresu, do którego jest stosowana transakcja.


<a name="calculate-your-bad-debt"></a>Oblicz zły dług
---------------------

Po pobraniu transakcji z modułu wypłaty Znajdź w kolumnie etykietę **stan końcowy kolekcji**i Zastosuj filtr, aby wyświetlić tylko wartość "Odpisz". Następnie Sumuj kolumnę z etykietą **Kwota wypłaty (komputer)**.


<a name="view-payout-or-customer-contact-information"></a>Wyświetlanie wypłat lub informacji kontaktowych klienta
-------------------------------------------

Zaloguj się jako użytkownik z rolą "Owner", a nie z rolą "Współautor". Tylko rola właściciela zobaczy wypłatę i informacje o kliencie. Więcej informacji o rolach użytkownika można znaleźć w artykule [Zarządzanie użytkownikami](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Oblicz moje wypłaty z góry
----------------------------

Po pobraniu transakcji z modułu wypłaty Znajdź kolumnę z etykietą **Typ transakcji**i Zastosuj filtr, aby wyświetlić tylko wartość "opłata". Następnie zlokalizuj kolumnę z etykietą **stan końcowy kolekcji**i Zastosuj filtr, aby wyświetlić tylko wartość "w toku". Na koniec należy zsumować kolumnę **Kwota wypłaty (komputer)** , aby obliczyć wszystkie zaliczki płatne dla Ciebie przed pobraniem od klienta.


<a name="calculate-customer-refunds"></a>Obliczanie zwrotów klienta
--------------------------

Po pobraniu transakcji z modułu wypłaty Znajdź w kolumnie etykietę **stan końcowy kolekcji**i Zastosuj filtr, aby wyświetlić tylko wartość "zwrot". Aby obliczyć wszystkie zwroty, które zostały przetworzone dla klientów, należy zsumować wartość w kolumnie **opłata (komputer)** .


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Określ, które transakcje mają być partnerem kanału firmy Microsoft
----------------------------------------------------------------

Wszystkie transakcje w kolumnie **Typ licencji platformy Azure** , które są filtrowane w celu wyświetlenia wartości "przedsiębiorstwo przez odsprzedawcę" i "dostawca rozwiązań w chmurze" obejmują partnera usługi Microsoft Channel. Aby uzyskać więcej informacji na temat partnera, możesz znaleźć **nazwę odsprzedawcy** i **adres e-mail odsprzedawcy** w ramach pobierania modułu wypłaty i pobrać moduł klienta.


<a name="identify-trial-usage-and-trial-conversions"></a>Identyfikowanie użycia wersji próbnej i konwersji wersji próbnej
------------------------------------------

Pobrania modułu zamówienie, użycie i wypłata zawierają teraz **datę zakończenia** okresu próbnego, aby pomóc zrozumieć, kiedy okres próbny zakończył się w danej kolejności, jeśli ma to zastosowanie. Aby zobaczyć użycie wersji próbnej i zamówień, Znajdź kolumnę **Typ rozliczenia jednostki SKU** w plikach do pobrania i Zastosuj filtr, aby wyświetlić tylko wartość "wersja próbna". Aby wyświetlić konwersje wersji próbnej, odszukaj kolumnę **Data zakończenia okresu próbnego** w plikach do pobrania i Zastosuj filtr, aby wyświetlić tylko zamówienia, gdy **Data zakończenia okresu próbnego** przypada na datę dzisiejszą, a kolumna " **Data anulowania** " jest pusta lub późniejsza niż **Data zakończenia okresu próbnego**.


<a name="when-is-my-monthly-payout-calculated"></a>Kiedy naliczana jest Miesięczna wypłata
------------------------------------

Twoje wypłaty są wystawiane przez 15. każdego miesiąca na wszystkie kwoty gotowe do wypłaty przez ostatni dzień kalendarzowy poprzedniego miesiąca. W trzecim dniu miesiąca firma Microsoft obliczy kwotę wypłaty w poprzednim miesiącu i zaktualizuje wszystkie stosowne transakcje opłat w ramach pobierania o "nadchodzącej wypłatach" w kolumnie **stan wypłaty** . Te transakcje będą znajdować się w tym stanie do momentu wysłania żądania płatności do konta bankowego, po upływie którego **stan wypłaty** zostanie zaktualizowany na "płatne" i "data wypłaty" zostanie zaktualizowana o datę przesłania żądania płatności do Twojego banku.


<a name="calculate-customer-acquisition-and-loss"></a>Obliczanie pozyskania i utraty klienta
---------------------------------------

Zobaczysz datę, kiedy klient kupił jedną z ofert, przenosząc kolumnę **Data pozyskaną** w pobraniu przez klienta. Podobnie, można zobaczyć datę, po której nie ma już żadnej oferty opublikowanej przez użytkownika przez znalezienie kolumny **Data utracony** w pobraniu przez klienta.


<a name="finding-more-help"></a>Znajdowanie większej pomocy
-----------------

- [Definicje](./si-insights-definitions-v4.md) informacji o sprzedawcy — Znajdowanie definicji metryk i danych

- Wprowadzenie do usługi [Informacje o sprzedawcy](./si-getting-started.md) — wprowadzenie do funkcji informacje o sprzedawcy.

