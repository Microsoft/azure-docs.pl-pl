---
title: Korzystanie z biblioteki zarządzania usługą Batch w celu zarządzania zasobami konta
description: Tworzenie, usuwanie i modyfikowanie zasobów konta Azure Batch przy użyciu biblioteki zarządzania usługą Batch dla platformy .NET.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896735"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Zarządzanie kontami i przydziałami usługi Batch za pomocą biblioteki klienta zarządzania usługą Batch dla platformy .NET

Możesz obniżyć koszty utrzymania w aplikacjach Azure Batch przy użyciu biblioteki [zarządzania usługą Batch .NET](/dotnet/api/overview/azure/batch) do automatyzowania tworzenia kont usługi Batch, usuwania, zarządzania kluczami i odnajdowania przydziałów.

- **Tworzenie i usuwanie kont wsadowych** w dowolnym regionie. Jeśli na przykład jako niezależny dostawca oprogramowania zostanie podaną usługę dla klientów, za pomocą której każdy ma przypisane oddzielne konto usługi Batch na potrzeby rozliczeń, można dodać możliwości tworzenia i usuwania kont do portalu klientów.
- **Pobierz i ponownie wygeneruj klucze kont** dla dowolnego konta usługi Batch. Może to ułatwić przestrzeganie zasad zabezpieczeń, które wymuszają okresowe Przerzucanie lub wygaśnięcie kluczy konta. Jeśli masz kilka kont usługi Batch w różnych regionach platformy Azure, Automatyzacja tego procesu przerzucania spowoduje zwiększenie wydajności rozwiązania.
- **Sprawdź przydziały kont** i zapoznaj się z informacjami o tym, które konta usługi Batch mają ograniczenia. Sprawdzając przydziały kont przed rozpoczęciem wykonywania zadań, tworzeniu pul lub dodawaniu węzłów obliczeniowych, możesz proaktywnie dostosować miejsce i czas tworzenia tych zasobów obliczeniowych. Można określić, które konta wymagają zwiększenia limitu przydziału przed przydzieleniem dodatkowych zasobów na tych kontach.
- **Połącz funkcje innych usług platformy Azure** , aby uzyskać w pełni funkcjonalne środowisko zarządzania przy użyciu usługi Batch Management .net, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)i [Azure Resource Manager](../azure-resource-manager/management/overview.md) razem w tej samej aplikacji. Korzystając z tych funkcji i ich interfejsów API, można zapewnić bezproblemowe środowisko uwierzytelniania, możliwość tworzenia i usuwania grup zasobów oraz funkcje, które są opisane powyżej dla kompleksowego rozwiązania do zarządzania.

> [!NOTE]
> Ten artykuł koncentruje się na zarządzaniu zarządzanymi kontami usługi Batch, kluczami i przydziałami, ale także wiele z tych działań można wykonać [przy użyciu Azure Portal](batch-account-create-portal.md).

## <a name="create-and-delete-batch-accounts"></a>Tworzenie i usuwanie kont w usłudze Batch

Jedną z podstawowych funkcji interfejsu API zarządzania usługą Batch jest tworzenie i usuwanie kont usługi [Batch](accounts.md) w regionie platformy Azure. Aby to zrobić, użyj [BatchManagementClient. Account. setasync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) i [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync)lub ich synchronicznych odpowiedników.

Poniższy fragment kodu tworzy konto, uzyskuje nowo utworzone konto z usługi Batch, a następnie usuwa je. W tym fragmencie kodu i innych w tym artykule `batchManagementClient` jest w pełni zainicjowane wystąpienie [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

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
> Aplikacje korzystające z biblioteki .NET Management Library i jej klasy BatchManagementClient wymagają dostępu administratora usługi lub współadministratora do subskrypcji należącej do konta usługi Batch, które ma być zarządzane. Aby uzyskać więcej informacji, zobacz sekcję Azure Active Directory i przykład kodu [zarządzania kontem](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) .

## <a name="retrieve-and-regenerate-account-keys"></a>Pobieranie i ponowne generowanie kluczy konta

Uzyskaj podstawowe i pomocnicze klucze konta z dowolnego konta usługi Batch w ramach subskrypcji przy użyciu usługi [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Klucze te można wygenerować ponownie przy użyciu [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
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
> Można utworzyć wydajny przepływ pracy połączenia dla aplikacji do zarządzania. Najpierw Uzyskaj klucz konta dla konta usługi Batch, którym chcesz zarządzać za pomocą usługi [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Następnie użyj tego klucza podczas inicjowania klasy [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) biblioteki .NET partii, która jest używana podczas inicjowania [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Sprawdź limity przydziału subskrypcji platformy Azure i konta usługi Batch

Subskrypcje platformy Azure i indywidualne usługi platformy Azure, takie jak Batch All, mają domyślne przydziały, które ograniczają liczbę niektórych jednostek w ramach tych usług. Aby uzyskać domyślne przydziały dla subskrypcji platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md). W przypadku domyślnych przydziałów usługi Batch zobacz [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md). Za pomocą biblioteki usługi Batch Management .NET można sprawdzić te przydziały w aplikacjach. Dzięki temu można podejmować decyzje dotyczące alokacji przed dodaniem kont lub zasobów obliczeniowych, takich jak pule i węzły obliczeniowe.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Sprawdź subskrypcję platformy Azure dla przydziałów konta usługi Batch

Przed utworzeniem konta usługi Batch w regionie możesz sprawdzić swoją subskrypcję platformy Azure, aby sprawdzić, czy można dodać konto w tym regionie.

W poniższym fragmencie kodu najpierw **ListAsync** się w celu pobrania kolekcji wszystkich kont usługi Batch, które znajdują się w ramach subskrypcji. Po uzyskaniu tej kolekcji określimy, ile kont znajduje się w regionie docelowym. Następnie korzystamy z **GetQuotasAsync** , aby uzyskać limit przydziału konta w usłudze Batch i określić, ile kont (jeśli istnieje) można utworzyć w tym regionie.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

W powyższym fragmencie kodu `creds` jest wystąpienie elementu **TokenCredentials**. Aby zapoznać się z przykładem tworzenia tego obiektu, zobacz przykładowy kod [zarządzania kontem](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) w witrynie GitHub.

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
> Chociaż istnieją domyślne przydziały dla subskrypcji i usług platformy Azure, wiele z tych limitów może zostać zgłoszonych przez [zażądanie zwiększenia limitu przydziału w Azure Portal](batch-quota-limit.md#increase-a-quota).

## <a name="use-azure-ad-with-batch-management-net"></a>Korzystanie z usługi Azure AD z usługą Batch Management .NET

Biblioteka platformy .NET zarządzania usługą Batch jest klientem dostawcy zasobów platformy Azure i jest używana razem z [Azure Resource Manager](../azure-resource-manager/management/overview.md) do programowego zarządzania zasobami konta. Usługa Azure AD jest wymagana do uwierzytelniania żądań wysyłanych za pomocą dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki usługi Batch Management .NET, a także za pomocą Azure Resource Manager. Aby uzyskać informacje o korzystaniu z usługi Azure AD z biblioteką zarządzania usługą Batch dla platformy .NET, zobacz [używanie Azure Active Directory do uwierzytelniania rozwiązań usługi Batch](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Przykładowy projekt w witrynie GitHub

Aby zobaczyć, jak działa środowisko .NET zarządzania usługą Batch, zapoznaj się z przykładowym projektem [zarządzania kontem](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) w witrynie GitHub. Przykładowa aplikacja zarządzania kontem ilustruje następujące operacje:

1. Uzyskaj token zabezpieczający z usługi Azure AD przy użyciu [biblioteki ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Jeśli użytkownik nie jest jeszcze zalogowany, zostanie wyświetlony monit o podanie poświadczeń platformy Azure.
2. Korzystając z tokenu zabezpieczającego uzyskanego z usługi Azure AD, Utwórz zapytanie o usługę Azure [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) , aby uzyskać listę subskrypcji skojarzonych z tym kontem. Użytkownik może wybrać subskrypcję z listy, jeśli zawiera więcej niż jedną subskrypcję.
3. Pobierz poświadczenia skojarzone z wybraną subskrypcją.
4. Utwórz obiekt [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) przy użyciu poświadczeń.
5. Użyj obiektu [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) , aby utworzyć grupę zasobów.
6. Użyj obiektu [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) , aby wykonać kilka operacji na koncie wsadowym:
   - Utwórz konto wsadowe w nowej grupie zasobów.
   - Pobierz nowo utworzone konto z usługi Batch.
   - Wydrukuj klucze kont dla nowego konta.
   - Wygeneruj ponownie nowy klucz podstawowy dla konta.
   - Wydrukuj informacje o przydziale dla konta.
   - Wydrukuj informacje o przydziale dla subskrypcji.
   - Drukuj wszystkie konta w ramach subskrypcji.
   - Usuń nowo utworzone konto.
7. Usuń grupę zasobów.

Aby pomyślnie uruchomić przykładową aplikację, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD w Azure Portal i przyznać uprawnienia do interfejsu API Azure Resource Manager. Wykonaj czynności podane w temacie [uwierzytelnianie rozwiązań do zarządzania partiami przy użyciu Active Directory](batch-aad-auth-management.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](quick-run-dotnet.md) lub języka [Python](quick-run-python.md). Te Przewodniki Szybki Start przeprowadzą Cię przez przykładową aplikację, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych przy użyciu magazynu platformy Azure na potrzeby przemieszczania i pobierania pliku obciążenia. pus usługi git
