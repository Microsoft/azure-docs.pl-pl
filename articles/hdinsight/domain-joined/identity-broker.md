---
title: Korzystanie z usługi ID brokera (wersja zapoznawcza) na potrzeby zarządzania poświadczeniami — Azure HDInsight
description: Dowiedz się więcej o usłudze HDInsight ID Broker, aby uprościć uwierzytelnianie dla przyłączonych do domeny Apache Hadoop klastrów.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 24f15b8a4d5a5afd3a2794fe686d3acb0036cdd8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565330"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Broker identyfikatorów usługi Azure HDInsight (wersja zapoznawcza)

W tym artykule opisano sposób konfigurowania i używania funkcji brokera identyfikatorów HDInsight (HIB) w usłudze Azure HDInsight. Za pomocą tej funkcji można uzyskać nowoczesne uwierzytelnianie OAuth na platformie Apache Ambari podczas wymuszania Multi-Factor Authentication (MFA) bez konieczności używania starszych skrótów haseł w usłudze Azure Active Directory Domain Services (AAD-DS).

## <a name="overview"></a>Omówienie

HIB upraszcza skomplikowane konfiguracje uwierzytelniania w następujących scenariuszach:

* Organizacja korzysta z Federacji, aby uwierzytelniać użytkowników w celu uzyskiwania dostępu do zasobów w chmurze. Wcześniej aby można było korzystać z klastrów usługi HDInsight pakiet Enterprise Security (ESP), należy włączyć synchronizację skrótów haseł z środowiska lokalnego do Azure Active Directory (Azure AD). To wymaganie może być trudne lub niepożądane w niektórych organizacjach.

* Organizacja chce wymusić uwierzytelnianie wieloskładnikowe dla dostępu za pośrednictwem sieci Web/HTTP do platformy Apache Ambari i innych zasobów klastra.

HIB zapewnia infrastrukturę uwierzytelniania, która umożliwia przejście protokołu z uwierzytelniania OAuth (nowoczesnego) do protokołu Kerberos (starsza wersja) bez konieczności synchronizowania skrótów haseł z usługą AAD-DS. Ta infrastruktura obejmuje składniki działające na maszynie wirtualnej z systemem Windows Server (węzłem brokera identyfikatorów) wraz z węzłami bramy klastra.

Skorzystaj z poniższej tabeli, aby określić najlepszą opcję uwierzytelniania w zależności od potrzeb organizacji:

|Opcje uwierzytelniania |Konfiguracja usługi HDInsight | Czynniki, które należy wziąć pod uwagę |
|---|---|---|
| Pełny protokół OAuth | ESP + HIB | 1. najbezpieczniejsza opcja (MFA jest obsługiwana) 2.    Przekazanie synchronizacji skrótu nie jest wymagane. 3.  Brak dostępu SSH/narzędzie kinit/plik KEYTAB dla kont on-Premium, które nie mają skrótu hasła w usłudze AAD-DS. 4.   Konta w chmurze mogą nadal być SSH/narzędzie kinit/plik KEYTAB. 5. Dostęp oparty na sieci Web do Ambari za pośrednictwem protokołu OAuth 6.  Wymaga aktualizacji starszych aplikacji (JDBC/ODBC itp.) w celu obsługi uwierzytelniania OAuth.|
| Uwierzytelnianie OAuth + podstawowa | ESP + HIB | 1. dostęp oparty na sieci Web do Ambari za pośrednictwem protokołu OAuth 2. Starsze aplikacje nadal używają uwierzytelniania podstawowego. 3. Uwierzytelnianie wieloskładnikowe musi być wyłączone dla podstawowego dostępu do uwierzytelniania. 4. Przekazanie synchronizacji skrótu nie jest wymagane. 5. Brak dostępu SSH/narzędzie kinit/plik KEYTAB dla kont on-Premium, które nie mają skrótu hasła w usłudze AAD-DS. 6. Konta w chmurze mogą nadal być SSH/narzędzie kinit. |
| Uwierzytelnianie w pełni podstawowe | ESP | 1. najbardziej podobne do konfiguracji Premium. 2. Wymagana jest synchronizacja skrótów haseł w usłudze AAD — DS. 3. Konta Premium mogą korzystać z protokołu SSH/narzędzie kinit lub plik KEYTAB. 4. Jeśli magazyn zapasowy jest ADLS Gen2, należy wyłączyć usługę MFA |

Na poniższym diagramie przedstawiono nowoczesne przepływy uwierzytelniania opartego na OAuth dla wszystkich użytkowników, w tym użytkowników federacyjnych, po włączeniu brokera identyfikatorów:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Przepływ uwierzytelniania z brokerem identyfikatorów":::

Na tym diagramie klient (tj. przeglądarka lub aplikacje) musi najpierw uzyskać token OAuth, a następnie przedstawić token do bramy w żądaniu HTTP. Jeśli zalogowano się już do innych usług platformy Azure, takich jak Azure Portal, możesz zalogować się do klastra usługi HDInsight przy użyciu logowania jednokrotnego (SSO).

Nadal może istnieć wiele starszych aplikacji, które obsługują tylko uwierzytelnianie podstawowe (tj. nazwa użytkownika/hasło). W tych scenariuszach nadal można nawiązać połączenie z bramami klastra przy użyciu uwierzytelniania podstawowego protokołu HTTP. W tej konfiguracji należy zapewnić łączność sieciową z węzłów bramy do punktu końcowego Federacji (AD FS Endpoint), aby zapewnić bezpośrednią linię wglądu z węzłów bramy. 

Na poniższym diagramie przedstawiono przepływ uwierzytelniania podstawowego dla użytkowników federacyjnych. Najpierw Brama próbuje ukończyć uwierzytelnianie przy użyciu usługi [ROPC Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) i w przypadku braku skrótów haseł synchronizowanych z usługą Azure AD, a następnie powróci do odnajdywania punktu końcowego AD FS i dokończyć uwierzytelnianie, uzyskując dostęp do punktu końcowego AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Przepływ uwierzytelniania z brokerem identyfikatorów":::


## <a name="enable-hdinsight-id-broker"></a>Włącz brokera identyfikatorów usługi HDInsight

Aby utworzyć klaster ESP z włączonym brokerem identyfikatorów, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Postępuj zgodnie z podstawowymi procedurami tworzenia klastra ESP. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Wybierz pozycję **Włącz brokera identyfikatorów HDInsight**.

Funkcja brokera identyfikatorów doda do klastra jedną dodatkową maszynę wirtualną. Ta maszyna wirtualna jest węzłem brokera identyfikatorów i zawiera składniki serwera do obsługi uwierzytelniania. Węzeł brokera identyfikatorów jest przyłączony do domeny AD DS platformy Azure.

![Opcja włączenia brokera identyfikatorów](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager
Jeśli dodasz nową rolę o nazwie `idbrokernode` z poniższymi atrybutami do profilu obliczeniowego szablonu, klaster zostanie utworzony z włączonym węzłem identyfikatora brokera:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Integracja narzędzi

Narzędzia HDIsngith zostały zaktualizowane w celu natywnego obsługi protokołu OAuth. Zdecydowanie zalecamy używanie tych narzędzi dla nowoczesnego dostępu opartego na protokole OAuth do klastrów. Wtyczka usługi HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) może być używana dla aplikacji opartych na języku Java, takich jak Scala. [Narzędzia programu Hive & platformy Spark dla vs Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) mogą służyć do PySpark i zadań Hive. Obsługują zarówno zadania wsadowe, jak i interaktywne.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Dostęp SSH bez skrótu hasła na platformie Azure AD DS

|Opcje protokołu SSH |Czynniki, które należy wziąć pod uwagę |
|---|---|
| Konto lokalnego maszyny wirtualnej (np. sshuser) | 1. to konto zostało podane podczas tworzenia klastra. 2.  Brak authication Kerberos dla tego konta |
| Konto tylko w chmurze (np. alice@contoso.onmicrosoft.com ) | 1. skrót hasła jest dostępny w usłudze AAD — DS. 2. Uwierzytelnianie Kerberos jest możliwe za pośrednictwem protokołu SSH Kerberos |
| Konto Premium (np. alice@contoso.com ) | 1. uwierzytelnianie Kerberos protokołu SSH jest możliwe tylko wtedy, gdy skrót hasła jest dostępny w usłudze AAD — w przeciwnym razie ten użytkownik nie może przeprowadzić połączenia SSH z klastrem |

Aby zapewnić połączenie SSH z przyłączoną do domeny maszyną wirtualną lub uruchomić `kinit` polecenie, należy podać hasło. Uwierzytelnianie protokołu SSH Kerberos wymaga, aby skrót był dostępny w usłudze AAD-DS. Jeśli chcesz używać protokołu SSH tylko w przypadku scenariuszy administracyjnych, możesz utworzyć jedno konto tylko w chmurze i używać go do protokołu SSH do klastra. Inni użytkownicy korzystający z usługi Premium mogą nadal używać narzędzi Ambari lub HDInsight lub uwierzytelniania podstawowego protokołu HTTP, bez potrzeby korzystania z skrótu hasła w usłudze AAD-DS.

Jeśli w organizacji nie są synchronizowane skróty haseł do usługi AAD-DS, najlepszym rozwiązaniem jest utworzenie tylko jednego użytkownika tylko w chmurze w usłudze Azure AD i przypisanie go jako administratora klastra podczas tworzenia klastra i użycie go do celów administracyjnych obejmujących dostęp do maszyn wirtualnych za pośrednictwem protokołu SSH.

Aby rozwiązać problemy z uwierzytelnianiem, zobacz ten [Przewodnik](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues).

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>Klienci korzystający z protokołu OAuth do nawiązywania połączenia z bramą usługi HDInsight z usługą HIB

W instalatorze HIB można zaktualizować niestandardowe aplikacje i klientów łączących się z bramą w celu uzyskania najpierw wymaganego tokenu OAuth. Możesz wykonać kroki opisane w tym [dokumencie](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) , aby uzyskać token z następującymi informacjami:

*   Identyfikator URI zasobu OAuth: `https://hib.azurehdinsight.net` 
*   Identyfikator aplikacji: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Uprawnienie: (nazwa: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Po uzyskaniu tokenu OAuth można użyć go w nagłówku autoryzacji żądania HTTP do bramy klastra (np. https:// <clustername> -int.azurehdinsight.NET). Przykładowo polecenie zwinięcie przykładu do interfejsu API Apache usługi Livy może wyglądać następująco:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastra usługi HDInsight z pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorowanie wydajności klastra](../hdinsight-key-scenarios-to-monitor.md)
