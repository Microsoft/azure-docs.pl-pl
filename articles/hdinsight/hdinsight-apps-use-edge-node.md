---
title: Używanie pustych węzłów krawędzi w klastrach Apache Hadoop w usłudze Azure HDInsight
description: Jak dodać pusty węzeł krawędzi do klastra HDInsight. Używany jako klient, a następnie przetestować lub hostować aplikacje HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537801"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Używanie pustych węzłów krawędziowych w klastrach Apache Hadoop w programie HDInsight

Dowiedz się, jak dodać pusty węzeł krawędziowy do klastra HDInsight. Pusty węzeł krawędzi to maszyna wirtualna systemu Linux z tymi samymi narzędziami klienckimi zainstalowanymi i skonfigurowanym, jak w węzłach headnodes. Ale bez [usług Apache Hadoop](./hadoop/apache-hadoop-introduction.md) uruchomionych. Węzeł brzegowy służy do uzyskiwania dostępu do klastra, testowania aplikacji klienckich i hostowania aplikacji klienckich.

Podczas tworzenia klastra można dodać pusty węzeł krawędziowy do istniejącego klastra HDInsight. Dodawanie pustego węzła krawędzi odbywa się przy użyciu szablonu usługi Azure Resource Manager.  W poniższym przykładzie pokazano, jak to się robi przy użyciu szablonu:

```json
"resources": [
    {
        "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
        "type": "Microsoft.HDInsight/clusters/applications",
        "apiVersion": "2015-03-01-preview",
        "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
        "properties": {
            "marketPlaceIdentifier": "EmptyNode",
            "computeProfile": {
                "roles": [{
                    "name": "edgenode",
                    "targetInstanceCount": 1,
                    "hardwareProfile": {
                        "vmSize": "{}"
                    }
                }]
            },
            "installScriptActions": [{
                "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                "uri": "[parameters('installScriptAction')]",
                "roles": ["edgenode"]
            }],
            "uninstallScriptActions": [],
            "httpsEndpoints": [],
            "applicationType": "CustomApplication"
        }
    }
],
```

Jak pokazano w przykładzie, można opcjonalnie wywołać [akcję skryptu,](hdinsight-hadoop-customize-cluster-linux.md) aby wykonać dodatkową konfigurację. Takie jak [instalacja Apache Hue](hdinsight-hadoop-hue-linux.md) w węźle krawędzi. Skrypt akcji skryptu musi być publicznie dostępny w sieci Web.  Na przykład jeśli skrypt jest przechowywany w usłudze Azure Storage, użyj kontenerów publicznych lub publicznych obiektów blob.

Rozmiar maszyny wirtualnej węzła krawędzi musi spełniać wymagania rozmiaru węzła maszyny wirtualnej węzła klastra HDInsight. Aby zapoznać się z zalecanymi rozmiarami maszyn wirtualnych węzła procesu roboczego, zobacz [Tworzenie klastrów Apache Hadoop w udziale usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Po utworzeniu węzła krawędzi można połączyć się z węzłem brzegowym za pomocą funkcji SSH i uruchomić narzędzia klienckie w celu uzyskania dostępu do klastra Hadoop w programie HDInsight.

> [!WARNING]
> Składniki niestandardowe, które są zainstalowane w węźle brzegowym otrzymują komercyjnie uzasadnioną pomoc techniczną od firmy Microsoft. Może to spowodować rozwiązanie problemów, które napotkasz. Możesz też zostać skierowany do zasobów społeczności w celu uzyskania dalszej pomocy. Oto niektóre z najbardziej aktywnych witryn, aby uzyskać pomoc od społeczności:
>
> * [Forum MSDN dla HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Jeśli korzystasz z technologii Apache, możesz znaleźć pomoc za pośrednictwem witryn projektu [https://apache.org](https://apache.org)Apache na , takich jak strona [Apache Hadoop.](https://hadoop.apache.org/)

> [!IMPORTANT]
> Obrazy Ubuntu stają się dostępne dla nowego tworzenia klastra HDInsight w ciągu 3 miesięcy od ich opublikowania. Od stycznia 2019 r. uruchomione klastry (w tym węzły brzegowe) **nie** są automatycznie poprawiane. Klienci muszą używać akcji skryptu lub innych mechanizmów, aby załatać uruchomiony klaster.  Aby uzyskać więcej informacji, zobacz [łatanie systemu operacyjnego dla usługi HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Dodawanie węzła krawędzi do istniejącego klastra

W tej sekcji można użyć szablonu Menedżera zasobów, aby dodać węzeł krawędzi do istniejącego klastra HDInsight.  Szablon Menedżera zasobów można znaleźć w [usłudze GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Szablon Menedżera zasobów wywołuje akcję https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.shskryptu znajdującą się pod adresem . Skrypt nie wykonuje żadnych akcji.  Ma zademonstrować wywołanie akcji skryptu z szablonu Menedżera zasobów.

1. Wybierz następujący obraz, aby zalogować się na platformie Azure i otworzyć szablon usługi Azure Resource Manager w witrynie Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Skonfiguruj następujące właściwości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure używaną do tworzenia klastra.|
    |Grupa zasobów|Wybierz grupę zasobów używaną dla istniejącego klastra HDInsight.|
    |Lokalizacja|Wybierz lokalizację istniejącego klastra HDInsight.|
    |Nazwa klastra|Wprowadź nazwę istniejącego klastra HDInsight.|

1. Zaznacz **opcję Zgadzam się z warunkami podanymi powyżej,** a następnie wybierz pozycję **Kup,** aby utworzyć węzeł krawędzi.

> [!IMPORTANT]  
> Upewnij się, aby wybrać grupę zasobów platformy Azure dla istniejącego klastra USŁUGI HDInsight.  W przeciwnym razie zostanie wyświetlony komunikat o błędzie "Nie można wykonać żądanej operacji na zagnieżdżonego zasobu. Nie znaleziono&lt;zasobu nadrzędnego ' ClusterName>'.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Dodawanie węzła krawędzi podczas tworzenia klastra

W tej sekcji można użyć szablonu Menedżera zasobów do utworzenia klastra HDInsight z węzłem krawędzi.  Szablon Menedżera zasobów można znaleźć w [galerii szablonów szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Szablon Menedżera zasobów wywołuje akcję https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.shskryptu znajdującą się pod adresem . Skrypt nie wykonuje żadnych akcji.  Ma zademonstrować wywołanie akcji skryptu z szablonu Menedżera zasobów.

1. Utwórz klaster HDInsight, jeśli jeszcze go nie masz.  Zobacz [Wprowadzenie do korzystania z usługi Hadoop w pliku HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Wybierz następujący obraz, aby zalogować się na platformie Azure i otworzyć szablon usługi Azure Resource Manager w witrynie Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Skonfiguruj następujące właściwości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure używaną do tworzenia klastra.|
    |Grupa zasobów|Utwórz nową grupę zasobów używaną dla klastra.|
    |Lokalizacja|Wybierz lokalizację dla grupy zasobów.|
    |Nazwa klastra|Wprowadź nazwę nowego klastra do utworzenia.|
    |Nazwa użytkownika logowania klastra|Wprowadź nazwę użytkownika HTTP Hadoop.  Nazwa domyślna to **admin**.|
    |Hasło logowania klastra|Wprowadź hasło użytkownika HTTP Hadoop.|
    |Nazwa użytkownika Ssh|Wprowadź nazwę użytkownika SSH. Domyślna nazwa **to sshuser**.|
    |Hasło Ssh|Wprowadź hasło użytkownika SSH.|
    |Akcja instalowania skryptu|Zachowaj wartość domyślną, aby przejść przez ten artykuł.|

    Niektóre właściwości zostały zakodowane na stałe w szablonie: typ klastra, liczba węzłów procesu roboczego klastra, rozmiar węzła krawędzi i nazwa węzła krawędzi.

1. Zaznacz **opcję Zgadzam się z warunkami podanymi powyżej,** a następnie wybierz pozycję **Kup,** aby utworzyć klaster z węzłem krawędzi.

## <a name="add-multiple-edge-nodes"></a>Dodawanie wielu węzłów krawędziowych

Do klastra HDInsight można dodać wiele węzłów brzegowych.  Konfigurację wielu węzłów brzegowych można wykonać tylko przy użyciu szablonów usługi Azure Resource Manager.  Zobacz przykład szablonu na początku tego artykułu.  Zaktualizuj **targetInstanceCount,** aby odzwierciedlić liczbę węzłów krawędzi, które chcesz utworzyć.

## <a name="access-an-edge-node"></a>Uzyskiwanie dostępu do węzła krawędzi

Punkt końcowy ssh węzła krawędzi jest &lt;EdgeNodeName>. &lt;> ssh.azurehdinsight.net:22.  Na przykład new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Węzeł krawędzi jest wyświetlany jako aplikacja w witrynie Azure portal.  Portal udostępnia informacje, aby uzyskać dostęp do węzła krawędzi przy użyciu SSH.

**Aby sprawdzić punkt końcowy węzła krawędzi SSH**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster HDInsight za pomocą węzła krawędziowego.
3. Wybierz **opcję Aplikacje**. Zobaczysz węzeł krawędzi.  Domyślna nazwa to **new-edgenode**.
4. Wybierz węzeł krawędzi. Zostanie wyświetlony punkt końcowy SSH.

**Aby użyć gałęzi w węźle krawędzi**

1. Połącz się z węzłem brzegowym za pomocą protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po nawiązaniu połączenia z węzłem brzegu za pomocą funkcji SSH użyj następującego polecenia, aby otworzyć konsolę Hive:

        hive

3. Uruchom następujące polecenie, aby wyświetlić tabele gałęzi w klastrze:

        show tables;

## <a name="delete-an-edge-node"></a>Usuwanie węzła krawędzi

Węzeł krawędzi można usunąć z witryny Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster HDInsight za pomocą węzła krawędziowego.
3. Wybierz **opcję Aplikacje**. Zostanie wyświetlona lista węzłów krawędziowych.  
4. Kliknij prawym przyciskiem myszy węzeł krawędzi, który chcesz usunąć, a następnie wybierz polecenie **Usuń**.
5. Wybierz pozycję **Tak**, aby potwierdzić.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak dodać węzeł krawędzi i jak uzyskać dostęp do węzła krawędzi. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md): dowiedz się, jak instalować aplikacje usługi HDInsight w klastrach.
* [Instalowanie niestandardowych aplikacji HDInsight](hdinsight-apps-install-custom-applications.md): dowiedz się, jak wdrożyć nieopublikowane aplikacje HDInsight w udojaźniowej aplikacji HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w systemie HDInsight przy użyciu szablonów Menedżera zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md): dowiedz się, jak wywoływać szablony Menedżera zasobów w celu tworzenia klastrów HDInsight.
