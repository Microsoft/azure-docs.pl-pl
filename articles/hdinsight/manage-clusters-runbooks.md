---
title: 'Samouczek: Używanie elementów Runbook Azure Automation do tworzenia klastrów — Azure HDInsight'
description: Dowiedz się, jak tworzyć i usuwać klastry usługi Azure HDInsight ze skryptami uruchomionymi w chmurze przy użyciu Azure Automation elementów Runbook.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 5eb0f353579233041bb5ccba46de2549ada7e9b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864792"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Samouczek: tworzenie klastrów usługi Azure HDInsight za pomocą Azure Automation

Azure Automation pozwala tworzyć skrypty uruchamiane w chmurze i zarządzać zasobami platformy Azure na żądanie lub na podstawie harmonogramu. W tym artykule opisano sposób tworzenia elementów Runbook programu PowerShell w celu tworzenia i usuwania klastrów usługi Azure HDInsight.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj moduły niezbędne do współpracy z usługą HDInsight.
> * Tworzenie i przechowywanie poświadczeń wymaganych podczas tworzenia klastra.
> * Utwórz nowy element Runbook Azure Automation, aby utworzyć klaster usługi HDInsight.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Istniejące [konto Azure Automation](../automation/automation-quickstart-create-account.md).
* Istniejące [konto usługi Azure Storage](../storage/common/storage-account-create.md), które będzie używane jako magazyn klastra.

## <a name="install-hdinsight-modules"></a>Instalowanie modułów usługi HDInsight

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Wybierz konta Azure Automation.
1. Wybierz pozycję **Galeria modułów** w obszarze **zasoby udostępnione**.
1. W polu wpisz **AzureRM. profile** i naciśnij klawisz ENTER, aby wyszukać. Wybierz dostępny wynik wyszukiwania.
1. Na ekranie **AzureRM. profil** wybierz pozycję **Importuj**. Zaznacz pole wyboru, aby zaktualizować moduły platformy Azure, a następnie wybierz przycisk **OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="Importuj moduł AzureRM. profile" border="false":::

1. Wróć do galerii modułów, wybierając pozycję **Galeria modułów** w obszarze **zasoby udostępnione**.
1. Typ **HDInsight**. Wybierz **AzureRM. HDInsight**.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="Przeglądaj moduły usługi HDInsight" border="true":::

1. W panelu **AzureRM. HDInsight** wybierz pozycję **Importuj** i **przycisk OK**.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="Importuj moduł AzureRM. HDInsight" border="true":::

## <a name="create-credentials"></a>Utwórz poświadczenia

1. W obszarze **udostępnione zasoby** wybierz pozycję **poświadczenia**.
1. Wybierz pozycję **Dodaj poświadczenie**.
1. Wprowadź wymagane informacje w panelu **nowe poświadczenia** . To poświadczenie służy do przechowywania hasła klastra, co umożliwi zalogowanie się do Ambari.

    | Właściwość | Wartość |
    | --- | --- |
    | Nazwa | `cluster-password` |
    | Nazwa użytkownika | `admin` |
    | Hasło | `SECURE_PASSWORD` |
    | Potwierdź hasło | `SECURE_PASSWORD` |

1. Wybierz przycisk **Utwórz**.
1. Powtórz ten sam proces dla nowego poświadczenia `ssh-password` przy użyciu nazwy użytkownika `sshuser` i wybranego hasła. Wybierz przycisk **Utwórz**. To poświadczenie ma na celu przechowywanie hasła SSH dla klastra.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="tworzenie poświadczenia" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>Tworzenie elementu Runbook w celu utworzenia klastra

1. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
1. Wybierz pozycję **Utwórz element Runbook**.
1. W panelu **Tworzenie elementu Runbook** wprowadź nazwę elementu Runbook, na przykład `hdinsight-cluster-create` . Wybierz opcję **PowerShell** z listy rozwijanej **Typ elementu Runbook** .
1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="Utwórz element Runbook" border="true":::

1. Wprowadź następujący kod na ekranie **Edycja elementu Runbook programu PowerShell** i wybierz pozycję **Publikuj**:

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="Publikowanie elementu Runbook" border="true":::

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Tworzenie elementu Runbook w celu usunięcia klastra

1. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
1. Wybierz pozycję **Utwórz element Runbook**.
1. W panelu **Tworzenie elementu Runbook** wprowadź nazwę elementu Runbook, na przykład `hdinsight-cluster-delete` . Wybierz opcję **PowerShell** z listy rozwijanej **Typ elementu Runbook** .
1. Wybierz przycisk **Utwórz**.
1. Wprowadź następujący kod na ekranie **Edycja elementu Runbook programu PowerShell** i wybierz pozycję **Publikuj**:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Wykonaj elementy Runbook

### <a name="create-a-cluster"></a>Tworzenie klastra

1. Wyświetl listę elementów Runbook dla konta usługi Automation, wybierając **elementy Runbook** w obszarze **Automatyzacja procesów**.
1. Wybierz `hdinsight-cluster-create` lub nazwę, która została użyta podczas tworzenia elementu Runbook tworzenia klastra.
1. Wybierz pozycję **Rozpocznij** , aby natychmiast wykonać element Runbook. Można również zaplanować okresowe uruchamianie elementów Runbook. Zobacz [Planowanie elementu Runbook w Azure Automation](../automation/shared-resources/schedules.md)
1. Wprowadź parametry wymagane dla skryptu i wybierz **przycisk OK**. Spowoduje to utworzenie nowego klastra usługi HDInsight o nazwie określonej w parametrze **ClusterName** .

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="Wykonaj Tworzenie elementu Runbook tworzenia klastra" border="true":::

### <a name="delete-a-cluster"></a>Usuwanie klastra

Usuń klaster, wybierając `hdinsight-cluster-delete` element Runbook, który został utworzony. Wybierz pozycję **Start**, wprowadź parametr **ClusterName** , a sselect **OK**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń konto Azure Automation, które zostało utworzone w celu uniknięcia niezamierzonych opłat. W tym celu przejdź do Azure Portal, wybierz grupę zasobów, w której utworzono konto Azure Automation, wybierz konto usługi Automation, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure PowerShell](hdinsight-administer-use-powershell.md)