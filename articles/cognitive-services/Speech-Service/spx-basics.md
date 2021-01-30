---
title: Przewodnik Szybki start interfejsu wiersza polecenia mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z interfejsem wiersza polecenia usługi Azure Speech. Można korzystać z usług mowy, takich jak Zamiana mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy bez pisania kodu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 4a6c7b36665c7a38534ce8e470bc8b327c274d95
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "99095188"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Wprowadzenie do interfejsu wiersza polecenia usługi Azure Speech

W tym artykule dowiesz się, jak korzystać z interfejsu wiersza polecenia usługi Speech, w celu uzyskania dostępu do usług mowy, takich jak Zamiana mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy bez pisania kodu. Interfejs wiersza polecenia mowy jest gotowy do produkcji i może służyć do automatyzowania prostych przepływów pracy w usłudze mowy przy użyciu `.bat` skryptów powłoki lub.

W tym artykule założono, że masz praktyczną wiedzę na temat wiersza polecenia, terminalu lub programu PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Podstawowy sposób użycia

W tej sekcji przedstawiono kilka podstawowych poleceń SPX, które często są przydatne podczas pierwszego testowania i eksperymentowania. Zacznij od przejrzenia pomocy wbudowanej do narzędzia, uruchamiając następujące polecenie.

```console
spx
```

Tematy pomocy można wyszukiwać według słowa kluczowego. Na przykład wprowadź następujące polecenie, aby wyświetlić listę przykładów użycia interfejsu wiersza polecenia mowy:

```console
spx help find --topics "examples"
```

Wprowadź następujące polecenie, aby wyświetlić opcje polecenia Rozpoznaj:

```console
spx help recognize
```

Dodatkowe polecenia pomocy wymienione w prawej kolumnie. Możesz wprowadzić te polecenia, aby uzyskać szczegółową pomoc dotyczącą podpoleceń.

## <a name="speech-to-text-speech-recognition"></a>Zamiana mowy na tekst (rozpoznawanie mowy)

Użyjemy interfejsu wiersza polecenia mowy, aby przekonwertować mowę na tekst (rozpoznawanie mowy) przy użyciu domyślnego mikrofonu systemu. Po wprowadzeniu polecenia protokół SPX rozpocznie nasłuchiwanie dźwięku na bieżącym aktywnym urządzeniu wejściowym i zatrzymuje się po naciśnięciu klawisza **Enter**. Zarejestrowane mowę są następnie rozpoznawane i konwertowane na tekst w danych wyjściowych konsoli.

>[!IMPORTANT]
> Jeśli używasz kontenera Docker, `--microphone` program nie będzie działał.

Uruchom następujące polecenie:

```console
spx recognize --microphone
```

Interfejs wiersza polecenia mowy umożliwia również rozpoznawanie mowy z pliku dźwiękowego.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Jeśli rozpoznajesz mowę z pliku dźwiękowego w kontenerze platformy Docker, upewnij się, że plik audio znajduje się w katalogu, który został zainstalowany w poprzednim kroku.

Nie zapomnij, Jeśli wiesz lub chcesz dowiedzieć się więcej o opcjach rozpoznawania interfejsu wiersza polecenia mowy, po prostu wpisz:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Zamiana tekstu na mowę (synteza mowy)

Uruchomienie następującego polecenia spowoduje zamianę tekstu na dane wejściowe i wyjście z syntezy głosu do bieżącego aktywnego urządzenia wyjściowego (na przykład głośników komputera).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Można również zapisać dane wyjściowe z syntezą w pliku. W tym przykładzie utworzymy plik o nazwie `my-sample.wav` w katalogu, w którym uruchomiono polecenie.

```console
spx synthesize --text "We hope that you enjoy using the Speech CLI." --audio output my-sample.wav
```

W tych przykładach zakłada się, że testy są testowane w języku angielskim. Jednak firma Microsoft obsługuje syntezę mowy w wielu językach. Pełną listę głosów można ściągnąć za pomocą tego polecenia lub odwiedzając [stronę pomocy technicznej języka](./language-support.md).

```console
spx synthesize --voices
```

Poniżej przedstawiono sposób użycia jednego z wykrytych głosów.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Nie zapomnij, Jeśli wiesz lub chcesz dowiedzieć się więcej na temat opcji syntezy interfejsu wiersza polecenia mowy, po prostu wpisz:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Tłumaczenie zamiany mowy na tekst

Korzystając z interfejsu wiersza polecenia mowy, można również przetłumaczyć mowę na tekst. Uruchom to polecenie, aby przechwycić dźwięk z domyślnego mikrofonu i wyprowadzić tłumaczenie jako tekst. Pamiętaj, że musisz podać `source` `target` język i za pomocą `translate` polecenia.

```console
spx translate --microphone --source en-US --target ru-RU
```

W przypadku tłumaczenia na wiele języków, należy oddzielić kody języka od `;` .

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Jeśli chcesz zapisać dane wyjściowe tłumaczenia, użyj `--output` flagi. W tym przykładzie zostanie również odczytany plik.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Zapoznaj się z listą wszystkich obsługiwanych języków z odpowiednimi kodami ustawień regionalnych w [artykule język i ustawienia regionalne](language-support.md) .

Nie zapomnij, Jeśli wiesz lub chcesz dowiedzieć się więcej na temat opcji tłumaczenia interfejsu wiersza polecenia mowy, po prostu wpisz:

```console
spx help translate
```

## <a name="next-steps"></a>Następne kroki

* [Opcje konfiguracji interfejsu wiersza polecenia usługi Mowa](./spx-data-store-configuration.md)
* [Operacje wsadowe przy użyciu interfejsu wiersza polecenia mowy](./spx-batch-operations.md)
