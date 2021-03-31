---
title: Zarządzanie rekordami kontroli dostępu dla macierzy wirtualnej StorSimple | Microsoft Docs
description: Opisuje, jak zarządzać rekordami kontroli dostępu (rekordami ACR) w celu określenia, które hosty mogą łączyć się z woluminem w macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad0d7adfd77dff53b1582e63a91f2cd87a9233d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85507623"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Używanie StorSimple Device Manager do zarządzania rekordami kontroli dostępu dla StorSimple wirtualnej macierzy

## <a name="overview"></a>Omówienie

Rekordy kontroli dostępu (rekordami ACR) umożliwiają określenie, które hosty mogą łączyć się z woluminem w macierzy wirtualnej StorSimple (znanej również jako StorSimple lokalne urządzenie wirtualne). Rekordami ACR są ustawiane na określony wolumin i zawierają kwalifikowane nazwy iSCSI (IQNs) hostów. Gdy host próbuje nawiązać połączenie z woluminem, urządzenie sprawdzi ACR skojarzone z tym woluminem dla nazwy IQN i jeśli jest zgodne, połączenie zostanie nawiązane. Blok **rekordów kontroli dostępu** w sekcji **Konfiguracja** usługi Device Manager wyświetla wszystkie rekordy kontroli dostępu z odpowiednimi IQNsmi hostów.

![Zarządzanie rekordami kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Ten samouczek wyjaśnia następujące typowe zadania związane z ACR:

* Pobieranie nazwy IQN
* Dodawanie rekordu kontroli dostępu
* Edytowanie rekordu kontroli dostępu
* Usuwanie rekordu kontroli dostępu

> [!IMPORTANT]
> 
> * Podczas przypisywania ACR do woluminu należy zadbać o to, aby wolumin nie był jednocześnie dostępny przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania elementu ACR z woluminu upewnij się, że odpowiedni host nie uzyskuje dostępu do woluminu, ponieważ usunięcie może spowodować zakłócenie odczytu i zapisu.


## <a name="get-the-iqn"></a>Pobieranie nazwy IQN

Wykonaj następujące kroki, aby uzyskać nazwę IQN hosta z systemem Windows, na którym działa system Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Dodawanie elementu ACR

Aby dodać rekordami ACR, należy użyć bloku **rekordy kontroli dostępu** w sekcji **konfiguracja** usługi StorSimple Device Manager. Zazwyczaj należy skojarzyć jeden ACR z jednym woluminem.

Aby uzyskać informacje dotyczące kojarzenia ACR z woluminem, przejdź do obszaru [Dodawanie woluminu](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Podczas przypisywania ACR do woluminu należy zadbać o to, aby wolumin nie był jednocześnie dostępny przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.


Wykonaj następujące kroki, aby dodać ACR.

#### <a name="to-add-an-acr"></a>Aby dodać ACR

1. Na stronie miejsce docelowe usługi wybierz swoją usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **rekordy kontroli dostępu**.
2. W bloku **rekordy kontroli dostępu** kliknij pozycję **Dodaj**.
3. W bloku **Dodaj ACR** wykonaj następujące czynności:
   
    1. Wypełnij pole **Nazwa** dla rekordu ACR.
    
    2. W obszarze **Nazwa inicjatora iSCSI** Podaj nazwę IQN hosta systemu Windows. Aby uzyskać nazwę IQN hosta z systemem Windows Server, wykonaj następujące czynności:
   
    3. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows. W oknie Właściwości inicjatora iSCSI na karcie **Konfiguracja** zaznacz i skopiuj ciąg z pola **Nazwa inicjatora**.
    Wklej ten ciąg w polu **IQN** w bloku **Dodawanie ACR** .
   
    6. Kliknij przycisk **Dodaj** , aby dodać ACR.  
   
        ![Dodawanie rekordów kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Lista tabelaryczna została zaktualizowana w celu odzwierciedlenia tego dodania.

## <a name="edit-an-acr"></a>Edytowanie elementu ACR

Użyj bloku **rekordy kontroli dostępu** w sekcji **Konfiguracja** usługi Device Manager w Azure Portal, aby edytować rekordami ACR.

> [!NOTE]
> Nie należy modyfikować elementu ACR, który jest obecnie używany. Aby edytować ACR skojarzone z aktualnie używanym woluminem, należy najpierw przełączyć wolumin w tryb offline.


Wykonaj następujące kroki, aby edytować ACR.

#### <a name="to-edit-an-acr"></a>Aby edytować ACR

1. Na stronie miejsce docelowe usługi wybierz swoją usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** **rekordy kontroli dostępu**.
2. W bloku **rekordy kontroli dostępu** , z tabelarycznej listy rekordów kontroli dostępu, kliknij dwukrotnie ACR, które chcesz zmodyfikować.
3. W bloku **Edytuj rekordy kontroli dostępu** wykonaj następujące czynności:
   
    1. Podaj nazwę IQN dla ACR.
   
    2. Kliknij pozycję **Zapisz** w górnej części bloku, aby zapisać zmodyfikowane ACR. Zobaczysz następujący komunikat potwierdzający:
   
        ![Edytowanie rekordów kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Usuwanie rekordu kontroli dostępu

Strona **Konfiguracja** w Azure Portal służy do usuwania rekordami ACR.

> [!NOTE]
> 
> * Nie należy usuwać ACR, który jest obecnie używany. Aby usunąć ACR skojarzone z aktualnie używanym woluminem, należy najpierw przełączyć wolumin w tryb offline.
> * Podczas usuwania elementu ACR z woluminu upewnij się, że odpowiedni host nie uzyskuje dostępu do woluminu, ponieważ usunięcie może spowodować zakłócenie odczytu i zapisu.


Wykonaj następujące kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu

1. Na stronie miejsce docelowe usługi wybierz swoją usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** **rekordy kontroli dostępu**.

2. W bloku **rekordy kontroli dostępu** , z tabelarycznej listy rekordów kontroli dostępu, kliknij dwukrotnie ACR, które chcesz usunąć.

3. W bloku Edycja rekordów kontroli dostępu kliknij pozycję **Usuń**.
   
    ![Usuń REKORDAMI ACR](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Usuń** , aby kontynuować usuwanie. Lista tabelaryczna została zaktualizowana w celu odzwierciedlenia usunięcia.
   
   ![Komunikat ostrzegawczy](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [dodawania woluminów i konfigurowania rekordami ACR](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

