---
title: Wykonywanie pakietów SSIS przez agenta wystąpienia zarządzanego sql azure
description: Dowiedz się, jak wykonywać pakiety SSIS przez agenta wystąpienia zarządzanego sql azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394724"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Wykonywanie pakietów SSIS przez agenta wystąpienia zarządzanego sql azure
W tym artykule opisano sposób uruchamiania pakietu usług integracji programu SQL Server (SSIS) przy użyciu agenta wystąpienia zarządzanego sql azure. Ta funkcja zapewnia podobne zachowania, podobnie jak podczas planowania pakietów SSIS przez agenta programu SQL Server w środowisku on-prem.

Za pomocą tej funkcji można uruchamiać pakiety SSIS, które są przechowywane w UDS wystąpienia zarządzanego programu Azure SQL lub systemu plików, takiego jak pliki Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z tej funkcji, pobierz i zainstaluj najnowszą wersję usługi SSMS, która jest w wersji 18.5 lub nowszej. Pobierz go z [tej strony](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

I należy aprowizować środowisko uruchomieniowe integracji platformy Azure-SSIS w usłudze Azure Data Factory, która używa wystąpienia zarządzanego sql platformy Azure jako serwera punktu końcowego. Jeśli nie zostały już zainicjowane, przepisać go, postępując zgodnie z instrukcjami w [samouczku](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Uruchamianie pakietów SSIS w Usłudze SSISDB przez agenta wystąpienia zarządzanego sql azure
W tym kroku używasz agenta wystąpienia zarządzanego sql platformy Azure do wywoływania pakietów SSIS przechowywanych w Usłudze SSISDB w wystąpieniu zarządzanym sql platformy Azure.
1. W najnowszej wersji usługi SSMS połącz się z wystąpieniem zarządzanym usługi Azure SQL.
2. Utwórz nową pracę agenta i nowy krok zadania.

![Nowa praca agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na stronie **Nowy krok zadania** wybierz typ pakietu usług **integracyjnych programu SQL Server.**

![Nowy krok zadania SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Na karcie **Pakiet** wybierz pozycję **Wykaz SSIS** jako typ źródła pakietu.
5. Ponieważ SSISDB znajduje się w tym samym wystąpieniu zarządzanym sql platformy Azure, nie trzeba określać uwierzytelniania.
6. Określ pakiet SSIS z bazy SSISDB.

![Typ źródła pakietu - Katalog SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. Na karcie **Konfiguracje** można określić wartości **parametrów,** zastąpić wartości w **menedżerach połączeń,** zastąpić **właściwość** i wybrać pozycję Poziom **rejestrowania**.

![Typ źródła pakietu — konfiguracja katalogu SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Po zakończeniu całej konfiguracji powyżej kliknij przycisk **OK,** aby zapisać konfigurację zadania agenta.
9. Uruchom zadanie agenta, aby wykonać pakiet SSIS.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Uruchamianie pakietów SSIS w systemie plików przez agenta wystąpienia zarządzanego sql azure
W tym kroku można użyć agenta wystąpienia zarządzanego sql platformy Azure do wywołania pakietów SSIS, który jest przechowywany w systemie plików do uruchomienia.
1. W najnowszej wersji usługi SSMS połącz się z wystąpieniem zarządzanym usługi Azure SQL.
2. Utwórz nową pracę agenta i nowy krok zadania.

   ![Nowa praca agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na stronie **Nowy krok zadania** wybierz typ pakietu usług **integracyjnych programu SQL Server.**

   ![Nowy krok zadania SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Na karcie **Pakiet** wybierz pozycję **System plików** jako typ źródła pakietu.

   ![Typ źródła pakietu — system plików](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Jeśli pakiet jest przekazyny do usługi Azure File, wybierz **udział plików platformy Azure** jako typ źródła pliku.
      - Ścieżka pakietu to ** \\ <storage account name>nazwa udziału\<plików file.core.windows.net>\<nazwa pakietu>.dtsx**
      - Wpisz nazwę konta pliku platformy Azure i klucz konta w **poświadczeniu dostępu do pliku pakietu,** aby uzyskać dostęp do pliku platformy Azure. Domena jest ustawiona jako **Azure**.
   2. Jeśli pakiet jest przekazyany do udziału sieciowego, wybierz **opcję Udział sieciowy** jako typ źródła pliku.
      - Ścieżka pakietu jest **ścieżką UNC** pliku pakietu z rozszerzeniem dtsx.
      - Wpisz odpowiednią **domenę,** **nazwę użytkownika**i **hasło,** aby uzyskać dostęp do pliku pakietu udziału sieciowego.
   3. Jeśli plik pakietu jest zaszyfrowany hasłem, wybierz **hasło szyfrowania** i wpisz hasło.

 5. Na karcie **Konfiguracje** wpisz **ścieżkę pliku konfiguracji,** jeśli potrzebujesz pliku konfiguracyjnego do wykonania pakietu SSIS.
 6. Na karcie **Opcje wykonywania** można wybrać, czy do wykonania pakietu SSIS ma być używane **uwierzytelnianie systemu Windows,** czy **32-bitowe środowisko uruchomieniowe.**
 7. Na karcie **Rejestrowanie** można wybrać **ścieżkę rejestrowania** i odpowiadające im poświadczenia dostępu do rejestrowania do przechowywania plików dziennika. Domyślnie ścieżka rejestrowania będzie taka sama jak ścieżka folderu pakietu, a poświadczenia dostępu do rejestrowania będą takie same jak poświadczenia dostępu do pakietu.
 8. Na karcie **Ustaw wartości** można wpisać ścieżkę **właściwości** i **wartość,** aby zastąpić właściwości pakietu.
 Na przykład, aby zastąpić wartość zmiennej użytkownika, wprowadź jej ścieżkę w następującym formacie: **<variable name>\Package.Variables[User:: ]. Wartość**.
 9. Po zakończeniu całej konfiguracji powyżej kliknij przycisk **OK,** aby zapisać konfigurację zadania agenta.
 10. Uruchom zadanie agenta, aby wykonać pakiet SSIS.


 ## <a name="cancel-ssis-package-execution"></a>Anulowanie wykonania pakietu SSIS
 Aby anulować wykonanie pakietu z zadania programu Azure SQL Managed Agent, należy wykonać poniższe kroki zamiast bezpośrednio zatrzymywać zadanie agenta.
 1. Znajdź swój **jobId agenta** SQL z **msdb.dbo.sysjobs**.
 2. Znajdź odpowiedni **identyfikator wykonywania** SSIS na podstawie identyfikatora zadania według poniższej kwerendy:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Wybierz **pozycję Aktywne operacje** w katalogu SSIS.

    ![Typ źródła pakietu — system plików](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. Zatrzymaj odpowiednią operację na podstawie **executionId**.

## <a name="next-steps"></a>Następne kroki
 Można również zaplanować pakiety SSIS przy użyciu usługi Azure Data Factory. Aby uzyskać instrukcje krok po kroku, zobacz [Wyzwalacz zdarzeń usługi Azure Data Factory .](how-to-create-event-trigger.md) 