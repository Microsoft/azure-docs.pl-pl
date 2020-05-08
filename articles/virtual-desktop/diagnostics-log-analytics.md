---
title: Analiza dzienników diagnostyki usług pulpitu wirtualnego systemu Windows — Azure
description: Jak używać usługi log Analytics z funkcją diagnostyki pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a9e68a2e0c39364cc5105f230b00ffb90d867d
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888791"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Użyj Log Analytics dla funkcji diagnostyki

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pulpit wirtualny systemu Windows używa [Azure monitor](../azure-monitor/overview.md) do monitorowania i alertów, takich jak wiele innych usług platformy Azure. Dzięki temu administratorzy mogą identyfikować problemy za pomocą jednego interfejsu. Usługa tworzy dzienniki aktywności zarówno dla użytkownika, jak i akcji administracyjnych. Każdy dziennik aktywności znajduje się w następujących kategoriach:  

- Działania związane z zarządzaniem:
    - Należy sprawdzić, czy próby zmiany obiektów pulpitu wirtualnego systemu Windows przy użyciu interfejsów API lub programu PowerShell zostały wykonane pomyślnie. Czy na przykład ktoś może pomyślnie utworzyć pulę hostów przy użyciu programu PowerShell?
- Kanału informacyjnego 
    - Czy użytkownicy mogą pomyślnie subskrybować obszary robocze? 
    - Czy użytkownicy widzą wszystkie zasoby opublikowane w kliencie Pulpit zdalny?
- Połączenia: 
    - Gdy użytkownicy inicjują i ukończą połączenia z usługą. 
- Rejestracja hosta: 
    - Czy Host sesji został pomyślnie zarejestrowany w usłudze podczas łączenia?
- Błędy 
    - Czy użytkownicy napotykają jakiekolwiek problemy związane z określonymi działaniami? Ta funkcja może generować tabelę, która śledzi dane dotyczące aktywności, o ile informacje są łączone z działaniami.
- Punkty kontrolne  
    - Określone kroki w okresie istnienia działania, które zostały osiągnięte. Na przykład w trakcie sesji użytkownik był obciążany obciążeniem określonego hosta, wówczas użytkownik był zalogowany podczas połączenia i tak dalej.

Połączenia, które nie docierają do pulpitu wirtualnego systemu Windows, nie będą wyświetlane w wynikach diagnostyki, ponieważ sama usługa roli diagnostyki jest częścią pulpitu wirtualnego systemu Windows. Problemy z połączeniem pulpitu wirtualnego systemu Windows mogą wystąpić, gdy użytkownik napotyka problemy z łącznością sieciową.

Azure Monitor umożliwia analizowanie danych pulpitu wirtualnego systemu Windows i przeglądanie liczników wydajności maszyn wirtualnych (VM) w ramach tego samego narzędzia. W tym artykule przedstawiono więcej informacji na temat włączania diagnostyki środowiska pulpitu wirtualnego systemu Windows. 

>[!NOTE] 
>Aby dowiedzieć się, jak monitorować maszyny wirtualne na platformie Azure, zobacz [monitorowanie maszyn wirtualnych platformy Azure przy użyciu Azure monitor](../azure-monitor/insights/monitor-vm-azure.md). Należy również sprawdzić [progi licznika wydajności](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) , aby lepiej zrozumieć środowisko użytkownika na hoście sesji.

## <a name="before-you-get-started"></a>Przed rozpoczęciem

Aby móc używać Log Analytics, musisz utworzyć obszar roboczy. Aby to zrobić, postępuj zgodnie z instrukcjami w jednym z następujących dwóch artykułów:

- Jeśli wolisz używać Azure Portal, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Jeśli wolisz program PowerShell, zobacz [Tworzenie obszaru roboczego log Analytics przy użyciu programu PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

Po utworzeniu obszaru roboczego postępuj zgodnie z instrukcjami w temacie [łączenie komputerów z systemem Windows, aby Azure monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) uzyskać następujące informacje:

- Identyfikator obszaru roboczego
- Klucz podstawowy obszaru roboczego

Te informacje będą potrzebne później w procesie instalacji.

Upewnij się, że zapoznaj się z tematem Zarządzanie uprawnieniami Azure Monitor, aby umożliwić dostęp do danych dla tych, którzy monitorują i utrzymują środowisko pulpitu wirtualnego systemu Windows. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/platform/roles-permissions-security.md). 

## <a name="push-diagnostics-data-to-your-workspace"></a>Wypychanie danych diagnostycznych do obszaru roboczego

Możesz wypchnąć dane diagnostyczne z obiektów pulpitu wirtualnego systemu Windows do Log Analytics obszaru roboczego. Tę funkcję można skonfigurować od razu po pierwszym utworzeniu obiektów.

Aby skonfigurować Log Analytics dla nowego obiektu:

1. Zaloguj się do Azure Portal i przejdź do **pulpitu wirtualnego systemu Windows**. 

2. Przejdź do obiektu (na przykład puli hostów, grupy aplikacji lub obszaru roboczego), dla którego chcesz przechwytywać dzienniki i zdarzenia. 

3. Wybierz pozycję **Ustawienia diagnostyczne** w menu po lewej stronie ekranu. 

4. Wybierz pozycję **Dodaj ustawienia diagnostyczne** w menu, które pojawia się po prawej stronie ekranu. 
   
    Opcje wyświetlane na stronie Ustawienia diagnostyczne różnią się w zależności od rodzaju edytowanego obiektu.

    Na przykład po włączeniu diagnostyki dla grupy aplikacji widoczne są opcje konfigurowania punktów kontrolnych, błędów i zarządzania. W przypadku obszarów roboczych te kategorie umożliwiają skonfigurowanie kanału informacyjnego do śledzenia, gdy użytkownicy subskrybują listę aplikacji. Aby dowiedzieć się więcej na temat ustawień diagnostycznych, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników zasobów i metryk na platformie Azure](../azure-monitor/platform/diagnostic-settings.md). 

     >[!IMPORTANT] 
     >Należy pamiętać o włączeniu diagnostyki dla każdego obiektu Azure Resource Manager, który ma być monitorowany. Dane będą dostępne dla działań po włączeniu diagnostyki. Po pierwszej konfiguracji może upłynąć kilka godzin.  

5. Wprowadź nazwę konfiguracji ustawień, a następnie wybierz pozycję **Wyślij do log Analytics**. Użyta nazwa nie powinna zawierać spacji i powinna być zgodna z [konwencjami nazewnictwa platformy Azure](../azure-resource-manager/management/resource-name-rules.md). W ramach dzienników można wybrać wszystkie opcje, które mają zostać dodane do Log Analytics, takie jak punkty kontrolne, błąd, zarządzanie i tak dalej.

6. Wybierz pozycję **Zapisz**.

>[!NOTE]
>Log Analytics udostępnia opcję przesyłania strumieniowego danych do [Event Hubs](../event-hubs/event-hubs-about.md) lub archiwizowania jej na koncie magazynu. Aby dowiedzieć się więcej na temat tej funkcji, zobacz [przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) i [Archiwizowanie dzienników zasobów platformy Azure na koncie magazynu](../azure-monitor/platform/resource-logs-collect-storage.md). 

## <a name="how-to-access-log-analytics"></a>Jak uzyskać dostęp do Log Analytics

Możesz uzyskać dostęp do Log Analytics obszarów roboczych na Azure Portal lub Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics dostępu do Log Analytics obszaru roboczego

1. Zaloguj się do Portalu Azure.

2. Wyszukaj **log Analytics obszar roboczy**. 

3. W obszarze usługi wybierz pozycję **log Analytics obszary robocze**. 
   
4. Z listy wybierz obszar roboczy, który został skonfigurowany dla obiektu pulpitu wirtualnego systemu Windows.

5. W obszarze roboczym wybierz pozycję **dzienniki**. Możesz odfiltrować listę menu za pomocą funkcji **wyszukiwania** . 

### <a name="access-log-analytics-on-azure-monitor"></a>Log Analytics dostępu Azure Monitor

1. Logowanie do witryny Azure Portal

2. Wyszukaj i wybierz pozycję **monitor**. 

3. Wybierz pozycję **dzienniki**.

4. Postępuj zgodnie z instrukcjami na stronie Rejestrowanie, aby ustawić zakres zapytania.  

5. Możesz przystąpić do wykonywania zapytań diagnostycznych. Wszystkie tabele diagnostyki mają prefiks "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Erze do wysyłania zdarzeń diagnostycznych

Zdarzenia diagnostyczne są wysyłane do Log Analytics po zakończeniu.

Log Analytics tylko raporty w tych Stanach pośrednich dla działań związanych z połączeniami:

- Rozpoczęto
- Połączone
- Zakończone

## <a name="example-queries"></a>Przykładowe zapytania

Poniższe przykładowe zapytania pokazują, jak funkcja diagnostyki generuje raport dla najbardziej częstych działań w systemie.

Aby uzyskać listę połączeń wykonanych przez użytkowników, uruchom następujące polecenie cmdlet:

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

Aby wyświetlić źródło danych dla użytkowników:

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

Aby znaleźć wszystkie połączenia dla pojedynczego użytkownika: 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

Aby sprawdzić liczbę przypadków, w których użytkownik nawiązał połączenie dziennie:

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

Aby wyszukać czas trwania sesji według użytkownika:

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

Aby znaleźć błędy dla określonego użytkownika:

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

Aby dowiedzieć się, czy wystąpił konkretny błąd:

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Aby znaleźć wystąpienie błędu dla wszystkich użytkowników:

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>Najważniejszym tabelą dotyczącą rozwiązywania problemów jest WVDErrors. Użyj tego zapytania, aby zrozumieć, które problemy występują dla działań użytkownika, takich jak połączenia lub źródła danych, gdy użytkownik subskrybuje listę aplikacji lub pulpitów. W tabeli będą widoczne błędy zarządzania, a także problemy z rejestracją hosta.
>
>W publicznej wersji zapoznawczej, jeśli potrzebujesz pomocy przy rozwiązywaniu problemu, upewnij się, że przekazujesz identyfikator korelacji dla błędu w żądaniu pomocy. Upewnij się również, że wartość błędu usługi zawsze mówi serviceerror = "false". Wartość "false" oznacza, że problem może zostać rozwiązany przez zadanie administracyjne na końcu. Jeśli serviceerror = "true", musisz eskalować problem do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki 

Aby zapoznać się z typowymi scenariuszami błędów, które mogą identyfikować funkcja diagnostyki, zobacz [Identyfikowanie i diagnozowanie problemów](diagnostics-role-service.md#common-error-scenarios).
