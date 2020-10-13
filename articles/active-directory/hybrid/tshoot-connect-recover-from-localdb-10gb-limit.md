---
title: 'Azure AD Connect: jak odzyskać problem z limitem LocalDB 10 GB | Microsoft Docs'
description: W tym temacie opisano sposób odzyskiwania usługi synchronizacji Azure AD Connect w przypadku napotkania problemu z LocalDB 10 GB.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca5361d8500ecd4ea22a577d0a4dc7ced606eab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89275906"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Jak usunąć limit 10 GB dla lokalnej bazy danych
Program Azure AD Connect wymaga bazy danych programu SQL Server do przechowywania danych tożsamości. Możesz korzystać z domyślnego programu SQL Server 2012 Express LocalDB zainstalowanego z programem Azure AD Connect lub użyć własnego pełnego programu SQL. Program SQL Server Express narzuca limit rozmiaru w wysokości 10 GB. Jeśli jest używany program LocalDB i limit zostanie osiągnięty, usługa synchronizacji programu Azure AD Connect nie będzie mogła uruchomić się ani prawidłowo wykonywać synchronizacji. Ten artykuł zawiera kroki odzyskiwania.

## <a name="symptoms"></a>Objawy
Istnieją dwa typowe objawy:

* Usługa synchronizacji Azure AD Connect **jest uruchomiona** , ale nie można przeprowadzić synchronizacji z błędem *"zatrzymana-baza danych-Full"* .

* **Nie można uruchomić**usługi synchronizacji Azure AD Connect. Próba uruchomienia usługi kończy się niepowodzeniem ze zdarzeniem 6323 i komunikatem o błędzie *"Serwer napotkał błąd, ponieważ SQL Server brakuje miejsca na dysku".*

## <a name="short-term-recovery-steps"></a>Krótkoterminowe kroki odzyskiwania
Ta sekcja zawiera instrukcje dotyczące odzyskiwania obszaru bazy danych wymaganego do wznowienia działania usługi synchronizacji Azure AD Connect. Kroki obejmują:
1. [Określanie stanu usługi synchronizacji](#determine-the-synchronization-service-status)
2. [Zmniejszanie bazy danych](#shrink-the-database)
3. [Usuń dane historii uruchamiania](#delete-run-history-data)
4. [Skrócenie okresu przechowywania danych historii uruchamiania](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Określanie stanu usługi synchronizacji
Najpierw Ustal, czy usługa synchronizacji jest nadal uruchomiona:

1. Zaloguj się do serwera Azure AD Connect jako administrator.

2. Przejdź do **Menedżera kontroli usług**.

3. Sprawdź stan **synchronizacji Microsoft Azure AD**.


4. Jeśli jest uruchomiona, nie zatrzymuj ani nie uruchamiaj ponownie usługi. Pomiń krok [zmniejszania bazy danych](#shrink-the-database) i przejdź do obszaru [usuwanie danych historii uruchamiania](#delete-run-history-data) .

5. Jeśli nie jest uruchomiona, spróbuj uruchomić usługę. Jeśli usługa zostanie uruchomiona pomyślnie, pomiń krok [zmniejszania bazy danych](#shrink-the-database) i przejdź do obszaru [usuwanie danych historii uruchamiania](#delete-run-history-data) . W przeciwnym razie Kontynuuj i [Zmniejsz krok bazy danych](#shrink-the-database) .

### <a name="shrink-the-database"></a>Zmniejszanie bazy danych
Użyj operacji zmniejszania, aby zwolnić wystarczającą ilość miejsca w bazie danych, aby uruchomić usługę synchronizacji. Zwalnia miejsce w bazie danych przez usunięcie białych znaków z bazy. Ten krok jest najlepszym rozwiązaniem, ponieważ nie gwarantuje to, że zawsze można odzyskać miejsce. Aby dowiedzieć się więcej na temat operacji zmniejszania, przeczytaj ten artykuł [zmniejszanie bazy danych](/sql/relational-databases/databases/shrink-a-database?view=sql-server-ver15).

> [!IMPORTANT]
> Pomiń ten krok, jeśli możesz uzyskać usługę synchronizacji do uruchomienia. Nie zaleca się zmniejszania bazy danych SQL, ponieważ może to doprowadzić do niskiej wydajności z powodu zwiększonej fragmentacji.

Nazwa bazy danych utworzonej dla Azure AD Connect to **ADSync**. Aby wykonać operację zmniejszania, należy zalogować się jako administrator lub DBO bazy danych. Podczas instalacji Azure AD Connect następujące konta otrzymują prawa sysadmin:
* Administratorzy lokalni
* Konto użytkownika używane do uruchamiania instalacji Azure AD Connect.
* Konto usługi synchronizacji używane jako kontekst operacyjny usługi synchronizacji Azure AD Connect.
* ADSyncAdmins grupy lokalnej, która została utworzona podczas instalacji.

1. Wykonaj kopię zapasową bazy danych, kopiując pliki **ADSync. mdf** i **ADSync_log. ldf** znajdujące się w obszarze `%ProgramFiles%\Microsoft Azure AD Sync\Data` do bezpiecznej lokalizacji.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do folderu `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` .

4. Uruchom narzędzie **sqlcmd** , uruchamiając polecenie `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` przy użyciu poświadczeń administratora systemu lub bazy danych.

5. Aby zmniejszyć bazę danych, w wierszu polecenia sqlcmd (1>) wprowadź `DBCC Shrinkdatabase(ADSync,1);` , a następnie `GO` w następnym wierszu.

6. Jeśli operacja się powiedzie, spróbuj ponownie uruchomić usługę synchronizacji. Jeśli możesz uruchomić usługę synchronizacji, przejdź do obszaru [usuwanie danych historii uruchamiania](#delete-run-history-data) . Jeśli nie, skontaktuj się z pomocą techniczną.

### <a name="delete-run-history-data"></a>Usuń dane historii uruchamiania
Domyślnie Azure AD Connect przechowuje dane historii uruchamiania do siedmiu dni. W tym kroku usuniemy dane historii uruchamiania w celu Odbierz przestrzeń bazy danych tak, aby usługa synchronizacji Azure AD Connect mogła ponownie rozpocząć synchronizację.

1. Rozpocznij **Synchronization Service Manager** , przechodząc do uruchamiania usługi synchronizacji →.

2. Przejdź do karty **operacje** .

3. W obszarze **Akcje**wybierz pozycję **Wyczyść uruchomienia**...

4. Można wybrać opcję **Wyczyść wszystkie uruchomienia** lub **Wyczyść uruchomienia przed... \<date> ** Zalecamy rozpoczęcie od czyszczenia danych historii uruchamiania, które są starsze niż dwa dni. Jeśli chcesz kontynuować pracę z problemem z rozmiarem bazy danych, wybierz opcję **Wyczyść wszystkie uruchomienia** .

### <a name="shorten-retention-period-for-run-history-data"></a>Skrócenie okresu przechowywania danych historii uruchamiania
Ten krok polega na zmniejszeniu prawdopodobieństwa uruchomienia do 10 GB problemów z limitem po kilku cyklach synchronizacji.

1. Otwórz nową sesję programu PowerShell.

2. Uruchom `Get-ADSyncScheduler` polecenie i zanotuj Właściwość PurgeRunHistoryInterval, która określa bieżący okres przechowywania.

3. Uruchom `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` , aby ustawić okres przechowywania na dwa dni. Dostosuj okres przechowywania odpowiednio do potrzeb.

## <a name="long-term-solution--migrate-to-full-sql"></a>Długoterminowe rozwiązanie — Migruj do pełnego języka SQL
Ogólnie rzecz biorąc, przyczyną problemu jest to, że rozmiar bazy danych o rozmiarze 10 GB nie wystarcza do Azure AD Connect synchronizowania Active Directory lokalnych z usługą Azure AD. Zaleca się przełączenie do korzystania z pełnej wersji programu SQL Server. Nie można bezpośrednio zastąpić programu LocalDB istniejącego wdrożenia programu Azure AD Connect za pomocą pełnej wersji programu SQL. Zamiast tego należy wdrożyć nowy serwer programu Azure AD Connect z pełną wersją programu SQL. Zalecana jest migracja typu swing, gdzie nowy serwer programu Azure AD Connect (z bazą danych w programie SQL) jest wdrażany jako serwer przejściowy obok istniejącego serwera programu Azure AD Connect (z programem LocalDB). 
* Instrukcje dotyczące konfigurowania zdalnego programu SQL pod kątem programu Azure AD Connect znajdują się w artykule [Niestandardowa instalacja programu Azure AD Connect](./how-to-connect-install-custom.md).
* Instrukcje dotyczące migracji typu swing w ramach uaktualniania programu Azure AD Connect znajdują się w artykule [Azure AD Connect: Upgrade from a previous version to the latest](./how-to-upgrade-previous-version.md#swing-migration) (Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).