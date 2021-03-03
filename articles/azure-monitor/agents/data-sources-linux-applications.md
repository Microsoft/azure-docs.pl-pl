---
title: Zbieranie danych o wydajności aplikacji systemu Linux w Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania agenta Log Analytics dla systemu Linux w celu zbierania liczników wydajności dla programu MySQL i Apache HTTP Server.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: f924bcdce0937c74c3deeab96f3d4b2a85e3944d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704450"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Zbieranie liczników wydajności dla aplikacji systemu Linux w Azure Monitor 

Ten artykuł zawiera szczegółowe informacje dotyczące konfigurowania [agenta log Analytics dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) w celu zbierania liczników wydajności dla określonych aplikacji do Azure monitor.  W tym artykule znajdują się następujące aplikacje:  

- [MySQL](#mysql)
- [Serwer Apache HTTP](#apache-http-server)

## <a name="mysql"></a>MySQL
Jeśli na komputerze zostanie wykryty serwer MySQL lub serwer MariaDB podczas instalowania agenta Log Analytics, zostanie automatycznie zainstalowany dostawca monitorowania wydajności dla serwera MySQL. Ten dostawca nawiązuje połączenie z lokalnym serwerem MySQL/MariaDB w celu udostępnienia statystyk wydajności. Poświadczenia użytkownika programu MySQL muszą być skonfigurowane tak, aby Dostawca mógł uzyskać dostęp do serwera MySQL.

### <a name="configure-mysql-credentials"></a>Skonfiguruj poświadczenia MySQL
Dostawca MySQL OMI wymaga wstępnie skonfigurowanego użytkownika programu MySQL i zainstalowanych bibliotek klienta MySQL, aby można było wysyłać zapytania o wydajność i kondycję z wystąpienia programu MySQL.  Te poświadczenia są przechowywane w pliku uwierzytelniania, który jest przechowywany w agencie systemu Linux.  Plik uwierzytelniania określa adres powiązania i port, na którym nasłuchuje wystąpienie MySQL, oraz poświadczenia, które mają być używane do zbierania metryk.  

Podczas instalacji agenta Log Analytics dla systemu Linux dostawca MySQL OMI skanuje pliki konfiguracji MySQL my. cnf (domyślne lokalizacje) dla powiązania i adresu oraz częściowo ustawi plik uwierzytelniania MySQL OMI.

Plik uwierzytelniania MySQL jest przechowywany w lokalizacji `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth` .


### <a name="authentication-file-format"></a>Format pliku uwierzytelniania
Poniżej znajduje się format pliku uwierzytelniania MySQL OMI

> [Port] = [bind-address], [username], [hasło zakodowane w formacie base64]  
> (Port) = (adres powiązania), (username), (hasło kodowane algorytmem Base64)  
> (Port) = (adres powiązania), (username), (hasło kodowane algorytmem Base64)  
> Autoaktualizacja = [true | false]  

Wpisy w pliku uwierzytelniania są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--|:--|
| Port | Reprezentuje bieżący port, na którym nasłuchuje wystąpienie MySQL. Port 0 Określa, że w przypadku wystąpienia domyślnego są używane następujące właściwości. |
| Bind-Address| Bieżący adres powiązania MySQL. |
| nazwa użytkownika| Użytkownik programu MySQL używany do monitorowania wystąpienia serwera MySQL. |
| Hasło zakodowane w formacie base64| Hasło użytkownika monitorowania MySQL zakodowane w formacie base64. |
| AutoUpdate| Określa, czy ponownie skanować pod kątem zmian w pliku my. cnf i zastępować plik uwierzytelniania MySQL OMI, gdy zostanie uaktualniony dostawca programu MySQL OMI. |

### <a name="default-instance"></a>Wystąpienie domyślne
Plik uwierzytelniania MySQL OMI może definiować wystąpienie domyślne i numer portu, aby ułatwić zarządzanie wieloma wystąpieniami programu MySQL na jednym hoście z systemem Linux.  Wystąpienie domyślne jest wskazywane przez wystąpienie z portem 0. Wszystkie dodatkowe wystąpienia będą dziedziczyć właściwości ustawione z wystąpienia domyślnego, chyba że określisz inne wartości. Na przykład jeśli zostanie dodane wystąpienie MySQL nasłuchuje na porcie "3308", zostanie użyte domyślne powiązanie — adres, nazwa użytkownika i hasło zakodowane algorytmem Base64, aby spróbować i monitorować wystąpienie nasłuchiwania na 3308. Jeśli wystąpienie na 3308 jest powiązane z innym adresem i używa tej samej pary nazw i hasła programu MySQL, wymagany jest tylko adres powiązania, a inne właściwości zostaną Odziedziczone.

W poniższej tabeli przedstawiono przykładowe ustawienia wystąpienia 

| Opis | File |
|:--|:--|
| Wystąpienie domyślne i wystąpienie z portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Wystąpienie domyślne i wystąpienie z portem 3308 oraz inną nazwą użytkownika i hasłem. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Program plików uwierzytelniania MySQL OMI
Dołączona do instalacji dostawcy MySQL OMI to program plików uwierzytelniania MySQL OMI, którego można użyć do edytowania pliku uwierzytelniania OMI MySQL. Program plików uwierzytelniania można znaleźć w następującej lokalizacji.

`/opt/microsoft/mysql-cimprov/bin/mycimprovauth`

> [!NOTE]
> Plik poświadczeń musi być możliwy do odczytania przez konto omsagent. Uruchamianie polecenia mycimprovauth jako omsgent jest zalecane.

Poniższa tabela zawiera szczegółowe informacje na temat składni programu mycimprovauth.

| Operacja | Przykład | Opis
|:--|:--|:--|
| Automatyczne *lub prawdziwe* | mycimprovauth — FAŁSZ | Określa, czy plik uwierzytelniania zostanie automatycznie zaktualizowany przy ponownym uruchomieniu lub aktualizacji. |
| domyślne *powiązanie — hasło nazwy użytkownika* | mycimprovauth domyślnie 127.0.0.1 root PWD | Ustawia domyślne wystąpienie w pliku uwierzytelniania MySQL OMI.<br>Pole hasła powinno być wprowadzane w postaci zwykłego tekstu — hasło w pliku uwierzytelniania MySQL OMI będzie kodowane zgodnie z podstawową 64. |
| Usuń *domyślne lub port_num* | mycimprovauth 3308 | Usuwa określone wystąpienie przez domyślne lub przez numer portu. |
| Pomoc | Pomoc mycimprov | Drukuje listę poleceń do użycia. |
| drukowanie | mycimprov druku | Drukuje łatwy do odczytania plik uwierzytelniania MySQL OMI. |
| Aktualizacja port_num *powiązana — adres użytkownika hasła* | mycimprov Update 3307 127.0.0.1 root PWD | Aktualizuje określone wystąpienie lub dodaje wystąpienie, jeśli nie istnieje. |

Następujące przykładowe polecenia definiują domyślne konto użytkownika dla serwera MySQL na hoście lokalnym.  Pole hasła powinno być wprowadzane w postaci zwykłego tekstu — hasło w pliku OMI uwierzytelniania MySQL zostanie zakodowane zgodnie z podstawową 64

```console
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
sudo /opt/omi/bin/service_control restart
```

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Uprawnienia bazy danych wymagane dla liczników wydajności programu MySQL
Użytkownik programu MySQL wymaga dostępu do następujących zapytań w celu zebrania danych wydajności serwera MySQL. 

```sql
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Użytkownik MySQL wymaga również WYBRANia dostępu do następujących tabel domyślnych.

- information_schema
- MySQL. 

Te uprawnienia można udzielić, uruchamiając następujące polecenia.

```sql
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

> [!NOTE]
> Aby udzielić uprawnień użytkownikowi monitorowania bazy danych MySQL, użytkownik mający uprawnienia do przydzielenia musi mieć uprawnienie "udzielenie opcji" oraz przyznane uprawnienie.

### <a name="define-performance-counters"></a>Definiowanie liczników wydajności

Po skonfigurowaniu agenta Log Analytics dla systemu Linux w celu wysyłania danych do Azure Monitor należy skonfigurować liczniki wydajności do zbierania.  Użyj procedury w [źródłach danych wydajności systemu Windows i Linux w Azure monitor](data-sources-performance-counters.md) z licznikami w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Baza danych MySQL | Miejsce na dysku w bajtach |
| Baza danych MySQL | Tabele |
| Serwer MySQL | Przerwano połączenie z PCT |
| Serwer MySQL | Użycie połączenia PCT |
| Serwer MySQL | Użycie miejsca na dysku w bajtach |
| Serwer MySQL | Pełne skanowanie tabeli — PCT |
| Serwer MySQL | Pula buforów InnoDB trafień PCT |
| Serwer MySQL | Pula buforów InnoDB użycie protokołu PCT |
| Serwer MySQL | Pula buforów InnoDB użycie protokołu PCT |
| Serwer MySQL | Pamięć podręczna kluczy osiągnęła PCT |
| Serwer MySQL | Użycie pamięci podręcznej kluczy |
| Serwer MySQL | Zapis PCT pamięci podręcznej kluczy |
| Serwer MySQL | Osiągnięto procent pamięci podręcznej zapytań |
| Serwer MySQL | Pamięć podręczna zapytań oczyszcza |
| Serwer MySQL | Pamięć podręczna zapytań używa protokołu PCT |
| Serwer MySQL | Pamięć podręczna tabeli osiągnęła PCT |
| Serwer MySQL | Pamięć podręczna tabel — użycie protokołu PCT |
| Serwer MySQL | Rywalizacja o blokady tabeli (%) |

## <a name="apache-http-server"></a>Serwer Apache HTTP 
Jeśli na komputerze zostanie wykryty serwer Apache HTTP, podczas instalowania pakietu omsagent zostanie automatycznie zainstalowany dostawca monitorowania wydajności dla serwera Apache HTTP. Ten dostawca jest zależny od modułu Apache, który musi zostać załadowany do serwera Apache HTTP w celu uzyskania dostępu do danych wydajności. Moduł można załadować przy użyciu następującego polecenia:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Aby zwolnić moduł monitorowania Apache, uruchom następujące polecenie:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definiowanie liczników wydajności

Po skonfigurowaniu agenta Log Analytics dla systemu Linux w celu wysyłania danych do Azure Monitor należy skonfigurować liczniki wydajności do zbierania.  Użyj procedury w [źródłach danych wydajności systemu Windows i Linux w Azure monitor](data-sources-performance-counters.md) z licznikami w poniższej tabeli.

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Serwer Apache HTTP | Zajęci pracownicy |
| Serwer Apache HTTP | Bezczynne procesy robocze |
| Serwer Apache HTTP | Pracownicy obciążonej PCT |
| Serwer Apache HTTP | Łączny czas procesora (%) |
| Host wirtualny Apache | Błędy na minutę — klient |
| Host wirtualny Apache | Błędy na minutę — serwer |
| Host wirtualny Apache | KB na żądanie |
| Host wirtualny Apache | Liczba żądań w KB na sekundę |
| Host wirtualny Apache | Liczba żądań na sekundę |



## <a name="next-steps"></a>Następne kroki
* [Zbieraj liczniki wydajności](data-sources-performance-counters.md) z agentów systemu Linux.
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.