---
title: Informacje o rabacie na rezerwacje — Azure Database for MariaDB
description: Dowiedz się, w jaki sposób rabat na rezerwacje jest stosowany do usługi Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2197f3ec9907aa9f1afde6272dd2e8a807465476
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240396"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>W jaki sposób rabat za rezerwacje jest stosowany do usługi Azure Database for MariaDB

Po zakupie pojemności zarezerwowanej usługi Azure Database for MariaDB rabat za rezerwację jest automatycznie stosowany do serwerów MariaDB zgodnych z atrybutami i ilością rezerwacji. Rezerwacja obejmuje tylko koszty obliczeniowe pojedynczego serwera usługi Azure Database for MariaDB. Opłata jest naliczana za magazyn i sieć według normalnych stawek.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za rezerwację jest dostępny na zasadzie * **wykorzystaj lub strać** _. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Rabat zastosowany do usługi Azure Database for MariaDB

Rabat za pojemność zarezerwowaną usługi Azure Database for MariaDB jest stosowany do działających serwerów MariaDB z rozliczeniem godzinowym. Kupowana rezerwacja jest dopasowywana do użycia zasobów obliczeniowych emitowanych przez działające pojedyncze serwery usługi Azure Database for MariaDB. W przypadku serwerów MariaDB, które nie działają przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych serwerów usługi Azure Database for MariaDB pasujących do atrybutów rezerwacji. Rabat może być stosowany do serwerów usługi Azure Database for MariaDB, które działają równolegle. Jeśli nie masz serwera MariaDB, który działa przez pełną godzinę i pasuje do atrybutów rezerwacji, nie wykorzystasz w pełni korzyści z rabatu na rezerwację w tej godzinie.

W poniższych przykładach pokazano, w jaki sposób rabat za pojemność zarezerwowaną usługi Azure Database for MariaDB jest stosowany w zależności od liczby zakupionych rdzeni oraz czasu ich działania.

_ **Przykład 1** : Kupujesz pojemność zarezerwowaną usługi Azure Database for MariaDB dla 8 rdzeni wirtualnych. W przypadku korzystania z serwerów usługi Azure Database for MariaDB z 16 rdzeniami wirtualnymi zgodnego z resztą atrybutów rezerwacji opłata jest naliczana według stawek płatności zgodnie z rzeczywistym użyciem za osiem rdzeni wirtualnych użycia mocy obliczeniowej serwera MariaDB, a rabat za rezerwację otrzymujesz na 1 godzinę użycia mocy obliczeniowej serwera MariaDB z 8 rdzeniami wirtualnymi.

W pozostałych przykładach przyjęto założenie, że zakupiona pojemność zarezerwowana usługi Azure Database for MariaDB dotyczy usługi Azure Database for MariaDB z 16 rdzeniami wirtualnymi, a pozostałe atrybuty rezerwacji są zgodne z uruchomionymi serwerami MariaDB.

* **Przykład 2** : Uruchamiasz 2 pojedyncze serwery usługi Azure Database for MariaDB, każdy z 8 rdzeniami wirtualnymi, na 1 godzinę. Rabat za rezerwację z 16 rdzeniami wirtualnymi jest stosowany do użycia mocy obliczeniowej dla obu serwerów usługi Azure Database for MariaDB z 8 rdzeniami wirtualnymi.

* **Przykład 3** : Uruchamiasz jeden serwer usługi Azure Database for MariaDB z 16 rdzeniami wirtualnymi od godziny 13:00 do 13:30. Uruchamiasz kolejny serwer usługi Azure Database for MariaDB z 16 rdzeniami wirtualnymi od godziny 13:30 do 14:00. Obie te bazy danych są objęte rabatem na rezerwację.

* **Przykład 4** : Uruchamiasz jeden serwer usługi Azure Database for MariaDB z 16 rdzeniami wirtualnymi od godziny 13:00 do 13:45. Uruchamiasz kolejny serwer usługi Azure Database for MariaDB z 16 rdzeniami wirtualnymi od godziny 13:30 do 14:00. Za 15-minutowy okres jednoczesnego działania obu baz danych jest naliczana opłata według stawek płatności zgodnie z rzeczywistym użyciem. Na użycie zasobów obliczeniowych przez resztę czasu jest stosowany rabat na rezerwację.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).