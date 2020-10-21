---
title: Szczegółowy dostęp oparty na rolach konfiguracje klastrów usługi Azure HDInsight
description: Dowiedz się więcej o zmianach wymaganych w ramach migracji do szczegółowego dostępu opartego na rolach dla konfiguracji klastra usługi HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: e67d705f8e878cff6934c2e8a172148fab3f1d71
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329002"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrowanie do szczegółowego dostępu opartego na rolach w przypadku konfiguracji klastrów

Wprowadzamy pewne ważne zmiany, aby obsługiwać bardziej szczegółowy dostęp oparty na rolach w celu uzyskania poufnych informacji. W ramach tych zmian niektóre działania mogą być wymagane **do 3 września 2019,** Jeśli używasz jednego z tych [jednostek/scenariuszy, których to dotyczy](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Co się zmieni?

Wcześniej wpisy tajne mogą być uzyskiwane za pośrednictwem interfejsu API usługi HDInsight przez użytkowników klastrów, którzy posiadają [role](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)właściciela, współautora lub czytelnika, ponieważ były dostępne dla wszystkich osób z `*/read` uprawnieniami. Wpisy tajne są definiowane jako wartości, które mogą być używane do uzyskania bardziej podwyższonego poziomu dostępu, niż powinna zezwalać rola użytkownika. Obejmują one wartości, takie jak poświadczenia HTTP bramy klastra, klucze konta magazynu i poświadczenia bazy danych.

Od 3 września 2019 dostęp do tych kluczy tajnych będzie wymagał `Microsoft.HDInsight/clusters/configurations/action` uprawnień, co oznacza, że użytkownicy z rolą czytelnik nie będą mieli dostępu do nich. Role, które mają to uprawnienie, to współautor, właściciel i Nowa rola operatora klastra usługi HDInsight (więcej informacji znajduje się poniżej).

Wprowadzamy również nową rolę [operatora klastra usługi HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) , która będzie mogła pobierać wpisy tajne bez przyznawania uprawnień administracyjnych współautora lub właściciela. Podsumowując:

| Rola                                  | Poprzedniej                                                                                       | Przechodzenie do przodu       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Czytelnik                                | — Dostęp do odczytu, w tym wpisy tajne.                                                                   | — Dostęp do odczytu, **z wyłączeniem** wpisów tajnych |           |   |   |
| Operator klastra usługi HDInsight<br>(Nowa rola) | Nie dotyczy                                                                                              | — Dostęp do odczytu/zapisu, w tym wpisy tajne         |   |   |
| Współautor                           | — Dostęp do odczytu/zapisu, w tym wpisy tajne.<br>— Tworzenie wszystkich typów zasobów platformy Azure i zarządzanie nimi.<br>— Wykonaj akcje skryptu.     | Bez zmian |
| Właściciel                                 | — Dostęp do odczytu/zapisu, w tym wpisy tajne.<br>-Pełny dostęp do wszystkich zasobów<br>-Deleguj dostęp do innych osób.<br>— Wykonaj akcje skryptu. | Bez zmian |

Aby uzyskać informacje na temat dodawania przypisania roli operatora klastra usługi HDInsight do użytkownika w celu udzielenia im dostępu do odczytu/zapisu do wpisów tajnych klastra, zapoznaj się z sekcją poniżej i [Dodaj przypisanie roli operatora klastra usługi HDInsight do użytkownika](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Czy te zmiany zostały naruszone?

Dotyczy to następujących jednostek i scenariuszy:

- [Interfejs API](#api): Użytkownicy korzystający z `/configurations` `/configurations/{configurationName}` punktów końcowych lub.
- [Narzędzia usługi Azure HDInsight dla Visual Studio Code w](#azure-hdinsight-tools-for-visual-studio-code) wersji 1.1.1 lub starszej.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) w wersji 3.20.0 lub niższej.
- [Azure Data Lake i Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) poniżej wersji 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) w wersji 3.15.0 lub niższej.
- [Zestaw SDK dla środowiska .NET](#sdk-for-net)
    - [wersje 1. x lub 2. x](#versions-1x-and-2x): Użytkownicy korzystający `GetClusterConfigurations` z `GetConnectivitySettings` metod,, `ConfigureHttpSettings` , `EnableHttp` lub `DisableHttp` z klasy ConfigurationsOperationsExtensions.
    - [wersje 3. x i w górę](#versions-3x-and-up): Użytkownicy korzystający z `Get` `Update` metod,, `EnableHttp` , lub `DisableHttp` z `ConfigurationsOperationsExtensions` klasy.
- [Zestaw SDK dla języka Python](#sdk-for-python): Użytkownicy korzystający z `get` `update` metod lub z `ConfigurationsOperations` klasy.
- [Zestaw SDK dla języka Java](#sdk-for-java): Użytkownicy korzystający `update` `get` z metod lub z `ConfigurationsInner` klasy.
- [Zestaw SDK dla języka go](#sdk-for-go): Użytkownicy korzystający z `Get` `Update` metod lub z `ConfigurationsClient` struktury.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) poniżej wersji 2.0.0.
Zapoznaj się z poniższymi sekcjami (lub Skorzystaj z powyższych linków), aby zapoznać się z krokami migracji dla danego scenariusza.

### <a name="api"></a>Interfejs API

Następujące interfejsy API zostaną zmienione lub przestarzałe:

- [**Pobierz/Configurations/{ConfigurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (usunięto poufne informacje)
    - Poprzednio używane do uzyskiwania poszczególnych typów konfiguracji (w tym kluczy tajnych).
    - Od 3 września 2019 to wywołanie interfejsu API zwróci teraz indywidualne typy konfiguracji z kluczami tajnymi, które zostały pominięte. Aby uzyskać wszystkie konfiguracje, w tym klucze tajne, Użyj nowego wywołania POST/Configurations. Aby uzyskać tylko ustawienia bramy, Użyj nowego wywołania POST/getGatewaySettings.
- [**Pobierz/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (przestarzałe)
    - Poprzednio używane do uzyskiwania wszystkich konfiguracji (w tym wpisów tajnych)
    - Od 3 września 2019 to wywołanie interfejsu API będzie przestarzałe i nie będzie już obsługiwane. Aby uzyskać wszystkie konfiguracje w przód, Użyj nowego wywołania POST/Configurations. Aby uzyskać konfigurację z pominiętymi parametrami, użyj wywołania GET/configurations/{configurationName}.
- [**/Configurations/{ConfigurationName} post**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (przestarzałe)
    - Poprzednio używane do aktualizacji poświadczeń bramy.
    - Od 3 września 2019 to wywołanie interfejsu API zostanie zaniechane i nie będzie już obsługiwane. Zamiast tego użyj nowego wpisu/updateGatewaySettings.

Dodano następujące zastępcze interfejsy API:</span>

- [**Opublikuj/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Użyj tego interfejsu API, aby uzyskać wszystkie konfiguracje, w tym wpisy tajne.
- [**Opublikuj/getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Użyj tego interfejsu API, aby uzyskać ustawienia bramy.
- [**Opublikuj/updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Użyj tego interfejsu API, aby zaktualizować ustawienia bramy (nazwa użytkownika i/lub hasło).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Narzędzia usługi Azure HDInsight dla Visual Studio Code

W przypadku używania wersji 1.1.1 lub starszej należy zaktualizować do [najnowszej wersji narzędzi usługi Azure HDInsight dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) , aby uniknąć przerw w działaniu.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Jeśli używasz wersji 3.20.0 lub starszej, zaktualizuj do [najnowszej wersji wtyczki Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) , aby uniknąć przerw w działaniu.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Narzędzia Azure Data Lake i Stream Analytics Tools for Visual Studio

Zaktualizuj do wersji 2.3.9000.1 lub nowszej [Azure Data Lake i Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) , aby uniknąć przerw w działaniu.  Aby uzyskać pomoc dotyczącą aktualizowania, zapoznaj się z naszą dokumentacją, [Update Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Jeśli używasz wersji 3.15.0 lub starszej, zaktualizuj do [najnowszej wersji Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) , aby uniknąć przerw w działaniu.

### <a name="sdk-for-net"></a>Zestaw SDK środowiska .NET

#### <a name="versions-1x-and-2x"></a>Wersje 1. x i 2. x

Aktualizacja do [wersji 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) zestawu HDInsight SDK dla platformy .NET. Jeśli używasz metody, której dotyczą te zmiany, mogą być wymagane minimalne modyfikacje kodu:

- `ClusterOperationsExtensions.GetClusterConfigurations`**nie będą już zwracać poufnych parametrów** , takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia http (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj `ClusterOperationsExtensions.ListConfigurations` przechodzenia do przodu.  Należy pamiętać, że użytkownicy z rolą "czytelnik" nie będą mogli korzystać z tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji dotyczących klastra.
    - Aby pobrać tylko poświadczenia bramy HTTP, użyj `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` jest teraz przestarzały i został zastąpiony przez `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` jest teraz przestarzały i został zastąpiony przez `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` i `DisableHttp` są obecnie przestarzałe. Protokół HTTP jest teraz zawsze włączony, dlatego te metody nie są już potrzebne.

#### <a name="versions-3x-and-up"></a>Wersje 3. x i w górę

Aktualizacja do [wersji 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) lub nowszej zestawu HDInsight SDK dla platformy .NET. Jeśli używasz metody, której dotyczą te zmiany, mogą być wymagane minimalne modyfikacje kodu:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**nie będą już zwracać poufnych parametrów** , takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia http (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) przechodzenia do przodu.Należy pamiętać, że użytkownicy z rolą "czytelnik" nie będą mogli korzystać z tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji dotyczących klastra. 
    - Aby pobrać tylko poświadczenia bramy HTTP, użyj [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet) . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) jest teraz przestarzały i został zastąpiony przez [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) i [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) są obecnie przestarzałe. Protokół HTTP jest teraz zawsze włączony, dlatego te metody nie są już potrzebne.

### <a name="sdk-for-python"></a>Zestaw SDK dla środowiska Python

Aktualizacja do [wersji 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) lub nowszej zestawu HDInsight SDK dla języka Python. Jeśli używasz metody, której dotyczą te zmiany, mogą być wymagane minimalne modyfikacje kodu:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**nie będą już zwracać poufnych parametrów** , takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia http (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) przechodzenia do przodu.Należy pamiętać, że użytkownicy z rolą "czytelnik" nie będą mogli korzystać z tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji dotyczących klastra. 
    - Aby pobrać tylko poświadczenia bramy HTTP, użyj [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) jest teraz przestarzały i został zastąpiony przez [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>Zestaw SDK dla języka Java

Aktualizacja do [wersji 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) lub nowszej zestawu HDInsight SDK dla języka Java. Jeśli używasz metody, której dotyczą te zmiany, mogą być wymagane minimalne modyfikacje kodu:

- `ConfigurationsInner.get`**nie będą już zwracać poufnych parametrów** , takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia http (brama).
- `ConfigurationsInner.update` jest obecnie przestarzałe.

### <a name="sdk-for-go"></a>Zestaw SDK dla języka go

Aktualizacja do [wersji 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) lub nowszej zestawu HDInsight SDK dla języka go. Jeśli używasz metody, której dotyczą te zmiany, mogą być wymagane minimalne modyfikacje kodu:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**nie będą już zwracać poufnych parametrów** , takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia http (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) przechodzenia do przodu.Należy pamiętać, że użytkownicy z rolą "czytelnik" nie będą mogli korzystać z tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji dotyczących klastra. 
    - Aby pobrać tylko poświadczenia bramy HTTP, użyj [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) jest teraz przestarzały i został zastąpiony przez [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>AZ. HDInsight PowerShell
Aby uniknąć przerw, zaktualizuj program [PowerShell w wersji 2.0.0](https://www.powershellgallery.com/packages/Az) lub nowszej.  Jeśli używasz metody, której dotyczą te zmiany, mogą być wymagane minimalne modyfikacje kodu.
- `Grant-AzHDInsightHttpServicesAccess` jest teraz przestarzały i został zastąpiony przez nowe `Set-AzHDInsightGatewayCredential` polecenie cmdlet.
- `Get-AzHDInsightJobOutput` została zaktualizowana w celu obsługi szczegółowego dostępu opartego na rolach do klucza magazynu.
    - Nie ma to wpływu na użytkowników mających rolę Operator, Współautor lub Właściciel w klastrze usługi HDInsight.
    - Tylko użytkownicy z rolą czytelnika będą musieli `DefaultStorageAccountKey` jawnie określić parametr.
- `Revoke-AzHDInsightHttpServicesAccess` jest obecnie przestarzałe. Protokół HTTP jest teraz zawsze włączony, więc to polecenie cmdlet nie jest już potrzebne.
 Zobacz [AZ. Przewodnik migracji usługi HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) , aby uzyskać więcej informacji.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Dodawanie przypisania roli operatora klastra usługi HDInsight do użytkownika

Użytkownik z rolą [właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) może przypisać rolę [operatora klastra usługi HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) do użytkowników, którzy chcą mieć dostęp do odczytu i zapisu do poufnych wartości konfiguracji klastra usługi HDInsight (takich jak poświadczenia bramy klastra i klucze kont magazynu).

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Najprostszym sposobem, aby dodać to przypisanie roli, jest użycie `az role assignment create` polecenia w usłudze Azure CLI.

> [!NOTE]
> To polecenie musi być uruchamiane przez użytkownika z rolą właściciela, ponieważ tylko mogą udzielić tych uprawnień. `--assignee`To nazwa nazwy głównej usługi lub adresu e-mail użytkownika, do którego chcesz przypisać rolę operatora klastra HDInsight. Jeśli wystąpi błąd niewystarczających uprawnień, zobacz często zadawane pytania poniżej.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Udziel roli na poziomie zasobów (klastra)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Udziel roli na poziomie grupy zasobów

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Udzielanie roli na poziomie subskrypcji

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Możesz również użyć Azure Portal, aby dodać przypisanie roli operatora klastra usługi HDInsight do użytkownika. Zobacz dokumentację, [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal-Dodaj przypisanie roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>Często zadawane pytania

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Dlaczego widzę odpowiedź 403 (Zabronione) po zaktualizowaniu żądań interfejsu API i/lub narzędzia?

Konfiguracje klastra są teraz za szczegółowej kontroli dostępu opartej na rolach i wymagają `Microsoft.HDInsight/clusters/configurations/*` uprawnienia dostępu do nich. Aby uzyskać to uprawnienie, przypisz rolę operatora klastra usługi HDInsight, współautora lub właściciela do nazwy głównej użytkownika lub usług, próbując uzyskać dostęp do konfiguracji.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Dlaczego widzę "niewystarczające uprawnienia do ukończenia tej operacji" podczas uruchamiania polecenia platformy Azure w celu przypisania roli operatora klastra usługi HDInsight do innego użytkownika lub jego nazwy głównej?

Oprócz roli właściciela użytkownik lub podmiot usługi wykonujący polecenie musi mieć wystarczające uprawnienia usługi Azure AD do wyszukiwania identyfikatorów obiektów osoby przydzielonej. Ten komunikat oznacza niewystarczające uprawnienia usługi Azure AD. Spróbuj zamienić `-–assignee` argument z `–assignee-object-id` i podaj identyfikator obiektu osoby przydzielonej jako parametr zamiast nazwy (lub identyfikatora podmiotu zabezpieczeń w przypadku tożsamości zarządzanej). Aby uzyskać więcej informacji, zobacz sekcję Parametry opcjonalne w sekcji [AZ role przypisanie Create Documentation](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) .

Jeśli to jeszcze nie zadziała, skontaktuj się z administratorem usługi Azure AD, aby uzyskać odpowiednie uprawnienia.

### <a name="what-will-happen-if-i-take-no-action"></a>Co się stanie, jeśli nie będę podejmować żadnych działań?

Od 3 września 2019 `GET /configurations` i `POST /configurations/gateway` wywołania nie będą już zwracały żadnych informacji i `GET /configurations/{configurationName}` wywołanie nie zwróci już parametrów poufnych, takich jak klucze konta magazynu czy hasło klastra. Ta sama wartość dotyczy odpowiednich metod zestawu SDK i poleceń cmdlet programu PowerShell.

Jeśli używasz starszej wersji jednego z narzędzi dla programu Visual Studio, programu vscode, IntelliJ lub zaćmienie wymienione powyżej, nie będą one już działać do momentu aktualizacji.

Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednią sekcją tego dokumentu w danym scenariuszu.
