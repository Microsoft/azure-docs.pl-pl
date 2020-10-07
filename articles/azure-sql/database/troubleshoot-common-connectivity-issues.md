---
title: Praca z błędami przejściowymi
description: Dowiedz się, jak rozwiązywać problemy, diagnozować i zapobiegać błędom połączenia SQL lub błędem przejściowym podczas nawiązywania połączenia z Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics.
keywords: połączenie SQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 46d8aab74f658b039fe07acab82f324ec6ad731f
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777075"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>Rozwiązywanie problemów z błędami połączeń przejściowych w SQL Database i wystąpieniu zarządzanym SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym artykule opisano, jak zapobiegać, rozwiązywać problemy, diagnozować i ograniczać błędy połączeń oraz błędy przejściowe, które napotyka aplikacja kliencka, gdy współdziała z Azure SQL Database, wystąpieniem zarządzanym Azure SQL i analizą usługi Azure Synapse. Dowiedz się, jak skonfigurować logikę ponowień, skompilować parametry połączenia i dostosować inne ustawienia połączenia.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Błędy przejściowe (awarie przejściowe)

Błąd przejściowy, nazywany także błędem przejściowym, ma zasadniczą przyczynę, która wkrótce wyeliminuje siebie. Sporadyczne wystąpienie błędów przejściowych polega na tym, że system Azure szybko przenosi zasoby sprzętowe w celu lepszego zrównoważenia obciążenia różnych obciążeń. Większość tych zdarzeń ponownej konfiguracji kończy się za mniej niż 60 sekund. Podczas tego przedziału czasu ponownej konfiguracji mogą wystąpić problemy z połączeniem z bazą danych w SQL Database. Aplikacje, które łączą się z bazą danych, powinny zostać skompilowane, aby oczekiwać na te błędy przejściowe. Aby je obsłużyć, należy wdrożyć logikę ponowień w kodzie, zamiast dopełniać je do użytkowników jako błędy aplikacji.

Jeśli program kliencki używa ADO.NET, program jest powiadamiany o błędzie przejściowym przez throw **SqlException**.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Połączenie a polecenie

Ponów SQL Database i połączenie wystąpienia zarządzanego SQL lub Ustanów je ponownie, w zależności od następujących:

- **Wystąpił błąd przejściowy podczas próby połączenia**

Po upływie kilku sekund spróbuj ponownie nawiązać połączenie.

- **Wystąpił błąd przejściowy podczas wykonywania zapytania dotyczącego wystąpienia zarządzanego SQL Database i SQL**

Nie należy natychmiast ponownie wykonywać polecenia. Zamiast tego po opóźnieniu nawiązać połączenie. Następnie ponów próbę wykonania tego polecenia.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logika ponawiania próby dla błędów przejściowych

Programy klienckie, które sporadycznie napotykają błąd przejściowy, są bardziej niezawodne, gdy zawierają logikę ponowień. Gdy program komunikuje się z bazą danych programu w SQL Database za pomocą oprogramowania pośredniczącego innej firmy, należy polecić dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponawiania prób w przypadku błędów przejściowych.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Zasady ponawiania prób

- Jeśli błąd jest przejściowy, spróbuj ponownie otworzyć połączenie.
- Nie należy bezpośrednio ponawiać próby SQL Database lub instrukcji wystąpienia zarządzanego SQL `SELECT` , które zakończyły się niepowodzeniem z powodu błędu przejściowego. Zamiast tego Ustanów nowe połączenie, a następnie ponów próbę `SELECT` .
- Gdy SQL Database lub instrukcja wystąpienia zarządzanego SQL `UPDATE` kończy się niepowodzeniem z błędem przejściowym, przed ponowieniem aktualizacji Ustanów nowe połączenie. Logika ponawiania musi mieć pewność, że cała transakcja bazy danych została zakończona lub cała transakcja jest wycofywana.

### <a name="other-considerations-for-retry"></a>Inne zagadnienia dotyczące ponawiania prób

- Program wsadowy, który jest uruchamiany automatycznie po godzinach pracy i kończy się przed rano, może być bardzo pacjenta z długimi interwałami czasu między ponownymi próbami.
- Program interfejsu użytkownika powinien uwzględnić tendencję ludzkiej, która ma zostać wystawiona po zbyt długim czasie oczekiwania. Rozwiązanie nie może ponawiać prób co kilka sekund, ponieważ te zasady mogą wypełniać system żądaniami.

### <a name="interval-increase-between-retries"></a>Wzrost interwału między ponownymi próbami

Zalecamy poczekanie przez 5 sekund przed pierwszym ponowieniem próby. Ponawianie próby po opóźnieniu krótszym niż 5 sekund ryzyka przeciążania usługi w chmurze. Dla każdej kolejnej ponowienia próby opóźnienie powinno się zwiększyć wykładniczo, maksymalnie 60 sekund.

Aby zapoznać się z omówieniem okresu blokowania dla klientów korzystających z ADO.NET, zobacz [pule połączeń (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Warto również ustawić maksymalną liczbę ponownych prób przed zakończeniem działania programu.

### <a name="code-samples-with-retry-logic"></a>Przykłady kodu z logiką ponawiania

Przykłady kodu z logiką ponowień są dostępne pod adresem:

- [Odłączanie się do usługi Azure SQL za pomocą ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Odłączanie się do usługi Azure SQL przy użyciu języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testowanie logiki ponowień

Aby przetestować logikę ponowień, należy symulować lub spowodować błąd, który można poprawić, gdy program jest nadal uruchomiony.

#### <a name="test-by-disconnecting-from-the-network"></a>Testowanie przez odłączenie od sieci

Jednym ze sposobów przetestowania logiki ponawiania jest odłączenie komputera klienckiego od sieci, gdy program jest uruchomiony. Błąd to:

- **SqlException. Number** = 11001
- Komunikat: "ten host nie jest znany"

W ramach pierwszej ponowienia próby można ponownie połączyć komputer kliencki z siecią, a następnie podjąć próbę nawiązania połączenia.

Aby ten test był praktyczny, Odłącz komputer od sieci przed uruchomieniem programu. Następnie program rozpoznaje parametr środowiska uruchomieniowego, który powoduje, że program:

- Tymczasowo Dodaj 11001 do listy błędów, które należy wziąć pod uwagę jako przejściowe.
- Próba pierwszego połączenia w zwykły sposób.
- Po przechwyconiu błędu Usuń 11001 z listy.
- Wyświetl komunikat informujący użytkownika o konieczności podłączenia komputera do sieci.
- Wstrzymaj dalsze wykonywanie przy użyciu metody **Console. ReadLine** lub okna dialogowego z przyciskiem OK. Użytkownik naciśnie klawisz Enter po podłączeniu komputera do sieci.
- Spróbuj ponownie nawiązać połączenie, oczekiwanie na powodzenie.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Przetestuj, wprowadzając błąd podczas nawiązywania połączenia nazwy użytkownika

Program może przeznaczenie nazwy użytkownika przed rozpoczęciem pierwszej próby połączenia. Błąd to:

- **SqlException. Number** = 18456
- Komunikat: "nie można zalogować użytkownika" WRONG_MyUserName "."

W ramach pierwszej ponowienia próby program może poprawić błąd pisowni, a następnie podjąć próbę nawiązania połączenia.

Aby ten test był praktyczny, program rozpoznaje parametr środowiska uruchomieniowego, który powoduje, że program:

- Tymczasowo Dodaj 18456 do listy błędów, które należy wziąć pod uwagę jako przejściowe.
- Należy do tego celu dodać "WRONG_" do nazwy użytkownika.
- Po przechwyconiu błędu Usuń 18456 z listy.
- Usuń element "WRONG_" z nazwy użytkownika.
- Spróbuj ponownie nawiązać połączenie, oczekiwanie na powodzenie.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametry .NET SqlConnection dla ponowienia połączenia

Jeśli program kliencki nawiązuje połączenie z bazą danych w SQL Database przy użyciu klasy .NET Framework **System. Data. SqlClient. SqlConnection**, użyj programu .NET 4.6.1 lub nowszego (lub platformy .NET Core), aby można było używać funkcji ponawiania połączeń. Aby uzyskać więcej informacji na temat funkcji, zobacz [Właściwość SqlConnection. ConnectionString](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Podczas tworzenia [parametrów połączenia](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) dla obiektu **SqlConnection** koordynuj wartości z następujących parametrów:

- **ConnectRetryCount**: &nbsp; &nbsp; wartość domyślna to 1. Zakres to od 0 do 255.
- **ConnectRetryInterval**: &nbsp; &nbsp; wartość domyślna to 10 sekund. Zakres to od 1 do 60.
- **Limit czasu połączenia**: &nbsp; &nbsp; wartość domyślna to 15 sekund. Zakres to od 0 do 2147483647.

W związku z tym wybrane wartości powinny mieć następujące równość: limit czasu połączenia = ConnectRetryCount * ConnectionRetryInterval

Na przykład, jeśli liczba jest równa 3, a interwał jest równy 10 sekund, przekroczenie limitu czasu przez 29 sekund nie daje systemowi wystarczającej czasu na jego trzecią i końcową ponowną próbę nawiązania połączenia: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Połączenie a polecenie

Parametry **ConnectRetryCount** i **ConnectRetryInterval** pozwalają obiektowi **SQLConnect** ponowić operację łączenia bez podania lub bothering programu, takiego jak zwracanie kontroli do programu. Ponawianie prób może wystąpić w następujących sytuacjach:

- Wywołanie metody SqlConnection. Open
- SqlConnection.Exewywołanie metody urocze

Istnieje element subtlety. Jeśli wystąpi błąd przejściowy podczas wykonywania *zapytania* , obiekt **SqlConnection** nie spróbuje wykonać operacji Connect. Nie powoduje to ponowienia próby wykonania zapytania. Jednak element **SqlConnection** bardzo szybko sprawdza połączenie przed wysłaniem zapytania do wykonania. Jeśli szybkie sprawdzenie wykryje problem z połączeniem, program **SQLConnect** próbuje wykonać operację łączenia. Jeśli ponowna próba powiodła się, zapytanie jest wysyłane do wykonania.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Czy ConnectRetryCount być połączone z logiką ponawiania aplikacji

Załóżmy, że Twoja aplikacja ma niestandardową logikę ponowień. Może ponowić próbę wykonania operacji połączenia czterokrotnie. Jeśli dodasz **ConnectRetryInterval** i **ConnectRetryCount** = 3 do parametrów połączenia, zostanie zwiększona liczba ponownych prób do 4 * 3 = 12 ponownych prób. Nie może to być duża liczba ponownych prób.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Połączenia z bazą danych w SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Połączenie: parametry połączenia

Parametry połączenia, które są niezbędne do nawiązania połączenia z bazą danych, różnią się nieco od ciągu używanego do łączenia się z SQL Server. Parametry połączenia dla bazy danych można skopiować z [Azure Portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Połączenie: adres IP

Należy skonfigurować SQL Database, aby akceptować komunikację z adresu IP komputera, na którym jest hostowany program kliencki. Aby skonfigurować tę konfigurację, Edytuj ustawienia zapory za pomocą [Azure Portal](https://portal.azure.com/).

Jeśli zapomnisz skonfigurować adres IP, program zakończy się niepowodzeniem z komunikatem o błędzie z informacją o wymaganym adresie IP.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień zapory w SQL Database](firewall-configure.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Połączenie: porty

Zazwyczaj należy upewnić się, że tylko port 1433 jest otwarty dla komunikacji wychodzącej na komputerze hostującym program kliencki.

Na przykład, gdy program kliencki jest hostowany na komputerze z systemem Windows, można użyć zapory systemu Windows na hoście, aby otworzyć port 1433.

1. Otwórz Panel sterowania.
2. Wybierz kolejno **pozycje wszystkie elementy panelu sterowania**  >  Opcje**Zapora systemu Windows**  >  **Zaawansowane ustawienia**  >  **reguły ruchu wychodzącego**  >  **Actions**  >  **Nowa reguła**.

Jeśli program kliencki jest hostowany na maszynie wirtualnej platformy Azure, Odczytaj [porty powyżej 1433 dla ADO.NET 4,5 i SQL Database](adonet-v12-develop-direct-route-ports.md).

Aby uzyskać ogólne informacje na temat konfiguracji portów i adresów IP w bazie danych, zobacz [Azure SQL Database zapory](firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Połączenie: ADO.NET 4.6.2 lub nowsze

Jeśli program używa klas ADO.NET, takich jak **System. Data. SqlClient.** SqlConnection, aby nawiązać połączenie z SQL Database, zalecamy użycie .NET Framework w wersji 4.6.2 lub nowszej.

#### <a name="starting-with-adonet-462"></a>Począwszy od ADO.NET 4.6.2

- Zostanie ponowiona próba otwarcia połączenia dla usługi Azure SQL, co poprawia wydajność aplikacji obsługujących chmurę.

#### <a name="starting-with-adonet-461"></a>Począwszy od ADO.NET 4.6.1

- W przypadku SQL Database Zwiększona niezawodność podczas otwierania połączenia przy użyciu metody **SQLConnect. Open** . Metoda **Open** ma teraz w odpowiedzi na błędy przejściowe, które są w trakcie przekroczenia limitu czasu połączenia, aby wystąpiły problemy z niektórymi błędami.
- Obsługiwane jest buforowanie połączeń, które obejmuje wydajne sprawdzenie, czy obiekt połączenia, który zapewnia program, działa.

W przypadku używania obiektu połączenia z puli połączeń zalecamy, aby program tymczasowo zamykał połączenie, gdy nie jest ono bezpośrednio używane. Ponowne otwarcie połączenia nie jest kosztowne, ale jest to konieczne do utworzenia nowego połączenia.

Jeśli używasz ADO.NET 4,0 lub starszej wersji, zalecamy przeprowadzenie uaktualnienia do najnowszej ADO.NET. Od sierpnia 2018 można [pobrać ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostyka

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostyka: testowanie, czy narzędzia mogą się łączyć

Jeśli program nie może nawiązać połączenia z bazą danych w SQL Database, jedną z opcji diagnostycznych jest próba nawiązania połączenia z programem narzędziowym. W idealnym przypadku narzędzie nawiązuje połączenie za pomocą tej samej biblioteki, która jest używana przez program.

Na dowolnym komputerze z systemem Windows można wypróbować następujące narzędzia:

- SQL Server Management Studio (ssms.exe), które nawiązuje połączenie za pomocą ADO.NET
- `sqlcmd.exe`, które nawiązuje połączenie za pomocą [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Po nawiązaniu połączenia z programem Sprawdź, czy jest działa krótkie zapytanie SELECT języka SQL.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostyka: Sprawdź otwarte porty

Jeśli podejrzewasz, że próba połączenia nie powiedzie się z powodu problemów z portem, możesz uruchomić na komputerze narzędzie, które zgłasza konfigurację portów.

W systemie Linux pomocne mogą być następujące narzędzia:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Zmień przykładową wartość na adres IP.

W systemie Windows narzędzie [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) może być przydatne. Oto przykładowe wykonanie, które zbadał sytuację portu w bazie danych w SQL Database i uruchomiono na komputerze przenośnym:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostyka: Rejestruj błędy

Sporadyczny problem jest czasami optymalnie zdiagnozowany przez wykrycie ogólnego wzorca w ciągu dni lub tygodni.

Klient może pomóc w diagnozowaniu, rejestrując wszystkie napotkane błędy. Możliwe jest skorelowanie wpisów dziennika z danymi o błędach, które zarejestrują się wewnętrznie SQL Database.

Biblioteka Enterprise Library 6 (EntLib60) oferuje klasy zarządzane przez platformę .NET, które pomagają w rejestrowaniu. Aby uzyskać więcej informacji, zobacz [5-tak prosty jak w przypadku przechowywania dziennika: Użyj bloku aplikacji rejestrowania](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostyka: Sprawdzenie dzienników systemowych pod kątem błędów

Poniżej przedstawiono niektóre instrukcje SELECT języka Transact-SQL, które wykonują zapytania dotyczące dzienników błędów i innych informacji.

| Zapytanie o dziennik | Opis |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Widok [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) zawiera informacje dotyczące poszczególnych zdarzeń, w tym niektóre, które mogą spowodować błędy przejściowe lub błędy łączności.<br/><br/>W idealnym przypadku można skorelować wartości **start_time** lub **end_time** z informacjami o tym, kiedy program kliencki napotkał problemy.<br/><br/>Musisz nawiązać połączenie z bazą danych *Master* , aby uruchomić to zapytanie. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Widok [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) oferuje zagregowane liczby typów zdarzeń do dodatkowej diagnostyki.<br/><br/>Musisz nawiązać połączenie z bazą danych *Master* , aby uruchomić to zapytanie. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostyka: wyszukiwanie zdarzeń problemów w dzienniku SQL Database

Wpisy dotyczące zdarzeń problemów można wyszukać w dzienniku SQL Database. Wypróbuj następującą instrukcję SELECT języka Transact-SQL w bazie danych *Master* :

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Kilka zwracanych wierszy z sys.fn_xe_telemetry_blob_target_read_file

Poniższy przykład pokazuje, jak może wyglądać zwrócony wiersz. Wyświetlane wartości null często nie mają wartości null w innych wierszach.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteka Enterprise — 6

Biblioteka Enterprise Library 6 (EntLib60) to struktura klas platformy .NET, która ułatwia wdrażanie niezawodnych klientów usług w chmurze, z których jeden jest SQL Database. Aby znaleźć tematy przeznaczone dla każdego obszaru, w którym EntLib60 może pomóc, zobacz [Biblioteka Enterprise 6-kwiecień 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logika ponowień do obsługi błędów przejściowych to jeden obszar, w którym EntLib60 może pomóc. Aby uzyskać więcej informacji, zobacz [4-Perseverance, klucz tajny wszystkich Triumphs: Użyj bloku aplikacji do obsługi błędów przejściowych](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Kod źródłowy EntLib60 jest dostępny do pobrania publicznie z [Centrum pobierania](https://go.microsoft.com/fwlink/p/?LinkID=290898). Firma Microsoft nie ma żadnych planów, aby podejmować dalsze aktualizacje funkcji lub aktualizacje konserwacji EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 klasy dla błędów przejściowych i ponów próbę

Następujące klasy EntLib60 są szczególnie przydatne w przypadku logiki ponawiania. Wszystkie te klasy są dostępne w lub w obszarze nazw **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling**.

W przestrzeni nazw **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling**:

- Klasa **RetryPolicy**
  - **ExecuteAction** , Metoda
- Klasa **ExponentialBackoff**
- Klasa **SqlDatabaseTransientErrorDetectionStrategy**
- Klasa **ReliableSqlConnection**
  - **ExecuteCommand** , Metoda

W przestrzeni nazw **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling. TestSupport**:

- Klasa **AlwaysTransientErrorDetectionStrategy**
- Klasa **NeverTransientErrorDetectionStrategy**

Poniżej znajdują się linki do informacji na temat EntLib60:

- Pobieranie bezpłatnej książki: [przewodnik dewelopera do biblioteki Enterprise firmy Microsoft, wersja 2](https://www.microsoft.com/download/details.aspx?id=41145).
- Najlepsze rozwiązania: [wskazówki dotyczące ponawiania prób ogólnych](/azure/architecture/best-practices/transient-faults) zawierają doskonałe Omówienie logiki ponawiania.
- Pobieranie plików NuGet: [Biblioteka przedsiębiorstwa — przejściowa obsługa błędów — blok aplikacji 6,0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: blok rejestrowania

- Blok rejestrowania to wysoce elastyczne i konfigurowalne rozwiązanie, za pomocą którego można:
  - Tworzenie i przechowywanie komunikatów dzienników w wielu różnych lokalizacjach.
  - Kategoryzowanie i filtrowanie komunikatów.
  - Zbieraj informacje kontekstowe, które są przydatne do debugowania i śledzenia, a także na potrzeby inspekcji i ogólnych wymagań dotyczących rejestrowania.
- Blok rejestrowania służy do wyodrębniania funkcji rejestrowania z miejsca docelowego dziennika, dzięki czemu kod aplikacji jest spójny, niezależnie od lokalizacji i typu docelowego magazynu rejestrowania.

Aby uzyskać więcej informacji, zobacz [5-tak prosty jak w przypadku przechowywania dziennika: Użyj bloku aplikacji rejestrowania](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Kod źródłowy metody EntLib60 isprzejściowej

Następnie z klasy **SqlDatabaseTransientErrorDetectionStrategy** jest kodem źródłowym C# dla metody **isprzejściowej** . Kod źródłowy wyjaśnia, które błędy zostały uznane za przejściowe i zaufanego ponowień, od kwietnia 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Następne kroki

- [Biblioteki połączeń dla SQL Database i SQL Server](connect-query-content-reference-guide.md#libraries)
- [Buforowanie połączeń (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [ *Ponawianie próby* to biblioteka Apache 2,0 z licencją ogólnego przeznaczenia, która została zastosowana w języku Python,](https://pypi.python.org/pypi/retrying) aby uprościć zadanie dodawania ponowień do wszystkiego.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
