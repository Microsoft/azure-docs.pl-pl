---
title: Moduł AADCloudSyncTools programu PowerShell na potrzeby synchronizacji z chmurą Azure AD Connect
description: W tym artykule opisano sposób instalowania agenta aprowizacji Azure AD Connect Cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3ce5a7a313ab0f0680558aa60b34e3ebb9b51c9
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255156"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Moduł AADCloudSyncTools programu PowerShell na potrzeby synchronizacji z chmurą Azure AD Connect

Wydanie publicznej wersji zapoznawczej 2, firma Microsoft wprowadziła moduł AADCloudSyncTools PowerShell.  Ten moduł udostępnia zestaw przydatnych narzędzi, których można użyć, aby pomóc w zarządzaniu Azure AD Connect wdrożeniami w chmurze.

## <a name="pre-requisites"></a>Wymagania wstępne
Wymagane są następujące wymagania wstępne:
- Ten moduł używa uwierzytelniania MSAL, więc wymaga zainstalowanego modułu MSAL.PS. Nie jest już ona zależała od usługi Azure AD lub wersji zapoznawczej usługi Azure AD.   Aby sprawdzić, w oknie administracyjnym programu PowerShell wykonaj polecenie `Get-module MSAL.PS` . Po poprawnym zainstalowaniu modułu otrzymasz odpowiedź.  Za pomocą `Install-AADCloudSyncToolsPrerequisites` programu można zainstalować najnowszą wersję programu MSAL.PS
- Moduł AzureAD programu PowerShell.  Niektóre polecenia cmdlet są zależne od fragmentów modułu AzureAD PowerShell do wykonywania ich zadań.  Aby sprawdzić, w oknie administracyjnym programu PowerShell wykonaj polecenie `Get-module AzureAD` . Należy uzyskać odpowiedź.  Za pomocą `Install-AADCloudSyncToolsPrerequisites` programu można zainstalować najnowszą wersję modułu AzureAD PowerShell.
- Instalowanie modułów z programu PowerShell może wymuszać użycie protokołu TLS 1,2.  Aby upewnić się, że można zainstalować moduły, ustaw następujące: \
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>Instalowanie modułu AADCloudSyncTools PowerShell
Aby zainstalować moduł AADCloudSyncTools i korzystać z niego, wykonaj następujące czynności:

1.  Otwórz program Windows PowerShell z uprawnieniami administracyjnymi
2.  Wpisz `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` i naciśnij klawisz ENTER.
3.  Wpisz lub skopiuj i wklej następujące elementy: 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  Naciśnij klawisz ENTER.
4.  Aby sprawdzić, czy moduł został zainstalowany, wprowadź lub skopiuj i wklej następujący tekst "
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  Powinny teraz być widoczne informacje o module.
6.  Następne uruchomienie
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  Spowoduje to zainstalowanie modułów Get programu PowerShell.  Zamknij okno programu PowerShell.
8.  Otwórz program Windows PowerShell z uprawnieniami administracyjnymi
9.  Ponownie zaimportuj moduł przy użyciu kroku 3.
10. Uruchom, `Install-AADCloudSyncToolsPrerequisites` Aby zainstalować moduły MSAL i AzureAD
11. Wszystkie wstępnie dotyczących powinny pomyślnie zainstalować ![ moduł instalacji](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>Polecenia cmdlet AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Używa modułu AzureAD do nawiązywania połączenia z usługą Azure AD i modułem MSAL.PS w celu zażądania tokenu dla Microsoft Graph


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Eksportuje i pakuje wszystkie dane rozwiązywania problemów w skompresowanym pliku w następujący sposób:
 1. Uruchamia pełne śledzenie przy użyciu elementu Start-AADCloudSyncToolsVerboseLogs.  Te dzienniki śledzenia można znaleźć w folderze C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Zbiera dziennik śledzenia przez 3 minuty.
   Można określić inny czas od-TracingDurationMins lub pominąć śledzenie pełne z opcją-SkipVerboseTrace
 3. Przerywa śledzenie pełne przy użyciu Stop-AADCloudSyncToolsVerboseLogs
 4. Zbiera dzienniki Podgląd zdarzeń w ciągu ostatnich 24 godzin
 5. Kompresuje wszystkie dzienniki agentów, pełne dzienniki i dzienniki podglądu zdarzeń w skompresowanym pliku zip w folderze dokumenty użytkownika. 
 </br>Możesz określić inny folder wyjściowy z-OutputPath \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Pokazuje szczegóły dzierżawy usługi Azure AD i stan zmiennych wewnętrznych

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Używa programu Graph do uzyskiwania jednostek usługi AD2AAD i zwraca informacje o zadaniach synchronizacji.
Może być również wywoływana przy użyciu określonego identyfikatora zadania synchronizacji jako parametru.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Używa programu Graph do uzyskiwania jednostek usługi AD2AAD i zwraca harmonogram zadania synchronizacji.
Może być również wywoływana przy użyciu określonego identyfikatora zadania synchronizacji jako parametru.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Używa grafu, aby pobrać jednostki usługi AD2AAD i zwraca schemat zadania synchronizacji.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Używa grafu do pobrania schematu zadania synchronizacji dla podanego identyfikatora zadania synchronizacji i wyprowadza wszystkie zakresy grupy filtrów.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Używa grafu, aby pobrać jednostki usługi AD2AAD i zwróci ustawienia zadania synchronizacji.
Może być również wywoływana przy użyciu określonego identyfikatora zadania synchronizacji jako parametru.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Używa programu Graph do uzyskiwania jednostek usługi AD2AAD i zwraca stan zadania synchronizacji.
Może być również wywoływana przy użyciu określonego identyfikatora zadania synchronizacji jako parametru.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Używa programu Graph do uzyskiwania nazw głównych usług dla AD2AAD i/lub SyncFabric.
Bez parametrów program zwróci tylko jednostki usługi AD2AAD.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Sprawdza obecność PowerShellGet v 2.2.4.1 lub nowszych oraz modułów usługi Azure AD i MSAL.PS oraz instaluje je, jeśli nie zostały one spełnione.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Wywołuje żądanie sieci Web dla identyfikatora URI, metody i treści określonej jako parametry

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Za pomocą programu Azure AD PowerShell Usuń bieżące konto (jeśli istnieje) i zresetuj uwierzytelnianie konta synchronizacji przy użyciu nowego konta synchronizacji w usłudze Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Uruchamia pełną synchronizację.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Kontynuuje synchronizację z poprzedniego znaku wodnego.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Modyfikuje "AADConnectProvisioningAgent.exe.config", aby włączyć pełne śledzenie i ponownie uruchomić usługę AADConnectProvisioningAgent, można użyć-SkipServiceRestart, aby zapobiec ponownemu uruchomieniu usługi, ale zmiany konfiguracji nie zaczną obowiązywać.  Te dzienniki śledzenia można znaleźć w folderze C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Modyfikuje "AADConnectProvisioningAgent.exe.config", aby wyłączyć pełne śledzenie i ponownie uruchamia usługę AADConnectProvisioningAgent. Możesz użyć-SkipServiceRestart, aby zapobiec ponownemu uruchomieniu usługi, ale zmiany konfiguracji nie zaczną obowiązywać.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Wstrzymuje synchronizację.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)

