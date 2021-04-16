---
title: Microsoft Azure StorSimple o wersji Update 1.3 macierzy wirtualnej | Microsoft Docs
description: Opisuje krytyczne otwarte problemy i rozwiązania dla macierzy wirtualnej usługi Azure StorSimple z aktualizacją Update 1.3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389523"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Informacje o wersji macierzy wirtualnej StorSimple Update 1.3

Poniższe informacje o wersji identyfikują krytyczne otwarte problemy i rozwiązane problemy dotyczące Microsoft Azure StorSimple macierzy wirtualnej.

Informacje o wersji są stale aktualizowane. W miarę odkrywania krytycznych problemów wymagających obejścia są do niego dodawane ich opisy. Przed wdrożeniem macierzy wirtualnej storSimple należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja Update 1.3 odpowiada oprogramowaniu w wersji 10.0.10319.0.

> [!IMPORTANT]
> - Aktualizacja Update 1.3 jest aktualizacją krytyczną. Zdecydowanie zalecamy zainstalowanie go tak szybko, jak to możliwe.
> - Aktualizację Update 1.3 można zainstalować tylko na urządzeniach z aktualizacją Update 1.2.
> - Aktualizacje zakłócają działanie i uruchamiają ponownie urządzenie. Jeśli we/wy jest w toku, urządzenie jest w stanie przestoju. Aby uzyskać szczegółowe instrukcje dotyczące pakietów używanych do stosowania tej aktualizacji, przejdź do [strony Pobieranie aktualizacji Update 1.3.](#download-update-13)

## <a name="whats-new-in-update-13"></a>Co nowego w aktualizacji Update 1.3

Ta aktualizacja zawiera następujące ulepszenia: KB4540725

- Transport Layer Security (TLS) 1.2 jest obowiązkową aktualizacją i należy go zainstalować. Od tej wersji protokół TLS 1.2 staje się standardowym protokołem dla wszystkich Azure Portal komunikacji.
  
   Jeśli zostanie wyświetlony następujące ostrzeżenie, przed podjęciem kolejnych czynności należy zaktualizować oprogramowanie na urządzeniu:

   Co najmniej jedno urządzenie StorSimple ma starszą wersję oprogramowania. Najnowsza dostępna aktualizacja dla wersji TLS 1.2 jest obowiązkową aktualizacją i powinna zostać natychmiast zainstalowana na tych urządzeniach. TLS 1.2 jest używany do całej komunikacji Azure Portal, a bez tej aktualizacji urządzenie nie będzie w stanie komunikować się z usługą StorSimple.

- Poprawki błędów dotyczące wyrzucania elementów bezużytecznych poprawiają wydajność cyklu wyrzucania elementów bezużytecznych, gdy urządzenie i konto magazynu znajdują się w dwóch odległych regionach.
- Poprawka błędów tworzenia kopii zapasowej spowodowanych limitami czasu obiektów blob.
- Zaktualizowano poprawki zabezpieczeń programu OS/.NET Framework:
  - [KB4540725:](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86)Marzec 2020 R. (aktualizacja stosu obsługi)
  - [KB4565541:](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01)Zbiorczy opis z lipca 2020 r.
  - [KB4565622:](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00)aktualizacja aktualizacji z lipca 2020 .NET Framework 2020 r.

## <a name="download-update-13"></a>Pobierz aktualizację Update 1.3

Aby pobrać tę aktualizację, przejdź do [serwera Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) i pobierz pakiet KB4575898. Ten pakiet zawiera następujące pakiety. Zainstaluj pakiety w tej kolejności:

1. **KB4540725**, który zawiera zbiorcze aktualizacje systemu Windows dla wersji 2012 R2 do marca 2020 r. Aby uzyskać więcej informacji na temat tego, co znajduje się w tym zestawieniu zbiorczym, przejdź do miesięcznego zestawień zabezpieczeń [w marcu.](https://support.microsoft.com/help/4540725)
1. **KB4565541**, który zawiera zbiorcze aktualizacje systemu Windows dla wersji 2012 R2 do lipca 2020 r. Aby uzyskać więcej informacji na temat tego, co znajduje się w tym zestawieniu zbiorczym, przejdź do tematu July monthly security rollup (Miesięczny [zbiorczy zestaw zabezpieczeń dla lipca).](https://support.microsoft.com/help/4565541)
1. **KB4565622**, który cumulative.NET aktualizacji programu Framework do lipca 2020 r. Aby uzyskać więcej informacji na temat tego, co zawiera ten zestaw zbiorczy, przejdź do [tematu KB4565622.](https://support.microsoft.com/help/4565622)<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. **KB3011067**, który zawiera aktualizacje oprogramowania urządzenia.

Pobierz aktualizację KB4575898 i postępuj zgodnie z tymi instrukcjami, aby [zastosować aktualizację za pośrednictwem lokalnego internetowego interfejsu użytkownika.](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="known-issues-in-update-13"></a>Znane problemy w aktualizacji Update 1.3
W aktualizacji Update 1.3 nie zanotował żadnych nowych problemów. Wszystkie problemy zanotowe w wersji są przeniesiene z poprzednich wersji. Aby wyświetlić podsumowanie znanych problemów z poprzednich wersji, przejdź do tematu Znane problemy w [aktualizacji Update 1.2.](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)

## <a name="next-steps"></a>Następne kroki
Pobierz aktualizację KB4575898 [i zastosuj ją za pomocą lokalnego internetowego interfejsu użytkownika.](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)

## <a name="references"></a>Odwołania
Szukasz starszej wersji? Przejdź do strony:

- [Informacje o wersji macierzy wirtualnej StorSimple Update 1.2](./storsimple-virtual-array-update-12-release-notes.md)
- [Informacje o wersji macierzy wirtualnej StorSimple Update 1.0](./storsimple-virtual-array-update-1-release-notes.md)
- [Informacje o wersji macierzy wirtualnej StorSimple Update 0.6](./storsimple-virtual-array-update-06-release-notes.md)
- [Informacje o wersji macierzy wirtualnej StorSimple Update 0.5](./storsimple-virtual-array-update-05-release-notes.md)
- [Informacje o wersji macierzy wirtualnej StorSimple Update 0.4](./storsimple-virtual-array-update-04-release-notes.md)
- [Informacje o wersji macierzy wirtualnej StorSimple Update 0.3](./storsimple-ova-update-03-release-notes.md)
- [Informacje o wersji macierzy wirtualnej StorSimple Update 0.1 i 0.2](./storsimple-ova-update-01-release-notes.md)
- [Informacje o wersji macierzy wirtualnej StorSimple](./storsimple-virtual-array-update-06-release-notes.md)