---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — usługa sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń związanych z zarządzaniem usługami, typami usług i pakietami usług.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 426220f38e6ddfaaf8b24bf4f7d34473d881ae56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245606"
---
# <a name="sfctl-service"></a>sfctl service
Tworzenie, usuwanie usług, typów usług i pakietów usług oraz zarządzanie nimi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Nazwa aplikacji | Pobiera nazwę aplikacji Service Fabric dla usługi. |
| kod pakietu — lista | Pobiera listę pakietów kodu wdrożonych w węźle Service Fabric. |
| create | Tworzy określoną usługę Service Fabric. |
| delete | Usuwa istniejącą usługę Service Fabric. |
| wdrożono-Type | Pobiera informacje o określonym typie usługi aplikacji wdrożonym w węźle klastra Service Fabric. |
| wdrożone — lista typów | Pobiera listę zawierającą informacje o typach usług z aplikacji wdrożonych w węźle w klastrze Service Fabric. |
| description | Pobiera opis istniejącej usługi Service Fabric. |
| Pobierz kontener — dzienniki | Pobiera dzienniki kontenerów dla kontenera wdrożonego w węźle Service Fabric. |
| kondycji | Pobiera kondycję określonej usługi Service Fabric. |
| informacje o | Pobiera informacje o konkretnej usłudze należącej do aplikacji Service Fabric. |
| list | Pobiera informacje o wszystkich usługach należących do aplikacji określonej przez identyfikator aplikacji. |
| manifest | Pobiera manifest opisujący typ usługi. |
| Pakiet — wdrażanie | Pobiera pakiety skojarzone z określonym manifestem usługi do pamięci podręcznej obrazu w określonym węźle. |
| Pakiet — kondycja | Pobiera informacje o kondycji pakietu usługi dla określonej aplikacji wdrożonej dla węzła Service Fabric i aplikacji. |
| Pakiet — informacje | Pobiera listę pakietów usługi wdrożonych w węźle Service Fabric zgodnym z określoną nazwą. |
| Lista pakietów | Pobiera listę pakietów usług wdrożonych w węźle Service Fabric. |
| odzyskiwanie | Wskazuje klaster Service Fabric, który powinien próbować odzyskać określoną usługę, która aktualnie jest zablokowana w utracie kworum. |
| Raport — kondycja | Wysyła raport o kondycji w usłudze Service Fabric. |
| wyjaśnić | Rozpoznaj partycję Service Fabric. |
| Lista typów | Pobiera listę zawierającą informacje o typach usług obsługiwanych przez typ aplikacji zainicjowanej w klastrze Service Fabric. |
| update | Aktualizuje określoną usługę za pomocą podanego opisu aktualizacji. |

## <a name="sfctl-service-app-name"></a>Nazwa aplikacji usługi sfctl
Pobiera nazwę aplikacji Service Fabric dla usługi.

Pobiera nazwę aplikacji dla określonej usługi. Błąd 404 FABRIC_E_SERVICE_DOES_NOT_EXIST jest zwracany, jeśli usługa o podanym IDENTYFIKATORze usługi nie istnieje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>kod usługi sfctl — lista pakietów
Pobiera listę pakietów kodu wdrożonych w węźle Service Fabric.

Pobiera listę pakietów kodu wdrożonych w węźle Service Fabric dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Code-package-name | Nazwa pakietu kodu określona w manifeście usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
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

## <a name="sfctl-service-create"></a>Tworzenie usługi sfctl
Tworzy określoną usługę Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-ID [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Name [wymagane] | Nazwa usługi. Powinien to być element podrzędny identyfikatora aplikacji. Jest to pełna nazwa, łącznie z `fabric\:` identyfikatorem URI. Na przykład usługa `fabric\:/A/B` jest elementem podrzędnym aplikacji `fabric\:/A` . |
| --Typ usługi [wymagane] | Nazwa typu usługi. |
| --Tryb aktywacji | Tryb aktywacji dla pakietu usługi. |
| --ograniczenia | Ograniczenia umieszczania w postaci ciągu. Ograniczenia umieszczania są wyrażeniami logicznymi we właściwościach węzła i umożliwiają ograniczenie usługi do określonych węzłów na podstawie wymagań usługi. Na przykład, aby umieścić usługę w węzłach, gdzie NodeType jest niebieska, określ następujące \: "NodeColor = = Blue". |
| --skorelowane-usługa | Nazwa docelowej usługi do powiązania. |
| --Korelacja | Należy skorelować usługę z istniejącą usługą przy użyciu koligacji wyrównania. |
| --DNS-Name | Nazwa DNS usługi, która ma zostać utworzona. Dla tego ustawienia musi być włączona usługa systemu DNS Service Fabric. |
| --instance-Count | Liczba wystąpień. Dotyczy to wyłącznie usług bezstanowych. |
| --int-Schema | Wskazuje, że usługa powinna być jednolicie partycjonowana dla zakresu liczb całkowitych bez znaku. |
| --int-Schema-Count | Liczba partycji w zakresie klucza liczb całkowitych do utworzenia, jeśli używany jest schemat partycji jednolitej liczby całkowitej. |
| --int-Schema-High | Koniec zakresu wartości całkowitych, jeśli używany jest schemat partycji jednolitej liczby całkowitej. |
| --int-Schema-niska | Początek zakresu wartości całkowitych, jeśli jest używany schemat partycji jednolitej liczby całkowitej. |
| --Load-Metrics | Zakodowana lista metryk w formacie JSON używana podczas równoważenia obciążenia usług między węzłami. |
| --min-Replica-Set-size | Minimalny rozmiar zestawu replik to liczba. Dotyczy to wyłącznie usług stanowych. |
| --Przenoszenie kosztów | Określa koszt przenoszenia usługi. Możliwe wartości to \: "zero", "Low", "medium", "High", "VeryHigh". |
| --nazwany schemat | Wskazuje, że usługa powinna mieć wiele nazwanych partycji. |
| --nazwany schemat-list | Zakodowana w formacie JSON lista nazw do partycjonowania usługi w programie, jeśli jest używany schemat partycji o nazwie. |
| --No-PERSISTED-State | W przypadku wartości true oznacza to, że usługa nie ma trwałego stanu przechowywanego na dysku lokalnym lub przechowuje tylko stan w pamięci. |
| --położenie-lista zasad | Zakodowana w formacie JSON lista zasad umieszczania dla usługi i skojarzonych nazw domen. Zasady mogą być jedną lub większą liczbą \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` , `RequireDomainDistribution` . |
| --kworum — utrata odczekania | Maksymalny czas (w sekundach), przez który partycja może być w stanie utraty kworum. Dotyczy to wyłącznie usług stanowych. |
| --Replika — oczekiwanie na ponowne uruchomienie | Czas trwania (w sekundach), po którym następuje awaria repliki i utworzenie nowej repliki. Dotyczy to wyłącznie usług stanowych. |
| --Skalowanie — zasady | Zakodowana w formacie JSON lista zasad skalowania dla tej usługi. |
| --Usługa-czas umieszczenia | Czas, przez który repliki mogą pozostać Nieskompilowane przed zgłoszeniem, że kompilacja zostanie zablokowana. Dotyczy to wyłącznie usług stanowych. |
| --Singleton-schemat | Wskazuje, że usługa powinna mieć pojedynczą partycję lub być usługą niepartycjonowaną. |
| --pogotowowanie do repliki — Zachowaj | Maksymalny czas (w sekundach) przechowywania replik rezerwy przed usunięciem. Dotyczy to wyłącznie usług stanowych. |
| --stanowe | Wskazuje, że usługa jest usługą stanową. |
| --bezstanowe | Wskazuje, że usługa jest usługą bezstanową. |
| --Target-Replica-Set-size | Docelowy rozmiar zestawu replik to liczba. Dotyczy to wyłącznie usług stanowych. |
| --timeout-t | Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-delete"></a>Usuwanie usługi sfctl
Usuwa istniejącą usługę Service Fabric.

Aby można było usunąć usługę, należy ją utworzyć. Domyślnie Service Fabric spróbuje zamknąć repliki usługi w sposób płynny, a następnie usunąć usługę. Jeśli jednak usługa ma problemy z zamykaniem repliki, operacja usuwania może zająć dużo czasu lub zostać zatrzymana. Użyj opcjonalnej flagi ForceRemove, aby pominąć przebieg sekwencji i wymuszone usunięcie usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
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

## <a name="sfctl-service-deployed-type"></a>wdrożono usługę sfctl-Type
Pobiera informacje o określonym typie usługi aplikacji wdrożonym w węźle klastra Service Fabric.

Pobiera listę zawierającą informacje o określonym typie usługi z aplikacji wdrożonych w węźle w klastrze Service Fabric. Odpowiedź obejmuje nazwę typu usługi, jego stan rejestracji, pakiet kodu, który zarejestrował go i identyfikator aktywacji pakietu usługi. Każdy wpis reprezentuje jedną aktywację typu usługi, zróżnicowaną przez identyfikator aktywacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-Type-Name [wymagane] | Określa nazwę typu usługi Service Fabric. |
| --Service-manifest-Name | Nazwa manifestu usługi, aby przefiltrować listę wdrożonych informacji o typie usługi. Jeśli ta wartość jest określona, odpowiedź będzie zawierać tylko informacje o typach usług, które są zdefiniowane w tym manifeście usługi. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-deployed-type-list"></a>wdrożono usługę sfctl-list typów
Pobiera listę zawierającą informacje o typach usług z aplikacji wdrożonych w węźle w klastrze Service Fabric.

Pobiera listę zawierającą informacje o typach usług z aplikacji wdrożonych w węźle w klastrze Service Fabric. Odpowiedź obejmuje nazwę typu usługi, jego stan rejestracji, pakiet kodu, który zarejestrował go i identyfikator aktywacji pakietu usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-manifest-Name | Nazwa manifestu usługi, aby przefiltrować listę wdrożonych informacji o typie usługi. Jeśli ta wartość jest określona, odpowiedź będzie zawierać tylko informacje o typach usług, które są zdefiniowane w tym manifeście usługi. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-description"></a>Opis usługi sfctl
Pobiera opis istniejącej usługi Service Fabric.

Pobiera opis istniejącej usługi Service Fabric. Aby można było uzyskać opis usługi, należy ją utworzyć.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl Service Get-Container-Logs
Pobiera dzienniki kontenerów dla kontenera wdrożonego w węźle Service Fabric.

Pobiera dzienniki kontenerów dla kontenera wdrożonego w węźle Service Fabric dla danego pakietu kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Code-package-name [wymagane] | Nazwa pakietu kodu określona w manifeście usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-manifest-Name [wymagane] | Nazwa manifestu usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --poprzednie | Określa, czy mają zostać pobrane dzienniki kontenerów z zakończonych/martwych kontenerów wystąpienia pakietu kodu. |
| --tail | Liczba wierszy do wyświetlenia na końcu dzienników. Wartość domyślna to 100. "wszystkie", aby wyświetlić kompletne dzienniki. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-health"></a>kondycja usługi sfctl
Pobiera kondycję określonej usługi Service Fabric.

Pobiera informacje o kondycji określonej usługi. Użyj EventsHealthStateFilter, aby odfiltrować kolekcję zdarzeń kondycji zgłoszonych w usłudze na podstawie stanu kondycji. Użyj PartitionsHealthStateFilter, aby odfiltrować kolekcję zwracanych partycji. W przypadku określenia usługi, która nie istnieje w magazynie kondycji, żądanie zwróci błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --Events-kondycja-filtr | Umożliwia filtrowanie kolekcji obiektów HealthEvent zwracanych na podstawie stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia zgodne z filtrem. Wszystkie zdarzenia są używane do oszacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczeniem opartym na flagach, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, zwracane są wszystkie zdarzenia z wartością HealthState równą OK (2) i ostrzeżenie (4).  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --Exclude-Health-Statistics | Wskazuje, czy statystyki kondycji mają być zwracane jako część wyniku zapytania. Domyślnie wartość false. Statystyki przedstawiają liczbę jednostek podrzędnych w stanie kondycji prawidłowy, ostrzegawczy i błąd. |
| --Partitions-Health-State-Filter | Umożliwia filtrowanie obiektów stanu kondycji partycji zwracanych w wyniku zapytania o kondycję usługi na podstawie ich stanu kondycji. Możliwe wartości tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko partycje zgodne z filtrem. Wszystkie partycje są używane do szacowania zagregowanego stanu kondycji. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczaniem opartym na flagach, więc wartość może być kombinacją wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość to 6, stan kondycji partycji z wartością HealthState równą OK (2) i ostrzeżenie (4) zostaną zwrócone.  <br> -Default — wartość domyślna. Dopasowuje dowolny HealthState. Wartość jest równa zero.  <br> -None-Filter, który nie pasuje do żadnej wartości HealthState. Używane w celu zwracania wyników w danej kolekcji Stanów. Wartość jest równa 1.  <br> -OK-Filter, który dopasowuje dane wejściowe z wartością HealthState równą OK. Wartość jest równa 2.  <br> -Warning-Filter, który dopasowuje dane wejściowe z ostrzeżeniem o wartości HealthState. Wartość to 4.  <br> -Error-Filter, który dopasowuje dane wejściowe z błędem wartości HealthState. Wartość to 8.  <br> -All-Filter, który dopasowuje dane wejściowe z dowolną wartością HealthState. Wartość to 65535. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-info"></a>Informacje o usłudze sfctl
Pobiera informacje o konkretnej usłudze należącej do aplikacji Service Fabric.

Zwraca informacje dotyczące określonej usługi należącej do określonej Service Fabric aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
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

## <a name="sfctl-service-list"></a>Lista usługi sfctl
Pobiera informacje o wszystkich usługach należących do aplikacji określonej przez identyfikator aplikacji.

Zwraca informacje o wszystkich usługach należących do aplikacji określonej przez identyfikator aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Kontynuacja — token | Parametr tokenu kontynuacji służy do uzyskiwania następnego zestawu wyników. Token kontynuacji z niepustą wartością jest dołączany do odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przenoszona do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --Service-Type-Name | Nazwa typu usługi używana do filtrowania usług w celu wykonywania zapytań. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-manifest"></a>Manifest usługi sfctl
Pobiera manifest opisujący typ usługi.

Pobiera manifest opisujący typ usługi. Odpowiedź zawiera kod XML manifestu usługi jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Typ aplikacji-nazwa [wymagane] | Nazwa typu aplikacji. |
| --Typ aplikacji — wersja [wymagana] | Wersja typu aplikacji. |
| --Service-manifest-Name [wymagane] | Nazwa manifestu usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-package-deploy"></a>Pakiet usługi sfctl — wdrażanie
Pobiera pakiety skojarzone z określonym manifestem usługi do pamięci podręcznej obrazu w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-type-name [wymagane] | Nazwa manifestu aplikacji dla odpowiadającego żądanego manifestu usługi. |
| --App-Type-Version [wymagane] | Wersja manifestu aplikacji dla odpowiadającego żądanego manifestu usługi. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-manifest-Name [wymagane] | Nazwa manifestu usługi skojarzonego z pakietami, które zostaną pobrane. |
| --Share-Policy | Zakodowana lista zasad udostępniania w formacie JSON. Każdy element zasad udostępniania składa się z wartości "name" i "Scope". Nazwa odpowiada nazwie kodu, konfiguracji lub pakietu danych, który ma zostać udostępniony. Zakresem może być "none", "All", "Code", "config" lub "Data". |
| --timeout-t | Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-package-health"></a>Pakiet usługi sfctl Service — kondycja
Pobiera informacje o kondycji pakietu usługi dla określonej aplikacji wdrożonej dla węzła Service Fabric i aplikacji.

Pobiera informacje o kondycji pakietu usługi dla określonej aplikacji wdrożonej w węźle Service Fabric. Użyj EventsHealthStateFilter, aby opcjonalnie odfiltrować kolekcję obiektów HealthEvent raportowanych dla wdrożonego pakietu usługi na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-package-name [wymagane] | Nazwa pakietu usługi. |
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

## <a name="sfctl-service-package-info"></a>Pakiet usługi sfctl Service — informacje
Pobiera listę pakietów usługi wdrożonych w węźle Service Fabric zgodnym z określoną nazwą.

Zwraca informacje o pakietach usług wdrożonych w węźle Service Fabric dla danej aplikacji. Te wyniki są pakietami usług, których nazwa pasuje dokładnie do nazwy pakietu usługi określonego jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-package-name [wymagane] | Nazwa pakietu usługi. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-package-list"></a>Lista pakietów usługi sfctl
Pobiera listę pakietów usług wdrożonych w węźle Service Fabric.

Zwraca informacje o pakietach usług wdrożonych w węźle Service Fabric dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-recover"></a>odzyskiwanie usługi sfctl
Wskazuje klaster Service Fabric, który powinien próbować odzyskać określoną usługę, która aktualnie jest zablokowana w utracie kworum.

Wskazuje klaster Service Fabric, który powinien próbować odzyskać określoną usługę, która aktualnie jest zablokowana w utracie kworum. Tę operację należy wykonać tylko wtedy, gdy wiadomo, że repliki nie mogą zostać odzyskane. Nieprawidłowe użycie tego interfejsu API może spowodować utratę danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>Raport usługi sfctl — kondycja
Wysyła raport o kondycji w usłudze Service Fabric.

Zgłasza stan kondycji określonej usługi Service Fabric. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raportowany. Raport jest wysyłany do usługi bramy Service Fabric, która przekazuje do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak stary numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w zdarzeniach dotyczących kondycji usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Health-Property [wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem i nie jest stałym wyliczeniem, aby umożliwić elastyczność programu Reporter na kategoryzowanie warunku stanu, który wyzwala raport. Na przykład, Raport z identyfikatorem SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może raportować Właściwość "AvailableDisk" w tym węźle. Ten sam raport może monitorować łączność z węzłami, dzięki czemu może zgłosić Właściwość "łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia dotyczące kondycji dla określonego węzła. Wraz z identyfikatorem SourceId Właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --kondycja — stan [wymagane] | Możliwe wartości to \: "nieprawidłowe", "OK", "ostrzeżenie", "błąd", "nieznany". |
| --ID usługi [wymagane] | Tożsamość usługi. <br><br> Jest to zazwyczaj pełna nazwa usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Jeśli na przykład nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi byłaby "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
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

## <a name="sfctl-service-resolve"></a>Rozpoznawanie usługi sfctl
Rozpoznaj partycję Service Fabric.

Aby uzyskać punkty końcowe replik usług, należy rozwiązać Service Fabric partycji usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład, jeśli nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi powinna mieć wartość "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --Partition-typ | Typ klucza dla partycji. Ten parametr jest wymagany, jeśli schemat partycji dla usługi jest Int64Range lub nazwany. Możliwe wartości są następujące. -Brak (1)-wskazuje, że parametr PartitionKeyValue nie jest określony. Jest to prawidłowe dla partycji z schematem partycjonowania jako pojedynczą. Jest to wartość domyślna. Wartość jest równa 1. -Int64Range (2)-wskazuje, że parametr PartitionKeyValue jest kluczem partycji Int64. Jest to prawidłowe dla partycji z schematem partycjonowania jako Int64Range. Wartość jest równa 2. -Nazwane (3) — wskazuje, że parametr PartitionKeyValue jest nazwą partycji. Jest to prawidłowe dla partycji z schematem partycjonowania o nazwie. Wartość to 3. |
| --Partition-key-value | Klucz partycji. Jest to wymagane, jeśli schemat partycji dla usługi jest Int64Range lub o nazwie. To nie jest identyfikator partycji, ale jego wartość całkowita lub nazwa identyfikatora partycji. Na przykład jeśli usługa korzysta z zakresów partycji z zakresu od 0 do 10, wówczas PartitionKeyValue będzie liczbą całkowitą z tego zakresu. Opis usługi kwerendy, aby zobaczyć zakres lub nazwę. |
| --Previous-w wersji | Wartość w polu wersja odpowiedzi, która została wcześniej odebrana. Jest to wymagane, jeśli użytkownik wie, że uzyskany wcześniej wynik jest przestarzały. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-type-list"></a>Lista typów usługi sfctl
Pobiera listę zawierającą informacje o typach usług obsługiwanych przez typ aplikacji zainicjowanej w klastrze Service Fabric.

Pobiera listę zawierającą informacje o typach usług obsługiwanych przez typ aplikacji zainicjowanej w klastrze Service Fabric. Podany typ aplikacji musi istnieć. W przeciwnym razie jest zwracany stan 404.

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

## <a name="sfctl-service-update"></a>Aktualizacja usługi sfctl
Aktualizuje określoną usługę za pomocą podanego opisu aktualizacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID usługi [wymagane] | Tożsamość usługi. Jest to zazwyczaj pełna nazwa usługi bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Jeśli na przykład nazwa usługi to "Fabric \: /MyApp/APP1/svc1", tożsamość usługi byłaby "MojaApl \~ APP1 \~ svc1" w wersji 6.0 + i "MojaApl/APP1/svc1" w poprzednich wersjach. |
| --ograniczenia | Ograniczenia umieszczania w postaci ciągu. Ograniczenia umieszczania są wyrażeniami logicznymi we właściwościach węzła i umożliwiają ograniczenie usługi do określonych węzłów na podstawie wymagań usługi. Na przykład, aby umieścić usługę w węzłach, gdzie NodeType jest niebieska, określ następujące \: "NodeColor = = Blue". |
| --skorelowane-usługa | Nazwa docelowej usługi do powiązania. |
| --Korelacja | Należy skorelować usługę z istniejącą usługą przy użyciu koligacji wyrównania. |
| --instance-Count | Liczba wystąpień. Dotyczy to wyłącznie usług bezstanowych. |
| --Load-Metrics | Zakodowana lista wartości JSON metryk używanych podczas równoważenia obciążenia między węzłami. |
| --min-Replica-Set-size | Minimalny rozmiar zestawu replik to liczba. Dotyczy to wyłącznie usług stanowych. |
| --Przenoszenie kosztów | Określa koszt przenoszenia usługi. Możliwe wartości to \: "zero", "Low", "medium", "High", "VeryHigh". |
| --położenie-lista zasad | Zakodowana w formacie JSON lista zasad umieszczania dla usługi i skojarzonych nazw domen. Zasady mogą być jedną lub większą liczbą \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` , `RequireDomainDistribution` . |
| --kworum — utrata odczekania | Maksymalny czas (w sekundach), przez który partycja może być w stanie utraty kworum. Dotyczy to wyłącznie usług stanowych. |
| --Replika — oczekiwanie na ponowne uruchomienie | Czas trwania (w sekundach), po którym następuje awaria repliki i utworzenie nowej repliki. Dotyczy to wyłącznie usług stanowych. |
| --Skalowanie — zasady | Zakodowana w formacie JSON lista zasad skalowania dla tej usługi. |
| --Usługa-czas umieszczenia | Czas, przez który repliki mogą pozostać Nieskompilowane przed zgłoszeniem, że kompilacja zostanie zablokowana. Dotyczy to wyłącznie usług stanowych. |
| --pogotowowanie do repliki — Zachowaj | Maksymalny czas (w sekundach) przechowywania replik rezerwy przed usunięciem. Dotyczy to wyłącznie usług stanowych. |
| --stanowe | Wskazuje, że usługa docelowa jest usługą stanową. |
| --bezstanowe | Wskazuje, że usługa docelowa jest usługą bezstanową. |
| --Target-Replica-Set-size | Docelowy rozmiar zestawu replik to liczba. Dotyczy to wyłącznie usług stanowych. |
| --timeout-t | Wartość domyślna \: 60. |

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
