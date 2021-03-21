---
title: Zastąp urządzenie Azure Stack EDGE Pro | Microsoft Docs
description: Zawiera opis sposobu pobierania zastępczego urządzenia Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2196c9463569dc43092b38de58e0103104efed0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443475"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Zastąp swoje urządzenie Azure Stack EDGE Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób zamiany urządzenia z usługą Azure Stack EDGE Pro. Urządzenie zamienne jest wymagane, gdy istniejące urządzenie ma awarię sprzętową lub wymaga uaktualnienia. 


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Otwórz bilet pomocy technicznej na potrzeby problemu ze sprzętem
> * Utwórz nowe zamówienie dla urządzenia zastępczego w Azure Portal
> * Instalowanie, aktywowanie urządzenia zastępującego
> * Zwróć oryginalne urządzenie

## <a name="open-a-support-ticket"></a>Otwórz bilet pomocy technicznej

Jeśli istniejące urządzenie ma awarię sprzętową, Otwórz bilet pomocy technicznej, wykonując następujące czynności:

1. Otwórz bilet pomocy technicznej z pomoc techniczna firmy Microsoft wskazujący, że chcesz zwrócić urządzenie. Wybierz typ problemu **sprzętowego programu Azure Stack EDGE Pro** i wybierz podtyp **problemów sprzętowych** .  

    ![Otwieranie biletu pomocy technicznej](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Inżynier pomoc techniczna firmy Microsoft będzie mógł skontaktować się z Tobą w celu ustalenia, czy jednostka zamienna pola (FRU) może rozwiązać ten problem i czy jest dostępna dla tego wystąpienia. Jeśli jednostka FRU jest niedostępna lub urządzenie wymaga uaktualnienia sprzętowego, pomoc techniczna przeprowadzi Cię przez proces umieszczania nowego zamówienia i zwrócenia starego urządzenia.

## <a name="create-a-new-order"></a>Utwórz nowe zamówienie

Utwórz nowy zasób na potrzeby aktywacji urządzenia zastępczego, wykonując kroki opisane w temacie [Tworzenie nowego zasobu](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

> [!NOTE]
> Aktywacja urządzenia zastępującego względem istniejącego zasobu nie jest obsługiwana. Nowy zasób jest traktowany jako nowe zamówienie. Po wysłaniu urządzenia do Ciebie zostanie naliczona stawka za 14 dni.

## <a name="install-and-activate-the-replacement-device"></a>Instalowanie i aktywowanie urządzenia zastępującego

Wykonaj następujące kroki, aby zainstalować i aktywować urządzenie zamienne:

1. [Zainstaluj urządzenie](azure-stack-edge-deploy-install.md).
2. [Aktywuj urządzenie](azure-stack-edge-deploy-connect-setup-activate.md) przy użyciu nowego zasobu, który został utworzony wcześniej.

## <a name="return-your-existing-device"></a>Zwróć istniejące urządzenie

Wykonaj wszystkie kroki, aby zwrócić oryginalne urządzenie:

1. [Wymazywanie danych z urządzenia](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Zainicjuj zwrot urządzenia](azure-stack-edge-return-device.md#initiate-device-return) dla oryginalnego urządzenia.
3. [Zaplanuj pobranie](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Po otrzymaniu urządzenia w firmie Microsoft można [usunąć zasób](azure-stack-edge-return-device.md#delete-the-resource) skojarzony z zwróconym urządzeniem.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, w jaki sposób [zwrócić urządzenie Azure Stack Edge](azure-stack-edge-return-device.md).
