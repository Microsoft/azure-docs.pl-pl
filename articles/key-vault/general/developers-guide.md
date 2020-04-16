---
title: Przewodnik dewelopera usługi Azure Key Vault
description: Deweloperzy mogą używać usługi Azure Key Vault do zarządzania kluczami kryptograficznymi w środowisku platformy Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: 18e1ab1d01394d585cfb06ced8c5fbac04114177
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431958"
---
# <a name="azure-key-vault-developers-guide"></a>Przewodnik dewelopera usługi Azure Key Vault

Usługa Key Vault umożliwia bezpieczny dostęp do poufnych informacji z poziomu aplikacji:

- Klucze i wpisy tajne są chronione bez konieczności samodzielnego pisania kodu i można łatwo z nich korzystać z aplikacji.
- Możesz mieć własnych klientów i zarządzać własnymi kluczami, dzięki czemu możesz skoncentrować się na dostarczaniu podstawowych funkcji oprogramowania. W ten sposób aplikacje nie będą ponosić odpowiedzialności ani potencjalnej odpowiedzialności za klucze i wpisy tajne klientów.
- Aplikacja może używać kluczy do podpisywania i szyfrowania, ale utrzymuje zarządzanie kluczami zewnętrzną z aplikacji, dzięki czemu rozwiązanie jest odpowiednie jako aplikacja rozproszona geograficznie.
- Zarządzanie certyfikatami usługi Key Vault. Aby uzyskać więcej informacji, zobacz [Certyfikaty](../certificates/about-certificates.md)

Aby uzyskać bardziej ogólne informacje na temat usługi Azure Key Vault, zobacz [Co to jest magazyn kluczy](overview.md)).

## <a name="public-previews"></a>Publiczne podglądy

Okresowo publikujemy publiczną wersję zapoznawczą nowej funkcji magazynu kluczy. Wypróbuj je i daj nam znać, co myślisz za pośrednictwem, azurekeyvault@microsoft.comnasz adres e-mail opinii.

## <a name="creating-and-managing-key-vaults"></a>Tworzenie magazynów kluczy i zarządzanie nimi

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Tożsamości zarządzane dla zasobów platformy Azure upraszcza rozwiązanie tego problemu, nadając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie. 

Aby uzyskać więcej informacji na temat tożsamości zarządzanych dla zasobów platformy Azure, zobacz [omówienie tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md). Aby uzyskać więcej informacji na temat pracy z usługą Azure AD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md).

Przed rozpoczęciem pracy z kluczami, wpisami tajnymi lub certyfikatami w magazynie kluczy utworzysz magazyn kluczy i zarządzasz nim za pomocą interfejsu wiersza polecenia, programu PowerShell, szablonów menedżera zasobów lub REST, zgodnie z opisem w następujących artykułach:

- [Tworzenie przechowalni kluczy i zarządzanie nimi za pomocą interfejsu wiersza polecenia](../secrets/quick-create-cli.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi za pomocą programu PowerShell](../secrets/quick-create-powershell.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi za pomocą portu azure](../secrets/quick-create-portal.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi za pomocą języka Python](../secrets/quick-create-python.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi za pomocą oprogramowania Java](../secrets/quick-create-java.md)
- [Tworzenie przechowalni kluczy i zarządzanie nimi za pomocą pliku Node.js](../secrets/quick-create-node.md)
- [Tworzenie magazynów kluczy i zarządzanie nimi za pomocą platformy .NET (sdk w wersji 4)](../secrets/quick-create-net.md)
- [Tworzenie magazynu kluczy i dodawanie klucza tajnego za pomocą szablonu usługi Azure Resource Manager](../secrets/quick-create-template.md)
- [Tworzenie przechowalni kluczy i zarządzanie nimi za pomocą REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kodowanie za pomocą przechowalni kluczy

System zarządzania przechowalnią kluczy dla programistów składa się z kilku interfejsów. Ta sekcja zawiera łącza do wszystkich języków, a także niektóre przykłady kodu. 

### <a name="supported-programming-and-scripting-languages"></a>Obsługiwane języki programowania i skryptów

#### <a name="rest"></a>REST

Wszystkie zasoby usługi Key Vault są dostępne za pośrednictwem interfejsu REST; skarbce, klucze, sekrety itp. 

[Odwołanie do interfejsu API REST magazynu kluczy](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Odwołanie do interfejsu API platformy .NET dla usługi Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Aby uzyskać więcej informacji na temat wersji 2.x sdk .NET, zobacz [informacje o wersji](dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK dla magazynu kluczy](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

W pliku Node.js interfejs API zarządzania usługi Key Vault i interfejs API obiektów usługi Key Vault są oddzielne. Poniższy artykuł omówienia daje dostęp do obu. 

[Moduły usługi Azure Key Vault dla node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Biblioteki usługi Azure Key Vault dla języka Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[Narzędzie cli platformy Azure dla magazynu kluczy](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Usługa Azure PowerShell dla magazynu kluczy](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Przykłady kodu

Aby uzyskać pełne przykłady użycia usługi Key Vault z aplikacjami, zobacz:

- [Przykłady kodu usługi Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) — przykłady kodu dla usługi Azure Key Vault. 
- [Użyj usługi Azure Key Vault z aplikacji sieci Web](../secrets/quick-create-net.md) — samouczek, aby dowiedzieć się, jak korzystać z usługi Azure Key Vault z aplikacji sieci web na platformie Azure. 

## <a name="how-tos"></a>Instrukcje

Następujące artykuły i scenariusze zawierają wskazówki dotyczące pracy z usługą Azure Key Vault:

- [Zmień identyfikator dzierżawy magazynu kluczy po przejściu subskrypcji](subscription-move-fix.md) — podczas przenoszenia subskrypcji platformy Azure z dzierżawy A do dzierżawy B istniejące magazyny kluczy są niedostępne przez podmioty zabezpieczeń (użytkowników i aplikacje) w dzierżawie B. Rozwiąż to za pomocą tego przewodnika.
- [Uzyskiwanie dostępu do magazynu kluczy za zaporą](access-behind-firewall.md) — aby uzyskać dostęp do magazynu kluczy, aplikacja kliencka magazynu kluczy musi mieć dostęp do wielu punktów końcowych dla różnych funkcji.
- [Jak wygenerować i przenieść klucze chronione przez moduł HSM dla usługi Azure Key Vault](../keys/hsm-protected-keys.md) — pomoże ci to zaplanować, wygenerować, a następnie przenieść własne klucze chronione przez moduł HSM do użycia z usługą Azure Key Vault.
- [Jak przekazać bezpieczne wartości (takie jak hasła) podczas wdrażania](../../azure-resource-manager/templates/key-vault-parameter.md) — gdy trzeba przekazać bezpieczną wartość (na przykład hasło) jako parametr podczas wdrażania, można przechowywać tę wartość jako klucz tajny w magazynie azure key vault i odwoływać się do wartości w innych szablonach Menedżera zasobów.
- [Jak używać usługi Key Vault do rozszerzalnego zarządzania kluczami za pomocą programu SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) — łącznik programu SQL Server dla usługi Azure Key Vault umożliwia programowi SQL Server i sql-in-a-vm korzystanie z usługi Azure Key Vault jako dostawcy ekm (Extensible Key Management) w celu ochrony kluczy szyfrowania dla łączy aplikacji; Przezroczyste szyfrowanie danych, szyfrowanie kopii zapasowych i szyfrowanie na poziomie kolumny.
- [Jak wdrożyć certyfikaty na maszynach wirtualnych z usługi Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) — aplikacja w chmurze uruchomiona na maszynie wirtualnej na platformie Azure wymaga certyfikatu. Jak uzyskać ten certyfikat do tej maszyny Wirtualnej dzisiaj?
- [Jak skonfigurować magazyn kluczy z rotacją kluczy od końca do końca —](../secrets/key-rotation-log-monitoring.md) w tym artykule opisano sposób konfigurowania rotacji kluczy i inspekcji za pomocą usługi Azure Key Vault.
- [Wdrażanie certyfikatu aplikacji Azure Web App za pośrednictwem magazynu kluczy]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) zapewnia instrukcje krok po kroku dotyczące wdrażania certyfikatów przechowywanych w magazynie kluczy w ramach oferty [certyfikatów usługi App Service.](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)
- [Udzielanie uprawnień wielu aplikacjom w celu uzyskania dostępu do magazynu kluczy](group-permissions-for-apps.md) Zasady kontroli dostępu usługi Key Vault obsługują maksymalnie 1024 wpisy. Można jednak utworzyć grupę zabezpieczeń usługi Azure Active Directory. Dodaj wszystkie skojarzone jednostki usługi do tej grupy zabezpieczeń, a następnie udzielij dostępu do tej grupy zabezpieczeń do usługi Key Vault.
- Aby uzyskać więcej wskazówek dotyczących poszczególnych zadań dotyczących integrowania i używania magazynów kluczy za pomocą platformy Azure, zobacz [przykłady szablonów usługi Azure Resource Manager firmy Ryan Jones dla usługi Key Vault.](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)
- [Sposób używania funkcji usuwania nietrwałego w uchodźstości w uchwale kluczy](soft-delete-cli.md) prowadzi użytkownika przez okres użytkowania i cyklu życia magazynu kluczy oraz różnych obiektów magazynu kluczy z włączonym usuwaniem nietrwałym.
- [Sposób używania funkcji usuwania programów Key Vault za pomocą programu PowerShell](soft-delete-powershell.md) prowadzi użytkownika przez okres użytkowania i cyklu życia magazynu kluczy oraz różnych obiektów magazynu kluczy z włączonym usuwaniem nietrwałym.

## <a name="integrated-with-key-vault"></a>Zintegrowany z przechowalnią kluczy

Te artykuły dotyczą innych scenariuszy i usług, które używają lub integrują się z usługą Key Vault.

- [Usługa Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) wykorzystuje standardową w branży funkcję [Funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcję [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowane z usługą Azure Key Vault, aby ułatwić kontrolowanie kluczy szyfrowania dysku i zarządzania nimi w ramach subskrypcji magazynu kluczy, zapewniając jednocześnie, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w magazynie platformy Azure.
- [Usługa Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) udostępnia opcję szyfrowania danych przechowywanych na koncie. W przypadku zarządzania kluczami usługa Data Lake Store udostępnia dwa tryby zarządzania głównymi kluczami szyfrowania (MEK), które są wymagane do odszyfrowywania wszelkich danych przechowywanych w magazynie Usługi Data Lake Store. Możesz pozwolić usługi Data Lake Store zarządzać meks dla Ciebie lub wybrać, aby zachować własność MEK przy użyciu konta usługi Azure Key Vault. Podczas tworzenia konta usługi Data Lake Store można określić tryb zarządzania kluczami.
- [Usługa Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umożliwia zarządzanie własnym kluczem dzierżawy. Na przykład zamiast firmy Microsoft zarządzania kluczem dzierżawy (domyślnie), można zarządzać własny klucz dzierżawy do zgodności z określonymi przepisami, które mają zastosowanie do organizacji. Samodzielne zarządzanie kluczem dzierżawy określa się także mianem strategii BYOK (Bring Your Own Key), czyli „Przynieś własny klucz”.

## <a name="key-vault-overviews-and-concepts"></a>Przeglądy i koncepcje usługi Key Vault

- [Zachowanie usuwania nietrwałego w magazynie kluczy](overview-soft-delete.md)) opisuje funkcję, która umożliwia odzyskiwanie usuniętych obiektów, niezależnie od tego, czy usunięcie było przypadkowe, czy zamierzone.
- [Ograniczanie przepustowości klienta usługi Key Vault](overview-throttling.md) wpisuje cię w podstawowe pojęcia ograniczania przepustowości i oferuje podejście do aplikacji.
- [Key Vault storage account keys overview](../secrets/overview-storage-keys.md)) opisuje klucze kont usługi Azure Storage integracji usługi Key Vault.
- [Światy zabezpieczeń magazynu kluczy](overview-security-worlds.md) opisują relacje między regionami a obszarami zabezpieczeń.

## <a name="social"></a>Społeczności

- [Blog magazynu kluczy](https://aka.ms/kvblog)
- [Forum magazynu kluczy](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Obsługa bibliotek

- [Biblioteka podstawowa usługi Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) udostępnia interfejsy **IKey** i **IKeyResolver** do lokalizowania kluczy z identyfikatorów i wykonywania operacji za pomocą kluczy.
- [Rozszerzenia usługi Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) zapewniają rozszerzone możliwości usługi Azure Key Vault.
