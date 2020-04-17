---
title: Zakończenie protokołu TLS za pomocą certyfikatów usługi Azure Key Vault
description: Dowiedz się, jak zintegrować usługę Azure Application Gateway z usługą Key Vault dla certyfikatów serwera dołączonych do odbiorników obsługujących protokół HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: be558c3e3a68ce6c194dcf98d8f5ff92c4c14edb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457828"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Zakończenie protokołu TLS z certyfikatami Usługi Key Vault

[Usługa Azure Key Vault](../key-vault/general/overview.md) to tajny magazyn zarządzany przez platformę, którego można używać do ochrony wpisów tajnych, kluczy i certyfikatów TLS/SSL. Usługa Azure Application Gateway obsługuje integrację z usługą Key Vault dla certyfikatów serwera dołączonych do odbiorników obsługujących protokół HTTPS. Ta obsługa jest ograniczona do jednostki SKU w wersji 2 bramy aplikacji.

Integracja z key vault oferuje dwa modele zakończenia protokołu TLS:

- Można jawnie podać certyfikaty TLS/SSL dołączone do odbiornika. Ten model jest tradycyjnym sposobem przekazywania certyfikatów TLS/SSL do bramy aplikacji w celu zakończenia protokołu TLS.
- Opcjonalnie można podać odwołanie do istniejącego certyfikatu usługi Key Vault lub klucza tajnego podczas tworzenia odbiornika obsługującego protokół HTTPS.

Integracja bramy aplikacji z usługą Key Vault oferuje wiele korzyści, w tym:

- Większe bezpieczeństwo, ponieważ certyfikaty TLS/SSL nie są bezpośrednio obsługiwane przez zespół deweloperów aplikacji. Integracja umożliwia oddzielne zespołowi zabezpieczeń:
  * Konfigurowanie bram aplikacji.
  * Sterowanie cyklami życia bramy aplikacji.
  * Udziel uprawnień do wybranych bram aplikacji, aby uzyskać dostęp do certyfikatów przechowywanych w magazynie kluczy.
- Obsługa importowania istniejących certyfikatów do magazynu kluczy. Możesz też używać interfejsów API usługi Key Vault do tworzenia nowych certyfikatów i zarządzania nimi u zaufanych partnerów usługi Key Vault.
- Obsługa automatycznego odnawiania certyfikatów przechowywanych w magazynie kluczy.

Brama aplikacji obsługuje obecnie tylko certyfikaty zweryfikowane programowo. Certyfikaty zweryfikowane przez sprzętowy moduł zabezpieczeń (HSM) nie są obsługiwane. Po skonfigurowaniu bramy aplikacji do używania certyfikatów usługi Key Vault jej wystąpienia pobierają certyfikat z usługi Key Vault i instalują je lokalnie w celu zakończenia protokołu TLS. Wystąpienia sondują również magazyn kluczy w odstępach 24-godzinnych, aby pobrać odnowioną wersję certyfikatu, jeśli istnieje. Jeśli zostanie znaleziony zaktualizowany certyfikat, certyfikat TLS/SSL aktualnie skojarzony z odbiornikiem HTTPS zostanie automatycznie obrócony.

> [!NOTE]
> Portal Azure obsługuje tylko certyfikaty keyvault, a nie wpisy tajne. Brama aplikacji nadal obsługuje odwoływanie się do wpisów tajnych z keyvault, ale tylko za pośrednictwem zasobów innych niż portal, takich jak PowerShell, CLI, API, szablony ARM itp. 

## <a name="how-integration-works"></a>Jak działa integracja

Integracja bramy aplikacji z usługą Key Vault wymaga trzyetapowego procesu konfiguracji:

1. **Tworzenie tożsamości zarządzanej przypisanej przez użytkownika**

   Tworzenie lub ponowne użycie istniejącej tożsamości zarządzanej przypisanej przez użytkownika, której brama aplikacji używa do pobierania certyfikatów z usługi Key Vault w Twoim imieniu. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md). Ten krok tworzy nową tożsamość w dzierżawie usługi Azure Active Directory. Tożsamość jest zaufana przez subskrypcję, która jest używana do tworzenia tożsamości.

1. **Konfigurowanie magazynu kluczy**

   Następnie zaimportujesz istniejący certyfikat lub utworzysz nowy w magazynie kluczy. Certyfikat będzie używany przez aplikacje uruchamiane za pośrednictwem bramy aplikacji. W tym kroku można również użyć klucza tajnego magazynu kluczy, który jest przechowywany jako bez hasła, podstawowy 64-zakodowany plik PFX. Zaleca się używanie typu certyfikatu ze względu na możliwości autorenewal, które są dostępne z obiektami typu certyfikatu w magazynie kluczy. Po utworzeniu certyfikatu lub klucza tajnego można zdefiniować zasady dostępu w magazynie kluczy, aby umożliwić tożsamości, aby *uzyskać* dostęp do klucza tajnego.

1. **Konfigurowanie bramy aplikacji**

   Po wykonaniu dwóch poprzednich kroków można skonfigurować lub zmodyfikować istniejącą bramę aplikacji, aby używać tożsamości zarządzanej przypisanej przez użytkownika. Można również skonfigurować certyfikat TLS/SSL odbiornika HTTP, aby wskazywał pełny identyfikator URI certyfikatu usługi Key Vault lub tajnego identyfikatora.

   ![Certyfikaty magazynu kluczy](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie zakończenia protokołu TLS przy użyciu certyfikatów usługi Key Vault przy użyciu programu Azure PowerShell](configure-keyvault-ps.md)
