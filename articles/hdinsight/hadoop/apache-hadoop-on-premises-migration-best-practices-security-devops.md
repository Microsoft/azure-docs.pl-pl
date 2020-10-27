---
title: 'Zabezpieczenia: Migrowanie Apache Hadoop lokalnych do usługi Azure HDInsight'
description: Poznaj zabezpieczenia i DevOps najlepsze rozwiązania dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 6fd59a346a36a304f699894c493d8ba9800489b2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545263"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — Security i DevOps Best Practices

W tym artykule przedstawiono zalecenia dotyczące zabezpieczeń i DevOps w systemach usługi Azure HDInsight. Jest ona częścią serii, która oferuje najlepsze rozwiązania ułatwiające Migrowanie lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Zabezpieczanie klastra i zarządzanie nim za pomocą pakiet Enterprise Security

Pakiet Enterprise Security (ESP) obsługuje uwierzytelnianie oparte na Active Directory, obsługę wielodostępności oraz kontrolę dostępu opartą na rolach. Po wybraniu opcji ESP klaster usługi HDInsight jest przyłączony do domeny Active Directory, a administrator przedsiębiorstwa może skonfigurować kontrolę dostępu opartą na rolach (RBAC) dla Apache Hive zabezpieczeń przy użyciu platformy Apache Ranger. Administrator może również przeprowadzać inspekcję dostępu do danych przez pracowników i wszelkie zmiany wprowadzone w zasadach kontroli dostępu.

Protokół ESP jest dostępny w następujących typach klastrów: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka i Interactive Query (Hive LLAP).

Wykonaj następujące kroki, aby wdrożyć przyłączony do domeny klaster usługi HDInsight:

- Wdróż Azure Active Directory (AAD) przez przekazanie nazwy domeny.
- Wdróż Azure Active Directory Domain Services (AAD DS).
- Utwórz wymagane Virtual Network i podsieć.
- Wdróż maszynę wirtualną w Virtual Network, aby zarządzać usługą AAD DS.
- Dołącz maszynę wirtualną do domeny.
- Zainstaluj narzędzia usług AD i DNS.
- Należy utworzyć jednostkę organizacyjną (OU) przez administratora usługi AAD.
- Włącz LDAPs dla usługi AAD DS.
- Utwórz konto usługi w Azure Active Directory z delegowanym uprawnieniem odczyt & zapis do jednostki organizacyjnej, aby to umożliwić. To konto usługi może następnie przyłączyć maszyny do domeny i umieścić jednostki maszyn głównych w jednostce organizacyjnej. Może również utworzyć jednostki usługi w jednostce organizacyjnej, która została określona podczas tworzenia klastra.

    > [!Note]
    > Konto usługi nie musi być kontem administratora domeny usługi AD.

- Wdróż klaster usługi HDInsight ESP przez ustawienie następujących parametrów:

    |Parametr |Opis |
    |---|---|
    |Nazwa domeny|Nazwa domeny skojarzona z usługą Azure AD DS.|
    |Nazwa użytkownika domeny|Konto usługi w domenie zarządzanej przez kontroler domeny platformy Azure AD DS, która została utworzona w poprzedniej sekcji, na przykład: `hdiadmin@contoso.onmicrosoft.com` . Ten użytkownik domeny będzie administratorem tego klastra usługi HDInsight.|
    |Hasło domeny|Hasło konta usługi.|
    |Jednostka organizacyjna|Nazwa wyróżniająca jednostki organizacyjnej, która ma być używana z klastrem usługi HDInsight, na przykład: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com` . Jeśli ta jednostka organizacyjna nie istnieje, klaster usługi HDInsight podejmie próbę utworzenia jednostki organizacyjnej przy użyciu uprawnień konta usługi.|
    |ADRES URL ADRESÓW LDAP|na przykład `ldaps://contoso.onmicrosoft.com:636` .|
    |Dostęp do grupy użytkowników|Grupy zabezpieczeń, których użytkownicy mają synchronizować z klastrem, na przykład: `HiveUsers` . Jeśli chcesz określić wiele grup użytkowników, rozdziel je średnikami ";". Grupy muszą istnieć w katalogu przed utworzeniem klastra ESP.|

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Wprowadzenie do Apache Hadoop zabezpieczeń z przyłączonymi do domeny klastrami usługi HDInsight](../domain-joined/hdinsight-security-overview.md)
- [Planowanie klastrów Apache Hadoop przyłączonych do domeny platformy Azure w usłudze HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurowanie przyłączonego do domeny klastra usługi HDInsight przy użyciu Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurowanie zasad Apache Hive w usłudze HDInsight przyłączonych do domeny](../domain-joined/apache-domain-joined-run-hive.md)
- [Uruchamianie platformy Apache Oozie w przyłączonych do domeny klastrach usługi HDInsight Hadoop](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Zaimplementuj kompleksowe zabezpieczenia przedsiębiorstwa

Kompleksowe zabezpieczenia przedsiębiorstwa można osiągnąć przy użyciu następujących kontrolek:

**Potok danych prywatnych i chronionych (zabezpieczenia na poziomie obwodu)**
    - Zabezpieczenia na poziomie obwodu można osiągnąć za pomocą sieci wirtualnych platformy Azure, sieciowych grup zabezpieczeń i usługi bramy.

**Uwierzytelnianie i autoryzacja dostępu do danych**
    - Utwórz przyłączony do domeny klaster usługi HDInsight przy użyciu Azure Active Directory Domain Services. (Pakiet Enterprise Security).
    - Użyj Ambari, aby zapewnić dostęp oparty na rolach do zasobów klastra dla użytkowników usługi AD.
    - Użyj platformy Apache Ranger do ustawiania zasad kontroli dostępu dla programu Hive na poziomie tabeli/kolumny/wiersza.
    - Dostęp SSH do klastra może być ograniczony tylko do administratora.

**Inspekcja**
    - Wyświetl i Zgłoś cały dostęp do zasobów i danych klastra usługi HDInsight.
    - Wyświetlaj i Raportuj wszystkie zmiany zasad kontroli dostępu.

**Szyfrowanie**
    - Przezroczyste szyfrowanie Server-Side przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta.
    - W przypadku szyfrowania tranzytowego przy użyciu Client-Side szyfrowania, protokołu HTTPS i protokołu TLS.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Omówienie usługi Azure Virtual Networks](../../virtual-network/virtual-networks-overview.md)
- [Omówienie grup zabezpieczeń sieci platformy Azure](../../virtual-network/network-security-groups-overview.md)
- [Komunikacja równorzędna sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md)
- [Azure Storage security guide](../../storage/blobs/security-recommendations.md) (Przewodnik po zabezpieczeniach usługi Azure Storage)
- [Usługa Azure szyfrowanie usługi Storage w stanie spoczynku](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Korzystanie z alertów & monitorowania

Aby uzyskać więcej informacji, zobacz artykuł:

[Przegląd Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Uaktualnianie klastrów

Regularnie uaktualniaj do najnowszej wersji usługi HDInsight, aby korzystać z najnowszych funkcji. Poniższe kroki mogą służyć do uaktualnienia klastra do najnowszej wersji:

1. Utwórz nowy TEST klastra usługi HDInsight przy użyciu najnowszej dostępnej wersji usługi HDInsight.
1. Przetestuj w nowym klastrze, aby upewnić się, że zadania i obciążenia działają zgodnie z oczekiwaniami.
1. Modyfikuj zadania lub aplikacje lub obciążenia zgodnie z potrzebami.
1. Wykonaj kopię zapasową wszystkich danych przejściowych przechowywanych lokalnie w węzłach klastra.
1. Usuń istniejący klaster.
1. Utwórz klaster najnowszej wersji usługi HDInsight w tej samej podsieci sieci wirtualnej, używając tych samych danych domyślnych i magazynu meta jako poprzedniego klastra.
1. Zaimportuj wszystkie dane przejściowe, których kopię zapasową utworzono.
1. Uruchom zadania/Kontynuuj przetwarzanie przy użyciu nowego klastra.

Aby uzyskać więcej informacji, zobacz artykuł: [Uaktualnianie klastra usługi HDInsight do nowej wersji](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Poprawianie systemów operacyjnych klastra

Aby uzyskać więcej informacji, zobacz artykuł: [poprawka systemu operacyjnego dla usługi HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Po migracji

1. **Koryguj aplikacje** — iteracyjnie wprowadź niezbędne zmiany w zadaniach, procesach i skryptach.
2. **Wykonaj testy** — iteracyjnie uruchamiaj testy funkcjonalne i wydajnościowe.
3. **Zoptymalizuj** wszystkie problemy z wydajnością w oparciu o powyższe wyniki testów, a następnie przetestuj je w celu potwierdzenia poprawy wydajności.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat usługi [HDInsight 4,0](./apache-hadoop-introduction.md).