---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — klaster sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do zarządzania klastrami.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 05132e4c7e644e681e2e4cfdedaa2b04a066ebeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259968"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Wybieranie klastrów Service Fabric i zarządzanie nimi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| kod — wersje | Pobiera listę wersji kodu sieci szkieletowej, które są obsługiwane w klastrze Service Fabric. |
| config — wersje | Pobiera listę wersji konfiguracji sieci szkieletowej, które są obsługiwane w klastrze Service Fabric. |
| kondycji | Pobiera kondycję klastra Service Fabric. |
| manifest | Pobierz manifest klastra Service Fabric. |
| Operacja — anulowanie | Anuluje operację niepowodzeniem wywoływaną przez użytkownika. |
| Lista operacji | Pobiera listę wywołanych przez użytkownika operacji odfiltrowanych przez podane dane wejściowe. |
| przepis | Inicjowanie obsługi kodu lub pakietów konfiguracyjnych klastra Service Fabric. |
| odzyskiwanie — system | Wskazuje klaster Service Fabric, który powinien próbować odzyskać usługi systemowe, które są aktualnie zablokowane w utracie kworum. |
| Raport — kondycja | Wysyła raport o kondycji w klastrze Service Fabric. |
| Wybierz | Nawiązuje połączenie z punktem końcowym klastra Service Fabric. |
| Pokaż — połączenie | Pokaż klaster Service Fabric, z którym jest połączone to wystąpienie sfctl. |
| Wstrzymaj obsługę administracyjną | Cofanie aprowizacji kodu lub pakietów konfiguracyjnych klastra Service Fabric. |
| uaktualnienie | Rozpocznij uaktualnianie kodu lub wersji konfiguracji klastra Service Fabric. |
| uaktualnienie — wznawianie | Uaktualnienie klastra można przenieść do następnej domeny uaktualnienia. |
| uaktualnienie — wycofanie | Wycofywanie uaktualnienia klastra Service Fabric. |
| uaktualnienie — stan | Pobiera postęp bieżącego uaktualniania klastra. |
| Uaktualnij — aktualizacja | Zaktualizuj parametry uaktualnienia Service Fabric uaktualniania klastra. |

## <a name="sfctl-cluster-code-versions"></a>kod klastra sfctl — wersje
Pobiera listę wersji kodu sieci szkieletowej, które są obsługiwane w klastrze Service Fabric.

Pobiera listę informacji o wersjach kodu sieci szkieletowej, które są obsługiwane w klastrze. Parametr CodeVersion może służyć do opcjonalnego filtrowania danych wyjściowych tylko do określonej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Code-Version | Wersja produktu Service Fabric. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-config-versions"></a>Konfiguracja klastra sfctl — wersje
Pobiera listę wersji konfiguracji sieci szkieletowej, które są obsługiwane w klastrze Service Fabric.

Pobiera listę informacji o wersjach konfiguracji sieci szkieletowej, które są obsługiwane w klastrze. Parametr ConfigVersion może służyć do opcjonalnego filtrowania danych wyjściowych tylko do określonej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --config-Version | Wersja konfiguracji Service Fabric. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-health"></a>Kondycja klastra sfctl
Pobiera kondycję klastra Service Fabric.

Użyj EventsHealthStateFilter, aby odfiltrować kolekcję zdarzeń kondycji zgłoszonych w klastrze na podstawie stanu kondycji. Podobnie należy używać NodesHealthStateFilter i ApplicationsHealthStateFilter do filtrowania kolekcji węzłów i aplikacji zwracanych w oparciu o zagregowany stan kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Applications-Health-State-Filter | Umożliwia filtrowanie obiektów stanu kondycji aplikacji zwracanych w wyniku zapytania o kondycję klastra w oparciu o ich stan kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą uzyskaną z elementów członkowskich lub operacji bitowych na elementach członkowskich wyliczenia HealthStateFilter. Zwracane są tylko aplikacje zgodne z filtrem. Wszystkie aplikacje są używane do szacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczaniem opartym na flagach, więc wartość może być kombinacją wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład, jeśli podana wartość to 6, stan kondycji aplikacji z wartością HealthState równą OK (2) i ostrzeżenie (4) są zwracane.  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Events-kondycja-filtr | Umożliwia filtrowanie kolekcji obiektów HealthEvent zwracanych na podstawie stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia zgodne z filtrem. Wszystkie zdarzenia są używane do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, zwracane są wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżenie (4).  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Exclude-Health-Statistics | Wskazuje, czy statystyki kondycji mają być zwracane jako część wyniku zapytania. Domyślnie wartość false. Statystyki przedstawiają liczbę jednostek podrzędnych w stanie kondycji prawidłowy, ostrzegawczy i błąd. |
| --include-system-Application-Health-Statistics | Wskazuje, czy statystyki kondycji powinny zawierać \: dane statystyczne dotyczące kondycji aplikacji sieci szkieletowej/odzyskiwanie. Domyślnie wartość false. Jeśli IncludeSystemApplicationHealthStatistics ma wartość true, statystyki kondycji obejmują jednostki należące do aplikacji sieci szkieletowej \: /odzyskiwanie. W przeciwnym razie wynik zapytania obejmuje statystyki kondycji tylko dla aplikacji użytkownika. Statystyka kondycji musi być uwzględniona w wyniku zapytania dla tego parametru, który ma zostać zastosowany. |
| --nodes-Health-State-Filter | Umożliwia filtrowanie obiektów stanu kondycji węzła zwracanych w wyniku zapytania o kondycję klastra w oparciu o ich stan kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko węzły, które pasują do filtru. Wszystkie węzły są używane do szacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczaniem opartym na flagach, więc wartość może być kombinacją wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład, jeśli podana wartość to 6, stan kondycji węzłów z wartością HealthState równą OK (2) i ostrzeżenie (4) są zwracane.  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-manifest"></a>Manifest klastra sfctl
Pobierz manifest klastra Service Fabric.

Pobierz manifest klastra Service Fabric. Manifest klastra zawiera właściwości klastra obejmujące różne typy węzłów w klastrze, konfiguracje zabezpieczeń, awarie i topologie domeny uaktualnienia itp. Te właściwości są określone jako część ClusterConfig.JSw pliku podczas wdrażania klastra autonomicznego. Jednak większość informacji w manifeście klastra jest generowana wewnętrznie przez usługę Service Fabric podczas wdrażania klastra w innych scenariuszach wdrażania (np. w przypadku korzystania z Azure Portal). Zawartość manifestu klastra jest przeznaczona wyłącznie do celów informacyjnych, a użytkownicy nie oczekują zależności od formatu zawartości pliku lub jej interpretacji.

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

## <a name="sfctl-cluster-operation-cancel"></a>Operacja klastra sfctl — anulowanie
Anuluje operację niepowodzeniem wywoływaną przez użytkownika.

Następujące interfejsy API uruchamiają operacje błędów, które mogą zostać anulowane przy użyciu CancelOperation \: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Jeśli Force ma wartość false, określona operacja wywołana przez użytkownika zostanie bezpiecznie zatrzymana i wyczyszczona.  Jeśli Force ma wartość true, polecenie zostanie przerwane i niektóre Stany wewnętrzne mogą pozostać poza.  Określenie siły jako prawdziwej powinno być używane z opieką. Wywołanie tego interfejsu API z opcją Force ustawioną na wartość true jest niedozwolone, dopóki ten interfejs API nie został już wywołany w tym samym poleceniu testowym z opcją Force o wartości false, lub jeśli polecenie testowe ma już OperationState OperationState. RollingBack. Wyjaśnienie \: OperationState. RollingBack oznacza, że system będzie/oczyszcza wewnętrzny stan systemu spowodowany przez wykonanie polecenia.  Dane nie zostaną przywrócone, jeśli polecenie testowe powodowało utratę danych.  Na przykład jeśli wywołasz StartDataLoss, wywołaj ten interfejs API, system wyczyści stan wewnętrzny tylko z uruchomienia polecenia. Nie spowoduje to przywrócenia danych partycji docelowej, jeśli polecenie postępuje zbyt długo, aby spowodować utratę danych. Ważna Uwaga \:  Jeśli ten interfejs API jest wywoływany przy użyciu siły = = true, stan wewnętrzny może pozostać poza.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Operation-ID [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Ta wartość jest przenoszona do odpowiedniego interfejsu API getprogress. |
| --Wymuś | Wskazuje, czy należy bezpiecznie wycofać i oczyścić wewnętrzny stan systemu zmodyfikowany przez wykonanie operacji wywołanej przez użytkownika. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-operation-list"></a>sfctl klastra operacji — lista
Pobiera listę wywołanych przez użytkownika operacji odfiltrowanych przez podane dane wejściowe.

Pobiera listę wywołanych przez użytkownika operacji odfiltrowanych przez podane dane wejściowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --State-Filter | Służy do filtrowania OperationState dla operacji wywoływanych przez użytkownika. -65535-Select All-1-SELECT Running-2-SELECT RollingBack-8-SELECT ukończone-16-SELECT Fault-32-SELECT anulowano-64-SELECT ForceCancelled.  Wartość domyślna \: 65535. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |
| --Type-filter | Służy do filtrowania operacji dla operacji wywoływanych przez użytkownika. -65535-Zaznacz wszystko-1-SELECT PartitionDataLoss. -2 — Wybierz PartitionQuorumLoss. -4 — Wybierz PartitionRestart. -8 — wybierz NodeTransition.  Wartość domyślna \: 65535. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-provision"></a>Inicjowanie obsługi klastra sfctl
Inicjowanie obsługi kodu lub pakietów konfiguracyjnych klastra Service Fabric.

Weryfikowanie i Inicjowanie obsługi kodu lub pakietów konfiguracyjnych klastra Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --klaster-manifest-pliku-ścieżka | Ścieżka pliku manifestu klastra. |
| --kod-ścieżka pliku | Ścieżka pliku pakietu kodu klastra. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-recover-system"></a>odzyskiwanie klastra sfctl — system
Wskazuje klaster Service Fabric, który powinien próbować odzyskać usługi systemowe, które są aktualnie zablokowane w utracie kworum.

Wskazuje klaster Service Fabric, który powinien próbować odzyskać usługi systemowe, które są aktualnie zablokowane w utracie kworum. Tę operację należy wykonać tylko wtedy, gdy wiadomo, że repliki nie mogą zostać odzyskane. Nieprawidłowe użycie tego interfejsu API może spowodować utratę danych.

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

## <a name="sfctl-cluster-report-health"></a>Raport klastra sfctl — kondycja
Wysyła raport o kondycji w klastrze Service Fabric.

Wysyła raport o kondycji w klastrze Service Fabric. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raportowany. Raport jest wysyłany do węzła bramy Service Fabric, który przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak stary numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, uruchom GetClusterHealth i sprawdź, czy raport jest wyświetlany w sekcji HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Health-Property [wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem i nie jest stałym wyliczeniem, aby umożliwić elastyczność programu Reporter na kategoryzowanie warunku stanu, który wyzwala raport. Na przykład, Raport z identyfikatorem SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może raportować Właściwość "AvailableDisk" w tym węźle. Ten sam raport może monitorować łączność z węzłami, dzięki czemu może zgłosić Właściwość "łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia dotyczące kondycji dla określonego węzła. Wraz z identyfikatorem SourceId Właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --kondycja — stan [wymagane] | Możliwe wartości to \: "nieprawidłowe", "OK", "ostrzeżenie", "błąd", "nieznany". |
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

## <a name="sfctl-cluster-select"></a>wybór klastra sfctl
Nawiązuje połączenie z punktem końcowym klastra Service Fabric.

W przypadku nawiązywania połączenia z zabezpieczonym klastrem należy określić ścieżkę bezwzględną do certyfikatu (. CRT) i plik klucza (. Key) lub pojedynczy plik z obu (. pem). Nie określaj obu tych wartości. Opcjonalnie w przypadku nawiązywania połączenia z zabezpieczonym klastrem należy również określić ścieżkę bezwzględną do pliku pakietu urzędu certyfikacji lub katalogu certyfikatów zaufanych urzędów certyfikacji.  Nie ma połączenia z klastrem bez wcześniejszego uruchomienia tego polecenia, w tym połączenia z hostem lokalnym. Jednak do nawiązywania połączenia z klastrem lokalnym nie jest wymagany żaden jawny punkt końcowy.  Jeśli używasz certyfikatu z podpisem własnym lub inny certyfikat, który nie jest podpisany przez dobrze znany urząd certyfikacji, Przekaż parametr--CA, aby upewnić się, że Walidacja kończy się powodzeniem. Jeśli nie znajduje się w klastrze produkcyjnym, aby pominąć weryfikację po stronie klienta (przydatną w przypadku podpisu z podpisem własnym lub nieznanego ze znanych urzędów certyfikacji), użyj opcji--No-VERIFY. Gdy jest to możliwe, nie jest to zalecane w przypadku klastrów produkcyjnych. W przeciwnym razie może wystąpić błąd weryfikacji certyfikatu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --AAD | Użyj Azure Active Directory na potrzeby uwierzytelniania. |
| --CA | Ścieżka bezwzględna do katalogu certyfikatów urzędu certyfikacji, który ma być traktowany jako prawidłowy lub plik zbioru urzędów certyfikacji. W przypadku korzystania z katalogu certyfikatów urzędu certyfikacji, `c_rehash <directory>` dostarczonego przez OpenSSL musi zostać najpierw uruchomione, aby obliczyć skróty certyfikatów i utworzyć odpowiednie linki symboliczne. Służy do sprawdzania, czy certyfikat zwrócony przez klaster jest prawidłowy. |
| --CERT | Ścieżka bezwzględna do pliku certyfikatu klienta. |
| --punkt końcowy | Adres URL punktu końcowego klastra, w tym port i prefiks HTTP lub HTTPS. Zazwyczaj punkt końcowy będzie wyglądać podobnie do protokołu HTTPS \: //<adres url>\: 19080. Jeśli nie podano żadnego punktu końcowego, będzie on domyślnie http \: //localhost \: 19080.  Domyślne \: http \: //localhost \: 19080. |
| --klucz | Ścieżka bezwzględna do pliku klucza certyfikatu klienta. |
| --No-verify | Wyłącz weryfikację dla certyfikatów przy użyciu protokołu HTTPS, pamiętaj, \: że jest to opcja niezabezpieczona i nie powinna być używana w środowiskach produkcyjnych. |
| --PEM | Ścieżka bezwzględna do certyfikatu klienta jako plik PEM. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-show-connection"></a>Pokaż klaster sfctl — połączenie
Pokaż klaster Service Fabric, z którym jest połączone to wystąpienie sfctl.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-unprovision"></a>Inicjowanie obsługi administracyjnej klastra sfctl
Cofanie aprowizacji kodu lub pakietów konfiguracyjnych klastra Service Fabric.

Jest on obsługiwany w celu wycofania aprowizacji kodu i konfiguracji osobno.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Code-Version | Wersja pakietu kodu klastra. |
| --config-Version | Wersja manifestu klastra. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade"></a>Uaktualnianie klastra sfctl
Rozpocznij uaktualnianie kodu lub wersji konfiguracji klastra Service Fabric.

Sprawdź poprawność podanych parametrów uaktualnienia i Rozpocznij uaktualnianie kodu lub wersji konfiguracji klastra Service Fabric, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Health-map | Zakodowany słownik JSON par nazw aplikacji i maksymalny procent w złej kondycji przed podnoszeniem błędu. |
| --App-Type-Health-map | Zakodowany słownik JSON par nazw typu aplikacji i maksymalny procent w złej kondycji przed podnoszeniem błędu. |
| --Code-Version | Wersja kodu klastra. |
| --config-Version | Wersja konfiguracji klastra. |
| --Delta-kondycja — Ocena | Włącza przyrostową ocenę kondycji, a nie bezwzględną ocenę kondycji po zakończeniu każdej domeny uaktualnienia. |
| --w złej kondycji — węzły | Maksymalny dozwolony procent pogorszenia kondycji węzłów dozwolony podczas uaktualniania klastra.  Wartość domyślna to \: 10. <br><br> Różnica jest mierzona między stanem węzłów na początku uaktualnienia i stanem węzłów w czasie oceny kondycji. Sprawdzenie jest wykonywane po każdym uaktualnieniu uaktualnienia domeny uaktualnienia, aby upewnić się, że globalny stan klastra mieści się w dopuszczalnym limicie. |
| --Niepowodzenie-akcja | Możliwe wartości to \: "nieprawidłowe", "Wycofaj", "ręczne". |
| --Wymuś ponowne uruchomienie | Procesy są wymuszane ponownie po uaktualnieniu, nawet gdy wersja kodu nie została zmieniona. <br><br> Uaktualnienie zmienia tylko konfigurację lub dane. |
| --Health-Check-retry | Czas między próbami sprawdzenia kondycji, jeśli aplikacja lub klaster nie są w dobrej kondycji. |
| --Kondycja-sprawdzanie stabilne | Ilość czasu, przez jaką aplikacja lub klaster muszą pozostawać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Health-Check-wait | Czas oczekiwania po zakończeniu domeny uaktualnienia przed rozpoczęciem procesu kontroli kondycji. |
| --rereplica-Set-Check-timeout | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. <br><br> Po upływie tego limitu czasu przetwarzanie domeny uaktualnienia będzie przebiegać bez względu na problemy z utratą dostępności. Limit czasu jest resetowany na początku każdej domeny uaktualnienia. Prawidłowe wartości należą do zakresu od 0 do 42949672925 włącznie. |
| --Tryb uaktualnienia stopniowego | Możliwe wartości to \: "nieprawidłowe", "UnmonitoredAuto", "UnmonitoredManual", "monitorowane".  Domyślny \: UnmonitoredAuto. |
| --timeout-t | Wartość domyślna \: 60. |
| --zła kondycja — aplikacje | Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić złej kondycji 10% aplikacji, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na łączną liczbę wystąpień aplikacji w klastrze, z wyłączeniem aplikacji typów aplikacji, które są uwzględnione w ApplicationTypeHealthPolicyMap. Obliczenia są zaokrąglane w górę, aby tolerować jeden błąd w niewielkiej liczbie aplikacji. |
| --zła kondycja — węzły | Maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby umożliwić złej kondycji 10% węzłów, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent węzłów, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji na łączną liczbę węzłów w klastrze. Obliczenia są zaokrąglane w górę, aby tolerować jeden błąd w niewielkiej liczbie węzłów. W dużych klastrach niektóre węzły będą zawsze wyłączane lub wychodzące w przypadku napraw, więc ta wartość procentowa powinna być skonfigurowana w taki sposób, że. |
| --Upgrade-Domain-Delta-zła kondycja — węzły | Maksymalny dozwolony procent pogorszenia kondycji węzłów domeny uaktualnienia dozwolony podczas uaktualniania klastra.  Wartość domyślna \: 15. <br><br> Różnica jest mierzona między stanem węzłów domeny uaktualnienia na początku uaktualnienia i stanem węzłów domeny uaktualnienia w momencie oceny kondycji. Sprawdzanie jest wykonywane po każdym zakończeniu uaktualniania domeny uaktualnienia dla wszystkich ukończonych domen uaktualnienia, aby upewnić się, że stan domeny uaktualnienia mieści się w dopuszczalnym limicie. |
| --Upgrade-Domain-timeout | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Upgrade-timeout | Czas, przez jaki całkowite uaktualnienie musi zakończyć się przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Ostrzeżenie-jako błąd | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade-resume"></a>Uaktualnianie klastra sfctl — wznawianie
Uaktualnienie klastra można przenieść do następnej domeny uaktualnienia.

W razie potrzeby zmień kod klastra lub Uaktualnij konfigurację na następną domenę uaktualnienia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Upgrade-Domain [wymagane] | Następna domena uaktualnienia dla tego uaktualnienia klastra. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-cluster-upgrade-rollback"></a>Uaktualnianie klastra sfctl — wycofywanie
Wycofywanie uaktualnienia klastra Service Fabric.

Wycofaj uaktualnienie kodu lub konfiguracji klastra Service Fabric.

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

## <a name="sfctl-cluster-upgrade-status"></a>Uaktualnianie klastra sfctl — stan
Pobiera postęp bieżącego uaktualniania klastra.

Pobiera bieżący postęp trwającego uaktualniania klastra. Jeśli uaktualnienie nie jest aktualnie w toku, Pobierz ostatni stan poprzedniego uaktualnienia klastra.

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

## <a name="sfctl-cluster-upgrade-update"></a>Uaktualnianie klastra sfctl — aktualizacja
Zaktualizuj parametry uaktualnienia Service Fabric uaktualniania klastra.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Health-map | Zakodowany słownik JSON par nazw aplikacji i maksymalny procent w złej kondycji przed podnoszeniem błędu. |
| --App-Type-Health-map | Zakodowany słownik JSON par nazw typu aplikacji i maksymalny procent w złej kondycji przed podnoszeniem błędu. |
| --Delta-kondycja — Ocena | Włącza przyrostową ocenę kondycji, a nie bezwzględną ocenę kondycji po zakończeniu każdej domeny uaktualnienia. |
| --w złej kondycji — węzły | Maksymalny dozwolony procent pogorszenia kondycji węzłów dozwolony podczas uaktualniania klastra.  Wartość domyślna to \: 10. <br><br> Różnica jest mierzona między stanem węzłów na początku uaktualnienia i stanem węzłów w czasie oceny kondycji. Sprawdzenie jest wykonywane po każdym uaktualnieniu uaktualnienia domeny uaktualnienia, aby upewnić się, że globalny stan klastra mieści się w dopuszczalnym limicie. |
| --Niepowodzenie-akcja | Możliwe wartości to \: "nieprawidłowe", "Wycofaj", "ręczne". |
| --Wymuś ponowne uruchomienie | Procesy są wymuszane ponownie po uaktualnieniu, nawet gdy wersja kodu nie została zmieniona. <br><br> Uaktualnienie zmienia tylko konfigurację lub dane. |
| --Health-Check-retry | Czas między próbami sprawdzenia kondycji, jeśli aplikacja lub klaster nie są w dobrej kondycji. |
| --Kondycja-sprawdzanie stabilne | Ilość czasu, przez jaką aplikacja lub klaster muszą pozostawać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Health-Check-wait | Czas oczekiwania po zakończeniu domeny uaktualnienia przed rozpoczęciem procesu kontroli kondycji. |
| --rereplica-Set-Check-timeout | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. <br><br> Po upływie tego limitu czasu przetwarzanie domeny uaktualnienia będzie przebiegać bez względu na problemy z utratą dostępności. Limit czasu jest resetowany na początku każdej domeny uaktualnienia. Prawidłowe wartości należą do zakresu od 0 do 42949672925 włącznie. |
| --Tryb uaktualnienia stopniowego | Możliwe wartości to \: "nieprawidłowe", "UnmonitoredAuto", "UnmonitoredManual", "monitorowane".  Domyślny \: UnmonitoredAuto. |
| --timeout-t | Wartość domyślna \: 60. |
| --zła kondycja — aplikacje | Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład, aby umożliwić złej kondycji 10% aplikacji, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na łączną liczbę wystąpień aplikacji w klastrze, z wyłączeniem aplikacji typów aplikacji, które są uwzględnione w ApplicationTypeHealthPolicyMap. Obliczenia są zaokrąglane w górę, aby tolerować jeden błąd w niewielkiej liczbie aplikacji. |
| --zła kondycja — węzły | Maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Na przykład aby umożliwić złej kondycji 10% węzłów, ta wartość będzie równa 10. Wartość procentowa reprezentuje maksymalny dopuszczalny procent węzłów, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycja jest szacowana jako ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji na łączną liczbę węzłów w klastrze. Obliczenia są zaokrąglane w górę, aby tolerować jeden błąd w niewielkiej liczbie węzłów. W dużych klastrach niektóre węzły będą zawsze wyłączane lub wychodzące w przypadku napraw, więc ta wartość procentowa powinna być skonfigurowana w taki sposób, że. |
| --Upgrade-Domain-Delta-zła kondycja — węzły | Maksymalny dozwolony procent pogorszenia kondycji węzłów domeny uaktualnienia dozwolony podczas uaktualniania klastra.  Wartość domyślna \: 15. <br><br> Różnica jest mierzona między stanem węzłów domeny uaktualnienia na początku uaktualnienia i stanem węzłów domeny uaktualnienia w momencie oceny kondycji. Sprawdzanie jest wykonywane po każdym zakończeniu uaktualniania domeny uaktualnienia dla wszystkich ukończonych domen uaktualnienia, aby upewnić się, że stan domeny uaktualnienia mieści się w dopuszczalnym limicie. |
| --Upgrade-Domain-timeout | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --rodzaj uaktualnienia | Możliwe wartości to \: "nieprawidłowe", "toczenia", "Rolling_ForceRestart".  Domyślne \: Przetaczanie. |
| --Upgrade-timeout | Czas, przez jaki całkowite uaktualnienie musi zakończyć się przed wykonaniem FailureAction. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Ostrzeżenie-jako błąd | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

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
