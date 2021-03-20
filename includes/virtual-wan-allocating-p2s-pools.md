---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919073"
---
W tej sekcji opisano wskazówki i wymagania dotyczące alokacji przestrzeni adresowych klientów, w których wybrana jednostka sieci wirtualnej "punkt-lokacja" VPN Gateway jest większa niż lub równa 40.

### <a name="background"></a>Tło

Bramy sieci VPN typu punkt-lokacja w wirtualnym koncentratorze sieci WAN są wdrażane z wieloma wystąpieniami. Każde wystąpienie bramy sieci VPN typu punkt-lokacja może obsłużyć do 10 000 równoczesnych połączeń użytkowników typu punkt-lokacja. W związku z tym w przypadku jednostek skalowania większych niż 40 wirtualne sieci WAN muszą wdrażać dodatkową pojemność, co wymaga minimalnej liczby pul adresów przyznanych dla różnych jednostek skalowania.

Na przykład jeśli wybrano jednostkę skalowania 100, zostaną wdrożone 5 wystąpień dla VPN Gateway punkt-lokacja w koncentratorze wirtualnym. To wdrożenie może obsłużyć 50 000 połączeń współbieżnych i **co najmniej** 5 odrębnych pul adresów.

**Dostępne jednostki skalowania**

| Jednostka skalowania | Maksymalna liczba obsługiwanych klientów | Minimalna liczba pul adresów |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90 000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Określanie pul adresów

Poniżej znajdują się wskazówki dotyczące wybierania pul adresów. Należy pamiętać, że przypisania puli adresów sieci VPN typu punkt-lokacja są wykonywane automatycznie przez wirtualną sieć WAN.

1. Jedno wystąpienie bramy umożliwia maksymalnie 10 000 połączeń współbieżnych. W związku z tym każda pula adresów powinna zawierać co najmniej 10 000 unikatowych adresów IP RFC1918.
1. Wiele zakresów puli adresów jest automatycznie połączonych i przypisanych do **jednego** wystąpienia bramy. Ten proces odbywa się w sposób okrężny dla wszystkich wystąpień bramy, które mają mniej niż 10 000 adresów IP. Na przykład Pula z adresami 5 000 może być automatycznie łączona przez wirtualną sieć WAN z inną pulą, która ma 8 000 adresów i jest przypisywana do jednego wystąpienia bramy.
1. Pojedyncza Pula adresów jest przypisana tylko do jednego wystąpienia bramy przez wirtualną sieć WAN.
1. Pule adresów muszą być różne. Nie można nakładać się między pulami adresów.

> [!NOTE] 
> Jeśli pula adresów jest skojarzona z wystąpieniem bramy, które jest w trakcie konserwacji, puli adresów nie można ponownie przypisać do innego wystąpienia.

### <a name="example"></a>Przykład 

W poniższym przykładzie opisano sytuację, w której jednostki skalowania 60 obsługują do 30 000 połączeń, ale pule adresów przydzielono mniej niż 30 000 współbieżnych połączeń.

Łączna liczba jednoczesnych połączeń obsługiwanych w tej konfiguracji to 28 192. Pierwsze wystąpienie bramy obsługuje 10 000 adresów, drugie wystąpienie 8 192 połączeń i trzecie wystąpienie obsługuje również adresy 10 000.

| Numer puli adresów | Pula adresów | Obsługiwane połączenia |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10 000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | .10.14.0.0/15 | 10 000|

**Zalecenie #1: Upewnij się, że pula adresów #2 ma co najmniej 10 000 odrębnych adresów IP. (przykład: 10.13.0.0/15)**

**Zalecenie #2: Dodaj jeszcze jedną pulę adresów. (przykład: Pula adresów #4 10.15.0.0/21 z adresami 2048). Pule adresów 2 i 4 zostaną automatycznie połączone i umożliwią temu wystąpieniu bramy obsługę połączeń współbieżnych 10 000.**
