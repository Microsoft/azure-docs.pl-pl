---
title: Obsługa oprogramowania Open Source w usłudze Azure HDInsight
description: Microsoft Azure zapewnia ogólny poziom wsparcia dla technologii typu open source.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fec4cff974031982c782c9265a7d3186d6bb0233
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942551"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Obsługa oprogramowania Open Source w usłudze Azure HDInsight

Usługa Microsoft Azure HDInsight używa środowiska dla technologii typu "open source" utworzonych wokół Apache Hadoop. Microsoft Azure zapewnia ogólny poziom wsparcia dla technologii typu open source. Aby uzyskać więcej informacji, zapoznaj się z sekcją dotyczącej **zakresu pomocy** [technicznej platformy Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom wsparcia dla wbudowanych składników programu.

## <a name="components"></a>Składniki

W usłudze HDInsight są dostępne dwa typy składników typu "open source":

### <a name="built-in-components"></a>Składniki wbudowane

Te składniki są wstępnie instalowane w klastrach usługi HDInsight i zapewniają podstawowe funkcje klastra. Następujące składniki należą do tej kategorii:

* [Przędza Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Menedżer zasobów.
* [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)języka zapytań programu Hive.
* [Apache Mahout](https://mahout.apache.org/).

Pełna lista składników klastra jest dostępna w temacie [co to są składniki Apache Hadoop i wersje dostępne w usłudze HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Składniki niestandardowe

Jako użytkownik klastra można zainstalować lub użyć w obciążeniu dowolny składnik dostępny w społeczności lub utworzony przez użytkownika.

> [!WARNING]  
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, aby ułatwić dalsze Rozwiązywanie problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać ten problem. Mogą też zadawać pytania o zaangażowanie dostępnych kanałów dla technologii open source, w przypadku których zostanie znaleziona Szczegółowa wiedza dla tej technologii. Można użyć wielu witryn społeczności. Przykłady to [Microsoft Q&stronie pytania dotyczącej usługi HDInsight](/answers/topics/azure-hdinsight.html) i [Stack Overflow](https://stackoverflow.com).
>
> Projekty Apache zawierają również witryny projektu w [witrynie Apache](https://apache.org). Przykładem jest usługa [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Użycie składników

Usługa HDInsight oferuje kilka sposobów korzystania ze składników niestandardowych. Ten sam poziom wsparcia ma zastosowanie, niezależnie od tego, w jaki sposób składnik jest używany lub instalowany w klastrze. W poniższej tabeli opisano najczęstsze sposoby używania składników niestandardowych w klastrach usługi HDInsight:

|Użycie |Opis |
|---|---|
|Przesyłanie zadania|Usługa Hadoop lub inne typy zadań, które wykonują lub używają niestandardowych składników można przesłać do klastra.|
|Dostosowywanie klastra|Podczas tworzenia klastra można określić dodatkowe ustawienia i składniki niestandardowe, które są zainstalowane w węzłach klastra.|
|Samples|W przypadku popularnych składników niestandardowych firma Microsoft i inne mogą przedstawić Przykłady sposobu używania tych składników w klastrach usługi HDInsight. Te przykłady są udostępniane bez pomocy technicznej.|

## <a name="next-steps"></a>Następne kroki

* [Dostosowywanie klastrów usługi Azure HDInsight za pomocą akcji skryptu](./hdinsight-hadoop-customize-cluster-linux.md)
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Bezpieczne zarządzanie środowiskiem Python w usłudze Azure HDInsight za pomocą akcji skryptu](./spark/apache-spark-python-package-installation.md)