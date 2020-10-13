---
title: Tworzenie elementów definicji interfejsu użytkownika
description: Opisuje elementy, które mają być używane podczas konstruowania definicji interfejsu użytkownika dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 8b6c6e78c64f83ca39a29f319a103e893d8d8b38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923725"
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

- [Microsoft. Common. CheckBox](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft. Common. EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. Slider](microsoft-common-slider.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft. ManagedIdentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft. Solutions. ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
