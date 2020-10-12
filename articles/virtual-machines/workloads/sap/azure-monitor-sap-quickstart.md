---
title: Wdróż Azure Monitor dla rozwiązań SAP z Azure Portal
description: Wdróż Azure Monitor dla rozwiązań SAP z Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.date: 08/17/2020
ms.openlocfilehash: 2c3b11ca0c2bb916a5a3fcaf50eb99c7db3aa8e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88643003"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Wdrażanie Azure Monitor dla rozwiązań SAP z Azure Portal

Azure Monitor dla zasobów rozwiązań SAP można utworzyć za pomocą [Azure Portal](https://azure.microsoft.com/features/azure-portal). Ta metoda zapewnia interfejs użytkownika oparty na przeglądarce służący do wdrażania Azure Monitor dla rozwiązań SAP i konfigurowania dostawców.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com

## <a name="create-monitoring-resource"></a>Utwórz zasób monitorowania

1. Wybierz **Azure monitor dla rozwiązań SAP** z **witryny Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Obraz przedstawia wybór oferty Azure Monitor dla rozwiązań SAP z witryny Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Na karcie **podstawy** podaj wymagane wartości. Jeśli ma to zastosowanie, można użyć istniejącego obszaru roboczego Log Analytics.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Obraz przedstawia wybór oferty Azure Monitor dla rozwiązań SAP z witryny Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. W przypadku wybrania sieci wirtualnej upewnij się, że systemy, które mają być monitorowane, są dostępne z poziomu tej wirtualnej. 

   > [!IMPORTANT]
   > Wybranie pozycji **Udostępnij** dla udostępniania danych w firmie Microsoft pozwala naszym zespołom pomocy technicznej zapewnić dodatkową pomoc techniczną.

## <a name="configure-providers"></a>Konfiguruj dostawców

### <a name="sap-hana-provider"></a>Dostawca SAP HANA 

1. Wybierz kartę **dostawca** , aby dodać dostawców, które chcesz skonfigurować. Możesz dodać wielu dostawców jeden po drugim lub dodać je po wdrożeniu zasobu monitorowania. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Obraz przedstawia wybór oferty Azure Monitor dla rozwiązań SAP z witryny Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Wybierz pozycję **Dodaj dostawcę** i wybierz pozycję **SAP HANA** z listy rozwijanej. 

3. Wprowadź prywatny adres IP dla serwera HANA.

4. Wprowadź nazwę dzierżawy bazy danych, której chcesz użyć. Można jednak wybrać dowolną dzierżawę, zalecamy użycie **SYSTEMDB** , ponieważ umożliwia ona szersze wykorzystanie obszarów monitorowania. 

5. Wprowadź numer portu SQL skojarzony z bazą danych platformy HANA. Numer portu musi mieć format **[3]**  +  **[wystąpienie #]**  +  **[13]** lub **[3]**  +  **[wystąpienie #]**  +  **[15]**. Na przykład 30013 lub 30015. 

6. Wprowadź nazwę użytkownika bazy danych, której chcesz użyć. Upewnij się, że użytkownik bazy danych ma przypisane role **monitorowania** i **odczytu katalogu** . 

7. Po zakończeniu wybierz pozycję **Dodaj dostawcę**. W razie potrzeby Kontynuuj dodawanie dodatkowych dostawców lub wybierz pozycję **Przegląd + Utwórz** , aby zakończyć wdrażanie.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Obraz przedstawia wybór oferty Azure Monitor dla rozwiązań SAP z witryny Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Dostawca klastra o wysokiej dostępności (Pacemaker)

1. Z listy rozwijanej wybierz pozycję **klaster o wysokiej dostępności (Pacemaker)** . 

   > [!IMPORTANT]
   > Aby skonfigurować dostawcę klastra o wysokiej dostępności (Pacemaker), upewnij się, że ha_cluster_provider jest zainstalowany w każdym węźle. Aby uzyskać więcej informacji, zobacz [eksport klastra HA](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Wprowadź punkt końcowy Prometheus w postaci http://IP:9664/metrics . 
 
3. Wprowadź identyfikator systemowy (SID), nazwę hosta i nazwa klastra.

4. Po zakończeniu wybierz pozycję **Dodaj dostawcę**. W razie potrzeby Kontynuuj dodawanie dodatkowych dostawców lub wybierz pozycję **Przegląd + Utwórz** , aby zakończyć wdrażanie.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Obraz przedstawia wybór oferty Azure Monitor dla rozwiązań SAP z witryny Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="microsoft-sql-server-provider"></a>Dostawca Microsoft SQL Server

1. Przed dodaniem dostawcy Microsoft SQL Server należy uruchomić następujący skrypt w SQL Server Management Studio, aby utworzyć użytkownika z odpowiednimi uprawnieniami wymaganymi do skonfigurowania dostawcy.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Wybierz pozycję **Dodaj dostawcę** i wybierz pozycję **Microsoft SQL Server** z listy rozwijanej. 

3. Wypełnij pola przy użyciu informacji skojarzonych z Microsoft SQL Server. 

4. Po zakończeniu wybierz pozycję **Dodaj dostawcę**. W razie potrzeby Kontynuuj dodawanie dodatkowych dostawców lub wybierz pozycję **Przegląd + Utwórz** , aby zakończyć wdrażanie.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Obraz przedstawia wybór oferty Azure Monitor dla rozwiązań SAP z witryny Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat Azure monitor dla rozwiązań SAP](azure-monitor-overview.md)
