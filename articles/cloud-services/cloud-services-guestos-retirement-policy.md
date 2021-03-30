---
title: Przewodnik dotyczący zasad obsługi i emerytury dla systemu operacyjnego gościa systemu Azure | Microsoft Docs
description: Zawiera informacje o tym, co firma Microsoft będzie obsługiwała w odniesieniu do systemu operacyjnego gościa platformy Azure używanego przez Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68945448"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Zasady dotyczące obsługi i wycofania systemu operacyjnego gościa platformy Azure
Informacje na tej stronie odnoszą się do systemu operacyjnego gościa platformy Azure ([systemu operacyjnego gościa](cloud-services-guestos-update-matrix.md)) dla Cloud Services procesów roboczych i ról sieci Web (PaaS). Nie ma zastosowania do Virtual Machines (IaaS).

Firma Microsoft opublikowała [zasady pomocy technicznej dla systemu operacyjnego gościa](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Strona, do której czytasz, opisuje, jak zasady są implementowane.

Zasady są

1. Firma Microsoft będzie obsługiwać **co najmniej najnowsze dwie rodziny systemu operacyjnego gościa**. W przypadku wycofania rodziny klienci mają 12 miesięcy od oficjalnej daty wycofania aktualizacji do nowszej obsługiwanej rodziny systemów operacyjnych gościa.
2. Firma Microsoft będzie obsługiwać **co najmniej najnowsze dwie wersje obsługiwanych rodzin systemów operacyjnych gościa**.
3. Firma Microsoft będzie obsługiwać **co najmniej najnowsze dwie wersje zestawu Azure SDK**. Po wycofaniu wersji zestawu SDK klienci będą mieć 12 miesięcy od oficjalnej daty wycofania aktualizacji do nowszej wersji.

Czasami może być obsługiwane więcej niż dwie rodziny lub wydania. Oficjalne informacje o pomocy technicznej systemu operacyjnego gościa będą wyświetlane w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawu SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Wycofanie wersji systemu operacyjnego gościa
Nowe **wersje** systemu operacyjnego gościa są wprowadzane co miesiąc w celu uwzględnienia najnowszych aktualizacji MSRC. Ze względu na regularne aktualizacje comiesięczne wersja systemu operacyjnego gościa jest zwykle wyłączona około 60 dni od jego wydania. To działanie utrzymuje co najmniej dwie wersje systemu operacyjnego gościa dla każdej rodziny dostępne do użycia.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces podczas wycofania rodziny systemów operacyjnych gościa
Po zaogłaszaniu emerytury klienci mają okres 12-miesięczny "przejście", zanim Starsza rodzina zostanie oficjalnie usunięta z usługi. Ten czas przejścia można rozszerzyć o wartość "rozwagą" w firmie Microsoft. Aktualizacje zostaną opublikowane w ramach [wersji systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawu SDK](cloud-services-guestos-update-matrix.md).

Stopniowy proces wycofywania na emeryturę rozpocznie się sześć (6) miesięcy w okresie przejściowym. W tym czasie:

1. Firma Microsoft będzie powiadamiać klientów o wycofaniu.
2. Nowsza wersja zestawu Azure SDK nie obsługuje wycofanej rodziny systemów operacyjnych gościa.
3. Nowe wdrożenia i ponowne wdrożenia Cloud Services nie będą dozwolone dla wycofanej rodziny

Firma Microsoft będzie nadal wprowadzać nową wersję systemu operacyjnego gościa obejmującą najnowsze aktualizacje MSRC do ostatniego dnia okresu przejściowego, nazywanego "datą wygaśnięcia". W dniu wygaśnięcia Cloud Services nadal uruchomiona nie będzie obsługiwana w ramach umowy SLA platformy Azure. Firma Microsoft ma prawo do wymuszania uaktualnienia, usuwania lub zatrzymywania tych usług po tej dacie.

### <a name="process-during-a-guest-os-version-retirement"></a>Przetwarzaj podczas wycofania wersji systemu operacyjnego gościa
Jeśli klienci ustawili automatyczne aktualizowanie systemu operacyjnego gościa, nigdy nie muszą martwić się o zajmowanie się informacjami na temat wersji systemu operacyjnego gościa. Będą zawsze używać najnowszej wersji systemu operacyjnego gościa.

Wersje systemu operacyjnego gościa są publikowane co miesiąc. Ze względu na częstotliwość regularnych wydań każda wersja ma stały cykl życia.

O 60 dni w cykl życia wersja jest *wyłączana*. Wartość "wyłączone" oznacza, że wersja jest usuwana z portalu. Nie można już ustawić wersji z pliku konfiguracyjnego CSCFG. Istniejące wdrożenia pozostają uruchomione. Ale nowe wdrożenia i aktualizacje kodu i konfiguracji do istniejących wdrożeń nie będą dozwolone.

Po zakończeniu "wyłączone" wersja systemu operacyjnego gościa "wygasa" i wszystkie instalacje nadal działają, że wygasła wersja jest narażona na problemy z zabezpieczeniami i lukami w zabezpieczeniach. Ogólnie rzecz biorąc, wygaśnięcie jest wykonywane w partiach, dlatego okres od wyłączenia do wygaśnięcia może się różnić.

Klienci, którzy konfigurują swoje usługi do ręcznego aktualizowania systemu operacyjnego gościa, powinni upewnić się, że ich usługi działają w obsługiwanym systemie operacyjnym gościa. Jeśli usługa jest skonfigurowana do automatycznego aktualizowania systemu operacyjnego gościa, bazowa platforma zapewni zgodność i przeprowadzi uaktualnienie do najnowszego systemu operacyjnego gościa.

Te okresy mogą być dłuższe w zależności od firmy Microsoft w celu ułatwienia przejścia od klientów. Wszystkie zmiany zostaną przekazane do [wersji systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawu SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Powiadomienia podczas wycofania
* **Wycofanie rodziny** <br>Firma Microsoft będzie używać wpisów w blogu i powiadomień portalu. Klienci, którzy nadal korzystają z wycofanej rodziny systemów operacyjnych gościa, otrzymają powiadomienie za pośrednictwem bezpośredniej komunikacji (wiadomość e-mail, wiadomość portalu, połączenie telefoniczne) do przypisanych administratorów usługi. Wszystkie zmiany zostaną ogłoszone w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawu SDK](cloud-services-guestos-update-matrix.md).
* **Wycofanie wersji** <br>Wszystkie zmiany i daty wystąpienia są ogłaszane w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawów SDK](cloud-services-guestos-update-matrix.md), w tym wersji, wyłączone i wygaśnięcie. Administratorzy usług będą otrzymywać wiadomości e-mail, jeśli mają uruchomione wdrożenia w wyłączonej wersji systemu operacyjnego gościa lub rodziny. Terminy tych wiadomości e-mail mogą się różnić. Zwykle są one co najmniej miesiąc przed wyłączeniem, ale ten chronometraż nie jest oficjalną umową SLA.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Jak można złagodzić wpływ migracji?**

Zalecamy użycie najnowszej rodziny systemów operacyjnych gościa do projektowania Cloud Services.

1. Zacznij najpierw zaplanować migrację do nowszej rodziny.
2. Skonfiguruj tymczasowe wdrożenia testowe, aby przetestować usługę w chmurze działającą w nowej rodzinie.
3. Ustaw **automatyczną** wersję systemu operacyjnego gościa (osVersion = * w pliku [cscfg](cloud-services-model-and-package.md#cscfg) ), aby migracja do nowych wersji systemu operacyjnego gościa była wykonywana automatycznie.

**Co zrobić, jeśli moja aplikacja sieci Web wymaga dokładniejszej integracji z systemem operacyjnym?**

Jeśli architektura aplikacji sieci Web zależy od podstawowych funkcji systemu operacyjnego, użyj funkcji obsługiwanych przez platformę, takich jak [zadania uruchamiania](cloud-services-startup-tasks.md) lub inne mechanizmy rozszerzalności. Alternatywnie można również użyć usługi [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS — infrastruktura jako usługa), gdzie użytkownik jest odpowiedzialny za obsługę podstawowego systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z najnowszymi [wersjami systemów operacyjnych gościa](cloud-services-guestos-update-matrix.md).
