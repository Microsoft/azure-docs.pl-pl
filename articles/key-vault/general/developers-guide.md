---
title: Przewodnik dewelopera usługi Azure Key Vault
description: Deweloperzy mogą używać Azure Key Vault do zarządzania kluczami kryptograficznymi w Microsoft Azure środowisku.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 4f9523594c07209d530a143713061be6d0467af8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753384"
---
# <a name="azure-key-vault-developers-guide"></a>Przewodnik dewelopera usługi Azure Key Vault

Key Vault umożliwia bezpieczny dostęp do poufnych informacji z poziomu aplikacji:

- Klucze, wpisy tajne i certyfikaty są chronione bez konieczności samodzielnie pisania kodu i łatwego korzystania z nich z aplikacji.
- Umożliwiasz klientom posiadanie własnych kluczy, wpisów tajnych i certyfikatów oraz zarządzanie nimi, dzięki czemu można skoncentrować się na udostępnianiu podstawowych funkcji oprogramowania. W ten sposób aplikacje nie będą ponosić odpowiedzialności ani ponosić odpowiedzialności za klucze dzierżawy, wpisy tajne i certyfikaty klientów.
- Aplikacja może używać kluczy do podpisywania i szyfrowania, ale zachowuje zarządzanie kluczami poza aplikacją. Aby uzyskać więcej informacji na temat kluczy, zobacz [Informacje o kluczach](../keys/about-keys.md)
- Poświadczeniami, np. hasłami, kluczami dostępu i tokenami sas, można zarządzać, przechowując je w Key Vault jako wpisami tajnymi. Zobacz [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- Zarządzanie certyfikatami. Aby uzyskać więcej informacji, zobacz [About Certificates (Informacje o certyfikatach)](../certificates/about-certificates.md)

Aby uzyskać więcej ogólnych informacji na Azure Key Vault, zobacz [Co to jest Key Vault](overview.md).

## <a name="public-previews"></a>Publiczne wersje zapoznawcze

Okresowo wydajemy publiczną wersję zapoznawczą nowej funkcji Key Vault wersji zapoznawczej. Wypróbuj funkcje publicznej wersji zapoznawczej i daj nam znać, co myślisz za pośrednictwem azurekeyvault@microsoft.com adresu e-mail z opiniami.

## <a name="creating-and-managing-key-vaults"></a>Tworzenie magazynów kluczy i zarządzanie nimi

Key Vault, podobnie jak w przypadku innych usług platformy Azure, odbywa się za pośrednictwem Azure Resource Manager service. Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów na koncie platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

Dostęp do warstwy zarządzania jest kontrolowany przez kontrolę [dostępu opartą na rolach platformy Azure.](../../role-based-access-control/overview.md) W Key Vault zarządzania, znana również jako płaszczyzna zarządzania lub kontroli, umożliwia tworzenie magazynów Kluczy i ich atrybutów, w tym zasad dostępu, ale nie kluczy, wpisów tajnych i certyfikatów, które są zarządzane na płaszczyźnie danych. Wstępnie zdefiniowana rola umożliwia przyznanie dostępu do zarządzania `Key Vault Contributor` Key Vault.     

**Zestawy SDK i interfejsu API do zarządzania magazynem kluczy:**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault)<br>[Szybki start](quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault)<br>[Szybki start](quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/)|[Odwołanie](/azure/templates/microsoft.keyvault/vaults)<br>[Szybki start](./vault-create-template.md)|[Odwołanie](/dotnet/api/microsoft.azure.management.keyvault)|[Odwołanie](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Odwołanie](/java/api/com.microsoft.azure.management.keyvault)|[Odwołanie](/javascript/api/@azure/arm-keyvault)|

Zobacz [Biblioteki klienckie,](client-libraries.md) aby uzyskać informacje o pakietach instalacyjnych i kodzie źródłowym.

Aby uzyskać więcej informacji na temat Key Vault zarządzania, zobacz [Key Vault Płaszczyzna zarządzania](security-overview.md)

## <a name="authenticate-to-key-vault-in-code"></a>Uwierzytelnianie w Key Vault w kodzie

Key Vault korzysta z uwierzytelniania usługi Azure AD, które wymaga od podmiotu zabezpieczeń usługi Azure AD udzielenia dostępu. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, jednostką usługi aplikacji, tożsamością zarządzaną dla zasobów platformy [Azure](../../active-directory/managed-identities-azure-resources/overview.md)lub grupą dowolnego typu podmiotów zabezpieczeń.

### <a name="authentication-best-practices"></a>Najlepsze rozwiązania dotyczące uwierzytelniania

Zaleca się używanie tożsamości zarządzanej dla aplikacji wdrożonych na platformie Azure. Jeśli używasz usług platformy Azure, które nie obsługują tożsamości zarządzanej lub [](../../active-directory/develop/howto-create-service-principal-portal.md) jeśli aplikacje są wdrażane lokalnie, jednostką usługi z certyfikatem jest możliwa alternatywa. W tym scenariuszu certyfikat powinien być przechowywany w Key Vault i często obracany. Jednostki usługi z kluczem tajnym można używać w środowiskach deweloperskich i testowych, a także lokalnie lub Cloud Shell przy użyciu jednostki użytkownika.

Zalecane podmioty zabezpieczeń na środowisko:
- **Środowisko produkcyjne:**
  - Tożsamość zarządzana lub jednostki usługi z certyfikatem
- **Środowiska testowe i programowe:**
  - Tożsamość zarządzana, jednostkę usługi z certyfikatem lub jednostką usługi z kluczem tajnym
- **Opracowywanie lokalne:**
  - Nazwa główna użytkownika lub nazwa główna usługi z kluczem tajnym

Powyższe scenariusze uwierzytelniania są obsługiwane przez **bibliotekę klienta tożsamości** platformy Azure i zintegrowane z Key Vault SDK. Biblioteki tożsamości platformy Azure można używać w różnych środowiskach i na różnych platformach bez konieczności zmieniania kodu. Tożsamość platformy Azure będzie również automatycznie pobierać token uwierzytelniania z konta zalogowanego użytkownika platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure Visual Studio, Visual Studio Code i innych. 

Aby uzyskać więcej informacji na temat usługi libarary klienta tożsamości platformy Azure, zobacz:

**Biblioteki klienta tożsamości platformy Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Zestaw SDK tożsamości platformy Azure dla języka Python](/python/api/overview/azure/identity-readme)|[Zestaw SDK tożsamości platformy Azure w języku Java](/java/api/overview/azure/identity-readme)|[Zestaw SDK tożsamości platformy Azure w języku JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Biblioteka uwierzytelniania aplikacji](/dotnet/api/overview/azure/service-to-service-authentication) zalecana dla Key Vault SDK platformy .NET w wersji 3, która jest obecnie niepracowana. Aby przeprowadzić migrację do zestawu SDK platformy .NET w wersji 4, postępuj zgodnie ze wskazówkami Key Vault [AppAuthentication to Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration) (Wskazówki dotyczące migracji na platformę Azure.Identity).

Aby uzyskać samouczki dotyczące uwierzytelniania w Key Vault w aplikacjach, zobacz:
- [Uwierzytelnianie w u Key Vault w aplikacji hostowanej na maszynie wirtualnej na .NET](./tutorial-net-virtual-machine.md)
- [Uwierzytelnianie w u Key Vault w aplikacji hostowanej na maszynie wirtualnej w języku Python](./tutorial-python-virtual-machine.md)
- [Uwierzytelnianie w Key Vault za pomocą App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Zarządzanie kluczami, certyfikatami i wpisami tajnymi

Dostęp do kluczy, wpisów tajnych i certyfikatów jest kontrolowany przez płaszczyznę danych. Kontrolę dostępu do płaszczyzny danych można wykonać przy użyciu zasad dostępu magazynu lokalnego lub kontroli dostępu na poziomie ról platformy Azure.

**Zestawy SDK i interfejsy API kluczy**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault/key)<br>[Szybki start](../keys/quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault/)<br>[Szybki start](../keys/quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/#key-operations)|[Odwołanie](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Szybki start](../keys/quick-create-template.md)|[Odwołanie](/dotnet/api/azure.security.keyvault.keys)<br>[Szybki start](../keys/quick-create-net.md)|[Odwołanie](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Szybki start](../keys/quick-create-python.md)|[Odwołanie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Szybki start](../keys/quick-create-java.md)|[Odwołanie](/javascript/api/@azure/keyvault-keys/)<br>[Szybki start](../keys/quick-create-node.md)|

**Zestawy SDK i interfejsy API certyfikatów**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault/certificate)<br>[Szybki start](../certificates/quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault)<br>[Szybki start](../certificates/quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/#certificate-operations)|Nie dotyczy|[Odwołanie](/dotnet/api/azure.security.keyvault.certificates)<br>[Szybki start](../certificates/quick-create-net.md)|[Odwołanie](/python/api/overview/azure/keyvault-certificates-readme)<br>[Szybki start](../certificates/quick-create-python.md)|[Odwołanie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Szybki start](../certificates/quick-create-java.md)|[Odwołanie](/javascript/api/@azure/keyvault-certificates/)<br>[Szybki start](../certificates/quick-create-node.md)|

**Zestawy SDK i interfejsy API wpisów tajnych**

| Interfejs wiersza polecenia platformy Azure | PowerShell | Interfejs API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odwołanie](/cli/azure/keyvault/secret)<br>[Szybki start](../secrets/quick-create-cli.md)|[Odwołanie](/powershell/module/az.keyvault/)<br>[Szybki start](../secrets/quick-create-powershell.md)|[Odwołanie](/rest/api/keyvault/#secret-operations)|[Odwołanie](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Szybki start](../secrets/quick-create-template.md)|[Odwołanie](/dotnet/api/azure.security.keyvault.secrets)<br>[Szybki start](../secrets/quick-create-net.md)|[Odwołanie](/python/api/overview/azure/keyvault-secrets-readme)<br>[Szybki start](../secrets/quick-create-python.md)|[Odwołanie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Szybki start](../secrets/quick-create-java.md)|[Odwołanie](/javascript/api/@azure/keyvault-secrets/)<br>[Szybki start](../secrets/quick-create-node.md)|

Zobacz [Biblioteki klienckie](client-libraries.md) pakietów instalacyjnych i kodu źródłowego.

Aby uzyskać więcej informacji na temat Key Vault zabezpieczeń płaszczyzny danych, zobacz Key Vault Security overview ( [Omówienie zabezpieczeń).](security-overview.md)

### <a name="code-examples"></a>Przykłady kodu

Aby uzyskać pełne przykłady Key Vault z aplikacjami, zobacz:

- [Azure Key Vault przykłady kodu](https://azure.microsoft.com/resources/samples/?service=key-vault) — przykłady kodu dla Azure Key Vault. 

## <a name="how-tos"></a>Instrukcje

Następujące artykuły i scenariusze zawierają wskazówki dotyczące pracy z Azure Key Vault:

- [Uzyskiwanie dostępu Key Vault](access-behind-firewall.md) za zaporą — aby uzyskać dostęp do magazynu kluczy, aplikacja klienczka magazynu kluczy musi mieć dostęp do wielu punktów końcowych dla różnych funkcji.
- Jak wdrożyć certyfikaty na maszynach wirtualnych z Key Vault — [Windows,](../../virtual-machines/extensions/key-vault-windows.md) [Linux](../../virtual-machines/extensions/key-vault-linux.md) — aplikacja w chmurze uruchomiona na maszynie wirtualnej na platformie Azure wymaga certyfikatu. Jak uzyskać ten certyfikat na tej maszynie wirtualnej dzisiaj?
- [Wdrażanie certyfikatu aplikacji internetowej platformy Azure za pośrednictwem Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Przypisywanie zasad dostępu[(portal](assign-access-policy-cli.md)  |  [programu PowerShell interfejsu wiersza](assign-access-policy-powershell.md)  |  [polecenia).](assign-access-policy-portal.md) 
- [Sposób korzystania z Key Vault z](./key-vault-recovery.md) interfejsem wiersza polecenia przeprowadzi Cię przez cykl życia i użycia magazynu kluczy oraz różnych obiektów magazynu kluczy z włączonym usuwaniem nie soft-delete.
- Jak przekazywać bezpieczne wartości (takie jak [hasła)](../../azure-resource-manager/templates/key-vault-parameter.md) podczas wdrażania — gdy musisz przekazać bezpieczną wartość (na przykład hasło) jako parametr podczas wdrażania, możesz zapisać ją jako klucz tajny w udaniu Azure Key Vault i odwoływać się do wartości w innych szablonach Resource Manager.

## <a name="integrated-with-key-vault"></a>Integracja z Key Vault

Te artykuły są dotyczące innych scenariuszy i usług, które korzystają z usługi lub integrują się z Key Vault.

- [Szyfrowanie danych w spoczynku](../../security/fundamentals/encryption-atrest.md) umożliwia kodowanie (szyfrowanie) danych, gdy są utrwalane. Klucze szyfrowania danych są często szyfrowane za pomocą klucza szyfrowania klucza Azure Key Vault celu dalszego ograniczenia dostępu.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umożliwia kierownika własnego klucza dzierżawy. Na przykład zamiast zarządzania kluczem dzierżawy przez firmę Microsoft (ustawienie domyślne) możesz zarządzać własnym kluczem dzierżawy, aby spełnić określone przepisy dotyczące Twojej organizacji. Samodzielne zarządzanie kluczem dzierżawy określa się także mianem strategii BYOK (Bring Your Own Key), czyli „Przynieś własny klucz”.
- [Usługa Azure Private Link Service](private-link-service.md) umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault, Azure Storage i Azure Cosmos DB) oraz usług klienta/partnera hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.
- Key Vault z [usługą Event Grid](../../event-grid/event-schema-key-vault.md)  umożliwia użytkownikom powiadomienie o zmianie stanu klucza tajnego przechowywanego w magazynie kluczy. Możesz dystrybuować nową wersję wpisów tajnych do aplikacji lub obracać wpisy tajne bliskie wygaśnięcia, aby zapobiec omdniom.
- Wpisy tajne usługi [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) można chronić przed niechcianym dostępem w Key Vault.
- [Nawiązywanie połączenia z usługą Azure Storage Key Vault kluczem tajnym przechowywanym w usłudze DataBricks](./integrate-databricks-blob-storage.md)
- Konfigurowanie i uruchamianie dostawcy Azure Key Vault dla sterownika CSI magazynu wpisów [tajnych na](./key-vault-integrate-kubernetes.md) platformie Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Key Vault i pojęcia

- [Key Vault nieumyślne usuwanie](soft-delete-overview.md) opisuje funkcję umożliwiającą odzyskiwanie usuniętych obiektów bez względu na to, czy usunięcie było przypadkowe, czy zamierzone.
- [Key Vault klienta](overview-throttling.md) pozwala zorientować się na podstawowe pojęcia dotyczące ograniczania przepustowości i oferuje podejście do aplikacji.
- [Key Vault security worlds opisuje](overview-security-worlds.md) relacje między regionami i obszarami zabezpieczeń.

## <a name="social"></a>Funkcje społecznościowe

- [Key Vault bloga](/archive/blogs/kv/)
- [Key Vault Forum](https://aka.ms/kvforum)