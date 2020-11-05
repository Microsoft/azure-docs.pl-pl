---
title: Informacje o rabacie na rezerwacje — pojedynczy serwer usługi Azure Database for PostgreSQL
description: Dowiedz się, jak jest stosowany rabat na rezerwację do pojedynczych serwerów usługi Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ace362872f0b7ba8e2f3d0302c887e2465c62982
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240345"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Omówienie stosowania rabatu za rezerwację do pojedynczego serwera usługi Azure Database for PostgreSQL

Po zakupie pojemności zarezerwowanej pojedynczego serwera usługi Azure Database for PostgreSQL rabat za rezerwację jest automatycznie stosowany do baz danych pojedynczych serwerów usługi Azure Database for PostgreSQL pasujących do atrybutów i ilości rezerwacji. Rezerwacja obejmuje tylko koszty obliczeniowe pojedynczego serwera usługi Azure Database for PostgreSQL. Opłata jest naliczana za magazyn i sieć według normalnych stawek.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za rezerwację jest dostępny na zasadzie * **wykorzystaj lub strać** _. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.</br>

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Rabat zastosowany do pojedynczego serwera usługi Azure Database for PostgreSQL

Rabat za pojemność zarezerwowaną pojedynczego serwera usługi Azure Database for PostgreSQL jest stosowany do działającego pojedynczego serwera PostgreSQL z rozliczeniem godzinowym. Kupowana rezerwacja jest dopasowywana do użycia zasobów obliczeniowych emitowanych przez działający pojedynczy serwer usługi Azure Database for PostgreSQL. W przypadku pojedynczych serwerów usługi Azure Database for PostgreSQL, które nie działają przez pełną godzinę, rezerwacja jest automatycznie stosowana do innego pojedynczego serwera usługi Azure Database for PostgreSQL pasującego do atrybutów rezerwacji. Rabat może być stosowany do pojedynczych serwerów usługi Azure Database for PostgreSQL, które działają równolegle. Jeśli nie masz pojedynczych serwerów usługi Azure Database for PostgreSQL, które działają przez pełną godzinę i pasują do atrybutów rezerwacji, nie wykorzystasz w pełni korzyści z rabatu na rezerwację w tej godzinie.

W poniższych przykładach pokazano, w jaki sposób rabat za pojemność zarezerwowaną pojedynczego serwera usługi Azure Database for PostgreSQL jest stosowany w zależności od liczby zakupionych rdzeni oraz czasu ich działania.

_ **Przykład 1** : Kupujesz pojemność zarezerwowaną pojedynczego serwera usługi Azure Database for PostgreSQL dla 8 rdzeni wirtualnych. W przypadku korzystania z pojedynczego serwera usługi Azure Database for PostgreSQL z 16 rdzeniami wirtualnymi zgodnego z resztą atrybutów rezerwacji opłata jest naliczana według stawek płatności zgodnie z rzeczywistym użyciem za osiem rdzeni wirtualnych użycia mocy obliczeniowej pojedynczego serwera PostgreSQL, a rabat za rezerwację otrzymujesz na 1 godzinę użycia mocy obliczeniowej pojedynczego serwera PostgreSQL z 8 rdzeniami wirtualnymi.</br>

W pozostałych przykładach przyjęto założenie, że zakupiona pojemność zarezerwowana pojedynczego serwera usługi Azure Database for PostgreSQL dotyczy pojedynczego serwera usługi Azure Database for PostgreSQL z 16 rdzeniami wirtualnymi, a pozostałe atrybuty rezerwacji są zgodne z uruchomionymi pojedynczymi serwerami PostgreSQL.

* **Przykład 2** : Uruchamiasz 2 pojedyncze serwery usługi Azure Database for PostgreSQL, każdy z 8 rdzeniami wirtualnymi, na 1 godzinę. Rabat za rezerwację 16 rdzeni wirtualnych jest stosowany do użycia mocy obliczeniowej dla obu pojedynczych serwerów usługi Azure Database for PostgreSQL z 8 rdzeniami wirtualnymi.

* **Przykład 3** : Uruchamiasz jeden pojedynczy serwer usługi Azure Database for PostgreSQL z 16 rdzeniami wirtualnymi od godziny 13:00 do 13:30. Uruchamiasz kolejny pojedynczy serwer usługi Azure Database for PostgreSQL z 16 rdzeniami wirtualnymi od godziny 13:30 do 14:00. Obie te bazy danych są objęte rabatem na rezerwację.

* **Przykład 4** : Uruchamiasz jeden pojedynczy serwer usługi Azure Database for PostgreSQL z 16 rdzeniami wirtualnymi od godziny 13:00 do 13:45. Uruchamiasz kolejny pojedynczy serwer usługi Azure Database for PostgreSQL z 16 rdzeniami wirtualnymi od godziny 13:30 do 14:00. Za 15-minutowy okres jednoczesnego działania obu baz danych jest naliczana opłata według stawek płatności zgodnie z rzeczywistym użyciem. Na użycie zasobów obliczeniowych przez resztę czasu jest stosowany rabat na rezerwację.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).