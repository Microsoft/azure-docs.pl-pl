---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl sa — klaster
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń zarządzania klastrami autonomicznymi.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7e9f4b55945afc04e5c826b26632d68cb75e502f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260279"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Zarządzaj autonomicznymi klastrami Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| config | Pobierz konfigurację autonomicznego klastra Service Fabric. |
| Konfiguracja — uaktualnianie | Rozpocznij uaktualnianie konfiguracji autonomicznego klastra Service Fabric. |
| uaktualnienie — stan | Pobierz stan uaktualnienia konfiguracji klastra dla autonomicznego klastra Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa — Konfiguracja klastra
Pobierz konfigurację autonomicznego klastra Service Fabric.

Konfiguracja klastra zawiera właściwości klastra obejmujące różne typy węzłów w klastrze, konfiguracje zabezpieczeń, awarie i topologie domeny uaktualnienia itp.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Configuration-API-Version [wymagane] | Wersja interfejsu API konfiguracji pliku JSON klastra autonomicznego. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa — Konfiguracja klastra — uaktualnienie
Rozpocznij uaktualnianie konfiguracji autonomicznego klastra Service Fabric.

Sprawdź poprawność dostarczonych parametrów uaktualnienia konfiguracji i Rozpocznij uaktualnianie konfiguracji klastra, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --cluster-config [wymagane] | Konfiguracja klastra. |
| --Application-Health-policies | Zakodowany słownik JSON par nazw typu aplikacji i maksymalny procent w złej kondycji przed podnoszeniem błędu. |
| --w złej kondycji — węzły | Maksymalny dozwolony procent obniżenia kondycji różnicowej podczas uaktualniania. Dozwolone wartości to liczby całkowite z przedziału od zera do 100. |
| --Health-Check-retry | Czas między próbami sprawdzenia kondycji, jeśli aplikacja lub klaster nie są w dobrej kondycji.  Domyślny \: PT0H0M0S. |
| --Kondycja-sprawdzanie stabilne | Ilość czasu, przez jaką aplikacja lub klaster muszą pozostawać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia.  Domyślny \: PT0H0M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Health-Check-wait | Czas oczekiwania po zakończeniu domeny uaktualnienia przed rozpoczęciem procesu kontroli kondycji.  Domyślny \: PT0H0M0S. |
| --timeout-t | Wartość domyślna \: 60. |
| --zła kondycja — aplikacje | Maksymalny dozwolony procent aplikacji w złej kondycji podczas uaktualniania. Dozwolone wartości to liczby całkowite z przedziału od zera do 100. |
| --zła kondycja — węzły | Maksymalny dozwolony procent węzłów w złej kondycji podczas uaktualniania. Dozwolone wartości to liczby całkowite z przedziału od zera do 100. |
| --Upgrade-Domain-Delta-zła kondycja — węzły | Maksymalny dozwolony procent obniżenia kondycji domeny uaktualnienia podczas uaktualniania. Dozwolone wartości to liczby całkowite z przedziału od zera do 100. |
| --Upgrade-Domain-timeout | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem FailureAction.  Domyślny \: PT0H0M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Upgrade-timeout | Czas, przez jaki całkowite uaktualnienie musi zakończyć się przed wykonaniem FailureAction.  Domyślny \: PT0H0M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Rozpocznij aktualizację konfiguracji klastra
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa — uaktualnienie klastra — stan
Pobierz stan uaktualnienia konfiguracji klastra dla autonomicznego klastra Service Fabric.

Pobierz szczegóły stanu uaktualnienia konfiguracji klastra dla autonomicznego klastra Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](./scripts/sfctl-upgrade-application.md).
