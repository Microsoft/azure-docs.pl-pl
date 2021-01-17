---
title: Opcje konfiguracji interfejsu wiersza polecenia mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak tworzyć pliki konfiguracji i zarządzać nimi do użycia z interfejsem wiersza polecenia usługi Azure Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540063"
---
# <a name="speech-cli-configuration-options"></a>Opcje konfiguracji interfejsu wiersza polecenia mowy

Zachowanie interfejsu wiersza polecenia mowy może polegać na ustawieniach w plikach konfiguracji, do których można się odwoływać przy użyciu `@` symbolu. Interfejs wiersza polecenia mowy zapisuje nowe ustawienie w nowym `./spx/data` podkatalogu, który jest tworzony w bieżącym katalogu roboczym interfejsu wiersza polecenia mowy. Podczas wyszukiwania wartości konfiguracji interfejs wiersza polecenia mowy wyszukuje bieżący katalog roboczy, a następnie w magazynie danych pod adresem `./spx/data` , a następnie w innych magazynach danych, łącznie z końcowym magazynem danych tylko do odczytu w `spx` pliku binarnym. 

W przewodniku szybki start interfejsu wiersza polecenia mowy użyto magazynu danych do zapisania `@key` `@region` wartości i, więc nie trzeba ich określać przy użyciu poszczególnych `spx` poleceń. Należy pamiętać, że za pomocą plików konfiguracji można przechowywać własne ustawienia konfiguracji, a nawet używać ich do przekazywania adresów URL lub innej zawartości dynamicznej wygenerowanej w czasie wykonywania.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Tworzenie plików konfiguracji i zarządzanie nimi w magazynie danych

W tej sekcji pokazano, jak używać pliku konfiguracji w lokalnym magazynie danych do przechowywania i pobierania ustawień poleceń przy użyciu `spx config` i przechowywania danych wyjściowych z interfejsu wiersza polecenia mowy przy użyciu `--output` opcji.

Poniższy przykład czyści `@my.defaults` plik konfiguracji, dodaje pary klucz-wartość dla **klucza** i **regionu** w pliku, a następnie używa konfiguracji w wywołaniu `spx recognize` .

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Zawartość dynamiczną można także zapisać w pliku konfiguracji. Na przykład następujące polecenie tworzy niestandardowy model mowy i zapisuje adres URL nowego modelu w pliku konfiguracji. Następne polecenie czeka, aż model w tym adresie URL będzie gotowy do użycia przed zwróceniem.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

Poniższy przykład zapisuje dwa adresy URL do `@my.datasets.txt` pliku konfiguracji. W tym scenariuszu `--output` może zawierać opcjonalne słowo kluczowe **Add** , aby utworzyć plik konfiguracji lub dołączyć do istniejącego pliku.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Aby uzyskać więcej informacji na temat plików magazynu danych, w tym używania domyślnych plików konfiguracji ( `@spx.default` , `@default.config` i `@*.default.config` dla ustawień domyślnych specyficznych dla poleceń), wprowadź następujące polecenie:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Następne kroki 

* [Operacje wsadowe przy użyciu interfejsu wiersza polecenia mowy](./spx-batch-operations.md)