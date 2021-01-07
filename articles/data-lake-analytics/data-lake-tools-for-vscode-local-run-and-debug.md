---
title: Debugowanie zadań U-SQL — kod Azure Data Lake Tools for Visual Studio
description: Dowiedz się, jak używać kodu Azure Data Lake Tools for Visual Studio, aby uruchamiać i debugować zadania U-SQL lokalnie.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 07/14/2017
ms.openlocfilehash: 194fe3494915be9f6784e1596e647885634817ad
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97969014"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Uruchom skrypt U-SQL i Debuguj lokalnie w Visual Studio Code
W tym artykule opisano sposób uruchamiania zadań U-SQL na lokalnym komputerze deweloperskim w celu przyspieszenia wczesnych faz kodowania lub debugowania kodu lokalnie w Visual Studio Code. Aby uzyskać instrukcje dotyczące Azure Data Lake narzędzia dla Visual Studio Code, zobacz [Korzystanie z kodu Azure Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-for-vscode.md).

Tylko instalacje systemu Windows Azure Data Lake Tools for Visual Studio obsługują akcję do lokalnego uruchamiania skryptu U-SQL i debugowania U-SQL lokalnie. Instalacje w systemach operacyjnych macOS i Linux nie obsługują tej funkcji.

## <a name="set-up-the-u-sql-local-run-environment"></a>Konfigurowanie lokalnego środowiska uruchomieniowego U-SQL

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Pobierz pakiet uruchamiania lokalnego** , aby pobrać pakiety.  

   ![Pobierz pakiety zależności ADL LocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Znajdź pakiety zależności z ścieżki pokazanej w okienku **dane wyjściowe** , a następnie zainstaluj BuildTools i Win10SDK 10240. Oto przykładowa ścieżka:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Lokalizowanie pakietów zależności](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2,1 Aby zainstalować **BuildTools**, kliknij przycisk visualcppbuildtools_full.exe w folderze LocalRunDependency, a następnie postępuj zgodnie z instrukcjami kreatora.   

    ![Zainstaluj BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2,2 Aby zainstalować **Win10SDK 10240**, kliknij sdksetup.exe w folderze LocalRunDependency/Win10SDK_10.0.10240 _2, a następnie postępuj zgodnie z instrukcjami kreatora.  

    ![Zainstaluj program Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Skonfiguruj zmienną środowiskową. Ustaw zmienną środowiskową **SCOPE_CPP_SDK** na:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Uruchom lokalną usługę uruchomieniową i Prześlij zadanie U-SQL do konta lokalnego 
W przypadku użytkownika pierwszego czasu Użyj **ADL: Pobierz pakiet Run Local** w celu pobrania lokalnych pakietów Run, jeśli nie [skonfigurowano lokalnego środowiska uruchomieniowego U-SQL](#set-up-the-u-sql-local-run-environment).

1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Start Local Run Service**.   
2. Wybierz pozycję **Akceptuj** , aby zaakceptować postanowienia licencyjne dotyczące oprogramowania firmy Microsoft po raz pierwszy. 

   ![Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Zostanie otwarta konsola cmd. W przypadku użytkowników po raz pierwszy musisz wprowadzić **3**, a następnie odszukać ścieżkę do lokalnego folderu dla danych wejściowych i wyjściowych. Jeśli nie powiodło się zdefiniowanie ścieżki przy użyciu ukośników odwrotnych, spróbuj ukośniki. W przypadku innych opcji można użyć wartości domyślnych.

   ![Narzędzia Data Lake dla Visual Studio Code lokalnego uruchomienia cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, wprowadź **ADL: Prześlij zadanie**, a następnie wybierz pozycję **Local** , aby przesłać zadanie do konta lokalnego.

   ![Data Lake narzędzia dla Visual Studio Code wybierz pozycję lokalne](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po przesłaniu zadania można wyświetlić szczegóły dotyczące przesyłania. Aby wyświetlić szczegóły dotyczące przesłania, wybierz pozycję **jobUrl** w oknie **danych wyjściowych** . Możesz również wyświetlić stan przesłania zadania z konsoli cmd. Wprowadź **7** w konsoli cmd, aby poznać więcej szczegółów zadań.

   ![Data Lake narzędzia dla Visual Studio Code lokalnego uruchamiania ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
    ![ Data Lake narzędzi dla Visual Studio Code lokalnego uruchomienia cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Rozpocznij debugowanie lokalne dla zadania U-SQL  
Dla użytkownika pierwszego czasu:

1. Użyj **ADL: Pobierz pakiet Run Local** w celu pobrania lokalnych pakietów Run, jeśli nie [skonfigurowano lokalnego środowiska uruchomieniowego U-SQL](#set-up-the-u-sql-local-run-environment).
2. Zainstaluj zestaw .NET Core SDK 2,0 zgodnie z opisem w polu komunikat, jeśli nie jest zainstalowany.
 
  ![przypomnienie instalacji dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Zainstaluj język C# dla Visual Studio Code zgodnie z opisem w oknie komunikatu, jeśli nie jest zainstalowany. Kliknij przycisk **Instaluj** , aby kontynuować, a następnie uruchom ponownie programu vscode.

![Przypomnienie o instalacji C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Wykonaj poniższe kroki, aby przeprowadzić debugowanie lokalne:
  
1. Wybierz kombinację klawiszy Ctrl + Shift + P, aby otworzyć paletę poleceń, a następnie wprowadź **ADL: Start Local Run Service**. Zostanie otwarta konsola cmd. Upewnij się, że **element dataroot** jest ustawiony.
2. Ustaw punkt przerwania w kodzie C#.
3. Wróć do edytora skryptów, kliknij prawym przyciskiem myszy i wybierz pozycję **ADL: local Debug**.
    
   ![Data Lake narzędzia do Visual Studio Code wyniku lokalnego debugowania](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Następne kroki
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Opracowywanie języka U-SQL przy użyciu języków Python, R i CSharp na potrzeby Azure Data Lake Analytics w programu vscode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Wprowadzenie do Data Lake Analytics przy użyciu programu PowerShell](data-lake-analytics-get-started-powershell.md)
* [Wprowadzenie do Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-get-started-portal.md)
* [Używanie narzędzi Data Lake Tools for Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Użyj wykazu Data Lake Analytics (U-SQL)](./data-lake-analytics-u-sql-get-started.md)
