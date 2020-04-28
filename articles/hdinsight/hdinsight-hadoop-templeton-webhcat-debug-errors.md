---
title: Zrozumienie i rozwiązywanie błędów WebHCat w usłudze HDInsight — Azure
description: Dowiedz się, jak informacje o typowych błędach zwracanych przez WebHCat w usłudze HDInsight i sposobach ich rozwiązywania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 011ef4f192bbae12be7d2464d5b0526f584821a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638854"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Understand and resolve errors received from WebHCat on HDInsight (Opis błędów i rozwiązywanie problemów dotyczących interfejsu API WebHCat w usłudze HDInsight)

Dowiedz się więcej o błędach odebranych podczas korzystania z usługi HDInsight w programie WebHCat i sposobach ich rozwiązywania. WebHCat jest używany wewnętrznie przez narzędzia po stronie klienta, takie jak Azure PowerShell i Data Lake Tools for Visual Studio.

## <a name="what-is-webhcat"></a>Co to jest WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) to interfejs API REST dla warstwy zarządzania [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), tabeli i magazynu dla Apache Hadoop. Usługa WebHCat jest domyślnie włączona w klastrach usługi HDInsight i jest używana przez różne narzędzia do przesyłania zadań, uzyskiwania stanu zadania i tak dalej, bez konieczności logowania się do klastra.

## <a name="modifying-configuration"></a>Modyfikowanie konfiguracji

Niektóre błędy wymienione w tym dokumencie występują z powodu przekroczenia skonfigurowanego maksymalnego limitu. Gdy w kroku rozwiązanie wskażesz, że możesz zmienić wartość, Użyj usługi Apache Ambari (interfejs API sieci Web lub REST), aby zmodyfikować tę wartość. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą HDInsight przy użyciu platformy Apache Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>Konfiguracja domyślna

W przypadku przekroczenia następujących wartości domyślnych można obniżyć wydajność WebHCat lub przyczynić się do błędów:

| Ustawienie | Wyniki działania | Wartość domyślna |
| --- | --- | --- |
| [przędzy. Scheduler. pojemność. Maximum — aplikacje][maximum-applications] |Maksymalna liczba zadań, które mogą być aktywne współbieżnie (oczekujące lub uruchomione) |10 000 |
| [Templeton. exec. Max-procs][max-procs] |Maksymalna liczba żądań, które mogą być obsługiwane współbieżnie |20 |
| [MapReduce. jobhistory. max-age-MS][max-age-ms] |Liczba dni zachowywania historii zadań |7 dni |

## <a name="too-many-requests"></a>Zbyt wiele żądań

**Kod stanu HTTP**: 429

| Przyczyna | Rozwiązanie |
| --- | --- |
| Przekroczono maksymalną liczbę równoczesnych żądań obsłużonych przez WebHCat na minutę (domyślnie 20) |Zmniejsz obciążenie, aby upewnić się, że nie jest przesyłanych więcej niż maksymalna liczba równoczesnych żądań lub Zwiększ limit liczby żądań `templeton.exec.max-procs`współbieżnych przez modyfikację. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) |

## <a name="server-unavailable"></a>Serwer niedostępny

**Kod stanu HTTP**: 503

| Przyczyna | Rozwiązanie |
| --- | --- |
| Ten kod stanu zwykle występuje podczas przejścia w tryb failover między podstawowym i pomocniczym węzła głównego klastra |Odczekaj dwie minuty, a następnie spróbuj ponownie wykonać operację. |

## <a name="bad-request-content-could-not-find-job"></a>Zła zawartość żądania: nie można odnaleźć zadania

**Kod stanu HTTP**: 400

| Przyczyna | Rozwiązanie |
| --- | --- |
| Szczegóły zadania zostały oczyszczone przez oczyszczarkę historii zadań |Domyślny okres przechowywania dla historii zadań wynosi 7 dni. Domyślny okres przechowywania można zmienić, modyfikując `mapreduce.jobhistory.max-age-ms`. Aby uzyskać więcej informacji, zobacz [modyfikowanie konfiguracji](#modifying-configuration) |
| Zadanie zostało zatrzymane z powodu przejścia w tryb failover |Przesyłanie zadania ponowienia przez maksymalnie dwie minuty |
| Użyto nieprawidłowego identyfikatora zadania |Sprawdź, czy identyfikator zadania jest poprawny |

## <a name="bad-gateway"></a>Zła brama

**Kod stanu HTTP**: 502

| Przyczyna | Rozwiązanie |
| --- | --- |
| Wewnętrzne wyrzucanie elementów bezużytecznych występuje w procesie WebHCat |Poczekaj na zakończenie odzyskiwania pamięci lub Uruchom ponownie usługę WebHCat |
| Przekroczono limit czasu podczas oczekiwania na odpowiedź z usługi ResourceManager. Ten błąd może wystąpić, gdy liczba aktywnych aplikacji przejdzie do skonfigurowanej wartości maksymalnej (domyślnie 10 000). |Zaczekaj na ukończenie aktualnie uruchomionych zadań lub Zwiększ limit liczby zadań współbieżnych przez modyfikację `yarn.scheduler.capacity.maximum-applications`. Aby uzyskać więcej informacji, zobacz sekcję [modyfikowanie konfiguracji](#modifying-configuration) . |
| Próba pobrania wszystkich zadań za pomocą wywołania [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) , gdy `Fields` jest ustawiona na`*` |Nie pobieraj *wszystkich* szczegółów zadania. Zamiast tego `jobid` należy użyć, aby pobrać szczegóły dotyczące zadań większych niż określony identyfikator zadania. Lub nie używaj`Fields` |
| Usługa WebHCat nie działa podczas pracy w trybie failover węzła głównego |Poczekaj dwie minuty i spróbuj ponownie wykonać operację |
| Liczba oczekujących zadań przesłanych przez WebHCat przekracza 500 |Zaczekaj na ukończenie aktualnie oczekujących zadań przed przesłaniem większej liczby zadań |

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
