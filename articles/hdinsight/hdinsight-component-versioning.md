---
title: Apache Hadoop składniki i wersje — Azure HDInsight
description: Dowiedz się więcej o składnikach i wersjach Apache Hadoop w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726587"
---
# <a name="azure-hdinsight-versions"></a>Wersje usługi Azure HDInsight

Usługa HDInsight umożliwia łączenie składników środowiska Apache Hadoop i platformy usługi HDInsight z pakietem wdrożonym w klastrze. Aby uzyskać więcej informacji, zobacz [jak działa przechowywanie wersji usługi HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Obsługiwane wersje usługi HDInsight

W tej tabeli wymieniono wersje usługi HDInsight, które są dostępne w Azure Portal i innych metod wdrażania, takich jak program PowerShell, interfejs wiersza polecenia i zestaw SDK platformy .NET.

| Wersja usługi HDInsight | System operacyjny maszyny wirtualnej | Data wydania| Typ pomocy technicznej | Data wygaśnięcia pomocy technicznej | Data wycofania | Wysoka dostępność |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |Wrzesień 24, 2018 | [Standardowa](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Tak |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 kwietnia 2017      | [Podstawowa](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Wygaśnięcie pomocy technicznej Standard — 30 czerwca 2021 <br> Wygaśnięcie pomoc techniczna Basic — 3 kwietnia 2022 |4 kwietnia 2022 |Tak |

* Od 1 lipca 2021 firma Microsoft będzie oferować pomoc techniczna Basic dla niektórych typów klastrów HDI 3,6. Zobacz [wersje składników usługi HDInsight 3,6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Informacje o wersji

Aby uzyskać dodatkowe informacje o wersji dotyczące najnowszych wersji usługi HDInsight, zobacz [Informacje o wersji usługi HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Opcje pomocy technicznej dla wersji usługi HDInsight

Pomoc techniczna jest definiowana jako czas, przez który wersja usługi HDInsight jest obsługiwana przez dział obsługi klienta firmy Microsoft. Usługa HDInsight oferuje dwa typy obsługi: 
- **Pomoc techniczna Standard** to okres, w którym firma Microsoft udostępnia aktualizacje i pomoc techniczną dla klastrów usługi HDInsight.  
    Zalecamy Kompilowanie rozwiązań przy użyciu najnowszej w pełni obsługiwanej wersji. 
- **Pomoc techniczna Basic** to okres, w którym firma Microsoft zapewni ograniczoną obsługę dostawcy zasobów usługi HDInsight. Obrazy HDInsight i składniki oprogramowania typu Open Source (OSS) nie będą obsługiwane.   W klastrach usługi HDInsight zostaną poprawione tylko krytyczne poprawki zabezpieczeń.  
  Firma Microsoft nie zachęca do tworzenia nowych klastrów ani tworzenia jakichkolwiek świeżych rozwiązań, gdy wersja jest w pomoc techniczna Basic. Zalecamy Migrowanie istniejących klastrów do najnowszej w pełni obsługiwanej wersji. 

**Wygaśnięcie pomocy technicznej** oznacza, że firma Microsoft nie zapewnia już pomocy technicznej dotyczącej określonej wersji usługi HDInsight. Program może nie być już dostępny w Azure Portal na potrzeby tworzenia klastra.

**Wycofanie** oznacza, że istniejące klastry wersji usługi HDInsight nadal działają zgodnie z oczekiwaniami. Nowych klastrów tej wersji nie można utworzyć za pomocą żadnych metod, które obejmują interfejs wiersza polecenia i zestawy SDK. Inne funkcje płaszczyzny kontroli, takie jak skalowanie ręczne i skalowanie automatyczne, nie są gwarantowane przed upływem daty wycofania. Pomoc techniczna nie jest dostępna dla wycofanych wersji.

## <a name="versioning-considerations"></a>Zagadnienia dotyczące wersji
- Po wdrożeniu klastra przy użyciu obrazu ten klaster nie jest automatycznie uaktualniany do nowszej wersji obrazu. Podczas tworzenia nowych klastrów zostanie wdrożona Najnowsza wersja obrazu.
- Klienci powinni testować i sprawdzać, czy aplikacje działają prawidłowo w przypadku korzystania z nowej wersji usługi HDInsight.
- Usługa HDInsight rezerwuje prawo do zmiany wersji domyślnej bez wcześniejszego powiadomienia. Jeśli masz zależność wersji, określ wersję usługi HDInsight podczas tworzenia klastrów.
- Usługa HDInsight może wycofać wersję składnika OSS przed wycofaniem wersji usługi HDInsight.

## <a name="next-steps"></a>Następne kroki

- [Konfiguracja klastra dla Apache Hadoop, Spark i nie tylko w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pakiet Enterprise Security](./enterprise-security-package.md)
- [Pracuj w Apache Hadoop w usłudze HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)
