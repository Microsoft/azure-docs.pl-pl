---
title: Opcje zabezpieczeń dla programu Hive w usłudze Azure HDInsight
description: Opcje zabezpieczeń dla programu Hive w klastrach w warstwie Standardowa i ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 14a41365640439ff99861bbb22cc04a40f35da5e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222652"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Opcje zabezpieczeń dla programu Hive w usłudze Azure HDInsight

W tym dokumencie opisano zalecane opcje zabezpieczeń dla usługi Hive w usłudze HDInsight. Te opcje można skonfigurować za poorednictwem Ambari.

!["Opcje zabezpieczeń dla programu Hive"](./media/hdinsight-security-options-for-hive/security-options-hive.png "Opcje zabezpieczeń dla programu Hive")

## <a name="hiveserver2-authentication"></a>Uwierzytelnianie serwera hiveserver2

W przypadku klastrów standardowych zalecanym ustawieniem uwierzytelniania serwera hiveserver2 jest wartość none. Aby włączyć uwierzytelnianie, zalecamy uaktualnienie do klastra [ESP](https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview) (pakiet Enterprise Security). 

W przypadku klastrów ESP uwierzytelnianie [Kerberos](https://web.mit.edu/Kerberos/) jest domyślnie włączone. Moduły uwierzytelniania (PAM) i niestandardowe schematy uwierzytelniania nie są obsługiwane.

## <a name="hiveserver2-authorization"></a>Autoryzacja serwera hiveserver2

W przypadku klastrów standardowych ustawieniem domyślnym jest None. [SqlStdAuth (Autoryzacja oparta na standardach SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) można włączyć. Autoryzacja za poorednictwem platformy [Apache Ranger](https://ranger.apache.org/) nie jest obsługiwana w przypadku klastrów w warstwie Standardowa. Zalecamy uaktualnienie do klastra ESP na potrzeby autoryzacji Ranger. 

W przypadku klastrów ESP autoryzacja za pomocą Ranger jest domyślnie włączona. 


## <a name="ssl-encryption-for-hiveserver2"></a>Szyfrowanie SSL dla serwera hiveserver2

Nie zaleca się włączania protokołu SSL serwera hiveserver2 w przypadku klastrów standardowych lub ESP. Zamiast tego jest włączony protokół SSL. [Szyfrowanie w tranzycie](https://docs.microsoft.com/azure/hdinsight/domain-joined/encryption-in-transit) można włączyć w celu szyfrowania komunikacji między węzłami klastra przy użyciu [zabezpieczeń protokołu internetowego (IPSec)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Następne kroki
* [Omówienie uwierzytelniania serwera hiveserver2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Przegląd autoryzacji serwera hiveserver2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [Włączanie uwierzytelniania Hive opartego na standardach SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger z usługą Hive](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive#:~:text=Create%20Hive%20ODBC%20data%20source%20%20%20,Enter%20hiveuser1%40contoso158.onmicrosoft.c%20...%20%205%20more%20rows%20)
