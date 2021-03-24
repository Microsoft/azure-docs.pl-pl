---
title: Apache Hive zapytań za pomocą sterownika ODBC & PowerShell — Azure HDInsight
description: Użyj sterownika Microsoft Hive ODBC i programu PowerShell, aby wysyłać zapytania do Apache Hive klastrów w usłudze Azure HDInsight.
keywords: Hive, Hive ODBC, PowerShell
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34ee7eb25b1d106ea8bb53197d69ca5a9d528773
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871609"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Samouczek: zapytanie Apache Hive z ODBC i programu PowerShell

Sterowniki ODBC firmy Microsoft zapewniają elastyczny sposób współpracy z różnymi rodzajami źródeł danych, w tym Apache Hive. Możesz napisać kod w językach skryptów, takich jak program PowerShell korzystający ze sterowników ODBC, aby otworzyć połączenie z klastrem Hive, przekazać wybrane zapytanie i wyświetlić wyniki.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Pobieranie i Instalowanie sterownika Microsoft Hive ODBC
> * Tworzenie źródła danych Apache Hive ODBC połączonego z klastrem
> * Badanie przykładowych informacji z klastra przy użyciu programu PowerShell

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* Interaktywny klaster zapytań w usłudze HDInsight. Aby go utworzyć, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz **zapytanie interaktywne** jako typ klastra.

## <a name="install-microsoft-hive-odbc-driver"></a>Zainstaluj sterownik Microsoft Hive ODBC

Pobierz i zainstaluj [Sterownik Microsoft Hive ODBC](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Tworzenie źródła danych Apache Hive ODBC

Poniższe kroki przedstawiają sposób tworzenia źródła danych Apache Hive ODBC.

1. W systemie Windows przejdź do   >  **menu Start Narzędzia administracyjne systemu Windows**  >  **źródła danych ODBC (32-bitowe)/(64-bitowe)**.  Otworzy się okno **Administrator źródła danych ODBC** .

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png " alt-text="Administrator źródła danych OBDC" border="true":::

1. Na karcie **DSN użytkownika** wybierz pozycję **Dodaj** , aby otworzyć okno **Utwórz nowe źródło danych** .

1. Wybierz pozycję **Sterownik Microsoft Hive ODBC**, a następnie wybierz pozycję **Zakończ** , aby otworzyć okno **instalacji sterownik Microsoft Hive ODBC DSN** .

1. Wpisz lub wybierz poniższe wartości:

   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Hosty |Wprowadź `CLUSTERNAME.azurehdinsight.net`. Na przykład `myHDICluster.azurehdinsight.net` |
   |  Port |Użyj portu **443**.|
   |  baza danych |Użyj **domyślnego**. |
   |  Mechanism (Mechanizm) |Wybieranie **usługi HDInsight systemu Windows Azure** |
   |  Nazwa użytkownika |Wprowadź nazwę użytkownika HTTP klastra usługi HDInsight. Domyślna nazwa użytkownika to **admin**. |
   |  Hasło |Wprowadź hasło użytkownika klastra usługi HDInsight. Zaznacz pole wyboru **Zapisz hasło (zaszyfrowane)**.|

1. Opcjonalne: Wybierz **Opcje zaawansowane**.  

   | Parametr | Opis |
   | --- | --- |
   |  Użyj zapytania natywnego |Po wybraniu tego sterownika ODBC nie próbuje skonwertować TSQL na HiveQL. Użyj tej opcji tylko wtedy, gdy masz 100% pewności, że przesyłasz czyste instrukcje HiveQL. W przypadku nawiązywania połączenia z usługą SQL Server lub Azure SQL Database należy pozostawić zaznaczenie pola wyboru. |
   |  Liczba pobranych wierszy na blok |Podczas pobierania dużej liczby rekordów dostrajanie tego parametru może być wymagane w celu zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciągu, długość kolumny binarnej, skala kolumn dziesiętnych |Długości i precyzji typu danych mogą mieć wpływ na sposób zwracania danych. Powodują one zwrócenie nieprawidłowych informacji ze względu na utratę dokładności i obcinanie. |

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png " alt-text="Zaawansowane opcje konfiguracji DSN" border="true":::

1. Wybierz **test** , aby przetestować źródło danych. Gdy źródło danych jest prawidłowo skonfigurowane, wynik testu pokazuje **powodzenie**.  

1. Wybierz **przycisk OK** , aby zamknąć okno testowe.  

1. Wybierz **przycisk OK** , aby zamknąć okno **instalacji sterownik Microsoft Hive ODBC DSN** .  

1. Wybierz **przycisk OK** , aby zamknąć okno **Administrator źródła danych ODBC** .  

## <a name="query-data-with-powershell"></a>Wykonywanie zapytań dotyczących danych za pomocą programu PowerShell

Poniższy skrypt programu PowerShell to funkcja ODBC do klastra Hive.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Poniższy fragment kodu używa funkcji powyżej, aby wykonać zapytanie w klastrze zapytań interaktywnych, który został utworzony na początku samouczka. Zamień na `DATASOURCENAME` **nazwę źródła danych** określoną na ekranie **instalacji sterownik Microsoft Hive ODBC DSN** . Gdy zostanie wyświetlony monit o podanie poświadczeń, wprowadź nazwę użytkownika i hasło wprowadzone w obszarze **Nazwa użytkownika logowania klastra** i **hasło logowania klastra** podczas tworzenia klastra.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, klaster usługi HDInsight i konto magazynu nie są już potrzebne, usuń je. W tym celu wybierz grupę zasobów, w której klaster został utworzony, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak za pomocą sterownika Microsoft Hive ODBC i programu PowerShell pobrać dane z klastra interakcyjnych zapytań usługi Azure HDInsight.

> [!div class="nextstepaction"]
> [Łączenie programu Excel z Apache Hive przy użyciu interfejsu ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
