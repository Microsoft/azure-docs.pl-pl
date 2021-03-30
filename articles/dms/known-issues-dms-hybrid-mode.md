---
title: Znane problemy/ograniczenia migracji przy użyciu trybu hybrydowego
description: Informacje o znanych problemach/ograniczeniach migracji przy użyciu Azure Database Migration Service w trybie hybrydowym.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291847"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Znane problemy/ograniczenia migracji przy użyciu trybu hybrydowego

W poniższych sekcjach opisano znane problemy i ograniczenia związane z używaniem Azure Database Migration Service w trybie hybrydowym.

## <a name="installer-fails-to-authenticate"></a>Nie można uwierzytelnić Instalatora

Po przekazaniu certyfikatu do AdApp istnieje opóźnienie do kilku minut, zanim będzie można uwierzytelnić się na platformie Azure. Instalator spróbuje ponowić próbę z pewnym opóźnieniem, ale możliwe jest, aby opóźnienie propagacji było dłuższe niż ponowienie i zobaczysz komunikat **FailedToGetAccessTokenException** . Jeśli certyfikat został przekazany do poprawnego AdApp i podano poprawny identyfikator AppId w dmsSettings.jsna, spróbuj ponownie uruchomić polecenie instalacji.

## <a name="service-offline-after-successful-installation"></a>Usługa "offline" po pomyślnej instalacji

Jeśli usługa jest wyświetlana w trybie offline po pomyślnym zakończeniu procesu instalacji, spróbuj wykonać poniższe czynności.

1. W Azure Portal, w wystąpieniu Azure Database Migration Service, przejdź do karty ustawienia **hybrydowe** , a następnie sprawdź, czy proces roboczy jest zarejestrowany, sprawdzając siatkę zarejestrowanych procesów roboczych.

    Stan tego procesu roboczego powinien być w **trybie online**, ale jeśli wystąpił problem, może on być wyświetlany jako w **trybie offline** .

2. Na komputerze roboczym Sprawdź stan usługi, uruchamiając następujące polecenie programu PowerShell:

    ```
    Get-Service Scenario*
    ```

    To polecenie umożliwia wyświetlenie stanu usługi systemu Windows, w której działa proces roboczy. Powinien istnieć tylko jeden wynik. Jeśli proces roboczy został zatrzymany, możesz spróbować ponownie uruchomić go za pomocą następującego polecenia programu PowerShell:

    ```
    Start-Service Scenario*
    ```

    Możesz również sprawdzić usługę w interfejsie użytkownika usług systemu Windows.

3. Jeśli usługa systemu Windows jest przełączana między uruchomioną i zatrzymaną, proces roboczy napotkał problemy podczas uruchamiania. Aby określić problem, Sprawdź dzienniki hybrydowych procesów roboczych Azure Database Migration Service.

    - Dzienniki procesu instalacji są przechowywane w folderze "Logs" w folderze, z którego został uruchomiony plik wykonywalny Instalatora.
    - Azure Database Migration Service hybrydowe dzienniki procesów roboczych są przechowywane w folderze **WorkerLogs** w folderze, w którym jest zainstalowany proces roboczy. Domyślną lokalizacją dla plików dziennika hybrydowego procesu roboczego jest **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Korzystanie z własnego podpisanego certyfikatu

Certyfikat wygenerowany przez akcję GenerateCert to certyfikat z podpisem własnym, który może nie być akceptowalny w oparciu o wewnętrzne zasady zabezpieczeń. Zamiast korzystać z tego certyfikatu, możesz podać własny certyfikat i podać odcisk palca w dmsSettings.js. Ten certyfikat będzie musiał zostać przekazany do AdApp i zainstalowany na komputerze, na którym jest instalowany Azure Database Migration Service hybrydowy proces roboczy. Następnie zainstaluj ten certyfikat z kluczem prywatnym w magazynie certyfikatów komputera lokalnego.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Uruchamianie usługi Worker jako konta z niskim poziomem uprawnień

Domyślnie usługa hybrydowego procesu roboczego Azure Database Migration Service jest uruchamiana jako konto systemu lokalnego. Można zmienić konto używane dla tej usługi, o ile używane konto ma uprawnienia sieciowe. Aby zmienić konto Uruchom jako usługi, użyj następującego procesu.

1. Zatrzymaj usługę za pośrednictwem usług systemu Windows lub za pomocą polecenia Stop-Service w programie PowerShell.

2. Zaktualizuj usługę tak, aby korzystała z innego konta logowania.

3. W programie certmgr dla certyfikatów komputera lokalnego nadaj kluczom prywatnym uprawnienia do nowego konta dla **klucza aplikacji hybrydowej DMS** i **kluczy aparatu usługi DMS** .

    a. Otwórz certmgr, aby wyświetlić następujące klucze:

    - Klucz aplikacji hybrydowej DMS
    - Klucz konfiguracji hybrydowego procesu roboczego DMS
    - Para kluczy aparatu scenariusza "DMS"

    b. Kliknij prawym przyciskiem myszy wpis **kluczowej aplikacji na DMS** , wskaż polecenie **wszystkie zadania**, a następnie wybierz pozycję **Zarządzaj kluczami prywatnymi**.

    c. Na karcie **zabezpieczenia** wybierz pozycję **Dodaj**, a następnie wprowadź nazwę konta.

    d. Wykonaj te same czynności, aby przyznać uprawnienia klucza prywatnego nowemu kontu do certyfikatu **pary kluczy aparatu usługi DMS** .

## <a name="unregistering-the-worker-manually"></a>Ręczne Wyrejestrowywanie procesu roboczego

Jeśli nie masz już dostępu do komputera roboczego, możesz wyrejestrować proces roboczy i ponownie użyć wystąpienia Azure Database Migration Service, wykonując następujące czynności:

1. W Azure Portal, ma do wystąpienia Azure Database Migration Service, a następnie przejdź do strony ustawienia **hybrydowe** .

   Pozycja proces roboczy zostanie wyświetlona na liście, a stan będzie wyświetlany jako **offline**.

2. Po prawej stronie listy wpisów procesu roboczego wybierz wielokropek, a następnie wybierz pozycję **Wyrejestruj**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Rozwiązywanie problemów dotyczących określonych scenariuszy migracji

W poniższych sekcjach opisano problemy dotyczące scenariusza związane z używaniem Azure Database Migration Service trybu hybrydowego do przeprowadzenia migracji w trybie online.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Migracje online do wystąpienia zarządzanego Azure SQL

**Wysokie użycie procesora**

**Problem**: w przypadku migracji w trybie online do wystąpienia zarządzanego SQL komputer, na którym działa hybrydowy proces roboczy, będzie napotykał duże użycie procesora, jeśli istnieje zbyt wiele kopii zapasowych lub kopia zapasowa jest zbyt duża.

Środki **zaradcze**: Aby rozwiązać ten problem, należy użyć skompresowanych kopii zapasowych, podzielić migrację tak, aby korzystała z wielu udziałów, lub skalować komputer z uruchomionym hybrydowym procesem roboczym.
