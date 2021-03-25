---
title: Parametry serwera — Azure Database for MySQL — elastyczny serwer
description: Ten temat zawiera wskazówki dotyczące konfigurowania parametrów serwera w Azure Database for MySQL-elastycznym serwerze.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: f83f743b692ae5a625a4c881b12cbad999f1f606
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106772"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Parametry serwera w Azure Database for MySQL-elastycznym serwerze

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Ten artykuł zawiera zagadnienia i wskazówki dotyczące konfigurowania parametrów serwera w Azure Database for MySQL elastycznym serwerze.

## <a name="what-are-server-variables"></a>Co to są zmienne serwera? 

Aparat MySQL udostępnia wiele różnych [zmiennych/parametrów serwera](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) , których można użyć do konfigurowania i dostrajania zachowania aparatu. Niektóre parametry można ustawić dynamicznie w czasie wykonywania, podczas gdy inne są "statyczne", co wymaga ponownego uruchomienia serwera, aby można było go zastosować.

Azure Database for MySQL elastyczny serwer uwidacznia możliwość zmiany wartości różnych parametrów serwera MySQL przy użyciu [interfejsu wiersza polecenia](./how-to-configure-server-parameters-cli.md) [Azure Portal](./how-to-configure-server-parameters-portal.md) i platformy Azure w celu dopasowania do potrzeb związanych z obciążeniem.

## <a name="configurable-server-parameters"></a>Konfigurowalne parametry serwera

Za pomocą parametrów serwera można zarządzać Azure Database for MySQL elastyczną konfiguracją serwera. Parametry serwera są skonfigurowane z domyślną i zalecaną wartością podczas tworzenia serwera. Blok parametrów serwera na Azure Portal pokazuje zarówno parametry serwera, które są modyfikowane, jak i niemodyfikowalne. Niemodyfikowalne parametry serwera są wyszarzone.

Lista obsługiwanych parametrów serwera stale rośnie. Użyj karty parametry serwera w Azure Portal, aby wyświetlić pełną listę i skonfigurować wartości parametrów serwera.

Zapoznaj się z poniższymi sekcjami poniżej, aby dowiedzieć się więcej o limitach kilku często aktualizowanych parametrów serwera. Limity są określane przez warstwę obliczeniową i rozmiar (rdzeni wirtualnych) serwera.

> [!NOTE]
> Jeśli zamierzasz zmodyfikować parametr serwera, który nie jest modyfikowany, ale chcesz zobaczyć jako modyfikowalny dla danego środowiska, Otwórz element usługi [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) lub zagłosuj, jeśli opinia już istnieje, co może pomóc nam określić priorytety.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

W Azure Database for MySQL elastycznym serwerze dzienniki binarne są zawsze włączone (oznacza to, że `log_bin` jest ustawiony na wartość włączone). Jeśli chcesz użyć wyzwalaczy, zostanie wyświetlony komunikat o błędzie podobny do tego, że *nie masz uprawnień administratora, a rejestrowanie binarne jest włączone (możesz chcieć użyć mniej bezpiecznej `log_bin_trust_function_creators` zmiennej)*. 

Format rejestrowania binarnego to zawsze **wiersz** i wszystkie połączenia z serwerem **zawsze** używają rejestrowania binarnego opartego na wierszach. W przypadku rejestrowania binarnego opartego na wierszach problemy z zabezpieczeniami nie istnieją i rejestrowanie danych binarnych nie może zostać zerwane, więc można bezpiecznie ustawić [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) **wartość true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Zapoznaj się z [dokumentacją programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Rozmiar pamięci (GiB)**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|---|
|Do przelicznikania (B1s)|1|1|134217728|33554432|134217728|
|Do przelicznikania (B1ms)|1|2|536870912|134217728|536870912|
|Z możliwością przenoszenia|2|4|2147483648|134217728|2147483648|
|Ogólnego przeznaczenia|2|8|6442450944|134217728|6442450944|
|Ogólnego przeznaczenia|4|16|12884901888|134217728|12884901888|
|Ogólnego przeznaczenia|8|32|25769803776|134217728|25769803776|
|Ogólnego przeznaczenia|16|64|51539607552|134217728|51539607552|
|Ogólnego przeznaczenia|32|128|103079215104|134217728|103079215104|
|Ogólnego przeznaczenia|48|192|154618822656|134217728|154618822656|
|Ogólnego przeznaczenia|64|256|206158430208|134217728|206158430208|
|Optymalizacja pod kątem pamięci|2|16|12884901888|134217728|12884901888|
|Optymalizacja pod kątem pamięci|4|32|25769803776|134217728|25769803776|
|Optymalizacja pod kątem pamięci|8|64|51539607552|134217728|51539607552|
|Optymalizacja pod kątem pamięci|16|128|103079215104|134217728|103079215104|
|Optymalizacja pod kątem pamięci|32|256|206158430208|134217728|206158430208|
|Optymalizacja pod kątem pamięci|48|384|309237645312|134217728|309237645312|
|Optymalizacja pod kątem pamięci|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

Baza danych MySQL przechowuje tabelę InnoDB w różnych obszarach tabel w oparciu o konfigurację podaną podczas tworzenia tabeli. Przestrzeń dyskowa [systemu](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) jest obszarem magazynu dla słownika danych InnoDB. Obszar tabel dla [poszczególnych tabel](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) zawiera dane i indeksy dla pojedynczej tabeli InnoDB i jest przechowywany w systemie plików w osobnym pliku danych. Takie zachowanie jest kontrolowane przez `innodb_file_per_table` parametr serwer. Ustawienie `innodb_file_per_table` `OFF` powoduje, że InnoDB utworzyć tabele w obszarze obszary obszaru systemu. W przeciwnym razie InnoDB tworzy tabele w tabelach tabel w tabeli.

Azure Database for MySQL elastyczny serwer obsługuje maksymalnie **4 TB** w pojedynczym pliku danych. Jeśli rozmiar bazy danych jest większy niż 4 TB, należy utworzyć tabelę w [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) obszar tabel. Jeśli rozmiar pojedynczej tabeli jest większy niż 4 TB, należy użyć tabeli partycji.

### <a name="max_connections"></a>max_connections

Wartość max_connection jest określana na podstawie rozmiaru pamięci serwera. 

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Rozmiar pamięci (GiB)**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|---|
|Do przelicznikania (B1s)|1|1|85|10|171|
|Do przelicznikania (B1ms)|1|2|171|10|341|
|Z możliwością przenoszenia|2|4|341|10|683|
|Ogólnego przeznaczenia|2|8|683|10|1365|
|Ogólnego przeznaczenia|4|16|1365|10|2731|
|Ogólnego przeznaczenia|8|32|2731|10|5461|
|Ogólnego przeznaczenia|16|64|5461|10|10923|
|Ogólnego przeznaczenia|32|128|10923|10|21845|
|Ogólnego przeznaczenia|48|192|16384|10|32768|
|Ogólnego przeznaczenia|64|256|21845|10|43691|
|Optymalizacja pod kątem pamięci|2|16|1365|10|2731|
|Optymalizacja pod kątem pamięci|4|32|2731|10|5461|
|Optymalizacja pod kątem pamięci|8|64|5461|10|10923|
|Optymalizacja pod kątem pamięci|16|128|10923|10|21845|
|Optymalizacja pod kątem pamięci|32|256|21845|10|43691|
|Optymalizacja pod kątem pamięci|48|384|32768|10|65536|
|Optymalizacja pod kątem pamięci|64|504|43008|10|86016|

Gdy połączenia przekroczą limit, może zostać wyświetlony następujący błąd:
> BŁĄD 1040 (08004): zbyt wiele połączeń

> [!IMPORTANT]
> W celu uzyskania najlepszego środowiska zalecamy użycie połączenia pulę, takiego jak ProxySQL, aby efektywnie zarządzać połączeniami.

Tworzenie nowych połączeń klientów z usługą MySQL trwa po upływie czasu i po jego ustanowieniu te połączenia zajmują zasoby bazy danych, nawet jeśli są bezczynne. Większość aplikacji żąda wielu krótkich połączeń, które są związane z tą sytuacją. Wynikiem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pulę połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń, będzie pomóc w uniknięciu tego działania. Aby dowiedzieć się więcej o konfigurowaniu ProxySQL, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL to narzędzie społecznościowe Open Source. Jest ona obsługiwana przez firmę Microsoft w oparciu o najlepszy nakład pracy. Aby uzyskać pomoc techniczną z wiarygodnymi wskazówkami, możesz oszacować i skontaktować się z [pomocą techniczną produktu ProxySQL](https://proxysql.com/services/support/).

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Jeśli zostanie wyświetlony komunikat o błędzie podobny do "rozmiar wiersza jest zbyt duży (> 8126)", możesz chcieć wyłączyć **innodb_strict_mode** parametru. **Innodb_strict_mode** parametru serwera nie można modyfikować globalnie na poziomie serwera, ponieważ jeśli rozmiar danych wierszy jest większy niż 8k, dane zostaną obcięte bez błędu, co może prowadzić do potencjalnej utraty danych. Zalecamy modyfikację schematu w celu dopasowania go do limitu rozmiaru strony. 

Ten parametr można ustawić na poziomie sesji przy użyciu `init_connect` . Aby ustawić **innodb_strict_mode** na poziomie sesji, zapoznaj się z [parametrem ustawienia nie](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)ma na liście.

> [!NOTE]
> Jeśli masz serwer repliki odczytu, ustawienie **innodb_strict_mode** na wyłączone na poziomie sesji na serwerze źródłowym spowoduje przerwanie replikacji. Zaleca się pozostawienie parametru jako WYŁĄCZONEgo w przypadku odczytu replik.

### <a name="time_zone"></a>time_zone

Po wstępnym wdrożeniu serwer elastyczny platformy Azure dla MySQL zawiera tabele systemowe dla informacji o strefie czasowej, ale te tabele nie są wypełnione. Tabele strefy czasowej mogą być wypełniane przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Zapoznaj się z artykułami [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) lub [interfejsem wiersza polecenia platformy Azure](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) dotyczącymi sposobu wywoływania procedury składowanej i ustawiania stref czasowych na poziomie globalnym lub w sesji.

## <a name="non-modifiable-server-parameters"></a>Niemodyfikowalne parametry serwera

Blok parametrów serwera na Azure Portal pokazuje zarówno parametry serwera, które są modyfikowane, jak i niemodyfikowane. Niemodyfikowalne parametry serwera są wyszarzone. Jeśli chcesz skonfigurować niemodyfikowalny parametr serwera na poziomie sesji, zapoznaj się z artykułem [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) lub [interfejs wiersza polecenia platformy Azure](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) w celu ustawienia parametru na poziomie połączenia przy użyciu `init_connect` .

## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w Azure Portal](./how-to-configure-server-parameters-portal.md)
- Jak skonfigurować [parametry serwera w interfejsie wiersza polecenia platformy Azure](./how-to-configure-server-parameters-cli.md)
