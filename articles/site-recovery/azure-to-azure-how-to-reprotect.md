---
title: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w regionie podstawowym przy użyciu Azure Site Recovery | Microsoft Docs
description: Opisuje sposób ponownego włączania ochrony maszyn wirtualnych platformy Azure po przejściu w tryb failover, do regionu podstawowego, przy użyciu Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 3b9edab6e908b4506a92c78aa8f3f53277b9c17b
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360875"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w regionie podstawowym po przełączeniu w tryb failover

Po przełączeniu maszyn wirtualnych platformy Azure w [tryb failover](site-recovery-failover.md) z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md)maszyny wirtualne są uruchamiane w regionie pomocniczym w stanie **niechronionym** . Jeśli chcesz zakończyć przywracanie maszyn wirtualnych do regionu podstawowego, wykonaj następujące zadania:

1. Ponownie Chroń maszyny wirtualne w regionie pomocniczym, tak aby rozpoczynać replikację do regionu podstawowego.
1. Po zakończeniu ponownych prób ochrony, gdy maszyny wirtualne są replikowane, można przejść do trybu failover z poziomu pomocniczego do regionu podstawowego.

## <a name="prerequisites"></a>Wymagania wstępne

- Należy zatwierdzić tryb failover maszyny wirtualnej z regionu podstawowego do pomocniczego.
- Podstawowa lokacja docelowa powinna być dostępna i powinna mieć możliwość uzyskiwania dostępu do zasobów lub tworzenia ich w tym regionie.

## <a name="reprotect-a-vm"></a>Ponowne włączanie ochrony maszyny wirtualnej

1. W **Vault**obszarze  >  **zreplikowane elementy**magazynu kliknij prawym przyciskiem myszy maszynę wirtualną w trybie failover, a następnie wybierz pozycję **Włącz ponownie ochronę**. Kierunek ochrony powinien być pokazywany z poziomu pomocniczego do podstawowego.

   ![Zrzut ekranu przedstawia maszynę wirtualną z menu kontekstowym z wybranym ponownie ochroną.](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Przejrzyj grupę zasobów, Sieć, magazyn i zestawy dostępności. Następnie kliknij przycisk **OK**. Jeśli istnieją jakieś zasoby oznaczone jako nowe, są one tworzone w ramach procesu ponownego ochrony.
1. Zadanie odochrony odsiewa lokację docelową z najnowszymi danymi. Po zakończeniu zadania replikacja różnicowa odbywa się. Następnie można przejść w tryb failover z powrotem do lokacji głównej. Można wybrać konto magazynu lub sieć, która ma być używana podczas ponownego włączania ochrony, przy użyciu opcji Dostosuj.

   ![Opcja dostosowywania](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Dostosuj ustawienia ponownego włączania ochrony

Podczas ponownej ochrony można dostosować następujące właściwości docelowej maszyny wirtualnej.

![Dostosowywanie](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Właściwość |Uwagi  |
|---------|---------|
|Docelowa Grupa zasobów | Zmodyfikuj docelową grupę zasobów, w której jest tworzona maszyna wirtualna. W ramach ochrony, docelowa maszyna wirtualna jest usuwana. Możesz wybrać nową grupę zasobów, w której ma zostać utworzona maszyna wirtualna po zakończeniu pracy w trybie failover. |
|Docelowa sieć wirtualna | Nie można zmienić sieci docelowej podczas zadania ponownego włączania ochrony. Aby zmienić sieć, wykonaj ponownie mapowanie sieci. |
|Magazyn docelowy (pomocnicza maszyna wirtualna nie używa dysków zarządzanych) | Można zmienić konto magazynu używane przez maszynę wirtualną po zakończeniu pracy w trybie failover. |
|Dyski zarządzane repliki (pomocnicza maszyna wirtualna korzysta z dysków zarządzanych) | Site Recovery tworzy dyski zarządzane repliki w regionie podstawowym w celu dublowania dysków zarządzanych pomocniczej maszyny wirtualnej. |
|Magazyn pamięci podręcznej | Możesz określić konto magazynu pamięci podręcznej, które ma być używane podczas replikacji. Domyślnie zostanie utworzone nowe konto magazynu pamięci podręcznej, jeśli nie istnieje. |
|Zestaw dostępności | Jeśli maszyna wirtualna w regionie pomocniczym jest częścią zestawu dostępności, możesz wybrać zestaw dostępności dla docelowej maszyny wirtualnej w regionie podstawowym. Domyślnie program Site Recovery próbuje znaleźć istniejący zestaw dostępności w regionie podstawowym i korzystać z niego. Podczas dostosowywania można określić nowy zestaw dostępności. |

### <a name="what-happens-during-reprotection"></a>Co się stanie w trakcie ochrony?

Domyślnie są wykonywane następujące czynności:

1. Konto magazynu pamięci podręcznej jest tworzone w regionie, w którym działa maszyna wirtualna w trybie failover.
1. Jeśli docelowe konto magazynu (oryginalne konto magazynu w regionie podstawowym) nie istnieje, zostanie utworzony nowy. Nazwa przypisanego konta magazynu to nazwa konta magazynu używanego przez pomocniczą maszynę wirtualną z sufiksem `asr` .
1. Jeśli maszyna wirtualna korzysta z dysków zarządzanych, dyski zarządzane repliki są tworzone w regionie podstawowym w celu przechowywania danych replikowanych z dysków pomocniczej maszyny wirtualnej.
1. Jeśli docelowy zestaw dostępności nie istnieje, w razie potrzeby zostanie utworzony nowy program w ramach zadania ponownego włączania ochrony. Jeśli ustawienia ponownej ochrony zostały dostosowane, wybrany zestaw jest używany.

Gdy Wyzwalasz zadanie ponownego włączania ochrony i istnieje docelowa maszyna wirtualna, występują następujące sytuacje:

1. Maszyna wirtualna po stronie docelowej jest wyłączona, jeśli jest uruchomiona.
1. Jeśli maszyna wirtualna korzysta z dysków zarządzanych, kopia oryginalnego dysku zostanie utworzona z `-ASRReplica` sufiksem. Oryginalne dyski zostaną usunięte. `-ASRReplica`Kopie są używane na potrzeby replikacji.
1. Jeśli maszyna wirtualna używa dysków niezarządzanych, docelowe dyski danych maszyny wirtualnej są odłączone i używane do replikacji. Kopia dysku systemu operacyjnego jest tworzona i dołączona do maszyny wirtualnej. Oryginalny dysk systemu operacyjnego zostanie odłączony i użyty do replikacji.
1. Synchronizowane są tylko zmiany między dyskiem źródłowym a dyskiem docelowym. Różnice są obliczane przez porównanie dysków, a następnie przetransferowane. Sprawdź poniżej, aby znaleźć szacowany czas na zakończenie ochrony.
1. Po zakończeniu synchronizacji rozpocznie się replikacja różnicowa, a punkt odzyskiwania zostanie utworzony w wierszu z zasadami replikacji.

Gdy Wyzwalasz zadanie ponownego włączania ochrony, a docelowa maszyna wirtualna i dyski nie istnieją:

1. Jeśli maszyna wirtualna korzysta z dysków zarządzanych, dyski repliki są tworzone z `-ASRReplica` sufiksem. `-ASRReplica`Kopie są używane na potrzeby replikacji.
1. Jeśli maszyna wirtualna korzysta z dysków niezarządzanych, na docelowym koncie magazynu są tworzone dyski replik.
1. Wszystkie dyski są kopiowane z obszaru przełączenia w tryb failover do nowego regionu docelowego.
1. Po zakończeniu synchronizacji rozpocznie się replikacja różnicowa, a punkt odzyskiwania zostanie utworzony w wierszu z zasadami replikacji.

#### <a name="estimated-time-to-do-the-reprotection"></a>Szacowany czas na przeprowadzenie ochrony

W większości przypadków Azure Site Recovery nie replikuje pełnych danych do regionu źródłowego.
Poniższe warunki określają, jak dużo danych jest replikowanych:

1. Jeśli źródłowe dane maszyny wirtualnej zostaną usunięte, uszkodzone lub niedostępne z jakiegoś powodu, takie jak zmiana/usunięcie grupy zasobów, podczas ponownej ochrony zostanie wykonana replikacja początkowa, ponieważ w regionie źródłowym nie ma dostępnych danych do użycia.
1. Jeśli źródłowe dane maszyny wirtualnej są dostępne, tylko różnice są obliczane przez porównanie dysków, a następnie przetransferowane. Zapoznaj się z tabelą poniżej, aby uzyskać szacowany czas.

|Przykładowa sytuacja | Czas potrzebny do ponownego włączenia ochrony |
|---|---|
|Region źródłowy ma 1 maszynę wirtualną z 1 TB dysku standardowego.<br/>Używane są tylko dane 127 GB, a pozostała część dysku jest pusta.<br/>Typ dysku to Standard z przepustowością 60 MB/s.<br/>Brak zmian danych po przejściu w tryb failover.| Przybliżony czas: 60-90 minut.<br/> Podczas ponownej ochrony Site Recovery wypełni sumę kontrolną wszystkich danych. To działa w 45MBps, więc łączny czas trwania wynosi 127 GB/45 MB/s, około 45 minut.<br/>Aby Site Recovery do automatycznego skalowania, wymagany jest jakiś czas dodatkowy (około 20-30 minut). |
|Region źródłowy ma 1 maszynę wirtualną z 1 TB dysku standardowego.<br/>Używane są tylko dane 127 GB, a reszta dysku jest pusta.<br/>Typ dysku to Standard z przepustowością 60 MB/s.<br/>zmiany danych 45 GB po przejściu do trybu failover.| Przybliżony czas: 2,5-3 godziny.<br/> Podczas ponownej ochrony Site Recovery wypełni sumę kontrolną wszystkich danych. To działa w 45MBps, więc łączny czas trwania wynosi 127 GB/45 MB/s, około 45 minut.<br/>Szybkość transferu wynosi około 16% przepływności lub 9.6 MB/s. W związku z tym należy przenieść czas, aby zastosować zmiany z 45 GB, czyli 45 GB/9.6 MB/s, około 80 minut.<br/>Aby Site Recovery do automatycznego skalowania, wymagany jest jakiś czas dodatkowy (około 20-30 minut). |
|Region źródłowy ma 1 maszynę wirtualną z 1 TB dysku standardowego.<br/>Używane są tylko 20 GB danych, a reszta dysku jest pusta.<br/>Typ dysku to Standard z przepustowością 60 MB/s.<br/>Początkowe dane na dysku natychmiast po przejściu w tryb failover to 15 GB. Po przejściu w tryb failover wprowadzono 5 GB zmian danych. Łączna liczba wypełnionych danych jest w związku z tym 20 GB.| Przybliżony czas: 1 – 1,5 godz.<br/>Ponieważ dane wypełnione na dysku są mniejsze niż 10% rozmiaru dysku, wykonujemy pełną replikację początkową.<br/> Szybkość transferu wynosi około 16% przepływności lub 9.6 MB/s. W związku z tym należy przenieść czas, aby zastosować zmiany wynoszące 20 GB, czyli 20 GB/9.6 MB/s, około 36 minut.<br/>Aby Site Recovery do automatycznego skalowania, wymagany jest jakiś czas dodatkowy (około 20-30 minut). |
|Region źródłowy ma 1 maszynę wirtualną z dyskiem Premium 1 TB.<br/>Używane są tylko dane 127 GB, a pozostała część dysku jest pusta.<br/>Typ dysku to Premium z przepustowością 200 MB/s.<br/>Brak zmian danych po przejściu w tryb failover.| Przybliżony czas: 2 godziny.<br/>Podczas ponownej ochrony Site Recovery wypełni sumę kontrolną wszystkich danych. Działa to w 25MBps (do 16% przepływności dysku), więc łączny czas trwania będzie 127 GB/25 MB/s, około 87 minut.<br/>Aby Site Recovery do automatycznego skalowania, wymagany jest jakiś czas dodatkowy (około 20-30 minut). |
|Region źródłowy ma 1 maszynę wirtualną z dyskiem Premium 1 TB.<br/>Używane są tylko dane 127 GB, a reszta dysku jest pusta.<br/>Typ dysku to Premium z przepustowością 200 MB/s.<br/>zmiany danych 45 GB po przejściu do trybu failover.| Przybliżony czas: 2,5-3 godziny.<br/>Podczas ponownej ochrony Site Recovery wypełni sumę kontrolną wszystkich danych. Działa to w 25MBps (do 16% przepływności dysku), więc łączny czas trwania będzie 127 GB/25 MB/s, około 87 minut.</br>Szybkość transferu wynosi około 16% przepływności lub 32MBps. W związku z tym należy przenieść czas, aby zastosować zmiany 45 GB, które są 45 GB/32 MB/s, około 24 minuty.<br/>Aby Site Recovery do automatycznego skalowania, wymagany jest jakiś czas dodatkowy (około 20-30 minut). |
|Region źródłowy ma 1 maszynę wirtualną z dyskiem Premium 1 TB.<br/>Używane są tylko 20 GB danych, a reszta dysku jest pusta.<br/>Typ dysku to Premium z przepustowością 200 MB/s.<br/>Początkowe dane na dysku natychmiast po przejściu w tryb failover to 15 GB. Po przejściu w tryb failover wprowadzono 5 GB zmian danych. Łączna liczba wypełnionych danych jest dlatego 20 GB| Przybliżony czas: 30-45 minut.<br/>Ponieważ dane wypełnione na dysku są mniejsze niż 10% rozmiaru dysku, wykonujemy pełną replikację początkową.<br/>Szybkość transferu wynosi około 16% przepływności lub 32MBps. W związku z tym należy przenieść czas, aby zastosować zmiany wynoszące 20 GB, czyli 20 GB/32 MB/s, około 11 minut.<br/>Aby Site Recovery do automatycznego skalowania, wymagany jest jakiś czas dodatkowy (około 20-30 minut) |

Po ponownym włączeniu ochrony maszyny wirtualnej po niepowodzeniu powrotu do regionu podstawowego (tj. Jeśli maszyna wirtualna jest ponownie chroniona z regionu podstawowego do regionu DR), docelowa maszyna wirtualna i skojarzone karty sieciowe zostaną usunięte.

Po ponownym włączeniu ochrony maszyny wirtualnej z regionu DR do regionu podstawowego nie jest usuwana podstawowa maszyna wirtualna Erstwhile ani skojarzone karty sieciowe.

## <a name="next-steps"></a>Następne kroki

Po włączeniu ochrony maszyny wirtualnej można zainicjować pracę w trybie failover. Tryb failover zamyka maszynę wirtualną w regionie pomocniczym i tworzy i uruchamia maszynę wirtualną w regionie podstawowym, z krótkim przestojem w trakcie tego procesu. Zalecamy wybranie odpowiedniego czasu dla tego procesu i przeprowadzenie testowego przejścia w tryb failover przed zainicjowaniem pełnej pracy w trybie failover w lokacji głównej. [Dowiedz się więcej](site-recovery-failover.md) o Azure Site Recovery pracy w trybie failover.
