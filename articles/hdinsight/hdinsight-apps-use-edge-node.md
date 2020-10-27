---
title: Używanie pustych węzłów brzegowych w klastrach Apache Hadoop w usłudze Azure HDInsight
description: Jak dodać pusty węzeł brzegowy do klastra usługi HDInsight. Używany jako klient, a następnie testowania lub hostowania aplikacji usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: ddd1a6fa2fc25add30664da5b739338e87e7e74f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547847"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Używanie pustych węzłów brzegowych w klastrach Apache Hadoop w usłudze HDInsight

Dowiedz się, jak dodać pusty węzeł brzegowy do klastra usługi HDInsight. Pusty węzeł brzegowy to maszyna wirtualna z systemem Linux z tymi samymi narzędziami klienta zainstalowanymi i skonfigurowanymi jako węzłów głównych. Ale bez [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) usług. Węzeł brzegowy służy do uzyskiwania dostępu do klastra, testowania aplikacji klienckich i hostowania aplikacji klienckich.

Po utworzeniu klastra można dodać pusty węzeł brzegowy do istniejącego klastra usługi HDInsight do nowego klastra. Dodawanie pustego węzła krawędzi jest wykonywane przy użyciu szablonu Azure Resource Manager.  W poniższym przykładzie pokazano, jak to zrobić za pomocą szablonu:

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

Jak pokazano w przykładzie, można opcjonalnie wywołać [akcję skryptu](hdinsight-hadoop-customize-cluster-linux.md) , aby wykonać dodatkową konfigurację. Takie jak instalowanie [odcienia Apache](hdinsight-hadoop-hue-linux.md) w węźle brzegowym. Skrypt akcji skryptu musi być publicznie dostępny w sieci Web.  Jeśli na przykład skrypt jest przechowywany w usłudze Azure Storage, należy użyć kontenerów publicznych lub publicznych obiektów BLOB.

Rozmiar maszyny wirtualnej węzła brzegowego musi być zgodny z wymaganiami dotyczącymi rozmiaru maszyny wirtualnej klastra usługi HDInsight. Aby uzyskać zalecane rozmiary maszyn wirtualnych węzłów procesu roboczego, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Po utworzeniu węzła brzegowego można nawiązać połączenie z węzłem brzegowym przy użyciu protokołu SSH, a następnie uruchomić narzędzia klienckie w celu uzyskania dostępu do klastra Hadoop w usłudze HDInsight.

> [!WARNING]
> Składniki niestandardowe zainstalowane w węźle brzegowym otrzymują komercyjnie uzasadnioną pomoc techniczną firmy Microsoft. Może to spowodować rozwiązanie napotkanych problemów. Możesz też przystąpić do zasobów społeczności, aby uzyskać dalszą pomoc. Poniżej wymieniono niektóre z najbardziej aktywnych witryn do uzyskiwania pomocy od społeczności:
>
> * [Microsoft Q&stronie pytania dla usługi HDInsight] (/Answers/topics/azure-hdinsight.html
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> W przypadku korzystania z technologii Apache można znaleźć pomoc w witrynach projektów Apache w systemie [https://apache.org](https://apache.org) , takich jak lokacja [Apache Hadoop](https://hadoop.apache.org/) .

> [!IMPORTANT]
> Obrazy Ubuntu są dostępne dla nowego klastra usługi HDInsight w ciągu 3 miesięcy od opublikowania. Począwszy od stycznia 2019, uruchomione klastry (w tym węzły brzegowe) **nie** są poprawiane przez program. Aby zastosować poprawki do działającego klastra, klienci muszą używać akcji skryptu lub innych mechanizmów.  Aby uzyskać więcej informacji, zobacz [poprawka systemu operacyjnego dla usługi HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Dodawanie węzła krawędzi do istniejącego klastra

W tej sekcji użyjesz szablonu Menedżer zasobów, aby dodać węzeł brzegowy do istniejącego klastra usługi HDInsight.  Szablon Menedżer zasobów można znaleźć w witrynie [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Szablon Menedżer zasobów wywołuje akcję skryptu znajdującą się w https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh . Skrypt nie wykonuje żadnych akcji.  Jest to zademonstrowanie wywoływanej akcji skryptu z szablonu Menedżer zasobów.

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon Azure Resource Manager w Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Skonfiguruj następujące właściwości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure używaną do tworzenia klastra.|
    |Grupa zasobów|Wybierz grupę zasobów używaną dla istniejącego klastra usługi HDInsight.|
    |Lokalizacja|Wybierz lokalizację istniejącego klastra usługi HDInsight.|
    |Nazwa klastra|Wprowadź nazwę istniejącego klastra usługi HDInsight.|

1. Zaznacz pole wyboru Akceptuję **warunki i postanowienia podane powyżej** , a następnie wybierz pozycję  **Kup** , aby utworzyć węzeł brzegowy.

> [!IMPORTANT]  
> Upewnij się, że wybrano grupę zasobów platformy Azure dla istniejącego klastra usługi HDInsight.  W przeciwnym razie zostanie wyświetlony komunikat o błędzie "nie można wykonać wymaganej operacji na zasobie zagnieżdżonym. Nie znaleziono zasobu nadrzędnego " &lt; clustername>". "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Dodawanie węzła krawędzi podczas tworzenia klastra

W tej sekcji użyjesz szablonu Menedżer zasobów, aby utworzyć klaster usługi HDInsight z węzłem krawędzi.  Szablon Menedżer zasobów można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Szablon Menedżer zasobów wywołuje akcję skryptu znajdującą się w https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh . Skrypt nie wykonuje żadnych akcji.  Jest to zademonstrowanie wywoływanej akcji skryptu z szablonu Menedżer zasobów.

1. Utwórz klaster usługi HDInsight, jeśli jeszcze go nie masz.  Zobacz [Rozpoczynanie korzystania z usługi Hadoop w usłudze HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon Azure Resource Manager w Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Skonfiguruj następujące właściwości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję platformy Azure używaną do tworzenia klastra.|
    |Grupa zasobów|Utwórz nową grupę zasobów używaną w klastrze.|
    |Lokalizacja|Wybierz lokalizację dla grupy zasobów.|
    |Nazwa klastra|Wprowadź nazwę nowego klastra do utworzenia.|
    |Nazwa użytkownika logowania klastra|Wprowadź nazwę użytkownika HTTP usługi Hadoop.  Nazwa domyślna to **admin** .|
    |Hasło logowania klastra|Wprowadź hasło użytkownika protokołu HTTP usługi Hadoop.|
    |Nazwa użytkownika SSH|Wprowadź nazwę użytkownika SSH. Nazwa domyślna to **sshuser** .|
    |Hasło ssh|Wprowadź hasło użytkownika SSH.|
    |Akcja instalacji skryptu|Pozostaw wartość domyślną, aby przejść przez ten artykuł.|

    Niektóre właściwości zostały stałe w szablonie: typ klastra, liczba węzłów procesu roboczego klastra, rozmiar węzła krawędzi i nazwa węzła krawędzi.

1. Zaznacz **Akceptuję warunki i** postanowienia wymienione powyżej, a następnie wybierz pozycję  **Kup** , aby utworzyć klaster z węzłem krawędzi.

## <a name="add-multiple-edge-nodes"></a>Dodawanie wielu węzłów brzegowych

Można dodać wiele węzłów krawędzi do klastra usługi HDInsight.  Konfigurację węzłów z wieloma krawędziami można wykonać tylko przy użyciu szablonów Azure Resource Manager.  Zapoznaj się z przykładem szablonu na początku tego artykułu.  Zaktualizuj **targetInstanceCount** , aby odzwierciedlała liczbę węzłów krawędzi, które chcesz utworzyć.

## <a name="access-an-edge-node"></a>Dostęp do węzła brzegowego

Punkt końcowy SSH węzła brzegowego to &lt; EdgeNodeName>. &lt; ClusterName>-ssh.azurehdinsight.net:22.  Na przykład new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Węzeł brzegowy jest wyświetlany jako aplikacja na Azure Portal.  Portal udostępnia informacje umożliwiające dostęp do węzła brzegowego przy użyciu protokołu SSH.

**Aby sprawdzić punkt końcowy SSH węzła brzegowego**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster usługi HDInsight z węzłem krawędzi.
3. Wybierz pozycję **aplikacje** . Zobaczysz węzeł brzegowy.  Nazwa domyślna to **New-edgenode** .
4. Wybierz węzeł brzegowy. Zobaczysz punkt końcowy SSH.

**Aby użyć programu Hive na węźle brzegowym**

1. Połącz się z węzłem brzegowym za pomocą protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po nawiązaniu połączenia z węzłem brzegowym przy użyciu protokołu SSH Użyj następującego polecenia, aby otworzyć konsolę programu Hive:

    ```console
    hive
    ```

3. Uruchom następujące polecenie, aby wyświetlić tabele Hive w klastrze:

    ```hiveql
    show tables;
    ```

## <a name="delete-an-edge-node"></a>Usuń węzeł krawędzi

Węzeł brzegowy można usunąć z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster usługi HDInsight z węzłem krawędzi.
3. Wybierz pozycję **aplikacje** . Zobaczysz listę węzłów brzegowych.  
4. Kliknij prawym przyciskiem myszy węzeł brzegowy, który chcesz usunąć, a następnie wybierz polecenie **Usuń** .
5. Kliknij przycisk **Tak** , aby potwierdzić.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak dodać węzeł brzegowy i jak uzyskać dostęp do węzła brzegowego. Więcej informacji można znaleźć w następujących artykułach:

* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md): dowiedz się, jak instalować aplikacje usługi HDInsight w klastrach.
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowaną aplikację usługi HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](/rest/api/hdinsight/hdinsight-application): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informacje o sposobie wywoływania szablonów Menedżer zasobów do tworzenia klastrów usługi HDInsight.