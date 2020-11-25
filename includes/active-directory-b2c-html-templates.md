---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990908"
---
## <a name="sample-templates"></a>Przykładowe szablony
Przykładowe szablony do dostosowywania interfejsu użytkownika można znaleźć tutaj:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Ten projekt zawiera następujące szablony:
- [Ocean niebieski](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Szare](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Aby użyć przykładu:

1. Sklonuj repozytorium na komputerze lokalnym. Wybierz folder szablonu `/ocean_blue` lub `/slate_gray` .
1. Przekaż wszystkie pliki z folderu Template i `/assets` folder do magazynu obiektów BLOB zgodnie z opisem w poprzednich sekcjach.
1. Następnie otwórz każdy `\*.html` plik w katalogu głównym `/ocean_blue` lub `/slate_gray` Zastąp wszystkie wystąpienia względnych adresów URL adresami URL plików CSS, obrazów i czcionek przekazanych w kroku 2. Na przykład:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Działanie
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Zapisz `\*.html` pliki i przekaż je do magazynu obiektów BLOB.
1. Teraz zmodyfikuj zasady, wskazując na plik HTML, jak wspomniano wcześniej.
1. Jeśli widzisz brakujące czcionki, obrazy lub CSS, Sprawdź odwołania w zasadach rozszerzeń i \* plikach. html.
