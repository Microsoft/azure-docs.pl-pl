---
title: Zabezpieczenia warstwy transportu w usłudze Azure HDInsight
description: Zabezpieczenia warstwy transportu (TLS) i warstwa bezpiecznych gniazd (SSL) to protokoły kryptograficzne, które zapewniają bezpieczeństwo komunikacji za pośrednictwem sieci komputerowej.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771965"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Zabezpieczenia warstwy transportu w usłudze Azure HDInsight

Połączenia z klastrem HDInsight za pośrednictwem `https://CLUSTERNAME.azurehdinsight.net` publicznego punktu końcowego klastra są przesyłane przez węzły bramy klastra. Połączenia te są zabezpieczone za pomocą protokołu o nazwie TLS. Wymuszanie wyższych wersji protokołu TLS na bramkach zwiększa bezpieczeństwo tych połączeń. Aby uzyskać więcej informacji na temat powodów używania nowszych wersji protokołu TLS, zobacz [Rozwiązywanie problemu protokołu TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Domyślnie klastry usługi Azure HDInsight akceptują połączenia TLS 1.2 w publicznych punktach końcowych HTTPS i starsze wersje ze względów zgodności z powrotem. Minimalną wersję protokołu TLS obsługiwaną w węzłach bramy można kontrolować za pomocą portalu Azure lub szablonu Menedżera zasobów. W przypadku portalu wybierz wersję protokołu TLS z karty **Zabezpieczenia + sieć** podczas tworzenia klastra. W przypadku szablonu Menedżera zasobów w czasie wdrażania należy użyć właściwości **minSupportedTlsVersion.** Przykładowy szablon można znaleźć w [szablonie szybkiego startu hdInsight minimalny szablon TLS 1.2 .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls) Ta właściwość obsługuje trzy wartości: "1.0", "1.1" i "1.2", które odpowiadają TLS 1.0+, TLS 1.1+ i TLS 1.2+ odpowiednio.

> [!IMPORTANT]
> Począwszy od 30 czerwca 2020 r. usługa Azure HDInsight wymusza wersje protokołu TLS 1.2 lub nowszych dla wszystkich połączeń HTTPS. Zaleca się, aby upewnić się, że wszyscy klienci są gotowe do obsługi TLS 1.2 lub nowszych wersji. Aby uzyskać więcej informacji, zobacz [Azure HDInsight TLS 1.2 Wymuszanie](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Następne kroki

* [Planowanie sieci wirtualnej dla usługi Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md).
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).
