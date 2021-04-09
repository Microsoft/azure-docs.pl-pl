---
title: Informacje o wersji 1,2 Microsoft Azure StorSimple wirtualnej macierzy Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla StorSimple wirtualnej macierzy z uruchomioną aktualizacją 1,2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960196"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1,2 — informacje o wersji

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane. W miarę odkrywania krytycznych problemów wymagających obejścia są do niego dodawane ich opisy. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 1,2 jest zgodna z wersją oprogramowania **10.0.10311.0**.

> [!IMPORTANT]
> - Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje. Aby uzyskać szczegółowe instrukcje dotyczące pakietów używanych do zastosowania tej aktualizacji, przejdź do pozycji [Pobierz aktualizację 1,2](#download-update-12).
> - Aktualizacja 1,2 jest dostępna za pośrednictwem Azure Portal tylko wtedy, gdy na urządzeniu jest uruchomiona aktualizacja 1,0 lub 1,1.

## <a name="whats-new-in-update-12"></a>Co nowego w aktualizacji Update 1,2

Ta aktualizacja zawiera następujące poprawki błędów:

- Ulepszona odporność podczas przetwarzania usuniętych plików.
- Ulepszono obsługę wyjątków w ścieżce uruchamiania kodu, co prowadzi do zredukowania błędów w ramach kopii zapasowych, przywracania, odczytów w chmurze i automatycznego odzyskiwania przestrzeni.

## <a name="download-update-12"></a>Pobierz aktualizację 1,2

Aby pobrać tę aktualizację, przejdź do serwera [wykazu Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) i Pobierz pakiet KB4502035. Ten pakiet zawiera następujące pakiety:

 - **KB4493446** , który zawiera zbiorcze aktualizacje systemu Windows dla 2012 R2 do 2019 kwietnia. Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym pakiecie zbiorczym, przejdź do [comiesięcznych zbiorczych zabezpieczeń](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** , który jest Microsoft Update autonomiczny plik pakietu WINDOWSTH-KB3011067-x64. Ten plik jest używany do aktualizacji oprogramowania urządzenia.

Pobierz KB4502035 i postępuj zgodnie z tymi instrukcjami, aby [zastosować aktualizację za pośrednictwem lokalnego interfejsu użytkownika sieci Web](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemy rozwiązane w aktualizacji 1,2

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Usunięcie| W poprzednich wersjach oprogramowania wystąpił problem polegający na tym, że użycie urządzenia nie uległo zmianie nawet po usunięciu plików. Ten problem został rozwiązany w tej wersji. Ścieżka kodu warstwowego została przeprowadzona bardziej odporna podczas przetwarzania usuniętych plików.|
| 2 |Obsługa wyjątków| W poprzednich wersjach oprogramowania wystąpił problem po ponownym uruchomieniu systemu, który może potencjalnie prowadzić do błędów w kopiach zapasowych, przywracania, odczytywania danych z chmury i automatycznego odzyskiwania przestrzeni. Ta wersja zawiera zmiany w sposobie obsługi wyjątków w ścieżce początkowej.|

## <a name="known-issues-in-update-12"></a>Znane problemy w aktualizacji 1,2

Nie znaleziono żadnych nowych problemów w wersji Update 1,2. Wszystkie problemy odnotowane w wersji są przenoszone z poprzednich wersji. Aby zobaczyć podsumowanie znanych problemów uwzględnionych w poprzednich wersjach, przejdź do sekcji [znane problemy w artykule aktualizacja 1,1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Następne kroki

Pobierz KB4502035 i [Zastosuj aktualizację za pośrednictwem lokalnego interfejsu użytkownika sieci Web](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Odwołania

Szukasz starszej wersji uwagi? Przejdź do strony:
* [StorSimple Virtual Array Update 1,1 — informacje o wersji](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1,0 — informacje o wersji](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 — informacje o wersji](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 — informacje o wersji](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 — Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 — informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple](./storsimple-virtual-array-update-06-release-notes.md)