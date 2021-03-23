---
title: Zakończenie protokołu TLS z certyfikatami Azure Key Vault
description: Dowiedz się, jak zintegrować Application Gateway platformy Azure z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 8a64956deb7849568e70e94c9b58170df60db1e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775746"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Zakończenie szyfrowania TLS z certyfikatami usługi Key Vault

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

Application Gateway obecnie obsługuje tylko certyfikaty zweryfikowane przez oprogramowanie. Sprzętowy moduł zabezpieczeń (HSM) — zweryfikowane certyfikaty nie są obsługiwane. Gdy Application Gateway jest skonfigurowany do korzystania z certyfikatów Key Vault, jego wystąpienia pobierają certyfikat z Key Vault i instalują je lokalnie w celu zakończenia protokołu TLS. Wystąpienia są również sondowane Key Vault w okresach 4-godzinnych w celu pobrania odnowionej wersji certyfikatu (jeśli istnieje). Jeśli zostanie znaleziony zaktualizowany certyfikat, certyfikat TLS/SSL aktualnie skojarzony z odbiornikiem HTTPS jest automatycznie obracany.

> [!NOTE]
> Azure Portal obsługuje tylko certyfikaty magazynu kluczy, a nie wpisy tajne. Application Gateway nadal obsługuje odwołania do wpisów tajnych z magazynu kluczy, ale tylko za pośrednictwem zasobów nienależących do portalu, takich jak program PowerShell, interfejs wiersza polecenia, interfejsy API, szablony ARM itd. 

## <a name="how-integration-works"></a>Jak działa integracja

Integracja Application Gateway z Key Vault wymaga procesu konfiguracji z trzema krokami:

1. **Tworzenie tożsamości zarządzanej przypisanej przez użytkownika**

   Należy utworzyć lub ponownie użyć istniejącej tożsamości zarządzanej przypisanej przez użytkownika, która Application Gateway używa do pobierania certyfikatów z usługi Key Vault w Twoim imieniu. Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Ten krok powoduje utworzenie nowej tożsamości w dzierżawie Azure Active Directory. Tożsamość jest zaufana przez subskrypcję, która jest używana do tworzenia tożsamości.

1. **Konfigurowanie magazynu kluczy**

   Następnie można zaimportować istniejący certyfikat lub utworzyć nowy w magazynie kluczy. Certyfikat będzie używany przez aplikacje uruchamiane za pomocą bramy aplikacji. W tym kroku można również użyć Key Vault klucz tajny, który umożliwia przechowywanie plików PFX o formacie mniejszym niż 64. Zalecamy używanie typu "Certificate" z powodu możliwości autoodnawiania, która jest dostępna dla tego typu obiektów w Key Vault. Po utworzeniu certyfikatu lub wpisu tajnego należy zdefiniować zasady dostępu w Key Vault, aby zezwolić na uzyskanie dostępu do tożsamości.
   
   > [!IMPORTANT]
   > Od 15 marca 2021 Key Vault rozpoznaje platformę Azure Application Gateway jako jedną z zaufanych usług, co pozwala na tworzenie bezpiecznej granicy sieci na platformie Azure. Dzięki temu można odmówić dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego) do Key Vault, ale nadal będzie on dostępny dla Application Gateway zasobów w ramach subskrypcji. 

   > Application Gateway można skonfigurować w sieci Key Vault z ograniczeniami w następujący sposób. <br />
   > a) w bloku sieć Key Vault <br />
   > b) wybierz pozycję prywatny punkt końcowy i wybrane sieci w zakładce "Zapora i sieci wirtualne" <br/>
   > c) następnie przy użyciu sieci wirtualnych Dodaj sieć wirtualną i podsieć Application Gateway. Podczas procesu Skonfiguruj również punkt końcowy usługi "Microsoft. datamagazyn", zaznaczając jego pole wyboru. <br/>
   > d) na koniec wybierz pozycję "tak", aby umożliwić zaufanym usługom obejście zapory Key Vault. <br/>
   > 
   > ![Zapora Key Vault](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > W przypadku wdrożenia bramy aplikacji za pomocą szablonu ARM przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell lub za pomocą aplikacji platformy Azure wdrożonej z Azure Portal certyfikat SSL jest przechowywany w magazynie kluczy jako plik PFX szyfrowany algorytmem Base64. Przed rozpoczęciem wdrażania należy wykonać kroki opisane w temacie [używanie Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > Szczególnie ważne jest, aby ustawić `enabledForTemplateDeployment` wartość `true` . Certyfikat może być bez hasła lub może mieć hasło. W przypadku certyfikatu z hasłem Poniższy przykład pokazuje możliwe konfiguracje dla `sslCertificates` wpisu w `properties` konfiguracji szablonu usługi ARM dla bramy aplikacji. Wartości `appGatewaySSLCertificateData` i `appGatewaySSLCertificatePassword` są wyszukiwane z magazynu kluczy, zgodnie z opisem w sekcji wpisy [tajne informacji o identyfikatorze dynamicznym](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Postępuj zgodnie z poprzednimi odwołaniami, `parameters('secretName')` Aby zobaczyć, jak działa wyszukiwanie. Jeśli certyfikat nie jest bez hasła, Pomiń ten `password` wpis.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Konfigurowanie bramy aplikacji**

   Po wykonaniu dwóch powyższych kroków można skonfigurować lub zmodyfikować istniejącą bramę aplikacji tak, aby korzystała z tożsamości zarządzanej przypisanej przez użytkownika. Aby uzyskać więcej informacji, zobacz [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   Istnieje również możliwość skonfigurowania certyfikatu TLS/SSL odbiornika HTTP w taki sposób, aby wskazywał pełny identyfikator URI certyfikatu Key Vault lub identyfikatora tajnego.

   ![Certyfikaty magazynu kluczy](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie zakończenia protokołu TLS przy użyciu certyfikatów Key Vault przy użyciu Azure PowerShell](configure-keyvault-ps.md)
