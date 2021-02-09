---
title: Korzystanie z edytora Azure Cloud Shell | Microsoft Docs
description: Omówienie korzystania z edytora Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832339"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Korzystanie z edytora Azure Cloud Shell

Azure Cloud Shell zawiera zintegrowany edytor plików utworzony z [edytora Monako](https://github.com/Microsoft/monaco-editor)"open source". Edytor usługi Cloud Shell obsługuje różne funkcje, takie jak wyróżnianie elementów języka, paleta poleceń i eksplorator plików.

![Edytor Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Otwieranie Edytora

Na potrzeby prostego tworzenia i edytowania plików otwórz edytor, uruchamiając polecenie `code .` w terminalu usługi Cloud Shell. Działanie to powoduje otwarcie edytora z aktywnym katalogiem roboczym ustawionym w terminalu.

Aby bezpośrednio otworzyć plik do szybkiej edycji, uruchom polecenie `code <filename>` w celu otwarcia edytora bez eksploratora plików.

Aby otworzyć edytor za pomocą przycisku, kliknij ikonę edytora `{}` na pasku narzędzi. Edytor zostanie otwarty, a w eksploratorze plików zostanie domyślnie wyświetlony katalog `/home/<user>`.

## <a name="closing-the-editor"></a>Zamykanie edytora

Aby zamknąć Edytor, Otwórz `...` panel akcji w prawym górnym rogu edytora i wybierz opcję `Close editor` .

![Zamknij Edytor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta poleceń

Aby uruchomić paletę poleceń, użyj `F1` klawisza, gdy fokus jest ustawiony w edytorze. Otwieranie palety poleceń można przeprowadzić za pomocą panelu akcji.

![Paleta cmd](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Współtworzenie edytora Monako

Obsługa wyróżniania języka w edytorze Cloud Shell jest obsługiwana za pomocą funkcji nadrzędnych w [Edytorze Monako](https://github.com/Microsoft/monaco-editor)użycie definicji składni Monarch. Aby dowiedzieć się, jak utworzyć wkłady, Przeczytaj [Przewodnik współautora w Monako](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Następne kroki

- [Wypróbuj Przewodnik Szybki Start dla usługi Bash w Cloud Shell](quickstart.md)
- [Zapoznaj się z pełną listą zintegrowanych narzędzi Cloud Shell](features.md)
