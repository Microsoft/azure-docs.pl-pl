---
title: Rozwiązywanie problemów z aktualizacją dostawcy Site Recovery Microsoft Azure
description: Rozwiązywanie typowych problemów występujących podczas uaktualniania dostawcy Site Recovery Microsoft Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: 5a6e4b415a9fe8ea80a84e415879df9d2f359478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84434376"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Rozwiązywanie problemów z błędami uaktualniania dostawcy Microsoft Azure Site Recovery

Ten artykuł pomaga w rozwiązywaniu problemów, które mogą powodować błędy podczas uaktualniania dostawcy Site Recovery Microsoft Azure.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Uaktualnienie nie powiedzie się, że jest już zainstalowany najnowszy dostawca Site Recovery

W przypadku uaktualniania Microsoft Azure Site Recovery dostawcy (DRA) ujednolicone Uaktualnienie Instalatora zakończy się niepowodzeniem i wygeneruje komunikat o błędzie:

Uaktualnianie nie jest obsługiwane, ponieważ jest już zainstalowana nowsza wersja oprogramowania.

Aby przeprowadzić uaktualnienie, wykonaj następujące czynności:

1. Pobierz Microsoft Azure Site Recovery ujednoliconą konfigurację:
   1. W sekcji "linki do obecnie obsługiwanych pakietów zbiorczych aktualizacji" [aktualizacji usługi w Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) artykule wybierz dostawcę, do którego chcesz przeprowadzić uaktualnienie.
   2. Na stronie zbiorczej Znajdź sekcję **Informacje o aktualizacji** i Pobierz pakiet zbiorczy aktualizacji dla Microsoft Azure Site Recovery Unified Setup.

2. Otwórz wiersz polecenia i przejdź do folderu, do którego został pobrany plik ujednoliconej konfiguracji. Wyodrębnij pliki instalacyjne z pobrania przy użyciu następującego polecenia, MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: &lt; ścieżka folderu dla wyodrębnionych plików &gt; .
    
    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: C:\Temp\Extracted

3. W wierszu polecenia przejdź do folderu, do którego wyodrębniono pliki, i uruchom następujące polecenia instalacji:
   
    CX_THIRDPARTY_SETUP.EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP.EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Wróć do folderu, do którego pobrano ujednoliconą konfigurację, i uruchom MicrosoftAzureSiteRecoveryUnifiedSetup.exe, aby zakończyć uaktualnienie. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Niepowodzenie uaktualniania z powodu zmiany nazwy folderu innej firmy

Aby uaktualnienie zakończyło się pomyślnie, nie można zmienić nazwy folderu innej firmy.

W celu rozwiązania tego problemu.

1. Uruchom Edytor rejestru (regedit.exe) i Otwórz gałąź HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Sprawdź `Build_Version` wartość klucza. Jeśli jest ustawiona na najnowszą wersję, Zmniejsz numer wersji. Na przykład jeśli Najnowsza wersja to 9,22. \* i `Build_Version` klucz ustawiony na tę wartość, a następnie Zmniejsz go do 9,21. \*
1. Pobierz najnowszą Microsoft Azure Site Recovery ujednoliconą konfigurację:
   1. W sekcji "linki do obecnie obsługiwanych pakietów zbiorczych aktualizacji" [aktualizacji usługi w Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) artykule wybierz dostawcę, do którego chcesz przeprowadzić uaktualnienie.
   2. Na stronie zbiorczej Znajdź sekcję **Informacje o aktualizacji** i Pobierz pakiet zbiorczy aktualizacji dla Microsoft Azure Site Recovery Unified Setup.
1. Otwórz wiersz polecenia i przejdź do folderu, do którego pobrano plik ujednoliconej konfiguracji i Wyodrębnij pliki instalacji z pobrania za pomocą następującego polecenia, MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: &lt; ścieżka folderu dla wyodrębnionych plików &gt; .

    Przykładowe polecenie:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: C:\Temp\Extracted

1. W wierszu polecenia przejdź do folderu, do którego wyodrębniono pliki, i uruchom następujące polecenia instalacji:
   
    CX_THIRDPARTY_SETUP.EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART

1. Za pomocą Menedżera zadań można monitorować postęp instalacji. Gdy proces CX_THIRDPARTY_SETUP.EXE nie jest już widoczny w Menedżerze zadań, przejdź do następnego kroku.
1. Sprawdź, czy C:\thirdparty istnieje i czy folder zawiera biblioteki RRD.
1. Wróć do folderu, do którego pobrano ujednoliconą konfigurację, i uruchom MicrosoftAzureSiteRecoveryUnifiedSetup.exe, aby zakończyć uaktualnienie.

## <a name="upgrade-failure-due-to-master-target-installation-failure"></a>Niepowodzenie uaktualnienia z powodu niepowodzenia instalacji głównego elementu docelowego

Podczas uaktualniania Microsoft Azure Site Recovery dostawcy (DRA) Instalacja głównego elementu docelowego kończy się niepowodzeniem z powodu błędu "Lokalizacja instalacji nie istnieje i/lub nie ma 1 GB wolnego miejsca i/lub nie istnieje na dysku stałym".

Może to być spowodowane wartością null parametru w kluczu rejestru. Aby rozwiązać ten problem —

1. Uruchom Edytor rejestru (regedit.exe) i Otwórz gałąź HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\4.
1. Sprawdź wartość klucza "InstallDirectory". Jeśli ma wartość null, Dodaj bieżącą wartość katalogu instalacyjnego.
1. Podobnie Otwórz gałąź HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5 w Edytorze rejestru.
1. Sprawdź wartość klucza "InstallDirectory" i Dodaj bieżącą wartość katalogu instalacyjnego.
1. Uruchom ponowne uruchomienie ujednoliconego Instalatora Instalatora.
