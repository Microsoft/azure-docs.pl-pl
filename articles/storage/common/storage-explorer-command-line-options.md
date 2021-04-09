---
title: Opcje wiersza polecenia Eksplorator usługi Azure Storage | Microsoft Docs
description: Dokumentacja Eksplorator usługi Azure Storage uruchamiania opcji wiersza polecenia
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745408"
---
# <a name="azure-storage-explorer-command-line-options"></a>Opcje wiersza polecenia Eksplorator usługi Azure Storage

Eksplorator usługi Microsoft Azure Storage zawiera zestaw opcji wiersza polecenia, które można dodać podczas uruchamiania aplikacji. Większość opcji wiersza polecenia umożliwia debugowanie lub Rozwiązywanie problemów.

## <a name="command-line-options"></a>Opcje wiersza polecenia
Opcja  | Opis
:------- | :-----------
`--debug`/`--prod`  | Uruchom aplikację w trybie debugowania lub produkcji. W trybie debugowania dane lokalnego załącznika będą przechowywane w lokalnym magazynie aplikacji i nie będą szyfrowane. Ukryte właściwości będą wyświetlane w panelu Właściwości dla wybranych węzłów zasobów. Poziom szczegółowości dziennika zostanie ustawiony na drukowanie komunikatów debugowania, które ujawniają logikę konfiguracji wewnętrznej Eksplorator usługi Storage. Wartość domyślna to `--prod`.
`--lang`  | Uruchom aplikację w danym języku. Na przykład `--lang="zh-Hans"`.
`--disable-gpu` | Uruchom aplikację bez przyspieszenia procesora GPU.
`--auto-open-dev-tools` | Zezwól aplikacji na otwieranie okna narzędzia deweloperskie, gdy tylko zostanie wyświetlone okno przeglądarki. Ta opcja jest przydatna, gdy chcesz umieścić punkt przerwania w wierszu w kodzie początkowym okna przeglądarki.
`--verbosity` | Ustaw poziom szczegółowości rejestrowania Eksplorator usługi Storage. Obsługiwane poziomy szczegółowości obejmują `debug` ,,,, `verbose` `info` `warn` `error` , i `silent` . Na przykład `--verbosity=verbose`. W trybie produkcyjnym domyślny poziom szczegółowości to `info` . Po uruchomieniu w trybie debugowania poziom szczegółowości dziennika zawsze będzie mieć wartość `debug` .
`--log-dir` | Ustaw katalog na zapisywanie plików dziennika. Na przykład `--log-dir=path_to_a_directory`.

Przykład uruchamiania Eksplorator usługi Storage z niestandardowymi opcjami wiersza polecenia

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Opcje wiersza polecenia mogą ulec zmianie w nowych wersjach Eksplorator usługi Storage.

## <a name="when-to-use-command-line-options"></a>Kiedy używać opcji wiersza polecenia

Niektóre opcje wiersza polecenia mogą służyć do dostosowywania Eksplorator usługi Storage. Dla tych opcji, które mają odpowiednie ustawienia użytkownika, na przykład `--lang` . Zalecamy korzystanie z ustawień użytkownika zamiast korzystać z opcji wiersza polecenia. 

Inne opcje wiersza polecenia mogą być przydatne do debugowania i rozwiązywania problemów. Jeśli wystąpi problem w Eksplorator usługi Storage, odtworzenie problemu w trybie debugowania może pomóc nam uzyskać bardziej szczegółowe informacje do zbadania.