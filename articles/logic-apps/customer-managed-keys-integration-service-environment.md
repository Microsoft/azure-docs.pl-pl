---
title: Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w ISEs
description: Utwórz własne klucze szyfrowania i zarządzaj nimi, aby zabezpieczyć dane przechowywane w środowiskach usługi Integration Environment (ISEs) w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 30b09d43cbe510318ac4f48e0655d5483491c215
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682778"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Skonfiguruj klucze zarządzane przez klienta, aby szyfrować dane przechowywane w środowiskach usługi Integration Environment (ISEs) w Azure Logic Apps

Azure Logic Apps korzysta z usługi Azure Storage, aby przechowywać i automatycznie [szyfrować dane](../storage/common/storage-service-encryption.md)przechowywane. To szyfrowanie chroni dane i pomaga sprostać zobowiązaniom dotyczącym bezpieczeństwa i zgodności w organizacji. Domyślnie usługa Azure Storage używa kluczy zarządzanych przez firmę Microsoft do szyfrowania danych. Aby uzyskać więcej informacji o tym, jak działa szyfrowanie usługi Azure Storage, zobacz [szyfrowanie usługi Azure Storage dla danych przechowywanych w usłudze REST](../storage/common/storage-service-encryption.md) i [szyfrowanie danych na platformie Azure](../security/fundamentals/encryption-atrest.md).

Podczas tworzenia [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) na potrzeby hostowania aplikacji logiki, a użytkownik chce mieć większą kontrolę nad kluczami szyfrowania używanymi przez usługę Azure Storage, można skonfigurować własny klucz, korzystać z niego i zarządzać nim przy użyciu [Azure Key Vault](../key-vault/general/overview.md). Ta funkcja jest również znana jako "Bring Your Own Key" (BYOK), a klucz jest nazywany "kluczem zarządzanym przez klienta".

W tym temacie przedstawiono sposób konfigurowania i określania własnego klucza szyfrowania, który ma być używany podczas tworzenia ISE przy użyciu interfejsu API REST Logic Apps. Aby zapoznać się z ogólnymi krokami tworzenia ISE za pośrednictwem interfejsu API REST Logic Apps, zobacz [Tworzenie środowiska usługi integracji (ISE) przy użyciu interfejsu API rest Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Kwestie do rozważenia

* W tej chwili obsługa klucza zarządzanego przez klienta dla ISE jest dostępna tylko w następujących regionach świadczenia usługi Azure: zachodnie stany USA 2, Wschodnie stany USA i Południowo-środkowe stany USA

* Klucz zarządzany przez klienta można określić tylko w *przypadku tworzenia ISE*, a nie później. Nie można wyłączyć tego klucza po utworzeniu ISE. Obecnie nie istnieje żadna pomoc dla rotacji klucza zarządzanego przez klienta dla ISE.

* Aby obsługiwały klucze zarządzane przez klienta, ISE wymaga, aby była włączona [tożsamość zarządzana przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) . Ta tożsamość pozwala ISE uwierzytelniać dostęp do zasobów w innych dzierżawach usługi Azure Active Directory (Azure AD), dzięki czemu nie musisz logować się przy użyciu swoich poświadczeń.

* Obecnie aby utworzyć element ISE, który obsługuje klucze zarządzane przez klienta i ma włączoną swoją tożsamość przypisaną do systemu, należy wywołać interfejs API REST Logic Apps przy użyciu żądania HTTPS PUT.

* W ciągu *30 minut* od wysłania żądania HTTPS Put, które tworzy ISE, należy [nadać magazynowi kluczy dostęp do tożsamości przypisanej do systemu ISE](#identity-access-to-key-vault). W przeciwnym razie tworzenie ISE kończy się niepowodzeniem i zgłasza błąd uprawnień.

## <a name="prerequisites"></a>Wymagania wstępne

* Te same wymagania [wstępne](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) i [wymagania dotyczące włączania dostępu do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jako podczas tworzenia ISE w Azure Portal

* Magazyn kluczy platformy Azure z włączonymi nietrwałymi właściwościami **usuwania** i **nieprzeczyszczania**

  Aby uzyskać więcej informacji na temat włączania tych właściwości, zobacz [Azure Key Vault narzędzia nietrwałego usuwania](../key-vault/general/soft-delete-overview.md) i [konfigurowania kluczy zarządzanych przez klienta przy użyciu Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md). Jeśli jesteś nowym do [Azure Key Vault](../key-vault/general/overview.md), Dowiedz się, jak utworzyć magazyn kluczy przy użyciu [Azure Portal](../key-vault/general/quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](../key-vault/general/quick-create-cli.md)lub [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

* W magazynie kluczy, który jest tworzony przy użyciu tych wartości właściwości:

  | Właściwość | Wartość |
  |----------|-------|
  | **Typ klucza** | RSA |
  | **Rozmiar klucza RSA** | 2048 |
  | **Włączone** | Tak |
  |||

  ![Tworzenie klucza szyfrowania zarządzanego przez klienta](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Aby uzyskać więcej informacji, zobacz [Konfigurowanie kluczy zarządzanych przez klienta za pomocą Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md) lub Azure PowerShell polecenie [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Narzędzie, za pomocą którego można utworzyć ISE przez wywołanie interfejsu API REST Logic Apps przy użyciu żądania HTTPS PUT. Na przykład można użyć programu [Poster](https://www.getpostman.com/downloads/)lub można utworzyć aplikację logiki, która wykonuje to zadanie.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Tworzenie ISE przy użyciu magazynu kluczy i pomocy technicznej dotyczącej tożsamości zarządzanej

Aby utworzyć ISE przez wywołanie interfejsu API REST Logic Apps, wykonaj to żądanie HTTP PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Wersja interfejsu API REST Logic Apps 2019-05-01 wymaga wprowadzenia własnego żądania HTTP PUT dla łączników ISE.

Wdrożenie zazwyczaj trwa w ciągu dwóch godzin. Czasami wdrożenie może trwać do czterech godzin. Aby sprawdzić stan wdrożenia, w [Azure Portal](https://portal.azure.com)na pasku narzędzi platformy Azure wybierz ikonę powiadomienia, która spowoduje otwarcie okienka powiadomienia.

> [!NOTE]
> Jeśli wdrożenie nie powiedzie się lub usuniesz ISE, platforma Azure może upłynąć do godziny przed zwolnieniem podsieci. To opóźnienie oznacza, że może być konieczne odczekanie przed ponownym użyciem tych podsieci w innym ISE.
>
> Po usunięciu sieci wirtualnej platforma Azure zazwyczaj zajmie maksymalnie dwie godziny przed zwolnieniem podsieci, ale ta operacja może trwać dłużej. 
> Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
> Zobacz [usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Nagłówek żądania

W nagłówku żądania uwzględnij następujące właściwości:

* `Content-type`: Ustaw tę wartość właściwości na `application/json` .

* `Authorization`: Ustaw tę wartość właściwości na token okaziciela dla klienta, który ma dostęp do subskrypcji platformy Azure lub grupy zasobów, której chcesz użyć.

### <a name="request-body"></a>Treść żądania

W treści żądania Włącz obsługę tych dodatkowych elementów, dostarczając ich informacje w definicji ISE:

* Tożsamość zarządzana przypisana przez system, której ISE używa do uzyskiwania dostępu do magazynu kluczy
* Magazyn kluczy i klucz zarządzany przez klienta, który ma być używany

#### <a name="request-body-syntax"></a>Składnia treści żądania

Poniżej przedstawiono składnię treści żądania opisującą właściwości używane podczas tworzenia ISE:

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

W tej przykładowej treści żądania pokazano przykładowe wartości:

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

## <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

W ciągu *30 minut* od wysłania żądania HTTP Put w celu utworzenia ISE należy dodać zasady dostępu do magazynu kluczy dla tożsamości przypisanej do systemu ISE. W przeciwnym razie tworzenie ISE kończy się niepowodzeniem i zostanie wyświetlony komunikat o błędzie uprawnień. 

W przypadku tego zadania można użyć polecenia Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) albo wykonać następujące czynności w Azure Portal:

1. W [Azure Portal](https://portal.azure.com)Otwórz swój magazyn kluczy platformy Azure.

1. W menu Magazyn kluczy wybierz pozycję **zasady dostępu**  >  **Dodaj zasady dostępu**, na przykład:

   ![Dodawanie zasad dostępu dla tożsamości zarządzanej przypisanej przez system](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Po otwarciu okienka **Dodawanie zasad dostępu** wykonaj następujące kroki:

   1. Wybierz następujące opcje:

      | Ustawienie | Wartości |
      |---------|--------|
      | **Konfiguruj z listy szablon (opcjonalnie)** | Zarządzanie kluczami |
      | **Uprawnienia klucza** | - **Operacje zarządzania kluczami**: Get, list <p><p>- **Operacje kryptograficzne**: odpakowywanie klucza, zawijanie klucza |
      |||

      ![Wybierz pozycję "Zarządzanie kluczami" > "uprawnienia klucza"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. W obszarze **wybór podmiotu zabezpieczeń** wybierz pozycję **nie wybrano**. Po otwarciu okienka **podmiotu zabezpieczeń** w polu wyszukiwania Znajdź i wybierz swój ISE. Gdy skończysz, wybierz **pozycję Wybierz**  >  **Dodaj**.

      ![Wybierz ISE do użycia jako podmiot zabezpieczeń](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Po zakończeniu pracy z okienkiem **zasady dostępu** wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz [jak uwierzytelniać się w celu Key Vault](../key-vault/general/authentication.md) i [przypisywania zasad dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Key Vault](../key-vault/general/overview.md)