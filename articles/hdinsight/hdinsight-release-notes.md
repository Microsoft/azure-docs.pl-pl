---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e76c75e172e48866565cc292ac673f7bbddabf1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232823"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-01092020"></a>Data wydania: 01/09/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w ciągu kilku dni.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz artykuł dotyczący obsługi [wersji usługi HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nowe funkcje
### <a name="tls-12-enforcement"></a>Wymuszanie protokołu TLS 1.2
Transport Layer Security (TLS) i SSL (SSL) to protokoły kryptograficzne zapewniające bezpieczeństwo komunikacji w sieci komputerowej. Dowiedz się więcej o protokole [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Usługa HDInsight używa protokołu TLS 1,2 na publicznych punktach końcowych HTTPs, ale protokół TLS 1,1 jest nadal obsługiwany dla zgodności z poprzednimi wersjami 

W tej wersji klienci mogą zrezygnować z protokołu TLS 1,2 tylko dla wszystkich połączeń za pomocą publicznego punktu końcowego klastra. Aby to umożliwić, wprowadzono nową właściwość **minSupportedTlsVersion** i można ją określić podczas tworzenia klastra. Jeśli właściwość nie jest ustawiona, klaster nadal obsługuje protokoły TLS 1,0, 1,1 i 1,2, które są takie same jak bieżące zachowanie. Klienci mogą ustawić wartość dla tej właściwości na "1,2", co oznacza, że klaster obsługuje tylko protokół TLS 1,2 lub nowszy. Aby uzyskać więcej informacji, zobacz [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Przenoszenie własnego klucza do szyfrowania dysków
Wszystkie dyski zarządzane w usłudze HDInsight są chronione za pomocą usługi Azure szyfrowanie usługi Storage (SSE). Dane na tych dyskach są domyślnie szyfrowane przez klucze zarządzane przez firmę Microsoft. Począwszy od tej wersji, można Bring Your Own Key (BYOK) na potrzeby szyfrowania dysków i zarządzać nimi przy użyciu Azure Key Vault. Szyfrowanie BYOK jest konfiguracją jednoetapową podczas tworzenia klastra bez dodatkowych kosztów. Zarejestruj się w usłudze HDInsight jako tożsamość zarządzana z Azure Key Vault i Dodaj klucz szyfrowania podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Przestarzałe
Brak zaniecheń dla tej wersji. Aby przygotować się do nadchodzących przestarzałych, zobacz [nadchodzące zmiany](#upcoming-changes).

## <a name="behavior-changes"></a>Zmiany zachowania
Brak zmian w zachowaniu dla tej wersji. Aby przygotować się do nadchodzących zmian, zobacz [nadchodzące zmiany](#upcoming-changes).

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach. 

### <a name="deprecate-spark-21-and-22-for-hdinsight-36-spark-cluster"></a>Przestarzałe platformy Spark 2,1 i 2,2 dla klastra usługi HDInsight 3,6 Spark
Począwszy od lipca 1 2020, klienci nie będą mogli tworzyć nowych klastrów Spark przy użyciu platformy Spark 2,1 i 2,2 w usłudze HDInsight 3,6. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście do platformy Spark 2,3 na HDInight 3,6 do czerwca 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.

### <a name="deprecate-spark-23-for-hdinsight-40-spark-cluster"></a>Przestarzałe rozwiązanie Spark 2,3 dla klastra usługi HDInsight 4,0 Spark
Począwszy od lipca 1 2020, klienci nie będą mogli tworzyć nowych klastrów Spark z platformą Spark 2,3 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście do platformy Spark 2,4 na HDInight 4,0 do czerwca 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.

### <a name="deprecate-kafka-11-for-hdinsight-40-kafka-cluster"></a>Przestarzałe Kafka 1,1 dla klastra usługi HDInsight 4,0 Kafka
Począwszy od lipca 1 2020, klienci nie będą mogli tworzyć nowych klastrów Kafka z Kafka 1,1 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przechodzenie do Kafka 2,1 w HDInight 4,0 do czerwca 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.

### <a name="hbase-20-to-21"></a>HBase 2,0 do 2,1
W nadchodzącym wydaniu usługi HDInsight 4,0 wersja HBase zostanie uaktualniona z wersji 2,0 do 2,1.

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Minimalna 4-rdzeniowa maszyna wirtualna jest wymagana dla węzła głównego. 
Aby węzeł główny zapewniał wysoką dostępność i niezawodność klastrów usługi HDInsight, wymagana jest co najmniej 4 rdzenie maszyny wirtualnej. Począwszy od kwietnia 6 2020, klienci mogą wybrać tylko 4-rdzeniową lub wyższą maszynę wirtualną jako węzeł główny dla nowych klastrów usługi HDInsight. Istniejące klastry będą nadal działać zgodnie z oczekiwaniami. 

### <a name="esp-spark-cluster-node-size-change"></a>Zmiana rozmiaru węzła klastra Spark w ramach platformy ESP 
W nadchodzącym wydaniu Minimalny dozwolony rozmiar węzła dla klastra ESP Spark zostanie zmieniony na Standard_D13_V2. Maszyny wirtualne z serii A mogą powodować problemy z klastrem ESP ze względu na stosunkowo niską pojemność procesora i pamięci. Maszyny wirtualne z serii A są przestarzałe do tworzenia nowych klastrów ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. W nadchodzącym wydaniu Usługa HDInsight będzie używać zestawów skalowania maszyn wirtualnych platformy Azure. Zobacz więcej informacji o zestawach skalowania maszyn wirtualnych platformy Azure.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 AD HDInsight 3,6 można znaleźć tutaj.

