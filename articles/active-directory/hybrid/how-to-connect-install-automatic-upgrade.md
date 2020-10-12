---
title: 'Azure AD Connect: automatyczne uaktualnianie | Microsoft Docs'
description: W tym temacie opisano wbudowaną funkcję automatycznego uaktualniania w Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8dcc8766b21551f3cd62289805fe735ef0f333
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317620"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatyczne uaktualnianie
Ta funkcja została wprowadzona w programie Build [1.1.105.0 (wydano luty 2016)](reference-connect-version-history.md).  Ta funkcja została zaktualizowana w usłudze [build 1.1.561](reference-connect-version-history.md) i obsługuje teraz dodatkowe scenariusze, które wcześniej nie były obsługiwane.

## <a name="overview"></a>Omówienie
Upewnienie się, że instalacja Azure AD Connect jest zawsze aktualna, nigdy nie było łatwiejsze dzięki funkcji **automatycznego uaktualniania** . Ta funkcja jest domyślnie włączona w przypadku instalacji ekspresowych i uaktualnień narzędzia DirSync. Po wydaniu nowej wersji instalacja zostanie automatycznie uaktualniona.
Automatyczne uaktualnianie jest domyślnie włączone dla następujących:

* Instalacja ustawień ekspresowych i uaktualnienia narzędzia DirSync.
* Korzystanie z programu SQL Express LocalDB, który jest zawsze używany przez ustawienia ekspresowe. Narzędzie DirSync z programem SQL Express również korzysta z LocalDB.
* Konto usługi AD jest domyślnym kontem MSOL_ utworzonym za pomocą ustawień ekspresowych i narzędzia DirSync.
* Ma mniej niż 100 000 obiektów w magazynie Metaverse.

Bieżący stan uaktualniania automatycznego można wyświetlić za pomocą polecenia cmdlet programu PowerShell `Get-ADSyncAutoUpgrade` . Ma następujące stany:

| State | Komentarz |
| --- | --- |
| Enabled (Włączony) |Automatyczne uaktualnianie jest włączone. |
| Suspended |Ustawiane tylko przez system. System nie kwalifikuje się **obecnie** do otrzymywania automatycznych uaktualnień. |
| Disabled |Automatyczne uaktualnianie jest wyłączone. |

Można zmienić między **włączonym** i **wyłączonym** programem `Set-ADSyncAutoUpgrade` . Tylko system powinien ustawić stan **wstrzymane**.  Przed 1.1.750.0 polecenie cmdlet Set-ADSyncAutoUpgrade blokuje autoupgrade, jeśli stan autouaktualniania został ustawiony na zawieszone. Ta funkcja została zmieniona, więc nie blokuje autouaktualniania.

Automatyczne uaktualnianie korzysta z Azure AD Connect Health dla infrastruktury uaktualniania. Aby automatyczne uaktualnianie działało, upewnij się, że na serwerze proxy zostały otwarte adresy URL **Azure AD Connect Health** zgodnie z opisem w temacie [adresy URL i zakresy adresów IP pakietu Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Jeśli interfejs użytkownika **Synchronization Service Manager** jest uruchomiony na serwerze, uaktualnienie zostanie wstrzymane do momentu zamknięcia interfejsu użytkownika.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli instalacja programu Connect nie zostanie uaktualniona zgodnie z oczekiwaniami, wykonaj następujące kroki, aby dowiedzieć się, co może być błędne.

Najpierw nie należy oczekiwać, że automatyczne uaktualnienie ma być podejmowane jako pierwszy dzień wydania nowej wersji. Przed próbą uaktualnienia należy zamierzać celową losowość, dlatego nie zostanie on zaktualizowany, jeśli instalacja nie zostanie natychmiast uaktualniona.

Jeśli uważasz, że coś nie jest właściwe, najpierw uruchom polecenie, `Get-ADSyncAutoUpgrade` Aby zapewnić włączenie automatycznego uaktualniania.

Jeśli stan jest zawieszony, możesz użyć, `Get-ADSyncAutoUpgrade -Detail` Aby wyświetlić przyczynę.  Przyczyna zawieszenia może zawierać dowolną wartość ciągu, ale zazwyczaj będzie zawierać wartość ciągu UpgradeResult, czyli `UpgradeNotSupportedNonLocalDbInstall` lub `UpgradeAbortedAdSyncExeInUse` .  Można również zwrócić wartość złożoną, na przykład `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

Istnieje również możliwość uzyskania wyniku, który nie jest UpgradeResult, np. "AADHealthEndpointNotDefined" lub "DirSyncInPlaceUpgradeNonLocalDb".

Upewnij się, że zostały otwarte wymagane adresy URL na serwerze proxy lub zaporze. Aktualizacja automatyczna korzysta z Azure AD Connect Health zgodnie z opisem w temacie [Omówienie](#overview). Jeśli używasz serwera proxy, upewnij się, że kondycja została skonfigurowana do korzystania z [serwera proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Przetestuj także [połączenie kondycji](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) z usługą Azure AD.

Po zweryfikowaniu łączności z usługą Azure AD czas zajrzeć do dziennika zdarzeń. Uruchom Podgląd zdarzeń i sprawdź w dzienniku zdarzeń **aplikacji** . Dodaj filtr EventLog dla źródłowego **Azure AD Connect uaktualniania** i identyfikator zdarzenia zakresu **300-399**.  
![Zrzut ekranu przedstawiający okno "Filtruj bieżący dziennik" z wyróżnionym polem "źródła zdarzeń" i "Dołącz/Wyklucz" identyfikatorów zdarzeń.](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Teraz można zobaczyć dzienniki zdarzeń skojarzone ze stanem automatycznego uaktualniania.  
![Filtr EventLog na potrzeby automatycznego uaktualniania](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Kod wyniku zawiera prefiks z omówieniem stanu.

| Prefiks kodu wyniku | Opis |
| --- | --- |
| Powodzenie |Instalacja została pomyślnie uaktualniona. |
| UpgradeAborted |Tymczasowy warunek zatrzymuje uaktualnienie. Ponowna próba zostanie ponowiona i oczekuje, że zostanie ona wykonana później. |
| UpgradeNotSupported |System ma konfigurację, która blokuje automatyczne uaktualnianie systemu. Zostanie ponowiona próba sprawdzenia, czy stan zmienia się, ale oczekuje się, że system należy uaktualnić ręcznie. |

Poniżej znajduje się lista najczęściej spotykanych komunikatów. Nie zawiera on wszystkich elementów, ale komunikat wynikowy powinien być oczywisty, co to jest problem.

| Komunikat z wynikami | Opis |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nie można zapisać do rejestru. |
| UpgradeAbortedInsufficientDatabasePermissions |Wbudowana grupa Administratorzy nie ma uprawnień do bazy danych. Ręcznie przeprowadź uaktualnienie do najnowszej wersji Azure AD Connect, aby rozwiązać ten problem. |
| UpgradeAbortedInsufficientDiskSpace |Za mało miejsca na dysku, aby obsłużyć uaktualnienie. |
| UpgradeAbortedSecurityGroupsNotPresent |Nie można znaleźć i rozpoznać wszystkich grup zabezpieczeń używanych przez aparat synchronizacji. |
| UpgradeAbortedServiceCanNotBeStarted |Nie można uruchomić **synchronizacji** usługi NT Microsoft Azure AD. |
| UpgradeAbortedServiceCanNotBeStopped |Nie można zatrzymać **synchronizacji** usługi NT Microsoft Azure AD. |
| UpgradeAbortedServiceIsNotRunning |Usługa NT **Microsoft Azure AD Sync** nie jest uruchomiona. |
| UpgradeAbortedSyncCycleDisabled |Opcja SyncCycle w [harmonogramie](how-to-connect-sync-feature-scheduler.md) została wyłączona. |
| UpgradeAbortedSyncExeInUse |[Interfejs użytkownika Menedżera usługi synchronizacji](how-to-connect-sync-service-manager-ui.md) jest otwarty na serwerze. |
| UpgradeAbortedSyncOrConfigurationInProgress |Kreator instalacji jest uruchomiony lub synchronizacja została zaplanowana poza harmonogramem. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |Do konfiguracji dodano własne reguły niestandardowe. |
| UpgradeNotSupportedInvalidPersistedState |Instalacja nie jest ustawieniami ekspresowymi ani uaktualnieniem narzędzia DirSync. |
| UpgradeNotSupportedNonLocalDbInstall |Nie używasz bazy danych LocalDB SQL Server Express. |
|UpgradeNotSupportedLocalDbSizeExceeded|Rozmiar lokalnej bazy danych jest większy lub równy 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|Operacje przekazywania danych o kondycji zostały wyłączone z portalu|

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
