---
title: Instalowanie niestandardowych aplikacji Apache Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować aplikacje HDInsight dla klastrów Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: b25e9d860f118c1b044b13e01a80aaf7a24963cf
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768236"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie niestandardowych aplikacji Apache Hadoop w usłudze Azure HDInsight

W tym artykule dowiesz się, jak zainstalować aplikację [Apache Hadoop](https://hadoop.apache.org/) na platformie Azure HDInsight, która nie została opublikowana w witrynie Azure portal. Aplikacja, którą zainstalujesz w tym [artykule,](https://gethue.com/)to Hue .

Aplikacja HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze HDInsight.  Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-hdinsight-applications"></a>Instalowanie aplikacji usługi HDInsight

Aplikacje usługi HDInsight można instalować podczas tworzenia klastra lub w istniejącym klastrze usługi HDInsight. Definiowanie szablonów usługi Azure Resource Manager opisano w temacie [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalowanie aplikacji usługi HDInsight).

Pliki potrzebne do wdrożenia tej aplikacji (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): szablon usługi Azure Resource Manager do instalowania aplikacji usługi HDInsight. Aby utworzyć własny szablon usługi Resource Manager, zobacz [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalowanie aplikacji usługi HDInsight).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): akcja skryptu wywoływana przez szablon usługi Resource Manager w celu skonfigurowania węzła krawędzi.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): plik binarny aplikacji hue wywoływany ze skryptu hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): plik binarny aplikacji hue wywoływany ze skryptu hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): przykładowa aplikacja internetowa (Tomcat) wywoływana ze skryptu hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Instalowanie aplikacji Hue w istniejącym klastrze usługi HDInsight

1. Wybierz następujący obraz, aby zalogować się na platformie Azure i otworzyć szablon Usługi Resource Manager w witrynie Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon Menedżera zasobów znajduje [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)się w pliku .  Aby dowiedzieć się, jak napisać ten szablon usługi Resource Manager, zobacz [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalowanie aplikacji usługi HDInsight).

1. Wybierz istniejącą **grupę zasobów** zawierającą klaster z listy rozwijanej. Jest to wymagane do korzystania z tej samej grupy zasobów co klaster.

1. Wprowadź nazwę klastra, w którym chcesz zainstalować aplikację. Musi to być istniejący klaster.

1. Zaznacz pole **wyboru, aby wyrazić zgodę na warunki podane powyżej.**

1. Wybierz pozycję **Kup**.

Możesz sprawdzić stan instalacji na kafelku przypiętym do pulpitu nawigacyjnego portalu i w powiadomieniu portalu (kliknij ikonę dzwonka w górnej części portalu).  Instalowanie aplikacji trwa około 10 minut.

### <a name="to-install-hue-while-creating-a-cluster"></a>Instalowanie aplikacji Hue podczas tworzenia klastra

1. Wybierz następujący obraz, aby zalogować się na platformie Azure i otworzyć szablon Usługi Resource Manager w witrynie Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon Menedżera zasobów znajduje [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)się w pliku .  Aby dowiedzieć się, jak napisać ten szablon usługi Resource Manager, zobacz [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalowanie aplikacji usługi HDInsight).

2. Postępuj zgodnie z instrukcjami, aby utworzyć klaster i zainstalować aplikację Hue. Aby uzyskać więcej informacji na temat tworzenia klastrów usługi HDInsight, zobacz temat [Tworzenie opartych na systemie Linux klastrów Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Inne metody instalacji

Oprócz witryny Azure Portal do wywołania szablonów usługi Resource Manager można również użyć programu [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) oraz [interfejsu wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli).

## <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji

Możesz zobaczyć stan aplikacji w witrynie Azure Portal, aby sprawdzić poprawność instalacji aplikacji. Ponadto można również sprawdzić poprawność wszystkich punktów końcowych HTTP pojawił się zgodnie z oczekiwaniami i strony sieci Web, jeśli istnieje.

W przypadku **programu Hue**można wykonać następujące czynności:

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz klaster, w którym zainstalowano aplikację.
1. W menu **Ustawienia** wybierz polecenie **Aplikacje**.
1. Wybierz **odcień** z listy, aby wyświetlić właściwości.  
1. Wybierz łącze strony sieci Web, aby zweryfikować witrynę sieci Web.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zastąp `CLUSTERNAME`i `RESOURCEGROUP` odpowiednie wartości, a następnie wprowadź poniższe polecenia:

* Aby wymienić wszystkie aplikacje dla klastra HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Aby pobrać właściwości określonej aplikacji.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Rozwiązywanie problemów z instalacją

Możesz sprawdzić stan instalacji aplikacji w powiadomieniu portalu (kliknij ikonę dzwonka w górnej części portalu).

Jeśli instalacja aplikacji nie powiodła się, można wyświetlić komunikaty o błędach i informacje debugowania z trzech miejsc:

* Aplikacje usługi HDInsight: ogólne informacje o błędach.

    Otwórz klaster z portalu i wybierz pozycję Aplikacje z ustawień:

    ![błąd instalacji aplikacji usługi hdinsight](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Akcja skryptu HDInsight: jeśli komunikat o błędzie aplikacji usługi HDInsight wskazuje niepowodzenie akcji skryptu, można znaleźć więcej szczegółów na temat błędu skryptu w okienku akcji skryptu.

    Wybierz akcję skryptu w ustawieniach. Historia akcji skryptu zawiera komunikaty o błędach.

    ![błąd akcji skryptu aplikacji usługi hdinsight](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari Web UI: Jeśli przyczyną błędu był skrypt instalacyjny, użyj interfejsu użytkownika ambari web, aby sprawdzić pełne dzienniki dotyczące skryptów instalacyjnych.

    Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z akcjami skryptu](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Usuwanie aplikacji usługi HDInsight

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz klaster, w którym zainstalowano aplikację.
1. W menu **Ustawienia** wybierz polecenie **Aplikacje**.
1. Kliknij prawym przyciskiem myszy aplikację, którą chcesz usunąć, a następnie wybierz polecenie **Usuń**.
1. Wybierz pozycję **Tak**, aby potwierdzić.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zastąp `NAME`, `CLUSTERNAME`i `RESOURCEGROUP` odpowiednie wartości, a następnie wprowadź polecenie poniżej:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Następne kroki

* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalowanie aplikacji usługi HDInsight): dowiedz się, jak opracowywać szablony usługi Resource Manager w celu wdrażania aplikacji usługi HDInsight.
* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md): dowiedz się, jak instalować aplikacje usługi HDInsight w klastrach.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w systemie HDInsight przy użyciu szablonów Menedżera zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md): dowiedz się, jak wywoływać szablony Menedżera zasobów w celu tworzenia klastrów HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.
