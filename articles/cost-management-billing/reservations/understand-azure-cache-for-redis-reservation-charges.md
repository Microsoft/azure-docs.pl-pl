---
title: Omówienie stosowania rabatu za rezerwację do usługi Azure Cache for Redis | Microsoft Docs
description: Dowiedz się, w jaki sposób rabat za rezerwację jest stosowany do usługi Azure Cache for Redis.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 7b57bfbd09cd2b528b5a879acdb41b4716289aa6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372008"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>W jaki sposób rabat za rezerwację jest stosowany do usługi Azure Cache for Redis

Po zakupie pojemności zarezerwowanej usługi Azure Cache for Redis rabat na rezerwację jest automatycznie stosowany do wystąpień pamięci podręcznej pasujących do atrybutów i ilości rezerwacji. Rezerwacja obejmuje tylko koszty obliczeniowe usługi Azure Cache for Redis. Opłata jest naliczana za magazyn i sieć według normalnych stawek. Pojemność zarezerwowana jest dostępna tylko w przypadku pamięci podręcznych [w warstwie Premium](/azure/azure-cache-for-redis/quickstart-create-redis).

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za rezerwację jest dostępny na zasadzie * **wykorzystaj lub strać** _. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Rabat stosowany do usługi Azure Cache for Redis

Rabat za pojemność zarezerwowaną usługi Azure Cache for Redis jest stosowany względem pamięci podręcznych z rozliczeniem godzinowym. Kupowana rezerwacja jest dopasowywana do użycia zasobów obliczeniowych emitowanych przez działające pamięci podręczne. W przypadku, gdy te pamięci podręczne nie działają przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych pamięci podręcznych pasujących do atrybutów rezerwacji. Rabat może być stosowany do pamięci podręcznych, które działają równolegle. Jeśli nie masz pamięci podręcznych, które działają przez pełną godzinę i pasują do atrybutów rezerwacji, nie wykorzystasz w pełni korzyści z rabatu za rezerwację w tej godzinie.

W poniższych przykładach pokazano, w jaki sposób rabat za pojemność zarezerwowaną usługi Azure Cache for Redis jest stosowany w zależności od liczby zakupionych pamięci podręcznych oraz czasu ich działania.

_ **Przykład 1** : Kupujesz pojemność zarezerwowaną usługi Azure Cache for Redis dla pamięci podręcznej 6 GB. Jeśli uruchamiasz pamięć podręczną 13 GB zgodną z resztą atrybutów rezerwacji, opłata jest naliczana według stawek płatności zgodnie z rzeczywistym użyciem za 7 GB użycia mocy obliczeniowej usługi Azure Cache for Redis, a rabat za rezerwację otrzymujesz na 1 godzinę użycia mocy obliczeniowej 6 GB pamięci podręcznej.

Na potrzeby pozostałych przykładów przyjęto założenie, że zakupiona pojemność zarezerwowana usługi Azure Cache for Redis jest przeznaczona dla 26 GB pamięci podręcznej, a pozostałe atrybuty rezerwacji pasują do działającej pamięci podręcznej.

* **Przykład 2** : Uruchamiasz dwie pamięci podręczne 13 GB przez godzinę. Rabat za rezerwację 26 GB jest stosowany do użycia mocy obliczeniowej obu pamięci podręcznych.

* **Przykład 3** : Uruchamiasz jedną pamięć podręczną 26 GB od 13:00 do 13:30. Uruchamiasz kolejną pamięć podręczną 26 GB od 13:30 do 14:00. Obie te bazy danych są objęte rabatem na rezerwację.

* **Przykład 4** : Uruchamiasz jedną pamięć podręczną 26 GB od 13:00 do 13:45. Uruchamiasz kolejną pamięć podręczną 26 GB od 13:30 do 14:00. Za 15-minutowy okres jednoczesnego działania obu baz danych jest naliczana opłata według stawek płatności zgodnie z rzeczywistym użyciem. Na użycie zasobów obliczeniowych przez resztę czasu jest stosowany rabat na rezerwację.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](./understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami
Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).