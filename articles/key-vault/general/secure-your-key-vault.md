---
title: Bezpieczny dostęp do magazynu kluczy — Azure Key Vault | Microsoft Docs
description: Zarządzaj uprawnieniami dostępu do Azure Key Vault, kluczy i wpisów tajnych. Obejmuje model uwierzytelniania i autoryzacji dla Key Vault oraz sposób zabezpieczania magazynu kluczy.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 348ddb0fa8bd973a7e8ebcf5ae14de1eee57d5a5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827526"
---
# <a name="secure-access-to-a-key-vault"></a>Bezpieczny dostęp do magazynu kluczy

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. Ponieważ te dane są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko na autoryzowane aplikacje i użytkowników. Ten artykuł zawiera omówienie modelu dostępu Key Vault. W tym artykule wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Przegląd modelu dostępu

Dostęp do magazynu kluczy jest kontrolowany przez dwa interfejsy: **płaszczyzny zarządzania** i **płaszczyzny danych**. Płaszczyzny zarządzania to miejsce, w którym zarządza się Key Vault. Operacje na tej płaszczyźnie obejmują tworzenie i usuwanie magazynów kluczy, pobieranie Key Vault właściwości i aktualizowanie zasad dostępu. Płaszczyzna danych to miejsce, w którym można korzystać z danych przechowywanych w magazynie kluczy. Możesz dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Aby uzyskać dostęp do magazynu kluczy w jednej z płaszczyzn, wszyscy wywołujący (Użytkownicy lub aplikacje) muszą mieć odpowiednie uwierzytelnianie i autoryzację. Uwierzytelnianie ustanawia tożsamość obiektu wywołującego. Autoryzacja określa, które operacje mogą zostać wykonane przez obiekt wywołujący.

Obie płaszczyzny używają Azure Active Directory (Azure AD) do uwierzytelniania. W przypadku autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), a płaszczyzna danych używa zasad dostępu Key Vault.

## <a name="active-directory-authentication"></a>Uwierzytelnianie Active Directory

Podczas tworzenia magazynu kluczy w ramach subskrypcji platformy Azure jest on automatycznie kojarzony z dzierżawą usługi Azure AD subskrypcji. Wszystkie obiekty wywołujące w obu płaszczyznach muszą rejestrować się w tej dzierżawie i uwierzytelniać się w celu uzyskania dostępu do magazynu kluczy. W obu przypadkach aplikacje mogą uzyskiwać dostęp do Key Vault na dwa sposoby:

- **Użytkownik i dostęp do aplikacji**: aplikacja uzyskuje dostęp do Key Vault w imieniu zalogowanego użytkownika. Przykłady tego typu dostępu obejmują Azure PowerShell i Azure Portal. Dostęp użytkowników odbywa się na dwa sposoby. Użytkownicy mogą uzyskiwać dostęp do Key Vault z dowolnej aplikacji lub muszą korzystać z określonej aplikacji (zwanej _tożsamością złożoną_).
- **Dostęp tylko do aplikacji**: aplikacja działa jako usługa demona lub zadanie w tle. Tożsamość aplikacji uzyskuje dostęp do magazynu kluczy.

W przypadku obu typów dostępu aplikacja uwierzytelnia się za pomocą usługi Azure AD. Aplikacja używa dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-scenarios.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu na płaszczyźnie, aby udzielić dostępu. Zasób jest punktem końcowym w obszarze zarządzania lub płaszczyzny danych w oparciu o środowisko platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do Key Vault. Aby dowiedzieć się więcej, zapoznaj się z [całym przepływem uwierzytelniania](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Model jednego mechanizmu uwierzytelniania w obu płaszczyznach ma kilka zalet:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich magazynów kluczy w organizacji.
- Jeśli użytkownik opuści te osoby, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie przy użyciu opcji w usłudze Azure AD, takich jak włączenie uwierzytelniania wieloskładnikowego w celu zwiększenia bezpieczeństwa.

## <a name="resource-endpoints"></a>Punkty końcowe zasobów

Aplikacje uzyskują dostęp do płaszczyzn za pomocą punktów końcowych. Kontrole dostępu dla dwóch płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do korzystania z kluczy w magazynie kluczy, przyznano dostęp do płaszczyzny danych przy użyciu zasad dostępu Key Vault. Aby udzielić użytkownikowi dostępu do odczytu do Key Vault właściwości i tagów, ale nie dostępu do danych (kluczy, wpisów tajnych lub certyfikatów), Udziel dostępu do płaszczyzny zarządzania za pomocą RBAC.

W poniższej tabeli przedstawiono punkty końcowe dla punktów zarządzania i płaszczyzny danych.

| &nbsp;Płaszczyzna dostępu | Punkty końcowe dostępu | Operacje | &nbsp;Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne**<br> management.azure.com:443<br><br> **Azure Chiny 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Administracja USA platformy Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> management.microsoftazure.de:443 | Tworzenie, odczytywanie, aktualizowanie i usuwanie magazynów kluczy<br><br>Ustawianie zasad dostępu Key Vault<br><br>Ustawianie tagów Key Vault | Azure Resource Manager RBAC |
| Płaszczyzna danych | **Globalne**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Azure Chiny 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Administracja USA platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 | Klucze: Odszyfruj, Szyfruj,<br> odpakowywanie, zawijanie, weryfikowanie, podpisywanie,<br> Pobieranie, wyświetlanie, aktualizowanie, tworzenie,<br> Importuj, Usuń, Utwórz kopię zapasową, Przywróć<br><br> Wpisy tajne: pobieranie, wyświetlanie, Ustawianie, usuwanie | Zasady dostępu Key Vault |

## <a name="management-plane-and-rbac"></a>Płaszczyzna zarządzania i kontrola RBAC

Na płaszczyźnie zarządzania należy używać RBAC (Access Control oparte na rolach) do autoryzacji operacji, które może wykonać obiekt wywołujący. W modelu RBAC Każda subskrypcja platformy Azure ma wystąpienie usługi Azure AD. Przyznasz użytkownikom, grupom i aplikacjom dostęp do tego katalogu. Dostęp jest udzielany do zarządzania zasobami w ramach subskrypcji platformy Azure, która używa modelu wdrażania Azure Resource Manager. Aby udzielić dostępu, użyj [Azure Portal](https://portal.azure.com/), [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsów API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Utwórz magazyn kluczy w grupie zasobów i Zarządzaj dostępem za pomocą usługi Azure AD. Użytkownicy lub grupy mogą zarządzać magazynami kluczy w grupie zasobów. Przyznanie dostępu do określonego poziomu zakresu przez przypisanie odpowiednich ról RBAC. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną `key vault Contributor` rolę do użytkownika w określonym zakresie. Następujące poziomy zakresów można przypisać do roli RBAC:

- **Subskrypcja**: rola RBAC przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów**: rola RBAC przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób**: dla danego zasobu jest stosowana rola RBAC przypisana do określonego zasobu. W tym przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie spełnia Twoich potrzeb, możesz zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [RBAC: role wbudowane](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Jeśli użytkownik ma `Contributor` uprawnienia do płaszczyzny zarządzania magazynu kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu Key Vault. Należy ściśle kontrolować, kto ma `Contributor` dostęp do roli do Twoich magazynów kluczy. Upewnij się, że tylko autoryzowani osoby mają dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz nimi zarządzać.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Zasady dostępu i płaszczyzny danych

Przyznanie dostępu do płaszczyzny danych przez ustawienie Key Vault zasad dostępu dla magazynu kluczy. Aby ustawić te zasady dostępu, użytkownik, Grupa lub aplikacja musi mieć `Contributor` uprawnienia do płaszczyzny zarządzania dla tego magazynu kluczy.

Przyznanie użytkownikowi, grupie lub aplikacji dostępu do wykonywania określonych operacji dla kluczy lub wpisów tajnych w magazynie kluczy. Key Vault obsługuje wpisy zasad dostępu do 1 024 dla magazynu kluczy. Aby udzielić dostępu do płaszczyzny danych kilku użytkownikom, należy utworzyć grupę zabezpieczeń usługi Azure AD i dodać użytkowników do tej grupy.

<a id="key-vault-access-policies"></a>Key Vault zasady dostępu udzielają uprawnień oddzielnie do kluczy, wpisów tajnych i certyfikatów. Można udzielić użytkownikowi dostępu tylko do kluczy i nie do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów znajdują się na poziomie magazynu. Zasady dostępu Key Vault nie obsługują szczegółowych uprawnień na poziomie obiektów, takich jak określony klucz, klucz tajny lub certyfikat. Aby ustawić zasady dostępu dla magazynu kluczy, użyj [Azure Portal](https://portal.azure.com/), [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsów API REST zarządzania Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zasady dostępu Key Vault są stosowane na poziomie magazynu. Gdy użytkownik uzyskuje uprawnienia do tworzenia i usuwania kluczy, mogą wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.
>

Dostęp do płaszczyzny danych można ograniczyć za pomocą [punktów końcowych usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md)). [Zapory i reguły sieci wirtualnej](network-security.md) można skonfigurować w celu uzyskania dodatkowej warstwy zabezpieczeń.

## <a name="example"></a>Przykład

W tym przykładzie tworzymy aplikację, która używa certyfikatu dla protokołów TLS/SSL, usługi Azure Storage do przechowywania danych oraz klucza RSA 2 048-bitowego dla operacji podpisywania. Nasza aplikacja działa na maszynie wirtualnej platformy Azure (lub w zestawie skalowania maszyn wirtualnych). Możemy użyć magazynu kluczy do przechowywania wpisów tajnych aplikacji. Firma Microsoft może przechowywać certyfikat Bootstrap używany przez aplikację do uwierzytelniania w usłudze Azure AD.

Potrzebujemy dostępu do następujących przechowywanych kluczy i wpisów tajnych:
- **Certyfikat TLS/SSL**: używany w przypadku protokołu TLS/SSL.
- **Klucz magazynu**: służy do uzyskiwania dostępu do konta magazynu.
- **Klucz RSA 2 048-bitowy**: służy do podpisywania operacji.
- **Certyfikat Bootstrap**: używany do uwierzytelniania w usłudze Azure AD. Po udzieleniu dostępu można pobrać klucz magazynu i użyć klucza RSA do podpisywania.

Musimy zdefiniować następujące role, aby określić, kto może zarządzać aplikacją, wdrażać ją i przeprowadzać inspekcję:
- **Zespół ds. zabezpieczeń**: personel działu IT z biura firmy CSO (Dyrektor ds. zabezpieczeń) lub podobnych współautorów. Zespół ds. zabezpieczeń jest odpowiedzialny za odpowiednie przechowywanie wpisów tajnych. Wpisy tajne mogą obejmować certyfikaty protokołu TLS/SSL, klucze RSA do podpisywania, parametry połączenia i klucze kont magazynu.
- **Deweloperzy i operatorzy**: pracownicy, którzy opracowują aplikację i wdrażają ją na platformie Azure. Członkowie tego zespołu nie są częścią personelu zabezpieczeń. Nie powinny mieć dostępu do poufnych danych, takich jak certyfikaty TLS/SSL i klucze RSA. Tylko wdrażana aplikacja powinna mieć dostęp do poufnych danych.
- **Audytorzy**: Ta rola jest dla współautorów, którzy nie są członkami rozwoju ani ogólnego personelu IT. Zapoznają się z użyciem i konserwacją certyfikatów, kluczy i wpisów tajnych w celu zapewnienia zgodności ze standardami zabezpieczeń.

Istnieje inna rola, która jest poza zakresem naszej aplikacji: Administrator subskrypcji (lub grupy zasobów). Administrator subskrypcji konfiguruje uprawnienia dostępu początkowego do zespołu ds. zabezpieczeń. Przyznają one dostęp do zespołu ds. zabezpieczeń przy użyciu grupy zasobów, która zawiera zasoby wymagane przez aplikację.

Musimy autoryzować następujące operacje dla naszych ról:

**Zespół ds. zabezpieczeń**
- Utwórz magazyny kluczy.
- Włącz rejestrowanie Key Vault.
- Dodaj klucze i wpisy tajne.
- Tworzenie kopii zapasowych kluczy na potrzeby odzyskiwania po awarii.
- Ustaw zasady dostępu Key Vault, aby przyznać uprawnienia użytkownikom i aplikacjom dla określonych operacji.
- Okresowe przywracanie kluczy i wpisów tajnych.

**Deweloperzy i operatorzy**
- Uzyskaj odwołania od zespołu ds. zabezpieczeń dla certyfikatów Bootstrap i TLS/SSL (odciski palców), klucza magazynu (Secret URI) i klucza RSA (identyfikator URI klucza) do podpisywania.
- Programowe tworzenie i wdrażanie aplikacji w celu programowanego dostępu do kluczy i wpisów tajnych.

**Audytorzy**
- Zapoznaj się z dziennikami Key Vault, aby potwierdzić odpowiednie użycie kluczy i wpisów tajnych oraz zapewnić zgodność ze standardami zabezpieczeń danych.

Poniższa tabela zawiera podsumowanie uprawnień dostępu dla naszych ról i aplikacji.

| Rola | Uprawnienia do płaszczyzny zarządzania | Uprawnienia do płaszczyzny danych |
| --- | --- | --- |
| Zespół ds. zabezpieczeń | Współautor Key Vault | Klucze: wykonywanie kopii zapasowej, tworzenie, usuwanie, pobieranie, importowanie, wyświetlanie, przywracanie<br>Wpisy tajne: wszystkie operacje |
| Deweloperzy i &nbsp; operatorzy | Uprawnienie do wdrażania Key Vault<br><br> **Uwaga**: to uprawnienie umożliwia wdrożonym maszynom wirtualnym pobieranie wpisów tajnych z magazynu kluczy. | Brak |
| Audytorzy | Brak | Klucze: wyświetlanie<br>Wpisy tajne: wyświetlanie<br><br> **Uwaga**: to uprawnienie umożliwia audytorom inspekcję atrybutów (tagów, dat aktywacji, dat wygaśnięcia) dla kluczy i wpisów tajnych, które nie są emitowane w dziennikach. |
| Aplikacja | Brak | Klucze: podpisywanie<br>Wpisy tajne: pobieranie |

Trzy role zespołu potrzebują dostępu do innych zasobów wraz z uprawnieniami Key Vault. Aby wdrożyć maszyny wirtualne (lub Web Apps funkcję Azure App Service), deweloperzy i operatorzy potrzebują `Contributor` dostępu do tych typów zasobów. Audytorzy muszą mieć dostęp do odczytu do konta magazynu, w którym są przechowywane dzienniki Key Vault.

Aby uzyskać więcej informacji na temat sposobu wdrażania certyfikatów, kluczy dostępu i wpisów tajnych, zobacz następujące zasoby:
- Dowiedz się, jak [wdrażać certyfikaty na maszynach wirtualnych z magazynu kluczy zarządzanego przez klienta](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (wpis w blogu).
- Wyświetl [przykłady klienta Azure Key Vault](https://docs.microsoft.com/samples/browse/?term=Key%20Vault). Ta zawartość pokazuje, jak używać certyfikatu Bootstrap do uwierzytelniania w usłudze Azure AD w celu uzyskania dostępu do magazynu kluczy.

Większość uprawnień dostępu można udzielić przy użyciu Azure Portal. Aby udzielić szczegółowych uprawnień, możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Fragmenty kodu programu PowerShell w tej sekcji zostały skompilowane przy użyciu następujących założeń:
- Administrator usługi Azure AD utworzył grupy zabezpieczeń, aby reprezentować trzy role: zespół zabezpieczeń contoso, DevOps aplikacji Contoso i audytorów aplikacji firmy Contoso. Administrator dodał użytkowników do odpowiednich grup.
- Wszystkie zasoby znajdują się w grupie zasobów **ContosoAppRG** .
- Dzienniki Key Vault są przechowywane na koncie magazynu **contosologstorage** .
- Magazyn kluczy **ContosoKeyVault** i konto magazynu **contosologstorage** znajdują się w tej samej lokalizacji platformy Azure.

Administrator subskrypcji przypisuje `key vault Contributor` role i `User Access Administrator` do zespołu ds. zabezpieczeń. Te role umożliwiają zespołowi ds. zabezpieczeń Zarządzanie dostępem do innych zasobów i magazynów kluczy, z których oba należą do grupy zasobów **ContosoAppRG** .

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Zespół ds. zabezpieczeń tworzy magazyn kluczy i konfiguruje uprawnienia do rejestrowania i dostępu.

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Nasze zdefiniowane role niestandardowe można przypisać tylko do subskrypcji, w której jest tworzona Grupa zasobów **ContosoAppRG** . Aby użyć roli niestandardowej dla innych projektów w innych subskrypcjach, Dodaj inne subskrypcje do zakresu roli.

W przypadku naszego DevOpsego pracownika przypisanie roli niestandardowej dla uprawnienia magazynu kluczy `deploy/action` jest ograniczone do grupy zasobów. Tylko maszyny wirtualne utworzone w grupie zasobów **ContosoAppRG** mają dostęp do wpisów tajnych (TLS/SSL i certyfikaty Bootstrap). Maszyny wirtualne utworzone w innych grupach zasobów przez DevOps element członkowski nie mogą uzyskać dostępu do tych kluczy tajnych, nawet jeśli maszyna wirtualna ma identyfikatory URI.

Nasz przykład opisuje prosty scenariusz. Scenariusze życiowe mogą być bardziej skomplikowane. Możesz dostosować uprawnienia do magazynu kluczy w zależności od potrzeb. Zakładamy, że zespół ds. zabezpieczeń zawiera odwołania do kluczy i wpisów tajnych (identyfikatorów URI i odcisków palców), które są używane przez personel DevOps w swoich aplikacjach. Deweloperzy i operatorzy nie potrzebują dostępu do płaszczyzny danych. Firma Microsoft koncentruje się na sposobie zabezpieczania magazynu kluczy. Zadawaj podobne kwestie w przypadku zabezpieczania [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/security/), [kont magazynu](../../storage/blobs/security-recommendations.md)i innych zasobów platformy Azure.

> [!NOTE]
> Ten przykład pokazuje, jak dostęp Key Vault jest blokowany w środowisku produkcyjnym. Deweloperzy powinni mieć własną subskrypcję lub grupę zasobów z pełnymi uprawnieniami do zarządzania swoimi magazynami, maszynami wirtualnymi i kontem magazynu, na którym opracowują aplikację.

Zalecamy skonfigurowanie dodatkowego bezpiecznego dostępu do magazynu kluczy przez [skonfigurowanie Key Vault zapór i sieci wirtualnych](network-security.md).

## <a name="resources"></a>Zasoby

* [Kontrola RBAC w usłudze Azure AD](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md)

* [Zrozumienie Menedżer zasobów wdrożenia i wdrożenia klasycznego](../../azure-resource-manager/management/deployment-models.md)

* [Zarządzanie RBAC przy użyciu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [Zarządzanie RBAC przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)

* [RBAC dla Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Ten film dotyczący konferencji o zapłonie 2015 firmy Microsoft omawia możliwości zarządzania dostępem i raportowania na platformie Azure. Przedstawiono w nim również najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji platformy Azure za pomocą usługi Azure AD.

* [Autoryzuj dostęp do aplikacji sieci Web przy użyciu protokołu OAuth 2,0 i usługi Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Interfejsy API REST zarządzania Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Dokumentacja interfejsów API REST do programistycznego zarządzania magazynem kluczy, w tym ustawiania zasad dostępu Key Vault.

* [Interfejsy API REST Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Kontrola dostępu do kluczy](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Kontrola dostępu do wpisów tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Ustawianie](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) i [usuwanie](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) zasad dostępu Key Vault przy użyciu programu PowerShell.

## <a name="next-steps"></a>Następne kroki

Skonfiguruj [Key Vault zapory i sieci wirtualne](network-security.md).

Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [co to jest Azure Key Vault?](overview.md)).

Aby uzyskać więcej informacji na temat rejestrowania użycia Key Vault, zobacz [Azure Key Vault rejestrowanie](logging.md)).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych z Azure Key Vault, zobacz [Informacje o kluczach i wpisach tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Jeśli masz pytania dotyczące Key Vault, odwiedź stronę pytań i odpowiedzi na [stronie Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
