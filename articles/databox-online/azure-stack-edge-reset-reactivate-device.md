---
title: Zresetuj i ponownie Aktywuj urządzenie Azure Stack EDGE Pro | Microsoft Docs
description: Dowiedz się, jak wyczyścić dane z usługi, a następnie uaktywnić ponownie urządzenie Azure Stack EDGE Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2020
ms.author: alkohli
ms.openlocfilehash: 95fbe5dc4a934fee10c558f640bc24f261203a33
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102443539"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Zresetuj i ponownie Aktywuj urządzenie Azure Stack EDGE Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób resetowania, ponownej konfiguracji i ponownego uaktywnienia urządzenia z programem Azure Stack EDGE Pro, jeśli występują problemy z urządzeniem lub trzeba zacząć od nowa przyczyna.

Po zresetowaniu urządzenia w celu usunięcia danych należy ponownie aktywować urządzenie jako nowy zasób. Zresetowanie urządzenia spowoduje usunięcie konfiguracji urządzenia, dlatego należy zmienić konfigurację urządzenia za pomocą lokalnego interfejsu użytkownika sieci Web.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Czyszczenie danych z dysków z danymi na urządzeniu
> * Aktywuj ponownie urządzenie, tworząc nowe zamówienie, konfigurując ponownie urządzenie i aktywując je

## <a name="reset-data-from-the-device"></a>Resetowanie danych z urządzenia

Aby wyczyścić dane z dysków danych urządzenia, należy zresetować urządzenie.

Przed zresetowaniem Utwórz kopię danych lokalnych na urządzeniu, jeśli jest to konieczne. Dane z urządzenia można skopiować do kontenera usługi Azure Storage.

>[!IMPORTANT]
> Zresetowanie urządzenia spowoduje wymazanie wszystkich danych lokalnych i obciążeń z urządzenia, a tym samym nie można cofnąć. Zresetuj urządzenie tylko wtedy, gdy chcesz zacząć od początku do urządzenia.

Możesz zresetować urządzenie w lokalnym interfejsie użytkownika sieci Web lub w programie PowerShell. Aby uzyskać instrukcje dotyczące programu PowerShell, zobacz [Resetowanie urządzenia](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>Aktywuj ponownie urządzenie

Po zresetowaniu urządzenia należy ponownie aktywować urządzenie jako nowy zasób. Po wprowadzeniu nowego zamówienia należy ponownie skonfigurować i ponownie aktywować nowy zasób.

Aby ponownie uaktywnić istniejące urządzenie, wykonaj następujące kroki:

1. Utwórz nowe zamówienie dla istniejącego urządzenia, wykonując kroki opisane w temacie [Tworzenie nowego zasobu](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource). Na karcie **adres wysyłkowy** wybierz pozycję **mam już urządzenie**.

   ![Nie określaj nowego urządzenia w adresie wysyłkowym](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Pobierz klucz aktywacji](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key).

1. [Nawiąż połączenie z urządzeniem](azure-stack-edge-gpu-deploy-connect.md).

1. [Skonfiguruj sieć dla urządzenia](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Skonfiguruj ustawienia urządzenia](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Konfiguruj certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Aktywuj urządzenie](azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [nawiązać połączenie z urządzeniem Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-connect.md).
