---
title: Zarządzanie rekordami kontroli dostępu w StorSimple | Microsoft Docs
description: Opisuje sposób używania rekordów kontroli dostępu (rekordami ACR) do określenia, które hosty mogą łączyć się z woluminem na urządzeniu StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949908"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Zarządzanie rekordami kontroli dostępu za pomocą usługi StorSimple Manager

## <a name="overview"></a>Omówienie
Rekordy kontroli dostępu (rekordami ACR) umożliwiają określenie, które hosty mogą łączyć się z woluminem na urządzeniu StorSimple. Rekordami ACR są ustawiane na określony wolumin i zawierają kwalifikowane nazwy iSCSI (IQNs) hostów. Gdy host próbuje nawiązać połączenie z woluminem, urządzenie sprawdzi ACR skojarzone z tym woluminem dla nazwy IQN i jeśli jest zgodne, połączenie zostanie nawiązane. Rekordy kontroli dostępu w sekcji **Konfiguracja** w bloku usługi StorSimple Device Manager są wyświetlane wszystkie rekordy kontroli dostępu z odpowiednimi IQNs hostów.

Ten samouczek wyjaśnia następujące typowe zadania związane z ACR:

* Dodawanie rekordu kontroli dostępu
* Edytowanie rekordu kontroli dostępu
* Usuwanie rekordu kontroli dostępu

> [!IMPORTANT]
> * Podczas przypisywania ACR do woluminu należy zadbać o to, aby wolumin nie był jednocześnie dostępny przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania elementu ACR z woluminu upewnij się, że odpowiedni host nie uzyskuje dostępu do woluminu, ponieważ usunięcie może spowodować zakłócenie odczytu i zapisu.

## <a name="get-the-iqn"></a>Pobieranie nazwy IQN

Wykonaj następujące kroki, aby uzyskać nazwę IQN hosta z systemem Windows, na którym działa system Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Dodawanie rekordu kontroli dostępu
Aby dodać rekordami ACR, należy użyć sekcji **Konfiguracja** w bloku usługi StorSimple Device Manager. Zazwyczaj należy skojarzyć jeden ACR z jednym woluminem.

Wykonaj następujące kroki, aby dodać ACR.

#### <a name="to-add-an-acr"></a>Aby dodać ACR

1. Przejdź do usługi StorSimple Device Manager, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **rekordy kontroli dostępu**.
2. W bloku **rekordy kontroli dostępu** kliknij pozycję **+ Dodaj ACR**.

    ![Kliknij pozycję Dodaj ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. W bloku **Dodaj ACR** wykonaj następujące czynności:

    1. Podaj nazwę dla ACR.
    
    2. Podaj nazwę IQN hosta systemu Windows Server w polu **Nazwa inicjatora iSCSI (IQN)**.

    3. Kliknij przycisk **Dodaj** , aby utworzyć ACR.

        ![Kliknij pozycję Dodaj ACR 2](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nowo dodana ACR zostanie wyświetlona na liście tabelarycznej rekordami ACR.

    ![Kliknij pozycję Dodaj ACR 3](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Edytowanie rekordu kontroli dostępu
Aby edytować rekordami ACR, należy użyć sekcji **Konfiguracja** w bloku usługi StorSimple Device Manager.

> [!NOTE]
> Zaleca się zmodyfikowanie tylko tych rekordami ACR, które nie są obecnie używane. Aby edytować ACR skojarzone z aktualnie używanym woluminem, musisz najpierw przełączyć wolumin w tryb offline.

Wykonaj następujące kroki, aby edytować ACR.

#### <a name="to-edit-an-access-control-record"></a>Aby edytować rekord kontroli dostępu
1.  Przejdź do usługi StorSimple Device Manager, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **rekordy kontroli dostępu**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabelarycznej liście rekordów kontroli dostępu kliknij i wybierz ACR, które chcesz zmodyfikować.

    ![Edytowanie rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. W bloku **Edytowanie rekordu kontroli dostępu** Podaj inną nazwę IQN odpowiadającą innemu hostowi.

    ![Edytuj rekordy kontroli dostępu 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kliknij pozycję **Zapisz**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Edytuj rekordy kontroli dostępu 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Otrzymasz powiadomienie, gdy ACR zostanie zaktualizowany. Lista tabelaryczna jest również aktualizowana w celu odzwierciedlenia zmiany.

   
## <a name="delete-an-access-control-record"></a>Usuwanie rekordu kontroli dostępu
Aby usunąć rekordami ACR, należy użyć sekcji **Konfiguracja** w bloku usługi StorSimple Device Manager.

> [!NOTE]
> Można usunąć tylko te rekordami ACR, które nie są obecnie używane. Aby usunąć ACR skojarzone z aktualnie używanym woluminem, należy najpierw przełączyć wolumin w tryb offline.

Wykonaj następujące kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu
1.  Przejdź do usługi StorSimple Device Manager, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **rekordy kontroli dostępu**.

    ![Przejdź do rekordów kontroli dostępu 1b](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabelarycznej liście rekordów kontroli dostępu kliknij i wybierz ACR, które chcesz usunąć.

    ![Przejdź do rekordu kontroli dostępu 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kliknij prawym przyciskiem myszy, aby wywoływać menu kontekstowe, a następnie wybierz pozycję **Usuń**.

    ![Przejdź do rekordu kontroli dostępu 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po wyświetleniu monitu o potwierdzenie Przejrzyj informacje, a następnie kliknij przycisk **Usuń**.

    ![Przejdź do rekordów kontroli dostępu 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Otrzymasz powiadomienie po zakończeniu usuwania. Lista tabelaryczna została zaktualizowana w celu odzwierciedlenia usunięcia.

    ![Przejdź do rekordu kontroli dostępu 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarządzaniu woluminami StorSimple](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się więcej [na temat używania usługi StorSimple Manager do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

