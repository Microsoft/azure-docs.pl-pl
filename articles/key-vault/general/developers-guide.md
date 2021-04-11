---
title: Przewodnik dewelopera usługi Azure Key Vault
description: Deweloperzy mogą używać Azure Key Vault do zarządzania kluczami kryptograficznymi w środowisku Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 550084ccbb1df24fe0cbc0a4630efe19e6685e5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709907"
---
# <a name="azure-key-vault-developers-guide"></a>Przewodnik dewelopera usługi Azure Key Vault

Key Vault pozwala bezpiecznie uzyskać dostęp do poufnych informacji z aplikacji:

- Klucze, wpisy tajne i certyfikaty są chronione bez konieczności samodzielnego pisania kodu i można z nich łatwo korzystać z aplikacji.
- Pozwalamy klientom na posiadanie własnych kluczy, wpisów tajnych i certyfikatów oraz zarządzanie nimi, aby skoncentrować się na dostarczaniu podstawowych funkcji oprogramowania. W ten sposób Twoje aplikacje nie będą własnością odpowiedzialności ani potencjalnej odpowiedzialności za klucze dzierżawy, wpisy tajne i certyfikaty klientów.
- Aplikacja może używać kluczy do podpisywania i szyfrowania, a tym samym zarządzać zarządzaniem kluczami zewnętrznymi z poziomu aplikacji. Aby uzyskać więcej informacji na temat kluczy, zobacz [Informacje o kluczach](../keys/about-keys.md)
- Możesz zarządzać poświadczeniami, takimi jak hasła, klucze dostępu i tokeny SAS, przechowując je w Key Vault jako wpisy tajne, zobacz [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- Zarządzanie certyfikatami. Aby uzyskać więcej informacji, zobacz [Informacje o certyfikatach](../certificates/about-certificates.md)

Aby uzyskać więcej ogólnych informacji na temat Azure Key Vault, zobacz [co to jest Key Vault](overview.md).

## <a name="public-previews"></a>Publiczne podglądy

Okresowo firma Microsoft udostępnia publiczną wersję zapoznawczą nowej funkcji Key Vault. Wypróbuj publiczną funkcję w wersji zapoznawczej i daj nam znać, co myślisz, za pośrednictwem azurekeyvault@microsoft.com adresu e-mail z opiniami.

## <a name="creating-and-managing-key-vaults"></a>Tworzenie magazynów kluczy i zarządzanie nimi

Key Vault zarządzanie, podobnie jak w przypadku innych usług platformy Azure, odbywa się za pomocą usługi Azure Resource Manager Service. Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów na koncie platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

Dostęp do warstwy zarządzania jest kontrolowany przez funkcję [kontroli dostępu opartej na rolach na platformie Azure](../../role-based-access-control/overview.md). W Key Vault warstwa zarządzania, znana również jako płaszczyzny zarządzania lub kontroli, umożliwia tworzenie magazynów kluczy i ich atrybutów oraz zarządzanie nimi, w tym zasady dostępu, ale nie klucze, wpisy tajne i certyfikaty, które są zarządzane w płaszczyźnie danych. Możesz użyć wstępnie zdefiniowanej `Key Vault Contributor` roli, aby udzielić dostępu do zarządzania Key Vault.     

**Interfejsy API i zestawy SDK do zarządzania magazynem kluczy:**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault)<br>[Szybki start](quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault)<br>[Szybki start](quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/)|[Odwołanie](/azure/templates/microsoft.keyvault/vaults)<br>[Szybki start](./vault-create-template.md)|[Odwołanie](/dotnet/api/microsoft.azure.management.keyvault)|[Odwołanie](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Odwołanie](/java/api/com.microsoft.azure.management.keyvault)|[Odwołanie](/javascript/api/@azure/arm-keyvault)|

Zobacz [biblioteki klienckie](client-libraries.md) dla pakietów instalacyjnych i kodu źródłowego.

Aby uzyskać więcej informacji na temat płaszczyzny zarządzania Key Vault, zobacz [Key Vault płaszczyzny zarządzania](./secure-your-key-vault.md#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Uwierzytelnianie w Key Vault w kodzie

Key Vault używa uwierzytelniania usługi Azure AD, który wymaga podmiotu zabezpieczeń usługi Azure AD, aby udzielić dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, główną usługą aplikacji, [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md)lub grupą dowolnego typu podmiotów zabezpieczeń.

### <a name="authentication-best-practices"></a>Najlepsze rozwiązania dotyczące uwierzytelniania

Zalecane jest używanie tożsamości zarządzanej dla aplikacji wdrożonych na platformie Azure. W przypadku korzystania z usług platformy Azure, które nie obsługują tożsamości zarządzanej lub jeśli aplikacje są wdrażane lokalnie, jednostka [usługi z certyfikatem](../../active-directory/develop/howto-create-service-principal-portal.md) jest możliwym alternatywą. W tym scenariuszu certyfikat powinien być przechowywany w Key Vault i obrócony często. Nazwa główna usługi z wpisem tajnym może być używana w środowiskach deweloperskich i testowych, a także lokalnie lub w Cloud Shell przy użyciu podmiotu zabezpieczeń użytkownika.

Zalecane podmioty zabezpieczeń na środowisko:
- **Środowisko produkcyjne**:
  - Zarządzana tożsamość lub jednostka usługi z certyfikatem
- **Środowiska testowe i programistyczne**:
  - Tożsamość zarządzana, nazwa główna usługi z certyfikatem lub jednostką usługi z kluczem tajnym
- **Programowanie lokalne**:
  - Nazwa główna użytkownika lub nazwa główna usługi z kluczem tajnym

Powyższe scenariusze uwierzytelniania są obsługiwane przez **bibliotekę klienta tożsamości platformy Azure** i są zintegrowane z zestawami SDK Key Vault. Biblioteka tożsamości platformy Azure może być używana w różnych środowiskach i platformach bez konieczności zmiany kodu. Usługa Azure Identity również automatycznie pobiera token uwierzytelniania z zalogowanego do użytkownika platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, programu Visual Studio, Visual Studio Code i innych. 

Aby uzyskać więcej informacji na temat usługi Azure Identity Client libarary, zobacz:

**Biblioteki klienta tożsamości platformy Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Zestaw Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Zestaw Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Biblioteka uwierzytelniania aplikacji](/dotnet/api/overview/azure/service-to-service-authentication) zalecana dla Key Vault .NET SDK w wersji 3, która jest obecnie depracated. Skorzystaj z [AppAuthentication na platformie Azure. wskazówki dotyczące migracji tożsamości](/dotnet/api/overview/azure/app-auth-migration) do migracji do Key Vault .NET SDK w wersji 4.

Aby zapoznać się z samouczkami dotyczącymi uwierzytelniania Key Vault w aplikacjach, zobacz:
- [Uwierzytelnianie w Key Vault w aplikacji hostowanej w maszynie wirtualnej na platformie .NET](./tutorial-net-virtual-machine.md)
- [Uwierzytelnianie w Key Vault w aplikacji hostowanej na maszynie wirtualnej w języku Python](./tutorial-python-virtual-machine.md)
- [Uwierzytelnianie w Key Vault przy użyciu App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Zarządzanie kluczami, certyfikatami i wpisami tajnymi

Dostęp do kluczy, wpisów tajnych i certyfikatów jest kontrolowany przez płaszczyznę danych. Kontrolę dostępu do płaszczyzny danych można wykonać przy użyciu zasad dostępu do magazynu lokalnego lub z usługi Azure RBAC (wersja zapoznawcza).

**Interfejsy API i zestawy SDK kluczy**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault/key)<br>[Szybki start](../keys/quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault/)<br>[Szybki start](../keys/quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/#key-operations)|[Odwołanie](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Szybki start](../keys/quick-create-template.md)|[Odwołanie](/dotnet/api/azure.security.keyvault.keys)<br>[Szybki start](../keys/quick-create-net.md)|[Odwołanie](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Szybki start](../keys/quick-create-python.md)|[Odwołanie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Szybki start](../keys/quick-create-java.md)|[Odwołanie](/javascript/api/@azure/keyvault-keys/)<br>[Szybki start](../keys/quick-create-node.md)|

**Interfejsy API i zestawy SDK certyfikatów**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault/certificate)<br>[Szybki start](../certificates/quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault)<br>[Szybki start](../certificates/quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/#certificate-operations)|Nie dotyczy|[Odwołanie](/dotnet/api/azure.security.keyvault.certificates)<br>[Szybki start](../certificates/quick-create-net.md)|[Odwołanie](/python/api/overview/azure/keyvault-certificates-readme)<br>[Szybki start](../certificates/quick-create-python.md)|[Odwołanie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Szybki start](../certificates/quick-create-java.md)|[Odwołanie](/javascript/api/@azure/keyvault-certificates/)<br>[Szybki start](../certificates/quick-create-node.md)|

**Interfejsy API i zestawy SDK dla kluczy tajnych**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault/secret)<br>[Szybki start](../secrets/quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault/)<br>[Szybki start](../secrets/quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/#secret-operations)|[Odwołanie](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Szybki start](../secrets/quick-create-template.md)|[Odwołanie](/dotnet/api/azure.security.keyvault.secrets)<br>[Szybki start](../secrets/quick-create-net.md)|[Odwołanie](/python/api/overview/azure/keyvault-secrets-readme)<br>[Szybki start](../secrets/quick-create-python.md)|[Odwołanie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Szybki start](../secrets/quick-create-java.md)|[Odwołanie](/javascript/api/@azure/keyvault-secrets/)<br>[Szybki start](../secrets/quick-create-node.md)|

Zobacz [biblioteki klienckie](client-libraries.md) dla pakietów instalacyjnych i kodu źródłowego.

Aby uzyskać więcej informacji na temat Key Vault zabezpieczeń płaszczyzny danych, zobacz [Key Vault płaszczyzny danych i zasad dostępu](./secure-your-key-vault.md#data-plane-and-access-policies) oraz [Key Vaultj płaszczyzny danych i usługi Azure RBAC (wersja zapoznawcza)](./secure-your-key-vault.md#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>Przykłady kodu

Aby zapoznać się z kompletnymi przykładami dotyczącymi używania Key Vault z aplikacjami, zobacz:

- [Przykłady kodu Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) — przykłady kodu dla Azure Key Vault. 

## <a name="how-tos"></a>Instrukcje

Poniższe artykuły i scenariusze zapewniają wskazówki dotyczące zadań związanych z pracą z Azure Key Vault:

- Dostęp do [Key Vault za zaporą](access-behind-firewall.md) — Aby uzyskać dostęp do magazynu kluczy, aplikacja kliencka magazynu kluczy musi mieć dostęp do wielu punktów końcowych dla różnych funkcji.
- Jak wdrażać certyfikaty na maszynach wirtualnych z Key Vault — [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) — aplikacja w chmurze działająca na maszynie wirtualnej na platformie Azure wymaga certyfikatu. Jak uzyskać ten certyfikat na tę maszynę wirtualną dzisiaj?
- [Wdrażanie certyfikatu aplikacji sieci Web platformy Azure za pomocą Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Przypisz zasady dostępu (Portal[CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  [](assign-access-policy-portal.md)). 
- [Jak używać narzędzia Key Vault Soft-Delete with CLI](./key-vault-recovery.md) przeprowadzi Cię przez użycie i cykl życia magazynu kluczy oraz różne obiekty magazynu kluczy z włączonym usuwaniem nietrwałego.
- [Jak przekazać bezpieczne wartości (takie jak hasła) podczas wdrażania](../../azure-resource-manager/templates/key-vault-parameter.md) — Jeśli chcesz przekazać bezpieczną wartość (na przykład hasło) jako parametr podczas wdrażania, możesz zapisać tę wartość jako klucz tajny w Azure Key Vault i odwołać się do wartości w innych szablonach Menedżer zasobów.

## <a name="integrated-with-key-vault"></a>Integracja z Key Vault

W tych artykułach przedstawiono inne scenariusze i usługi, które korzystają z Key Vault lub integrują się z nimi.

- [Szyfrowanie w spoczynku](../../security/fundamentals/encryption-atrest.md) umożliwia kodowanie (szyfrowanie) danych, gdy są utrwalane. Klucze szyfrowania danych są często szyfrowane za pomocą klucza szyfrowania kluczy w Azure Key Vault, aby dodatkowo ograniczyć dostęp.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umożliwia kierownika własnego klucza dzierżawy. Na przykład zamiast firmy Microsoft zarządzającej kluczem dzierżawy (domyślnie) można zarządzać własnym kluczem dzierżawy w celu zapewnienia zgodności z określonymi przepisami obowiązującymi w organizacji. Samodzielne zarządzanie kluczem dzierżawy określa się także mianem strategii BYOK (Bring Your Own Key), czyli „Przynieś własny klucz”.
- [Usługa link prywatny platformy Azure](private-link-service.md) umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.
- Key Vault integracja z usługą [Event Grid](../../event-grid/event-schema-key-vault.md)  umożliwia użytkownikom otrzymywanie powiadomień o zmianie stanu wpisu tajnego przechowywanego w magazynie kluczy. Nową wersję wpisów tajnych można dystrybuować do aplikacji lub obracać w bliskich okresach tajnych, aby zapobiec awariom.
- Możesz chronić Twoje wpisy tajne [platformy Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) przed niechcianym dostępem w Key Vault.
- [Aby nawiązać połączenie z usługą Azure Storage, użyj wpisu tajnego przechowywanego w Key Vault w obszarze datakosteks](./integrate-databricks-blob-storage.md)
- Skonfiguruj i uruchom dostawcę Azure Key Vault dla [sterownika CSI magazynu wpisów tajnych](./key-vault-integrate-kubernetes.md) w systemie Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Key Vault przeglądy i koncepcje

- [Key Vault zachowanie usuwania nietrwałego](soft-delete-overview.md) opisuje funkcję, która umożliwia Odzyskiwanie usuniętych obiektów, niezależnie od tego, czy usunięcie było przypadkowe czy celowe.
- [Key Vault ograniczanie klientów](overview-throttling.md) ukierunkowane na podstawowe koncepcje ograniczania i oferuje podejście do aplikacji.
- [Key Vault światy zabezpieczeń](overview-security-worlds.md) opisuje relacje między regionami i obszarami zabezpieczeń.

## <a name="social"></a>Funkcje społecznościowe

- [Blog Key Vault](/archive/blogs/kv/)
- [Forum Key Vault](https://aka.ms/kvforum)