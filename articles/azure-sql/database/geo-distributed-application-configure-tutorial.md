---
title: Implementowanie rozwiązania rozproszonego geograficznie
description: Dowiedz się, jak skonfigurować bazę danych w Azure SQL Database i aplikacji klienckiej w celu przełączenia w tryb failover do zreplikowanej bazy danych i przetestować tryb failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 14031118a93f5760b302e4d2b76334205be6d087
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789628"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Samouczek: implementowanie rozproszonej geograficznie bazy danych (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Skonfiguruj bazę danych w SQL Database i aplikacji klienckiej w celu przełączenia w tryb failover do zdalnego regionu i przetestuj plan trybu failover. Omawiane kwestie:

> [!div class="checklist"]
>
> - Tworzenie [grupy trybu failover](auto-failover-group-overview.md)
> - Uruchamianie aplikacji języka Java w celu wykonywania zapytań do bazy danych w SQL Database
> - Testowanie pracy w trybie failover

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby ukończyć ten samouczek, upewnij się, że zainstalowano następujące elementy:

- [Azure PowerShell](/powershell/azure/)
- Pojedyncza baza danych w Azure SQL Database. Aby utworzyć jedno użycie,
  - [Witryna Azure Portal](single-database-create-quickstart.md)
  - [Interfejs wiersza polecenia platformy Azure](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > Samouczek używa przykładowej bazy danych *AdventureWorksLT* .

- Java i Maven, zobacz [Tworzenie aplikacji przy użyciu SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), wyróżnianie **języka Java** i wybieranie środowiska, a następnie wykonaj kroki opisane poniżej.

> [!IMPORTANT]
> Należy pamiętać o skonfigurowaniu reguł zapory do korzystania z publicznego adresu IP komputera, na którym wykonywane są kroki opisane w tym samouczku. Reguły zapory na poziomie bazy danych zostaną automatycznie zreplikowane na serwerze pomocniczym.
>
> Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zapory na poziomie bazy danych](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) lub Określanie adresu IP używanego dla reguły zapory na poziomie serwera dla komputera, zobacz [Tworzenie zapory na poziomie serwera](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Tworzenie grupy trybu failover

Korzystając z Azure PowerShell, Utwórz [grupy trybu failover](auto-failover-group-overview.md) między istniejącym serwerem a nowym serwerem w innym regionie. Następnie Dodaj przykładową bazę danych do grupy trybu failover.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Aby utworzyć grupę trybu failover, uruchom następujący skrypt:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!IMPORTANT]
> Uruchom `az login` , aby zalogować się do platformy Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Ustawienia replikacji geograficznej można także zmienić w Azure Portal, wybierając bazę danych, a następnie **Ustawienia**  >  **replikacja geograficzna** .

![Ustawienia replikacji geograficznej](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>Uruchamianie przykładowego projektu

1. W konsoli programu Utwórz projekt Maven za pomocą następującego polecenia:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Wpisz **Y** i naciśnij klawisz **Enter** .

1. Zmień katalogi na nowy projekt.

   ```bash
   cd SqlDbSample
   ```

1. Korzystając z ulubionego edytora, Otwórz plik *pom.xml* w folderze projektu.

1. Dodaj sterownik JDBC firmy Microsoft dla zależności SQL Server, dodając następującą `dependency` sekcję. Zależność należy wkleić w większej `dependencies` sekcji.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Określ wersję języka Java, dodając ją do `properties` `dependencies` sekcji:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Obsługa plików manifestu przez dodanie `build` sekcji po `properties` sekcji:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Zapisz i zamknij plik *pom.xml* .

1. Otwórz plik *App. Java* znajdujący się w.. \SqlDbSample\src\main\java\com\sqldbsamples i Zastąp zawartość następującym kodem:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Zapisz i zamknij plik *App. Java* .

1. W konsoli poleceń Uruchom następujące polecenie:

   ```bash
   mvn package
   ```

1. Uruchom aplikację, która będzie działać od około 1 godziny do momentu zatrzymania ręcznie, co pozwoli na przeprowadzenie testu pracy w trybie failover.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Testowanie pracy w trybie failover

Uruchom następujące skrypty, aby zasymulować pracę w trybie failover i obserwować wyniki aplikacji. Zwróć uwagę, jak niektóre operacje INSERT i Select będą kończyć się niepowodzeniem podczas migracji bazy danych.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Podczas testu można sprawdzić rolę serwera odzyskiwania po awarii za pomocą następującego polecenia:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Aby przetestować tryb failover:

1. Rozpocznij ręczną pracę awaryjną grupy trybu failover:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Podczas testu można sprawdzić rolę serwera odzyskiwania po awarii za pomocą następującego polecenia:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Aby przetestować tryb failover:

1. Rozpocznij ręczną pracę awaryjną grupy trybu failover:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano bazę danych w Azure SQL Database i aplikację do przejścia w tryb failover do zdalnego regionu i przetestowano plan trybu failover. W tym samouczku omówiono:

> [!div class="checklist"]
>
> - Tworzenie grupy trybu failover replikacji geograficznej
> - Uruchamianie aplikacji języka Java w celu wykonywania zapytań do bazy danych w SQL Database
> - Testowanie pracy w trybie failover

Przejdź do następnego samouczka, jak dodać wystąpienie wystąpienia zarządzanego Azure SQL do grupy trybu failover:

> [!div class="nextstepaction"]
> [Dodaj wystąpienie wystąpienia zarządzanego usługi Azure SQL do grupy trybu failover](../managed-instance/failover-group-add-instance-tutorial.md)