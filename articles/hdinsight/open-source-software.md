---
title: Obsługa oprogramowania typu open source w usłudze Azure HDInsight
description: Platforma Microsoft Azure zapewnia ogólny poziom obsługi technologii typu open source.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: f93be73af4bbbd159ffc01804617892251d96347
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772173"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Obsługa oprogramowania typu open source w usłudze Azure HDInsight

Usługa Microsoft Azure HDInsight korzysta ze środowiska technologii open source utworzonych wokół apache Hadoop. Platforma Microsoft Azure zapewnia ogólny poziom obsługi technologii typu open source. Aby uzyskać więcej informacji, zobacz sekcję **Zakres pomocy technicznej** w często [zadawanych pytaniach dotyczących pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom obsługi wbudowanych składników.

## <a name="components"></a>Składniki

W usłudze HDInsight dostępne są dwa typy składników typu open source:

### <a name="built-in-components"></a>Wbudowane komponenty

Te składniki są preinstalowane w klastrach HDInsight i zapewniają podstawowe funkcje klastra. Do tej kategorii należą następujące składniki:

* [Apache Hadoop Przędza](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Menedżer zasobów.
* Język zapytania [hiveQl](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

Pełna lista składników klastra jest dostępna w [co są apache Hadoop składników i wersji dostępnych z HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Składniki niestandardowe

Jako użytkownik klastra można zainstalować lub użyć w obciążeniu dowolnego składnika dostępnego w społeczności lub utworzonego przez użytkownika.

> [!WARNING]  
> Komponenty dostarczane z klastrem HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, aby pomóc w dalszym rozwiązywaniu problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać ten problem. Mogą też poprosić cię o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się głęboka wiedza specjalistyczna w zakresie tej technologii. Można korzystać z wielu witryn społecznościowych. Przykładami są [forum MSDN dla HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) i [Stack Overflow](https://stackoverflow.com).
>
> Projekty Apache mają również witryny projektu na [stronie internetowej Apache](https://apache.org). Przykładem jest [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Użycie komponentu

Usługa HDInsight oferuje kilka sposobów używania składników niestandardowych. Ten sam poziom pomocy technicznej ma zastosowanie, niezależnie od tego, jak składnik jest używany lub zainstalowany w klastrze. W poniższej tabeli opisano najczęstsze sposoby używania składników niestandardowych w klastrach usługi HDInsight:

|Sposób użycia |Opis |
|---|---|
|Składanie ofert pracy|Hadoop lub inne typy zadań, które wykonują lub używają składników niestandardowych mogą być przesyłane do klastra.|
|Dostosowywanie klastra|Podczas tworzenia klastra można określić dodatkowe ustawienia i składniki niestandardowe, które są instalowane w węzłach klastra.|
|Samples|W przypadku popularnych składników niestandardowych firma Microsoft i inne firmy mogą dostarczyć przykłady sposobu, w jaki te składniki mogą być używane w klastrach HDInsight. Te próbki są dostarczane bez pomocy technicznej.|

## <a name="next-steps"></a>Następne kroki

* [Dostosowywanie klastrów usługi Azure HDInsight przy użyciu akcji skryptów](./hdinsight-hadoop-customize-cluster-linux.md)
* [Tworzenie skryptów akcji skryptów dla hdinsight](hdinsight-hadoop-script-actions-linux.md)
* [Bezpieczne zarządzanie środowiskiem Python w usłudze Azure HDInsight za pomocą akcji skryptu](./spark/apache-spark-python-package-installation.md)
