---
title: Bezpieczny dostęp do zarządzanego modułu HSM Azure Key Vault zarządzanego modułu HSM
description: Dowiedz się, jak zabezpieczyć dostęp do zarządzanego modułu HSM przy użyciu usług Azure RBAC i lokalnego zarządzanego modułu HSM RBAC
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 18ffa0f878effda8888200c13ab312851aaebdcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91000782"
---
# <a name="secure-access-to-your-managed-hsms"></a>Bezpieczny dostęp do zarządzanego sprzętowych modułów zabezpieczeń

Azure Key Vault zarządzany moduł HSM to usługa w chmurze, która chroni klucze szyfrowania. Ponieważ te dane są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do zarządzanego sprzętowych modułów zabezpieczeń, zezwalając na dostęp do niego tylko autoryzowanym aplikacjom i użytkownikom. Ten artykuł zawiera omówienie modelu kontroli dostępu zarządzanego modułu HSM. W tym artykule wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do zarządzanego sprzętowych modułów zabezpieczeń.

Ten samouczek przeprowadzi Cię przez prosty przykład pokazujący, jak osiągnąć Rozdzielenie obowiązków i kontroli dostępu przy użyciu funkcji RBAC platformy Azure i lokalnego zarządzanego modułu HSM. Zobacz [zarządzana kontrola dostępu modułu HSM](access-control.md) , aby dowiedzieć się więcej o zarządzanym modelu kontroli dostępu modułu HSM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować następującymi elementami:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzany moduł HSM w ramach subskrypcji. Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu aprowizacji i aktywowania zarządzanego modułu HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="example"></a>Przykład

W tym przykładzie tworzymy aplikację, która używa klucza RSA 2 048-bitowego do operacji podpisywania. Nasza aplikacja działa na maszynie wirtualnej platformy Azure (VM) z [tożsamością zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md). Klucz RSA używany do podpisywania jest przechowywany w naszym zarządzanym module HSM.

Zidentyfikowano następujące role, które zarządzają, wdrażają i przeprowadźą inspekcję aplikacji:

- **Zespół ds. zabezpieczeń**: personel działu IT z biura firmy CSO (Dyrektor ds. zabezpieczeń) lub podobnych współautorów. Zespół ds. zabezpieczeń jest odpowiedzialny za odpowiednie przechowywanie kluczy. Klucze RSA lub klucze we do podpisywania oraz klucze RSA lub AES na potrzeby szyfrowania danych.
- **Deweloperzy i operatorzy**: pracownicy, którzy opracowują aplikację i wdrażają ją na platformie Azure. Członkowie tego zespołu nie są częścią personelu zabezpieczeń. Nie powinny mieć dostępu do poufnych danych, takich jak klucze RSA. Tylko wdrażana aplikacja powinna mieć dostęp do danych poufnych.
- **Audytorzy**: Ta rola jest dla współautorów, którzy nie są członkami rozwoju ani ogólnego personelu IT. Zapoznają się z użyciem i konserwacją certyfikatów, kluczy i wpisów tajnych w celu zapewnienia zgodności ze standardami zabezpieczeń.

Istnieje inna rola, która jest poza zakresem naszej aplikacji: Administrator subskrypcji (lub grupy zasobów). Administrator subskrypcji konfiguruje uprawnienia dostępu początkowego do zespołu ds. zabezpieczeń. Przyznają one dostęp do zespołu ds. zabezpieczeń przy użyciu grupy zasobów, która zawiera zasoby wymagane przez aplikację.

Musimy autoryzować następujące operacje dla naszych ról:

**Zespół ds. zabezpieczeń**
- Utwórz zarządzany moduł HSM.
- Pobierz zarządzaną domenę zabezpieczeń modułu HSM (na potrzeby odzyskiwania po awarii)
- Włącz rejestrowanie.
- Generuj lub Importuj klucze
- Utwórz zarządzane kopie zapasowe modułu HSM na potrzeby odzyskiwania po awarii.
- Ustaw zarządzane lokalne RBAC modułu HSM, aby przyznać uprawnienia użytkownikom i aplikacjom dla określonych operacji.
- Okresowe rzutowanie kluczy.

**Deweloperzy i operatorzy**
- Pobierz odwołanie (identyfikator URI klucza) z zespołu ds. zabezpieczeń klucz RSA używany do podpisywania.
- Opracowywanie i wdrażanie aplikacji, która umożliwia programistyczne uzyskiwanie dostępu do klucza.

**Audytorzy**
- Przejrzyj daty wygaśnięcia kluczy, aby upewnić się, że klucze są aktualne
- Monitorowanie przypisań ról w celu zapewnienia dostępu do kluczy tylko autoryzowanym użytkownikom/aplikacjom
- Przejrzyj dzienniki zarządzanego modułu HSM, aby potwierdzić odpowiednie użycie kluczy zgodnych ze standardami zabezpieczeń danych.

Poniższa tabela zawiera podsumowanie przypisań ról do zespołów i zasobów, aby uzyskać dostęp do zarządzanego modułu HSM.

| Rola | Rola płaszczyzny zarządzania | Rola płaszczyzny danych |
| --- | --- | --- |
| Zespół ds. zabezpieczeń | Współautor zarządzanego modułu HSM | Zarządzany administrator modułu HSM |
| Deweloperzy i operatorzy | Brak | Brak |
| Audytorzy | Brak | Zarządzany audytor kryptograficzny modułu HSM |
| Zarządzana identyfikacja maszyny wirtualnej używanej przez aplikację| Brak | Zarządzany użytkownik kryptograficzny modułu HSM |
| Zarządzana tożsamość konta magazynu używanego przez aplikację| Brak| Szyfrowanie zarządzanej usługi modułu HSM |

Trzy role zespołu potrzebują dostępu do innych zasobów wraz z zarządzanymi uprawnieniami modułu HSM. Aby wdrożyć maszyny wirtualne (lub Web Apps funkcję Azure App Service), deweloperzy i operatorzy potrzebują `Contributor` dostępu do tych typów zasobów. Audytorzy muszą mieć dostęp do odczytu do konta magazynu, w którym są przechowywane zarządzane dzienniki modułu HSM.

Aby przypisać role płaszczyzny zarządzania (RBAC), można użyć Azure Portal lub dowolnego innego interfejsu zarządzania, takiego jak interfejs wiersza polecenia platformy Azure lub Azure PowerShell. Aby przypisać zarządzane role płaszczyzny danych modułu HSM, należy użyć interfejsu wiersza polecenia platformy Azure.

Fragmenty kodu interfejsu wiersza polecenia platformy Azure w tej sekcji zostały skompilowane przy użyciu następujących założeń:

- Administrator Azure Active Directory utworzył grupy zabezpieczeń, aby reprezentować trzy role: zespół zabezpieczeń contoso, DevOps aplikacji firmy Contoso i audytorów aplikacji firmy Contoso. Administrator dodał użytkowników do odpowiednich grup.
- Wszystkie zasoby znajdują się w grupie zasobów **ContosoAppRG** .
- Zarządzane dzienniki modułu HSM są przechowywane na koncie magazynu **contosologstorage** .
- Zarządzany moduł HSM **ContosoMHSM** i konto magazynu **contosologstorage** znajdują się w tej samej lokalizacji platformy Azure.

Administrator subskrypcji przypisuje `Managed HSM Contributor` rolę do zespołu ds. zabezpieczeń. Ta rola pozwala zespołowi zabezpieczeń zarządzać istniejącymi zarządzanymi sprzętowych modułów zabezpieczeń i tworzyć nowe. Jeśli istnieją już zarządzane sprzętowych modułów zabezpieczeń, muszą oni mieć przypisaną rolę "zarządzani Administratorzy modułu HSM", aby móc zarządzać nimi.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

Zespół ds. zabezpieczeń konfiguruje rejestrowanie i przypisuje role do audytorów i aplikacji maszyny wirtualnej.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

Ten samouczek zawiera tylko akcje dotyczące kontroli dostępu w większości części. Inne akcje i operacje związane z wdrażaniem aplikacji na maszynie wirtualnej, Włączanie szyfrowania przy użyciu klucza zarządzanego przez klienta dla konta magazynu, tworzenie zarządzanego modułu HSM nie są tutaj wyświetlane, aby zachować przykład ukierunkowany na kontrolę dostępu i zarządzanie rolami.

Nasz przykład opisuje prosty scenariusz. Scenariusze życiowe mogą być bardziej skomplikowane. Możesz dostosować uprawnienia do magazynu kluczy w zależności od potrzeb. Zakładamy, że zespół ds. zabezpieczeń zawiera odwołania do kluczy i wpisów tajnych (identyfikatorów URI i odcisków palców), które są używane przez personel DevOps w swoich aplikacjach. Deweloperzy i operatorzy nie potrzebują dostępu do płaszczyzny danych. Firma Microsoft koncentruje się na sposobie zabezpieczania magazynu kluczy. Zadawaj podobne kwestie w przypadku zabezpieczania [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/security/), [kont magazynu](../../storage/blobs/security-recommendations.md)i innych zasobów platformy Azure.

## <a name="resources"></a>Zasoby

- [Dokumentacja kontroli RBAC na platformie Azure](../../role-based-access-control/overview.md)
- [Azure RBAC: Wbudowane role](../../role-based-access-control/built-in-roles.md)
- [Zarządzanie usługą Azure RBAC przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [co to jest zarządzany moduł HSM?](overview.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia zarządzanego modułu HSM, zobacz [Rejestrowanie zarządzanego modułu HSM](logging.md).
