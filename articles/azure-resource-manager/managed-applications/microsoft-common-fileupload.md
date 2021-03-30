---
title: FileUpload — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. FileUpload dla Azure Portal. Umożliwia użytkownikom przesyłanie plików podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: c4eb3972be85ac7e291380f64127c20424b0d0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004288"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft. Common. FileUpload — element interfejsu użytkownika

Kontrolka, która umożliwia użytkownikowi określenie co najmniej jednego pliku do przekazania.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft-common-fileupload.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Jeśli opcje. Multiple ma wartość false, a opcje. parametr-loadmode jest plikiem, a dane wyjściowe zawierają zawartość pliku jako ciąg JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Jeśli opcja. Multiple ma wartość true, and'options. FileMode ma plik, a następnie dane wyjściowe zawierają zawartość plików jako tablicę JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Jeśli opcje. Multiple ma wartość false, a opcje. parametr-loadmode ma wartość URL, a wynikiem jest adres URL w postaci ciągu JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Jeśli opcje. wiele ma wartość true, a opcja. Właściwość-loadmode ma adres URL, a dane wyjściowe zawierają listę adresów URL jako tablicę JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Podczas testowania CreateUiDefinition, niektóre przeglądarki (na przykład Google Chrome) obcinają adresy URL wygenerowane przez element Microsoft. Common. FileUpload w konsoli przeglądarki. Może być konieczne kliknięcie prawym przyciskiem myszy poszczególnych linków w celu skopiowania pełnych adresów URL.

## <a name="remarks"></a>Uwagi

- `constraints.accept` Określa typy plików, które są wyświetlane w oknie dialogowym pliku przeglądarki. Aby uzyskać dozwolone wartości, zobacz [specyfikację HTML5](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) . Wartość domyślna to **null**.
- Jeśli `options.multiple` jest ustawiona na **true**, użytkownik może wybrać więcej niż jeden plik w oknie dialogowym pliku przeglądarki. Wartość domyślna to **fałsz**.
- Ten element obsługuje przekazywanie plików w dwóch trybach na podstawie wartości `options.uploadMode` . Jeśli **plik** jest określony, dane wyjściowe mają zawartość pliku jako obiekt BLOB. Jeśli określono **adres URL** , plik zostanie przekazany do tymczasowej lokalizacji, a dane wyjściowe zawierają adres URL obiektu BLOB. Tymczasowe obiekty blob zostaną przeczyszczone po 24 godzinach. Wartość domyślna to **plik**.
- Przekazany plik jest chroniony. Wyjściowy adres URL zawiera [token SAS](../../storage/common/storage-sas-overview.md?toc=/azure/storage/blobs/toc.json) służący do uzyskiwania dostępu do pliku podczas wdrażania.
- Wartość `options.openMode` określa, jak odczytywany jest plik. Jeśli oczekujesz, że plik ma być zwykłym tekstem, określ **tekst**; w przeciwnym razie Określ dane **binarne**. Wartość domyślna to **Text**.
- Jeśli `options.uploadMode` jest ustawiona na wartość **File** i `options.openMode` jest ustawiona na wartość **binarną**, dane wyjściowe są kodowane algorytmem Base64.
- `options.encoding` Określa kodowanie, które ma być używane podczas odczytywania pliku. Wartość domyślna to **UTF-8** i jest używana tylko wtedy, gdy `options.openMode` jest ustawiona na **Text**.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
