---
title: Interfejs API REST do wysyłania zapytań do usługi Apache HBase w usłudze Azure HDInsight
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 361eefa9b0dce811533bb4da9c236c207e31c61c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547881"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>Interfejs API REST do wysyłania zapytań do usługi Apache HBase w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Korzystanie z interfejsu REST usługi Apache HBase w celu wykonywania zapytań względem tabeli w przestrzeni nazw innej niż domyślna powoduje błąd w czasie wykonywania (stan HTTP 500).

## <a name="cause"></a>Przyczyna

Interfejs API REST HBase jest obsługiwany tylko w przypadku korzystania z domyślnej przestrzeni nazw. Jest to znany problem związany z używaniem przestrzeni nazw HBase lub wykonywaniem wywołań odwołujących się do określonych kolumn get w kolumnach z rodzinami kolumn z serwerem REST w usłudze HDInsight. Jest to spowodowane problemem z zabezpieczeniami w usłudze HDInsight Gateway. Korzystając z interfejsu API do tworzenia tabeli z przestrzenią nazw, uzyskując dostęp do kolumn za pośrednictwem rodzin kolumn, należy określić `:` znak, który jest uznawany za problem z zabezpieczeniami w module bramy usług IIS.

## <a name="mitigation"></a>Ograniczanie ryzyka

Pomiń bramę/serwer REST, wdrażając aplikację na maszynie wirtualnej znajdującej się w tej samej sieci wirtualnej platformy Azure co klaster usługi HDInsight. Następnie można komunikować się z usługą HBase bezpośrednio za pośrednictwem usługi RPC (całkowicie pomijając serwer REST) lub do poszczególnych serwerów REST działających w węzłach procesu roboczego pomijających bramy usługi HDInsight.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).