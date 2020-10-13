---
title: Żądanie zwiększenia limitu przydziału procesora CPU w usłudze Azure HDInsight
description: Poznaj proces żądania zwiększenia liczby rdzeni procesora CPU przypisanych do subskrypcji.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 999be38e413288d2eb2f6342874e692972f300ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409718"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Zwiększanie limitu przydziału dla usługi Azure HDInsight

Limity przydziału procesora CPU pomagają zapewnić, że użycie zasobów jest stosunkowo rozproszone między wszystkimi klientami w określonym regionie świadczenia usługi Azure. Jednak w niektórych przypadkach wymagania biznesowe mogą wymagać większej liczby zasobów klastra niż będzie to możliwe. W takich przypadkach można zażądać zwiększenia limitu przydziału procesora CPU w taki sposób, aby można było wdrożyć klastry spełniające wymagania dotyczące przetwarzania danych.

Po osiągnięciu limitu przydziału nie można wdrażać nowych klastrów ani skalować istniejących klastrów przez dodanie kolejnych węzłów procesu roboczego. Jedynym limitem przydziału jest przydział rdzeni procesora CPU, który istnieje na poziomie regionu dla każdej subskrypcji. Na przykład subskrypcja może mieć 30 rdzeni procesora CPU w regionie Wschodnie stany USA z innymi 30 rdzeniami procesora CPU w Wschodnie stany USA.

## <a name="gather-required-information"></a>Zbierz wymagane informacje

Jeśli wystąpił błąd wskazujący, że osiągnięto limit przydziału, Użyj procesu opisanego w tej sekcji, aby zebrać ważne informacje i przesłać żądanie zwiększenia limitu przydziału.

1. Określ żądany rozmiar, skalę i typ maszyny wirtualnej klastra.
1. Sprawdź bieżące limity pojemności przydziału dla subskrypcji. Aby sprawdzić dostępne rdzenie, wykonaj następujące czynności:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
    1. Przejdź do strony **Przegląd** klastra usługi HDInsight.
    1. W menu po lewej stronie wybierz pozycję **limity przydziału**. Na stronie zostanie wyświetlona liczba używanych rdzeni, liczba dostępnych rdzeni i łączna liczba rdzeni.

Aby zażądać zwiększenia limitu przydziału, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **Pomoc i obsługa techniczna** w lewej dolnej części strony.

    ![przycisk Pomoc i obsługa techniczna](./media/quota-increase-request/help-support-button.png)

1. Wybierz pozycję **Nowe żądanie obsługi**.
1. Na stronie **Nowy wniosek o pomoc techniczną** na karcie **Podstawowe** wybierz następujące opcje:

   - **Typ problemu**: **limity usług i subskrypcji (przydziały)**
   - **Subskrypcja**: subskrypcja, którą chcesz zmodyfikować
   - **Typ limitu przydziału**: **HDInsight**

     ![Utwórz żądanie obsługi, aby zwiększyć limit przydziału rdzeni usługi HDInsight](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Wybierz pozycję **Dalej: rozwiązania >>**.
1. Na stronie **szczegóły** wprowadź opis problemu, wybierz ważność problemu, preferowaną metodę kontaktu i inne wymagane pola. Użyj szablonu wymienionego poniżej, aby upewnić się, że podajesz wymagane informacje. Żądania zwiększenia limitu przydziału są oceniane przez zespół pojemności platformy Azure, a nie przez zespół produktu HDInsight. Im więcej informacji podano, tym bardziej prawdopodobnie Twoje żądanie zostanie zatwierdzone.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![Szczegóły problemu](./media/quota-increase-request/problem-details.png)

1. Wybierz kolejno pozycje **Dalej: recenzja + utwórz >>**.
1. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

> [!NOTE]  
> Jeśli potrzebujesz zwiększyć przydział rdzenia usługi HDInsight w regionie prywatnym, [Prześlij żądanie zatwierdzenia listy](https://aka.ms/canaryintwhitelist).

Możesz [skontaktować się z pomocą techniczną, aby zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Istnieją pewne stałe limity przydziału. Na przykład jedna subskrypcja platformy Azure może mieć co najwyżej 10 000 rdzeni. Aby uzyskać szczegółowe informacje dotyczące tych limitów, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastrów w usłudze HDInsight za pomocą Apache Hadoop, Spark, Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md): informacje na temat konfigurowania i konfigurowania klastrów w usłudze HDInsight.
* [Monitorowanie wydajności klastra](hdinsight-key-scenarios-to-monitor.md): informacje na temat kluczowych scenariuszy monitorowania klastrów usługi HDInsight, które mogą wpływać na wydajność klastra.
