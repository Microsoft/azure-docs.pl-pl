---
title: Uzyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej
titleSuffix: Azure Storage
description: Użyj Azure Active Directory do uwierzytelniania z poziomu aplikacji klienckiej, uzyskania tokenu OAuth 2,0 i autoryzacji żądań do magazynu obiektów blob platformy Azure i usługi queue storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6dacb1cd910c6569d94f365b34a15494dde70a4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787690"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Uzyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej

Główną zaletą korzystania z Azure Active Directory (Azure AD) z usługą Azure Blob Storage lub kolejką queue storage jest to, że Twoje poświadczenia nie muszą już być przechowywane w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2,0 z platformy tożsamości firmy Microsoft. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupę lub nazwę główną usługi), na którym działa aplikacja. W przypadku pomyślnego uwierzytelnienia usługa Azure AD zwraca token dostępu do aplikacji, a następnie może użyć tokenu dostępu do autoryzowania żądań do usługi Azure Blob Storage lub magazynu kolejek.

W tym artykule pokazano, jak skonfigurować aplikację natywną lub aplikację sieci Web do uwierzytelniania za pomocą platformy tożsamości firmy Microsoft przy użyciu przykładowej aplikacji, która jest dostępna do pobrania. Przykładowa aplikacja zawiera platformę .NET, ale inne języki używają podobnego podejścia. Aby uzyskać więcej informacji na temat platformy tożsamości firmy Microsoft, zobacz [Microsoft Identity platform — Omówienie](../../active-directory/develop/v2-overview.md).

Aby zapoznać się z omówieniem przepływu przyznawania kodu OAuth 2,0, zobacz temat [Autoryzuj dostęp do aplikacji sieci web Azure Active Directory przy użyciu przepływu przyznawania kodu oauth 2,0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>Informacje o aplikacji przykładowej

Przykładowa aplikacja zawiera kompleksowe środowisko, w którym pokazano, jak skonfigurować aplikację sieci Web do uwierzytelniania za pomocą usługi Azure AD w lokalnym środowisku programistycznym. Aby wyświetlić i uruchomić przykładową aplikację, należy najpierw sklonować ją lub pobrać z witryny [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Następnie postępuj zgodnie z instrukcjami przedstawionymi w artykule, aby skonfigurować rejestrację aplikacji platformy Azure i zaktualizować aplikację dla danego środowiska.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Przypisywanie roli do podmiotu zabezpieczeń usługi Azure AD

Aby uwierzytelnić podmiot zabezpieczeń z aplikacji usługi Azure Storage, najpierw Skonfiguruj ustawienia kontroli dostępu opartej na rolach (Azure RBAC) dla tego podmiotu zabezpieczeń. Usługa Azure Storage definiuje wbudowane role, które obejmują uprawnienia do kontenerów i kolejek. Po przypisaniu roli platformy Azure do podmiotu zabezpieczeń ten podmiot zabezpieczeń ma udzielony dostęp do tego zasobu. Aby uzyskać więcej informacji, zobacz [Zarządzanie prawami dostępu do obiektów blob platformy Azure i Kolejkowanie danych przy użyciu RBAC na platformie Azure](./storage-auth-aad-rbac-portal.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi Azure AD

Pierwszy krok w korzystaniu z usługi Azure AD do autoryzacji dostępu do zasobów magazynu polega na zarejestrowaniu aplikacji klienckiej z dzierżawą usługi Azure AD na podstawie [Azure Portal](https://portal.azure.com). Po zarejestrowaniu aplikacji klienckiej podaj informacje o aplikacji do usługi Azure AD. Następnie usługa Azure AD udostępnia identyfikator klienta (nazywany również *identyfikatorem aplikacji* ) używany do kojarzenia aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o IDENTYFIKATORze klienta, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). Aby zarejestrować aplikację usługi Azure Storage, wykonaj kroki przedstawione w [przewodniku szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). 

Na poniższej ilustracji przedstawiono typowe ustawienia rejestracji aplikacji sieci Web. Należy pamiętać, że w tym przykładzie identyfikator URI przekierowania jest ustawiany na `http://localhost:5000/signin-oidc` na potrzeby testowania przykładowej aplikacji w środowisku deweloperskim. Możesz zmodyfikować to ustawienie później w obszarze Ustawienia **uwierzytelniania** dla zarejestrowanej aplikacji w Azure Portal:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania aplikacji magazynu w usłudze Azure AD":::

> [!NOTE]
> Jeśli aplikacja jest zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI dla **identyfikatora URI przekierowania** . W przypadku aplikacji natywnych ta wartość nie musi być rzeczywistym adresem URL. W przypadku aplikacji sieci Web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, na który są udostępniane tokeny.

Po zarejestrowaniu aplikacji zobaczysz identyfikator aplikacji (lub identyfikator klienta) w obszarze **Ustawienia** :

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania aplikacji magazynu w usłudze Azure AD":::

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-register-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Przyznanie zarejestrowanej aplikacji uprawnień do usługi Azure Storage

Następnie Udziel uprawnień aplikacji do wywoływania interfejsów API usługi Azure Storage. Ten krok umożliwia aplikacji autoryzowanie żądań do usługi Azure Storage za pomocą usługi Azure AD.

1. Na stronie **uprawnienia interfejsu API** dla zarejestrowanej aplikacji wybierz pozycję **Dodaj uprawnienie** .
1. Na karcie **interfejsy API firmy Microsoft** wybierz pozycję **Azure Storage** .
1. W okienku **uprawnienia do interfejsu API żądania** w obszarze **jakiego typu uprawnienia aplikacja jest wymagana?** Zwróć uwagę na to, że dostępnym typem uprawnień jest **delegowane uprawnienia** . Ta opcja jest domyślnie wybrana.
1. W obszarze **uprawnienia** zaznacz pole wyboru obok pozycji **user_impersonation** , a następnie wybierz przycisk **Dodaj uprawnienia** .

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania aplikacji magazynu w usłudze Azure AD":::

1. Następnie udziel zgody administratora na te uprawnienia, klikając pozycję **Udziel zgody administratora dla katalogu domyślnego** .

W okienku **uprawnienia interfejsu API** widać, że zarejestrowana aplikacja usługi Azure AD ma dostęp zarówno do Microsoft Graph, jak i interfejsów API usługi Azure Storage, a ta zgoda jest przyznawana dla katalogu domyślnego. Uprawnienia są udzielane Microsoft Graph automatycznie po pierwszym zarejestrowaniu aplikacji w usłudze Azure AD.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania aplikacji magazynu w usłudze Azure AD":::

### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Aplikacja wymaga klucza tajnego klienta, aby potwierdzić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w Azure Portal.
1. Wybierz ustawienie **certyfikaty & wpisy tajne** .
1. W obszarze wpisy **tajne klienta** kliknij pozycję **Nowy klient klucz tajny** , aby utworzyć nowy wpis tajny.
1. Podaj opis wpisu tajnego i wybierz żądany interwał ważności.
1. Natychmiast skopiuj wartość nowego wpisu tajnego do bezpiecznej lokalizacji. Pełna wartość jest wyświetlana tylko raz.

    ![Zrzut ekranu przedstawiający klucz tajny klienta](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Włącz przepływ niejawnego udzielenia

Następnie skonfiguruj niejawny przepływ dla aplikacji. Wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w Azure Portal.
1. W sekcji **Zarządzanie** wybierz ustawienie **uwierzytelnianie** .
1. W sekcji **niejawne przyznanie** zaznacz pole wyboru, aby włączyć tokeny identyfikatora, jak pokazano na poniższej ilustracji:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Zrzut ekranu przedstawiający sposób rejestrowania aplikacji magazynu w usłudze Azure AD":::

## <a name="client-libraries-for-token-acquisition"></a>Biblioteki klienckie do pozyskiwania tokenów

Po zarejestrowaniu aplikacji i udzieleniu uprawnień do uzyskiwania dostępu do danych w usłudze Azure Blob Storage lub queue storage możesz dodać kod do aplikacji, aby uwierzytelnić podmiot zabezpieczeń i uzyskać token OAuth 2,0. Aby uwierzytelnić i uzyskać token, można użyć jednej z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](../../active-directory/develop/reference-v2-libraries.md) lub innej biblioteki typu open source, która obsługuje openid connect Connect 1,0. Aplikacja może następnie użyć tokenu dostępu do autoryzowania żądania do magazynu obiektów blob platformy Azure lub magazynu kolejek.

Aby zapoznać się z listą scenariuszy, w których są obsługiwane uzyskiwanie tokenów, zobacz sekcję [przepływy uwierzytelniania](../../active-directory/develop/msal-authentication-flows.md) w dokumentacji [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Dobrze znane wartości uwierzytelniania przy użyciu usługi Azure AD

Aby uwierzytelnić podmiot zabezpieczeń w usłudze Azure AD, musisz uwzględnić w kodzie pewne dobrze znane wartości.

### <a name="azure-ad-authority"></a>Urząd usługi Azure AD

W przypadku chmury publicznej firmy Microsoft podstawowy urząd usługi Azure AD jest następujący, gdzie *dzierżawca* to identyfikator dzierżawy Active Directory (lub identyfikator katalogu):

`https://login.microsoftonline.com/<tenant-id>/`

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD do użycia na potrzeby uwierzytelniania. Jest również określany jako identyfikator katalogu. Aby pobrać identyfikator dzierżawy, przejdź do strony **Przegląd** rejestracji aplikacji w Azure Portal i skopiuj wartość z tego miejsca.

### <a name="azure-storage-resource-id"></a>Identyfikator zasobu usługi Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu platformy .NET: Tworzenie blokowego obiektu BLOB

W przykładzie kodu pokazano, jak uzyskać token dostępu z usługi Azure AD. Token dostępu służy do uwierzytelniania określonego użytkownika, a następnie autoryzowania żądania utworzenia blokowego obiektu BLOB. Aby uzyskać ten przykład pracy, należy najpierw wykonać czynności opisane w poprzednich sekcjach.

Aby zażądać tokenu, potrzebne są następujące wartości z rejestracji aplikacji:

- Nazwa domeny usługi Azure AD. Pobierz tę wartość ze strony **przegląd** Azure Active Directory.
- Identyfikator dzierżawy (lub katalogu). Pobierz tę wartość ze strony **Przegląd** rejestracji aplikacji.
- Identyfikator klienta (lub aplikacji). Pobierz tę wartość ze strony **Przegląd** rejestracji aplikacji.
- Identyfikator URI przekierowania klienta. Pobierz tę wartość z ustawień **uwierzytelniania** dla rejestracji aplikacji.
- Wartość klucza tajnego klienta. Pobierz tę wartość z lokalizacji, do której została wcześniej skopiowana.

### <a name="create-a-storage-account-and-container"></a>Tworzenie konta magazynu i kontenera

Aby uruchomić przykładowy kod, Utwórz konto magazynu w ramach tej samej subskrypcji co Azure Active Directory. Następnie utwórz kontener w ramach tego konta magazynu. Przykładowy kod spowoduje utworzenie blokowego obiektu BLOB w tym kontenerze.

Następnie jawnie Przypisz rolę **współautor danych obiektu blob magazynu** do konta użytkownika, w ramach którego zostanie uruchomiony przykładowy kod. Aby uzyskać instrukcje dotyczące sposobu przypisywania tej roli w Azure Portal, zobacz [Korzystanie z Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Podczas tworzenia konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych za pośrednictwem usługi Azure AD. Musisz jawnie przypisać sobie rolę platformy Azure dla usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera lub kolejki.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Tworzenie aplikacji sieci Web, która autoryzuje dostęp do usługi BLOB Storage w usłudze Azure AD

Gdy aplikacja uzyskuje dostęp do usługi Azure Storage, odbywa się to w imieniu użytkownika, co oznacza, że dostęp do zasobów obiektów blob lub kolejki uzyskuje się za pomocą uprawnień użytkownika, który jest zalogowany. Aby wypróbować ten przykład kodu, potrzebna jest aplikacja sieci Web, która poprosi użytkownika o zalogowanie się przy użyciu tożsamości usługi Azure AD. Możesz utworzyć własne lub użyć przykładowej aplikacji udostępnionej przez firmę Microsoft.

Zakończona przykładową aplikacją sieci Web, która uzyskuje token i używa jej do tworzenia obiektów BLOB w usłudze Azure Storage, jest dostępna w serwisie [GitHub](https://aka.ms/aadstorage). Przeglądanie i uruchamianie wykonanego przykładu może być pomocne w zrozumieniu przykładów kodu. Aby uzyskać instrukcje dotyczące sposobu uruchamiania kompletnego przykładu, zobacz sekcję zatytułowaną [Wyświetlanie i uruchamianie ukończonego przykładu](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Dodaj odwołania i instrukcje using  

W programie Visual Studio Zainstaluj bibliotekę klienta usługi Azure Storage. Z menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet** , a następnie pozycję **Konsola menedżera pakietów** . Wpisz następujące polecenia w oknie konsoli, aby zainstalować wymagane pakiety z biblioteki klienta usługi Azure Storage dla platformy .NET:

# <a name="net-v12-sdk"></a>[Zestaw SDK .NET V12](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Następnie Dodaj następujące instrukcje using do pliku HomeController.cs:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[Zestaw SDK .NET v11](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Następnie Dodaj następujące instrukcje using do pliku HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Tworzenie blokowego obiektu BLOB

Dodaj następujący fragment kodu, aby utworzyć blokowy obiekt BLOB. Pamiętaj, aby zamienić wartości w nawiasy kątowe własnymi wartościami:

# <a name="net-v12-sdk"></a>[Zestaw SDK .NET V12](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[Zestaw SDK .NET v11](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Aby autoryzować operacje obiektów blob i kolejek przy użyciu tokenu OAuth 2,0, należy użyć protokołu HTTPS.

W powyższym przykładzie Biblioteka klienta .NET obsługuje autoryzację żądania, aby utworzyć blokowy obiekt BLOB. Biblioteki klienckie usługi Azure Storage dla innych języków również obsługują autoryzację żądania. Jeśli jednak wywołujesz operację usługi Azure Storage z tokenem OAuth przy użyciu interfejsu API REST, musisz utworzyć nagłówek **autoryzacji** przy użyciu tokenu OAuth.

Aby wywoływać operacje obiektów blob i usługa kolejki przy użyciu tokenów dostępu OAuth, należy przekazać token dostępu do nagłówka **autoryzacji** za pomocą schematu **Bearer** i określić wersję usługi 2017-11-09 lub wyższą, jak pokazano w następującym przykładzie:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Uzyskiwanie tokenu dostępu z usługi Azure AD

Następnie Dodaj metodę, która żąda tokenu z usługi Azure AD w imieniu użytkownika. Ta metoda określa zakres, dla którego mają zostać przyznane uprawnienia. Aby uzyskać więcej informacji o uprawnieniach i zakresach, zobacz [uprawnienia i wyrażanie zgody w punkcie końcowym platformy tożsamości firmy Microsoft](../../active-directory/develop/v2-permissions-and-consent.md).

Użyj identyfikatora zasobu, aby skonstruować zakres, dla którego ma zostać pozyskany token. Przykład konstruuje zakres przy użyciu identyfikatora zasobu wraz z wbudowanym `user_impersonation` zakresem, który wskazuje, że token jest żądany w imieniu użytkownika.

Należy pamiętać, że może być konieczne zaprezentowanie użytkownikowi interfejsu, który umożliwia użytkownikowi zgodę na zażądanie tokenu w ich imieniu:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Zgoda polega na tym, że użytkownik udzielający autoryzacji aplikacji dostępu do chronionych zasobów w ich imieniu. Platforma tożsamości firmy Microsoft obsługuje przyrostową zgodę, co oznacza, że podmiot zabezpieczeń może początkowo zażądać minimalnego zestawu uprawnień i w razie potrzeby dodać uprawnienia w miarę upływu czasu. Gdy kod żąda tokenu dostępu, określ zakres uprawnień wymaganych przez aplikację. Aby uzyskać więcej informacji na temat łącznej zgody, zobacz [przyrostowe i dynamiczne wyrażanie zgody](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>Wyświetl i uruchom ukończony przykład

Aby uruchomić przykładową aplikację, należy najpierw sklonować ją lub pobrać z witryny [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Następnie zaktualizuj aplikację zgodnie z opisem w poniższych sekcjach.

### <a name="provide-values-in-the-settings-file"></a>Podaj wartości w pliku ustawień

Zaktualizuj *appsettings.jsw* pliku przy użyciu własnych wartości w następujący sposób:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>"
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Aktualizowanie konta magazynu i nazwy kontenera

W pliku *HomeController.cs* zaktualizuj identyfikator URI, który odwołuje się do blokowego obiektu BLOB, aby użyć nazwy konta magazynu i kontenera, zastępując wartości w nawiasach ostrych własnymi wartościami:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Następne kroki

- [Platforma tożsamości firmy Microsoft](../../active-directory/develop/index.yml)
- [Zarządzanie prawami dostępu do danych magazynu za pomocą usługi Azure RBAC](./storage-auth-aad-rbac-portal.md)
- [Uwierzytelnianie dostępu do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](storage-auth-aad-msi.md)