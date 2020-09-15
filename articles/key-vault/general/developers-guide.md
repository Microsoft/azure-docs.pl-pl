---
title: Przewodnik dewelopera usługi Azure Key Vault
description: Deweloperzy mogą używać Azure Key Vault do zarządzania kluczami kryptograficznymi w środowisku Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: e6ee8ce065361ac27bba0e80349eb5e1d1877526
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532294"
---
# <a name="azure-key-vault-developers-guide"></a>Przewodnik dewelopera usługi Azure Key Vault

Key Vault pozwala bezpiecznie uzyskać dostęp do poufnych informacji z aplikacji:

- Klucze i wpisy tajne są chronione bez konieczności pisania kodu i można z nich łatwo korzystać z aplikacji.
- Możesz korzystać z własnych klientów i zarządzać własnymi kluczami, aby skoncentrować się na dostarczaniu podstawowych funkcji oprogramowania. W ten sposób Twoje aplikacje nie będą własnością odpowiedzialności ani potencjalnej odpowiedzialności za klucze dzierżawy i wpisy tajne klientów.
- Aplikacja może korzystać z kluczy do podpisywania i szyfrowania, a jednocześnie zarządzaniem kluczami zewnętrznymi z poziomu aplikacji, dzięki czemu rozwiązanie może być odpowiednie jako aplikacji rozproszonej geograficznie.
- Zarządzanie certyfikatami Key Vault. Aby uzyskać więcej informacji, zobacz [Certyfikaty](../certificates/about-certificates.md)

Aby uzyskać więcej ogólnych informacji na temat Azure Key Vault, zobacz [co to jest Key Vault](overview.md).

## <a name="public-previews"></a>Publiczne podglądy

Okresowo firma Microsoft udostępnia publiczną wersję zapoznawczą nowej funkcji Key Vault. Wypróbuj je i daj nam znać, co myślisz, za pośrednictwem azurekeyvault@microsoft.com adresu e-mail z opiniami.

## <a name="creating-and-managing-key-vaults"></a>Tworzenie magazynów kluczy i zarządzanie nimi

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Zarządzane tożsamości dla zasobów platformy Azure ułatwiają rozwiązanie tego problemu, dzięki czemu usługi platformy Azure są automatycznie zarządzane tożsamości w Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie. 

Aby uzyskać więcej informacji na temat zarządzanych tożsamości dla zasobów platformy Azure, zobacz [Omówienie zarządzanych tożsamości](../../active-directory/managed-identities-azure-resources/overview.md). Aby uzyskać więcej informacji na temat pracy z usługą Azure AD, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md).

Przed rozpoczęciem pracy z kluczami, wpisami tajnymi lub certyfikatami w magazynie kluczy utworzysz Magazyn kluczy i zarządzasz nim za pomocą interfejsu wiersza polecenia, programu PowerShell, Menedżer zasobów szablonów lub REST, zgodnie z opisem w następujących artykułach:

- [Tworzenie magazynów kluczy i zarządzanie nimi za pomocą interfejsu wiersza polecenia](quick-create-cli.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi przy użyciu programu PowerShell](quick-create-powershell.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi za pomocą Azure Portal](quick-create-portal.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi przy użyciu interfejsu REST](/rest/api/keyvault/vaults/createorupdate)

### <a name="set-and-retrieve-secrets"></a>Ustawianie i pobieranie wpisów tajnych

- [Ustawianie i pobieranie wpisu tajnego za pomocą interfejsu wiersza polecenia](../secrets/quick-create-cli.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu programu PowerShell](../secrets/quick-create-powershell.md)
- [Ustawianie i pobieranie klucza tajnego za pomocą Azure Portal](../secrets/quick-create-portal.md)
- [Operacje na wpisach tajnych przy użyciu interfejsu REST](/rest/api/keyvault/#secret-operations)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu języka Python](../secrets/quick-create-python.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu języka Java](../secrets/quick-create-java.md)
- [Ustawianie i pobieranie klucza tajnego za pomocą Node.js](../secrets/quick-create-node.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu platformy .NET (zestaw SDK v4)](../secrets/quick-create-net.md)
- [Tworzenie magazynu kluczy i Dodawanie wpisu tajnego za pomocą szablonu Azure Resource Manager](../secrets/quick-create-template.md)

### <a name="set-and-retrieve-keys"></a>Ustawianie i pobieranie kluczy

- [Ustawianie i pobieranie klucza przy użyciu interfejsu wiersza polecenia](../keys/quick-create-cli.md)
- [Ustawianie i pobieranie klucza przy użyciu programu PowerShell](../keys/quick-create-powershell.md)
- [Ustawianie i pobieranie klucza z Azure Portal](../keys/quick-create-portal.md)
- [Operacje na kluczach przy użyciu interfejsu REST](/rest/api/keyvault/#key-operations)
- [Ustawianie i pobieranie klucza przy użyciu języka Python](../secrets/quick-create-python.md)

### <a name="set-and-retrieve-certificates"></a>Ustawianie i pobieranie certyfikatów
- [Ustawianie i pobieranie certyfikatu za pomocą interfejsu wiersza polecenia](../certificates/quick-create-cli.md)
- [Ustawianie i pobieranie certyfikatu przy użyciu programu PowerShell](../certificates/quick-create-powershell.md)
- [Ustawianie i pobieranie certyfikatu z Azure Portal](../certificates/quick-create-portal.md)
- [Operacje na certyfikatach przy użyciu interfejsu REST](/rest/api/keyvault/#certificate-operations)
- [Ustawianie i pobieranie certyfikatu przy użyciu języka Python](../certificates/quick-create-python.md)

## <a name="coding-with-key-vault"></a>Kodowanie za pomocą Key Vault

System zarządzania Key Vault dla programistów składa się z kilku interfejsów. Ta sekcja zawiera linki do wszystkich języków, a także przykłady kodu. 

### <a name="supported-programming-and-scripting-languages"></a>Obsługiwane języki programowania i skryptów

#### <a name="rest"></a>REST

Wszystkie zasoby Key Vault są dostępne za pomocą interfejsu REST; magazyny, klucze, wpisy tajne itp. 

[Key Vault dokumentacja interfejsu API REST](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Dokumentacja interfejsu API platformy .NET dla Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

#### <a name="java"></a>Java

[Zestaw Java SDK dla Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

W Node.js interfejs API zarządzania Key Vault i interfejs API obiektu Key Vault są oddzielone. Poniższy artykuł omówienia zapewnia dostęp do obu tych programów. 

[Moduły Azure Key Vault dla Node.js](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest)

#### <a name="python"></a>Python

[Biblioteki Azure Key Vault dla języka Python](https://docs.microsoft.com/python/api/overview/azure/key-vault-index?view=azure-python)

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[Interfejs wiersza polecenia platformy Azure dla Key Vault](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell Key Vault](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Przykłady kodu

Aby zapoznać się z kompletnymi przykładami dotyczącymi używania Key Vault z aplikacjami, zobacz:

- [Przykłady kodu Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) — przykłady kodu dla Azure Key Vault. 

## <a name="how-tos"></a>Instrukcje

Poniższe artykuły i scenariusze zapewniają wskazówki dotyczące zadań związanych z pracą z Azure Key Vault:

- [Zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](move-subscription.md) — po przeniesieniu subskrypcji platformy Azure z dzierżawy A do dzierżawy b istniejące magazyny kluczy są niedostępne dla podmiotów zabezpieczeń (użytkowników i aplikacji) w dzierżawie b. Rozwiąż ten problem, korzystając z tego przewodnika.
- Dostęp do [Key Vault za zaporą](access-behind-firewall.md) — Aby uzyskać dostęp do magazynu kluczy, aplikacja kliencka magazynu kluczy musi mieć dostęp do wielu punktów końcowych dla różnych funkcji.
- [Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault](../keys/hsm-protected-keys.md) — ułatwia to planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu ich użycia z Azure Key Vault.
- [Jak przekazać bezpieczne wartości (takie jak hasła) podczas wdrażania](../../azure-resource-manager/templates/key-vault-parameter.md) — Jeśli chcesz przekazać bezpieczną wartość (na przykład hasło) jako parametr podczas wdrażania, możesz zapisać tę wartość jako klucz tajny w Azure Key Vault i odwołać się do wartości w innych szablonach Menedżer zasobów.
- [Jak używać Key Vault do rozszerzonego zarządzania kluczami z SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -SQL Server Connector dla Azure Key Vault umożliwia SQL Server i SQL-in-a-VM korzystanie z usługi Azure Key Vault jako dostawcy rozszerzalnego zarządzania kluczami (EKM) w celu ochrony kluczy szyfrowania dla aplikacji. Transparent Data Encryption, szyfrowanie kopii zapasowej i szyfrowanie na poziomie kolumny.
- [Jak wdrażać certyfikaty na maszynach wirtualnych z Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) — aplikacja w chmurze działająca na maszynie wirtualnej na platformie Azure wymaga certyfikatu. Jak uzyskać ten certyfikat na tę maszynę wirtualną dzisiaj?
- [Wdrażanie certyfikatu aplikacji sieci Web platformy Azure za pomocą Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) zawiera instrukcje krok po kroku dotyczące wdrażania certyfikatów przechowywanych w Key Vault w ramach oferty [certyfikat usługi App Service](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) .
- Przypisz zasady dostępu (Portal[CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  [Portal](assign-access-policy-portal.md)). Key Vault obsługuje do 1024 wpisów zasad dostępu. Aby pozostało w tym limicie dla użytkowników, Utwórz Azure Active Directory grupy zabezpieczeń, Dodaj wszystkie skojarzone jednostki usługi do tej grupy, a następnie nadaj grupie dostęp do Key Vault.
- Aby uzyskać więcej wskazówek dotyczących zadań związanych z integracją i użyciem magazynów kluczy z platformą Azure, zobacz artykuł [Ryan Kowalski "Azure Resource Manager Template przykłady for Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Jak używać narzędzia Key Vault Soft-Delete with CLI](soft-delete-cli.md) przeprowadzi Cię przez użycie i cykl życia magazynu kluczy oraz różne obiekty magazynu kluczy z włączonym usuwaniem nietrwałego.
- [Jak używać narzędzia Key Vault Soft-DELETE w programie PowerShell](soft-delete-powershell.md) przeprowadzi Cię przez użycie i cykl życia magazynu kluczy i różne obiekty magazynu kluczy z włączonym usuwaniem nietrwałego.

## <a name="integrated-with-key-vault"></a>Integracja z Key Vault

W tych artykułach przedstawiono inne scenariusze i usługi, które korzystają z Key Vault lub integrują się z nimi.

- [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) korzysta z funkcji funkcji [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standardowego w branży w systemie Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych. Rozwiązanie jest zintegrowane z Azure Key Vault, które ułatwiają sterowanie kluczami szyfrowania dysków i wpisami tajnymi w ramach subskrypcji magazynu kluczy oraz zarządzanie nimi, przy jednoczesnym zapewnieniu, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze Azure Storage.
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) zapewnia opcję szyfrowania danych przechowywanych na koncie. W przypadku zarządzania kluczami Data Lake Store zapewnia dwa tryby zarządzania głównymi kluczami szyfrowania (głównymi kluczami szyfrowania), które są wymagane do odszyfrowania danych przechowywanych w Data Lake Store. Możesz pozwolić, Data Lake Store zarządzać głównymi kluczami szyfrowaniaem, lub wybrać opcję zachowania własności głównymi kluczami szyfrowania przy użyciu konta Azure Key Vault. Podczas tworzenia konta Data Lake Store należy określić tryb zarządzania kluczami.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umożliwia kierownika własnego klucza dzierżawy. Na przykład zamiast firmy Microsoft zarządzającej kluczem dzierżawy (domyślnie) można zarządzać własnym kluczem dzierżawy w celu zapewnienia zgodności z określonymi przepisami obowiązującymi w organizacji. Samodzielne zarządzanie kluczem dzierżawy określa się także mianem strategii BYOK (Bring Your Own Key), czyli „Przynieś własny klucz”.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault przeglądy i koncepcje

- [Key Vault zachowanie usuwania nietrwałego](soft-delete-overview.md) opisuje funkcję, która umożliwia Odzyskiwanie usuniętych obiektów, niezależnie od tego, czy usunięcie było przypadkowe czy celowe.
- [Key Vault ograniczanie klientów](overview-throttling.md) ukierunkowane na podstawowe koncepcje ograniczania i oferuje podejście do aplikacji.
- [Key Vault światy zabezpieczeń](overview-security-worlds.md) opisuje relacje między regionami i obszarami zabezpieczeń.

## <a name="social"></a>Funkcje społecznościowe

- [Blog Key Vault](https://aka.ms/kvblog)
- [Forum Key Vault](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Biblioteki pomocnicze

- [Biblioteka Microsoft Azure Key Vault Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) udostępnia interfejsy **iKey** i **IKeyResolver** do lokalizowania kluczy z identyfikatorów i wykonywania operacji z kluczami.
- [Rozszerzenia Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) zapewniają rozszerzone możliwości Azure Key Vault.
