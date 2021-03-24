---
title: Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight
description: Poznaj różne metody zapewniające bezpieczeństwo przedsiębiorstw w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: 713559a747b58e65d04c8310755baedc93db929e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863160"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight

Usługa Azure HDInsight oferuje różne metody zaspokajania potrzeb związanych z bezpieczeństwem przedsiębiorstwa. Większość z tych rozwiązań nie jest domyślnie aktywowana. Ta elastyczność umożliwia wybranie funkcji zabezpieczeń, które są najważniejsze dla Ciebie i pomaga uniknąć płacenia za niepotrzebne funkcje. Taka elastyczność oznacza również, że odpowiedzialność za zapewnienie, że poprawne rozwiązania zostały włączone dla instalacji i środowiska.

Ten artykuł dotyczy rozwiązań zabezpieczeń dzielących rozwiązania zabezpieczeń na cztery tradycyjne filary zabezpieczeń: zabezpieczenia obwodowe, uwierzytelnianie, autoryzacja i szyfrowanie.

W tym artykule wprowadzono również **pakiet Enterprise Security usługi Azure HDInsight (ESP)**, która zapewnia uwierzytelnianie oparte na Active Directory, obsługę wieloużytkownikom oraz kontrolę dostępu opartą na rolach dla klastrów usługi HDInsight.

## <a name="enterprise-security-pillars"></a>Filary zabezpieczeń przedsiębiorstwa

Jednym ze sposobów na wyszukanie zabezpieczeń przedsiębiorstwa jest dzielenie rozwiązań zabezpieczeń na cztery główne grupy w oparciu o typ formantu. Te grupy są również nazywane filarami zabezpieczeń i są następującymi typami: zabezpieczenia obwodowe, uwierzytelnianie, autoryzacja i szyfrowanie.

### <a name="perimeter-security"></a>Zabezpieczenia obwodu

Zabezpieczenia obwodowe w usłudze HDInsight są realizowane za poorednictwem [sieci wirtualnych](../hdinsight-plan-virtual-network-deployment.md). Administrator przedsiębiorstwa może utworzyć klaster w sieci wirtualnej (VNET) i użyć sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu do sieci wirtualnej. Tylko dozwolone adresy IP w regułach sieciowej grupy zabezpieczeń dla ruchu przychodzącego mogą komunikować się z klastrem usługi HDInsight. Ta konfiguracja zapewnia ochronę obwodową.

Wszystkie klastry wdrożone w sieci wirtualnej również mają prywatny punkt końcowy. Punkt końcowy jest rozpoznawany jako prywatny adres IP w sieci wirtualnej na potrzeby prywatnego dostępu HTTP do bram klastra.

### <a name="authentication"></a>Authentication

[Pakiet Enterprise Security](apache-domain-joined-architecture.md) z usługi HDInsight zapewnia uwierzytelnianie oparte na Active Directoryach, obsługa przez wiele użytkowników oraz kontrolę dostępu opartą na rolach. Integracja Active Directory jest realizowana przy użyciu [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Dzięki tym funkcjom można utworzyć klaster usługi HDInsight przyłączony do domeny Active Directory. Następnie skonfiguruj listę pracowników w przedsiębiorstwie, którzy mogą uwierzytelniać się w klastrze.

W przypadku tej konfiguracji pracownicy przedsiębiorstwa mogą zalogować się do węzłów klastra przy użyciu ich poświadczeń domeny. Mogą również używać poświadczeń domeny do uwierzytelniania z innymi zatwierdzonymi punktami końcowymi. Takie jak Apache Ambari views, ODBC, JDBC, PowerShell i interfejsy API REST do współpracy z klastrem.

### <a name="authorization"></a>Autoryzacja

Najlepszym rozwiązaniem w przypadku większości przedsiębiorstw jest upewnienie się, że nie każdy pracownik ma pełny dostęp do wszystkich zasobów przedsiębiorstwa. Analogicznie, administrator może definiować zasady kontroli dostępu opartej na rolach dla zasobów klastra. Ta akcja jest dostępna tylko w klastrach ESP.

Administrator usługi Hadoop może skonfigurować kontrolę dostępu opartą na rolach (RBAC). Konfiguracje Secure [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)i [Kafka](apache-domain-joined-run-kafka.md) z dodatkami Apache Ranger. Skonfigurowanie zasad RBAC umożliwia kojarzenie uprawnień z rolą w organizacji. Ta warstwa abstrakcji ułatwia zapewnienie, że osoby mają tylko uprawnienia niezbędne do wykonania swoich obowiązków służbowych. Ranger umożliwia także inspekcję dostępu do danych pracowników i wszelkich zmian dokonanych w zasadach kontroli dostępu.

Na przykład administrator może skonfigurować środowisko [Apache Ranger](https://ranger.apache.org/) do ustawiania zasad kontroli dostępu dla usługi Hive. Ta funkcja zapewnia filtrowanie na poziomie wiersza i na poziomie kolumny (Maskowanie danych). I filtruje dane poufne przed nieautoryzowanymi użytkownikami.

### <a name="auditing"></a>Inspekcja

Inspekcja dostępu do zasobów klastra jest niezbędna do śledzenia nieautoryzowanego lub niezamierzonego dostępu do zasobów. Jest to ważne, aby chronić zasoby klastra przed nieautoryzowanym dostępem.

Administrator może wyświetlić i zgłosić cały dostęp do zasobów i danych klastra usługi HDInsight. Administrator może wyświetlać i raportować zmiany zasad kontroli dostępu.

Aby uzyskać dostęp do dzienników inspekcji oprogramowania Apache Ranger i Ambari oraz dzienników dostępu SSH, [włącz Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) i Wyświetl tabele, które udostępniają rekordy inspekcji.

### <a name="encryption"></a>Szyfrowanie

Ochrona danych jest istotna dla spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Wraz z ograniczaniem dostępu do danych przed nieautoryzowanymi pracownikami należy go zaszyfrować.

Usługa HDInsight obsługuje szyfrowanie danych przechowywane zarówno w przypadku zarządzanych przez platformę, jak i [kluczy zarządzanych przez klienta](../disk-encryption.md). Szyfrowanie danych w trakcie przesyłania jest obsługiwane zarówno przy użyciu protokołów TLS, jak i IPSec. Aby uzyskać więcej informacji, zobacz [szyfrowanie w usłudze Azure HDInsight](encryption-in-transit.md) .

### <a name="compliance"></a>Zgodność

Oferty zgodności z platformą Azure są oparte na różnych typach gwarancji, w tym w formalnych certyfikatach. Ponadto poświadczenia, walidacje i autoryzacje. Oceny opracowane przez niezależne firmy audytorów innych firm. Umowne zmiany, samooceny i dokumenty wskazówki klienta utworzone przez firmę Microsoft. Informacje o zgodności usługi HDInsight można znaleźć w [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trust-center) i [Omówienie zgodności Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Model wspólnej odpowiedzialności

Poniższy obraz podsumowuje główne obszary zabezpieczeń systemu i dostępne dla Ciebie rozwiązania zabezpieczeń. Wyróżnia także, które obszary zabezpieczeń są odpowiedzialne za klienta. I które obszary są odpowiedzialne za usługę HDInsight jako usługodawcę.

:::image type="content" source="./media/hdinsight-security-overview/hdinsight-shared-responsibility.png" alt-text="Udostępniony diagram obowiązków usługi HDInsight" border="false":::

Poniższa tabela zawiera linki do zasobów dla każdego typu rozwiązania zabezpieczeń.

| Obszar zabezpieczeń | Dostępne rozwiązania | Osoba odpowiedzialna |
|---|---|---|
| Zabezpieczenia dostępu do danych | Konfigurowanie [kontroli dostępu listy ACL](../../storage/blobs/data-lake-storage-access-control.md) dla Azure Data Lake Storage Gen1 i Gen2  | Customer |
|  | Włącz Właściwość ["wymagany bezpieczny transfer"](../../storage/common/storage-require-secure-transfer.md) na kontach magazynu. | Customer |
|  | Konfigurowanie zapór i sieci wirtualnych [usługi Azure Storage](../../storage/common/storage-network-security.md) | Customer |
|  | Konfigurowanie [punktów końcowych usługi sieci wirtualnej platformy Azure](../../virtual-network/virtual-network-service-endpoints-overview.md) dla Cosmos DB i [usługi Azure SQL DB](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) | Customer |
|  | Upewnij się, że funkcja [szyfrowania w ramach przesyłania](./encryption-in-transit.md) jest włączona, aby używać protokołów TLS i IPSec do komunikacji wewnątrz klastra. | Customer |
|  | Konfigurowanie [kluczy zarządzanych przez klienta](../../storage/common/customer-managed-keys-configure-key-vault.md) do szyfrowania za pomocą usługi Azure Storage | Customer |
|  | Kontroluj dostęp do danych przez pomoc techniczną platformy Azure przy użyciu [skrytki klienta](../../security/fundamentals/customer-lockbox-overview.md) | Customer |
| Zabezpieczenia aplikacji i oprogramowania pośredniczącego | Integracja z usługą AAD-DS oraz [Konfigurowanie protokołu ESP](apache-domain-joined-configure-using-azure-adds.md) lub korzystanie z [Hib do uwierzytelniania OAuth](identity-broker.md)| Customer |
|  | Konfigurowanie zasad [autoryzacji Apache Ranger](apache-domain-joined-run-hive.md) | Customer |
|  | Korzystanie z [dzienników Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Customer |
| Zabezpieczenia systemu operacyjnego | Tworzenie klastrów z najnowszym bezpiecznym obrazem podstawowym | Customer |
|  | Zapewnianie [stosowania poprawek systemu operacyjnego](../hdinsight-os-patching.md) w regularnych odstępach czasu | Customer |
|  | Zapewnij [szyfrowanie dysków CMK dla maszyn wirtualnych](../disk-encryption.md) | Customer |
| Bezpieczeństwo sieci | Konfigurowanie [sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) |
|  | Skonfiguruj [przychodzące reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](../control-network-traffic.md) lub [link prywatny](../hdinsight-private-link.md) | Customer |
|  | Konfigurowanie [ograniczenia ruchu wychodzącego](../hdinsight-restrict-outbound-traffic.md) za pomocą zapory | Customer |
|  | Konfigurowanie [szyfrowania protokołu IPSec podczas przesyłania](encryption-in-transit.md) między węzłami klastra | Customer |
| Zwirtualizowana infrastruktura | Nie dotyczy | HDInsight (dostawca usług w chmurze) |
| Zabezpieczenia infrastruktury fizycznej | Nie dotyczy | HDInsight (dostawca usług w chmurze) |

## <a name="next-steps"></a>Następne kroki

* [Planowanie klastrów usługi HDInsight przy użyciu ESP](apache-domain-joined-architecture.md)
* [Konfigurowanie klastrów usługi HDInsight przy użyciu ESP](./apache-domain-joined-configure-using-azure-adds.md)
* [Zarządzanie klastrami usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-manage.md)
