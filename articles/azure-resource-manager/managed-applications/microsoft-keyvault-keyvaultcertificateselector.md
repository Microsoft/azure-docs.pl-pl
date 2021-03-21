---
title: KeyVaultCertificateSelector — element interfejsu użytkownika
description: Opisuje Azure Portal element interfejsu użytkownika Microsoft. KeyVaultCertificateSelector.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101225"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft. KeyVaultCertificateSelector — element interfejsu użytkownika

Kontrolka służąca do wybierania certyfikatu magazynu kluczy.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Użytkownik zobaczy opcję wybrania dostępnego certyfikatu.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. KeyVaultCertificateSelector — magazyn.":::

Po wybraniu opcji **Wybierz certyfikat** użytkownik może określić Magazyn kluczy i certyfikat z magazynu kluczy.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft. KeyVaultCertificateSelector — Wybieranie certyfikatu":::

Formant zostanie zaktualizowany w celu wyświetlenia wybranego magazynu kluczy i nazwy certyfikatu.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. KeyVaultCertificateSelector — Pokaż wybrany certyfikat":::

## <a name="schema"></a>Schemat

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
