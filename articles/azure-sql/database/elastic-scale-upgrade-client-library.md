---
title: Uaktualnianie do najnowszej biblioteki klienta Elastic Database
description: Użyj NuGet, aby uaktualnić bibliotekę klienta Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793419"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uaktualnij aplikację, aby użyć najnowszej biblioteki klienta Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nowe wersje [biblioteki klienta Elastic Database](elastic-database-client-library.md) są dostępne za pomocą narzędzia NuGet i interfejsu Menedżera pakietów NuGet w programie Visual Studio. Uaktualnienia zawierają poprawki błędów i obsługują nowe możliwości biblioteki klienta.

**Najnowsza wersja:** Przejdź do [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Skompiluj ponownie aplikację przy użyciu nowej biblioteki, a także zmień istniejące metadane Menedżera mapy fragmentu przechowywane w bazach danych w Azure SQL Database, aby umożliwić obsługę nowych funkcji.

Wykonanie tych kroków w celu zapewnienia, że stare wersje biblioteki klienta nie będą już obecne w Twoim środowisku, gdy zostaną zaktualizowane obiekty metadanych, co oznacza, że po uaktualnieniu nie zostaną utworzone obiekty metadanych starej wersji.

## <a name="upgrade-steps"></a>Kroki uaktualniania

**1. Uaktualnij aplikacje.** W programie Visual Studio Pobierz najnowszą wersję biblioteki klienta i odwołuje się do wszystkich projektów programistycznych korzystających z biblioteki; następnie Skompiluj i Wdróż.

* W rozwiązaniu programu Visual Studio wybierz kolejno pozycje **Narzędzia**  -->  **Menedżer pakietów NuGet** zarządzanie pakietami  -->   **NuGet dla rozwiązania**.
* (Visual Studio 2013) W lewym panelu wybierz pozycję **aktualizacje**, a następnie wybierz przycisk **aktualizuj** w pakiecie **Azure SQL Database biblioteki klienta elastycznej skalowania** , która jest wyświetlana w oknie.
* (Visual Studio 2015) Ustaw pole filtru na **dostępne do uaktualnienia**. Wybierz pakiet do zaktualizowania, a następnie kliknij przycisk **Aktualizuj** .
* (Visual Studio 2017) W górnej części okna dialogowego wybierz pozycję **aktualizacje**. Wybierz pakiet do zaktualizowania, a następnie kliknij przycisk **Aktualizuj** .
* Kompiluj i wdrażaj.

**2. Uaktualnij skrypty.** Jeśli używasz skryptów **programu PowerShell** do zarządzania usługą fragmentów, [Pobierz nową wersję biblioteki](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) i skopiuj ją do katalogu, z którego wykonujesz skrypty.

**3. Uaktualnij usługę Split-Merge.** W przypadku używania narzędzia do dzielenia i scalania baz danych w celu reorganizowania danych podzielonej na fragmenty należy [pobrać i wdrożyć najnowszą wersję narzędzia](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Szczegółowe kroki uaktualniania usługi można znaleźć [tutaj](elastic-scale-overview-split-and-merge.md).

**4. Uaktualnij bazy danych Menedżera map fragmentu**. Uaktualnij metadane obsługujące mapy fragmentu w Azure SQL Database.  Można to zrobić na dwa sposoby przy użyciu programu PowerShell lub języka C#. Poniżej przedstawiono obie opcje.

***Opcja 1: Uaktualnianie metadanych przy użyciu programu PowerShell***

1. Pobierz najnowsze narzędzie wiersza polecenia dla programu NuGet z tego [miejsca](https://nuget.org/nuget.exe) i Zapisz je w folderze.
2. Otwórz wiersz polecenia, przejdź do tego samego folderu i wydaj polecenie: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Przejdź do podfolderu zawierającego nową wersję biblioteki DLL klienta, na przykład: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Pobierz skrypt uaktualnienia Elastic Database Client z [Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)i Zapisz go w tym samym folderze, w którym znajduje się biblioteka DLL.
5. W tym folderze Uruchom polecenie "PowerShell .\upgrade.ps1" w wierszu polecenia i postępuj zgodnie z monitami.

***Opcja 2: Uaktualnianie metadanych przy użyciu języka C #***

Alternatywnie możesz utworzyć aplikację programu Visual Studio, która otwiera ShardMapManager, iteruje wszystkie fragmentów i wykonuje uaktualnienie metadanych, wywołując metody [UpgradeLocalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) i [UpgradeGlobalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) , jak w poniższym przykładzie:

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

Metody uaktualniania metadanych można stosować wiele razy bez szkody. Na przykład jeśli Starsza wersja klienta przypadkowo tworzy fragmentu po aktualizacji, można uruchomić uaktualnienie ponownie dla wszystkich fragmentów, aby upewnić się, że Najnowsza wersja metadanych jest obecna w całej infrastrukturze.

**Uwaga:**  Nowe wersje biblioteki klienta opublikowane w tym dniu nadal pracują z wcześniejszymi wersjami metadanych Menedżera mapy fragmentu na Azure SQL Database i na odwrót.   Jednak aby korzystać z niektórych nowych funkcji w najnowszym kliencie, należy uaktualnić metadane.   Należy pamiętać, że uaktualnienia metadanych nie wpłyną na dane użytkownika lub dane specyficzne dla aplikacji, tylko obiekty utworzone i używane przez Menedżera mapy fragmentu.  Aplikacje i nadal działają przez sekwencję uaktualniania opisaną powyżej.

## <a name="elastic-database-client-version-history"></a>Historia wersji klienta Elastic Database

W obszarze historia wersji przejdź do pozycji [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png