---
title: Opcje zabezpieczeń dla programu Hive w usłudze Azure HDInsight
description: Opcje zabezpieczeń dla programu Hive w klastrach w warstwie Standardowa i ESP.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869722"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Opcje zabezpieczeń dla programu Hive w usłudze Azure HDInsight

W tym dokumencie opisano zalecane opcje zabezpieczeń dla usługi Hive w usłudze HDInsight. Te opcje można skonfigurować za poorednictwem Ambari.

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="&quot;Opcje zabezpieczeń dla programu Hive&quot;" border="true":::

## <a name="hiveserver2-authentication"></a>Uwierzytelnianie serwera hiveserver2

W przypadku klastrów standardowych zalecanym ustawieniem uwierzytelniania serwera hiveserver2 jest wartość none. Aby włączyć uwierzytelnianie, zalecamy uaktualnienie do klastra [ESP](../domain-joined/hdinsight-security-overview.md) (pakiet Enterprise Security). 

W przypadku klastrów ESP uwierzytelnianie [Kerberos](https://web.mit.edu/Kerberos/) jest domyślnie włączone. Moduły uwierzytelniania (PAM) i niestandardowe schematy uwierzytelniania nie są obsługiwane.

## <a name="hiveserver2-authorization"></a>Autoryzacja serwera hiveserver2

W przypadku klastrów standardowych ustawieniem domyślnym jest None. [SqlStdAuth (Autoryzacja oparta na standardach SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) można włączyć. Autoryzacja za poorednictwem platformy [Apache Ranger](https://ranger.apache.org/) nie jest obsługiwana w przypadku klastrów w warstwie Standardowa. Zalecamy uaktualnienie do klastra ESP na potrzeby autoryzacji Ranger. 

W przypadku klastrów ESP autoryzacja za pomocą Ranger jest domyślnie włączona. 


## <a name="ssl-encryption-for-hiveserver2"></a>Szyfrowanie SSL dla serwera hiveserver2

Nie zaleca się włączania protokołu SSL serwera hiveserver2 w przypadku klastrów standardowych lub ESP. Zamiast tego jest włączony protokół SSL. [Szyfrowanie w tranzycie](../domain-joined/encryption-in-transit.md) można włączyć w celu szyfrowania komunikacji między węzłami klastra przy użyciu [zabezpieczeń protokołu internetowego (IPSec)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Następne kroki
* [Omówienie uwierzytelniania serwera hiveserver2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Przegląd autoryzacji serwera hiveserver2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [Włączanie uwierzytelniania Hive opartego na standardach SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger z usługą Hive](../domain-joined/apache-domain-joined-run-hive.md)
