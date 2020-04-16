---
title: Bezpieczny dostęp do magazynu kluczy — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Zarządzanie uprawnieniami dostępu do usługi Azure Key Vault, kluczami i wpisami tajnymi. Obejmuje model uwierzytelniania i autoryzacji dla usługi Key Vault oraz sposób zabezpieczania magazynu kluczy.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 0ae1b26bb2e01d388f3f91d94134bb9723a5a305
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432023"
---
# <a name="secure-access-to-a-key-vault"></a>Bezpieczny dostęp do magazynu kluczy

Usługa Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. Ponieważ te dane są poufne i krytyczne dla firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko autoryzowanym aplikacjom i użytkownikom. Ten artykuł zawiera omówienie modelu dostępu usługi Key Vault. Wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób bezpiecznego dostępu do magazynów kluczy.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Omówienie modelu programu Access

Dostęp do magazynu kluczy jest kontrolowany za pomocą dwóch interfejsów: **płaszczyzny zarządzania** i **płaszczyzny danych**. Płaszczyzna zarządzania to miejsce, w którym samodzielnie zarządzasz magazynem kluczy. Operacje na tej płaszczyźnie obejmują tworzenie i usuwanie magazynów kluczy, pobieranie właściwości usługi Key Vault i aktualizowanie zasad dostępu. Płaszczyzna danych to miejsce, w którym pracujesz z danymi przechowywanymi w magazynie kluczy. Można dodawać, usuwać i modyfikować klucze, wpisy tajne i certyfikaty.

Aby uzyskać dostęp do magazynu kluczy na obu płaszczyznach, wszystkie osoby wywołujące (użytkownicy lub aplikacje) muszą mieć odpowiednie uwierzytelnianie i autoryzację. Uwierzytelnianie ustanawia tożsamość wywołującego. Autoryzacja określa, które operacje wywołujący można wykonać.

Obie płaszczyzny używają usługi Azure Active Directory (Azure AD) do uwierzytelniania. Do autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), a płaszczyzna danych używa zasad dostępu usługi Key Vault.

## <a name="active-directory-authentication"></a>Uwierzytelnianie usługi Active Directory

Podczas tworzenia magazynu kluczy w subskrypcji platformy Azure jest automatycznie skojarzony z dzierżawy usługi Azure AD subskrypcji. Wszystkie osoby wywołujące w obu płaszczyznach muszą zarejestrować się w tej dzierżawie i uwierzytelnić, aby uzyskać dostęp do magazynu kluczy. W obu przypadkach aplikacje mogą uzyskiwać dostęp do usługi Key Vault na dwa sposoby:

- **Użytkownik plus dostęp do aplikacji**: Aplikacja uzyskuje dostęp do usługi Key Vault w imieniu zalogowanego użytkownika. Przykłady tego typu dostępu obejmują usługi Azure PowerShell i witryny Azure portal. Dostęp użytkownika jest przyznawany na dwa sposoby. Użytkownicy mogą uzyskać dostęp do Usługi Key Vault z dowolnej aplikacji lub muszą użyć określonej aplikacji (określanej jako _tożsamość złożona)._
- **Dostęp tylko do aplikacji:** Aplikacja działa jako usługa demona lub zadanie w tle. Tożsamość aplikacji jest przyznawany dostęp do magazynu kluczy.

Dla obu typów dostępu aplikacja uwierzytelnia się za pomocą usługi Azure AD. Aplikacja używa dowolnej [obsługiwanej metody uwierzytelniania](../../active-directory/develop/authentication-scenarios.md) na podstawie typu aplikacji. Aplikacja uzyskuje token dla zasobu w płaszczyźnie, aby udzielić dostępu. Zasób jest punktem końcowym w płaszczyźnie zarządzania lub danych, na podstawie środowiska platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do usługi Key Vault. Aby dowiedzieć się więcej, przejrzyj [cały przepływ uwierzytelniania](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Model pojedynczego mechanizmu uwierzytelniania w obu płaszczyznach ma kilka zalet:

- Organizacje mogą centralnie kontrolować dostęp do wszystkich magazynów kluczy w swojej organizacji.
- Jeśli użytkownik odejdzie, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosować uwierzytelnianie przy użyciu opcji w usłudze Azure AD, takich jak włączenie uwierzytelniania wieloskładnikowego dla dodatkowych zabezpieczeń.

## <a name="resource-endpoints"></a>Punkty końcowe zasobu

Aplikacje uzyskują dostęp do płaszczyzn za pośrednictwem punktów końcowych. Formanty dostępu dla obu płaszczyzn działają niezależnie. Aby udzielić aplikacji dostępu do używania kluczy w magazynie kluczy, można udzielić dostępu do płaszczyzny danych przy użyciu zasad dostępu usługi Key Vault. Aby udzielić użytkownikowi dostępu do odczytu do właściwości i tagów usługi Key Vault, ale nie dostępu do danych (kluczy, wpisów tajnych lub certyfikatów), należy przyznać dostęp do płaszczyzny zarządzania za pomocą rbac.

W poniższej tabeli przedstawiono punkty końcowe dla płaszczyzn zarządzania i danych.

| Płaszczyzna dostępu&nbsp; | Punkty końcowe dostępu | Operacje | Mechanizm&nbsp;kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Globalne:**<br> management.azure.com:443<br><br> **Usługa Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Niemcy:**<br> management.microsoftazure.de:443 | Tworzenie, odczytywanie, aktualizowanie i usuwanie magazynów kluczy<br><br>Ustawianie zasad dostępu do usługi Key Vault<br><br>Ustawianie znaczników Przechowalni kluczy | Azure Resource Manager RBAC |
| Płaszczyzna danych | **Globalne:**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Usługa Azure China 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Niemcy:**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 | Klucze: odszyfrowywanie, szyfrowanie,<br> rozpakować, zawinąć, zweryfikować, podpisać,<br> uzyskać, lista, aktualizacja, tworzenie,<br> importowanie, usuwanie, tworzenie kopii zapasowych, przywracanie<br><br> Sekrety: pobierz, lista, zestaw, usuń | Zasady dostępu do usługi Key Vault |

## <a name="management-plane-and-rbac"></a>Płaszczyzna zarządzania i RBAC

Na płaszczyźnie zarządzania można użyć RBAC(Kontrola dostępu oparta na rolach) do autoryzacji operacji, które można wykonać wywołujący. W modelu RBAC każda subskrypcja platformy Azure ma wystąpienie usługi Azure AD. Udzielasz dostępu użytkownikom, grupom i aplikacjom z tego katalogu. Dostęp jest przyznawany do zarządzania zasobami w ramach subskrypcji platformy Azure, które korzystają z modelu wdrażania usługi Azure Resource Manager. Aby udzielić dostępu, użyj [witryny Azure portal](https://portal.azure.com/), [interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli?view=azure-cli-latest) [programu Azure PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsów API REST usługi Azure Resource Manager.](https://msdn.microsoft.com/library/azure/dn906885.aspx)

Tworzenie magazynu kluczy w grupie zasobów i zarządzanie dostępem przy użyciu usługi Azure AD. Użytkownikom lub grupom można zarządzać magazynami kluczy w grupie zasobów. Udzielić dostępu na poziomie określonego zakresu, przypisując odpowiednie role RBAC. Aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną `key vault Contributor` rolę do użytkownika w określonym zakresie. Do roli RBAC można przypisać następujące poziomy zakresów:

- **Subskrypcja:** Rola RBAC przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów:** Rola RBAC przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w tej grupie zasobów.
- **Określony zasób:** Rola RBAC przypisana do określonego zasobu ma zastosowanie do tego zasobu. W takim przypadku zasób jest określonym magazynem kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowana rola nie odpowiada Twoim potrzebom, można zdefiniować własną rolę. Aby uzyskać więcej informacji, zobacz [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Jeśli użytkownik `Contributor` ma uprawnienia do płaszczyzny zarządzania przechowalnią kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych, ustawiając zasady dostępu usługi Key Vault. Należy ściśle kontrolować, `Contributor` kto ma dostęp do roli do magazynów kluczy. Upewnij się, że tylko autoryzowane osoby mogą uzyskiwać dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Płaszczyzna danych i zasady dostępu

Dostęp do płaszczyzny danych można przyznać, ustawiając zasady dostępu usługi Key Vault dla magazynu kluczy. Aby ustawić te zasady dostępu, użytkownik, grupa `Contributor` lub aplikacja musi mieć uprawnienia do płaszczyzny zarządzania dla tego magazynu kluczy.

Udzielasz użytkownikowi, grupie lub aplikacji dostępu do wykonywania określonych operacji dla kluczy lub wpisów tajnych w magazynie kluczy. Usługa Key Vault obsługuje maksymalnie 1024 wpisy zasad dostępu dla magazynu kluczy. Aby udzielić dostępu do płaszczyzny danych kilku użytkownikom, utwórz grupę zabezpieczeń usługi Azure AD i dodaj użytkowników do tej grupy.

<a id="key-vault-access-policies"></a>Zasady dostępu usługi Key Vault przyznają uprawnienia oddzielnie do kluczy, wpisów tajnych i certyfikatów. Można udzielić użytkownikowi dostępu tylko do kluczy, a nie do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów znajdują się na poziomie przechowalni. Zasady dostępu usługi Key Vault nie obsługują szczegółowych uprawnień na poziomie obiektu, takich jak określony klucz, klucz tajny lub certyfikat. Aby ustawić zasady dostępu dla magazynu kluczy, użyj [witryny Azure Portal](https://portal.azure.com/), [interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli?view=azure-cli-latest) [programu Azure PowerShell](/powershell/azureps-cmdlets-docs)lub [interfejsów API REST usługi Key Vault Management](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zasady dostępu do magazynu kluczy mają zastosowanie na poziomie przechowalni. Gdy użytkownikowi udzielono uprawnień do tworzenia i usuwania kluczy, mogą wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.
>

Dostęp do płaszczyzny danych można ograniczyć przy użyciu [punktów końcowych usługi sieci wirtualnej dla usługi Azure Key Vault](overview-vnet-service-endpoints.md)). Można skonfigurować [zapory i reguły sieci wirtualnej](network-security.md) dla dodatkowej warstwy zabezpieczeń.

## <a name="example"></a>Przykład

W tym przykładzie opracowujemy aplikację, która używa certyfikatu dla protokołu TLS/SSL, usługi Azure Storage do przechowywania danych i klucza 2048-bitowego RSA dla operacji podpisu. Nasza aplikacja działa na maszynie wirtualnej platformy Azure (VM) (lub zestaw skalowania maszyny wirtualnej). Możemy użyć magazynu kluczy do przechowywania wpisów tajnych aplikacji. Możemy przechowywać certyfikat bootstrap, który jest używany przez aplikację do uwierzytelniania za pomocą usługi Azure AD.

Potrzebujemy dostępu do następujących przechowywanych kluczy i wpisów tajnych:
- **Certyfikat TLS/SSL:** Używany dla protokołu TLS/SSL.
- **Klucz magazynu:** służy do uzyskiwania dostępu do konta magazynu.
- **Klucz 2048-bitowy RSA:** używany do operacji podpisywania.
- **Certyfikat bootstrap:** służy do uwierzytelniania za pomocą usługi Azure AD. Po udzieleniu dostępu możemy pobrać klucz magazynu i użyć klucza RSA do podpisywania.

Musimy zdefiniować następujące role, aby określić, kto może zarządzać, wdrażać i przeprowadzać inspekcję naszej aplikacji:
- **Zespół bezpieczeństwa**: pracownicy IT z biura GUS (Chief Security Officer) lub podobni współpracownicy. Zespół bezpieczeństwa jest odpowiedzialny za właściwe przechowywanie tajemnic. Wpisy tajne mogą zawierać certyfikaty TLS/SSL, klucze RSA do podpisywania, parametry połączenia i klucze kont magazynu.
- **Deweloperzy i operatorzy:** Pracownicy, którzy opracowują aplikację i wdrażają ją na platformie Azure. Członkowie tego zespołu nie są częścią personelu ochrony. Nie powinny mieć dostępu do poufnych danych, takich jak certyfikaty TLS/SSL i klucze RSA. Tylko aplikacja, którą wdrażają, powinna mieć dostęp do poufnych danych.
- **Audytorzy**: Ta rola jest dla współpracowników, którzy nie są członkami rozwoju lub ogólnego personelu IT. Przeglądają one użycie i konserwację certyfikatów, kluczy i wpisów tajnych, aby zapewnić zgodność ze standardami zabezpieczeń.

Istnieje inna rola, która jest poza zakresem naszej aplikacji: administrator subskrypcji (lub grupy zasobów). Administrator subskrypcji konfiguruje początkowe uprawnienia dostępu dla zespołu zabezpieczeń. Udzielają dostępu do zespołu zabezpieczeń przy użyciu grupy zasobów, która ma zasoby wymagane przez aplikację.

Musimy autoryzować następujące operacje dla naszych ról:

**Zespół ds. zabezpieczeń**
- Tworzenie magazynów kluczy.
- Włącz rejestrowanie magazynu kluczy.
- Dodaj klucze i wpisy tajne.
- Tworzenie kopii zapasowych kluczy do odzyskiwania po awarii.
- Ustaw zasady dostępu usługi Key Vault, aby udzielić uprawnień użytkownikom i aplikacjom do określonych operacji.
- Okresowo przewracaj klawisze i wpisy tajne.

**Deweloperzy i operatorzy**
- Uzyskaj odwołania od zespołu zabezpieczeń dla certyfikatów boottrap i TLS/SSL (odciski palców), klucza magazynu (tajnego identyfikatora URI) i klucza RSA (klucz URI) do podpisywania.
- Programowo twórz i wdrażaj aplikację, aby programowo uzyskiwać dostęp do kluczy i wpisów tajnych.

**Audytorzy**
- Przejrzyj dzienniki usługi Key Vault, aby potwierdzić prawidłowe użycie kluczy i wpisów tajnych oraz zgodność ze standardami bezpieczeństwa danych.

W poniższej tabeli podsumowano uprawnienia dostępu dla naszych ról i aplikacji.

| Rola | Uprawnienia do płaszczyzny zarządzania | Uprawnienia do płaszczyzny danych |
| --- | --- | --- |
| Zespół ds. zabezpieczeń | Współautor magazynu kluczy | Klucze: wykonywanie kopii zapasowej, tworzenie, usuwanie, pobieranie, importowanie, wyświetlanie, przywracanie<br>Sekrety: wszystkie operacje |
| Deweloperzy&nbsp;i operatorzy | Uprawnienie do wdrażania usługi Key Vault<br><br> **Uwaga:** To uprawnienie umożliwia wdrożonym maszynom wirtualnym pobieranie wpisów tajnych z magazynu kluczy. | Brak |
| Audytorzy | Brak | Klucze: wyświetlanie<br>Wpisy tajne: wyświetlanie<br><br> **Uwaga:** To uprawnienie umożliwia audytorom sprawdzanie atrybutów (znaczników, dat aktywacji, dat wygaśnięcia) kluczy i wpisów tajnych, które nie zostały wyemitowane w dziennikach. |
| Aplikacja | Brak | Klucze: podpisywanie<br>Wpisy tajne: pobieranie |

Trzy role zespołu potrzebują dostępu do innych zasobów wraz z uprawnieniami usługi Key Vault. Aby wdrożyć maszyny wirtualne (lub funkcję aplikacji sieci Web `Contributor` usługi Azure App Service), deweloperzy i operatorzy potrzebują dostępu do tych typów zasobów. Audytorzy potrzebują dostępu do odczytu konta Magazynu, na którym są przechowywane dzienniki usługi Key Vault.

Aby uzyskać więcej informacji na temat programowego wdrażania certyfikatów, kluczy dostępu i wpisów tajnych, zobacz następujące zasoby:
- Dowiedz się, jak [wdrażać certyfikaty na maszynach wirtualnych z magazynu kluczy zarządzanych przez klienta](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (wpis w blogu).
- Pobierz [przykłady klienta usługi Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Ta zawartość ilustruje sposób korzystania z certyfikatu bootstrap do uwierzytelniania w usłudze Azure AD w celu uzyskania dostępu do magazynu kluczy.

Większość uprawnień dostępu można udzielić za pomocą witryny Azure portal. Aby udzielić szczegółowych uprawnień, można użyć programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Fragmenty kodu programu PowerShell w tej sekcji są tworzone z następującymi założeniami:
- Administrator usługi Azure AD utworzył grupy zabezpieczeń do reprezentowania trzech ról: Zespołu zabezpieczeń Contoso, Contoso App DevOps i Contoso App Auditors. Administrator dodał użytkowników do swoich grup.
- Wszystkie zasoby znajdują się w grupie zasobów **ContosoAppRG.**
- Dzienniki usługi Key Vault są przechowywane na koncie magazynu **contosologstorage.**
- Magazyn kluczy **ContosoKeyVault** i konto magazynu **contosologstorage** znajdują się w tej samej lokalizacji platformy Azure.

Administrator subskrypcji przypisuje `key vault Contributor` i `User Access Administrator` role do zespołu zabezpieczeń. Te role umożliwiają zespołowi zabezpieczeń zarządzanie dostępem do innych zasobów i magazynów kluczy, które są w grupie zasobów **ContosoAppRG.**

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Zespół zabezpieczeń tworzy magazyn kluczy i konfiguruje uprawnienia do rejestrowania i dostępu.

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

Zdefiniowane przez nas role niestandardowe można przypisać tylko do subskrypcji, w której tworzona jest grupa zasobów **ContosoAppRG.** Aby użyć roli niestandardowej dla innych projektów w innych subskrypcjach, dodaj inne subskrypcje do zakresu roli.

Dla naszego personelu DevOps przypisanie roli `deploy/action` niestandardowej dla uprawnień magazynu kluczy jest ograniczone do grupy zasobów. Tylko maszyny wirtualne utworzone w grupie zasobów **ContosoAppRG** mają dostęp do wpisów tajnych (certyfikatów TLS/SSL i bootstrap). Maszyny wirtualne utworzone w innych grupach zasobów przez członka DevOps nie mogą uzyskać dostępu do tych wpisów tajnych, nawet jeśli maszyna wirtualna ma tajne identyfikatory URI.

W naszym przykładzie opisano prosty scenariusz. Rzeczywiste scenariusze mogą być bardziej złożone. Uprawnienia do magazynu kluczy można dostosować do swoich potrzeb. Założyliśmy, że zespół zabezpieczeń udostępnia klucz i tajne odwołania (URI i odciski palców), które są używane przez pracowników DevOps w swoich aplikacjach. Deweloperzy i operatorzy nie wymagają dostępu do płaszczyzny danych. Skupiliśmy się na tym, jak zabezpieczyć magazyn kluczy. Należy zwrócić szczególną uwagę podczas [zabezpieczania maszyn wirtualnych,](https://azure.microsoft.com/services/virtual-machines/security/) [kont magazynu](../../storage/blobs/security-recommendations.md)i innych zasobów platformy Azure.

> [!NOTE]
> W tym przykładzie pokazano, jak dostęp do usługi Key Vault jest zablokowany w produkcji. Deweloperzy powinni mieć własną subskrypcję lub grupę zasobów z pełnymi uprawnieniami do zarządzania ich magazynami, maszynami wirtualnymi i kontem magazynu, na którym rozwijają aplikację.

Zalecamy skonfigurowanie dodatkowego bezpiecznego dostępu do magazynu kluczy przez [skonfigurowanie zapór i sieci wirtualnych usługi Key Vault.](network-security.md)

## <a name="resources"></a>Zasoby

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md)

* [Poznaj wdrażanie Menedżera zasobów i wdrażanie klasyczne](../../azure-resource-manager/management/deployment-models.md)

* [Zarządzanie programem RBAC za pomocą programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [Zarządzanie rbac za pomocą interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)

* [RBAC dla platformy Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    W tym klipie wideo z konferencji Microsoft Ignite 2015 omówiono możliwości zarządzania dostępem i raportowania na platformie Azure. Eksploruje również najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji platformy Azure przy użyciu usługi Azure AD.

* [Autoryzuj dostęp do aplikacji sieci Web przy użyciu usług OAuth 2.0 i usługi Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Interfejsy API REST zarządzania magazynem kluczy](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Odwołanie do interfejsów API REST do programowania zarządzania magazynem kluczy, w tym ustawiania zasad dostępu usługi Key Vault.

* [Interfejsy API rest magazynu kluczy](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Kontrola dostępu do kluczy](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Kontrola dostępu do wpisów tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Ustawianie](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) i [usuwanie](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) zasad dostępu usługi Key Vault przy użyciu programu PowerShell.

## <a name="next-steps"></a>Następne kroki

Konfigurowanie [zapór i sieci wirtualnych usługi Key Vault](network-security.md).

Aby zapoznać się z samouczkiem dla administratora, zobacz [Co to jest usługa Azure Key Vault?](overview.md)).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Rejestrowanie usługi Azure Key Vault](logging.md)).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych w usłudze Azure Key Vault, zobacz [Informacje o kluczach i wpisach tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Jeśli masz pytania dotyczące Programu Key Vault, odwiedź [fora](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
