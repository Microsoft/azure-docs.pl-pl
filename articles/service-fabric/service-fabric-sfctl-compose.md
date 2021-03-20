---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — tworzenie sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń dla Docker Compose aplikacji.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: e84b35ff3dfa7be0c848a4d7b906c622d9b88914
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257351"
---
# <a name="sfctl-compose"></a>sfctl compose
Twórz i usuwaj Docker Compose aplikacje oraz zarządzaj nimi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy wdrożenie Service Fabric tworzenia. |
| list | Pobiera listę wdrożeń tworzenia utworzonych w klastrze Service Fabric. |
| remove | Usuwa istniejące Service Fabric redagowanie wdrożenia z klastra. |
| status | Pobiera informacje o rozmieszczeniu tworzenia Service Fabric. |
| uaktualnienie | Zaczyna uaktualniać wdrożenie redagowania w klastrze Service Fabric. |
| uaktualnienie — wycofanie | Uruchamia wycofywanie uaktualnienia redagowania wdrożenia w klastrze Service Fabric. |
| uaktualnienie — stan | Pobiera szczegóły dotyczące najnowszego uaktualnienia wykonywanego na tym Service Fabric redagowanie wdrożenia. |

## <a name="sfctl-compose-create"></a>Tworzenie sfctl Utwórz
Tworzy wdrożenie Service Fabric tworzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Nazwa wdrożenia. |
| --ścieżka pliku [wymagana] | Ścieżka do docelowego pliku Docker Compose. |
| --Encrypted-Pass | Zamiast monitowania o hasło rejestru kontenera, należy użyć już zaszyfrowanego frazy. |
| --zakończone powodzeniem | Zostanie wyświetlony monit o podanie hasła do rejestru kontenerów. |
| --timeout-t | Wartość domyślna \: 60. |
| --user | Nazwa użytkownika, aby połączyć się z rejestrem kontenerów. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-list"></a>Lista redagowania sfctl
Pobiera listę wdrożeń tworzenia utworzonych w klastrze Service Fabric.

Pobiera stan utworzonych wdrożeń tworzenia lub w procesie tworzonym w klastrze Service Fabric. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożeń redagowania. Jeśli lista wdrożeń nie mieści się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --Max-Results | Maksymalna liczba wyników do zwrócenia w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę wartości zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w komunikacie zgodnie z maksymalnymi ograniczeniami rozmiaru komunikatów zdefiniowanymi w konfiguracji. Jeśli ten parametr ma wartość zero lub nie zostanie określony, zapytanie stronicowane zawiera tyle wyników, ile to możliwe, które mieszczą się w komunikacie zwrotnym. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-remove"></a>Usuwanie sfctl
Usuwa istniejące Service Fabric redagowanie wdrożenia z klastra.

Usuwa istniejące wdrożenie Service Fabric tworzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-status"></a>stan redagowania sfctl
Pobiera informacje o rozmieszczeniu tworzenia Service Fabric.

Zwraca stan utworzonego wdrożenia tworzenia lub w procesie tworzenia w klastrze Service Fabric, którego nazwa jest zgodna z nazwą określoną jako parametr. Odpowiedź zawiera nazwę, stan i inne szczegóły dotyczące wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade"></a>sfctl — uaktualnienie
Zaczyna uaktualniać wdrożenie redagowania w klastrze Service Fabric.

Sprawdza poprawność podanych parametrów uaktualnienia i zaczyna uaktualniać wdrożenie, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Nazwa wdrożenia. |
| --ścieżka pliku [wymagana] | Ścieżka do docelowego pliku redagowania platformy Docker. |
| --Default-SVC-Type-Health-map | Zakodowany słownik JSON, który opisuje zasady dotyczące kondycji używane do oszacowania kondycji usług. |
| --Encrypted-Pass | Zamiast monitowania o hasło rejestru kontenera, należy użyć już zaszyfrowanego frazy. |
| --Niepowodzenie-akcja | Możliwe wartości to \: "nieprawidłowe", "Wycofaj", "ręczne". |
| --Wymuś ponowne uruchomienie | Procesy są wymuszane ponownie po uaktualnieniu, nawet gdy wersja kodu nie została zmieniona. <br><br> Uaktualnienie zmienia tylko konfigurację lub dane. |
| --zakończone powodzeniem | Zostanie wyświetlony monit o podanie hasła do rejestru kontenerów. |
| --Health-Check-retry | Czas między próbami sprawdzenia kondycji, jeśli aplikacja lub klaster nie są w dobrej kondycji. |
| --Kondycja-sprawdzanie stabilne | Ilość czasu, przez jaką aplikacja lub klaster muszą pozostawać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Health-Check-wait | Czas oczekiwania po zakończeniu domeny uaktualnienia przed rozpoczęciem procesu kontroli kondycji. |
| --— Sprawdzanie zestawu replik | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. <br><br> Po upływie tego limitu czasu przetwarzanie domeny uaktualnienia będzie przebiegać bez względu na problemy z utratą dostępności. Limit czasu jest resetowany na początku każdej domeny uaktualnienia. Prawidłowe wartości należą do zakresu od 0 do 42949672925 włącznie. |
| --SVC-Type-Health-map | Zakodowana w formacie JSON lista obiektów, które opisują zasady dotyczące kondycji używane do obliczania kondycji różnych typów usług. |
| --timeout-t | Wartość domyślna \: 60. |
| --zła kondycja-aplikacja | Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić złej kondycji 10% aplikacji, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na łączną liczbę wystąpień aplikacji w klastrze. |
| --Upgrade-Domain-timeout | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --rodzaj uaktualnienia | Domyślne \: Przetaczanie. |
| --Tryb uaktualnienia | Możliwe wartości to \: "nieprawidłowe", "UnmonitoredAuto", "UnmonitoredManual", "monitorowane".  Domyślny \: UnmonitoredAuto. |
| --Upgrade-timeout | Czas, przez jaki całkowite uaktualnienie musi zakończyć się przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --user | Nazwa użytkownika, aby połączyć się z rejestrem kontenerów. |
| --Ostrzeżenie-jako błąd | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl redagowania — wycofywanie
Uruchamia wycofywanie uaktualnienia redagowania wdrożenia w klastrze Service Fabric.

Wycofywanie uaktualnienia tworzenia i wdrażania usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl redagowania — stan
Pobiera szczegóły dotyczące najnowszego uaktualnienia wykonywanego na tym Service Fabric redagowanie wdrożenia.

Zwraca informacje o stanie uaktualnienia redagowania wdrożenia wraz ze szczegółami dotyczącymi pomocy w debugowaniu problemów z kondycją aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Deployment-Name [wymagane] | Tożsamość wdrożenia. |
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
