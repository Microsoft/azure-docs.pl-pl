---
title: Uwierzytelnianie Azure Batch Services za pomocą Azure Active Directory
description: Usługa Batch obsługuje uwierzytelnianie z usługi Batch w usłudze Azure AD. Dowiedz się, jak uwierzytelniać się na jeden z dwóch sposobów.
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: cb8306da4022ea1819e2da32a2f513c83bed309f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309374"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Uwierzytelnianie rozwiązań usługi Batch za pomocą Active Directory

Azure Batch obsługuje uwierzytelnianie z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD to usługa zarządzania tożsamościami w chmurze z wieloma dzierżawcami firmy Microsoft. Sama platforma Azure korzysta z usługi Azure AD do uwierzytelniania klientów, administratorów usług i użytkowników w organizacji.

W przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą Azure Batch można uwierzytelnić się na jeden z dwóch sposobów:

- Przy użyciu **uwierzytelniania zintegrowanego** do uwierzytelniania użytkownika, który działa z aplikacją. Aplikacja używająca uwierzytelniania zintegrowanego zbiera poświadczenia użytkownika i używa tych poświadczeń do uwierzytelniania dostępu do zasobów usługi Batch.
- Za pomocą **nazwy głównej usługi** do uwierzytelniania aplikacji nienadzorowanej. Nazwa główna usługi definiuje zasady i uprawnienia dla aplikacji w celu reprezentowania aplikacji podczas uzyskiwania dostępu do zasobów w czasie wykonywania.

Aby dowiedzieć się więcej o usłudze Azure AD, zapoznaj się z [dokumentacją Azure Active Directory](../active-directory/index.yml).

## <a name="endpoints-for-authentication"></a>Punkty końcowe uwierzytelniania

Aby uwierzytelniać aplikacje wsadowe za pomocą usługi Azure AD, należy uwzględnić w kodzie niektóre dobrze znane punkty końcowe.

### <a name="azure-ad-endpoint"></a>Punkt końcowy usługi Azure AD

Podstawowy punkt końcowy urzędu usługi Azure AD to:

`https://login.microsoftonline.com/`

Aby uwierzytelnić się w usłudze Azure AD, użyj tego punktu końcowego wraz z IDENTYFIKATORem dzierżawy (identyfikator katalogu). Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD do użycia na potrzeby uwierzytelniania. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> Punkt końcowy specyficzny dla dzierżawy jest wymagany podczas uwierzytelniania za pomocą nazwy głównej usługi.
>
> Punkt końcowy specyficzny dla dzierżawy jest opcjonalny w przypadku uwierzytelniania przy użyciu uwierzytelniania zintegrowanego, ale zalecane. Można jednak również użyć wspólnego punktu końcowego usługi Azure AD. Wspólny punkt końcowy zawiera interfejs zbierania poświadczeń ogólnych, gdy nie podano określonej dzierżawy. Wspólny punkt końcowy to `https://login.microsoftonline.com/common` .

Aby uzyskać więcej informacji na temat punktów końcowych usługi Azure AD, zobacz [uwierzytelnianie a autoryzacja](). /active-directory/develop/authentication-vs-authorization.md).

### <a name="batch-resource-endpoint"></a>Punkt końcowy zasobu partii

Użyj **punktu końcowego zasobów Azure Batch** , aby uzyskać token do uwierzytelniania żądań do usługi Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Rejestrowanie aplikacji w dzierżawie

Pierwszym krokiem korzystania z usługi Azure AD do uwierzytelniania jest rejestrowanie aplikacji w dzierżawie usługi Azure AD. Zarejestrowanie aplikacji umożliwia wywołanie [Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) platformy Azure (ADAL) z kodu. Biblioteka ADAL udostępnia interfejs API do uwierzytelniania w usłudze Azure AD z poziomu aplikacji. Rejestracja aplikacji jest wymagana niezależnie od tego, czy zamierzasz używać uwierzytelniania zintegrowanego, czy nazwy głównej usługi.

Po zarejestrowaniu aplikacji dostarczasz informacje o aplikacji do usługi Azure AD. Następnie usługa Azure AD udostępnia identyfikator aplikacji (nazywany także *identyfikatorem klienta*) używany do kojarzenia aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o IDENTYFIKATORze aplikacji, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować aplikację w usłudze Batch, wykonaj kroki opisane w sekcji **Rejestrowanie aplikacji** w [przewodniku szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md). Jeśli aplikacja jest zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI dla **identyfikatora URI przekierowania**. Nie musi to być rzeczywisty punkt końcowy.

Po zarejestrowaniu aplikacji zobaczysz identyfikator aplikacji:

![Zrzut ekranu identyfikatora aplikacji pokazanego w Azure Portal.](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>Uzyskaj identyfikator dzierżawy dla Active Directory

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD, która udostępnia usługi uwierzytelniania aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W Azure Portal wybierz Active Directory.
1. Wybierz pozycję **Właściwości**.
1. Skopiuj wartość identyfikatora GUID podaną dla **identyfikatora katalogu**. Ta wartość jest również określana mianem identyfikatora dzierżawy.

![Zrzut ekranu identyfikatora katalogu w Azure Portal.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Użyj uwierzytelniania zintegrowanego

Aby uwierzytelnić się przy użyciu uwierzytelniania zintegrowanego, należy przyznać aplikacji uprawnienia do nawiązywania połączeń z interfejsem API usługi Batch. Ten krok umożliwia aplikacji uwierzytelnianie wywołań interfejsu API usługi Batch za pomocą usług Azure AD.

Po zarejestrowaniu aplikacji wykonaj następujące kroki w Azure Portal, aby udzielić dostępu do usługi Batch:

1. W Azure Portal wybierz pozycję **wszystkie usługi**, a następnie wybierz pozycję **rejestracje aplikacji**.
1. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji.
1. Wybierz aplikację i wybierz pozycję **uprawnienia interfejsu API**.
1. W sekcji **uprawnienia interfejsu API** wybierz pozycję **Dodaj uprawnienie**.
1. W obszarze **Wybierz interfejs API**Wyszukaj interfejs API usługi Batch. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch.
1. Po znalezieniu interfejsu API usługi Batch zaznacz go, a następnie wybierz **pozycję Wybierz**.
1. W obszarze **Wybierz uprawnienia**zaznacz pole wyboru obok pozycji **dostęp do usługi Azure Batch** , a następnie wybierz pozycję **Dodaj uprawnienia**.

Sekcja **uprawnienia interfejsu API** pokazuje teraz, że aplikacja usługi Azure AD ma dostęp do zarówno Microsoft Graph, jak i interfejsu API usługi Batch. Uprawnienia są udzielane Microsoft Graph automatycznie po pierwszym zarejestrowaniu aplikacji w usłudze Azure AD.

## <a name="use-a-service-principal"></a>Korzystanie z nazwy głównej usługi

Aby uwierzytelnić aplikację, w której działa nienadzorowana, należy użyć nazwy głównej usługi. Po zarejestrowaniu aplikacji wykonaj następujące kroki w Azure Portal, aby skonfigurować jednostkę usługi:

1. Zażądaj wpisu tajnego dla aplikacji.
1. Przypisz funkcję kontroli dostępu opartej na rolach (Azure RBAC) do aplikacji.

### <a name="request-a-secret-for-your-application"></a>Żądaj wpisu tajnego dla aplikacji

Gdy aplikacja jest uwierzytelniana za pomocą nazwy głównej usługi, wysyła zarówno identyfikator aplikacji, jak i klucz tajny do usługi Azure AD. Należy utworzyć i skopiować klucz tajny do użycia w kodzie.

Wykonaj następujące kroki w witrynie Azure Portal:

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację z listy rejestracji aplikacji.
1. Wybierz aplikację, a następnie wybierz pozycję **certyfikaty & wpisy tajne**. W sekcji wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**.
1. Aby utworzyć wpis tajny, wprowadź opis wpisu tajnego. Następnie wybierz opcję wygaśnięcie klucza tajnego jednego roku, dwóch lat lub bez wygaśnięcia.
1. Wybierz pozycję **Dodaj** , aby utworzyć i wyświetlić wpis tajny. Skopiuj wartość klucza tajnego do bezpiecznego miejsca, ponieważ nie będzie można uzyskać do niego dostępu po opuszczeniu strony.

### <a name="assign-azure-rbac-to-your-application"></a>Przypisywanie RBAC platformy Azure do aplikacji

Aby uwierzytelnić się za pomocą nazwy głównej usługi, musisz przypisać do swojej aplikacji wartość RBAC platformy Azure. Wykonaj następujące kroki:

1. W Azure Portal przejdź do konta wsadowego używanego przez aplikację.
1. W sekcji **Ustawienia** konta Partia zadań wybierz pozycję **Access Control (IAM)**.
1. Wybierz kartę **przypisania ról** .
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. Z listy rozwijanej **rola** wybierz rolę *współautor* lub *czytelnika* dla aplikacji. Aby uzyskać więcej informacji na temat tych ról, zobacz [Rozpoczynanie pracy z kontrolą dostępu opartą na rolach na platformie Azure w Azure Portal](../role-based-access-control/overview.md).
1. W polu **Wybierz** wprowadź nazwę aplikacji. Wybierz aplikację z listy, a następnie wybierz pozycję **Zapisz**.

Aplikacja powinna teraz pojawić się w ustawieniach kontroli dostępu z przypisaną rolą platformy Azure.

![Przypisywanie roli platformy Azure do aplikacji](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Przypisywanie roli niestandardowej

Rola niestandardowa przyznaje użytkownikowi szczegółowe uprawnienia do przesyłania zadań, zadań i nie tylko. Dzięki temu użytkownicy mogą uniemożliwić wykonywanie operacji, które mają wpływ na koszt, na przykład tworząc pule lub modyfikując węzły.

Możesz użyć roli niestandardowej, aby udzielić uprawnień użytkownikowi, grupie lub jednostce usługi usługi Azure AD dla następujących operacji RBAC usługi Azure:

- Microsoft.Batch/batchAccounts/pule/zapis
- Microsoft.Batch/batchAccounts/pule/usuwanie
- Microsoft.Batch/batchAccounts/pule/odczyt
- Microsoft.Batch/batchAccounts/jobSchedules/zapis
- Microsoft.Batch/batchAccounts/jobSchedules/Delete
- Microsoft.Batch/batchAccounts/jobSchedules/odczyt
- Microsoft.Batch/batchAccounts/Jobs/Write
- Microsoft.Batch/batchAccounts/Jobs/Delete
- Microsoft.Batch/batchAccounts/Jobs/Read
- Microsoft.Batch/batchAccounts/Certificates/Write
- Microsoft.Batch/batchAccounts/Certificates/Delete
- Microsoft.Batch/batchAccounts/Certificates/Read
- Microsoft.Batch/batchAccounts/odczyt (dla każdej operacji odczytu)
- Microsoft.Batch/batchAccounts/listKeys/Action (dla dowolnej operacji)

Role niestandardowe są przeznaczone dla użytkowników uwierzytelnionych przez usługę Azure AD, a nie poświadczenia konta usługi Batch (klucz współużytkowany). Zwróć uwagę, że poświadczenia konta w usłudze Batch dają pełne uprawnienia do konta w usłudze Batch. Należy również pamiętać, że zadania używające autopuli wymagają uprawnień na poziomie puli.

> [!NOTE]
> W polu akcja należy określić pewne przypisania ról, podczas gdy inne muszą być określone w polu dataaction. Aby uzyskać więcej informacji, zobacz operacje związane z [dostawcą zasobów platformy Azure](../role-based-access-control/resource-provider-operations.md#microsoftbatch).

Oto przykład definicji roli niestandardowej:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Aby uzyskać więcej informacji na temat tworzenia roli niestandardowej, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Uzyskaj identyfikator dzierżawy dla Azure Active Directory

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD, która udostępnia usługi uwierzytelniania aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W Azure Portal wybierz Active Directory.
1. Wybierz pozycję **Właściwości**.
1. Skopiuj wartość identyfikatora GUID podaną dla **identyfikatora katalogu**. Ta wartość jest również określana mianem identyfikatora dzierżawy.

![Skopiuj identyfikator katalogu](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Przykłady kodu

Przykłady kodu w tej sekcji przedstawiają sposób uwierzytelniania za pomocą usługi Azure AD przy użyciu uwierzytelniania zintegrowanego i nazwy głównej usługi. Większość z tych przykładów kodu korzysta z platformy .NET, ale koncepcje są podobne dla innych języków.

> [!NOTE]
> Token uwierzytelniania usługi Azure AD wygasa po upływie godziny. W przypadku korzystania z długotrwałego obiektu **BatchClient** zalecamy pobranie tokenu z biblioteki ADAL na każde żądanie, aby upewnić się, że zawsze masz prawidłowy token.
>
> Aby to osiągnąć, należy napisać metodę pobierającą token z usługi Azure AD i przekazać tę metodę do obiektu **BatchTokenCredentials** jako delegata. Metoda Delegate jest wywoływana dla każdego żądania do usługi Batch, aby upewnić się, że podano prawidłowy token. Domyślnie tokeny pamięci podręcznej biblioteki ADAL, dlatego nowy token jest pobierany z usługi Azure AD tylko wtedy, gdy jest to konieczne. Aby uzyskać więcej informacji na temat tokenów w usłudze Azure AD, zobacz [tokeny zabezpieczające](../active-directory/develop/security-tokens.md).

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Przykład kodu: używanie zintegrowanego uwierzytelniania usługi Azure AD z usługą Batch .NET

Aby uwierzytelnić się przy użyciu zintegrowanego uwierzytelniania z usługi Batch .NET, należy odwołać się do pakietu [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) i pakietu [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Uwzględnij następujące `using` instrukcje w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołuje się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikatora dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołuje się do punktu końcowego zasobu usługi Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołuje się do konta w usłudze Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) aplikacji. Identyfikator aplikacji jest dostępny w ramach rejestracji aplikacji w Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Skopiuj również określony identyfikator URI przekierowania, jeśli aplikacja została zarejestrowana jako aplikacja natywna. Identyfikator URI przekierowania określony w kodzie musi być zgodny z identyfikatorem URI przekierowania, który został podany podczas rejestracji aplikacji:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Napisz metodę wywołania zwrotnego, aby uzyskać token uwierzytelniania z usługi Azure AD. Metoda wywołania zwrotnego **GetAuthenticationTokenAsync** pokazana tutaj wywołuje bibliotekę ADAL w celu uwierzytelnienia użytkownika, który działa z aplikacją. Metoda **AcquireTokenAsync** udostępniona przez bibliotekę ADAL wyświetla użytkownikowi informacje o poświadczeniach, a aplikacja jest wykonywana, gdy użytkownik je udostępni (chyba że ma już poświadczenia w pamięci podręcznej):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Utwórz obiekt **BatchTokenCredentials** , który przyjmuje delegat jako parametr. Użyj tych poświadczeń, aby otworzyć obiekt **BatchClient** . Tego obiektu **BatchClient** można użyć do kolejnych operacji w usłudze Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Przykład kodu: używanie jednostki usługi Azure AD w usłudze Batch .NET

Aby uwierzytelnić się za pomocą jednostki usługi w usłudze Batch .NET, należy odwołać się do pakietu [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) i pakietu [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) .

Uwzględnij następujące `using` instrukcje w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołuje się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikatora dzierżawy. W przypadku korzystania z jednostki usługi należy podać punkt końcowy specyficzny dla dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołuje się do punktu końcowego zasobu usługi Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołuje się do konta w usłudze Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) aplikacji. Identyfikator aplikacji jest dostępny w ramach rejestracji aplikacji w Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Określ klucz tajny, który został skopiowany z Azure Portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Napisz metodę wywołania zwrotnego, aby uzyskać token uwierzytelniania z usługi Azure AD. Metoda wywołania zwrotnego **GetAuthenticationTokenAsync** pokazana tutaj wywołuje biblioteki ADAL do uwierzytelniania nienadzorowanego:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Utwórz obiekt **BatchTokenCredentials** , który przyjmuje delegat jako parametr. Użyj tych poświadczeń, aby otworzyć obiekt **BatchClient** . Następnie użyj tego obiektu **BatchClient** dla kolejnych operacji w usłudze Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Przykład kodu: używanie jednostki usługi Azure AD w usłudze Batch Python

Aby uwierzytelnić się za pomocą jednostki usługi w usłudze Batch Python, zainstaluj i Odwołuj się do modułów [Azure-Batch](https://pypi.org/project/azure-batch/) i [Azure-Common](https://pypi.org/project/azure-common/) .

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

W przypadku korzystania z jednostki usługi należy podać identyfikator dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w temacie [Pobieranie identyfikatora dzierżawy dla Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Odwołuje się do punktu końcowego zasobu usługi Batch:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Odwołuje się do konta w usłudze Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Określ identyfikator aplikacji (identyfikator klienta) aplikacji. Identyfikator aplikacji jest dostępny w ramach rejestracji aplikacji w Azure Portal:

```python
CLIENT_ID = "<application-id>"
```

Określ klucz tajny, który został skopiowany z Azure Portal:

```python
SECRET = "<secret-key>"
```

Utwórz obiekt **ServicePrincipalCredentials** :

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Użyj poświadczeń jednostki usługi, aby otworzyć obiekt **BatchServiceClient** . Następnie użyj tego obiektu **BatchServiceClient** dla kolejnych operacji w usłudze Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [dokumentacją Azure Active Directory](../active-directory/index.yml). Szczegółowe przykłady pokazujące, jak używać biblioteki ADAL, są dostępne w bibliotece [przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .
- Więcej informacji o [obiektach głównych aplikacji i usług w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) oraz [sposobie tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md).
- Dowiedz się więcej o [uwierzytelnianiu rozwiązań do zarządzania partiami przy użyciu Active Directory](batch-aad-auth-management.md).
- Przykładowy kod w języku Python dotyczący tworzenia klienta usługi Batch uwierzytelniany przy użyciu tokenu usługi Azure AD znajduje się w temacie [wdrażanie Azure Batch niestandardowego obrazu przy użyciu skryptu języka Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) .

