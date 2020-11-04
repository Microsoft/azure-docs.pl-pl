---
title: Broker identyfikatorów usługi Azure HDInsight (HIB)
description: Dowiedz się więcej o usłudze Azure HDInsight ID Broker, aby uprościć uwierzytelnianie dla przyłączonych do domeny Apache Hadoop klastrów.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: df4faf367951402914abb03285498e0da6f3105f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337680"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Broker identyfikatorów usługi Azure HDInsight (HIB)

W tym artykule opisano sposób konfigurowania i używania funkcji brokera identyfikatorów usługi Azure HDInsight. Za pomocą tej funkcji można uzyskać nowoczesne uwierzytelnianie OAuth na platformie Apache Ambari przy jednoczesnym wymuszeniu uwierzytelniania wieloskładnikowego bez konieczności używania starszych skrótów haseł w Azure Active Directory Domain Services (AD DS platformy Azure).

## <a name="overview"></a>Omówienie

Broker identyfikatorów usługi HDInsight upraszcza konfiguracje uwierzytelniania złożonego w następujących scenariuszach:

* Organizacja korzysta z Federacji, aby uwierzytelniać użytkowników w celu uzyskiwania dostępu do zasobów w chmurze. Wcześniej aby można było korzystać z klastrów pakiet Enterprise Security HDInsight, należy włączyć synchronizację skrótów haseł z środowiska lokalnego do Azure Active Directory (Azure AD). To wymaganie może być trudne lub niepożądane w niektórych organizacjach.
* Organizacja chce wymusić uwierzytelnianie wieloskładnikowe na potrzeby dostępu opartego na sieci Web lub HTTP do usługi Apache Ambari i innych zasobów klastra.

Broker identyfikatora usługi HDInsight udostępnia infrastrukturę uwierzytelniania, która umożliwia przejście protokołu z uwierzytelniania OAuth (nowoczesnego) do protokołu Kerberos (starsza wersja) bez konieczności synchronizacji skrótów haseł do usługi Azure AD DS. Ta infrastruktura obejmuje składniki działające na maszynie wirtualnej z systemem Windows Server z włączonym węzłem brokera identyfikatorów usługi HDInsight wraz z węzłami bramy klastra.

Skorzystaj z poniższej tabeli, aby określić najlepszą opcję uwierzytelniania w zależności od potrzeb organizacji.

|Opcje uwierzytelniania |Konfiguracja usługi HDInsight | Czynniki, które należy wziąć pod uwagę |
|---|---|---|
| Pełny protokół OAuth | pakiet Enterprise Security + Broker identyfikatorów usługi HDInsight | Opcja najbezpieczniejsza. (Uwierzytelnianie wieloskładnikowe jest obsługiwane). Przekazanie synchronizacji skrótu *nie* jest wymagane. Brak dostępu SSH/narzędzie kinit/plik KEYTAB dla kont lokalnych, które nie mają skrótu hasła na platformie Azure AD DS. Konta tylko w chmurze mogą nadal być SSH/narzędzie kinit/plik KEYTAB. Dostęp oparty na sieci Web do Ambari za pośrednictwem protokołu OAuth. Wymaga aktualizacji starszych aplikacji (na przykład JDBC/ODBC) do obsługi protokołu OAuth.|
| Uwierzytelnianie OAuth + podstawowa | pakiet Enterprise Security + Broker identyfikatorów usługi HDInsight | Dostęp oparty na sieci Web do Ambari za pośrednictwem protokołu OAuth. Starsze aplikacje nadal używają uwierzytelniania podstawowego. Uwierzytelnianie wieloskładnikowe musi być wyłączone dla podstawowego dostępu do uwierzytelniania. Przekazanie synchronizacji skrótu *nie* jest wymagane. Brak dostępu SSH/narzędzie kinit/plik KEYTAB dla kont lokalnych, które nie mają skrótu hasła na platformie Azure AD DS. Konta tylko w chmurze mogą nadal być SSH/narzędzie kinit. |
| Uwierzytelnianie w pełni podstawowe | Pakiet Enterprise Security | Najbardziej podobne do konfiguracji lokalnych. Wymagana jest synchronizacja skrótów haseł do usługi Azure AD DS. Konta lokalne mogą korzystać z protokołu SSH/narzędzie kinit lub plik KEYTAB. Jeśli magazyn zapasowy jest Azure Data Lake Storage Gen2, należy wyłączyć uwierzytelnianie wieloskładnikowe. |

Na poniższym diagramie przedstawiono nowoczesne przepływy uwierzytelniania opartego na protokole OAuth dla wszystkich użytkowników, w tym użytkowników federacyjnych, po włączeniu brokera identyfikatora usługi HDInsight:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagram przedstawiający przepływ uwierzytelniania przy użyciu brokera identyfikatora usługi HDInsight.":::

Na tym diagramie klient (czyli przeglądarka lub aplikacja) musi najpierw uzyskać token uwierzytelniania OAuth. Następnie przedstawia token bramy w żądaniu HTTP. Jeśli zalogowano się już do innych usług platformy Azure, takich jak Azure Portal, możesz zalogować się do klastra usługi HDInsight przy użyciu logowania jednokrotnego.

Nadal może istnieć wiele starszych aplikacji, które obsługują tylko uwierzytelnianie podstawowe (to jest nazwa użytkownika i hasło). W tych scenariuszach nadal można nawiązać połączenie z bramami klastra przy użyciu uwierzytelniania podstawowego protokołu HTTP. W tej konfiguracji należy zapewnić łączność sieciową z węzłów bramy do punktu końcowego Active Directory Federation Services (AD FS), aby zapewnić bezpośrednią linię wglądu z węzłów bramy.

Na poniższym diagramie przedstawiono przepływ uwierzytelniania podstawowego dla użytkowników federacyjnych. Najpierw Brama próbuje ukończyć uwierzytelnianie przy użyciu usługi [ROPC Flow](../../active-directory/develop/v2-oauth-ropc.md). W przypadku braku skrótów haseł synchronizowanych z usługą Azure AD powracanie do odnajdywania punktu końcowego AD FS i kończenia uwierzytelniania przez uzyskanie dostępu do punktu końcowego AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagram przedstawiający architekturę z uwierzytelnianiem podstawowym.":::


## <a name="enable-hdinsight-id-broker"></a>Włącz brokera identyfikatorów usługi HDInsight

Aby utworzyć klaster pakiet Enterprise Security z włączonym brokerem identyfikatora usługi HDInsight:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Postępuj zgodnie z podstawowymi procedurami tworzenia klastra pakiet Enterprise Security. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra usługi HDInsight przy użyciu pakiet Enterprise Security](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Wybierz pozycję **Włącz brokera identyfikatorów HDInsight**.

Funkcja brokera identyfikatorów usługi HDInsight dodaje do klastra jedną dodatkową maszynę wirtualną. Ta maszyna wirtualna jest węzłem brokera identyfikatora usługi HDInsight i obejmuje składniki serwera do obsługi uwierzytelniania. Węzeł brokera identyfikatorów HDInsight jest przyłączony do domeny AD DS platformy Azure.

![Diagram przedstawiający opcję włączenia brokera identyfikatora usługi HDInsight.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager

Jeśli dodasz nową rolę o nazwie `idbrokernode` z następującymi atrybutami do profilu obliczeniowego szablonu, klaster zostanie utworzony z włączonym węzłem brokera identyfikatorów usługi HDInsight:

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
            "targetInstanceCount": 2,
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

Aby wyświetlić pełny przykład szablonu ARM, zobacz szablon opublikowany w [tym miejscu](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).


## <a name="tool-integration"></a>Integracja narzędzi

Narzędzia usługi HDInsight są aktualizowane w celu natywnej obsługi protokołu OAuth. Użyj tych narzędzi dla nowoczesnego dostępu opartego na protokole OAuth do klastrów. Wtyczka usługi HDInsight [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) może być używana w przypadku aplikacji opartych na języku Java, takich jak Scala. [Narzędzia Spark i Hive dla Visual Studio Code](../hdinsight-for-vscode.md) mogą być używane na potrzeby zadań PySpark i Hive. Narzędzia obsługują zadanie wsadowe i zadania interaktywne.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Dostęp SSH bez skrótu hasła na platformie Azure AD DS

|Opcje protokołu SSH |Czynniki, które należy wziąć pod uwagę |
|---|---|
| Konto lokalnego maszyny wirtualnej (na przykład sshuser) | To konto zostało podane podczas tworzenia klastra. Brak uwierzytelniania Kerberos dla tego konta. |
| Konto tylko w chmurze (na przykład alice@contoso.onmicrosoft.com ) | Skrót hasła jest dostępny w usłudze Azure AD DS. Uwierzytelnianie Kerberos jest możliwe za pośrednictwem protokołu SSH Kerberos. |
| Konto lokalne (na przykład alice@contoso.com ) | Uwierzytelnianie za pomocą protokołu SSH Kerberos jest możliwe tylko wtedy, gdy skrót hasła jest dostępny w usłudze Azure AD DS. W przeciwnym razie ten użytkownik nie może nawiązać połączenia SSH z klastrem. |

Aby zapewnić połączenie SSH z maszyną wirtualną przyłączoną do domeny lub uruchomić `kinit` polecenie, musisz podać hasło. Uwierzytelnianie protokołu SSH Kerberos wymaga, aby skrót był dostępny na platformie Azure AD DS. Jeśli chcesz używać protokołu SSH tylko w przypadku scenariuszy administracyjnych, możesz utworzyć jedno konto tylko w chmurze i używać go do protokołu SSH do klastra. Inni użytkownicy lokalni nadal mogą korzystać z narzędzi Ambari lub HDInsight lub uwierzytelniania podstawowego protokołu HTTP bez posiadania skrótu hasła dostępnego w usłudze Azure AD DS.

Jeśli Twoja organizacja nie synchronizuje skrótów haseł do usługi Azure AD DS, najlepszym rozwiązaniem jest utworzenie jednego użytkownika tylko w chmurze w usłudze Azure AD. Następnie przypisz go jako administratora klastra podczas tworzenia klastra i użyj go do celów administracyjnych. Można go użyć, aby uzyskać dostęp do maszyn wirtualnych za pośrednictwem protokołu SSH.

Aby rozwiązać problemy z uwierzytelnianiem, zobacz [ten przewodnik](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Klienci korzystający z protokołu OAuth do nawiązywania połączenia z bramą usługi HDInsight z brokerem identyfikatorów usługi HDInsight

W konfiguracji brokera identyfikatora usługi HDInsight można zaktualizować niestandardowe aplikacje i klientów, którzy łączą się z bramą w celu uzyskania najpierw wymaganego tokenu OAuth. Wykonaj kroki opisane w [tym dokumencie](../../storage/common/storage-auth-aad-app.md) , aby uzyskać token z następującymi informacjami:

*   Identyfikator URI zasobu OAuth: `https://hib.azurehdinsight.net` 
*   Identyfikator aplikacji: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Uprawnienie: (nazwa: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Po uzyskaniu tokenu OAuth Użyj go w nagłówku autoryzacji żądania HTTP do bramy klastra (na przykład https:// <clustername> -int.azurehdinsight.NET). Przykładowa postać polecenia zwinięcie do interfejsu API Apache usługi Livy może wyglądać następująco:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

W przypadku korzystania z Z usługi Beeline i usługi Livy można także wykonać poniższe kody próbek, [Aby skonfigurować](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) klienta do korzystania z protokołu OAuth i łączenia się z klastrem.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastra usługi HDInsight z pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizowanie użytkowników usługi Azure Active Directory z klastrem usługi HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorowanie wydajności klastra](../hdinsight-key-scenarios-to-monitor.md)
