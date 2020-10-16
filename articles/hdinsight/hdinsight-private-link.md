---
title: Zabezpiecz i Izoluj klastry usługi Azure HDInsight za pomocą prywatnego linku (wersja zapoznawcza)
description: Informacje o sposobie izolowania klastrów usługi Azure HDInsight w sieci wirtualnej przy użyciu prywatnego linku platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: c51b99ed04357cdebaabbde2b2bd0400adcfef30
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92134184"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Zabezpiecz i Izoluj klastry usługi Azure HDInsight za pomocą prywatnego linku (wersja zapoznawcza)

W przypadku [domyślnej architektury sieci wirtualnej](./hdinsight-virtual-network-architecture.md)w usłudze Azure HDInsight dostawca zasobów usługi HDINSIGHT (RP) komunikuje się z klastrem przy użyciu publicznych adresów IP. Niektóre scenariusze wymagają pełnej izolacji sieci bez używania publicznych adresów IP. Ten artykuł zawiera informacje na temat zaawansowanych kontrolek, których można użyć do utworzenia prywatnego klastra usługi HDInsight. Aby uzyskać informacje dotyczące sposobu ograniczania ruchu do i z klastra bez pełnej izolacji sieci, zobacz [Kontrola ruchu sieciowego w usłudze Azure HDInsight](./control-network-traffic.md).

Prywatne klastry usługi HDInsight można tworzyć przez skonfigurowanie określonych właściwości sieci w szablonie Azure Resource Manager (ARM). Istnieją dwie właściwości, które służą do tworzenia prywatnych klastrów usługi HDInsight:

* Usuń publiczne adresy IP, ustawiając `resourceProviderConnection` wychodzące.
* Włącz łącze prywatne platformy Azure i Użyj [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) , ustawiając ustawienie `privateLink` na włączone.

## <a name="remove-public-ip-addresses"></a>Usuń publiczne adresy IP

Domyślnie element RP usługi HDInsight używa połączenia *przychodzącego* z klastrem przy użyciu publicznych adresów IP. Gdy `resourceProviderConnection` Właściwość Network jest ustawiona na *wychodzące*, powoduje odwrócenie połączeń do jednostki uzależnionej usługi HDInsight, dzięki czemu połączenia są zawsze inicjowane z wnętrza klastra do jednostki uzależnionej. Bez połączenia przychodzącego nie ma potrzeby stosowania w przypadku tagów usługi przychodzącej ani publicznych adresów IP.

Podstawowe usługi równoważenia obciążenia używane w domyślnej architekturze sieci wirtualnej automatycznie zapewniają publiczne translatora adresów sieciowych (NAT), aby uzyskać dostęp do wymaganych zależności wychodzących, takich jak HDInsight RP. Jeśli chcesz ograniczyć łączność wychodzącą do publicznej sieci Internet, możesz [skonfigurować zaporę](./hdinsight-restrict-outbound-traffic.md), ale nie jest to wymagane.

Skonfigurowanie `resourceProviderConnection` do wychodzącego umożliwia również dostęp do zasobów specyficznych dla klastra, takich jak Azure Data Lake Storage Gen2 lub zewnętrznych magazynów metadanych, za pomocą prywatnych punktów końcowych. Przed utworzeniem klastra usługi HDInsight należy skonfigurować prywatne punkty końcowe i wpisy DNS. Zalecamy utworzenie i udostępnienie wszystkich potrzebnych zewnętrznych baz danych SQL, takich jak Apache Ranger, Ambari, Oozie i Hive, podczas tworzenia klastra.

Prywatne punkty końcowe dla Azure Key Vault nie są obsługiwane. Jeśli używasz Azure Key Vault do szyfrowania CMK w spoczynku, punkt końcowy Azure Key Vault musi być dostępny z poziomu podsieci usługi HDInsight bez prywatnego punktu końcowego.

Na poniższym diagramie przedstawiono, jak może wyglądać potencjalna architektura sieci wirtualnej usługi HDInsight `resourceProviderConnection` :

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagram architektury usługi HDInsight przy użyciu połączenia dostawcy zasobów wychodzących":::

Po utworzeniu klastra należy skonfigurować odpowiednie rozwiązanie DNS. Następująca nazwa kanoniczna rekordu DNS (CNAME) jest tworzona w publicznej strefie DNS zarządzanej przez platformę Azure: `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

Aby uzyskać dostęp do klastra przy użyciu nazw FQDN klastra, możesz bezpośrednio użyć prywatnych adresów IP wewnętrznego modułu równoważenia obciążenia lub użyć własnej strefy Prywatna strefa DNS, aby zastąpić punkty końcowe klastra odpowiednio do potrzeb. Na przykład można mieć strefę Prywatna strefa DNS, `azurehdinsight.net` . i Dodaj prywatne adresy IP w razie konieczności:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Włącz link prywatny

Link prywatny, który jest domyślnie wyłączony, wymaga rozległej wiedzy o sieci, aby skonfigurować trasy zdefiniowane przez użytkownika (UDR) i reguły zapory prawidłowo przed utworzeniem klastra. Dostęp do prywatnego linku do klastra jest dostępny tylko wtedy, gdy `resourceProviderConnection` Właściwość Network jest ustawiona na ruch *wychodzący* zgodnie z opisem w poprzedniej sekcji.

Gdy `privateLink` jest ustawiona na wartość *enable*, są tworzone wewnętrzne [standardowe moduły równoważenia obciążenia](../load-balancer/load-balancer-overview.md) , a usługa Azure Private link jest obsługiwana dla każdego modułu wystawcy. Usługa link prywatny umożliwia dostęp do klastra usługi HDInsight z prywatnych punktów końcowych.

Usługi równoważenia obciążenia w warstwie Standardowa nie zapewniają automatycznie wychodzącego NAT, takiego jak podstawowe usługi równoważenia obciążenia. Należy podać własne rozwiązanie NAT, takie jak [Virtual Network NAT](../virtual-network/nat-overview.md) lub [Zapora](./hdinsight-restrict-outbound-traffic.md), dla zależności wychodzących. Klaster usługi HDInsight nadal potrzebuje dostępu do jego zależności wychodzących. Jeśli te zależności wychodzące nie są dozwolone, tworzenie klastra może się nie powieść.

### <a name="prepare-your-environment"></a>Przygotowywanie środowiska

Na poniższym diagramie przedstawiono przykład konfiguracji sieci wymaganej przed utworzeniem klastra. W tym przykładzie cały ruch wychodzący jest [wymuszany](../firewall/forced-tunneling.md) w zaporze platformy Azure przy użyciu programu UDR, a przed utworzeniem klastra wymagane są "dozwolone" w zaporze. W przypadku klastrów pakiet Enterprise Security połączenie sieciowe z Azure Active Directory Domain Services może być zapewnione przez komunikację równorzędną sieci wirtualnych.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagram architektury usługi HDInsight przy użyciu połączenia dostawcy zasobów wychodzących":::

Po skonfigurowaniu sieci można utworzyć klaster z połączeniem dostawcy zasobów wychodzących i z włączonym linkiem prywatnym, jak pokazano na poniższej ilustracji. W tej konfiguracji nie ma publicznych adresów IP i prywatnych usług linków dla każdego standardowego modułu równoważenia obciążenia.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagram architektury usługi HDInsight przy użyciu połączenia dostawcy zasobów wychodzących":::

### <a name="access-a-private-cluster"></a>Dostęp do prywatnego klastra

Aby uzyskać dostęp do prywatnych klastrów, można używać prywatnych adresów IP wewnętrznego modułu równoważenia obciążenia bezpośrednio lub używać prywatnych połączeń DNS i prywatnych punktów końcowych. Gdy `privateLink` ustawienie ma wartość włączone, można utworzyć własne prywatne punkty końcowe i skonfigurować rozpoznawanie nazw DNS za pomocą prywatnych stref DNS.

Prywatne wpisy linku utworzone w publicznej strefie DNS zarządzanej przez platformę Azure `azurehdinsight.net` są następujące:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

Na poniższej ilustracji przedstawiono przykład prywatnych wpisów DNS wymaganych do uzyskania dostępu do klastra z sieci wirtualnej, która nie jest równorzędna lub nie ma bezpośredniego wglądu w usługi równoważenia obciążenia klastra. Strefy prywatnej platformy Azure można użyć do przesłania `*.privatelink.azurehdinsight.net` nazw FQDN i rozpoznawania własnych adresów IP prywatnych punktów końcowych.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagram architektury usługi HDInsight przy użyciu połączenia dostawcy zasobów wychodzących":::

## <a name="arm-template-properties"></a>Właściwości szablonu ARM

Poniższy fragment kodu JSON zawiera dwie właściwości sieci, które należy skonfigurować w szablonie ARM w celu utworzenia prywatnego klastra usługi HDInsight.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Pełny szablon z wieloma funkcjami zabezpieczeń systemu HDInsight Enterprise, w tym link prywatny, można znaleźć w temacie [szablon zabezpieczeń usługi HDInsight Enterprise](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

## <a name="next-steps"></a>Następne kroki

* [pakiet Enterprise Security usługi Azure HDInsight](enterprise-security-package.md)
* [Ogólne informacje i wskazówki dotyczące zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/general-guidelines.md)
