---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — partycja sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń zarządzania partycjami dla usługi.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fff569c586548d84ed55018764363ad7f05e526d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257091"
---
# <a name="sfctl-partition"></a>sfctl partition
Wykonywanie zapytań dotyczących partycji i zarządzanie nimi dla dowolnej usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| utrata danych | Ten interfejs API będzie powodować utratę danych dla określonej partycji. |
| utrata danych — stan | Pobiera postęp operacji utraty danych partycji rozpoczętej przy użyciu interfejsu API StartDataLoss. |
| kondycji | Pobiera kondycję określonej partycji Service Fabric. |
| informacje o | Pobiera informacje o partycji Service Fabric. |
| list | Pobiera listę partycji usługi Service Fabric. |
| ładowanie | Pobiera informacje o ładowaniu określonej partycji Service Fabric. |
| obciążenie — Resetowanie | Resetuje bieżące obciążenie partycji Service Fabric. |
| kworum — utrata | Wywołuje utratę kworum dla danej partycji usługi stanowej. |
| kworum — utrata stanu | Pobiera postęp operacji utraty kworum na partycji uruchomionej przy użyciu interfejsu API StartQuorumLoss. |
| odzyskiwanie | Wskazuje klaster Service Fabric, który powinien próbować odzyskać określoną partycję, która aktualnie jest zablokowana w utracie kworum. |
| Odzyskaj — wszystkie | Wskazuje klaster Service Fabric, który ma podejmować próbę odzyskania wszelkich usług (w tym usług systemowych), które są aktualnie zablokowane w utracie kworum. |
| Raport — kondycja | Wysyła raport o kondycji na partycji Service Fabric. |
| restart | Ten interfejs API spowoduje ponowne uruchomienie niektórych lub wszystkich replik lub wystąpień określonej partycji. |
| restart-status | Pobiera postęp operacji PartitionRestart rozpoczęty przy użyciu StartPartitionRestart. |
| SVC — nazwa | Pobiera nazwę usługi Service Fabric dla partycji. |

## <a name="sfctl-partition-data-loss"></a>dane partycji sfctl — utrata
Ten interfejs API będzie powodować utratę danych dla określonej partycji.

Wywołanie interfejsu API OnDataLossAsync partycji zostanie wyzwolone.  Ten interfejs API będzie powodować utratę danych dla określonej partycji. Wywołanie interfejsu API OnDataLoss partycji zostanie wyzwolone. Rzeczywista utrata danych będzie zależeć od określonego parametru datastrata.
- PartialDataLoss: Usunięto tylko kworum replik, a OnDataLoss jest wyzwalany dla partycji, ale rzeczywista utrata danych zależy od obecności replikacji w locie.  
- FullDataLoss: wszystkie repliki są usuwane, dlatego wszystkie dane są tracone i OnDataLoss jest wyzwalane. Ten interfejs API powinien być wywoływany tylko z usługą stanową jako obiektem docelowym. Wywoływanie tego interfejsu API z usługą systemową jako obiekt docelowy nie jest zalecane.

> [!NOTE]   
> Po wywołaniu tego interfejsu API nie można go cofnąć. Wywołanie CancelOperation spowoduje zatrzymanie i oczyszczenie wewnętrznego stanu systemu. Dane nie zostaną przywrócone, jeśli polecenie ma wystarczająco dużo miejsca, aby spowodować utratę danych. Wywołaj interfejs API GetDataLossProgress z tym samym OperationId, aby zwrócić informacje dotyczące operacji uruchomionej przy użyciu tego interfejsu API.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --tryb utraty danych [wymagane] | Ten element Enum jest przesyłany do interfejsu API StartDataLoss w celu wskazania typu utraty danych, który ma zostać wywołany. |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-data-loss-status"></a>dane partycji sfctl — utrata stanu
Pobiera postęp operacji utraty danych partycji rozpoczętej przy użyciu interfejsu API StartDataLoss.

Pobiera postęp operacji utraty danych rozpoczętej z StartDataLoss przy użyciu OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-health"></a>Kondycja partycji sfctl
Pobiera kondycję określonej partycji Service Fabric.

Użyj EventsHealthStateFilter, aby odfiltrować kolekcję zdarzeń kondycji zgłoszonych w usłudze na podstawie stanu kondycji. Użyj ReplicasHealthStateFilter, aby odfiltrować kolekcję obiektów ReplicaHealthState na partycji. W przypadku określenia partycji, która nie istnieje w magazynie kondycji, żądanie zwróci błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Events-kondycja-filtr | Umożliwia filtrowanie kolekcji obiektów HealthEvent zwracanych na podstawie stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia zgodne z filtrem. Wszystkie zdarzenia są używane do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, zwracane są wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżenie (4).  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Exclude-Health-Statistics | Wskazuje, czy statystyki kondycji mają być zwracane jako część wyniku zapytania. Domyślnie wartość false. Statystyki przedstawiają liczbę jednostek podrzędnych w stanie kondycji prawidłowy, ostrzegawczy i błąd. |
| --Replicas-Health-State-Filter | Umożliwia filtrowanie kolekcji obiektów ReplicaHealthState na partycji. Wartość można uzyskać od członków lub operacji bitowych na elementach członkowskich HealthStateFilter. Zwracane są tylko repliki zgodne z filtrem. Wszystkie repliki zostaną użyte do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zostaną zwrócone wszystkie wpisy. Wartości stanu są wyliczaniem opartym na flagach, więc wartość może być kombinacją wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład, jeśli podana wartość to 6, zostaną zwrócone wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżeniem (4). Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji.  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-info"></a>Informacje o partycji sfctl
Pobiera informacje o partycji Service Fabric.

Pobiera informacje o określonej partycji. Odpowiedź obejmuje identyfikator partycji, informacje o schemacie partycjonowania, klucze obsługiwane przez partycję, stan, kondycję oraz inne szczegóły dotyczące partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-list"></a>Lista partycji sfctl
Pobiera listę partycji usługi Service Fabric.

Odpowiedź obejmuje identyfikator partycji, informacje o schemacie partycjonowania, klucze obsługiwane przez partycję, stan, kondycję oraz inne szczegóły dotyczące partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-load"></a>Ładowanie partycji sfctl
Pobiera informacje o ładowaniu określonej partycji Service Fabric.

Zwraca informacje o ładowaniu określonej partycji. Odpowiedź zawiera listę raportów obciążenia dla partycji Service Fabric. Każdy raport zawiera nazwę metryki ładowania, wartość i ostatni raportowany czas w formacie UTC.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-load-reset"></a>Ładowanie partycji sfctl — Resetowanie
Resetuje bieżące obciążenie partycji Service Fabric.

Resetuje bieżące obciążenie partycji Service Fabric do domyślnego obciążenia dla usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-quorum-loss"></a>kworum partycji sfctl — utrata
Wywołuje utratę kworum dla danej partycji usługi stanowej.

Ten interfejs API jest przydatny do tymczasowej utraty kworum w usłudze. Wywołaj interfejs API GetQuorumLossProgress z tym samym OperationId, aby zwrócić informacje dotyczące operacji uruchomionej przy użyciu tego interfejsu API. Ta właściwość może być wywoływana tylko w przypadku utrwalania stanowych (HasPersistedState = = true) Services.  Nie należy używać tego interfejsu API w przypadku usług bezstanowych lub stanowych usług tylko w pamięci.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --kworum-utrata wartości [wymagane] | Czas przechowywania partycji w utracie kworum.  Ten ciąg musi być określony w sekundach. |
| --kworum-tryb utraty [wymagane] | To wyliczenie jest przesyłane do interfejsu API StartQuorumLoss w celu wskazania typu utraty kworum do wywołania. |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-quorum-loss-status"></a>kworum sfctl — utrata stanu
Pobiera postęp operacji utraty kworum na partycji uruchomionej przy użyciu interfejsu API StartQuorumLoss.

Pobiera postęp operacji utraty kworum rozpoczętej z StartQuorumLoss przy użyciu podanej OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-recover"></a>odzyskiwanie partycji sfctl
Wskazuje klaster Service Fabric, który powinien próbować odzyskać określoną partycję, która aktualnie jest zablokowana w utracie kworum.

Tę operację należy wykonać tylko wtedy, gdy wiadomo, że repliki nie mogą zostać odzyskane. Nieprawidłowe użycie tego interfejsu API może spowodować utratę danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-recover-all"></a>odzyskiwanie partycji sfctl — wszystkie
Wskazuje klaster Service Fabric, który ma podejmować próbę odzyskania wszelkich usług (w tym usług systemowych), które są aktualnie zablokowane w utracie kworum.

Tę operację należy wykonać tylko wtedy, gdy wiadomo, że repliki nie mogą zostać odzyskane. Nieprawidłowe użycie tego interfejsu API może spowodować utratę danych.

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

## <a name="sfctl-partition-report-health"></a>Raport partycji sfctl — kondycja
Wysyła raport o kondycji na partycji Service Fabric.

Zgłasza stan kondycji określonej partycji Service Fabric. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raportowany. Raport jest wysyłany do partycji bramy Service Fabric, która przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak stary numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji zdarzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Health-Property [wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem i nie jest stałym wyliczeniem, aby umożliwić elastyczność programu Reporter na kategoryzowanie warunku stanu, który wyzwala raport. Na przykład, Raport z identyfikatorem SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może raportować Właściwość "AvailableDisk" w tym węźle. Ten sam raport może monitorować łączność z węzłami, dzięki czemu może zgłosić Właściwość "łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia dotyczące kondycji dla określonego węzła. Wraz z identyfikatorem SourceId Właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --kondycja — stan [wymagane] | Możliwe wartości to \: "nieprawidłowe", "OK", "ostrzeżenie", "błąd", "nieznany". |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --source-ID [wymagane] | Nazwa źródłowa, która identyfikuje składnik klienta/alarm/składnika systemu, który wygenerował informacje o kondycji. |
| --Description | Opis informacji o kondycji. <br><br> Reprezentuje on bezpłatny tekst używany do dodawania informacji ludzkich o raporcie. Maksymalna długość ciągu dla opisu to 4096 znaków. Jeśli podany ciąg jest dłuższy, zostanie automatycznie obcięty. Po obcięciu ostatnie znaki opisu zawierają znacznik "[obcięty]", a całkowity rozmiar ciągu to 4096 znaków. Obecność znacznika wskazuje użytkownikom, że wystąpiło obcinanie. Należy pamiętać, że w przypadku obcinania opis ma mniej niż 4096 znaków od oryginalnego ciągu. |
| --natychmiastowe | Flaga wskazująca, czy raport ma być wysyłany od razu. <br><br> Raport o kondycji jest wysyłany do aplikacji bramy Service Fabric, która przekazuje do magazynu kondycji. Jeśli jest ustawiona wartość true, raport jest wysyłany natychmiast z bramy HTTP do magazynu kondycji, niezależnie od ustawień klienta sieci szkieletowej używanej przez aplikację bramy HTTP. Jest to przydatne w przypadku raportów o kluczowym znaczeniu, które powinny być wysyłane najszybciej, jak to możliwe. W zależności od chronometrażu i innych warunków wysyłanie raportu może nadal zakończyć się niepowodzeniem, na przykład jeśli Brama HTTP jest ZAMKNIĘTA lub komunikat nie dociera do bramy. Jeśli wartość jest ustawiona na wartość FAŁSZ, raport jest wysyłany na podstawie ustawień klienta kondycji z bramy HTTP. W związku z tym będzie on przetwarzany wsadowo zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie, ponieważ pozwala klientowi kondycji zoptymalizować komunikaty raportowania kondycji do magazynu kondycji, a także przetwarzania raportów kondycji. Domyślnie raporty nie są wysyłane od razu. |
| --Usuń po wygaśnięciu | Wartość wskazująca, czy raport został usunięty z magazynu kondycji po jego wygaśnięciu. <br><br> W przypadku ustawienia wartości true raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli zostanie ustawiona na wartość false, raport jest traktowany jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie fałszywa. Gdy klienci raportują okresowo, powinni ustawić RemoveWhenExpired false (domyślnie). W ten sposób, to program reporter ma problemy (np. zakleszczenie) i nie może zgłosić, gdy Raport kondycji wygaśnie. Oznacza to, że jednostka jest w stanie błędu kondycji. |
| --Sequence-Number | Numer sekwencyjny dla tego raportu kondycji jako ciąg liczbowy. <br><br> Numer sekwencyjny raportu jest używany przez magazyn kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer sekwencyjny jest generowany automatycznie przez klienta kondycji, gdy raport zostanie dodany. |
| --timeout-t | Wartość domyślna \: 60. |
| --TTL | Czas trwania okresu ważności tego raportu kondycji. To pole używa formatu ISO8601 do określenia czasu trwania. <br><br> Gdy klienci raportują okresowo, powinni wysyłać raporty o wyższej częstotliwości niż czas wygaśnięcia. Jeśli klienci raportują przejście, mogą ustawić czas wygaśnięcia na wartość nieskończoną. Po wygaśnięciu wygaśnięcia zdarzenia dotyczącego kondycji, które zawiera informacje o kondycji, zostaną usunięte z magazynu kondycji, jeśli RemoveWhenExpired ma wartość true lub jest oceniane w przypadku błędu, jeśli RemoveWhenExpired false. Jeśli nie zostanie określony, wartość czasu wygaśnięcia będzie równa wartości nieskończonej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-restart"></a>ponowne uruchamianie partycji sfctl
Ten interfejs API spowoduje ponowne uruchomienie niektórych lub wszystkich replik lub wystąpień określonej partycji.

Ten interfejs API jest przydatny do testowania pracy w trybie failover. Jeśli jest używana do określania bezstanowej partycji usługi, tryb restartpartitionmode musi mieć wartość AllReplicasOrInstances. Wywołaj interfejs API GetPartitionRestartProgress przy użyciu tego samego OperationId, aby uzyskać postęp.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --restart-Partition-Mode [wymagane] | Opisz partycje, które mają zostać ponownie uruchomione. |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-restart-status"></a>ponowne uruchamianie partycji sfctl — stan
Pobiera postęp operacji PartitionRestart rozpoczęty przy użyciu StartPartitionRestart.

Pobiera postęp PartitionRestart rozpoczęty z StartPartitionRestart przy użyciu podanej OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-svc-name"></a>sfctl — nazwa
Pobiera nazwę usługi Service Fabric dla partycji.

Pobiera nazwę usługi dla określonej partycji. Błąd 404 jest zwracany, jeśli identyfikator partycji nie istnieje w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
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
