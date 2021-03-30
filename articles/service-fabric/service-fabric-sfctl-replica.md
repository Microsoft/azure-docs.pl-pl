---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl Replica
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń służących do zarządzania replikami.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3ed1ecca686471e052f07e945738ad1a49e68464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260305"
---
# <a name="sfctl-replica"></a>sfctl replica
Zarządzanie replikami należącymi do partycji usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| szczebl | Pobiera szczegóły repliki wdrożonej w węźle Service Fabric. |
| wdrożone — lista | Pobiera listę replik wdrożonych w węźle Service Fabric. |
| kondycji | Pobiera kondycję Service Fabricj repliki usługi stanowej lub wystąpienia usługi bezstanowej. |
| informacje o | Pobiera informacje o replice partycji Service Fabric. |
| list | Pobiera informacje o replikach partycji usługi Service Fabric. |
| remove | Usuwa replikę usługi działającą w węźle. |
| Raport — kondycja | Wysyła raport o kondycji w replice Service Fabric. |
| restart | Uruchamia ponownie replikę usługi trwałej uruchomionej w węźle. |

## <a name="sfctl-replica-deployed"></a>wdrożono replikę sfctl
Pobiera szczegóły repliki wdrożonej w węźle Service Fabric.

Pobiera szczegóły repliki wdrożonej w węźle Service Fabric. Informacje obejmują typ usługi, nazwę usługi, bieżącą operację usługi, bieżącą datę początkową bieżącej operacji usługi, identyfikator partycji, identyfikator repliki/wystąpienia, zgłoszone obciążenie i inne informacje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Replica-ID [wymagane] | Identyfikator repliki. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-deployed-list"></a>wdrożona replika sfctl — lista
Pobiera listę replik wdrożonych w węźle Service Fabric.

Pobiera listę zawierającą informacje o replikach wdrożonych w węźle Service Fabric. Informacje obejmują identyfikator partycji, identyfikator repliki, stan repliki, nazwę usługi, nazwę typu usługi i inne informacje. Użyj parametrów zapytania PartitionId lub servicemanifestname, aby zwrócić informacje o wdrożonych replikach pasujących do określonych wartości tych parametrów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Partition-ID | Tożsamość partycji. |
| --Service-manifest-Name | Nazwa manifestu usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-health"></a>Kondycja repliki sfctl
Pobiera kondycję Service Fabricj repliki usługi stanowej lub wystąpienia usługi bezstanowej.

Pobiera kondycję repliki Service Fabric. Użyj EventsHealthStateFilter, aby odfiltrować kolekcję zdarzeń kondycji zgłoszonych w replice na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Replica-ID [wymagane] | Identyfikator repliki. |
| --Events-kondycja-filtr | Umożliwia filtrowanie kolekcji obiektów HealthEvent zwracanych na podstawie stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia zgodne z filtrem. Wszystkie zdarzenia są używane do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, zwracane są wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżenie (4).  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-info"></a>Informacje o replice sfctl
Pobiera informacje o replice partycji Service Fabric.

Odpowiedź obejmuje identyfikator, rolę, stan, kondycję, nazwę węzła, czas przestoju i inne szczegóły dotyczące repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Replica-ID [wymagane] | Identyfikator repliki. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-replica-list"></a>Lista replik sfctl
Pobiera informacje o replikach partycji usługi Service Fabric.

Punkt końcowy getreplicas zwraca informacje o replikach określonej partycji. Odpowiedź obejmuje identyfikator, rolę, stan, kondycję, nazwę węzła, czas przestoju i inne szczegóły dotyczące repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Partition-ID [wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-replica-remove"></a>usuwanie repliki sfctl
Usuwa replikę usługi działającą w węźle.

Ten interfejs API symuluje awarię Service Fabric repliki przez usunięcie repliki z klastra Service Fabric. Usunięcie spowoduje zamknięcie repliki, przejście repliki do roli brak, a następnie usunięcie wszystkich informacji o stanie repliki z klastra. Ten interfejs API testuje ścieżkę usuwania stanu repliki i symuluje trwałą ścieżkę błędów raportów za pomocą interfejsów API klienta. Ostrzeżenie — podczas korzystania z tego interfejsu API nie są wykonywane żadne kontrole bezpieczeństwa. Nieprawidłowe użycie tego interfejsu API może prowadzić do utraty danych dla usług stanowych. Dodatkowo flaga forceRemove ma wpływ na wszystkie inne repliki hostowane w tym samym procesie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Replica-ID [wymagane] | Identyfikator repliki. |
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

## <a name="sfctl-replica-report-health"></a>Raport repliki sfctl — kondycja
Wysyła raport o kondycji w replice Service Fabric.

Zgłasza stan kondycji określonej repliki Service Fabric. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raportowany. Raport jest wysyłany do repliki bramy Service Fabric, która przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak stary numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, uruchom polecenie Pobierz kondycję repliki i sprawdź, czy raport jest wyświetlany w sekcji HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Health-Property [wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem i nie jest stałym wyliczeniem, aby umożliwić elastyczność programu Reporter na kategoryzowanie warunku stanu, który wyzwala raport. Na przykład, Raport z identyfikatorem SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może raportować Właściwość "AvailableDisk" w tym węźle. Ten sam raport może monitorować łączność z węzłami, dzięki czemu może zgłosić Właściwość "łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia dotyczące kondycji dla określonego węzła. Wraz z identyfikatorem SourceId Właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --kondycja — stan [wymagane] | Możliwe wartości to \: "nieprawidłowe", "OK", "ostrzeżenie", "błąd", "nieznany". |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Replica-ID [wymagane] | Tożsamość partycji. |
| --source-ID [wymagane] | Nazwa źródłowa, która identyfikuje składnik klienta/alarm/składnika systemu, który wygenerował informacje o kondycji. |
| --Description | Opis informacji o kondycji. <br><br> Reprezentuje on bezpłatny tekst używany do dodawania informacji ludzkich o raporcie. Maksymalna długość ciągu dla opisu to 4096 znaków. Jeśli podany ciąg jest dłuższy, zostanie automatycznie obcięty. Po obcięciu ostatnie znaki opisu zawierają znacznik "[obcięty]", a całkowity rozmiar ciągu to 4096 znaków. Obecność znacznika wskazuje użytkownikom, że wystąpiło obcinanie. Należy pamiętać, że w przypadku obcinania opis ma mniej niż 4096 znaków od oryginalnego ciągu. |
| --natychmiastowe | Flaga wskazująca, czy raport ma być wysyłany od razu. <br><br> Raport o kondycji jest wysyłany do aplikacji bramy Service Fabric, która przekazuje do magazynu kondycji. Jeśli jest ustawiona wartość true, raport jest wysyłany natychmiast z bramy HTTP do magazynu kondycji, niezależnie od ustawień klienta sieci szkieletowej używanej przez aplikację bramy HTTP. Jest to przydatne w przypadku raportów o kluczowym znaczeniu, które powinny być wysyłane najszybciej, jak to możliwe. W zależności od chronometrażu i innych warunków wysyłanie raportu może nadal zakończyć się niepowodzeniem, na przykład jeśli Brama HTTP jest ZAMKNIĘTA lub komunikat nie dociera do bramy. Jeśli wartość jest ustawiona na wartość FAŁSZ, raport jest wysyłany na podstawie ustawień klienta kondycji z bramy HTTP. W związku z tym będzie on przetwarzany wsadowo zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie, ponieważ pozwala klientowi kondycji zoptymalizować komunikaty raportowania kondycji do magazynu kondycji, a także przetwarzania raportów kondycji. Domyślnie raporty nie są wysyłane od razu. |
| --Usuń po wygaśnięciu | Wartość wskazująca, czy raport został usunięty z magazynu kondycji po jego wygaśnięciu. <br><br> W przypadku ustawienia wartości true raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli zostanie ustawiona na wartość false, raport jest traktowany jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie fałszywa. Gdy klienci raportują okresowo, powinni ustawić RemoveWhenExpired false (domyślnie). W ten sposób, to program reporter ma problemy (np. zakleszczenie) i nie może zgłosić, gdy Raport kondycji wygaśnie. Oznacza to, że jednostka jest w stanie błędu kondycji. |
| --Sequence-Number | Numer sekwencyjny dla tego raportu kondycji jako ciąg liczbowy. <br><br> Numer sekwencyjny raportu jest używany przez magazyn kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer sekwencyjny jest generowany automatycznie przez klienta kondycji, gdy raport zostanie dodany. |
| --rodzaj usługi | Rodzaj repliki usług (bezstanowe lub stanowe), dla których kondycja jest raportowana. Możliwe wartości to " \: bezstanowe", "stanowe".  Domyślny \: stanowy. |
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

## <a name="sfctl-replica-restart"></a>ponowne uruchomienie repliki sfctl
Uruchamia ponownie replikę usługi trwałej uruchomionej w węźle.

Uruchamia ponownie replikę usługi trwałej uruchomionej w węźle. Ostrzeżenie — podczas korzystania z tego interfejsu API nie są wykonywane żadne kontrole bezpieczeństwa. Nieprawidłowe użycie tego interfejsu API może prowadzić do utraty dostępności dla usług stanowych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Partition-ID [wymagane] | Tożsamość partycji. |
| --Replica-ID [wymagane] | Identyfikator repliki. |
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
