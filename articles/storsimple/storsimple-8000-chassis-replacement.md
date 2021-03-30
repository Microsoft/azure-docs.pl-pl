---
title: Zastąp podstawę na urządzeniu z serii StorSimple 8000 | Microsoft Docs
description: Opisuje sposób usuwania i zastępowania obudowy podstawowego obudowy StorSimple lub obudowy EBOD.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: de0f6299f35f9d76fdade976bf70456426e5ec51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85513433"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Zastępowanie obudowy na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak usunąć i zastąpić podstawę na urządzeniu z serii StorSimple 8000. Model StorSimple 8100 to pojedyncze urządzenie obudowy (jedna obudowa), a 8600 to urządzenie dwuobudówowe (dwie obudowy). W przypadku modelu 8600 istnieje potencjalnie dwie obudowy, które mogą kończyć się niepowodzeniem na urządzeniu: Obudowa podstawowa lub obudowa obudowy EBOD.

W obu przypadkach obudowa zamienna dostarczana przez firmę Microsoft jest pusta. Nie będą uwzględniane moduły modułów do zarządzania i chłodzenia (PCMs), moduły kontrolerów, dyski półprzewodnikowe (dysków SSD), dyski twarde (HDD) ani moduły EBOD.

> [!IMPORTANT]
> Przed usunięciem i zastępowaniem obudowy zapoznaj się z informacjami o zabezpieczeniach w [StorSimple składników sprzętowych](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Usuwanie obudowy
Wykonaj następujące kroki, aby usunąć podstawę na urządzeniu StorSimple.

#### <a name="to-remove-a-chassis"></a>Aby usunąć obudowę
1. Upewnij się, że urządzenie StorSimple jest wyłączone i odłączone ze wszystkich źródeł zasilania.
2. Usuń wszystkie kable sieci i sygnatury dostępu współdzielonego, jeśli ma to zastosowanie.
3. Usuń jednostkę z stojaka.
4. Usuń wszystkie dyski i zanotuj gniazda, z których zostały usunięte. Aby uzyskać więcej informacji, zobacz [usuwanie dysku](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. W obudowie EBOD (jeśli jest to obudowa, która się nie powiodła), Usuń moduły kontrolera EBOD. Aby uzyskać więcej informacji, zobacz [Usuwanie kontrolera EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    W obudowie podstawowej (jeśli jest to obudowa, która się nie powiodła), Usuń kontrolery i zanotuj gniazda, z których zostały usunięte. Aby uzyskać więcej informacji, zobacz [Usuwanie kontrolera](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalowanie obudowy
Wykonaj następujące kroki, aby zainstalować podstawę na urządzeniu StorSimple.

#### <a name="to-install-a-chassis"></a>Aby zainstalować obudowę
1. Zainstaluj podstawę w stojaku. Aby uzyskać więcej informacji, zobacz [stojak — Instalowanie urządzenia z systemem StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) lub [stojaka — instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Po zainstalowaniu obudowy w stojaku Zainstaluj moduły kontrolera w tych samych miejscach, w których zostały wcześniej zainstalowane.
3. Zainstaluj dyski w tych samych położeniach i miejscach, w których zostały wcześniej zainstalowane.
   
   > [!NOTE]
   > Zalecamy najpierw zainstalować dysków SSD w gniazdach, a następnie zainstalować HDD.
  
4. Na urządzeniu zainstalowanym w stojaku i zainstalowanych składnikach Podłącz urządzenie do odpowiednich źródeł, a następnie włącz urządzenie. Aby uzyskać szczegółowe informacje, zobacz [kabel urządzenia z StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) lub [kabel urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

