---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady dotyczące rozwoju i szczegóły dotyczące usług Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: cdb31f1a46d7f46b69e9e0ad47a77ba6b32a50a0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770829"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje o **najnowszych aktualizacjach** wersji usługi Azure HDInsight. Aby uzyskać informacje na temat wcześniejszych wydań, zobacz [ARCHIWUM INFORMACJI o wersji HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight jest jedną z najpopularniejszych usług wśród klientów korporacyjnych w zakresie analizy typu open source na platformie Azure.

## <a name="release-date-01092020"></a>Data wydania: 01/09/2020

Ta wersja dotyczy zarówno hdinsight 3.6 i 4.0. Wersja HDInsight jest dostępna dla wszystkich regionów w ciągu kilku dni. Data wydania w tym miejscu wskazuje datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj, aż wydanie będzie dostępne w Twoim regionie za kilka dni.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [artykuł HDInsight versioning](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nowe funkcje
### <a name="tls-12-enforcement"></a>Wymuszanie protokołu TLS 1.2
Transport Layer Security (TLS) i Secure Sockets Layer (SSL) to protokoły kryptograficzne, które zapewniają bezpieczeństwo komunikacji za pośrednictwem sieci komputerowej. Dowiedz się więcej o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HdInsight używa protokołu TLS 1.2 w publicznych punktach końcowych HTTPs, ale TLS 1.1 jest nadal obsługiwany ze zgodnym z powrotem. 

W tej wersji klienci mogą zdecydować się na TLS 1.2 tylko dla wszystkich połączeń za pośrednictwem punktu końcowego klastra publicznego. Aby to poprzeć, nowa właściwość **minSupportedTlsVersion** jest wprowadzana i może być określona podczas tworzenia klastra. Jeśli właściwość nie jest ustawiona, klaster nadal obsługuje TLS 1.0, 1.1 i 1.2, który jest taki sam jak dzisiejsze zachowanie. Klienci mogą ustawić wartość dla tej właściwości na "1.2", co oznacza, że klaster obsługuje tylko protokół TLS 1.2 i powyżej. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia warstwy transportu](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Przynieś własny klucz do szyfrowania dysku
Wszystkie dyski zarządzane w usłudze HDInsight są chronione za pomocą szyfrowania usługi Azure Storage Service (SSE). Dane na tych dyskach są domyślnie szyfrowane przez klucze zarządzane przez firmę Microsoft. Począwszy od tej wersji, można przynieść własny klucz (BYOK) do szyfrowania dysku i zarządzać nim przy użyciu usługi Azure Key Vault. Szyfrowanie BYOK jest konfiguracją jednoetapową podczas tworzenia klastra bez dodatkowych kosztów. Wystarczy zarejestrować usługę HDInsight jako tożsamość zarządzaną w usłudze Azure Key Vault i dodać klucz szyfrowania podczas tworzenia klastra. Aby uzyskać więcej informacji, zobacz [Szyfrowanie dysku klucza zarządzanego przez klienta](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Przestarzałe
Brak deprecations dla tej wersji. Aby przygotować się na nadchodzące przestarzałe zmiany, zobacz [Nadchodzące zmiany](#upcoming-changes).

## <a name="behavior-changes"></a>Zmiany w zachowaniu
Brak zmian w zachowaniu dla tej wersji. Aby przygotować się na nadchodzące zmiany, zobacz [Nadchodzące zmiany](#upcoming-changes).

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany nastąpią w nadchodzących wersjach. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Minimalna 4-rdzeniowa maszyna wirtualna jest wymagana dla węzła głównego 
Minimalna 4-rdzeniowa maszyna wirtualna jest wymagana dla węzła głównego, aby zapewnić wysoką dostępność i niezawodność klastrów HDInsight. Począwszy od 6 kwietnia 2020 r. klienci mogą wybierać tylko 4-rdzeniową lub nowszą maszynę wirtualną jako węzeł główny dla nowych klastrów HDInsight. Istniejące klastry będą nadal działać zgodnie z oczekiwaniami. 

### <a name="esp-spark-cluster-node-size-change"></a>Zmiana rozmiaru węzła klastra ESP Spark 
W nadchodzącej wersji minimalny dozwolony rozmiar węzła dla klastra ESP Spark zostanie zmieniony na Standard_D13_V2. Maszyny wirtualne z serii A mogą powodować problemy z klastrem ESP ze względu na stosunkowo niską pojemność procesora i pamięci. Maszyny wirtualne z serii A zostaną przestarzałe do tworzenia nowych klastrów ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przenoszenie do zestawów skalowania maszyny wirtualnej platformy Azure
Usługa HDInsight używa teraz maszyn wirtualnych platformy Azure do aprowizowania klastra. W nadchodzącej wersji usługa HDInsight użyje zamiast tego zestawów skalowania maszyny wirtualnej platformy Azure. Zobacz więcej o zestawach skalowania maszyny wirtualnej platformy Azure.

### <a name="hbase-20-to-21"></a>Baza HBase od 2,0 do 2,1
W nadchodzącej wersji HDInsight 4.0 wersja HBase zostanie uaktualniona z wersji 2.0 do 2.1.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight w dalszym ciągu poprawia niezawodność i wydajność klastra. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmiany wersji składnika dla tej wersji. Aktualne wersje komponentów dla HDInsight 4.0 ad HDInsight 3.6 można znaleźć tutaj.

## <a name="known-issues"></a>Znane problemy

Od 18 marca 2020 r. niektórzy klienci usługi Azure HDInsight w Europie Zachodniej lub Północnej otrzymywali powiadomienia o błędach podczas tworzenia lub skalowania klastrów USŁUGI HDInsight w tych regionach. Błędy związane z tym problemem obejmują:

- Wystąpił wewnętrzny błąd serwera podczas przetwarzania żądania. Ponów próbę złożenia wniosku lub skontaktuj się z pomocą techniczną.
- Co najmniej jedna operacja wdrażania zasobów nie powiodła się. Aby uzyskać szczegółowe informacje, wyszczełzaj operacje wdrażania. Szczegółowe https://aka.ms/DeployOperations informacje na temat użytkowania można znaleźć w
- Identyfikator subskrypcji\<użytkownika '\>Identyfikator subskrypcji ' nie ma\<rdzeni do utworzenia nazwy klastra>". Wymagane: \<X\>, Dostępne: 0.

Inżynierowie są świadomi tego problemu i aktywnie badają.

Aby uzyskać dodatkową pomoc, utwórz [żądanie pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
