---
title: Zakończenie protokołu TLS z certyfikatami Azure Key Vault
description: Dowiedz się, jak zintegrować Application Gateway platformy Azure z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 934cf854b0c526ed994c7dc91763f65de64fd14b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617503"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Zakończenie protokołu TLS z certyfikatami Key Vault

[Azure Key Vault](../key-vault/general/overview.md) to magazyn tajny zarządzany przez platformę, za pomocą którego można chronić klucze tajne oraz certyfikaty protokołu TLS/SSL. Usługa Azure Application Gateway obsługuje integrację z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS. Ta obsługa jest ograniczona do wersji 2 jednostki SKU systemu Application Gateway.

Integracja Key Vault oferuje dwa modele dla zakończenia protokołu TLS:

- Można jawnie dostarczyć certyfikaty TLS/SSL dołączone do odbiornika. Ten model jest tradycyjnym sposobem przekazywania certyfikatów protokołu TLS/SSL do Application Gateway na potrzeby zakończenia protokołu TLS.
- Opcjonalnie można podać odwołanie do istniejącego certyfikatu Key Vault lub klucza tajnego podczas tworzenia odbiornika z obsługą protokołu HTTPS.

Integracja Application Gateway z Key Vault oferuje wiele korzyści, w tym:

- Silniejsze zabezpieczenia, ponieważ certyfikaty TLS/SSL nie są bezpośrednio obsługiwane przez zespół deweloperów aplikacji. Integracja umożliwia oddzielnemu zespołowi ds. zabezpieczeń:
  * Konfigurowanie bram aplikacji.
  * Kontroluj cykle życia bramy aplikacji.
  * Udziel uprawnień wybranym bramom aplikacji dostępu do certyfikatów przechowywanych w magazynie kluczy.
- Obsługa importowania istniejących certyfikatów do magazynu kluczy. Lub użyj Key Vault interfejsów API, aby tworzyć nowe certyfikaty i zarządzać nimi za pomocą dowolnych zaufanych partnerów Key Vault.
- Obsługa automatycznego odnawiania certyfikatów przechowywanych w magazynie kluczy.

Application Gateway obecnie obsługuje tylko certyfikaty zweryfikowane przez oprogramowanie. Sprzętowy moduł zabezpieczeń (HSM) — zweryfikowane certyfikaty nie są obsługiwane. Gdy Application Gateway jest skonfigurowany do korzystania z certyfikatów Key Vault, jego wystąpienia pobierają certyfikat z Key Vault i instalują je lokalnie w celu zakończenia protokołu TLS. Wystąpienia są również sondowane Key Vault co 24 godziny w celu pobrania odnowionej wersji certyfikatu (jeśli istnieje). Jeśli zostanie znaleziony zaktualizowany certyfikat, certyfikat TLS/SSL aktualnie skojarzony z odbiornikiem HTTPS jest automatycznie obracany.

> [!NOTE]
> Azure Portal obsługuje tylko certyfikaty magazynu kluczy, a nie wpisy tajne. Application Gateway nadal obsługuje odwołania do wpisów tajnych z magazynu kluczy, ale tylko za pośrednictwem zasobów nienależących do portalu, takich jak program PowerShell, interfejs wiersza polecenia, interfejsy API, szablony ARM itd. 

## <a name="how-integration-works"></a>Jak działa integracja

Integracja Application Gateway z Key Vault wymaga procesu konfiguracji z trzema krokami:

1. **Tworzenie tożsamości zarządzanej przypisanej przez użytkownika**

   Należy utworzyć lub ponownie użyć istniejącej tożsamości zarządzanej przypisanej przez użytkownika, która Application Gateway używa do pobierania certyfikatów z usługi Key Vault w Twoim imieniu. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md). Ten krok powoduje utworzenie nowej tożsamości w dzierżawie Azure Active Directory. Tożsamość jest zaufana przez subskrypcję, która jest używana do tworzenia tożsamości.

1. **Konfigurowanie magazynu kluczy**

   Następnie można zaimportować istniejący certyfikat lub utworzyć nowy w magazynie kluczy. Certyfikat będzie używany przez aplikacje uruchamiane za pomocą bramy aplikacji. W tym kroku można także użyć klucza tajnego magazynu kluczy, który jest przechowywany jako plik PFX o postaci mniejszej niż 64. Zalecamy używanie typu certyfikatu z powodu możliwości autoodnawiania, która jest dostępna z obiektami typu certyfikat w magazynie kluczy. Po utworzeniu certyfikatu lub wpisu tajnego należy zdefiniować zasady dostępu w magazynie kluczy, aby *umożliwić udzielenie tożsamości* dostępu do klucza tajnego.
   
   > [!NOTE]
   > W przypadku wdrożenia bramy aplikacji za pomocą szablonu ARM przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell albo za pośrednictwem aplikacji platformy Azure wdrożonej z Azure Portal certyfikat SSL przechowywany w magazynie kluczy jako plik PFX z algorytmem Base-64 **musi mieć hasła**. Ponadto przed wdrożeniem należy wykonać kroki opisane w temacie [używanie Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Szczególnie ważne jest, aby ustawić `enabledForTemplateDeployment` wartość `true`.

1. **Konfigurowanie bramy aplikacji**

   Po wykonaniu dwóch powyższych kroków można skonfigurować lub zmodyfikować istniejącą bramę aplikacji tak, aby korzystała z tożsamości zarządzanej przypisanej przez użytkownika. Istnieje również możliwość skonfigurowania certyfikatu TLS/SSL odbiornika HTTP w taki sposób, aby wskazywał pełny identyfikator URI certyfikatu Key Vault lub identyfikatora tajnego.

   ![Certyfikaty magazynu kluczy](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie zakończenia protokołu TLS przy użyciu certyfikatów Key Vault przy użyciu Azure PowerShell](configure-keyvault-ps.md)
