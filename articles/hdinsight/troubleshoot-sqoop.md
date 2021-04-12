---
title: Sqoop polecenia Import/Export nie powiedzie się w przypadku niektórych użytkowników w klastrach ESP — Azure HDInsight
description: 'Polecenie importu/eksportu Apache Sqoop nie powiodło się z powodu "import nie powiódł się: Java. IO. IOException: własność w katalogu przemieszczania/User/yourUserName/.Staging nie jest zgodna z oczekiwaniami" dla niektórych użytkowników w klastrze usługi Azure HDInsight ESP'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387358"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Scenariusz: polecenie Sqoop Import/Export kończy się niepowodzeniem w przypadku nazw użytkowników o długości większej niż 20 znaków w klastrach ESP usługi HDInsight Azure

W tym artykule opisano znany problem i obejście tego problemu podczas korzystania z klastrów usługi Azure HDInsight ESP (Enterprise Security Pack) przy użyciu konta magazynu ADLS Gen2 (ABFS).

## <a name="issue"></a>Problem

Po uruchomieniu polecenia Sqoop Import/Export nie powiedzie się z powodu błędu poniżej dla niektórych użytkowników:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

W powyższym przykładzie `/user/yourlongdomainuserna/.staging` wyświetla skróconą 20-znakową nazwę użytkownika dla nazwy użytkownika `yourlongdomainusername` .

## <a name="cause"></a>Przyczyna

Długość nazwy użytkownika przekracza 20 znaków. 

Więcej informacji można znaleźć [w temacie jak obiekty i poświadczenia są synchronizowane w Azure Active Directory Domain Servicesej domenie zarządzanej](../active-directory-domain-services/synchronization.md) .

## <a name="workaround"></a>Obejście

Użyj nazwy użytkownika mniejszej lub równej 20 znaków.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
