---
title: Szyfrowanie usługi Azure HDInsight w trakcie przesyłania
description: Dowiedz się więcej o funkcjach zabezpieczeń, aby zapewnić szyfrowanie podczas przesyłania dla klastra usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946849"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Szyfrowanie IPSec podczas przesyłania dla usługi Azure HDInsight

W tym artykule omówiono implementację szyfrowania podczas przesyłania komunikacji między węzłami klastra usługi Azure HDInsight.

## <a name="background"></a>Tło

Usługa Azure HDInsight oferuje różne funkcje zabezpieczeń w celu zabezpieczania danych w przedsiębiorstwie. Te rozwiązania są pogrupowane pod filarami zabezpieczeń obwodowych, uwierzytelniania, autoryzacji, inspekcji, szyfrowania i zgodności. Szyfrowanie może być stosowane do danych przechowywanych w czasie spoczynku i podczas przesyłania.

Szyfrowanie w spoczynku obejmuje szyfrowanie po stronie serwera na kontach usługi Azure Storage, a także szyfrowanie dysków na maszynach wirtualnych platformy Azure, które są częścią klastra usługi HDInsight.

Szyfrowanie danych przesyłanych w usłudze HDInsight jest realizowane przy użyciu [Transport Layer Security (TLS)](../transport-layer-security.md) w celu uzyskania dostępu do bram klastra i [zabezpieczeń protokołu internetowego (IPSec)](https://wikipedia.org/wiki/IPsec) między węzłami klastra. Protokół IPSec może być opcjonalnie włączony między wszystkimi węzłami głównymi, węzłami roboczymi, węzłami krawędzi, węzłami dozorcy, a także węzłami [brokera i identyfikatora](./identity-broker.md) bramy.

## <a name="enable-encryption-in-transit"></a>Włącz szyfrowanie podczas przesyłania

### <a name="azure-portal"></a>Witryna Azure Portal

Aby utworzyć nowy klaster z włączonym szyfrowaniem przy użyciu Azure Portal, wykonaj następujące czynności:

1. Rozpocznij proces tworzenia klastra normalnego. Zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) na potrzeby początkowych kroków tworzenia klastra.
1. Ukończ karty **podstawowe** i **magazynowe** . Przechodzenie do karty **zabezpieczenia i sieć** .

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Utwórz klaster — karta Zabezpieczenia i sieć.":::

1. Na karcie **zabezpieczenia i sieć** zaznacz pole wyboru **Włącz szyfrowanie podczas przesyłania** .

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Tworzenie klastra — Włącz szyfrowanie podczas przesyłania.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Tworzenie klastra z włączonym szyfrowaniem przy użyciu interfejsu wiersza polecenia platformy Azure

Szyfrowanie podczas przesyłania jest włączone przy użyciu `isEncryptionInTransitEnabled` właściwości.

Możesz [pobrać przykładowy plik szablonu i parametru](https://github.com/Azure-Samples/hdinsight-enterprise-security). Przed użyciem szablonu i poniższego fragmentu kodu interfejsu wiersza polecenia platformy Azure Zastąp następujące symbole zastępcze odpowiednimi wartościami:

| Symbol zastępczy | Opis |
|---|---|
| `<SUBSCRIPTION_ID>` | Identyfikator subskrypcji platformy Azure |
| `<RESOURCE_GROUP>` | Grupa zasobów, w której ma zostać utworzony nowy klaster i konto magazynu. |
| `<STORAGEACCOUNTNAME>` | Istniejące konto magazynu, które ma być używane z klastrem. Nazwa powinna mieć postać `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | Nazwa klastra usługi HDInsight. |
| `<PASSWORD>` | Wybrane hasło do logowania się do klastra przy użyciu protokołu SSH i pulpitu nawigacyjnego Ambari. |
| `<VNET_NAME>` | Sieć wirtualna, w której zostanie wdrożony klaster. |

Poniższy fragment kodu wykonuje następujące czynności wstępne:

1. Loguje się do konta platformy Azure.
1. Ustawia aktywną subskrypcję, w której będą wykonywane operacje tworzenia.
1. Tworzy nową grupę zasobów dla nowych działań wdrożenia.
1. Wdróż szablon, aby utworzyć nowy klaster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Następne kroki

* [Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](hdinsight-security-overview.md)
* [Synchronizuj Azure Active Directory użytkowników z klastrem usługi HDInsight](../disk-encryption.md).
