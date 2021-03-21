---
title: StorSimple 8000 Series Update 5,1 — informacje o wersji
description: W tym artykule opisano nowe funkcje, problemy i Obejścia dotyczące aktualizacji z serii StorSimple 8000 5,1.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657573"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 Series Update 5,1 — informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowanie krytycznych problemów otwartych dla aktualizacji z serii StorSimple 8000 5,1. Zawierają także listę StorSimple aktualizacji oprogramowania zawartych w tej wersji.

Aktualizację 5,1 można zastosować do dowolnego urządzenia StorSimple z aktualizacją Update 5. Jeśli używasz wersji niższej niż 5, najpierw Zastosuj aktualizację Update 5, a następnie Zastosuj 5,1. Wersja urządzenia skojarzona z aktualizacją 5,1 to 6.3.9600.17885.

Przed wdrożeniem aktualizacji w rozwiązaniu StorSimple należy zapoznać się z informacjami zawartymi w informacjach o wersji.

> [!IMPORTANT]
>
> * Aktualizacja 5,1 jest obowiązkową aktualizacją i należy ją zainstalować od razu. Aby uzyskać więcej informacji, zobacz How to [apply Update 5,1](storsimple-8000-install-update-51.md).
> * Aktualizacja 5,1 ma tylko aktualizacje zabezpieczeń. Zainstalowanie tej aktualizacji trwa około 30 minut. Zdecydowanie zalecamy zastosowanie aktualizacji 5,1 w celu zapewnienia działania urządzenia.
> * W przypadku nowych wersji aktualizacje mogą nie być od razu widoczne, ponieważ przeprowadzamy stopniowe wdrażanie aktualizacji. Poczekaj kilka dni, a następnie ponownie przeprowadź skanowanie w poszukiwaniu aktualizacji, ponieważ te aktualizacje staną się wkrótce dostępne.

## <a name="whats-new-in-update-51"></a>Co nowego w aktualizacji Update 5,1

W aktualizacji Update 5,1 wprowadzono następujące kluczowe ulepszenia i poprawki błędów:

* **Tls 1,2** — ta aktualizacja StorSimple wymusza użycie protokołu TLS 1,2 na wszystkich klientach. Jest to obowiązkowa aktualizacja dla wszystkich urządzeń StorSimple z serii 8000.

   Jeśli zostanie wyświetlone następujące ostrzeżenie, przed kontynuowaniem należy zaktualizować oprogramowanie na urządzeniu:

   Na co najmniej jednym urządzeniu StorSimple jest uruchomiona Starsza wersja oprogramowania. Najnowsza dostępna aktualizacja protokołu TLS 1,2 jest obowiązkową aktualizacją i należy ją od razu zainstalować na tych urządzeniach. Protokół TLS 1,2 jest używany do wszystkich Azure Portal komunikacji i bez tej aktualizacji urządzenie nie będzie w stanie komunikować się z usługą StorSimple.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Znane problemy z aktualizacją 5,1 z poprzednich wersji

W aktualizacji 5,1 nie ma nowych znanych problemów. Aby zapoznać się z listą problemów przeprowadzonych w celu aktualizacji 5,1 z poprzednich wersji, przejdź do [aktualizacji Update 3 informacje o wersji](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Aktualizacje urządzenia w chmurze StorSimple w aktualizacji 5,1

Tej aktualizacji nie można zastosować do urządzenia w chmurze StorSimple (nazywanego również urządzeniem wirtualnym). Nowe urządzenia w chmurze muszą zostać utworzone przy użyciu obrazu aktualizacji 5,1. Aby uzyskać informacje na temat tworzenia urządzenia w chmurze StorSimple, przejdź do obszaru [wdrażanie i zarządzanie urządzeniem w chmurze StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [zainstalować aktualizację 5,1](storsimple-8000-install-update-51.md) na urządzeniu StorSimple.
