---
title: Transport Layer Security w usłudze Azure HDInsight
description: Transport Layer Security (TLS) i Secure Sockets Layer (SSL) to protokoły kryptograficzne zapewniające bezpieczeństwo komunikacji w sieci komputerowej.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183504"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Transport Layer Security w usłudze Azure HDInsight

Połączenia z klastrem usługi HDInsight za pośrednictwem publicznego punktu końcowego klastra są nawiązywane `https://CLUSTERNAME.azurehdinsight.net` za pośrednictwem węzłów bramy klastra. Te połączenia są zabezpieczone przy użyciu protokołu o nazwie TLS. Wymuszanie wyższych wersji protokołu TLS na bramach usprawnia zabezpieczenia tych połączeń. Aby uzyskać więcej informacji na temat tego, dlaczego należy używać nowszych wersji protokołu TLS, zobacz [Rozwiązywanie problemów z protokołem tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

Domyślnie klastry usługi Azure HDInsight akceptują połączenia protokołu TLS 1,2 na publicznych punktach końcowych HTTPS i starszych wersjach w celu zapewnienia zgodności z poprzednimi wersjami. Można kontrolować minimalną wersję protokołu TLS obsługiwaną w węzłach bramy podczas tworzenia klastra przy użyciu szablonu Azure Portal lub Menedżer zasobów. W przypadku portalu wybierz wersję TLS z karty **zabezpieczenia i sieć** podczas tworzenia klastra. W przypadku szablonu Menedżer zasobów w czasie wdrażania należy użyć właściwości **minSupportedTlsVersion** . Aby zapoznać się z przykładowym szablonem, zobacz [minimalny szablon szybkiego startu protokołu TLS 1,2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)w usłudze HDInsight. Ta właściwość obsługuje trzy wartości: "1,0", "1,1" i "1,2", które odnoszą się do protokołów TLS 1.0 +, TLS 1.1 + i TLS 1.2 + odpowiednio.

> [!IMPORTANT]
> Począwszy od 30 czerwca 2020, usługa Azure HDInsight będzie wymuszać stosowanie protokołu TLS 1,2 lub nowszego dla wszystkich połączeń HTTPS. Zalecamy, aby upewnić się, że wszyscy klienci są gotowi do obsługi protokołu TLS 1,2 lub jego nowszych wersji. Aby uzyskać więcej informacji, zobacz [Wymuszanie usługi Azure HDINSIGHT TLS 1,2](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Następne kroki

* [Planowanie sieci wirtualnej dla usługi Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Utwórz sieci wirtualne dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md).
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).
