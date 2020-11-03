---
title: Błąd Jupyter 404 — "Blokowanie interfejsu API między źródłami" — Azure HDInsight
description: Błąd Jupyter serwera 404 "nie znaleziono" z powodu "blokowania interfejsu API między źródłami" w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f2000c3938e7366dd85f36b8de9a8425e91fab8a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287941"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenariusz: błąd Jupyter serwera 404 "nie znaleziono" z powodu "blokowania interfejsu API między źródłami" w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Gdy uzyskujesz dostęp do usługi Jupyter w usłudze HDInsight, zobaczysz komunikat o błędzie mówiący "nie znaleziono". W przypadku zaznaczenia dzienników Jupyter zostanie wyświetlony następujący element:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Adres IP może być również widoczny w polu "Źródło" w dzienniku Jupyter.

## <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez kilka rzeczy:

- Jeśli skonfigurowano reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu do klastra. Ograniczanie dostępu za pomocą reguł sieciowej grupy zabezpieczeń nadal umożliwia bezpośredni dostęp do platformy Apache Ambari i innych usług przy użyciu adresu IP, a nie nazwy klastra. Jednak podczas uzyskiwania dostępu do Jupyter może zostać wyświetlony komunikat o błędzie "nie znaleziono" 404.

- Jeśli masz przyznanym bramie HDInsight niestandardową nazwę DNS inną niż standardowa `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Rozwiązanie

1. Zmodyfikuj pliki jupyter.py w tych dwóch miejscach:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Znajdź wiersz, który brzmi: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` i zmień go na: `NotebookApp.allow_origin='\"*\"'` .

1. Uruchom ponownie usługę Jupyter z Ambari.

1. Wpisywanie `ps aux | grep jupyter` w wierszu polecenia powinno wskazywać, że umożliwia nawiązanie połączenia z adresem URL.

Jest to mniej bezpieczne niż ustawienie, które już się zakończyło. Jednak zakłada się, że dostęp do klastra jest ograniczony i że jeden z zewnątrz może nawiązać połączenie z klastrem, ponieważ sieciowej grupy zabezpieczeń się na miejscu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]