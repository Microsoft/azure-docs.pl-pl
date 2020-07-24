---
title: Zastąp urządzenie Azure Stack Edge | Microsoft Docs
description: Opisuje sposób pobierania zamiennika Azure Stack urządzenia brzegowego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098750"
---
# <a name="replace-your-azure-stack-edge-device"></a>Zastąp urządzenie Azure Stack Edge

W tym artykule opisano sposób pobierania zamiennika Azure Stack urządzenia brzegowego. Urządzenie zamienne jest wymagane, gdy istniejące urządzenie ma awarię sprzętową lub wymaga uaktualnienia. 


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Otwórz bilet pomocy technicznej na potrzeby problemu ze sprzętem
> * Utwórz nowy zasób dla urządzenia zastępczego w Azure Portal
> * Instalowanie, aktywowanie urządzenia zastępującego
> * Zwróć oryginalne urządzenie

## <a name="open-a-support-ticket"></a>Otwórz bilet pomocy technicznej

Jeśli istniejące urządzenie ma awarię sprzętową, Otwórz bilet pomocy technicznej. Pomoc techniczna firmy Microsoft ustali, że jednostka zamienna pola (FRU) nie jest dostępna dla tego wystąpienia lub urządzenie wymaga uaktualnienia sprzętowego. W obu przypadkach pomoc techniczna będzie zamawiać urządzenie zamienne.

1. Otwórz bilet pomocy technicznej z pomoc techniczna firmy Microsoft wskazujący, że chcesz zwrócić urządzenie. Wybierz typ problemu jako **urządzenie Azure Stack Edge**.

    ![Otwieranie biletu pomocy technicznej](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Inżynier pomoc techniczna firmy Microsoft skontaktuje się z Tobą. Podaj szczegóły dotyczące wysyłki.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Tworzenie zasobu dla urządzenia zastępczego

Wykonaj następujące kroki, aby utworzyć zasób.

1. Wykonaj kroki opisane w temacie [Tworzenie nowego zasobu](azure-stack-edge-deploy-prep.md#create-a-new-resource) , aby utworzyć zasób dla urządzenia zastępczego. 

2. Upewnij się, że pole wyboru jest zaznaczone z **urządzeniem Azure Stack Edge**. 

    ![Zasób urządzenia do zastępowania](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Instalowanie i aktywowanie urządzenia zastępującego

Wykonaj następujące kroki, aby zainstalować i aktywować urządzenie zamienne:

1. [Zainstaluj urządzenie](azure-stack-edge-deploy-install.md).

2. [Aktywuj urządzenie](azure-stack-edge-deploy-connect-setup-activate.md) przy użyciu nowego zasobu, który został utworzony wcześniej.

## <a name="return-your-existing-device"></a>Zwróć istniejące urządzenie

Wykonaj wszystkie kroki, aby zwrócić oryginalne urządzenie:

1. [Wymazywanie danych z urządzenia](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Zainicjuj zwrot urządzenia](azure-stack-edge-return-device.md#initiate-device-return) dla oryginalnego urządzenia.
3. [Zaplanuj pobranie](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Usuń zasób](azure-stack-edge-return-device.md#delete-the-resource) skojarzony z zwróconym urządzeniem.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zwrócić Azure Stack Urządzenie brzegowe](azure-stack-edge-return-device.md).
