---
title: Analiza dzienników diagnostycznych Windows Virtual Desktop (Classic) — Azure
description: Jak używać usługi log Analytics z funkcją diagnostyki pulpitu wirtualnego systemu Windows (klasycznego).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bfe8026f099c341b68a1aa1fc5e3fac425e29403
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578902"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Używanie Log Analytics dla funkcji diagnostyki na pulpicie wirtualnym systemu Windows (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../diagnostics-log-analytics.md).

Pulpit wirtualny systemu Windows oferuje funkcję diagnostyki, która umożliwia administratorowi identyfikowanie problemów za pomocą jednego interfejsu. Ta funkcja rejestruje informacje diagnostyczne za każdym razem, gdy ktoś przypisany do roli pulpitu wirtualnego systemu Windows używa usługi. Każdy dziennik zawiera informacje o tym, które role pulpitu wirtualnego systemu Windows dotyczyły działania, wszystkich komunikatów o błędach, które pojawiają się podczas sesji, informacje o dzierżawie i informacje o użytkowniku. Funkcja diagnostyki tworzy dzienniki aktywności zarówno dla użytkownika, jak i akcji administracyjnych. Każdy dziennik aktywności znajduje się poniżej trzech głównych kategorii:

- Działania subskrypcji kanału informacyjnego: gdy użytkownik próbuje nawiązać połączenie ze źródłem danych za pomocą aplikacji Pulpit zdalny Microsoft.
- Działania połączenia: Kiedy użytkownik próbuje nawiązać połączenie z pulpitem lub funkcją RemoteApp za pomocą aplikacji Pulpit zdalny Microsoft.
- Działania związane z zarządzaniem: w przypadku wykonywania przez administratora operacji zarządzania w systemie, takich jak tworzenie pul hostów, przypisywanie użytkowników do grup aplikacji i tworzenie przypisań ról.

Połączenia, które nie docierają do pulpitu wirtualnego systemu Windows, nie będą wyświetlane w wynikach diagnostyki, ponieważ sama usługa roli diagnostyki jest częścią pulpitu wirtualnego systemu Windows. Problemy z połączeniem pulpitu wirtualnego systemu Windows mogą wystąpić, gdy użytkownik napotyka problemy z łącznością sieciową.

## <a name="why-you-should-use-log-analytics"></a>Dlaczego należy używać Log Analytics

Zalecamy używanie Log Analytics do analizowania danych diagnostycznych w kliencie platformy Azure, które wykraczają poza Rozwiązywanie problemów z pojedynczym użytkownikiem. Jak można ściągnąć liczniki wydajności maszyn wirtualnych do Log Analytics masz jedno narzędzie do zbierania informacji dla wdrożenia.

## <a name="before-you-get-started"></a>Przed rozpoczęciem

Aby można było używać Log Analytics z funkcją diagnostyki, należy [utworzyć obszar roboczy](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace).

Po utworzeniu obszaru roboczego postępuj zgodnie z instrukcjami w temacie [łączenie komputerów z systemem Windows, aby Azure monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) uzyskać następujące informacje:

- Identyfikator obszaru roboczego
- Klucz podstawowy obszaru roboczego

Te informacje będą potrzebne później w procesie instalacji.

## <a name="push-diagnostics-data-to-your-workspace"></a>Wypychanie danych diagnostycznych do obszaru roboczego

Możesz wypchnąć dane diagnostyczne z dzierżawy pulpitu wirtualnego systemu Windows do Log Analytics obszaru roboczego. Tę funkcję można skonfigurować od razu w momencie utworzenia dzierżawy przez połączenie obszaru roboczego z dzierżawcą lub można skonfigurować go później przy użyciu istniejącej dzierżawy.

Aby połączyć dzierżawcę z obszarem roboczym Log Analytics podczas konfigurowania nowej dzierżawy, uruchom następujące polecenie cmdlet, aby zalogować się do pulpitu wirtualnego systemu Windows przy użyciu konta użytkownika TenantCreator:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Jeśli chcesz połączyć istniejącą dzierżawę zamiast nowej dzierżawy, Uruchom to polecenie cmdlet w zamian:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Należy uruchomić te polecenia cmdlet dla każdej dzierżawy, do której chcesz połączyć Log Analytics.

>[!NOTE]
>Jeśli nie chcesz połączyć obszaru roboczego Log Analytics podczas tworzenia dzierżawy, uruchom `New-RdsTenant` polecenie cmdlet zamiast tego.

## <a name="cadence-for-sending-diagnostic-events"></a>Erze do wysyłania zdarzeń diagnostycznych

Zdarzenia diagnostyczne są wysyłane do Log Analytics po zakończeniu.

## <a name="example-queries"></a>Przykładowe zapytania

Poniższe przykładowe zapytania pokazują, jak funkcja diagnostyki generuje raport dla najbardziej częstych działań w systemie:

W pierwszym przykładzie przedstawiono działania połączenia zainicjowane przez użytkowników z obsługiwanymi klientami pulpitu zdalnego:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

W następnym przykładzie zapytanie ukazuje działania związane z zarządzaniem według administratorów w dzierżawcach:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Zatrzymaj wysyłanie danych do Log Analytics

Aby zatrzymać wysyłanie danych z istniejącej dzierżawy do Log Analytics, uruchom następujące polecenie cmdlet i ustaw puste ciągi:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Należy uruchomić to polecenie cmdlet dla każdej dzierżawy, z której ma zostać zatrzymane wysyłanie danych.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z typowymi scenariuszami błędów, które mogą identyfikować funkcja diagnostyki, zobacz [Identyfikowanie i diagnozowanie problemów](diagnostics-role-service-2019.md#common-error-scenarios).
