---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76772424"
---
### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

Za pomocą klucza czasu wykonywania i punktu końcowego środowiska uruchomieniowego Utwórz zmienne środowiskowe do uwierzytelniania i dostępu:

* `LUIS_RUNTIME_KEY`— Klucz zasobu środowiska uruchomieniowego do uwierzytelniania żądań.
* `LUIS_RUNTIME_ENDPOINT`— Punkt końcowy środowiska uruchomieniowego skojarzony z kluczem.
* `LUIS_APP_ID`— Identyfikator aplikacji Public LUIS IoT to `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production`oraz`staging`

Jeśli zamierzasz korzystać z tego przewodnika Szybki Start, aby uzyskać dostęp do swojej aplikacji, musisz wykonać dodatkowe czynności:
* Utwórz aplikację i uzyskaj identyfikator aplikacji
* Przypisywanie klucza czasu wykonywania do aplikacji w portalu LUIS
* Przetestuj adres URL w przeglądarce, aby uzyskać dostęp do aplikacji

Skorzystaj z instrukcji dotyczących systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych ponownie uruchom okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych Uruchom `source ~/.bashrc` polecenie z okna konsoli, aby zmiany zaczęły obowiązywać.

#### <a name="macos"></a>[macOS](#tab/unix)

`.bash_profile`Edytuj i Dodaj zmienną środowiskową:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po dodaniu zmiennych środowiskowych Uruchom `source .bash_profile` polecenie z okna konsoli, aby zmiany zaczęły obowiązywać.

***
