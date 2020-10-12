---
title: Tworzenie klastrów Apache Hadoop przy użyciu szablonów — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry dla usługi HDInsight przy użyciu szablonów Menedżer zasobów
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 533fe0f391779a5bbe53bf7f2a136c3e9fb19017
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86080622"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżer zasobów

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W tym artykule przedstawiono kilka sposobów tworzenia klastrów usługi Azure HDInsight przy użyciu [szablonów Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). Aby dowiedzieć się więcej o innych narzędziach i funkcjach tworzenia klastra, kliknij selektor karty w górnej części tej strony. Zobacz również [metody tworzenia klastra](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Szablon Menedżer zasobów ułatwia tworzenie następujących zasobów dla aplikacji w ramach jednej, skoordynowanej operacji:

* Klastry usługi HDInsight i ich zasoby zależne (takie jak domyślne konto magazynu).
* Inne zasoby (takie jak Azure SQL Database korzystania z oprogramowania [Apache Sqoop](https://sqoop.apache.org/)).

W szablonie należy zdefiniować zasoby, które są potrzebne dla aplikacji. Należy również określić parametry wdrożenia do wartości wejściowych dla różnych środowisk. Szablon składa się z notacji JSON i wyrażeń, które służą do konstruowania wartości dla wdrożenia.

Przykłady szablonów usługi HDInsight można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Użyj [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) międzyplatformowego z [rozszerzeniem Menedżer zasobów](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) lub edytorem tekstu, aby zapisać szablon w pliku na stacji roboczej.

Aby uzyskać więcej informacji na temat szablonów Menedżer zasobów, zobacz następujące artykuły i przykłady:

* [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Wdrażanie aplikacji za pomocą szablonów Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* Dokumentacja szablonu [Microsoft. HDInsight/klastrów](/azure/templates/microsoft.hdinsight/allversions)
* [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generowanie szablonów

Menedżer zasobów umożliwia eksportowanie szablonu Menedżer zasobów z istniejących zasobów w ramach subskrypcji przy użyciu różnych narzędzi. Możesz użyć wygenerowanego szablonu, aby dowiedzieć się więcej o składni szablonu lub aby zautomatyzować ponowne wdrożenie rozwiązania, w razie potrzeby. Aby uzyskać więcej informacji, zobacz [Eksportowanie szablonów](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Wdrażanie przy użyciu portalu

Szablon Menedżer zasobów można wdrożyć przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Szablon Menedżer zasobów można wdrożyć przy użyciu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) i [wdrażanie prywatnego szablonu Menedżer zasobów z tokenem SAS i Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Szablon Menedżer zasobów można wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md) oraz [wdrażanie szablonu prywatnego Menedżer zasobów z tokenem SAS i interfejsem wiersza polecenia platformy Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

Szablon Menedżer zasobów można wdrożyć za pomocą interfejsu API REST. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Menedżer zasobów interfejsu API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio

 Użyj programu Visual Studio, aby utworzyć projekt grupy zasobów i wdrożyć go na platformie Azure za pomocą interfejsu użytkownika. Wybierasz typ zasobów do uwzględnienia w projekcie. Te zasoby są automatycznie dodawane do szablonu Menedżer zasobów. Projekt udostępnia także skrypt programu PowerShell służący do wdrażania szablonu.

Aby zapoznać się z wprowadzeniem do korzystania z programu Visual Studio z grupami zasobów, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono kilka sposobów tworzenia klastra usługi HDInsight. Więcej informacji można znaleźć w następujących artykułach:

* Aby uzyskać więcej szablonów związanych z usługą HDInsight, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Aby zapoznać się z przykładem wdrażania zasobów za pośrednictwem biblioteki klienta .NET, zobacz [wdrażanie zasobów przy użyciu bibliotek .NET i szablonu](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby zapoznać się z szczegółowym przykładem wdrażania aplikacji, zobacz temat [udostępnianie i wdrażanie mikrousług na platformie Azure](../app-service/deploy-complex-application-predictably.md).
* Aby uzyskać wskazówki dotyczące wdrażania rozwiązania w różnych środowiskach, zobacz [Development and test environments in Microsoft Azure](../solution-dev-test-environments.md) (Środowiska projektowe i testowe na platformie Microsoft Azure).
* Aby dowiedzieć się więcej na temat sekcji szablonu Azure Resource Manager, zobacz [Tworzenie szablonów](../azure-resource-manager/templates/template-syntax.md).
* Aby zapoznać się z listą funkcji, których można użyć w szablonie Azure Resource Manager, zobacz [funkcje szablonu](../azure-resource-manager/templates/template-functions.md).
