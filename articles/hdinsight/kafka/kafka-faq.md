---
title: Często zadawane pytania dotyczące Apache Kafka w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące Apache Kafka w usłudze Azure HDInsight — zarządzanej usługi w chmurze.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939101"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Często zadawane pytania dotyczące platformy Apache Kafka w ramach usługi Azure HDInsight

W tym artykule opisano niektóre często zadawane pytania dotyczące korzystania z Apache Kafka w usłudze Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Jakie wersje Kafka są obsługiwane przez usługi HDInsight?

Więcej informacji na temat wersji oficjalnie obsługiwanych składników usługi HDInsight znajduje się w temacie [co to są składniki Apache Hadoop i wersje dostępne w usłudze HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Zaleca się, aby zawsze używać najnowszej wersji, aby zapewnić najlepszą możliwą wydajność i środowisko użytkownika.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Jakie zasoby są udostępniane w klastrze usługi HDInsight Kafka i jakie zasoby są rozliczane?

Klaster usługi HDInsight Kafka obejmuje następujące zasoby:

* Węzły główne
* Dozorcy węzły
* Węzły brokera (proces roboczy) 
* Managed Disks platformy Azure dołączone do węzłów brokera
* Węzły bramy

Wszystkie te zasoby są rozliczone w oparciu o nasz [model cenowy usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), z wyjątkiem węzłów bramy. Nie są naliczone opłaty za węzły bramy.

Aby uzyskać bardziej szczegółowy opis różnych typów węzłów, zobacz [Architektura sieci wirtualnej usługi Azure HDInsight](../hdinsight-virtual-network-architecture.md). Ceny są ustalane na podstawie użycia węzłów na minutę. Ceny różnią się w zależności od rozmiaru węzła, liczby węzłów, używanego dysku zarządzanego oraz regionu.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Czy Apache Kafka interfejsy API działają z usługą HDInsight?

Tak, Usługa HDInsight używa natywnych interfejsów API Kafka. Nie trzeba zmieniać kodu aplikacji klienckiej. Zobacz [Samouczek: korzystanie z interfejsów API producenta i klienta Apache Kafka](./apache-kafka-producer-consumer-api.md) , aby zobaczyć, jak można używać interfejsów API producentów/klientów opartych na języku Java w klastrze.

## <a name="can-i-change-cluster-configurations"></a>Czy mogę zmienić konfiguracje klastrów?

Tak, za pomocą portalu Ambari. Każdy składnik w portalu zawiera sekcję **configs** , która może służyć do zmiany konfiguracji składników. Niektóre zmiany mogą wymagać ponownego uruchomienia brokera.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Jakiego typu uwierzytelniania obsługuje Usługa HDInsight dla Apache Kafka?

Za pomocą [pakiet Enterprise Security (ESP)](../domain-joined/apache-domain-joined-architecture.md)można uzyskać zabezpieczenia na poziomie tematu dla swoich klastrów Kafka. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie zasad Apache Kafka w usłudze HDInsight z pakiet Enterprise Security (wersja zapoznawcza)](../domain-joined/apache-domain-joined-run-kafka.md).

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Czy moje dane są szyfrowane? Czy mogę użyć własnych kluczy?

Wszystkie komunikaty Kafka na dyskach zarządzanych są szyfrowane za pomocą [usługi Azure szyfrowanie usługi Storage (SSE)](../../storage/common/storage-service-encryption.md). Dane przesyłane w trakcie przesyłania (na przykład dane wysyłane z klientów do brokerów i inne sposoby) nie są domyślnie szyfrowane. Istnieje możliwość zaszyfrowania takiego ruchu przez [skonfigurowanie własnego protokołu TLS](./apache-kafka-ssl-encryption-authentication.md). Ponadto Usługa HDInsight umożliwia zarządzanie własnymi kluczami w celu szyfrowania danych przechowywanych w stanie spoczynku. Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków klucza zarządzanego przez klienta](../disk-encryption.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Jak mogę połączyć klientów z moim klastrem?

Aby klienci Kafka mogli komunikować się z brokerami usługi Kafka, muszą być w stanie dotrzeć do brokerów za pośrednictwem sieci. W przypadku klastrów usługi HDInsight Virtual Network (VNet) jest granicą zabezpieczeń. W związku z tym Najprostszym sposobem łączenia klientów z klastrem usługi HDInsight jest utworzenie klientów w tej samej sieci wirtualnej co klaster. Inne scenariusze obejmują:

* Łączenie klientów w innej sieci wirtualnej platformy Azure — równorzędna Sieć wirtualna klastra i Sieć wirtualna klienta oraz konfiguracja klastra na potrzeby [anonsowania adresów IP](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). W przypadku korzystania z reklamy IP klienci Kafka muszą używać adresów IP brokera do łączenia się z brokerami, zamiast w pełni kwalifikowanych nazw domen (FQDN).

* Łączenie klientów lokalnych — przy użyciu sieci VPN i Konfigurowanie niestandardowych serwerów DNS zgodnie z opisem w temacie [Planowanie sieci wirtualnej dla usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Tworzenie publicznego punktu końcowego dla usługi Kafka — Jeśli jej wymagania dotyczące zabezpieczeń przedsiębiorstwa, można wdrożyć publiczny punkt końcowy dla brokerów usługi Kafka lub samozarządzanego punktu końcowego "open source" z publicznym punktem końcowym.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Czy mogę dodać więcej miejsca na dysku w istniejącym klastrze?

Aby zwiększyć ilość miejsca dostępnego dla komunikatów Kafka, można zwiększyć liczbę węzłów. Obecnie Dodawanie większej liczby dysków do istniejącego klastra nie jest obsługiwane.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Czy klaster Kafka współpracuje z datakostki? 

Tak, klastry Kafka mogą współpracować z kostkami, tak długo, jak znajdują się w tej samej sieci wirtualnej. Aby użyć klastra Kafka z kostkami danych, należy utworzyć sieć wirtualną z klastrem usługi HDInsight Kafka w tym obszarze, a następnie określić tę sieć wirtualną podczas tworzenia obszaru roboczego datakostki i użyć iniekcji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Databricks w Virtual Network platformy Azure (iniekcja sieci wirtualnej)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Podczas tworzenia obszaru roboczego datakostki należy podać nazwy brokera Bootstrap klastra Kafka. Aby uzyskać informacje na temat pobierania nazw brokera Kafka, zobacz [pobieranie informacji o hoście Apache dozorcy i brokera](./apache-kafka-get-started.md#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Jak mogę mieć maksymalną trwałość danych?

Trwałość danych umożliwia osiągnięcie najmniejszego ryzyka utraty komunikatów. Aby osiągnąć maksymalną trwałość danych, zalecamy użycie następujących ustawień:

* Użycie minimalnego współczynnika replikacji wynoszącego 3 w większości regionów
* Użyj minimalnego współczynnika replikacji wynoszącego 4 w regionach z tylko dwiema domenami błędów
* Wyłącz wybory lidera nieoczyszczonego
* Ustaw wartość **min. unsync. Replicas** na 2 lub więcej — spowoduje to zmianę liczby replik, które muszą być całkowicie zsynchronizowane z liderem, zanim będzie można kontynuować zapisywanie
* Ustaw właściwość **ACK** na wartość **All** — ta właściwość wymaga, aby wszystkie repliki potwierdziły wszystkie komunikaty

Skonfigurowanie Kafka na potrzeby wyższego poziomu spójności danych ma wpływ na dostępność brokerów do tworzenia żądań.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Czy można replikować dane do wielu klastrów?

Tak, dane mogą być replikowane do wielu klastrów przy użyciu Kafka narzędzia MirrorMaker. Szczegółowe informacje na temat konfigurowania narzędzia MirrorMaker można znaleźć w [tematach Apache Kafka duplikatów](apache-kafka-mirroring.md). Ponadto istnieją inne samozarządzane technologie typu open source i dostawcy, które mogą pomóc w osiągnięciu replikacji do wielu klastrów, takich jak [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Czy mogę uaktualnić klaster? Jak należy uaktualnić klaster?

Obecnie nie obsługujemy uaktualnień wersji klastra miejscowego. Aby zaktualizować klaster do nowszej wersji Kafka, Utwórz nowy klaster z żądaną wersją i Przeprowadź migrację klientów Kafka do korzystania z nowego klastra.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Jak mogę monitorować klaster Kafka?

Użyj usługi Azure monitor, aby przeanalizować [dzienniki usługi Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie szyfrowania i uwierzytelniania TLS dla Apache Kafka w usłudze Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Replikowanie tematów platformy Apache Kafka przy użyciu platformy Kafka w usłudze HDInsight za pomocą narzędzia MirrorMaker](./apache-kafka-mirroring.md)