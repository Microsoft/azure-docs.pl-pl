---
title: Monitorowanie działania migracji — Azure Database Migration Service
description: Dowiedz się, jak używać Azure Database Migration Service do monitorowania aktywności migracji.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91297474"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorowanie aktywności migracji przy użyciu Azure Database Migration Service
W tym artykule dowiesz się, jak monitorować postęp migracji zarówno na poziomie bazy danych, jak i na poziomie tabeli.

## <a name="monitor-at-the-database-level"></a>Monitoruj na poziomie bazy danych
Aby monitorować aktywność na poziomie bazy danych, Wyświetl blok poziomu bazy danych:

![Blok poziomu bazy danych](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Po wybraniu hiperłącza do bazy danych zostanie wyświetlona lista tabel i postęp migracji.

W poniższej tabeli wymieniono pola w bloku na poziomie bazy danych i opisano różne wartości stanu skojarzone z każdym z nich.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nazwa pola</strong></th>
      <th><strong>Podstan pola</strong></th>
      <th><strong>Opis</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Stan działania</strong></td>
      <td>Uruchomienie</td>
      <td>Działanie migracji jest uruchomione.</td>
    </tr>
    <tr>
      <td>Powodzenie</td>
      <td>Działanie migracji zakończyło się pomyślnie bez problemów.</td>
    </tr>
    <tr>
      <td>Faulted</td>
      <td>Migracja nie powiodła się. Wybierz link "Zobacz szczegóły błędu" w obszarze Szczegóły migracji, aby uzyskać pełny komunikat o błędzie.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Stan</strong></td>
      <td>Inicjowanie</td>
      <td>DMS konfiguruje potok migracji.</td>
    </tr>
    <tr>
      <td>Uruchomienie</td>
      <td>Potok DMS jest uruchomiony i przeprowadza migrację.</td>
    </tr>
    <tr>
      <td>Ukończ</td>
      <td>Migracja została zakończona.</td>
    </tr>
    <tr>
      <td>Niepowodzenie</td>
      <td>Migracja nie powiodła się. Kliknij Szczegóły migracji, aby zobaczyć błędy migracji.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Szczegóły migracji</strong></td>
      <td>Inicjowanie potoku migracji</td>
      <td>DMS konfiguruje potok migracji.</td>
    </tr>
    <tr>
      <td>Pełne ładowanie danych jest w toku</td>
      <td>Usługa DMS wykonuje ładowanie początkowe.</td>
    </tr>
    <tr>
      <td>Gotowe do uruchomienie produkcyjne</td>
      <td>Po zakończeniu ładowania początkowego DMS spowoduje oznaczenie bazy danych jako gotowej do uruchomienie produkcyjne. Użytkownik powinien sprawdzić, czy dane przechwyciły synchronizację ciągłą.</td>
    </tr>
    <tr>
      <td>Wszystkie zmiany zostały zastosowane</td>
      <td>Ładowanie wstępne i ciągła synchronizacja są kompletne. Ten stan występuje również po pomyślnym uruchomienie produkcyjne bazy danych.</td>
    </tr>
    <tr>
      <td>Zobacz szczegóły błędu</td>
      <td>Kliknij link, aby wyświetlić szczegóły błędu.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Czas trwania</strong></td>
      <td>Nie dotyczy</td>
      <td>Łączny czas trwania działania migracji na zakończenie migracji lub błąd migracji.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitoruj na poziomie tabeli — szybkie podsumowanie
Aby monitorować aktywność na poziomie tabeli, zobacz blok poziomu tabeli. W górnej części bloku wyświetlana jest szczegółowa liczba wierszy migrowanych w ramach pełnych obciążeń i aktualizacji przyrostowych. 

Dolna część bloku wyświetla listę tabel i zawiera krótkie podsumowanie postępu migracji.

![Blok poziomu tabeli — szybkie podsumowanie](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

W poniższej tabeli opisano pola wyświetlane w szczegółach na poziomie tabeli.

| Nazwa pola        | Opis       |
| ------------- | ------------- |
| **Ukończono pełne ładowanie**      | Liczba tabel, które ukończyły pełne ładowanie danych. |
| **Pełne ładowanie w kolejce**      | Liczba tabel umieszczonych w kolejce w celu pełnego załadowania.      |
| **Pełne ładowanie ładowania** | Liczba tabel nie powiodła się.      |
| **Aktualizacje przyrostowe**      | Liczba aktualizacji funkcji przechwytywania zmian danych w wierszach zastosowanych do celu. |
| **Wstawiania przyrostowe**      | Liczba wstawek operacji przechwytywania w wierszach zastosowanych do celu.      |
| **Usunięcia przyrostowe** | Liczba usunięć danych przechwytywania w wierszach zastosowanych do celu.      |
| **Oczekujące zmiany**      | Liczba obiektów przestawnych w wierszach, które nadal oczekują na zastosowanie do celu. |
| **Zastosowane zmiany**      | Łączna liczba aktualizacji, wstawianych i usuwanych obiektów przestawnych w wierszach zastosowanych do celu.      |
| **Tabele w stanie błąd** | Liczba tabel, które znajdują się w stanie "Error" podczas migracji. Niektóre przykłady, które mogą przechodzić do stanu błędu, są w przypadku, gdy istnieją duplikaty identyfikowane w elemencie docelowym lub dane nie są zgodne z ładowaniem w tabeli docelowej.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitoruj na poziomie tabeli — szczegółowe podsumowanie
Istnieją dwie karty pokazujące postęp migracji w ramach pełnego ładowania i przyrostowej synchronizacji danych.
    
![Karta pełne ładowanie](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Karta przyrostowa synchronizacja danych](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

W poniższej tabeli opisano pola, które przedstawiono w postęp migracji na poziomie tabeli.

| Nazwa pola        | Opis       |
| ------------- | ------------- |
| **Stan — synchronizacja**      | Działa ciągła synchronizacja. |
| **Insert**      | Liczba wstawek operacji przechwytywania w wierszach zastosowanych do celu.      |
| **Aktualizowanie** | Liczba aktualizacji przechwytywania zmian w wierszach zastosowanych do celu.      |
| **Usuwanie**      | Liczba usunięć danych przechwytywania w wierszach zastosowanych do celu. |
| **Suma zastosowania**      | Łączna liczba aktualizacji, wstawianych i usuwanych obiektów przestawnych w wierszach zastosowanych do celu. |
| **Błędy danych** | Liczba błędów danych w tej tabeli. Przykłady błędów to *511: nie można utworzyć wiersza o rozmiarze% d, który jest większy niż dozwolony maksymalny rozmiar wiersza równy% d, 8114: błąd podczas konwertowania typu danych% ls na% ls.*  Klient powinien wykonać zapytanie z tabeli dms_apply_exceptions w elemencie docelowym platformy Azure, aby wyświetlić szczegóły błędu.    |

> [!NOTE]
> Wartości przestawne dotyczące operacji INSERT, Update i DELETE oraz łącznego zastosowania mogą ulec zmniejszeniu, gdy baza danych jest uruchomienie produkcyjnea, a migracja zostanie uruchomiona ponownie.

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się ze wskazówkami dotyczącymi migracji w [przewodniku migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.