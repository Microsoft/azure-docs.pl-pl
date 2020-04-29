---
title: 'Szybki Start: Tworzenie klastra Apache HBase przy użyciu szablonu — Azure HDInsight'
description: W tym przewodniku szybki start pokazano, jak za pomocą szablonu Menedżer zasobów utworzyć klaster Apache HBase w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/12/2020
ms.openlocfilehash: 395078b9a973b6255685feb6a858daed7667207a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605438"
---
# <a name="quickstart-create-apache-hbase-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Szybki Start: Tworzenie klastra Apache HBase w usłudze Azure HDInsight przy użyciu szablonu Menedżer zasobów

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager, aby utworzyć klaster [Apache HBase](./apache-hbase-overview.md) w usłudze Azure HDInsight. HBase to baza danych "open source", która jest oparta na Apache Hadoop i modelowana po [BigTable](https://cloud.google.com/bigtable/).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-an-apache-hbase-cluster"></a>Tworzenie klastra Apache HBase

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-hbase-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-hbase-linux/azuredeploy.json" range="1-145":::


Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft. Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): Tworzenie konta usługi Azure Storage.
* [Microsoft. HDInsight/klaster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): Tworzenie klastra usługi HDInsight.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz przycisk **Wdróż na platformie Azure** poniżej, aby zalogować się do platformy Azure i otworzyć szablon Menedżer zasobów.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

1. Wprowadź lub wybierz poniższe wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**.|
    |Lokalizacja|Wartość zostanie automatycznie wypełniona przy użyciu lokalizacji używanej przez grupę zasobów.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. Dla tego szablonu należy używać tylko małych liter i cyfr.|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika — wartość domyślna to **admin**.|
    |Hasło logowania klastra|Podaj hasło. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką i jedną małą literę oraz jeden znak inny niż alfanumeryczny (z wyjątkiem znaków "" "). |
    |Nazwa użytkownika SSH|Podaj nazwę użytkownika, wartość domyślna to sshuser|
    |Hasło ssh|Podaj hasło.|

    ![HBase szablonu Menedżer zasobów](./media/quickstart-resource-manager-template/resource-manager-template-hbase.png)

1. Przejrzyj **warunki i postanowienia**. Następnie wybierz opcję **Zgadzam się na powyższe warunki i**postanowienia, a następnie **Kup**. Otrzymasz powiadomienie, że wdrożenie jest w toku. Utworzenie klastra trwa około 20 minut.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po utworzeniu klastra otrzymasz powiadomienie o **pomyślnym wdrożeniu** za pomocą linku **Przejdź do zasobu** . Strona Grupa zasobów będzie zawierać listę nowego klastra usługi HDInsight oraz domyślny magazyn skojarzony z klastrem. Każdy klaster ma konto [usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md) lub [Azure Data Lake Storage zależności konta](../hdinsight-hadoop-use-data-lake-store.md) . Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

W Azure Portal przejdź do klastra, a następnie wybierz pozycję **Usuń**.

![HBase szablonu Menedżer zasobów](./media/quickstart-resource-manager-template/azure-portal-delete-hbase.png)

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, należy usunąć zarówno klaster usługi HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra Apache HBase w usłudze HDInsight przy użyciu szablonu Menedżer zasobów. W następnym artykule dowiesz się, jak wysyłać zapytania do HBase w usłudze HDInsight przy użyciu powłoki HBase.

> [!div class="nextstepaction"]
> [Zapytanie dotyczące platformy Apache HBase w usłudze Azure HDInsight przy użyciu powłoki HBase](./query-hbase-with-hbase-shell.md)
