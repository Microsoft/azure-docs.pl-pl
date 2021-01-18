---
title: Uruchamianie pakietów SSIS przy użyciu agenta wystąpienia zarządzanego usługi Azure SQL
description: Dowiedz się, jak uruchamiać pakiety usług SSIS przy użyciu agenta wystąpienia zarządzanego usługi Azure SQL.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 5f8b18375c517ab5b620b2d6d897f5133b14705d
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556464"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>Uruchamianie pakietów SSIS za pomocą agenta wystąpienia zarządzanego usługi Azure SQL

W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) przy użyciu agenta wystąpienia zarządzanego usługi Azure SQL. Ta funkcja udostępnia zachowania podobne do sytuacji, w której pakiety SSIS są planowane przy użyciu agenta SQL Server w środowisku lokalnym.

Za pomocą tej funkcji można uruchamiać pakiety usług SSIS, które są przechowywane w SSISDB w wystąpieniu zarządzanym SQL, system plików, taki jak Azure Files, lub magazyn pakietów Azure-SSIS Integration Runtime.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tej funkcji, [Pobierz](/sql/ssms/download-sql-server-management-studio-ssms) i zainstaluj najnowszą SQL Server Management Studio (SSMS). Szczegóły obsługi wersji poniżej:

- Aby uruchomić pakiety w SSISDB lub systemie plików, zainstaluj program SSMS w wersji 18,5 lub nowszej.
- Aby uruchomić pakiety w sklepie pakietu, zainstaluj program SSMS w wersji 18,6 lub nowszej.

Musisz również [udostępnić środowisko Azure-SSIS Integration Runtime](./tutorial-deploy-ssis-packages-azure.md) w Azure Data Factory. Używa wystąpienia zarządzanego SQL jako serwera punktu końcowego.

## <a name="run-an-ssis-package-in-ssisdb"></a>Uruchamianie pakietu usług SSIS w SSISDB

W ramach tej procedury należy użyć agenta wystąpienia zarządzanego SQL, aby wywołać pakiet SSIS, który jest przechowywany w SSISDB.

1. W najnowszej wersji programu SSMS Połącz się z wystąpieniem zarządzanym SQL.
1. Utwórz nowe zadanie agenta i nowy krok zadania. W obszarze **Agent SQL Server** kliknij prawym przyciskiem myszy folder **Jobs (zadania** ), a następnie wybierz pozycję **nowe zadanie**.

   ![Wybrane opcje tworzenia nowego zadania agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na stronie **nowy etap zadania** wybierz pozycję **SQL Server Integration Services pakiet** jako typ.

   ![Opcje tworzenia nowego kroku zadania SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Na karcie **pakiet** wybierz pozycję **Katalog usług SSIS** jako lokalizację pakietu.
1. Ponieważ SSISDB znajduje się w wystąpieniu zarządzanym SQL, nie trzeba określać uwierzytelniania.
1. Określ pakiet SSIS z SSISDB.

   ![Karta pakiet z opcjami wyboru dla typu źródła pakietu](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. Na karcie **Konfiguracja** można:
  
   - Określ wartości parametrów w obszarze **Parametry**.
   - Zastąp wartości w obszarze **menedżerów połączeń**.
   - Zastąp Właściwość i wybierz poziom rejestrowania w obszarze **Zaawansowane**.

   ![Karta Konfiguracja z opcjami wyboru dla typu źródła pakietu](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Wybierz **przycisk OK** , aby zapisać konfigurację zadania agenta.
1. Uruchom zadanie agenta, aby uruchomić pakiet SSIS.

## <a name="run-an-ssis-package-in-the-file-system"></a>Uruchamianie pakietu usług SSIS w systemie plików

W ramach tej procedury należy użyć agenta wystąpienia zarządzanego SQL, aby uruchomić pakiet SSIS, który jest przechowywany w systemie plików.

1. W najnowszej wersji programu SSMS Połącz się z wystąpieniem zarządzanym SQL.
1. Utwórz nowe zadanie agenta i nowy krok zadania. W obszarze **Agent SQL Server** kliknij prawym przyciskiem myszy folder **Jobs (zadania** ), a następnie wybierz pozycję **nowe zadanie**.

   ![Wybrane opcje tworzenia nowego zadania agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na stronie **nowy etap zadania** wybierz pozycję **SQL Server Integration Services pakiet** jako typ.

   ![Opcje tworzenia nowego kroku zadania SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Na karcie **pakiet** :

   1. W obszarze **Lokalizacja pakietu** wybierz pozycję **system plików**.

   1. Dla **typu źródła pliku**:

      - Jeśli pakiet zostanie przekazany do Azure Files, wybierz pozycję **udział plików platformy Azure**.

        ![Opcje dla typu źródła pliku](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        Ścieżka pakietu to **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** .

        W obszarze **poświadczenia dostępu do pliku pakietu** wprowadź nazwę konta i klucz konta usługi Azure File, aby uzyskać dostęp do pliku platformy Azure. Domena jest ustawiona na **platformę Azure**.

      - Jeśli pakiet zostanie przekazany do udziału sieciowego, wybierz pozycję **udział sieciowy**.

        Ścieżka pakietu jest ścieżką UNC pliku pakietu z rozszerzeniem. dtsx.

        Wprowadź odpowiednią domenę, nazwę użytkownika i hasło, aby uzyskać dostęp do pliku pakietu udziału sieciowego.
   1. Jeśli plik pakietu jest szyfrowany przy użyciu hasła, wybierz opcję **hasło szyfrowania** i wprowadź hasło.
1. Na karcie **konfiguracje** wprowadź ścieżkę pliku konfiguracji, jeśli potrzebujesz pliku konfiguracji do uruchomienia pakietu usług SSIS.
   Jeśli konfiguracja zostanie przechowana w Azure Files, jej ścieżka konfiguracji będzie **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Na karcie **Opcje wykonywania** , możesz wybrać, czy użyć **uwierzytelniania systemu Windows** lub **32-bitowego środowiska uruchomieniowego** , aby uruchomić pakiet SSIS.
1. Na karcie **Rejestrowanie** możesz wybrać ścieżkę rejestrowania i odpowiednie poświadczenia dostępu do rejestrowania do przechowywania plików dziennika. 
   Domyślnie ścieżka rejestrowania jest taka sama jak ścieżka folderu pakietu, a poświadczenia dostępu do logowania są takie same, jak poświadczenia dostępu do pakietu.
   W przypadku przechowywania dzienników w Azure Files ścieżka rejestrowania będzie **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Na karcie **ustawienia wartości** możesz wprowadzić ścieżkę i wartość właściwości, aby zastąpić właściwości pakietu.

   Na przykład aby zastąpić wartość zmiennej użytkownika, wprowadź jej ścieżkę w następującym formacie: **`\Package.Variables[User::<variable name>].Value`** .
1. Wybierz **przycisk OK** , aby zapisać konfigurację zadania agenta.
1. Uruchom zadanie agenta, aby uruchomić pakiet SSIS.

## <a name="run-an-ssis-package-in-the-package-store"></a>Uruchamianie pakietu usług SSIS w sklepie pakietu

W ramach tej procedury należy użyć agenta wystąpienia zarządzanego SQL, aby uruchomić pakiet SSIS, który jest przechowywany w magazynie pakietów Azure-SSIS IR.

1. W najnowszej wersji programu SSMS Połącz się z wystąpieniem zarządzanym SQL.
1. Utwórz nowe zadanie agenta i nowy krok zadania. W obszarze **Agent SQL Server** kliknij prawym przyciskiem myszy folder **Jobs (zadania** ), a następnie wybierz pozycję **nowe zadanie**.

   ![Wybrane opcje tworzenia nowego zadania agenta](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na stronie **nowy etap zadania** wybierz pozycję **SQL Server Integration Services pakiet** jako typ.

   ![Opcje tworzenia nowego kroku zadania SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Na karcie **pakiet** :

   1. W obszarze **Lokalizacja pakietu** wybierz pozycję **Magazyn pakietów**.

   1. **Ścieżka pakietu**:

      Ścieżka pakietu to **`<package store name>\<folder name>\<package name>`** .

      ![Opcje typu magazynu pakietów](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. Jeśli plik pakietu jest szyfrowany przy użyciu hasła, wybierz opcję **hasło szyfrowania** i wprowadź hasło.
1. Na karcie **konfiguracje** wprowadź ścieżkę pliku konfiguracji, jeśli potrzebujesz pliku konfiguracji do uruchomienia pakietu usług SSIS.
   Jeśli konfiguracja zostanie przechowana w Azure Files, jej ścieżka konfiguracji będzie **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Na karcie **Opcje wykonywania** , możesz wybrać, czy użyć **uwierzytelniania systemu Windows** lub **32-bitowego środowiska uruchomieniowego** , aby uruchomić pakiet SSIS.
1. Na karcie **Rejestrowanie** możesz wybrać ścieżkę rejestrowania i odpowiednie poświadczenia dostępu do rejestrowania do przechowywania plików dziennika.
   Domyślnie ścieżka rejestrowania jest taka sama jak ścieżka folderu pakietu, a poświadczenia dostępu do logowania są takie same, jak poświadczenia dostępu do pakietu.
   W przypadku przechowywania dzienników w Azure Files ścieżka rejestrowania będzie **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Na karcie **ustawienia wartości** możesz wprowadzić ścieżkę i wartość właściwości, aby zastąpić właściwości pakietu.

   Na przykład aby zastąpić wartość zmiennej użytkownika, wprowadź jej ścieżkę w następującym formacie: **`\Package.Variables[User::<variable name>].Value`** .
1. Wybierz **przycisk OK** , aby zapisać konfigurację zadania agenta.
1. Uruchom zadanie agenta, aby uruchomić pakiet SSIS.

## <a name="cancel-ssis-package-execution"></a>Anuluj wykonywanie pakietu SSIS

Aby anulować wykonywanie pakietu z zadania agenta wystąpienia zarządzanego SQL, wykonaj następujące czynności zamiast bezpośredniego zatrzymywania zadania agenta:

1. Znajdź identyfikator **jobId** agenta SQL z **msdb.dbo.syszadań**.
1. Znajdź odpowiednie **EXECUTIONID** SSIS na podstawie identyfikatora zadania, korzystając z tego zapytania:
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   Jeśli pakiety SSIS są w SSISDB, użyj **cution_parameter_valuesssisdb.internal.exe** jako tabeli do wykonania zadania. Jeśli pakiety SSIS znajdują się w systemie plików, użyj **ssisdb.internal.execution_parameter_values_noncatalog**.
1. Kliknij prawym przyciskiem myszy katalog SSISDB, a następnie wybierz pozycję **aktywne operacje**.

   !["Operacje aktywne" w menu skrótów dla katalogu SSISDB](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Zatrzymywanie odpowiedniej operacji na podstawie **ExecutionID**.

## <a name="next-steps"></a>Następne kroki
Możesz również zaplanować pakiety SSIS przy użyciu Azure Data Factory. Aby uzyskać instrukcje krok po kroku, zobacz [Azure Data Factory wyzwalacza zdarzeń](how-to-create-event-trigger.md).