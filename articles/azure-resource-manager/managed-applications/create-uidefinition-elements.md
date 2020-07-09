---
title: Tworzenie elementów definicji interfejsu użytkownika
description: Opisuje elementy, które mają być używane podczas konstruowania definicji interfejsu użytkownika dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77086703"
---
# <a name="createuidefinition-elements"></a>Elementy CreateUiDefinition

W tym artykule opisano schemat i właściwości wszystkich obsługiwanych elementów CreateUiDefinition. 

## <a name="schema"></a>Schemat

Schemat większości elementów jest następujący:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| name | Tak | Wewnętrzny identyfikator, aby odwołać się do określonego wystąpienia elementu. Najbardziej typowym użyciem nazwy elementu jest w `outputs` , gdzie wartości wyjściowe określonych elementów są mapowane na parametry szablonu. Można go również użyć, aby powiązać wartość wyjściową elementu z `defaultValue` innego elementu. |
| typ | Tak | Kontrolka interfejsu użytkownika do renderowania dla elementu. Aby uzyskać listę obsługiwanych typów, zobacz [elementy](#elements). |
| label | Tak | Wyświetlany tekst elementu. Niektóre typy elementów zawierają wiele etykiet, więc wartość może być obiektem zawierającym wiele ciągów. |
| defaultValue | Nie | Wartość domyślna elementu. Niektóre typy elementów obsługują złożone wartości domyślne, więc wartość może być obiektem. |
| Wyowietlon | Nie | Tekst, który ma być wyświetlany w etykietce narzędzia elementu. Podobnie jak w `label` przypadku niektórych elementów obsługiwane są wiele ciągów etykietek narzędzi. Linki wbudowane można osadzić przy użyciu składni promocji.
| powiązanych | Nie | Co najmniej jedna właściwość, która jest używana do dostosowywania zachowania walidacji elementu. Obsługiwane właściwości ograniczeń różnią się w zależności od typu elementu. Niektóre typy elementów nie obsługują dostosowywania zachowania weryfikacji i w ten sposób nie mają właściwości ograniczenia. |
| opcje | Nie | Dodatkowe właściwości, które dostosowują zachowanie elementu. Podobnie jak w `constraints` przypadku, obsługiwane właściwości różnią się w zależności od typu elementu. |
| widać | Nie | Wskazuje, czy element jest wyświetlany. Jeśli `true` zostanie wyświetlony element i odpowiednie elementy podrzędne. Wartość domyślna to `true`. Użyj [funkcji logicznych](create-uidefinition-functions.md#logical-functions) do dynamicznego kontrolowania wartości tej właściwości.

## <a name="elements"></a>Elementy

Dokumentacja dla każdego elementu zawiera przykładowy interfejs użytkownika, schemat, uwagi dotyczące zachowania elementu (zwykle dotyczy walidacji i obsługiwanego dostosowania) oraz przykładowe dane wyjściowe.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft. ManagedIdentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
