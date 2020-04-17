---
title: Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania danych w stanie spoczynku w programach ISE
description: Tworzenie własnych kluczy szyfrowania i zarządzanie nimi w celu zabezpieczania danych w stanie spoczynku dla środowisk usług integracyjnych (ISE) w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 7314559849f0b2019820ec3cb4fb10c684d330d6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458441"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych w stanie spoczynku dla środowisk usług integracyjnych (ISE) w usłudze Azure Logic Apps

Usługa Azure Logic Apps polega na usłudze Azure Storage do przechowywania i automatycznego [szyfrowania danych w spoczynku.](../storage/common/storage-service-encryption.md) To szyfrowanie chroni dane i pomaga spełnić zobowiązania dotyczące zabezpieczeń i zgodności z przepisami organizacji. Domyślnie usługa Azure Storage używa kluczy zarządzanych przez firmę Microsoft do szyfrowania danych. Aby uzyskać więcej informacji na temat funkcjonowania szyfrowania usługi Azure Storage, zobacz [Szyfrowanie usługi Azure Storage w celu uzyskania danych w spoczynku](../storage/common/storage-service-encryption.md) i [szyfrowanie danych platformy Azure w spoczynku.](../security/fundamentals/encryption-atrest.md)

Podczas tworzenia [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) do obsługi aplikacji logiki i chcesz mieć większą kontrolę nad kluczami szyfrowania używanymi przez usługę Azure Storage, można skonfigurować, używać i zarządzać własnym kluczem przy użyciu [usługi Azure Key Vault](../key-vault/general/overview.md). Ta funkcja jest również znana jako "Bring Your Own Key" (BYOK), a klucz jest nazywany "kluczem zarządzanym przez klienta".

W tym temacie pokazano, jak skonfigurować i określić własny klucz szyfrowania do użycia podczas tworzenia interfejsu ISE przy użyciu interfejsu API REST aplikacji logiki. Aby uzyskać ogólne kroki tworzenia środowiska ISE za pośrednictwem interfejsu API REST aplikacji logiki, zobacz [Tworzenie środowiska usługi integracji (ISE) przy użyciu interfejsu API REST aplikacji logiki](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

* Obecnie obsługa kluczy zarządzanych przez klienta dla ise jest dostępna tylko w tych regionach platformy Azure: Zachodnie stany USA 2, wschodnie stany USA i południowo-środkowe stany USA

* Klucz zarządzany przez klienta można określić *tylko podczas tworzenia ise*, a nie później. Nie można wyłączyć tego klucza po utworzeniu ise. Obecnie nie istnieje żadna obsługa obracania klucza zarządzanego przez klienta dla ise.

* Aby obsługiwać klucze zarządzane przez klienta, oprogramowanie ISE wymaga włączenia [tożsamości zarządzanej przypisanej przez system.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) Ta tożsamość umożliwia ise uwierzytelnić dostęp do zasobów w innych dzierżaw usługi Azure Active Directory (Azure AD), dzięki czemu nie trzeba logować się przy użyciu poświadczeń.

* Obecnie, aby utworzyć interfejs ISE, który obsługuje klucze zarządzane przez klienta i ma włączoną tożsamość przypisaną do systemu, należy wywołać interfejs API REST aplikacji logiki przy użyciu żądania HTTPS PUT.

* W ciągu *30 minut* po wysłaniu żądania HTTPS PUT, które tworzy ise, należy [dać dostęp do magazynu kluczy do tożsamości przypisanej przez system ise](#identity-access-to-key-vault). W przeciwnym razie tworzenie ISE kończy się niepowodzeniem i zgłasza błąd uprawnień.

## <a name="prerequisites"></a>Wymagania wstępne

* Te same [wymagania wstępne](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) i [wymagania, aby włączyć dostęp do ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jak podczas tworzenia ise w witrynie Azure portal

* Magazyn kluczy platformy Azure z włączonymi właściwościami **Usuwanie nietrwale** i **Nie przeczyszczaj**

  Aby uzyskać więcej informacji na temat włączania tych właściwości, zobacz [Omówienie usuwania programów nietrwałych usługi Azure Key Vault](../key-vault/general/overview-soft-delete.md) i [konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Jeśli jesteś nowym użytkownikiem usługi Azure Key Vault, dowiedz się, [jak utworzyć magazyn kluczy](../key-vault/secrets/quick-create-portal.md#create-a-vault) przy użyciu witryny Azure portal lub za pomocą polecenia Azure PowerShell, [New-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)

* W magazynie kluczy klucz, który jest tworzony przy następujących wartościach właściwości:

  | Właściwość | Wartość |
  |----------|-------|
  | **Typ klucza** | RSA |
  | **Rozmiar klucza RSA** | 2048 |
  | **Enabled (Włączony)** | Tak |
  |||

  ![Tworzenie klucza szyfrowania zarządzanego przez klienta](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Aby uzyskać więcej informacji, zobacz [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) lub polecenia Azure PowerShell, [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Narzędzie, którego można użyć do utworzenia interfejsu ISE, wywołując interfejs API REST aplikacji logiki z żądaniem HTTPS PUT. Na przykład można użyć [postman](https://www.getpostman.com/downloads/)lub można utworzyć aplikację logiki, która wykonuje to zadanie.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Tworzenie ise z obsługą magazynu kluczy i tożsamości zarządzanej

Aby utworzyć interfejs ISE, wywołując interfejs API REST aplikacji logiki, należy wykonać to żądanie HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Wersja interfejsu API INTERFEJSU API 2019-05-01 aplikacji logic apps wymaga wykonania własnego żądania HTTP PUT dla łączników ISE.

Wdrożenie zwykle trwa w ciągu dwóch godzin, aby zakończyć. Od czasu do czasu wdrożenie może potrwać do czterech godzin. Aby sprawdzić stan wdrożenia, w [witrynie Azure portal](https://portal.azure.com), na pasku narzędzi platformy Azure wybierz ikonę powiadomień, która otwiera okienko powiadomień.

> [!NOTE]
> Jeśli wdrożenie zakończy się niepowodzeniem lub usuniesz ise, platforma Azure może potrwać do godziny przed zwolnieniem podsieci. To opóźnienie oznacza, że może być trzeba poczekać przed ponownym użyciem tych podsieci w innym ise.
>
> Jeśli usuniesz sieć wirtualną, platforma Azure zwykle trwa do dwóch godzin przed zwolnieniem podsieci, ale ta operacja może potrwać dłużej. 
> Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
> Zobacz [Usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Nagłówek żądania

W nagłówku żądania uwzględnij następujące właściwości:

* `Content-type`: Ustaw tę `application/json`wartość właściwości na .

* `Authorization`: Ustaw tę wartość właściwości na token nośny dla klienta, który ma dostęp do subskrypcji platformy Azure lub grupy zasobów, której chcesz użyć.

### <a name="request-body"></a>Treść żądania

W treści żądania włącz obsługę tych dodatkowych elementów, podając ich informacje w definicji ISE:

* Przypisana przez system tożsamość zarządzana używana przez oprogramowanie ISE do uzyskiwania dostępu do magazynu kluczy
* Magazyn kluczy i klucz zarządzany przez klienta, którego chcesz użyć

#### <a name="request-body-syntax"></a>Składnia treści żądania

Oto składnia treści żądania, która opisuje właściwości, które mają być używane podczas tworzenia ise:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Przykład treści żądania

W tej przykładzie treści żądania przedstawiono przykładowe wartości:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Udziel dostępu do magazynu kluczy

W ciągu *30 minut* po wysłaniu żądania HTTP PUT w celu utworzenia ise należy dodać zasady dostępu do magazynu kluczy dla tożsamości przypisanej przez system ise. W przeciwnym razie utworzenie ise kończy się niepowodzeniem i zostanie wyświetlony błąd uprawnień. 

W tym zadaniu można użyć polecenia Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) lub wykonać następujące kroki w witrynie Azure portal:

1. W [witrynie Azure portal](https://portal.azure.com)otwórz magazyn kluczy platformy Azure.

1. W menu magazynu kluczy wybierz pozycję **Zasady** > dostępu**Dodaj zasady dostępu**, na przykład:

   ![Dodawanie zasad dostępu dla tożsamości zarządzanej przypisanej do systemu](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Po otwarciu okienka **Dodaj zasady dostępu** wykonaj następujące czynności:

   1. Wybierz następujące opcje:

      | Ustawienie | Wartości |
      |---------|--------|
      | **Konfigurowanie z listy szablonów (opcjonalnie)** | Zarządzanie kluczami |
      | **Uprawnienia klucza** | - **Kluczowe operacje zarządzania:** Get, List <p><p>- **Operacje kryptograficzne:** Rozpakuj klucz, zawij klucz |
      |||

      ![Wybierz "Zarządzanie kluczami" > "Uprawnienia klucza"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. W **obszarze Wybierz głównego**wybierz pozycję **Brak.** Po otwarciu **okienka Główny** element w polu wyszukiwania znajdź i wybierz ise. Po zakończeniu wybierz **pozycję Wybierz** > **dodaj**.

      ![Wybierz ise do użycia jako główny](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Po zakończeniu pracy z okienkiem **Zasady programu Access** wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [Zapewnianie uwierzytelniania magazynu kluczy z tożsamością zarządzaną](../key-vault/general/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [usłudze Azure Key Vault](../key-vault/general/overview.md)