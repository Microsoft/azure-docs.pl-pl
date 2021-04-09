---
title: Przekształcanie danych przy użyciu programu Hive na platformie Azure Virtual Network przy użyciu Azure Portal
description: Ten samouczek zawiera instrukcje krok po kroku przekształcania danych przy użyciu działania programu Hive w usłudze Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/04/2018
ms.openlocfilehash: 4c8ae67720cf6ac9d577286898b95cdd10f38152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377962"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>Przekształcanie danych na platformie Azure Virtual Network przy użyciu działania programu Hive w Azure Data Factory przy użyciu Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku witryna Azure Portal umożliwia tworzenie potoku usługi Data Factory przekształcającego dane przy użyciu działania programu Hive w klastrze usługi HDInsight, który znajduje się w usłudze Azure Virtual Network (VNet). Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Tworzenie własnego środowiska Integration Runtime
> * Tworzenie połączonych usług Azure Storage i Azure HDInsight
> * Tworzenie potoku przy użycia działania Hive
> * Wyzwalanie uruchomienia potoku
> * Monitorowanie działania potoku 
> * Sprawdzanie danych wyjściowych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Konto usługi Azure Storage**. Utwórz skrypt programu Hive i przekaż go do usługi Azure Storage. Dane wyjściowe skryptu programu Hive są przechowywane na tym koncie magazynu. W tym przykładzie klaster usługi HDInsight używa tego konta usługi Azure Storage jako magazynu głównego. 
- **Virtual Network platformy Azure.** Jeśli nie masz sieci wirtualnej platformy Azure, utwórz ją, wykonując [te instrukcje](../virtual-network/quick-create-portal.md). W tym przykładzie usługa HDInsight znajduje się w usłudze Azure Virtual Network. Oto przykładowa konfiguracja usługi Azure Virtual Network. 

    ![Tworzenie sieci wirtualnej](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **Klaster usługi HDInsight.** Utwórz klaster usługi HDInsight i przyłącz go do sieci wirtualnej utworzonej w poprzednim kroku, postępując zgodnie z opisem podanym w tym artykule: [Extend Azure HDInsight using an Azure Virtual Network (Rozszerzenie usługi Azure HDInsight za pomocą usługi Azure Virtual Network)](../hdinsight/hdinsight-plan-virtual-network-deployment.md). Oto przykładowa konfiguracja usługi HDInsight w sieci wirtualnej. 

    ![Usługa HDInsight w sieci wirtualnej](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- Zainstalowanie programu **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).
- **Maszyna wirtualna**. Utwórz maszynę wirtualną platformy Azure i przyłącz ją do tej samej sieci wirtualnej, która zawiera Twój klaster usługi HDInsight. Aby uzyskać szczegółowe informacje, zobacz [How to create virtual machines (Jak utworzyć maszyny wirtualne)](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Przekazywanie skryptu programu Hive do konta usługi Blob Storage

1. Utwórz plik SQL programu Hive SQL o nazwie **hivescript.hql** i następującej zawartości:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. W usłudze Azure Blob Storage utwórz kontener o nazwie **adftutorial**, jeśli nie istnieje.
3. Utwórz folder o nazwie **hivescripts**.
4. Przekaż plik **hivescript.hql** do podfolderu **hivescripts**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).    
2. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy->Fabryka danych](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Na stronie **Nowa fabryka danych** wprowadź jako **nazwę** wartość **ADFTutorialHiveFactory**. 
      
     ![Strona Nowa fabryka danych](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych Azure musi być **globalnie unikatowa**. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaMyAzureSsisDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
    *Nazwa fabryki danych "wartość myazuressisdatafactory" jest niedostępna*
3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz opcję **V2** w obszarze **Wersja**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko lokalizacje obsługiwane na potrzeby tworzenia fabryk danych.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij pozycję **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **wdrażanie fabryki danych**. 

     ![kafelek Wdrażanie fabryki danych](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
    ![Strona główna fabryki danych](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Kliknij pozycję **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Data Factory.
11. Na stronie **Wprowadzenie** przejdź do karty **Edycja** w lewym panelu, jak pokazano na poniższej ilustracji: 

    ![Karta Edycja](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime
Ponieważ klaster usługi Hadoop znajduje się wewnątrz sieci wirtualnej, należy zainstalować środowisko Integration Runtime (Self-hosted) w tej samej sieci wirtualnej. W tej sekcji utworzysz nową maszynę wirtualną, dołączysz ją do tej samej sieci wirtualnej i zainstalujesz na niej środowisko IR (Self-hosted). Środowisko IR (Self-hosted) umożliwia usłudze Data Factory wysyłanie żądań przetwarzania do usługi obliczeniowej, takiej jak HDInsight, w sieci wirtualnej. Pozwala ono również na przenoszenie danych do i z magazynów danych w sieci wirtualnej na platformę Azure. Środowisko IR (Self-hosted) jest używane, jeśli magazyn danych lub obliczenie znajduje się również w środowisku lokalnym. 

1. W interfejsie użytkownika usługi Azure Data Factory kliknij pozycję **Połączenia** w dolnej części okna, przejdź do karty **Środowiska Integration Runtime**, a następnie kliknij przycisk **+ Nowy** na pasku narzędzi. 

   ![Menu Nowe środowisko Integration Runtime](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. W oknie **Konfiguracja środowiska Integration Runtime** wybierz opcję **Wykonuj przenoszenie danych i wysyłaj działania do obliczeń zewnętrznych**, a następnie kliknij pozycję **Dalej**. 

   ![Wybieranie opcji Wykonuj przenoszenie danych i wysyłaj działania](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Wybierz pozycję **Sieć prywatna** i kliknij pozycję **Dalej**.
    
   ![Wybór sieci prywatnej](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Wprowadź ciąg **MySelfHostedIR** w polu **Nazwa** i kliknij pozycję **Dalej**. 

   ![Określanie nazwy środowiska Integration Runtime](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Skopiuj **klucz uwierzytelniania** środowiska Integration Runtime, klikając przycisk kopiowania, i zapisz go. Nie zamykaj okna. Ten klucz jest używany do rejestrowania środowiska IR zainstalowanego na maszynie wirtualnej. 

   ![Kopiowanie klucza uwierzytelniania](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Instalowanie środowiska IR na maszynie wirtualnej

1. Na maszynę wirtualną platformy Azure pobierz [środowisko Integration Runtime (Self-hosted)](https://www.microsoft.com/download/details.aspx?id=39717). Użyj **klucza uwierzytelniania** uzyskanego w poprzednim kroku, aby ręcznie zarejestrować własne środowisko Integration Runtime. 

    ![Rejestrowanie środowiska Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. Po pomyślnym zakończeniu rejestrowania środowiska Integration Runtime (self-hosted) zostanie wyświetlony poniższy komunikat. 
   
    ![Pomyślnie zarejestrowano](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Kliknij pozycję **Uruchom program Configuration Manager**. Gdy węzeł zostanie połączony z usługą w chmurze, zostanie wyświetlona następująca strona: 
   
    ![Węzeł jest połączony](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Środowisko IR (Self-hosted) w interfejsie użytkownika usługi Azure Data Factory

1. W **interfejsie użytkownika usługi Azure Data Factory** powinna być widoczna nazwa samodzielnie hostowanej maszyny wirtualnej.

   ![Istniejące węzły samodzielnie hostowane](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Kliknij pozycję **Zakończ**, aby zamknąć okno **Konfiguracja środowiska Integration Runtime** okna. Środowisko IR (Self-hosted) pojawi się na liście środowisk Integration Runtime.

   ![Środowisko IR (Self-hosted) na liście](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tej sekcji zredagujesz i wdrożysz dwie połączone usługi:
- **Połączoną usługę Azure Storage**, która łączy konto usługi Azure Storage z fabryką danych. Ten magazyn jest podstawowym magazynem używanym przez Twój klaster usługi HDInsight. W takim przypadku używasz konta usługi Azure Storage do przechowywania skryptu programu Hive i przekazywania skryptu na wyjście.
- **Połączona Usługa HDInsight**. Usługa Azure Data Factory przesyła skrypt programu Hive do tego klastra usługi HDInsight w celu wykonania.

### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. Przejdź do karty **Połączone usługi**, a następnie kliknij pozycję **Nowy**.

   ![Przycisk Nowa połączona usługa](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie kliknij pozycję **Kontynuuj**. 

   ![Wybieranie pozycji Azure Blob Storage](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

    1. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.
    2. Wybierz pozycję **MySelfHostedIR** w polu **Połącz za pośrednictwem środowiska Integration Runtime**.
    3. Wybierz konto usługi Azure Storage dla **nazwy konta magazynu**. 
    4. Aby przetestować połączenie z kontem magazynu, kliknij pozycję **Testuj połączenie**.
    5. Kliknij pozycję **Zapisz**.
   
        ![Określanie konta usługi Azure Blob Storage](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight

1. Kliknij ponownie pozycję **Nowy**, aby utworzyć kolejną połączoną usługę. 
    
   ![Przycisk Nowa połączona usługa](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Przejdź do karty **Obliczanie**, wybierz pozycję **Azure HDInsight** i kliknij pozycję **Kontynuuj**.

    ![Wybieranie usługi Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. W oknie **Nowa połączona usługa** wykonaj następujące czynności:

    1. Wprowadź ciąg **AzureHDInsightLinkedService** w polu **Nazwa**.
    2. Wybierz pozycję **Użyj własnej usługi HDInsight**. 
    3. Wybierz klaster usługi HDInsight w obszarze **Klaster HDI**. 
    4. Wprowadź **nazwę użytkownika** dla klastra usługi HDInsight.
    5. Wprowadź **hasło** dla użytkownika. 
    
        ![Ustawienia usługi Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

W tym artykule przyjęto założenie, że masz dostęp do klastra za pośrednictwem Internetu. Na przykład możesz połączyć się z klastrem pod adresem `https://clustername.azurehdinsight.net`. Ten adres używa publicznej bramy, która jest niedostępna w przypadku używania sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika (UDR) do ograniczania dostępu z Internetu. Aby fabryka danych mogła przekazać zadania do klastra usługi HDInsight w usłudze Azure Virtual Network, musisz skonfigurować swoją usługę Azure Virtual Network tak, aby adres URL mógł zostać rozpoznany jako prywatny adres IP bramy używany przez usługę HDInsight.

1. W witrynie Azure Portal otwórz sieć wirtualną, w której znajduje się usługa HDInsight. Otwórz interfejs sieciowy mający nazwę zaczynającą się od `nic-gateway-0`. Zanotuj jego prywatny adres IP. Na przykład 10.6.0.15. 
2. Jeśli usługa Azure Virtual Network ma serwer usługi DNS, zaktualizuj rekord usługi DNS tak, aby adres URL klastra usługi HDInsight `https://<clustername>.azurehdinsight.net` można było rozpoznać jako `10.6.0.15`. Jeśli w usłudze Azure Virtual Network nie masz serwera DNS, możesz tymczasowo obejść to, edytując plik hosts (C:\Windows\System32\drivers\etc) wszystkich maszyn wirtualnych, które są zarejestrowane jako węzły środowiska Integration Runtime (Self-hosted) przez dodanie wpisu podobnego do następującego: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Tworzenie potoku 
W tym kroku utworzysz nowy potok za pomocą działania programu Hive. Działanie wykonuje skrypt programu Hive służący do zwracania danych z przykładowej tabeli i zapisania ich w ramach ścieżki zdefiniowanej przez użytkownika.

Pamiętaj o następujących kwestiach:

- Argument **scriptPath** wskazuje ścieżkę do skryptu programu Hive na koncie Azure Storage użytym dla usługi MyStorageLinkedService. W ścieżce jest rozróżniana wielkość liter.
- **Output** jest argumentem używanym w skrypcie programu Hive. Użyj formatu `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`, aby wskazać istniejący folder w usłudze Azure Storage. W ścieżce jest rozróżniana wielkość liter. 

1. W interfejsie użytkownika usługi Data Factory kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **Potok**. 

    ![Menu Nowy potok](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. W przyborniku **Działania** rozwiń pozycję **HDInsight**, a następnie przeciągnij i upuść działanie **Hive** do powierzchni projektanta potoku. 

    ![przeciąganie i upuszczanie działania Hive](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. W oknie właściwości przejdź do karty **Klaster usługi HDI**, a następnie wybierz pozycję **AzureHDInsightLinkedService** w polu **Połączona usługa HDInsight**.

    ![Wybieranie połączonej usługi HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Przejdź do karty **Skrypty** i wykonaj następujące czynności: 

    1. Wybierz pozycję **AzureStorageLinkedService** w polu **Połączona usługa skryptu**. 
    2. W polu **Ścieżka pliku** kliknij pozycję **Przeglądaj magazyn**. 
 
        ![Przeglądanie magazynu](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. W oknie **Wybieranie pliku lub folderu** przejdź do folderu **hivescripts** w kontenerze **adftutorial**, wybierz plik **hivescript.hql**, a następnie kliknij przycisk **Zakończ**.  
        
        ![Wybieranie pliku lub folderu](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Sprawdź, czy w polu **Ścieżka pliku** jest widoczna lokalizacja **adftutorial/hivescripts/hivescript.hql**.

        ![Ustawienia skryptu](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. Na **karcie Skrypt** rozwiń sekcję **Zaawansowane**. 
    6. Kliknij pozycję **Automatycznie wypełniaj ze skryptu** w obszarze **Parametry**. 
    7. Wprowadź wartość parametru **Dane wyjściowe** w następującym formacie: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Na przykład: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Argumenty skryptu](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Aby opublikować artefakty do usługi Data Factory, kliknij pozycję **Opublikuj**.

    ![Zrzut ekranu przedstawia opcję opublikowania w Data Factory.](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku

1. Najpierw zweryfikuj potok, klikając przycisk **Weryfikuj** na pasku narzędzi. Zamknij okno **Dane wyjściowe weryfikacji potoku**, klikając przycisk **strzałkę w prawo (>>)**. 

    ![Weryfikowanie potoku](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Aby wyzwolić uruchomienie potoku, kliknij pozycję Wyzwól na pasku narzędzi, a następnie kliknij pozycję Wyzwól teraz. 

    ![Wyzwól teraz](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Uruchomienie potoku zostanie wyświetlone na liście **Uruchomienia potoku**. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Aby odświeżyć listę, kliknij pozycję **Odśwież**.
4. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniami potoku, kliknij pozycję **Wyświetl uruchomienia działań** w kolumnie **Akcje**. Inne linki akcji służą do zatrzymywania/ponownego uruchamiania potoku. 

    ![Wyświetlanie uruchomień działania](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Zobaczysz tylko jedno uruchomienie działania, ponieważ w potoku typu **HDInsightHive** jest tylko jedno działanie. Aby wrócić do poprzedniego widoku, kliknij link **Potoki** w górnej części strony.

    ![Uruchomienia działania](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Sprawdź, czy plik wyjściowy znajduje się w folderze **outputfolder** kontenera **adftutorial**. 

    ![Plik wyjściowy](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Tworzenie własnego środowiska Integration Runtime
> * Tworzenie połączonych usług Azure Storage i Azure HDInsight
> * Tworzenie potoku przy użycia działania Hive
> * Wyzwalanie uruchomienia potoku
> * Monitorowanie działania potoku 
> * Sprawdzanie danych wyjściowych

Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Branching and chaining Data Factory control flow (Rozgałęzianie i tworzenie łańcucha przepływu sterowania fabryki danych)](tutorial-control-flow-portal.md)