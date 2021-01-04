---
title: Niestandardowa baza danych Apache Ambari w usłudze Azure HDInsight
description: Dowiedz się, jak tworzyć klastry usługi HDInsight z własną niestandardową bazą danych Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: d6dd67e9e0cd9dcd4afb8f1ae2cc32ccf30617fd
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704949"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Konfigurowanie klastrów usługi HDInsight za pomocą niestandardowej bazy danych Ambari

Apache Ambari upraszcza zarządzanie i monitorowanie klastra Apache Hadoop. Ambari zapewnia łatwy w użyciu interfejs użytkownika sieci Web i interfejs API REST. Usługa Ambari jest dołączana do klastrów usługi HDInsight i służy do monitorowania klastra i wprowadzania zmian w konfiguracji.

W przypadku normalnego tworzenia klastra zgodnie z opisem w innych artykułach, takich jak [Konfigurowanie klastrów w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari jest wdrażany w [S0 Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) , który jest zarządzany przez usługi HDInsight i nie jest dostępny dla użytkowników.

Funkcja Custom Ambari DB pozwala wdrożyć nowy klaster i skonfigurować Ambari w zewnętrznej zarządzanej bazie danych. Wdrożenie jest wykonywane z szablonem Azure Resource Manager. Ta funkcja ma następujące zalety:

- Dostosowanie — wybierasz rozmiar i pojemność przetwarzania bazy danych. W przypadku dużych klastrów przetwarzania intensywnych obciążeń baza danych Ambari z niższymi specyfikacjami może stać się wąskim gardłem dla operacji zarządzania.
- Elastyczność — w razie potrzeby można skalować bazę danych zgodnie z wymaganiami.
- Kontrolka — umożliwia zarządzanie kopiami zapasowymi i zabezpieczeniami dla bazy danych w sposób odpowiadający wymaganiom organizacji.

W pozostałej części tego artykułu omówiono następujące kwestie:

- wymagania dotyczące korzystania z funkcji Custom Ambari DB
- kroki niezbędne do aprowizacji klastrów usługi HDInsight przy użyciu własnej zewnętrznej bazy danych dla platformy Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Wymagania dotyczące niestandardowych baz danych Ambari DB

Możesz wdrożyć niestandardową bazę danych Ambari z wszystkimi typami i wersjami klastra. Wiele klastrów nie może korzystać z tej samej bazy danych Ambari.

Niestandardowa baza danych Ambari ma następujące inne wymagania:

- Nazwa bazy danych nie może zawierać łączników ani spacji
- Musisz mieć istniejący serwer usługi Azure SQL DB i bazę danych.
- Baza danych dostarczana dla Instalatora Ambari musi być pusta. W domyślnym schemacie dbo nie powinno być żadnych tabel.
- Użytkownik użyty do nawiązania połączenia z bazą danych powinien mieć uprawnienia SELECT, CREATE TABLE i INSERT w bazie danych.
- Włącz opcję [zezwalania na dostęp do usług platformy Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) na serwerze, na którym będzie hostowany Ambari.
- Adresy IP zarządzania z usługi HDInsight muszą być dozwolone w regule zapory. Zobacz [adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md) , aby uzyskać listę adresów IP, które należy dodać do reguły zapory na poziomie serwera.

W przypadku hostowania usług Apache Ambari DB w zewnętrznej bazie danych należy pamiętać o następujących kwestiach:

- Użytkownik jest odpowiedzialny za dodatkowe koszty usługi Azure SQL DB, która zawiera Ambari.
- Okresowo twórz kopię zapasową niestandardowej bazy danych Ambari. Azure SQL Database automatycznie generuje kopie zapasowe, ale czas przechowywania kopii zapasowej jest różny. Aby uzyskać więcej informacji, zobacz informacje [o automatycznym SQL Database kopii zapasowych](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Wdrażanie klastrów przy użyciu niestandardowej bazy danych Ambari

Aby utworzyć klaster usługi HDInsight, który używa własnej zewnętrznej bazy danych Ambari, użyj [szablonu niestandardowego szybkiego startu usługi AMBARI DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Edytuj parametry w programie, `azuredeploy.parameters.json` Aby określić informacje o nowym klastrze i bazie danych, która będzie zawierać Ambari.

Wdrożenie można rozpocząć przy użyciu interfejsu wiersza polecenia platformy Azure. Zamień na `<RESOURCEGROUPNAME>` grupę zasobów, w której ma zostać wdrożony klaster.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Następne kroki

- [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight](hdinsight-use-external-metadata-stores.md)
