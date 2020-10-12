---
title: 'Azure AD Connect Sync: Scheduler | Microsoft Docs'
description: W tym temacie opisano wbudowaną funkcję harmonogramu w programie Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084740"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizacja programu Azure AD Connect: Harmonogram
W tym temacie opisano wbudowany harmonogram w programie Azure AD Connect Sync (aparat synchronizacji).

Ta funkcja została wprowadzona w programie build 1.1.105.0 (wydano luty 2016).

## <a name="overview"></a>Omówienie
Synchronizacja Azure AD Connect zsynchronizuj zmiany w katalogu lokalnym przy użyciu harmonogramu. Istnieją dwa procesy harmonogramu — jeden do synchronizacji haseł i drugi do zadań synchronizacji obiektów/atrybutów i obsługi. Ten temat obejmuje te ostatnie.

We wcześniejszych wersjach harmonogram dla obiektów i atrybutów był zewnętrzny względem aparatu synchronizacji. Używa harmonogramu zadań systemu Windows lub oddzielnej usługi systemu Windows, aby wyzwolić proces synchronizacji. Harmonogram jest z wbudowanymi wersjami 1,1 do aparatu synchronizacji i zezwalają na dostosowanie. Nowa domyślna częstotliwość synchronizacji to 30 minut.

Harmonogram jest odpowiedzialny za dwa zadania:

* **Cykl synchronizacji**. Proces importowania, synchronizowania i eksportowania zmian.
* **Zadania konserwacji**. Odnawianie kluczy i certyfikatów do resetowania haseł i usługi rejestracji urządzeń (DRS). Przeczyść stare wpisy w dzienniku operacji.

Sam harmonogram jest zawsze uruchomiony, ale można go skonfigurować tak, aby uruchamiał tylko jedno lub żadne z tych zadań. Na przykład jeśli musisz mieć własny proces cyklu synchronizacji, możesz wyłączyć to zadanie w harmonogramie, ale nadal uruchomić zadanie obsługi.

>[!IMPORTANT]
>Domyślnie co 30 minut jest uruchamiany cykl synchronizacji. Jeśli zmodyfikowano cykl synchronizacji, należy się upewnić, że cykl synchronizacji jest uruchamiany co najmniej raz w ciągu 7 dni. 
>
>* Synchronizacja różnicowa musi wystąpić w ciągu 7 dni od ostatniej synchronizacji różnicowej.
>* Synchronizacja różnicowa (po pełnej synchronizacji) musi nastąpić w ciągu 7 dni od momentu zakończenia ostatniej pełnej synchronizacji.
>
>Niewykonanie tej czynności może spowodować problemy z synchronizacją, co spowoduje konieczność uruchomienia pełnej synchronizacji w celu rozwiązania problemu. Dotyczy to również serwerów w trybie przejściowym.

## <a name="scheduler-configuration"></a>Konfiguracja harmonogramu
Aby wyświetlić bieżące ustawienia konfiguracji, przejdź do programu PowerShell i uruchom polecenie `Get-ADSyncScheduler` . Przedstawiono tutaj następujący obraz:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Jeśli po uruchomieniu tego polecenia cmdlet zobaczysz **polecenie Synchronizuj, lub polecenie cmdlet nie jest dostępne** , moduł programu PowerShell nie zostanie załadowany. Przyczyną tego problemu może być uruchomienie Azure AD Connect na kontrolerze domeny lub na serwerze z wyższymi poziomami ograniczeń programu PowerShell niż ustawienia domyślne. Jeśli ten błąd jest wyświetlany, uruchom polecenie, `Import-Module ADSync` Aby wyświetlić dostępne polecenia cmdlet.

* **AllowedSyncCycleInterval**. Najkrótszy odstęp czasu między cyklami synchronizacji dozwolonymi przez usługę Azure AD. Nie można synchronizować częściej niż to ustawienie i nadal jest obsługiwane.
* **CurrentlyEffectiveSyncCycleInterval**. Harmonogram aktualnie działa. Ma taką samą wartość jak CustomizedSyncInterval (jeśli jest ustawiona), jeśli nie jest częściej niż AllowedSyncInterval. Jeśli używasz kompilacji przed 1.1.281 i zmienisz CustomizedSyncCycleInterval, ta zmiana zacznie obowiązywać po kolejnym cyklu synchronizacji. Od kompilacji 1.1.281 zmiana zacznie obowiązywać natychmiast.
* **CustomizedSyncCycleInterval**. Jeśli chcesz, aby harmonogram był uruchamiany z inną częstotliwością niż domyślna 30 minut, skonfiguruj to ustawienie. Na powyższym rysunku harmonogram został ustawiony tak, aby był uruchamiany co godzinę. W przypadku ustawienia tego ustawienia na wartość niższą niż AllowedSyncInterval, ta ostatnia zostanie użyta.
* **NextSyncCyclePolicyType**. Różnicowe lub początkowe. Określa, czy następny przebieg powinien przetwarzać zmiany różnicowe, czy też następne uruchomienie powinno wykonać pełny import i synchronizację. Spowoduje to również przetworzenie ponownie wszystkich nowych lub zmienionych reguł.
* **NextSyncCycleStartTimeInUTC**. Następnym razem, gdy harmonogram zacznie następnym cyklem synchronizacji.
* **PurgeRunHistoryInterval**. Należy zachować dzienniki operacji czasu. Te dzienniki można przejrzeć w Menedżerze usługi synchronizacji. Domyślnie te dzienniki są przechowywane przez 7 dni.
* **SyncCycleEnabled**. Wskazuje, czy w ramach operacji w harmonogramie działają procesy importowania, synchronizowania i eksportowania.
* **MaintenanceEnabled**. Pokazuje, czy proces konserwacji jest włączony. Aktualizuje ona certyfikaty/klucze i Przeczyszcza dziennik operacji.
* **StagingModeEnabled**. Pokazuje, czy [tryb przejściowy](how-to-connect-sync-staging-server.md) jest włączony. Jeśli to ustawienie jest włączone, program pomija operacje eksportowania z uruchomionym programem, ale nadal uruchamia Importowanie i synchronizację.
* **SchedulerSuspended**. Ustawiane przez połączenie podczas uaktualniania w celu tymczasowego blokowania uruchamiania harmonogramu.

Niektóre z tych ustawień można zmienić za pomocą programu `Set-ADSyncScheduler` . Następujące parametry można modyfikować:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

We wcześniejszych kompilacjach Azure AD Connect **isStagingModeEnabled** został ujawniony w Set-ADSyncScheduler. Ustawienie tej właściwości nie jest **obsługiwane** . Właściwość **SchedulerSuspended** powinna być modyfikowana tylko przez połączenie. To ustawienie nie jest **obsługiwane** bezpośrednio w programie PowerShell.

Konfiguracja harmonogramu jest przechowywana w usłudze Azure AD. W przypadku serwera przejściowego jakakolwiek zmiana na serwerze podstawowym ma wpływ na serwer przejściowy (z wyjątkiem IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Obowiązuje `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d-dni, GG godziny, mm-min, SS-s

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Zmienia harmonogram uruchamiania co 3 godziny.

Przykład: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Zmiany zmieniają harmonogram uruchamiania codziennie.

### <a name="disable-the-scheduler"></a>Wyłączanie harmonogramu  
Jeśli musisz wprowadzić zmiany w konfiguracji, chcesz wyłączyć harmonogram. Na przykład podczas [konfigurowania filtrowania](how-to-connect-sync-configure-filtering.md) lub [wprowadzania zmian w regułach synchronizacji](how-to-connect-sync-change-the-configuration.md).

Aby wyłączyć harmonogram, uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Wyłączanie harmonogramu](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Po wprowadzeniu zmian nie zapomnij ponownie włączyć harmonogramu przy użyciu programu `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="start-the-scheduler"></a>Uruchom Harmonogram
Harmonogram jest domyślnie uruchamiany co 30 minut. W niektórych przypadkach może być konieczne uruchomienie cyklu synchronizacji między planowanymi cyklami lub uruchomienie innego typu.

### <a name="delta-sync-cycle"></a>Cykl synchronizacji różnicowej
Cykl synchronizacji delty obejmuje następujące kroki:


- Import Delta na wszystkich łącznikach
- Synchronizacja różnicowa na wszystkich łącznikach
- Eksportuj na wszystkich łącznikach

### <a name="full-sync-cycle"></a>Cykl pełnej synchronizacji
Pełny cykl synchronizacji obejmuje następujące kroki:

- Pełny import na wszystkich łącznikach
- Pełna synchronizacja wszystkich łączników
- Eksportuj na wszystkich łącznikach

Może się zdarzyć, że masz pilną zmianę, która musi zostać natychmiast zsynchronizowana, co jest potrzebne do ręcznego uruchomienia cyklu. 

Jeśli musisz ręcznie uruchomić cykl synchronizacji, uruchom polecenie z programu PowerShell `Start-ADSyncSyncCycle -PolicyType Delta` .

Aby zainicjować cykl pełnej synchronizacji, uruchom polecenie `Start-ADSyncSyncCycle -PolicyType Initial` w wierszu polecenia programu PowerShell.   

Uruchamianie pełnego cyklu synchronizacji może być bardzo czasochłonne. Przeczytaj następną sekcję, aby zapoznać się z tematem Optymalizacja tego procesu.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Kroki synchronizacji wymagane dla różnych zmian konfiguracji
Różne zmiany konfiguracji wymagają różnych kroków synchronizacji, aby upewnić się, że zmiany są poprawnie zastosowane do wszystkich obiektów.

- Dodano więcej obiektów lub atrybutów do zaimportowania z katalogu źródłowego (przez dodanie/zmodyfikowanie reguł synchronizacji)
    - Na łączniku dla tego katalogu źródłowego jest wymagany pełny import
- Wprowadzono zmiany w regułach synchronizacji
    - Na łączniku wymagana jest pełna synchronizacja dla zmienionych reguł synchronizacji
- Zmieniono [filtrowanie](how-to-connect-sync-configure-filtering.md) , aby uwzględnić inną liczbę obiektów
    - Na łączniku dla każdego łącznika usługi AD jest wymagany pełny import, chyba że jest używane filtrowanie oparte na atrybutach na podstawie atrybutów, które są już importowane do aparatu synchronizacji

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Dostosowanie cyklu synchronizacji polega na tym, że został uruchomiony właściwy kombinacja różnic między etapami i pełną synchronizacją
Aby uniknąć uruchomienia pełnego cyklu synchronizacji, można oznaczyć określone łączniki, aby uruchomić pełny krok przy użyciu następujących poleceń cmdlet.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Przykład: Jeśli wprowadzono zmiany w regułach synchronizacji łącznika "AD Forest A", które nie wymagają żadnych nowych atrybutów do zaimportowania, uruchom następujące polecenia cmdlet, aby uruchomić cykl synchronizacji różnicowej, który również przeprowadził krok pełnej synchronizacji dla tego łącznika.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Przykład: w przypadku wprowadzenia zmian w regułach synchronizacji łącznika "AD Forest A", aby teraz wymagały importowania nowego atrybutu, uruchom następujące polecenia cmdlet, aby uruchomić cykl synchronizacji różnicowej, który również ukończył pełny krok importu, pełną synchronizację dla tego łącznika.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Zatrzymaj harmonogram
Jeśli harmonogram jest aktualnie uruchomiony w cyklu synchronizacji, może być konieczne jego zatrzymanie. Na przykład jeśli uruchomisz kreatora instalacji i wystąpi ten błąd:

![Zrzut ekranu pokazujący nie można zmienić komunikatu o błędzie konfiguracji.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Gdy cykl synchronizacji jest uruchomiony, nie można wprowadzać zmian w konfiguracji. Możesz poczekać, aż harmonogram zakończy proces, ale możesz również zatrzymać go, aby można było natychmiast wprowadzić zmiany. Zatrzymywanie bieżącego cyklu nie jest szkodliwe i oczekujące zmiany są przetwarzane z następnym uruchomieniem.

1. Zacznij od poinformowania harmonogramu, aby zatrzymał bieżący cykl przy użyciu polecenia cmdlet programu PowerShell `Stop-ADSyncSyncCycle` .
2. Jeśli używasz kompilacji przed 1.1.281, zatrzymanie harmonogramu nie zatrzyma bieżącego łącznika z jego bieżącego zadania. Aby wymusić zatrzymanie łącznika, wykonaj następujące czynności:

   ![Zrzut ekranu pokazuje Synchronization Service Manager z wybranymi łącznikami i uruchomionym łącznikiem z wybraną akcją Zatrzymaj.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Uruchom **usługę synchronizacji** z menu Start. Przejdź do **łączników**, podświetl łącznik z **uruchomionym**stanem i wybierz pozycję **Zatrzymaj** w akcjach.

Harmonogram jest nadal aktywny i zostanie uruchomiony ponownie przy następnej okazji.

## <a name="custom-scheduler"></a>Harmonogram niestandardowy
Polecenia cmdlet udokumentowane w tej sekcji są dostępne tylko w kompilacjach [1.1.130.0](reference-connect-version-history.md) i nowszych.

Jeśli wbudowany harmonogram nie spełnia wymagań użytkownika, można zaplanować łączniki przy użyciu programu PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Profil łącznika można uruchomić w następujący sposób:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Nazwy, które mają być używane w [nazwach łączników](how-to-connect-sync-service-manager-ui-connectors.md) i [nazwy profilów uruchamiania](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) , można znaleźć w [interfejsie użytkownika Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

![Wywołaj przebieg profilu](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile`Polecenie cmdlet jest synchroniczne, czyli nie zwraca kontroli, dopóki łącznik nie ukończy operacji, pomyślnie lub z powodu błędu.

Po zaplanowaniu łączników zaleca się zaplanowanie ich w następującej kolejności:

1. (Pełne/różnicowe) Importuj z katalogów lokalnych, takich jak Active Directory
2. (Pełne/różnicowe) Importuj z usługi Azure AD
3. (Pełne/różnicowe) Synchronizacja z katalogów lokalnych, takich jak Active Directory
4. (Pełne/różnicowe) Synchronizacja z usługi Azure AD
5. Eksportuj do usługi Azure AD
6. Eksportuj do katalogów lokalnych, takich jak Active Directory

Ta kolejność polega na tym, jak wbudowany harmonogram uruchamia łączniki.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Możesz również monitorować aparat synchronizacji, aby sprawdzić, czy jest on zajęty lub bezczynny. To polecenie cmdlet zwraca pusty wynik, jeśli aparat synchronizacji jest w stanie bezczynności i nie jest uruchomiony łącznik. Jeśli łącznik jest uruchomiony, zwraca nazwę łącznika.

```
Get-ADSyncConnectorRunStatus
```

![Stan uruchomienia łącznika](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na powyższym rysunku pierwszy wiersz pochodzi z stanu, w którym aparat synchronizacji jest bezczynny. Drugi wiersz od momentu uruchomienia łącznika usługi Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Kreator harmonogramu i instalacji
W przypadku uruchomienia Kreatora instalacji harmonogram jest tymczasowo zawieszony. Dzieje się tak, ponieważ zakłada się, że wprowadzono zmiany w konfiguracji i nie można zastosować tych ustawień, jeśli aparat synchronizacji aktywnie działa. Z tego powodu nie należy pozostawiać otwartego Kreatora instalacji, ponieważ uniemożliwia aparatowi synchronizacji wykonywanie akcji synchronizacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
