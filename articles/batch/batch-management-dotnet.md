---
title: Korzystanie z biblioteki zarządzania usługą Batch w celu zarządzania zasobami konta
description: Tworzenie, usuwanie i modyfikowanie zasobów konta Azure Batch przy użyciu biblioteki zarządzania usługą Batch dla platformy .NET.
ms.topic: how-to
ms.date: 04/24/2017
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: 0672a7dc1a5c26eff88806ca37b28d70b49f2288
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88926528"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Zarządzanie kontami i przydziałami usługi Batch za pomocą biblioteki klienta zarządzania usługą Batch dla platformy .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Możesz obniżyć koszty utrzymania w aplikacjach Azure Batch przy użyciu biblioteki [zarządzania usługą Batch .NET][api_mgmt_net] do automatyzowania tworzenia kont usługi Batch, usuwania, zarządzania kluczami i odnajdowania przydziałów.

* **Tworzenie i usuwanie kont wsadowych** w dowolnym regionie. Jeśli na przykład jako niezależny dostawca oprogramowania zostanie podaną usługę dla klientów, za pomocą której każdy ma przypisane oddzielne konto usługi Batch na potrzeby rozliczeń, można dodać możliwości tworzenia i usuwania kont do portalu klientów.
* **Pobierz i ponownie wygeneruj klucze kont** dla dowolnego konta usługi Batch. Może to ułatwić przestrzeganie zasad zabezpieczeń, które wymuszają okresowe Przerzucanie lub wygaśnięcie kluczy konta. Jeśli masz kilka kont usługi Batch w różnych regionach platformy Azure, Automatyzacja tego procesu przerzucania spowoduje zwiększenie wydajności rozwiązania.
* **Sprawdź przydziały kont** i zapoznaj się z informacjami o tym, które konta usługi Batch mają ograniczenia. Sprawdzając przydziały kont przed rozpoczęciem wykonywania zadań, tworzeniu pul lub dodawaniu węzłów obliczeniowych, możesz proaktywnie dostosować miejsce i czas tworzenia tych zasobów obliczeniowych. Można określić, które konta wymagają zwiększenia limitu przydziału przed przydzieleniem dodatkowych zasobów na tych kontach.
* **Połącz funkcje innych usług platformy Azure** , aby skorzystać z w pełni funkcjonalnego środowiska zarządzania — za pomocą usługi Batch Management .net, [Azure Active Directory][aad_about]i [Azure Resource Manager][resman_overview] razem w tej samej aplikacji. Korzystając z tych funkcji i ich interfejsów API, można zapewnić bezproblemowe środowisko uwierzytelniania, możliwość tworzenia i usuwania grup zasobów oraz funkcje, które są opisane powyżej dla kompleksowego rozwiązania do zarządzania.

> [!NOTE]
> Ten artykuł koncentruje się na zarządzaniu zarządzanymi kontami usługi Batch, kluczami i przydziałami, ale można wykonać wiele z tych działań za pomocą [Azure Portal][azure_portal]. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Batch przy użyciu Azure Portal](batch-account-create-portal.md) i [przydziałów i limitów dla usługi Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Tworzenie i usuwanie kont w usłudze Batch
Jak wspomniano, jedną z podstawowych funkcji interfejsu API zarządzania usługą Batch jest tworzenie i usuwanie kont usługi Batch w regionie platformy Azure. Aby to zrobić, użyj [BatchManagementClient. Account. setasync][net_create] i [DeleteAsync][net_delete]lub ich synchronicznych odpowiedników.

Poniższy fragment kodu tworzy konto, uzyskuje nowo utworzone konto z usługi Batch, a następnie usuwa je. W tym fragmencie kodu i innych w tym artykule `batchManagementClient` jest w pełni zainicjowane wystąpienie [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Aplikacje korzystające z biblioteki .NET Management Library i jej klasy BatchManagementClient wymagają dostępu **administratora usługi** lub **współadministratora** do subskrypcji należącej do konta usługi Batch, które ma być zarządzane. Aby uzyskać więcej informacji, zobacz sekcję Azure Active Directory i przykład kodu [zarządzania kontem][acct_mgmt_sample] .
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Pobieranie i ponowne generowanie kluczy konta
Uzyskaj podstawowe i pomocnicze klucze konta z dowolnego konta usługi Batch w ramach subskrypcji przy użyciu usługi [ListKeysAsync][net_list_keys]. Klucze te można wygenerować ponownie przy użyciu [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Można utworzyć wydajny przepływ pracy połączenia dla aplikacji do zarządzania. Najpierw Uzyskaj klucz konta dla konta usługi Batch, którym chcesz zarządzać za pomocą usługi [ListKeysAsync][net_list_keys]. Następnie użyj tego klucza podczas inicjowania klasy [BatchSharedKeyCredentials][net_sharedkeycred] biblioteki .NET partii, która jest używana podczas inicjowania [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Sprawdź limity przydziału subskrypcji platformy Azure i konta usługi Batch
Subskrypcje platformy Azure i indywidualne usługi platformy Azure, takie jak Batch All, mają domyślne przydziały, które ograniczają liczbę niektórych jednostek w ramach tych usług. Aby uzyskać domyślne przydziały dla subskrypcji platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md). W przypadku domyślnych przydziałów usługi Batch zobacz [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md). Za pomocą biblioteki usługi Batch Management .NET można sprawdzić te przydziały w aplikacjach. Dzięki temu można podejmować decyzje dotyczące alokacji przed dodaniem kont lub zasobów obliczeniowych, takich jak pule i węzły obliczeniowe.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Sprawdź subskrypcję platformy Azure dla przydziałów konta usługi Batch
Przed utworzeniem konta usługi Batch w regionie możesz sprawdzić swoją subskrypcję platformy Azure, aby sprawdzić, czy można dodać konto w tym regionie.

W poniższym fragmencie kodu najpierw użyto [BatchManagementClient. Account. ListAsync][net_mgmt_listaccounts] , aby uzyskać kolekcję wszystkich kont usługi Batch, które znajdują się w ramach subskrypcji. Po uzyskaniu tej kolekcji określimy, ile kont znajduje się w regionie docelowym. Następnie korzystamy z [BatchManagementClient. subscriptions][net_mgmt_subscriptions] w celu uzyskania przydziału konta wsadowego i określenia, ile kont (jeśli istnieje) można utworzyć w tym regionie.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

W powyższym fragmencie kodu `creds` jest wystąpienie elementu [TokenCloudCredentials][azure_tokencreds]. Aby zapoznać się z przykładem tworzenia tego obiektu, zobacz przykładowy kod [zarządzania kontem][acct_mgmt_sample] w witrynie GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Sprawdź konto usługi Batch dla przydziałów zasobów obliczeniowych
Przed zwiększeniem zasobów obliczeniowych w rozwiązaniu wsadowym można sprawdzić, czy zasoby, które mają zostać przydzielone, nie przekraczają przydziałów konta. W poniższym fragmencie kodu wypisujemy informacje o przydziale dla konta usługi Batch o nazwie `mybatchaccount` . W aplikacji można użyć takich informacji w celu ustalenia, czy konto może obsługiwać dodatkowe zasoby, które mają zostać utworzone.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Chociaż istnieją domyślne przydziały dla subskrypcji i usług platformy Azure, wiele z tych limitów może zostać zgłoszonych przez wystawienie żądania w [Azure Portal][azure_portal]. Na przykład zapoznaj [się z przydziałami i limitami usługi Azure Batch](batch-quota-limit.md) , aby uzyskać instrukcje dotyczące zwiększenia liczby przydziałów konta usługi Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Korzystanie z usługi Azure AD z usługą Batch Management .NET

Biblioteka platformy .NET zarządzania usługą Batch jest klientem dostawcy zasobów platformy Azure i jest używana razem z [Azure Resource Manager][resman_overview] do programowego zarządzania zasobami konta. Usługa Azure AD jest wymagana do uwierzytelniania żądań wysyłanych za pomocą dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki usługi Batch Management .NET, a także za pomocą [Azure Resource Manager][resman_overview]. Aby uzyskać informacje o korzystaniu z usługi Azure AD z biblioteką zarządzania usługą Batch dla platformy .NET, zobacz [używanie Azure Active Directory do uwierzytelniania rozwiązań usługi Batch](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Przykładowy projekt w witrynie GitHub

Aby zobaczyć, jak działa środowisko .NET zarządzania usługą Batch, zapoznaj się z przykładowym projektem [zarządzania kontem][acct_mgmt_sample] w witrynie GitHub. Przykładowa aplikacja zarządzania kontem ilustruje następujące operacje:

1. Uzyskaj token zabezpieczający z usługi Azure AD przy użyciu [biblioteki ADAL][aad_adal]. Jeśli użytkownik nie jest jeszcze zalogowany, zostanie wyświetlony monit o podanie poświadczeń platformy Azure.
2. Korzystając z tokenu zabezpieczającego uzyskanego z usługi Azure AD, Utwórz zapytanie o usługę Azure [SubscriptionClient][resman_subclient] , aby uzyskać listę subskrypcji skojarzonych z tym kontem. Użytkownik może wybrać subskrypcję z listy, jeśli zawiera więcej niż jedną subskrypcję.
3. Pobierz poświadczenia skojarzone z wybraną subskrypcją.
4. Utwórz obiekt [ResourceManagementClient][resman_client] przy użyciu poświadczeń.
5. Użyj obiektu [ResourceManagementClient][resman_client] , aby utworzyć grupę zasobów.
6. Użyj obiektu [BatchManagementClient][net_mgmt_client] , aby wykonać kilka operacji na koncie wsadowym:
   * Utwórz konto wsadowe w nowej grupie zasobów.
   * Pobierz nowo utworzone konto z usługi Batch.
   * Wydrukuj klucze kont dla nowego konta.
   * Wygeneruj ponownie nowy klucz podstawowy dla konta.
   * Wydrukuj informacje o przydziale dla konta.
   * Wydrukuj informacje o przydziale dla subskrypcji.
   * Drukuj wszystkie konta w ramach subskrypcji.
   * Usuń nowo utworzone konto.
7. Usuń grupę zasobów.

Przed usunięciem nowo utworzonego konta partii i grupy zasobów można je wyświetlić w [Azure Portal][azure_portal]:

Aby pomyślnie uruchomić przykładową aplikację, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD w Azure Portal i przyznać uprawnienia do interfejsu API Azure Resource Manager. Wykonaj czynności podane w temacie [uwierzytelnianie rozwiązań do zarządzania partiami przy użyciu Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest Azure Active Directory?"
[aad_adal]: ../active-directory/azuread-dev/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrowanie aplikacji z Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: /dotnet/api/microsoft.azure.batch
[api_mgmt_net]: /dotnet/api/overview/azure/batch
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: /previous-versions/azure/reference/mt167728(v=azure.100)
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_list_keys]: /previous-versions/azure/mt463199(v=azure.100)
[net_create]: /previous-versions/azure/mt463210(v=azure.100)
[net_delete]: /previous-versions/azure/mt463128(v=azure.100)
[net_regenerate_keys]: /previous-versions/azure/mt463213(v=azure.100)
[net_sharedkeycred]: /dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials
[net_mgmt_client]: /dotnet/api/microsoft.azure.management.batch.batchmanagementclient
[net_mgmt_subscriptions]: /previous-versions/azure/mt592937(v=azure.100)
[net_mgmt_listaccounts]: /previous-versions/azure/mt463134(v=azure.100)
[resman_api]: /previous-versions/azure/mt463134(v=azure.100)
[resman_client]: /dotnet/api/microsoft.azure.management.resourcemanager
[resman_subclient]: /dotnet/api/microsoft.azure.management.resourcemanager
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
