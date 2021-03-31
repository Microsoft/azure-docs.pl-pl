---
title: Jak skanowanie wykrywa usunięte zasoby
description: W tym artykule wyjaśniono, w jaki sposób konto usługi Azure kontrolą wykrywa usunięte zasoby podczas skanowania.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555384"
---
# <a name="how-scans-detect-deleted-assets"></a>Jak skanowanie wykrywa usunięte zasoby

W tym artykule opisano sposób, w jaki usługa Azure kontrolą używa wyników skanowania do wykrywania usuniętych zasobów.

## <a name="background-info"></a>Informacje ogólne

Katalog usługi Azure kontrolą jest świadomy stanu magazynu danych tylko podczas jego skanowania. Aby wykaz był wiadomo, czy usunięto plik, tabelę lub kontener, porównuje ostatnie dane wyjściowe skanowania z bieżącymi danymi wyjściowymi skanowania. Załóżmy na przykład, że podczas ostatniego skanowania konta Azure Data Lake Storage Gen2 zostało ono dołączone do folderu o nazwie *Folder1*. Po ponownym przeskanowaniu tego samego konta brakuje *Folder1* . W związku z tym katalog założono, że folder został usunięty.

## <a name="detecting-deleted-files"></a>Wykrywanie usuniętych plików

Logika wykrywania brakujących plików działa w przypadku wielu skanów przez tego samego użytkownika, a także przez różnych użytkowników. Załóżmy na przykład, że użytkownik uruchamia jednorazowe skanowanie w Data Lake Storage Gen2 magazynie danych w folderach A, B i C. Później inny użytkownik na tym samym koncie uruchamia inne jednorazowe skanowania folderów C, D i E tego samego magazynu danych. Ponieważ folder C został dwukrotnie przeskanowany, katalog sprawdza go pod kątem możliwych usunięć. Foldery A, B, D i E są jednak skanowane tylko raz, a wykaz nie będzie sprawdzał ich dla usuniętych zasobów.

Aby zachować pliki usunięte z wykazu, ważne jest uruchamianie zwykłych skanów. Interwał skanowania jest ważny, ponieważ wykaz nie może wykryć usuniętych zasobów do momentu uruchomienia innego skanowania. Tak więc w przypadku uruchamiania skanów raz na miesiąc w określonym magazynie katalog nie będzie mógł wykryć żadnych usuniętych zasobów danych w tym magazynie do momentu uruchomienia następnego skanowania w miesiącu później.

W przypadku wyliczania dużych magazynów danych, takich jak Data Lake Storage Gen2, istnieje wiele sposobów (w tym błędy wyliczeniowe i zdarzenia porzucone) w celu zwolnienia informacji. Określone skanowanie może pominąć utworzenie lub usunięcie pliku. Tak więc, chyba, że katalog nie został usunięty, plik nie jest usuwany z wykazu. Ta strategia oznacza, że błędy mogą wystąpić, jeśli plik, który nie istnieje w skanowanym magazynie danych, nadal istnieje w wykazie. W niektórych przypadkach magazyn danych może wymagać skanowania dwa lub trzy razy przed przechwyceniem niektórych usuniętych zasobów.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z wykazami usługi Azure kontrolą, zobacz [Szybki Start: Tworzenie konta kontrolą](create-catalog-portal.md).
