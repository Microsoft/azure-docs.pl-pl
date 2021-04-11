---
title: Początkowy rozmiar grupy serwerów — Citus) — Azure Database for PostgreSQL
description: Wybierz właściwy rozmiar początkowy przypadku użycia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012510"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Wybierz początkowy rozmiar dla grupy serwerów Citus (preskaling)

[Można łatwo zmieniać](howto-hyperscale-scale-grow.md)rozmiar grupy serwerów, liczbę węzłów i ich pojemność sprzętu. Jednak nadal trzeba wybrać rozmiar początkowy nowej grupy serwerów. Poniżej przedstawiono niektóre wskazówki dotyczące rozsądnego wyboru.

## <a name="use-cases"></a>Przypadki użycia

Funkcja przeskalowania (Citus) jest często używana w następujący sposób.

### <a name="multi-tenant-saas"></a>Wielodostępne oprogramowanie SaaS

W przypadku migrowania do funkcji Citus) z istniejącego wystąpienia bazy danych z jednym węzłem PostgreSQL wybierz klaster, w którym liczba rdzeni wirtualnych procesów roboczych i pamięci RAM łącznie jest równa pierwotnemu wystąpieniu. W takich scenariuszach pojawiły się następujące udoskonalenia wydajności, ponieważ fragmentowania zwiększa wykorzystanie zasobów, co pozwala na mniejsze indeksy itd.

Liczba rdzeń wirtualny jest w rzeczywistości jedyną decyzją. Alokacja pamięci RAM jest obecnie określana na podstawie liczby rdzeń wirtualny, zgodnie z opisem na stronie [Opcje konfiguracji strony Moje skalowanie (Citus)](concepts-hyperscale-configuration-options.md) .
Węzeł koordynatora nie wymaga jako wielu pamięci RAM jako pracownicy, ale nie ma możliwości niezależnego wyboru pamięci RAM i rdzeni wirtualnych.

### <a name="real-time-analytics"></a>Analiza w czasie rzeczywistym

Łącznie rdzeni wirtualnych: gdy praca danych mieści się w pamięci RAM, można spodziewać się liniowe zwiększenie wydajności na potrzeby skalowania (Citus) proporcjonalnie do liczby rdzeni roboczych. Aby określić odpowiednią liczbę rdzeni wirtualnych dla potrzeb, należy wziąć pod uwagę bieżące opóźnienie dla zapytań w bazie danych z jednym węzłem i wymagane opóźnienie w ramach funkcji (Citus). Podziel bieżące opóźnienie przez żądane opóźnienie i zaokrąglij wynik.

Pamięć RAM procesu roboczego: najlepszym rozwiązaniem jest zapewnienie wystarczającej ilości pamięci, która jest wystarczająca dla zestawu roboczego w pamięci. Typ zapytań używanych przez aplikację wpływa na wymagania dotyczące pamięci. Można uruchomić wyjaśnienie analizowanie zapytania, aby określić, ile pamięci wymaga. Należy pamiętać, że rdzeni wirtualnych i pamięć RAM są skalowane ze sobą, zgodnie z opisem w artykule [Opcje konfiguracji funkcji Moje skalowanie (Citus)](concepts-hyperscale-configuration-options.md) .

## <a name="choosing-a-hyperscale-citus-tier"></a>Wybieranie warstwy ze skalowaniem (Citus)

> [!IMPORTANT]
> Warstwa Podstawowa Citus (deskale) jest obecnie dostępna w wersji zapoznawczej.  Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

W powyższych sekcjach przedstawiono, ile rdzeni wirtualnych i ile pamięci RAM są zbędne dla każdego przypadku użycia. Można spełnić te wymagania przez wybór między dwiema warstwami wieloskal (Citus): warstwą podstawową i warstwą standardowa.

Warstwa Podstawowa używa jednego węzła bazy danych do przeprowadzenia przetwarzania, podczas gdy warstwa standardowa zezwala na więcej węzłów. Warstwy różnią się w inny sposób, oferując te same funkcje. W niektórych przypadkach rdzeni wirtualnych i miejsce na dysku jednego węzła można skalować do wystarczającej liczby, a w innych przypadkach wymaga ono współpracy z wieloma węzłami.

Porównanie warstw można znaleźć na stronie pojęcia dotyczące [warstwy podstawowej](concepts-hyperscale-tiers.md) .

## <a name="next-steps"></a>Następne kroki

- [Skalowanie grupy serwerów](howto-hyperscale-scale-grow.md)
- Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
