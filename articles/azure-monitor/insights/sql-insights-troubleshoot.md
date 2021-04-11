---
title: Rozwiązywanie problemów z usługą SQL Insights (wersja zapoznawcza)
description: Rozwiązywanie problemów z usługą SQL Insights w Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 4d4a801d0cf0a2355334272053ff86dd846b6bbf
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030308"
---
# <a name="troubleshooting-sql-insights-preview"></a>Rozwiązywanie problemów z usługą SQL Insights (wersja zapoznawcza)
Aby rozwiązać problemy z zbieraniem danych w usłudze SQL Insights, sprawdź stan maszyny monitorowania na karcie **Zarządzanie profilem** . Będzie to miało jeden z następujących stanów:

- Zbiorczej 
- Nie zebrano 
- Zbieranie z błędami 
 
Kliknij **stan** , aby przejść do szczegółów, aby wyświetlić dzienniki i szczegółowe informacje, co może pomóc w rozwiązaniu problemu. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Stan maszyny monitorowania":::

## <a name="not-collecting-state"></a>Niezbieranie stanu 
Maszyna monitorowania ma stan *nie zbiera* się, jeśli w ciągu ostatnich 10 minut nie ma danych w programie *InsightsMetrics* for SQL. 

Usługi SQL Insights wykorzystują następujące zapytanie, aby pobrać te informacje:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Sprawdź, czy istnieją jakieś dzienniki z telegraf, które ułatwiają zidentyfikowanie przyczyny problemu. W przypadku wpisów dziennika można kliknąć pozycję *nie Zbieraj* i sprawdzić dzienniki i informacje dotyczące rozwiązywania problemów, aby uzyskać typowe problemy. 


Jeśli nie ma żadnych dzienników, należy sprawdzić dzienniki na maszynie wirtualnej monitorowania dla następujących usług zainstalowanych przez dwa rozszerzenia maszyny wirtualnej:

- Microsoft. Azure. Monitor. AzureMonitorLinuxAgent 
  - Usługa: procesem MDSD 
- Microsoft. Azure. Monitor. obciążenia. obciążenie. WLILinuxExtension 
  - Usługa: WLI 
  - Usługa: MS-telegraf 
  - Usługa: TD-Agent-bit-WLI 
  - Dziennik rozszerzeń w celu sprawdzenia błędów instalacji:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>Dzienniki usługi WLI 

Dzienniki usługi: `/var/log/wli.log`

Aby wyświetlić ostatnie dzienniki: `tail -n 100 -f /var/log/wli.log`
 

Jeśli zobaczysz następujący dziennik błędów, wskazuje on problem z usługą **procesem MDSD** .

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Dzienniki usługi telegraf 

Dzienniki usługi: `/var/log/ms-telegraf/telegraf.log`

Aby wyświetlić ostatnie dzienniki: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` Aby zobaczyć ostatnie dzienniki błędów i ostrzeżeń: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Konfiguracja używana przez telegraf jest generowana przez usługę WLI i umieszczona w: `/etc/ms-telegraf/telegraf.d/wli`
 
W przypadku wygenerowania nieprawidłowej konfiguracji usługa MS-telegraf może się nie powieść, sprawdź, czy usługa MS-telegraf działa z poleceniem: `service ms-telegraf status`

Aby wyświetlić komunikaty o błędach z usługi telegraf, uruchom ją ręcznie przy użyciu następującego polecenia: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>Dzienniki usługi procesem MDSD 

Sprawdź [bieżące ograniczenia](../agents/azure-monitor-agent-overview.md#current-limitations) dotyczące agenta Azure monitor. 


Dzienniki usługi:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Aby zobaczyć ostatnie błędy: `tail -n 100 -f /var/log/mdsd.err`

 Jeśli musisz skontaktować się z pomocą techniczną, Zbierz następujące informacje: 

- Logowanie `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Zaloguj się `/var/log/waagent.log` 
- Logowanie `/var/log/mdsd*`
- Pliki w `/etc/mdsd.d/`
- Rozszerzeniem `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Nieprawidłowa monitorowanie konfiguracji maszyny wirtualnej

Jedną z przyczyn *niezbierania* stanu jest nieprawidłową konfigurację maszyny wirtualnej monitorowania.  Następująca konfiguracja jest domyślna:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Ta konfiguracja określa tokeny wymiany, które mają być używane w konfiguracji profilu na maszynie wirtualnej monitorowania. Umożliwia także odwoływanie się do wpisów tajnych z Azure Key Vault, dzięki czemu nie masz żadnych wartości tajnych w żadnej konfiguracji, co jest zdecydowanie zalecane.

#### <a name="secrets"></a>Wpisy tajne
Wpisy tajne to tokeny, których wartości są pobierane w czasie wykonywania z Azure Key Vault. Wpis tajny jest definiowany przez parę Key Vault odwołania i nazwy wpisu tajnego. Pozwala to Azure Monitor uzyskać wartość dynamiczną wpisu tajnego i użyć jej do konfiguracji podrzędnych.

W konfiguracji można zdefiniować dowolną liczbę wpisów tajnych, w tym wpisy tajne przechowywane w osobnych magazynach kluczy.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Uprawnienia dostępu do Key Vault są udostępniane tożsamość usługi zarządzanej na maszynie wirtualnej monitorowania. Azure Monitor oczekuje, że Key Vault dostarczyć co najmniej wpisów tajnych do maszyny wirtualnej. Można ją włączyć z szablonu Azure Portal, programu PowerShell, interfejsu wiersza polecenia lub Menedżer zasobów.

#### <a name="parameters"></a>Parametry
Parametry to tokeny, do których można odwoływać się w konfiguracji profilu za pomocą tworzenia szablonów JSON. Parametry mają nazwę i wartość. Wartości mogą być dowolnego typu JSON, w tym obiektów i tablic. W konfiguracji profilu występuje odwołanie do parametru, którego nazwa jest używana w tej Konwencji `.Parameters.<name>` .

Parametry mogą odwoływać się do wpisów tajnych w Key Vault przy użyciu tej samej Konwencji. Na przykład `sqlAzureConnections` odwołuje się do wpisu tajnego `telegrafPassword` przy użyciu konwencji `$telegrafPassword` .

W czasie wykonywania wszystkie parametry i wpisy tajne zostaną rozwiązane i scalone z konfiguracją profilu, aby utworzyć rzeczywistą konfigurację do użycia na komputerze.

> [!NOTE]
> `sqlAzureConnections` `sqlVmConnections` W konfiguracji są wymagane nazwy parametrów, i, `sqlManagedInstanceConnections` nawet jeśli nie masz parametrów połączenia, które będą udostępniane dla niektórych z nich.


## <a name="collecting-with-errors-state"></a>Zbieranie z powodu błędu stanu
Komputer monitorujący będzie w stanie *zbierać z błędami* , jeśli istnieje co najmniej jeden dziennik *InsightsMetrics* , ale w tabeli *operacji* występują także błędy.

Usługi SQL Insights wykorzystują następujące zapytania, aby pobrać te informacje:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> Jeśli nie widzisz żadnych danych w typie danych "WorkloadDiagnosticLogs", może być konieczne zaktualizowanie profilu monitorowania w celu zapisania tych danych.  W środowisku użytkownika usługi SQL Insights wybierz pozycję "Zarządzaj profilem", a następnie wybierz pozycję "Edytuj profil", a następnie wybierz pozycję "Aktualizuj profil monitorowania".


W przypadku typowych przypadków informacje o rozwiązywaniu problemów znajdują się w naszym widoku dzienników: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Rozwiązywanie problemów z widokiem dzienników.":::



## <a name="next-steps"></a>Następne kroki

- Uzyskaj szczegółowe informacje na temat [włączania usługi SQL Insights](sql-insights-enable.md).
