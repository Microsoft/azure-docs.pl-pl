---
title: Wdrażanie Azure Monitor rozwiązań SAP za pomocą Azure Portal
description: Wdrażanie Azure Monitor rozwiązań SAP za pomocą Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 908de54ee66772f3eb648895529c843675c3bf15
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538637"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Wdrażanie Azure Monitor dla rozwiązań SAP za pomocą Azure Portal

Azure Monitor dla rozwiązań SAP zasoby można tworzyć za pomocą [Azure Portal](https://azure.microsoft.com/features/azure-portal). Ta metoda zapewnia oparty na przeglądarce interfejs użytkownika do wdrażania Azure Monitor dla rozwiązań SAP i konfigurowania dostawców.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com

## <a name="create-monitoring-resource"></a>Tworzenie zasobu monitorowania

1. Wybierz **Azure Monitor dla rozwiązań SAP** z **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Obraz przedstawia wybieranie Azure Monitor rozwiązań SAP z Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Na **karcie Podstawowe** podaj wymagane wartości. Jeśli ma to zastosowanie, możesz użyć istniejącego obszaru roboczego usługi Log Analytics.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Wyświetlanie opcji Azure Portal konfiguracji." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Podczas wybierania sieci wirtualnej upewnij się, że systemy, które chcesz monitorować, są dostępne z poziomu tej sieci wirtualnej. 

   > [!IMPORTANT]
   > Wybranie **opcji Udostępnij** w celu udostępniania danych firmie Microsoft umożliwia naszym zespołom pomocy technicznej zapewnienie dodatkowej pomocy technicznej.

## <a name="configure-providers"></a>Konfigurowanie dostawców

### <a name="sap-netweaver-provider"></a>Dostawca SAP NetWeaver

#### <a name="prerequisites-for-adding-netweaver-provider"></a>Wymagania wstępne dotyczące dodawania dostawcy NetWeaver

"Usługa uruchamiania SAP" udostępnia wiele usług, w tym monitorowanie systemu SAP. Używamy mechanizmu "SAPControl", który jest interfejsem usługi internetowej protokołu SOAP, który uwidacznia te możliwości. Ten interfejs usługi sieci Web SAPControl rozróżnia metody chronionych i [niechronionych](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) usług sieci Web. Aby można było pobrać określone metryki, należy wyłączyć ochronę niektórych metod. Aby wyłączyć ochronę wymaganych metod dla bieżącej wersji, wykonaj poniższe kroki dla każdego systemu SAP:

1. Otwieranie połączenia z graficznym interfejsem użytkownika sap do serwera SAP
2. Logowanie przy użyciu konta administracyjnego
3. Wykonywanie transakcji RZ10
4. Wybierz odpowiedni profil (DOMYŚLNY). PFL)
5. Wybierz pozycję "Konserwacja rozszerzona", a następnie kliknij pozycję Zmień. 
6. Zmodyfikuj wartość parametru ,,/protectedwebmethods" na "SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList" na zalecane ustawienie, a następnie kliknij przycisk Kopiuj
7. Wstecz i wybierz pozycję Profile->Save (Zapisz profil)
8. Uruchom ponownie system, aby parametr został uruchomiony

>[!Tip]
> Użyj listy Access Control (ACL), aby filtrować dostęp do portu serwera. Zapoznaj się z jego [uwagami na temat oprogramowania SAP](https://launchpad.support.sap.com/#/notes/1495075)

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Instalowanie dostawcy NetWeaver na Azure Portal
1.  Upewnij się, że zostały wykonane kroki wymagań wstępnych i serwer został uruchomiony ponownie
2.  Na stronie Azure Portal w obszarze AMS wybierz pozycję Dodaj dostawcę, a następnie z listy rozwijanej wybierz pozycję SAP NetWeaver
3.  Wprowadź nazwę hosta systemu SAP i poddomenę (jeśli dotyczy)
4.  Wprowadź numer wystąpienia odpowiadający wprowadzonej nazwie hosta 
5.  Wprowadź identyfikator systemowy (SID)
6.  Po zakończeniu wybierz pozycję Dodaj dostawcę
7.  W razie potrzeby dodaj dodatkowych dostawców lub wybierz pozycję Przejrzyj i utwórz, aby ukończyć wdrożenie

![image (obraz)](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

### <a name="sap-hana-provider"></a>SAP HANA dostawcy 

1. Wybierz **kartę Dostawca,** aby dodać dostawców, których chcesz skonfigurować. Możesz dodać wielu dostawców jeden po drugim lub dodać ich po wdrożeniu zasobu monitorowania. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Wyświetla kartę dostawcy, na która można dodać dodatkowych dostawców do Azure Monitor dla rozwiązań SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Wybierz **pozycję Dodaj dostawcę** i **SAP HANA** pozycję z listy rozwijanej. 

   > [!IMPORTANT]
   > Upewnij się, SAP HANA jest skonfigurowany dla SAP HANA "master".

3. Wprowadź prywatny adres IP serwera HANA.

4. Wprowadź nazwę dzierżawy bazy danych, której chcesz użyć. Możesz jednak wybrać dowolną dzierżawę. Zalecamy użycie **bazy danych SYSTEMDB,** ponieważ umożliwia to korzystanie z szerszego zakresu obszarów monitorowania. 

5. Wprowadź numer portu SQL skojarzony z bazą danych HANA. Numer portu powinien mieć format **[3]**  +  **[instance#]**  +  **[13]**. Na przykład 30013. 

6. Wprowadź nazwę użytkownika bazy danych, której chcesz użyć. Upewnij się, że użytkownik bazy danych ma **przypisane role monitorowania** i odczytu katalogu.  

7. Po zakończeniu wybierz pozycję **Dodaj dostawcę**. W razie potrzeby dodaj kolejnych dostawców lub wybierz **pozycję Przejrzyj i utwórz,** aby ukończyć wdrożenie.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Obraz opcji konfiguracji podczas dodawania informacji o dostawcy." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::
   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server dostawcy

1. Przed dodaniem Microsoft SQL Server należy uruchomić następujący skrypt w programie SQL Server Management Studio, aby utworzyć użytkownika z odpowiednimi uprawnieniami wymaganymi do skonfigurowania dostawcy.

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

2. Wybierz **pozycję Dodaj dostawcę** i **Microsoft SQL Server** pozycję z listy rozwijanej. 

3. Wypełnij pola, używając informacji skojarzonych z Microsoft SQL Server. 

4. Po zakończeniu wybierz pozycję **Dodaj dostawcę**. W razie potrzeby dodaj kolejnych dostawców lub wybierz **pozycję Przejrzyj i utwórz,** aby ukończyć wdrożenie.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Obraz przedstawia informacje związane z dodawaniem Microsoft SQL Server dostawcy." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Dostawca klastra wysokiej dostępności (Pacemaker)

1. Wybierz **pozycję Klaster wysokiej dostępności (Pacemaker)** z listy rozwijanej. 

   > [!IMPORTANT]
   > Aby skonfigurować dostawcę klastra wysokiej dostępności (Pacemaker), upewnij się, że ha_cluster_provider jest zainstalowany w każdym węźle. Aby uzyskać więcej informacji, zobacz [temat Eksporter klastrów o ha-](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Wprowadź punkt końcowy Prometheus w postaci http://IP:9664/metrics . 
 
3. Wprowadź identyfikator systemu (SID), nazwę hosta i nazwę klastra.

4. Po zakończeniu wybierz pozycję **Dodaj dostawcę**. W razie potrzeby dodaj kolejnych dostawców lub wybierz **pozycję Przejrzyj i utwórz,** aby ukończyć wdrożenie.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Obraz przedstawia opcje związane z dostawcą pacemaker klastra ha." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::

### <a name="os-linux-provider"></a>Dostawca systemu operacyjnego (Linux) 

1. Wybierz pozycję System operacyjny (Linux) z listy rozwijanej 

   >[!IMPORTANT]
   > Aby skonfigurować dostawcę systemu operacyjnego (Linux), upewnij się, że najnowsza wersja programu Node_Exporter jest zainstalowana na każdym hoście (BareMetal lub VM), który chcesz monitorować. Użyj tego [linku,](https://prometheus.io/download/#node_exporter) aby znaleźć najnowszą wersję. Aby uzyskać więcej informacji, zobacz [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Wprowadź nazwę, która będzie identyfikatorem wystąpienia baremetalu.
3. Wprowadź punkt końcowy eksportera węzłów w postaci http://IP:9100/metrics .

   >[!IMPORTANT]
   >Użyj prywatnego adresu IP hosta z systemem Linux. Upewnij się, że host i zasób usługi AMS znajdują się w tej samej sieci wirtualnej. 

   >[!Note]
   > Port zapory "9100" powinien zostać otwarty na hoście z systemem Linux.
   >W przypadku korzystania z polecenia firewall-cmd: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload Jeśli używasz narzędzia ufw: ufw allow 9100/tcp ufw reload

    >[!Tip]
    > Jeśli host systemu Linux jest maszyną wirtualną platformy Azure, upewnij się, że wszystkie odpowiednie sieciowe konta zezwalają na ruch przychodzący na porcie 9100 z sieci wirtualnej "VirtualNetwork" jako źródło.
 
5. Po zakończeniu wybierz pozycję **Dodaj dostawcę.** W razie potrzeby dodaj kolejnych dostawców lub wybierz **pozycję Przejrzyj i utwórz,**   aby ukończyć wdrożenie. 


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Monitor dla rozwiązań SAP](azure-monitor-overview.md)
