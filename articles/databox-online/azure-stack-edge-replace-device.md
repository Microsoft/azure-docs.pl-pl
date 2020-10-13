---
title: Zastąp urządzenie Azure Stack EDGE Pro | Microsoft Docs
description: Zawiera opis sposobu pobierania zastępczego urządzenia Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: ec16a2b42b818e96399b8fdbad4a0951f84ef825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893891"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Zastąp swoje urządzenie Azure Stack EDGE Pro

W tym artykule opisano, jak uzyskać zastępowanie Azure Stack Edge na urządzeniu. Urządzenie zamienne jest wymagane, gdy istniejące urządzenie ma awarię sprzętową lub wymaga uaktualnienia. 


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Otwórz bilet pomocy technicznej na potrzeby problemu ze sprzętem
> * Utwórz nowy zasób dla urządzenia zastępczego w Azure Portal
> * Instalowanie, aktywowanie urządzenia zastępującego
> * Zwróć oryginalne urządzenie

## <a name="open-a-support-ticket"></a>Otwórz bilet pomocy technicznej

Jeśli istniejące urządzenie ma awarię sprzętową, Otwórz bilet pomocy technicznej. Pomoc techniczna firmy Microsoft ustali, że jednostka wymiany w terenie (FRU) nie jest dostępna dla tego wystąpienia lub urządzenie wymaga uaktualnienia sprzętowego. W obu przypadkach pomoc techniczna będzie zamawiać urządzenie zamienne.

1. Otwórz bilet pomocy technicznej z pomoc techniczna firmy Microsoft wskazujący, że chcesz zwrócić urządzenie. Wybierz typ problemu jako **urządzenie Azure Stack EDGE Pro**.

    ![Otwieranie biletu pomocy technicznej](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Inżynier pomoc techniczna firmy Microsoft skontaktuje się z Tobą. Podaj szczegóły dotyczące wysyłki.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Tworzenie zasobu dla urządzenia zastępczego

Wykonaj następujące kroki, aby utworzyć zasób.

1. Wykonaj kroki opisane w temacie [Tworzenie nowego zasobu](azure-stack-edge-deploy-prep.md#create-a-new-resource) , aby utworzyć zasób dla urządzenia zastępczego. 

2. Upewnij się, że pole wyboru jest zaznaczone, aby **było dostępne urządzenie Azure Stack EDGE Pro**. 

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

- Dowiedz się, w jaki sposób [zwrócić urządzenie Azure Stack Edge](azure-stack-edge-return-device.md).
