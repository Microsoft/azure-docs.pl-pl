---
title: Instalowanie niestandardowych aplikacji Apache Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować aplikacje usługi HDInsight dla klastrów Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: d110f4ee0248b6ea069c6033dd69840eec1a18f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007158"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie niestandardowych aplikacji Apache Hadoop w usłudze Azure HDInsight

W tym artykule dowiesz się, jak zainstalować aplikację [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight, która nie została opublikowana w Azure Portal. Aplikacja, którą zainstalujesz w tym artykule, to [odcień](https://gethue.com/).

Aplikacja HDInsight An jest aplikacją, którą użytkownicy mogą instalować w klastrze usługi HDInsight.  Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-hdinsight-applications"></a>Instalowanie aplikacji usługi HDInsight

Aplikacje usługi HDInsight można instalować podczas tworzenia klastra lub w istniejącym klastrze usługi HDInsight. Definiowanie szablonów usługi Azure Resource Manager opisano w temacie [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: instalowanie aplikacji usługi HDInsight).

Pliki potrzebne do wdrożenia tej aplikacji (Hue):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): szablon usługi Azure Resource Manager do instalowania aplikacji usługi HDInsight. Aby utworzyć własny szablon usługi Resource Manager, zobacz [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: instalowanie aplikacji usługi HDInsight).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): akcja skryptu wywoływana przez szablon usługi Resource Manager w celu skonfigurowania węzła krawędzi.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): plik binarny aplikacji hue wywoływany ze skryptu hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): plik binarny aplikacji hue wywoływany ze skryptu hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): przykładowa aplikacja internetowa (Tomcat) wywoływana ze skryptu hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Instalowanie aplikacji Hue w istniejącym klastrze usługi HDInsight

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon Menedżer zasobów w Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon Menedżer zasobów znajduje się w lokalizacji [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) .  Aby dowiedzieć się, jak napisać ten szablon usługi Resource Manager, zobacz [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: instalowanie aplikacji usługi HDInsight).

1. Wybierz z listy rozwijanej istniejącą **grupę zasobów** zawierającą klaster. Wymagane jest użycie tej samej grupy zasobów co klaster.

1. Wprowadź nazwę klastra, w którym chcesz zainstalować aplikację. Musi to być istniejący klaster.

1. Zaznacz pole wyboru **Akceptuję warunki i postanowienia podane powyżej**.

1. Wybierz pozycję **Kup**.

Możesz sprawdzić stan instalacji na kafelku przypiętym do pulpitu nawigacyjnego portalu i w powiadomieniu portalu (kliknij ikonę dzwonka w górnej części portalu).  Instalowanie aplikacji trwa około 10 minut.

### <a name="to-install-hue-while-creating-a-cluster"></a>Instalowanie aplikacji Hue podczas tworzenia klastra

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon Menedżer zasobów w Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon Menedżer zasobów znajduje się w lokalizacji [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) .  Aby dowiedzieć się, jak napisać ten szablon usługi Resource Manager, zobacz [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: instalowanie aplikacji usługi HDInsight).

2. Postępuj zgodnie z instrukcjami, aby utworzyć klaster i zainstalować aplikację Hue. Aby uzyskać więcej informacji na temat tworzenia klastrów usługi HDInsight, zobacz temat [Tworzenie opartych na systemie Linux klastrów Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Inne metody instalacji

Oprócz witryny Azure Portal do wywołania szablonów usługi Resource Manager można również użyć programu [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) oraz [interfejsu wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli).

## <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji

Możesz zobaczyć stan aplikacji w witrynie Azure Portal, aby sprawdzić poprawność instalacji aplikacji. Ponadto można również sprawdzić, czy wszystkie punkty końcowe HTTP zostały dostarczone zgodnie z oczekiwaniami, a strona sieci Web, jeśli istnieje.

W przypadku **odcienia** można wykonać następujące czynności:

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz klaster, w którym zainstalowano aplikację.
1. Z menu **Ustawienia** wybierz pozycję **aplikacje**.
1. Wybierz **odcień** z listy, aby wyświetlić właściwości.  
1. Wybierz link do strony sieci Web, aby sprawdzić poprawność witryny sieci Web.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zamień `CLUSTERNAME` i `RESOURCEGROUP` z odpowiednimi wartościami, a następnie wprowadź poniższe polecenia:

* Aby uzyskać listę wszystkich aplikacji dla klastra usługi HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Aby pobrać właściwości określonej aplikacji.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Rozwiązywanie problemów z instalacją

Możesz sprawdzić stan instalacji aplikacji w powiadomieniu portalu (kliknij ikonę dzwonka w górnej części portalu).

Jeśli instalacja aplikacji nie powiodła się, można zobaczyć komunikaty o błędach i informacje debugowania z trzech miejsc:

* Aplikacje usługi HDInsight: ogólne informacje o błędach.

    Otwórz klaster z portalu i wybierz pozycję aplikacje z ustawień:

    ![błąd instalacji aplikacji usługi hdinsight](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Akcja skryptu HDInsight: jeśli komunikat o błędzie aplikacji usługi HDInsight wskazuje niepowodzenie akcji skryptu, można znaleźć więcej szczegółów na temat błędu skryptu w okienku akcji skryptu.

    Wybierz pozycję Akcja skryptu z ustawień. Historia akcji skryptu zawiera komunikaty o błędach.

    ![błąd akcji skryptu aplikacji usługi hdinsight](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Interfejs użytkownika sieci Web platformy Apache Ambari: Jeśli przyczyną błędu jest skrypt instalacji, użyj interfejsu użytkownika Ambari Web, aby sprawdzić pełne dzienniki dotyczące skryptów instalacji.

    Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z skryptami](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Usuwanie aplikacji usługi HDInsight

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz klaster, w którym zainstalowano aplikację.
1. Z menu **Ustawienia** wybierz pozycję **aplikacje**.
1. Kliknij prawym przyciskiem myszy aplikację, którą chcesz usunąć, a następnie wybierz polecenie **Usuń**.
1. Wybierz pozycję **Tak**, aby potwierdzić.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zastąp `NAME` `CLUSTERNAME` wartości, i `RESOURCEGROUP` z odpowiednimi wartościami, a następnie wprowadź poniższe polecenie:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Następne kroki

* [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: instalowanie aplikacji usługi HDInsight): dowiedz się, jak opracowywać szablony usługi Resource Manager w celu wdrażania aplikacji usługi HDInsight.
* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md): dowiedz się, jak instalować aplikacje usługi HDInsight w klastrach.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informacje o sposobie wywoływania szablonów Menedżer zasobów do tworzenia klastrów usługi HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.
