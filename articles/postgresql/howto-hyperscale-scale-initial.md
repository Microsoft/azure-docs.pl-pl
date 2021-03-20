---
title: Początkowy rozmiar grupy serwerów — Citus) — Azure Database for PostgreSQL
description: Wybierz właściwy rozmiar początkowy przypadku użycia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026407"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Wybierz początkowy rozmiar dla grupy serwerów Citus (preskaling)

Jest to [Łatwa zmiana](howto-hyperscale-scale-grow.md)rozmiaru grupy serwerów, liczby węzłów i ich pojemności sprzętowej. Jednak nadal trzeba wybrać rozmiar początkowy nowej grupy serwerów. Poniżej przedstawiono niektóre wskazówki dotyczące rozsądnego wyboru.

## <a name="multi-tenant-saas-use-case"></a>SaaS użycia wielu dzierżawców

W przypadku migrowania do funkcji Citus) z istniejącego wystąpienia bazy danych z jednym węzłem PostgreSQL wybierz klaster, w którym liczba rdzeni wirtualnych procesów roboczych i pamięci RAM łącznie jest równa pierwotnemu wystąpieniu. W takich scenariuszach pojawiły się następujące udoskonalenia wydajności, ponieważ fragmentowania zwiększa wykorzystanie zasobów, co pozwala na mniejsze indeksy itd.

Liczba rdzeń wirtualny jest w rzeczywistości jedyną decyzją. Alokacja pamięci RAM jest obecnie określana na podstawie liczby rdzeń wirtualny, zgodnie z opisem na stronie [Opcje konfiguracji strony Moje skalowanie (Citus)](concepts-hyperscale-configuration-options.md) .
Węzeł koordynatora nie wymaga jako wielu pamięci RAM jako pracownicy, ale nie ma możliwości niezależnego wyboru pamięci RAM i rdzeni wirtualnych.

## <a name="real-time-analytics-use-case"></a>Przypadek użycia analizy w czasie rzeczywistym

Łącznie rdzeni wirtualnych: gdy praca danych mieści się w pamięci RAM, można spodziewać się liniowe zwiększenie wydajności na potrzeby skalowania (Citus) proporcjonalnie do liczby rdzeni roboczych. Aby określić odpowiednią liczbę rdzeni wirtualnych dla potrzeb, należy wziąć pod uwagę bieżące opóźnienie dla zapytań w bazie danych z jednym węzłem i wymagane opóźnienie w ramach funkcji (Citus). Podziel bieżące opóźnienie przez żądane opóźnienie i zaokrąglij wynik.

Pamięć RAM procesu roboczego: najlepszym rozwiązaniem jest zapewnienie wystarczającej ilości pamięci, aby większość zestawu roboczego mieściła się w pamięci. Typ zapytań używanych przez aplikację wpływa na wymagania dotyczące pamięci. Można uruchomić wyjaśnienie analizowanie zapytania, aby określić, ile pamięci wymaga. Należy pamiętać, że rdzeni wirtualnych i pamięć RAM są skalowane ze sobą, zgodnie z opisem w artykule [Opcje konfiguracji funkcji Moje skalowanie (Citus)](concepts-hyperscale-configuration-options.md) .

## <a name="next-steps"></a>Następne kroki

- [Skalowanie grupy serwerów](howto-hyperscale-scale-grow.md)
- Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
