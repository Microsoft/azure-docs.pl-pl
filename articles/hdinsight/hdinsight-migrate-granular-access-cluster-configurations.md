---
title: Szczegółowy dostęp oparty na rolach Azure HDInsight konfiguracji klastra
description: Dowiedz się więcej o zmianach wymaganych w ramach migracji do szczegółowego dostępu opartego na rolach dla konfiguracji klastra usługi HDInsight.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: afb30f4648f1649bf6cc6cc6a3bf02f433f49d45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774935"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrowanie do szczegółowego dostępu opartego na rolach w przypadku konfiguracji klastrów

Wprowadzamy pewne ważne zmiany w celu obsługi bardziej szczegółowego dostępu opartego na rolach w celu uzyskania poufnych informacji. W ramach tych zmian niektóre akcje mogą być wymagane do 3 września **2019 r.,** jeśli używasz jednej z jednostek/scenariuszy, których dotyczy [problem.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Co się zmienia?

Wcześniej wpisy tajne można było uzyskać za pośrednictwem interfejsu API usługi HDInsight przez użytkowników klastra posiadających role właściciela, współautora lub czytelnika platformy [Azure,](../role-based-access-control/rbac-and-directory-admin-roles.md)ponieważ były one dostępne dla wszystkich użytkowników z `*/read` uprawnieniami. Wpisy tajne są definiowane jako wartości, które mogą służyć do uzyskania większej liczby podniesionych uprawnień dostępu niż rola użytkownika powinna zezwalać. Należą do nich takie wartości jak poświadczenia HTTP bramy klastra, klucze konta magazynu i poświadczenia bazy danych.

Począwszy od 3 września 2019 r., uzyskiwanie dostępu do tych wpisów tajnych będzie wymagało uprawnień, co oznacza, że użytkownicy z rolą Czytelnik nie będą już mieli do nich `Microsoft.HDInsight/clusters/configurations/action` dostępu. Role, które mają to uprawnienie, to Współautor, Właściciel i nowa rola Operator klastra usługi HDInsight (więcej informacji na ten temat znajduje się poniżej).

Wprowadzamy również nową rolę operatora klastra usługi [HDInsight,](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) która będzie mogła pobierać wpisy tajne bez przyznawania uprawnień administracyjnych współautora lub właściciela. Podsumowując:

| Rola                                  | Wcześniej                                                                                       | W przyszłości       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Czytelnik                                | — Dostęp do odczytu, w tym wpisy tajne.                                                                   | — Dostęp do odczytu z **wyłączeniem wpisów** tajnych | 
| Operator klastra usługi HDInsight<br>(Nowa rola) | Nie dotyczy                                                                                              | — Dostęp do odczytu/zapisu, w tym wpisów tajnych         | 
| Współautor                           | — Dostęp do odczytu/zapisu, w tym wpisów tajnych.<br>— Tworzenie wszystkich typów zasobów platformy Azure i zarządzanie nimi.<br>— Wykonywanie akcji skryptu.     | Bez zmian |
| Właściciel                                 | — Dostęp do odczytu/zapisu, w tym wpisów tajnych.<br>— Pełny dostęp do wszystkich zasobów<br>— Delegowanie dostępu do innych osób.<br>— Wykonywanie akcji skryptu. | Bez zmian |

Aby uzyskać informacje na temat dodawania przypisania roli Operator klastra usługi HDInsight do użytkownika w celu udzielenia użytkownikowi dostępu do odczytu/zapisu wpisów tajnych klastra, zobacz sekcję Dodawanie przypisania roli operator klastra usługi [HDInsight do użytkownika](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Czy te zmiany mają wpływ na mnie?

Dotyczy to następujących jednostek i scenariuszy:

- [Interfejs API:](#api)użytkownicy korzystający z `/configurations` punktów końcowych lub `/configurations/{configurationName}` .
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) w wersji 1.1.1 lub poniższej.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) wersji 3.20.0 lub poniższej.
- [Usługi Azure Data Lake i Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) w wersji 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) wersji 3.15.0 lub poniższej.
- [Zestaw SDK dla środowiska .NET](#sdk-for-net)
    - [wersje 1.x lub 2.x:](#versions-1x-and-2x)użytkownicy korzystający z metod , , lub z klasy `GetClusterConfigurations` `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` `DisableHttp` ConfigurationsOperationsExtensions.
    - [wersje 3.x i up:](#versions-3x-and-up)użytkownicy korzystający z `Get` metod , , lub z klasy `Update` `EnableHttp` `DisableHttp` `ConfigurationsOperationsExtensions` .
- [Zestaw SDK dla języka Python:](#sdk-for-python)użytkownicy korzystający z `get` metod lub z klasy `update` `ConfigurationsOperations` .
- [Zestaw SDK dla języka Java:](#sdk-for-java)użytkownicy korzystający z `update` metod lub z klasy `get` `ConfigurationsInner` .
- [Zestaw SDK dla języka Go:](#sdk-for-go)użytkownicy korzystający z `Get` metod lub ze struktury `Update` `ConfigurationsClient` .
- [Az.HDInsight PowerShell](#azhdinsight-powershell) w wersji 2.0.0.
Zapoznaj się z poniższymi sekcjami (lub użyj powyższych linków), aby zobaczyć kroki migracji dla swojego scenariusza.

### <a name="api"></a>Interfejs API

Następujące interfejsy API zostaną zmienione lub przestarzałe:

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (usunięte informacje poufne)
    - Wcześniej używane do uzyskiwania poszczególnych typów konfiguracji (w tym wpisów tajnych).
    - Począwszy od 3 września 2019 r., to wywołanie interfejsu API zwróci teraz poszczególne typy konfiguracji z pominiętym wpisami tajnymi. Aby uzyskać wszystkie konfiguracje, w tym wpisy tajne, użyj nowego wywołania POST /configurations. Aby uzyskać tylko ustawienia bramy, użyj nowego wywołania POST /getGatewaySettings.
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (przestarzałe)
    - Wcześniej używane do uzyskiwania wszystkich konfiguracji (w tym wpisów tajnych)
    - Począwszy od 3 września 2019 r., to wywołanie interfejsu API będzie przestarzałe i nie będzie już obsługiwane. Aby uzyskać wszystkie konfiguracje w przyszłości, użyj nowego wywołania POST /configurations. Aby uzyskać konfiguracje z pominięcia poufnymi parametrami, użyj wywołania GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (przestarzałe)
    - Wcześniej używane do aktualizowania poświadczeń bramy.
    - Począwszy od 3 września 2019 r., to wywołanie interfejsu API będzie przestarzałe i nie będzie już obsługiwane. Zamiast tego użyj nowego zestawu POST /updateGatewaySettings.

Dodano następujące zastępcze interfejsy API:</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ten interfejs API umożliwia uzyskanie wszystkich konfiguracji, w tym wpisów tajnych.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Użyj tego interfejsu API, aby uzyskać ustawienia bramy.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Ten interfejs API umożliwia aktualizowanie ustawień bramy (nazwy użytkownika i/lub hasła).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Jeśli używasz wersji 1.1.1 lub poniższej, zaktualizuj program do najnowszej wersji narzędzi [Azure HDInsight Tools for Visual Studio Code,](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) aby uniknąć przerw.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Jeśli używasz wersji 3.20.0 lub poniższej, zaktualizuj do najnowszej wersji wtyczki [Azure Toolkit for IntelliJ,](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) aby uniknąć przerw.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Usługi Azure Data Lake i Stream Analytics Tools for Visual Studio

Zaktualizuj usługę Azure Data Lake i narzędzia [Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) do wersji 2.3.9000.1 lub nowszej, aby uniknąć przerw.  Aby uzyskać pomoc dotyczącą aktualizowania, zapoznaj się z naszą dokumentacją, [Update Data Lake Tools for Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Jeśli używasz wersji 3.15.0 lub poniższej, zaktualizuj program do najnowszej wersji Azure Toolkit for Eclipse, aby uniknąć przerw. [](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)

### <a name="sdk-for-net"></a>Zestaw SDK środowiska .NET

#### <a name="versions-1x-and-2x"></a>Wersje 1.x i 2.x

Zaktualizuj zestaw SDK usługi HDInsight dla platformy .NET do wersji [2.1.0.](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) Jeśli używasz metody, na która te zmiany mają wpływ, mogą być wymagane minimalne modyfikacje kodu:

- `ClusterOperationsExtensions.GetClusterConfigurations` Nie **zwraca już poufnych parametrów,** takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj `ClusterOperationsExtensions.ListConfigurations` polecenia w przyszłości.  Należy pamiętać, że użytkownicy z rolą "Czytelnik" nie będą mogli używać tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji w klastrze.
    - Aby pobrać tylko poświadczenia bramy HTTP, `ClusterOperationsExtensions.GetGatewaySettings` użyj .

- `ClusterOperationsExtensions.GetConnectivitySettings` Jest teraz przestarzała i została zastąpiona przez `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` Jest teraz przestarzała i została zastąpiona przez `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` i `DisableHttp` są teraz przestarzałe. Protokół HTTP jest teraz zawsze włączony, więc te metody nie są już potrzebne.

#### <a name="versions-3x-and-up"></a>Wersje 3.x i starsze

Zaktualizuj zestaw SDK usługi HDInsight dla platformy .NET do wersji [5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) lub nowszej. Jeśli używasz metody, na która te zmiany mają wpływ, może być wymagana minimalna modyfikacja kodu:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get) Nie **zwraca już poufnych parametrów,** takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) polecenia w przyszłości.Należy pamiętać, że użytkownicy z rolą "Czytelnik" nie będą mogli używać tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji dotyczących klastra. 
    - Aby pobrać tylko poświadczenia bramy HTTP, [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) użyj . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) jest teraz przestarzała i została zastąpiona przez [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) i [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) są teraz przestarzałe. Protokół HTTP jest teraz zawsze włączony, więc te metody nie są już potrzebne.

### <a name="sdk-for-python"></a>Zestaw SDK dla środowiska Python

Zaktualizuj zestaw HDInsight SDK dla języka Python do wersji [1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) lub nowszej. Jeśli używasz metody, na która te zmiany mają wpływ, może być wymagana minimalna modyfikacja kodu:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) Nie **zwraca już poufnych parametrów,** takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) polecenia w przyszłości.Należy pamiętać, że użytkownicy z rolą "Czytelnik" nie będą mogli używać tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji dotyczących klastra. 
    - Aby pobrać tylko poświadczenia bramy HTTP, [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) użyj .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) jest teraz przestarzała i została zastąpiona przez [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>Zestaw SDK dla języka Java

Zaktualizuj zestaw HDInsight SDK dla języka Java do wersji [1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) lub nowszej. Jeśli używasz metody, na która te zmiany mają wpływ, mogą być wymagane minimalne modyfikacje kodu:

- `ConfigurationsInner.get` Nie **zwraca już poufnych parametrów,** takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia HTTP (brama).
- `ConfigurationsInner.update` jest teraz przestarzała.

### <a name="sdk-for-go"></a>Zestaw SDK dla języka Go

Zaktualizuj zestaw SDK usługi HDInsight dla języka Go do wersji [27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) lub nowszej. Jeśli używasz metody, na która te zmiany mają wpływ, mogą być wymagane minimalne modyfikacje kodu:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) Nie **zwraca już poufnych parametrów,** takich jak klucze magazynu (lokacja podstawowa) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w tym poufne parametry, użyj [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) polecenia w przyszłości.Należy pamiętać, że użytkownicy z rolą "Czytelnik" nie będą mogli używać tej metody. Pozwala to na szczegółową kontrolę nad tym, którzy użytkownicy mogą uzyskać dostęp do poufnych informacji w klastrze. 
    - Aby pobrać tylko poświadczenia bramy HTTP, [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) użyj .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) Jest teraz przestarzała i została zastąpiona przez [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Zaktualizuj program [Az PowerShell do wersji 2.0.0](https://www.powershellgallery.com/packages/Az) lub nowszej, aby uniknąć przerw.  Jeśli używasz metody, na która te zmiany mają wpływ, mogą być wymagane minimalne modyfikacje kodu.
- `Grant-AzHDInsightHttpServicesAccess` Jest teraz przestarzała i został zastąpiony przez nowe `Set-AzHDInsightGatewayCredential` polecenie cmdlet.
- `Get-AzHDInsightJobOutput` Został zaktualizowany w celu obsługi szczegółowego dostępu opartego na rolach do klucza magazynu.
    - Nie ma to wpływu na użytkowników mających rolę Operator, Współautor lub Właściciel w klastrze usługi HDInsight.
    - Użytkownicy z tylko rolą Czytelnik będą musieli jawnie określić `DefaultStorageAccountKey` parametr.
- `Revoke-AzHDInsightHttpServicesAccess` jest teraz przestarzała. Protokół HTTP jest teraz zawsze włączony, więc to polecenie cmdlet nie jest już potrzebne.
 Zobacz [az. Przewodnik po migracji do usługi HDInsight,](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) aby uzyskać więcej szczegółów.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Dodawanie przypisania roli operator klastra usługi HDInsight do użytkownika

Użytkownik z [](../role-based-access-control/built-in-roles.md#owner) rolą właściciela może przypisać rolę Operator klastra usługi [HDInsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) do użytkowników, którzy mają mieć dostęp do odczytu/zapisu do poufnych wartości konfiguracji klastra usługi HDInsight (takich jak poświadczenia bramy klastra i klucze konta magazynu).

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Najprostszym sposobem dodania tego przypisania roli jest użycie polecenia `az role assignment create` w interfejsie wiersza polecenia platformy Azure.

> [!NOTE]
> To polecenie musi być uruchamiane przez użytkownika z rolą Właściciel, ponieważ tylko on może udzielić tych uprawnień. To nazwa jednostki usługi lub adres e-mail użytkownika, do którego chcesz przypisać rolę operator klastra usługi `--assignee` HDInsight. Jeśli wystąpi błąd niewystarczających uprawnień, zobacz często zadawane pytania poniżej.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Udzielanie roli na poziomie zasobu (klastra)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Udzielanie roli na poziomie grupy zasobów

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Udzielanie roli na poziomie subskrypcji

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Możesz też użyć tego Azure Portal, aby dodać przypisanie roli Operator klastra usługi HDInsight do użytkownika. Zapoznaj się z dokumentacją [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="faq"></a>Często zadawane pytania

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Dlaczego widzę odpowiedź 403 (Zabronione) po zaktualizowaniu żądań interfejsu API i/lub narzędzia?

Konfiguracje klastra znajdują się teraz za szczegółową kontrolą dostępu opartą na rolach i wymagają `Microsoft.HDInsight/clusters/configurations/*` uprawnień dostępu do nich. Aby uzyskać to uprawnienie, przypisz rolę Operator klastra usługi HDInsight, Współautor lub Właściciel do użytkownika lub jednostki usługi próbującej uzyskać dostęp do konfiguracji.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Dlaczego podczas uruchamiania polecenia interfejsu wiersza polecenia platformy Azure w celu przypisania roli operator klastra usługi HDInsight do innego użytkownika lub jednostki usługi widzę "Niewystarczające uprawnienia do ukończenia operacji"?

Oprócz posiadania roli Właściciel użytkownik lub nazwa główna usługi wykonująca polecenie musi mieć wystarczające uprawnienia usługi Azure AD do wyszukiwania identyfikatorów obiektów przypisanego. Ten komunikat wskazuje na niewystarczające uprawnienia usługi Azure AD. Spróbuj zastąpić argument wartością i podaj identyfikator obiektu przypisanego jako parametr zamiast nazwy (lub identyfikatora podmiotu zabezpieczeń w przypadku tożsamości `-–assignee` `–assignee-object-id` zarządzanej). Aby uzyskać więcej informacji, zobacz sekcję dotyczącą parametrów opcjonalnych w dokumentacji [polecenia az role assignment create.](/cli/azure/role/assignment#az_role_assignment_create)

Jeśli to nadal nie działa, skontaktuj się z administratorem usługi Azure AD, aby uzyskać odpowiednie uprawnienia.

### <a name="what-will-happen-if-i-take-no-action"></a>Co się stanie, jeśli nie zajmę żadnej akcji?

Począwszy od 3 września 2019 r., wywołania nie będą już zwracać żadnych informacji, a wywołanie nie będzie już zwracać poufnych parametrów, takich jak klucze konta magazynu lub `GET /configurations` `POST /configurations/gateway` hasło `GET /configurations/{configurationName}` klastra. To samo dotyczy odpowiednich metod zestawu SDK i poleceń cmdlet programu PowerShell.

Jeśli używasz starszej wersji jednego z narzędzi dla programu Visual Studio, VSCode, IntelliJ lub Eclipse wymienionych powyżej, nie będą one już działać do momentu aktualizacji.

Aby uzyskać bardziej szczegółowe informacje, zobacz odpowiednią sekcję tego dokumentu dla twojego scenariusza.
