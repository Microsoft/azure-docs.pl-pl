---
title: Rozwiązywanie problemów z usługą VM Insights
description: Rozwiązywanie problemów z instalacją usługi VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555655"
---
# <a name="troubleshoot-vm-insights"></a>Rozwiązywanie problemów z usługą VM Insights
Ten artykuł zawiera informacje dotyczące rozwiązywania problemów w przypadku problemów z włączaniem lub używaniem szczegółowych informacji o maszynach wirtualnych.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Nie można włączyć usługi VM Insights na maszynie
Podczas dołączania maszyny wirtualnej platformy Azure z Azure Portal należy wykonać następujące czynności:

- Jeśli wybrano tę opcję, tworzony jest domyślny obszar roboczy Log Analytics.
- Agent Log Analytics jest instalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej, jeśli Agent jest już zainstalowany.
- Agent zależności jest instalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia, o ile jest to wymagane.
  
Podczas procesu dołączania każdy z tych kroków zostanie zweryfikowany, a w portalu zostanie wyświetlony stan powiadomienia. Konfiguracja obszaru roboczego i Instalacja agenta zazwyczaj trwa od 5 do 10 minut. Udostępnienie danych do wyświetlenia w portalu zajmie od 5 do 10 minut.

Jeśli zostanie wyświetlony komunikat informujący o konieczności dołączenia maszyny wirtualnej po przeprowadzeniu procesu dołączania, poczekaj maksymalnie 30 minut na ukończenie procesu. Jeśli problem będzie się powtarzać, zobacz następujące sekcje w celu uzyskania możliwych przyczyn.

### <a name="is-the-virtual-machine-running"></a>Czy maszyna wirtualna jest uruchomiona?
 Jeśli maszyna wirtualna została wyłączona przez pewien czas, jest wyłączona obecnie lub niedawno włączona, nie będzie można wyświetlać danych dla bitu, dopóki dane nie zostaną odebrane.

### <a name="is-the-operating-system-supported"></a>Czy system operacyjny jest obsługiwany?
Jeśli system operacyjny nie znajduje się na liście [obsługiwanych systemów operacyjnych](vminsights-enable-overview.md#supported-operating-systems) , instalacja nie powiedzie się i zostanie wyświetlony komunikat informujący o tym, że czekamy na dostarczenie danych.

### <a name="did-the-extension-install-properly"></a>Czy rozszerzenie zostało poprawnie zainstalowane?
Jeśli nadal zobaczysz komunikat, że maszyna wirtualna musi zostać dołączona, może to oznaczać, że instalacja jednego lub obu rozszerzeń nie powiodła się. Sprawdź stronę **rozszerzenia** maszyny wirtualnej w Azure Portal, aby sprawdzić, czy wymienione są następujące rozszerzenia.

| System operacyjny | Agenci | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft. Azure. Monitoring. DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Jeśli na liście zainstalowanych rozszerzeń nie są widoczne oba rozszerzenia systemu operacyjnego, należy je zainstalować. Jeśli rozszerzenia są wyświetlane, ale ich stan nie jest wyświetlany po *pomyślnym zainicjowaniu obsługi administracyjnej*, należy usunąć rozszerzenie i zainstalować je ponownie.

### <a name="do-you-have-connectivity-issues"></a>Czy masz problemy z połączeniem?
W przypadku maszyn z systemem Windows można zidentyfikować problem z łącznością za pomocą narzędzia  *TestCloudConnectivity* . To narzędzie jest instalowane domyślnie z agentem w folderze *%systemroot%\Program Files\Microsoft monitoring Agent\Agent*. Uruchom narzędzie z wiersza polecenia z podwyższonym poziomem uprawnień. Zwróci wyniki i wyróżnienia, gdzie test zakończy się niepowodzeniem. 

![Narzędzie TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Więcej problemów z agentem

Zapoznaj się z następującymi artykułami dotyczącymi rozwiązywania problemów z agentem Log Analytics:

- [Jak rozwiązywać problemy z agentem usługi Log Analytics dla systemu Windows](../agents/agent-windows-troubleshoot.md)
- [Jak rozwiązywać problemy z agentem usługi Log Analytics dla systemu Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>Widok wydajności nie zawiera żadnych danych
Jeśli agenci są prawidłowo instalowani, ale nie ma żadnych danych w widoku wydajności, zobacz następujące sekcje, aby poznać możliwe przyczyny.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Czy obszar roboczy Log Analytics osiągnął limit danych?
Sprawdź [rezerwacje pojemności i Cennik](https://azure.microsoft.com/pricing/details/monitor/)pozyskiwania danych.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Czy maszyna wirtualna wysyła dane dziennika i wydajności do dzienników Azure Monitor?

Otwórz Log Analytics z **dzienników** w menu Azure Monitor w Azure Portal. Uruchom następujące zapytanie dla komputera:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Jeśli nie widzisz żadnych danych, mogą wystąpić problemy z agentem. Zapoznaj się z sekcją powyżej dotyczącej rozwiązywania problemów z agentem.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>Maszyna wirtualna nie jest wyświetlana w widoku mapy

### <a name="is-the-dependency-agent-installed"></a>Czy Agent zależności jest zainstalowany?
 Skorzystaj z informacji podanych w powyższych sekcjach, aby ustalić, czy Agent zależności został zainstalowany i działa prawidłowo.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Czy jesteś w Log Analytics warstwy Bezpłatna?
[Warstwa Bezpłatna w log Analytics](https://azure.microsoft.com/pricing/details/monitor/) jest to starszy plan cenowy, który umożliwia maksymalnie pięć unikatowych maszyn Service map. Wszystkie kolejne maszyny nie będą wyświetlane w usłudze Service Map, nawet jeśli poprzednie pięć nie wysyła już danych.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Czy maszyna wirtualna wysyła dane dziennika i wydajności do dzienników Azure Monitor?
Aby określić, czy dane są zbierane dla maszyny wirtualnej, należy użyć zapytania dziennika w [widoku wydajności](#performance-view-has-no-data) . Jeśli nie zbierasz danych, użyj narzędzia TestCloudConnectivity opisanego powyżej, aby określić, czy masz problemy z łącznością.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>Maszyna wirtualna jest wyświetlana w widoku mapy, ale zawiera brakujące dane
Jeśli maszyna wirtualna znajduje się w widoku mapy, Agent zależności jest zainstalowany i uruchomiony, ale nie załadowano sterownika jądra. Sprawdź plik dziennika w następujących lokalizacjach:

| System operacyjny | Dziennik | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

W ostatnich wierszach pliku powinna znajdować się odpowiedź, dlaczego nie załadowano jądra. Na przykład jądro może nie być obsługiwane w systemie Linux, jeśli zostało zaktualizowane.
## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat dołączania agentów usługi VM Insights, zobacz [Włączanie usługi VM Insights Overview](vminsights-enable-overview.md).
