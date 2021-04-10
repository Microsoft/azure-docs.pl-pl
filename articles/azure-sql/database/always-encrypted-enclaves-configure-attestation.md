---
title: Konfigurowanie zaświadczania platformy Azure dla serwera logicznego usługi Azure SQL
description: Skonfiguruj zaświadczanie platformy Azure dla Always Encrypted przy użyciu bezpiecznego enclaves w programie Azure SQL Database.
keywords: Szyfruj dane, szyfrowanie SQL, szyfrowanie bazy danych, dane poufne, Always Encrypted, Secure enclaves, SGX, zaświadczanie
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: a51aa15e1338380d4b4179e7fb8899273750c374
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107184"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Konfigurowanie zaświadczania platformy Azure dla serwera logicznego usługi Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted z opcją Secure enclaves for Azure SQL Database jest obecnie w **publicznej wersji zapoznawczej**.

[Zaświadczanie Microsoft Azure](../../attestation/overview.md) to rozwiązanie służące do zaświadczania zaufanych środowisk wykonywania (TEEs), w tym rozszerzeń firmy Intel do zabezpieczeń (Intel SGX) enclaves. 

Aby skorzystać z zaświadczania platformy Azure na potrzeby zaświadczania technologii Intel SGX enclaves używanej do [Always Encrypted z zabezpieczeniami enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) w Azure SQL Database, należy wykonać następujące:

1. Utwórz [dostawcę zaświadczania](../../attestation/basic-concepts.md#attestation-provider) i skonfiguruj go przy użyciu zalecanych zasad zaświadczania.

2. Przyznaj serwerowi logicznemu usługi Azure SQL dostęp do swojego dostawcy zaświadczania.

> [!NOTE]
> Konfigurowanie zaświadczania jest odpowiedzialne za administratora zaświadczania. Zobacz [role i obowiązki podczas KONFIGUROWANIA SGX enclaves i zaświadczania](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Wymagania

Serwer logiczny usługi Azure SQL i dostawca zaświadczania muszą należeć do tej samej dzierżawy Azure Active Directory. Interakcje między dzierżawcami nie są obsługiwane. 

Serwer logiczny usługi Azure SQL musi mieć przypisaną tożsamość usługi Azure AD. Jako administrator zaświadczania musisz uzyskać tożsamość serwera usługi Azure AD z poziomu administratora Azure SQL Database tego serwera. Tożsamość zostanie użyta w celu udzielenia serwerowi dostępu do dostawcy zaświadczania. 

Aby uzyskać instrukcje dotyczące sposobu tworzenia serwera z tożsamością lub przypisywania tożsamości do istniejącego serwera przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure, zobacz [przypisywanie tożsamości usługi Azure AD do serwera](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server).

## <a name="create-and-configure-an-attestation-provider"></a>Tworzenie i Konfigurowanie dostawcy zaświadczania

[Dostawca zaświadczania](../../attestation/basic-concepts.md#attestation-provider) jest zasobem zaświadczania platformy Azure, który szacuje [żądania](../../attestation/basic-concepts.md#attestation-request) zaświadczania o [zasady](../../attestation/basic-concepts.md#attestation-request) zaświadczania i wystawia [tokeny zaświadczania](../../attestation/basic-concepts.md#attestation-token). 

Zasady zaświadczania są określane przy użyciu [gramatyki reguł dotyczących roszczeń](../../attestation/claim-rule-grammar.md).

Firma Microsoft zaleca następujące zasady zaświadczania firmy Intel SGX enclaves używanej do Always Encrypted w Azure SQL Database:

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

Powyższe zasady sprawdzają:

- Enklawy wewnątrz Azure SQL Database nie obsługuje debugowania. 
  > Enclaves można załadować z wyłączonym lub włączonym debugowaniem. Obsługa debugowania została zaprojektowana tak, aby umożliwić deweloperom Rozwiązywanie problemów z kodem uruchomionym w enklawy. W systemie produkcyjnym debugowanie może umożliwić administratorowi badanie zawartości enklawy, co zmniejsza poziom ochrony zapewniany przez enklawy. Zalecane zasady uniemożliwiają debugowanie, aby upewnić się, że jeśli złośliwy administrator podejmie próbę włączenia obsługi debugowania przez przejęcie maszyny enklawy, zaświadczanie zakończy się niepowodzeniem. 
- Identyfikator produktu enklawy jest zgodny z IDENTYFIKATORem produktu przypisanym do Always Encrypted za pomocą bezpiecznego enclaves.
  > Każdy enklawy ma unikatowy identyfikator produktu, który odróżnia enklawy od innych enclaves. Identyfikator produktu przypisany do Always Encrypted enklawy to 4639. 
- Numer wersji zabezpieczeń (SVN) biblioteki jest większy niż 0.
  > System SVN umożliwia firmie Microsoft reagowanie na potencjalne usterki dotyczące zabezpieczeń zidentyfikowane w kodzie enklawy. Jeśli problem z zabezpieczeniami został rozkryty i rozwiązany, firma Microsoft wdroży nową wersję enklawy z nowym (kolejnym) systemem SVN. Powyższe zalecane zasady zostaną zaktualizowane w celu odzwierciedlenia nowego SVN. Aktualizując zasady zgodnie z zalecanymi zasadami, można upewnić się, że jeśli złośliwy administrator próbuje załadować starsze i niezabezpieczone enklawy, zaświadczanie zakończy się niepowodzeniem.
- Biblioteka w enklawy została podpisana przy użyciu klucza podpisywania firmy Microsoft (wartość żądania x-MS-SGX-mrsigner jest skrótem klucza podpisywania).
  > Jednym z głównych celów zaświadczania jest przekonanie klientów, że plik binarny działający w enklawy to plik binarny, który powinien zostać uruchomiony. Zasady zaświadczania zapewniają dwa mechanizmy do tego celu. Jednym z nich jest **mrenclave** , który jest skrótem pliku binarnego, który ma być uruchamiany w enklawy. Problem z **mrenclave** polega na tym, że skrót binarny zmienia się nawet przy użyciu prostych zmian w kodzie, co sprawia, że jest to trudne w przypadku kodu działającego w enklawy. W związku z tym zaleca się użycie **mrsigner**, który jest skrótem klucza, który jest używany do podpisywania danych binarnych enklawy. Gdy firma Microsoft Revs enklawy, **mrsigner** pozostaje taka sama, o ile klucz podpisywania nie zostanie zmieniony. W ten sposób będzie możliwe wdrożenie zaktualizowanych plików binarnych bez przerywania aplikacji klientów. 

> [!IMPORTANT]
> Dostawca zaświadczania zostanie utworzony przy użyciu domyślnych zasad dla Intel SGX enclaves, który nie sprawdza poprawności kodu działającego wewnątrz enklawy. Firma Microsoft zdecydowanie zaleca ustawienie powyższych zalecanych zasad i nieużywanie zasad domyślnych dla Always Encrypted z bezpiecznym enclaves.

Instrukcje dotyczące sposobu tworzenia dostawcy zaświadczania i konfigurowania za pomocą zasad zaświadczania przy użyciu:

- [Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą Azure Portal](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Konfigurując zasady zaświadczania przy użyciu Azure Portal, należy ustawić typ zaświadczania na `SGX-IntelSDK` .
- [Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Konfigurując zasady zaświadczania przy użyciu Azure PowerShell, ustaw `Tee` parametr na `SgxEnclave` .
- [Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Konfigurując zasady zaświadczania przy użyciu interfejsu wiersza polecenia platformy Azure, ustaw `attestation-type` parametr na `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Określanie adresu URL zaświadczania dla zasad zaświadczania

Po skonfigurowaniu zasad zaświadczania należy udostępnić adres URL zaświadczania, odwołujący się do zasad, administratorów aplikacji, które używają Always Encrypted z bezpiecznym enclaves w Azure SQL Database. Administratorzy aplikacji lub Użytkownicy aplikacji będą musieli skonfigurować swoje aplikacje za pomocą adresu URL zaświadczania, aby mogli uruchamiać instrukcje korzystające z bezpiecznego enclaves.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Określanie adresu URL zaświadczania przy użyciu programu PowerShell

Użyj następującego skryptu, aby określić adres URL zaświadczania:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + "/attest/SgxEnclave"
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Użyj Azure Portal, aby określić adres URL zaświadczania

1. W okienku Przegląd dla dostawcy zaświadczania skopiuj wartość właściwości zaświadcz URI do Schowka. Identyfikator URI zaświadczania powinien wyglądać następująco: `https://MyAttestationProvider.us.attest.azure.net` .

2. Dołącz następujący do identyfikatora URI zaświadczania: `/attest/SgxEnclave` . 

Adres URL wynikający z zaświadczania powinien wyglądać następująco: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Przyznaj serwerowi logicznemu usługi Azure SQL dostęp do swojego dostawcy zaświadczania

Podczas przepływu pracy zaświadczania serwer logiczny usługi Azure SQL zawierający bazę danych wywołuje dostawcę zaświadczania o przesłanie żądania zaświadczania. Aby serwer logiczny usługi Azure SQL mógł przesyłać żądania zaświadczania, serwer musi mieć uprawnienie do `Microsoft.Attestation/attestationProviders/attestation/read` działania w ramach dostawcy zaświadczania. Zalecanym sposobem udzielenia uprawnień jest administrator dostawcy zaświadczania, aby przypisać tożsamość usługi Azure AD serwera do roli czytelnik zaświadczania dla dostawcy zaświadczania lub zawierającej grupę zasobów.

### <a name="use-azure-portal-to-assign-permission"></a>Użyj Azure Portal, aby przypisać uprawnienia

Aby przypisać tożsamość serwera SQL platformy Azure do roli czytelnik zaświadczania dla dostawcy zaświadczania, postępuj zgodnie z ogólnymi instrukcjami zawartymi w temacie [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md). Gdy jesteś w okienku **Dodaj przypisanie roli** :

1. Z listy rozwijanej **rola** wybierz rolę **czytelnik zaświadczania** .
1. W polu **Wybierz** wprowadź nazwę serwera SQL Azure, w którym ma zostać wyszukane.

Przykład można znaleźć na poniższym zrzucie ekranu.

![przypisanie roli czytnika zaświadczania](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Aby serwer był wyświetlany w okienku **Dodaj przypisanie roli** , serwer musi mieć przypisaną tożsamość usługi Azure AD — Zobacz [wymagania](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Przypisywanie uprawnień przy użyciu programu PowerShell

1. Znajdź serwer logiczny usługi Azure SQL.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
```
 
2. Przypisz serwer do roli czytelnik zaświadczania dla grupy zasobów zawierającej dostawcę zaświadczania.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples).

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie kluczami dla funkcji Always Encrypted z bezpiecznymi enklawami](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Zobacz też

- [Samouczek: wprowadzenie do Always Encrypted za pomocą bezpiecznego enclaves w Azure SQL Database](always-encrypted-enclaves-getting-started.md)
