---
title: Informacje o wersji 1,3 Microsoft Azure StorSimple wirtualnej macierzy Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla macierzy wirtualnej Azure StorSimple z aktualizacją 1,3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: b2c2af4267140370c2aa9421a67cb3a6f413165f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657556"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array Update 1,3 — informacje o wersji

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane. W miarę odkrywania krytycznych problemów wymagających obejścia są do niego dodawane ich opisy. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 1,3 jest zgodna z wersją oprogramowania 10.0.10319.0.

> [!IMPORTANT]
> - Aktualizacja 1,3 jest aktualizacją krytyczną. Zdecydowanie zalecamy zainstalowanie go tak szybko, jak to możliwe.
> - Aktualizację Update 1,3 można zainstalować tylko na urządzeniach z aktualizacją Update 1,2.
> - Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje. Aby uzyskać szczegółowe instrukcje dotyczące pakietów używanych do zastosowania tej aktualizacji, przejdź do pozycji [Pobierz aktualizację 1,3](#download-update-13).

## <a name="whats-new-in-update-13"></a>Co nowego w aktualizacji Update 1,3

Ta aktualizacja zawiera następujące ulepszenia: KB4540725

- Transport Layer Security (TLS) 1,2 jest obowiązkową aktualizacją i musi być zainstalowana. W tym przypadku protokół TLS 1,2 jest standardowym protokołem dla całej komunikacji Azure Portal.
  
   Jeśli zostanie wyświetlone następujące ostrzeżenie, przed kontynuowaniem należy zaktualizować oprogramowanie na urządzeniu:

   Na co najmniej jednym urządzeniu StorSimple jest uruchomiona Starsza wersja oprogramowania. Najnowsza dostępna aktualizacja protokołu TLS 1,2 jest obowiązkową aktualizacją i należy ją od razu zainstalować na tych urządzeniach. Protokół TLS 1,2 jest używany do wszystkich Azure Portal komunikacji i bez tej aktualizacji urządzenie nie będzie w stanie komunikować się z usługą StorSimple.

- Poprawki błędów wyrzucania elementów bezużytecznych zwiększają wydajność cyklu wyrzucania elementów bezużytecznych, gdy urządzenie i konto magazynu znajdują się w dwóch odległych regionach.
- Poprawka niepowodzeń tworzenia kopii zapasowej z powodu przekroczeń limitu czasu obiektów BLOB.
- Zaktualizowano poprawki zabezpieczeń systemu operacyjnego/. NET Framework:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): marzec 2020 SSU (obsługa aktualizacji stosu)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): zbiorcze informacje o 2020 lipca
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): Aktualizacja .NET Framework lipca 2020

## <a name="download-update-13"></a>Pobierz aktualizację 1,3

Aby pobrać tę aktualizację, przejdź do serwera [wykazu Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) i Pobierz pakiet KB4575898. Ten pakiet zawiera następujące pakiety:

- **KB4540725**, który zawiera zbiorcze aktualizacje systemu Windows dla 2012 R2 do marca 2020. Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym pakiecie zbiorczym, przejdź do [comiesięcznego zestawienia zabezpieczeń w marcu](https://support.microsoft.com/help/4540725).
- **KB4565541**, który zawiera zbiorcze aktualizacje systemu Windows dla 2012 R2 do lipca 2020. Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym pakiecie zbiorczym, przejdź do [comiesięcznych zestawień zabezpieczeń w lutym](https://support.microsoft.com/help/4565541).
- **KB4565622**, który zawiera aktualizacje Cumulative.NET Framework do lipca 2020. Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym pakiecie zbiorczym, przejdź do [comiesięcznych zestawień zabezpieczeń w lutym](https://support.microsoft.com/help/4565622).
- **KB3011067**, który zawiera aktualizacje oprogramowania urządzenia.

Pobierz KB4575898 i postępuj zgodnie z tymi instrukcjami, aby [zastosować aktualizację za pośrednictwem lokalnego interfejsu użytkownika sieci Web](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Znane problemy w aktualizacji 1,3
Nie znaleziono żadnych nowych problemów w wersji Update 1,3. Wszystkie problemy odnotowane w wersji są przenoszone z poprzednich wersji. Aby zobaczyć podsumowanie znanych problemów uwzględnionych w poprzednich wersjach, przejdź do sekcji [znane problemy w artykule aktualizacja 1,2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Następne kroki
Pobierz KB4575898 i [Zastosuj aktualizację za pośrednictwem lokalnego interfejsu użytkownika sieci Web](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Odwołania
Szukasz starszej wersji uwagi? Przejdź do strony:

- [StorSimple Virtual Array Update 1,2 — informacje o wersji](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1,0 — informacje o wersji](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0,6 — informacje o wersji](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0,5 — informacje o wersji](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0,4 — Informacje o wersji](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0,3 — informacje o wersji](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](./storsimple-ova-update-01-release-notes.md)
- [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple](./storsimple-virtual-array-update-06-release-notes.md)