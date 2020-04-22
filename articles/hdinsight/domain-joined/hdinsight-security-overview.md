---
title: Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight
description: Poznaj różne metody zapewnienia bezpieczeństwa przedsiębiorstwa w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 797767e0c463161f29e486aef7db0ccaf459e299
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733549"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight

Usługa Azure HDInsight oferuje szereg metod, które odpowiadają twoim potrzebom w zakresie zabezpieczeń przedsiębiorstwa. Większość z tych rozwiązań nie jest domyślnie aktywowana. Ta elastyczność pozwala wybrać funkcje zabezpieczeń, które są dla Ciebie najważniejsze. I pomaga uniknąć płacenia za funkcje, które nie chcesz. Ta elastyczność oznacza również, że twoim obowiązkiem jest upewnienie się, że odpowiednie rozwiązania są włączone dla twojej konfiguracji i środowiska.

W tym artykule omówiono rozwiązania zabezpieczeń, dzieląc rozwiązania zabezpieczeń na cztery tradycyjne filary zabezpieczeń: zabezpieczenia obwodowe, uwierzytelnianie, autoryzacja i szyfrowanie.

W tym artykule przedstawiono również **pakiet zabezpieczeń usługi Azure HDInsight Enterprise Security Package (ESP),** który zapewnia uwierzytelnianie oparte na usłudze Active Directory, obsługę wielu użytkowników i kontrolę dostępu opartą na rolach dla klastrów HDInsight.

## <a name="enterprise-security-pillars"></a>Filary zabezpieczeń przedsiębiorstwa

Jednym ze sposobów patrzenia na zabezpieczenia przedsiębiorstwa dzieli rozwiązania zabezpieczeń na cztery główne grupy w zależności od typu kontroli. Grupy te są również nazywane filarami zabezpieczeń i są następującymi typami: zabezpieczenia obwodowe, uwierzytelnianie, autoryzacja i szyfrowanie.

### <a name="perimeter-security"></a>Zabezpieczenia obwodowe

Bezpieczeństwo obwodowe w HDInsight uzyskuje się za pośrednictwem [sieci wirtualnych.](../hdinsight-plan-virtual-network-deployment.md) Administrator przedsiębiorstwa może utworzyć klaster wewnątrz sieci wirtualnej (VNET) i użyć sieciowych grup zabezpieczeń (NSG) w celu ograniczenia dostępu do sieci wirtualnej. Tylko dozwolone adresy IP w przychodzących regułach sieciowej grupy sieciowej mogą komunikować się z klastrem HDInsight. Ta konfiguracja zapewnia zabezpieczenia obwodowe.

Wszystkie klastry wdrożone w sieci wirtualnej będą również miały prywatny punkt końcowy. Punkt końcowy jest rozpoznawany na prywatny adres IP wewnątrz sieci wirtualnej dla prywatnego dostępu HTTP do bram klastra.

### <a name="authentication"></a>Authentication

[Pakiet zabezpieczeń przedsiębiorstwa](apache-domain-joined-architecture.md) firmy HDInsight zapewnia uwierzytelnianie oparte na usłudze Active Directory, obsługę wielu użytkowników i kontrolę dostępu opartą na rolach. Integracja z usługą Active Directory jest osiągana za pomocą [usług domenowych Active Directory platformy Azure](../../active-directory-domain-services/overview.md). Dzięki tym funkcjom można utworzyć klaster HDInsight przyłączony do domeny usługi Active Directory. Następnie skonfiguruj listę pracowników z przedsiębiorstwa, którzy mogą uwierzytelniać się w klastrze.

Dzięki tej konfiguracji pracownicy przedsiębiorstwa mogą logować się do węzłów klastra przy użyciu poświadczeń domeny. Mogą również używać swoich poświadczeń domeny do uwierzytelniania przy użyciu innych zatwierdzonych punktów końcowych. Podobnie jak Apache Ambari Views, ODBC, JDBC, PowerShell i REST INTERFEJSÓW API do interakcji z klastrem.

### <a name="authorization"></a>Autoryzacja

Najlepszym rozwiązaniem, za którymi podąża większość przedsiębiorstw, jest upewnienie się, że nie każdy pracownik ma pełny dostęp do wszystkich zasobów przedsiębiorstwa. Podobnie administrator może zdefiniować zasady kontroli dostępu oparte na rolach dla zasobów klastra. Ta akcja jest dostępna tylko w klastrach ESP.

Administrator usługi Hadoop może skonfigurować kontrolę dostępu opartą na rolach (RBAC). Konfiguracje bezpieczne Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)i [Kafka](apache-domain-joined-run-kafka.md) z Apache Range wtyczek. Konfigurowanie zasad RBAC umożliwia skojarzenie uprawnień z rolą w organizacji. Ta warstwa abstrakcji ułatwia zapewnienie, że ludzie mają tylko uprawnienia potrzebne do wykonywania swoich obowiązków służbowych. Ranger umożliwia również inspekcję dostępu do danych pracowników i wszelkich zmian dokonanych w zasadach kontroli dostępu.

Na przykład administrator może skonfigurować środowisko [Apache Ranger](https://ranger.apache.org/) do ustawiania zasad kontroli dostępu dla usługi Hive. Ta funkcja zapewnia filtrowanie na poziomie wiersza i kolumny (maskowanie danych). I filtruje poufne dane od nieautoryzowanych użytkowników.

### <a name="auditing"></a>Inspekcja

Inspekcja dostępu do zasobów klastra jest niezbędna do śledzenia nieautoryzowanego lub niezamierzonego dostępu do zasobów. Jest to równie ważne, jak ochrona zasobów klastra przed nieautoryzowanym dostępem.

Administrator może wyświetlać i raportować cały dostęp do zasobów i danych klastra HDInsight. Administrator może wyświetlać i zgłaszać zmiany w zasadach kontroli dostępu.

Aby uzyskać dostęp do dzienników inspekcji Apache Ranger i Ambari oraz dzienników dostępu ssh, [włącz usługę Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing). I wyświetlić tabele, które zawierają rekordy inspekcji.

### <a name="encryption"></a>Szyfrowanie

Ochrona danych jest ważna dla spełnienia wymagań dotyczących zabezpieczeń i zgodności organizacji. Wraz z ograniczeniem dostępu do danych od nieautoryzowanych pracowników należy je zaszyfrować.

Usługa Azure storage i Data Lake Storage Gen1/Gen2 obsługują przezroczyste [szyfrowanie danych](../../storage/common/storage-service-encryption.md) po stronie serwera w spoczynku. Bezpieczne klastry HDInsight bezproblemowo współpracują z szyfrowaniem danych po stronie serwera w stanie spoczynku.

### <a name="compliance"></a>Zgodność

Oferty zgodności platformy Azure są oparte na różnych typach gwarancji, w tym formalnych certyfikatach. Ponadto zaświadczenia, walidacji i autoryzacji. Oceny przeprowadzane przez niezależne zewnętrzne firmy audytorów. Zmiany umowy, samoocenę i dokumenty dotyczące porad dotyczących klientów opracowane przez firmę Microsoft. Aby uzyskać informacje o zgodności usługi HDInsight, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trust-center) i [omówienie zgodności platformy Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Model wspólnej odpowiedzialności

Na poniższej ilustracji podsumowano główne obszary zabezpieczeń systemu i rozwiązania zabezpieczeń, które są dostępne w każdym z nich. Podkreśla również, które obszary bezpieczeństwa są twoim obowiązkiem jako klienta. I które obszary są odpowiedzialne za HDInsight jako usługodawcy.

![Diagram współodpowiedzialności hdinsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Poniższa tabela zawiera łącza do zasobów dla każdego typu rozwiązania zabezpieczeń.

| Obszar bezpieczeństwa | Dostępne rozwiązania | Strona odpowiedzialna |
|---|---|---|
| Zabezpieczenia dostępu do danych | Konfigurowanie [list kontroli dostępu](../../storage/blobs/data-lake-storage-access-control.md) dla usługi Azure Data Lake Storage Gen1 i Gen2  | Klient |
|  | Włącz właściwość ["Bezpieczne wymagane przeniesienie"](../../storage/common/storage-require-secure-transfer.md) na kontach magazynu. | Klient |
|  | Konfigurowanie [zapór usługi Azure Storage](../../storage/common/storage-network-security.md) i sieci wirtualnych | Klient |
|  | Konfigurowanie [punktów końcowych usługi sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) dla usługi Cosmos DB i usługi Azure SQL [DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Klient |
|  | Upewnij się, że [szyfrowanie TLS](../../storage/common/storage-security-tls.md) jest włączone dla przesyłanych danych. | Klient |
|  | Konfigurowanie [kluczy zarządzanych przez klienta](../../storage/common/storage-encryption-keys-portal.md) do szyfrowania usługi Azure Storage | Klient |
| Zabezpieczenia aplikacji i oprogramowania pośredniczącego | Integracja z usługą AAD-DS i [konfigurowanie uwierzytelniania](apache-domain-joined-configure-using-azure-adds.md) | Klient |
|  | Konfigurowanie zasad [autoryzacji Łowców Apache](apache-domain-joined-run-hive.md) | Klient |
|  | Korzystanie z [dzienników usługi Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Klient |
| Bezpieczeństwo systemu operacyjnego | Tworzenie klastrów z najnowszym bezpiecznym obrazem podstawowym | Klient |
|  | Zapewnianie [łatanie systemu operacyjnego](../hdinsight-os-patching.md) w regularnych odstępach czasu | Klient |
| Bezpieczeństwo sieci | Konfigurowanie [sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurowanie [reguł grupy zabezpieczeń sieci przychodzącej](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Klient |
|  | Konfigurowanie [ograniczenia ruchu wychodzącego](../hdinsight-restrict-outbound-traffic.md) za pomocą zapory | Klient |
| Zwirtualizowanej infrastruktury | Nie dotyczy | HDInsight (dostawca chmury) |
| Bezpieczeństwo infrastruktury fizycznej | Nie dotyczy | HDInsight (dostawca chmury) |

## <a name="next-steps"></a>Następne kroki

* [Planowanie klastrów HDInsight za pomocą esp](apache-domain-joined-architecture.md)
* [Konfigurowanie klastrów usługi HDInsight za pomocą usługi ESP](apache-domain-joined-configure.md)
* [Zarządzanie klastrami HDInsight za pomocą esp](apache-domain-joined-manage.md)
