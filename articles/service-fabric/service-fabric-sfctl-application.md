---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — aplikacja sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń związanych z zarządzaniem aplikacjami.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 880770345eb7d65850db322bd97d64c60b6681ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260874"
---
# <a name="sfctl-application"></a>sfctl application
Twórz i usuwaj aplikacje i typy aplikacji oraz zarządzaj nimi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy aplikację Service Fabric przy użyciu podanego opisu. |
| delete | Usuwa istniejącą aplikację Service Fabric. |
| szczebl | Pobiera informacje o aplikacji wdrożonej w węźle Service Fabric. |
| wdrożone — kondycja | Pobiera informacje o kondycji aplikacji wdrożonej w węźle Service Fabric. |
| wdrożone — lista | Pobiera listę aplikacji wdrożonych w węźle Service Fabric. |
| kondycji | Pobiera kondycję aplikacji usługi Service Fabric. |
| informacje o | Pobiera informacje o aplikacji Service Fabric. |
| list | Pobiera listę aplikacji utworzonych w klastrze Service Fabric, które pasują do określonych filtrów. |
| ładowanie | Pobiera informacje ładowania dotyczące Service Fabric aplikacji. |
| manifest | Pobiera manifest opisujący typ aplikacji. |
| przepis | Inicjuje lub rejestruje typ aplikacji Service Fabric z klastrem przy użyciu pakietu ". sfpkg" w magazynie zewnętrznym lub przy użyciu pakietu aplikacji w magazynie obrazów. |
| Raport — kondycja | Wysyła raport o kondycji na Service Fabric aplikacji. |
| typ | Pobiera listę typów aplikacji w klastrze Service Fabric pasujące dokładnie do określonej nazwy. |
| Lista typów | Pobiera listę typów aplikacji w klastrze Service Fabric. |
| Wstrzymaj obsługę administracyjną | Usuwa lub wyrejestrowuje typ aplikacji Service Fabric z klastra. |
| uaktualnienie | Uruchamia Uaktualnianie aplikacji w klastrze Service Fabric. |
| uaktualnienie — wznawianie | Wznawia Uaktualnianie aplikacji w klastrze Service Fabric. |
| uaktualnienie — wycofanie | Uruchamia wycofywanie bieżącego uaktualnienia aplikacji w klastrze Service Fabric. |
| uaktualnienie — stan | Pobiera szczegóły dotyczące najnowszej aktualizacji wykonywanej w tej aplikacji. |
| przekazywanie | Skopiuj pakiet aplikacji Service Fabric do magazynu obrazów. |

## <a name="sfctl-application-create"></a>Tworzenie aplikacji sfctl
Tworzy aplikację Service Fabric przy użyciu podanego opisu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Name [wymagane] | Nazwa aplikacji, w tym schemat identyfikatora URI "Sieć szkieletowa \: ". |
| --Typ aplikacji [wymagane] | Nazwa typu aplikacji znaleziona w manifeście aplikacji. |
| --App-Version [wymagane] | Wersja typu aplikacji zgodnie z definicją w manifeście aplikacji. |
| --Max-Node-Count | Maksymalna liczba węzłów, w których Service Fabric będzie zarezerwować pojemności dla tej aplikacji. Należy zauważyć, że nie oznacza to, że usługi tej aplikacji będą umieszczane na wszystkich tych węzłach. |
| --metryki | Zakodowana w formacie JSON Lista opisów metryk pojemności aplikacji. Metryka jest definiowana jako nazwa skojarzona z zestawem pojemności dla każdego węzła, w którym znajduje się aplikacja. |
| --min-liczba węzłów | Minimalna liczba węzłów, w których Service Fabric będzie zarezerwować pojemności dla tej aplikacji. Należy zauważyć, że nie oznacza to, że usługi tej aplikacji będą umieszczane na wszystkich tych węzłach. |
| --parametry | Zakodowana w formacie JSON lista przesłonięć parametrów aplikacji do zastosowania podczas tworzenia aplikacji. |
| --timeout-t | Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-delete"></a>Usuwanie aplikacji sfctl
Usuwa istniejącą aplikację Service Fabric.

Aby można było usunąć aplikację, należy ją utworzyć. Usunięcie aplikacji spowoduje usunięcie wszystkich usług, które są częścią tej aplikacji. Domyślnie Service Fabric spróbuje zamknąć repliki usługi w sposób płynny, a następnie usunąć usługę. Jeśli jednak usługa ma problemy z zamykaniem repliki, operacja usuwania może zająć dużo czasu lub zostać zatrzymana. Użyj opcjonalnej flagi ForceRemove, aby pominąć przebieg sekwencji i wymusić usunięcie aplikacji i wszystkich jej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Wymuś usunięcie | Usuń wymuszanie Service Fabric aplikacji lub usługi bez przechodzenia przez łagodną sekwencję zamykania. Tego parametru można użyć, aby wymusić usunięcie aplikacji lub usługi, dla której limit czasu usuwania jest spowodowany przez problemy w kodzie usługi, które uniemożliwiają płynne zamknięcie replik. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-deployed"></a>wdrożono aplikację sfctl
Pobiera informacje o aplikacji wdrożonej w węźle Service Fabric.

To zapytanie zwraca informacje o aplikacji systemowej, jeśli podany identyfikator aplikacji jest przeznaczony dla aplikacji systemowej. Wyniki obejmują wdrożone aplikacje w Stanach aktywne, aktywowanie i pobieranie. To zapytanie wymaga, aby nazwa węzła odpowiadała węzłowi w klastrze. Zapytanie kończy się niepowodzeniem, jeśli podana nazwa węzła nie wskazuje żadnych aktywnych węzłów Service Fabric w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --include-Health-State | Uwzględnij stan kondycji jednostki. Jeśli ten parametr ma wartość false lub nie został określony, zwracany jest stan kondycji "nieznany". Po ustawieniu na wartość true zapytanie przechodzi równolegle do węzła i usługi systemowej kondycji przed scaleniem wyników. W efekcie zapytanie jest droższe i może trwać dłużej. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-deployed-health"></a>wdrożone aplikacje sfctl — kondycja
Pobiera informacje o kondycji aplikacji wdrożonej w węźle Service Fabric.

Pobiera informacje o kondycji aplikacji wdrożonej w węźle Service Fabric. Użyj EventsHealthStateFilter, aby opcjonalnie odfiltrować kolekcję obiektów HealthEvent raportowanych w wdrożonej aplikacji na podstawie stanu kondycji. Użyj DeployedServicePackagesHealthStateFilter, aby opcjonalnie odfiltrować elementy podrzędne DeployedServicePackageHealth na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --wdrożone-Service-Packages-Health-State-Filter | Umożliwia filtrowanie wdrożonych obiektów stanu kondycji pakietów usługi w wyniku wdrożenia zapytania o kondycję aplikacji na podstawie ich stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko wdrożone pakiety usług zgodne z filtrem. Wszystkie wdrożone pakiety usługi są używane do szacowania zagregowanego stanu kondycji wdrożonej aplikacji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład, jeśli podana wartość to 6, stan kondycji pakietów usługi z wartością HealthState równą OK (2) i ostrzeżenie (4) są zwracane.  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Events-kondycja-filtr | Umożliwia filtrowanie kolekcji obiektów HealthEvent zwracanych na podstawie stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia zgodne z filtrem. Wszystkie zdarzenia są używane do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, zwracane są wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżenie (4).  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Exclude-Health-Statistics | Wskazuje, czy statystyki kondycji mają być zwracane jako część wyniku zapytania. Domyślnie wartość false. Statystyki przedstawiają liczbę jednostek podrzędnych w stanie kondycji prawidłowy, ostrzegawczy i błąd. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-deployed-list"></a>wdrożono aplikację sfctl — lista
Pobiera listę aplikacji wdrożonych w węźle Service Fabric.

Pobiera listę aplikacji wdrożonych w węźle Service Fabric. Wyniki nie zawierają informacji o wdrożonych aplikacjach systemowych, chyba że jawne zapytanie nie jest wykonywane przez identyfikator. Wyniki obejmują wdrożone aplikacje w Stanach aktywne, aktywowanie i pobieranie. To zapytanie wymaga, aby nazwa węzła odpowiadała węzłowi w klastrze. Zapytanie kończy się niepowodzeniem, jeśli podana nazwa węzła nie wskazuje żadnych aktywnych węzłów Service Fabric w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --include-Health-State | Uwzględnij stan kondycji jednostki. Jeśli ten parametr ma wartość false lub nie został określony, zwracany jest stan kondycji "nieznany". Po ustawieniu na wartość true zapytanie przechodzi równolegle do węzła i usługi systemowej kondycji przed scaleniem wyników. W efekcie zapytanie jest droższe i może trwać dłużej. |
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

## <a name="sfctl-application-health"></a>Kondycja aplikacji sfctl
Pobiera kondycję aplikacji usługi Service Fabric.

Zwraca stan kondycji aplikacji usługi Service Fabric. Odpowiedź raportuje, czy stan kondycji jest prawidłowy, błąd lub ostrzeżenie. Jeśli jednostka nie zostanie znaleziona w magazynie kondycji, zostanie zwrócony błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --wdrożone-Applications-State-Filter | Umożliwia filtrowanie wdrożonych obiektów stanu kondycji aplikacji zwracanych w wyniku zapytania o kondycję aplikacji na podstawie ich stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zostaną zwrócone tylko wdrożone aplikacje zgodne z filtrem. Wszystkie wdrożone aplikacje są używane do szacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, stan kondycji wdrożonych aplikacji z wartością HealthState równą OK (2) i ostrzeżeniem (4) są zwracane.  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Events-kondycja-filtr | Umożliwia filtrowanie kolekcji obiektów HealthEvent zwracanych na podstawie stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia zgodne z filtrem. Wszystkie zdarzenia są używane do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, zwracane są wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżenie (4).  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Exclude-Health-Statistics | Wskazuje, czy statystyki kondycji mają być zwracane jako część wyniku zapytania. Domyślnie wartość false. Statystyki przedstawiają liczbę jednostek podrzędnych w stanie kondycji prawidłowy, ostrzegawczy i błąd. |
| --Services-Health-State-Filter | Umożliwia filtrowanie obiektów stanu kondycji usług zwracanych w wyniku zapytania o kondycję usług w zależności od jego stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko usługi zgodne z filtrem. Wszystkie usługi są używane do szacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, stan kondycji usług z wartością HealthState równą OK (2) i ostrzeżenie (4) zostaną zwrócone.  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-info"></a>Informacje o aplikacji sfctl
Pobiera informacje o aplikacji Service Fabric.

Zwraca informacje o aplikacji, która została utworzona lub w procesie tworzenia w klastrze Service Fabric, której nazwa jest zgodna z nazwą określoną jako parametr. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegóły dotyczące aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Exclude-Application-Parameters | Flaga określająca, czy parametry aplikacji będą wykluczone z wyniku. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-list"></a>Lista aplikacji sfctl
Pobiera listę aplikacji utworzonych w klastrze Service Fabric, które pasują do określonych filtrów.

Pobiera informacje o utworzonych przez siebie aplikacjach lub w procesie tworzenia w klastrze Service Fabric i dopasowanie do określonych filtrów. Odpowiedź zawiera nazwę, typ, stan, parametry i inne szczegóły dotyczące aplikacji. Jeśli aplikacje nie mieszczą się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, którego można użyć do uzyskania następnej strony. Nie można jednocześnie określić filtrów ApplicationTypeName i ApplicationDefinitionKindFilter.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Definicja typu aplikacji — filtr | Służy do filtrowania w ApplicationDefinitionKind, który jest mechanizmem używanym do definiowania aplikacji Service Fabric.  <br> -Default — wartość domyślna, która wykonuje tę samą funkcję, co wybranie opcji "wszystkie". Wartość jest równa 0.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością ApplicationDefinitionKind. Wartość to 65535.  <br> -ServiceFabricApplicationDescription-Filter pasujący do danych wejściowych o wartości ApplicationDefinitionKind ServiceFabricApplicationDescription. Wartość jest równa 1.  <br> -Zredaguj-Filter, który dopasowuje dane wejściowe za pomocą ApplicationDefinitionKind wartości. Wartość jest równa 2. |
| --Typ aplikacji | Nazwa typu aplikacji używana do filtrowania aplikacji do zapytania. Ta wartość nie powinna zawierać wersji typu aplikacji. |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --Exclude-Application-Parameters | Flaga określająca, czy parametry aplikacji będą wykluczone z wyniku. |
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

## <a name="sfctl-application-load"></a>ładowanie aplikacji sfctl
Pobiera informacje ładowania dotyczące Service Fabric aplikacji.

Zwraca informacje ładowania dotyczące aplikacji, która została utworzona, lub w procesie tworzenia w klastrze Service Fabric, którego nazwa jest zgodna z nazwą określoną jako parametr. Odpowiedź obejmuje nazwę, węzły minimalne, węzły maksymalne, liczbę węzłów, które aktualnie zajmują aplikację, oraz informacje o metrykach obciążenia aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-manifest"></a>manifest aplikacji sfctl
Pobiera manifest opisujący typ aplikacji.

Odpowiedź zawiera kod XML manifestu aplikacji jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Typ aplikacji-nazwa [wymagane] | Nazwa typu aplikacji. |
| --Typ aplikacji — wersja [wymagana] | Wersja typu aplikacji. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-provision"></a>Inicjowanie obsługi aplikacji sfctl
Inicjuje lub rejestruje typ aplikacji Service Fabric z klastrem przy użyciu pakietu ". sfpkg" w magazynie zewnętrznym lub przy użyciu pakietu aplikacji w magazynie obrazów.

Inicjuje obsługę Service Fabric typu aplikacji z klastrem. Aby można było utworzyć wystąpienia nowych aplikacji, wymagane jest wykonanie tej czynności. Operację udostępniania można wykonać w pakiecie aplikacji określonym przez relativePathInImageStore lub za pomocą identyfikatora URI zewnętrznego ". sfpkg". O ile nie określono opcji--External-zastrzegania, to polecenie będzie oczekiwać na zainicjowanie obsługi magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Application-Package-Download-URI | Ścieżka do pakietu aplikacji ". sfpkg", z poziomu którego można pobrać pakiet aplikacji przy użyciu protokołów HTTP lub HTTPS. <br><br> Dotyczy tylko magazynu zewnętrznego. Pakiet aplikacji może być przechowywany w zewnętrznym magazynie, który udostępnia operację pobierania, aby pobrać plik. Obsługiwane protokoły to HTTP i HTTPS, a ścieżka musi zezwalać na dostęp do odczytu. |
| --Typ aplikacji-kompilacja-ścieżka | Tylko do przechowywania obrazów rodzajowych. Ścieżka względna pakietu aplikacji w magazynie obrazów określona podczas poprzedniej operacji przekazywania. |
| --Typ aplikacji | Dotyczy tylko magazynu zewnętrznego. Nazwa typu aplikacji reprezentuje nazwę typu aplikacji znalezionego w manifeście aplikacji. |
| --Typ aplikacji — wersja | Dotyczy tylko magazynu zewnętrznego. Wersja typu aplikacji reprezentuje wersję typu aplikacji znalezionego w manifeście aplikacji. |
| --External-zastrzeganie | Lokalizacja, w której można zarejestrować lub zainicjować pakiet aplikacji. Wskazuje, że jest to pakiet aplikacji, który został wcześniej przekazany do magazynu zewnętrznego. Pakiet aplikacji zostaje zakończony z rozszerzeniem *. sfpkg. |
| --No-wait | Wskazuje, czy inicjowanie obsługi powinno odbywać się asynchronicznie. <br><br> Po ustawieniu na wartość true operacja inicjowania obsługi zwraca wartość, gdy żądanie jest akceptowane przez system, a operacja inicjowania obsługi jest kontynuowana bez limitu czasu. Wartość domyślna to false. W przypadku dużych pakietów aplikacji zaleca się ustawienie wartości na true. |
| --timeout-t | Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-report-health"></a>Raport aplikacji sfctl — kondycja
Wysyła raport o kondycji na Service Fabric aplikacji.

Zgłasza stan kondycji określonego Service Fabric aplikacji. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raportowany. Raport jest wysyłany do aplikacji bramy Service Fabric, która przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak stary numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, Pobierz kondycję aplikacji i sprawdź, czy raport jest wyświetlany.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
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

## <a name="sfctl-application-type"></a>Typ aplikacji sfctl
Pobiera listę typów aplikacji w klastrze Service Fabric pasujące dokładnie do określonej nazwy.

Zwraca informacje o typach aplikacji, które są obsługiwane lub w procesie aprowizacji w klastrze Service Fabric. Te wyniki są typami aplikacji, których nazwa pasuje dokładnie do określonej w parametrze, i która jest zgodna z podanymi parametrami zapytania. Zwracane są wszystkie wersje typu aplikacji pasujące do nazwy typu aplikacji, z każdą wersją zwracaną jako jeden typ aplikacji. Odpowiedź zawiera nazwę, wersję, stan i inne szczegóły dotyczące typu aplikacji. Jest to zapytanie stronicowane, co oznacza, że jeśli nie wszystkie typy aplikacji mieszczą się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony. Na przykład jeśli jest 10 typów aplikacji, ale strona dopasowuje tylko pierwsze trzy typy aplikacji lub jeśli wartość maksymalna liczba wyników wynosi 3, zwracane są trzy. Aby uzyskać dostęp do reszty wyników, Pobierz kolejne strony przy użyciu zwróconego tokenu kontynuacji w następnym zapytaniu. Pusty token kontynuacji jest zwracany, jeśli nie ma żadnych kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Typ aplikacji-nazwa [wymagane] | Nazwa typu aplikacji. |
| --Typ aplikacji — wersja | Wersja typu aplikacji. |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --Exclude-Application-Parameters | Flaga określająca, czy parametry aplikacji będą wykluczone z wyniku. |
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

## <a name="sfctl-application-type-list"></a>Typ aplikacji sfctl — lista
Pobiera listę typów aplikacji w klastrze Service Fabric.

Zwraca informacje o typach aplikacji, które są obsługiwane lub w procesie aprowizacji w klastrze Service Fabric. Każda wersja typu aplikacji jest zwracana jako jeden typ aplikacji. Odpowiedź zawiera nazwę, wersję, stan i inne szczegóły dotyczące typu aplikacji. Jest to zapytanie stronicowane, co oznacza, że jeśli nie wszystkie typy aplikacji mieszczą się na stronie, zwracana jest jedna strona wyników, a także token kontynuacji, który może służyć do uzyskania następnej strony. Na przykład jeśli jest 10 typów aplikacji, ale strona dopasowuje tylko pierwsze trzy typy aplikacji lub jeśli wartość maksymalna liczba wyników wynosi 3, zwracane są trzy. Aby uzyskać dostęp do reszty wyników, Pobierz kolejne strony przy użyciu zwróconego tokenu kontynuacji w następnym zapytaniu. Pusty token kontynuacji jest zwracany, jeśli nie ma żadnych kolejnych stron.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Typ aplikacji-definicji — filtr | Służy do filtrowania ApplicationTypeDefinitionKind, który jest mechanizmem używanym do definiowania typu aplikacji Service Fabric.  <br> -Default — wartość domyślna, która wykonuje tę samą funkcję, co wybranie opcji "wszystkie". Wartość jest równa 0.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością ApplicationTypeDefinitionKind. Wartość to 65535.  <br> -ServiceFabricApplicationPackage-Filter pasujący do danych wejściowych o wartości ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. Wartość jest równa 1.  <br> -Zredaguj-Filter, który dopasowuje dane wejściowe za pomocą ApplicationTypeDefinitionKind wartości. Wartość jest równa 2. |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --Exclude-Application-Parameters | Flaga określająca, czy parametry aplikacji będą wykluczone z wyniku. |
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

## <a name="sfctl-application-unprovision"></a>Inicjowanie obsługi administracyjnej aplikacji sfctl
Usuwa lub wyrejestrowuje typ aplikacji Service Fabric z klastra.

Tę operację można wykonać tylko wtedy, gdy wszystkie wystąpienia aplikacji typu aplikacji zostały usunięte. Po wyrejestrowaniu typu aplikacji nie można utworzyć nowych wystąpień aplikacji dla tego typu aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Typ aplikacji-nazwa [wymagane] | Nazwa typu aplikacji. |
| --Typ aplikacji — wersja [wymagana] | Wersja typu aplikacji zgodnie z definicją w manifeście aplikacji. |
| --Async-parametr | Flaga oznaczająca, czy cofnięcie aprowizacji powinno odbywać się asynchronicznie. Po ustawieniu na wartość true operacja anulowania aprowizacji zwraca wartość, gdy żądanie zostanie zaakceptowane przez system, a operacja anulowania aprowizacji będzie kontynuowana bez limitu czasu. Wartość domyślna to false. Zaleca się jednak ustawienie na wartość true w przypadku dużych pakietów aplikacji, które zostały zainicjowane. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade"></a>Uaktualnianie aplikacji sfctl
Uruchamia Uaktualnianie aplikacji w klastrze Service Fabric.

Sprawdza poprawność podanych parametrów uaktualnienia aplikacji i uruchamia Uaktualnianie aplikacji, jeśli parametry są prawidłowe. Należy zauważyć, że opis uaktualnienia zastępuje istniejący opis aplikacji. Oznacza to, że jeśli parametry nie zostaną określone, istniejące parametry aplikacji zostaną zastąpione pustą listą parametrów. Spowoduje to, że aplikacja korzysta z wartości domyślnej parametrów z manifestu aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --wersja aplikacji [wymagana] | Docelowa wersja typu aplikacji (znaleziona w manifeście aplikacji) dla uaktualnienia aplikacji. |
| --parametry [wymagane] | Zakodowana w formacie JSON lista przesłonięć parametrów aplikacji do zastosowania podczas uaktualniania aplikacji. |
| --domyślne-Service-Health-Policy | Zakodowana w formacie JSON Specyfikacja zasad kondycji używana domyślnie do oszacowania kondycji typu usługi. |
| --Niepowodzenie-akcja | Akcja, która ma zostać wykonana po napotkaniu przez monitorowane uaktualnienie zasad monitorowania lub naruszeń zasad dotyczących kondycji. |
| --Wymuś ponowne uruchomienie | Wymuszone ponowne uruchamianie procesów podczas uaktualniania, nawet gdy wersja kodu nie została zmieniona. |
| --Health-Check-retry-timeout | Czas między próbami sprawdzenia kondycji, jeśli aplikacja lub klaster nie są w dobrej kondycji.  Domyślny \: PT0H10M0S. |
| --Kondycja-sprawdzanie stabilne — czas trwania | Ilość czasu, przez jaką aplikacja lub klaster muszą pozostawać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia.  Domyślny \: PT0H2M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Health-Check-wait-Duration | Czas oczekiwania po zakończeniu domeny uaktualnienia przed rozpoczęciem procesu kontroli kondycji.  Wartość domyślna to \: 0. |
| — maks.-zła kondycja — aplikacje | Maksymalny dozwolony procent aplikacji w złej kondycji. Reprezentowane jako liczba z zakresu od 0 do 100. |
| --Tryb | Tryb służący do monitorowania kondycji podczas stopniowego uaktualniania.  Domyślny \: UnmonitoredAuto. |
| --rereplica-Set-Check-timeout | Maksymalny czas blokowania przetwarzania domeny uaktualnienia i zapobiegania utracie dostępności w przypadku wystąpienia nieoczekiwanych problemów. Mierzony w sekundach. |
| --Service-Health-Policy | Zakodowana w notacji JSON zasada kondycji typu usługi dla nazwy typu usługi. Mapa jest pusta. |
| --timeout-t | Wartość domyślna \: 60. |
| --Upgrade-Domain-timeout | Czas, przez jaki każda domena uaktualnienia musi zakończyć pracę przed wykonaniem FailureAction.  Domyślne \: p10675199dt02h48m 05.4775807 s. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Upgrade-timeout | Czas, przez jaki całkowite uaktualnienie musi zakończyć się przed wykonaniem FailureAction.  Domyślne \: p10675199dt02h48m 05.4775807 s. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, jest interpretowana jako liczba reprezentująca łączną liczbę milisekund. |
| --Ostrzeżenie-jako błąd | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade-resume"></a>Uaktualnienie aplikacji sfctl — wznawianie
Wznawia Uaktualnianie aplikacji w klastrze Service Fabric.

Wznawia niemonitorowane ręczne uaktualnienie aplikacji Service Fabric. Service Fabric uaktualnia jedną domenę uaktualnienia w danym momencie. W przypadku niemonitorowanych uaktualnień ręcznych po Service Fabric zakończeniu domeny uaktualnienia oczekuje na wywołanie tego interfejsu API przed przejściem do następnej domeny uaktualnienia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Upgrade-Domain-Name [wymagane] | Nazwa domeny uaktualnienia, w której ma zostać wznowione uaktualnienie. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade-rollback"></a>Uaktualnienie aplikacji sfctl — wycofywanie
Uruchamia wycofywanie bieżącego uaktualnienia aplikacji w klastrze Service Fabric.

Zaczyna wycofywanie uaktualnienia bieżącej aplikacji do poprzedniej wersji. Tego interfejsu API można użyć tylko w celu wycofania bieżącego uaktualnienia w toku, które jest przenoszone do nowej wersji. Jeśli aplikacja nie jest obecnie uaktualniana, użyj interfejsu API StartApplicationUpgrade, aby uaktualnić ją do żądanej wersji, włącznie z przywróceniem poprzedniej wersji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upgrade-status"></a>Uaktualnienie aplikacji sfctl — stan
Pobiera szczegóły dotyczące najnowszej aktualizacji wykonywanej w tej aplikacji.

Zwraca informacje o stanie najnowszej aktualizacji aplikacji wraz ze szczegółami dotyczącymi pomocy w debugowaniu problemów z kondycją aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-application-upload"></a>przekazywanie aplikacji sfctl
Skopiuj pakiet aplikacji Service Fabric do magazynu obrazów.

Opcjonalnie Wyświetl postęp przekazywania dla każdego pliku w pakiecie. Postęp przekazywania jest wysyłany do `stderr` .

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ścieżka [wymagane] | Ścieżka do pakietu aplikacji lokalnej. |
| --Kompresuj | Dotyczy tylko Service Fabric pakietów aplikacji. Utwórz nowy folder zawierający skompresowany pakiet aplikacji do lokalizacji domyślnej lub do lokalizacji określonej przez parametr skompresowanej lokalizacji, a następnie Przekaż nowo utworzony folder. <br><br> Jeśli jest już skompresowany plik wygenerowany przez sfctl, zostanie on zastąpiony, jeśli ta flaga jest ustawiona. Jeśli katalog nie jest pakietem aplikacji, zostanie zwrócony błąd. Jeśli jest już skompresowanym pakietem aplikacji, folder zostanie skopiowany w postaci, w jakiej jest. Nowo utworzony skompresowany pakiet aplikacji zostanie domyślnie usunięty po pomyślnym przekazaniu. Jeśli przekazywanie nie powiedzie się, należy ręcznie oczyścić skompresowany pakiet w razie konieczności. Usunięcie nie powoduje usunięcia pustej katalogów, która mogła zostać utworzona, jeśli parametr lokalizacji skompresowanej odwołuje się do nieistniejących katalogów. |
| --skompresowana lokalizacja | Lokalizacja, w której ma zostać umieszczony skompresowany pakiet aplikacji. <br><br> Jeśli nie podano lokalizacji, skompresowany pakiet zostanie umieszczony w nowo utworzonym folderze o nazwie sfctl_compressed_temp w katalogu nadrzędnym określonym w argumencie Path. Na przykład jeśli argument Path ma wartość C \: /foldera/AppPkg, skompresowany pakiet zostanie dodany do c \: /foldera/sfctl_compressed_temp/apppkg. |
| --magazynu ImageStore-String | Docelowy magazyn obrazów, do którego ma zostać przekazany pakiet aplikacji.  Domyślna \: magazynu ImageStore sieci szkieletowej \: . <br><br> Aby przekazać do lokalizacji pliku, Uruchom ten parametr z plikiem \: . W przeciwnym razie wartość powinna być ciągiem połączenia magazynu obrazu, na przykład wartością domyślną. |
| --Zachowaj skompresowane | Określa, czy ma być zachowywany wygenerowany skompresowany pakiet po pomyślnym ukończeniu przekazywania. <br><br> Jeśli nie zostanie ustawiona, po pomyślnym zakończeniu zostaną usunięte skompresowane pakiety aplikacji. Jeśli przekazywanie nie powiodło się, pakiet aplikacji będzie zawsze przechowywany w katalogu wyjściowym do ponownego przekazania. |
| --Pokaż-postęp | Pokaż postęp przekazywania plików dla dużych pakietów. |
| --timeout-t | Łączny limit czasu w sekundach. Przekazywanie zakończy się niepowodzeniem i zwróci błąd po upływie okresu przekazywania. Ten limit czasu ma zastosowanie do całego pakietu aplikacji, a limity czasu poszczególnych plików będą równe pozostałemu czasowi limitu czasu. Limit czasu nie obejmuje czasu wymaganego do skompresowania pakietu aplikacji.  Wartość domyślna \: 300. |

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
