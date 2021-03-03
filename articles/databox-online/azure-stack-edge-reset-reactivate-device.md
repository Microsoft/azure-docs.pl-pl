---
title: Zresetuj i ponownie Aktywuj urządzenie Azure Stack EDGE Pro | Microsoft Docs
description: Dowiedz się, jak wyczyścić dane z usługi, a następnie uaktywnić ponownie urządzenie Azure Stack EDGE Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746900"
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

[! INCLUDE] [Resetowanie danych z urządzenia](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>Aktywuj ponownie urządzenie

Po zresetowaniu urządzenia należy ponownie aktywować urządzenie jako nowy zasób. Po wprowadzeniu nowego zamówienia należy ponownie skonfigurować i ponownie aktywować nowy zasób.

Aby ponownie uaktywnić istniejące urządzenie, wykonaj następujące kroki:

1. Utwórz nowe zamówienie dla istniejącego urządzenia, wykonując kroki opisane w temacie [Tworzenie nowego zasobu](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource). Na karcie **adres wysyłkowy** wybierz pozycję **mam już urządzenie**.

   ![Nie określaj nowego urządzenia w adresie wysyłkowym](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Pobierz klucz aktywacji](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key).

1. [Nawiąż połączenie z urządzeniem](azure-stack-edge-gpu-deploy-connect.md).

1. [Skonfiguruj sieć dla urządzenia](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Skonfiguruj ustawienia urządzenia](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Konfiguruj certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Aktywuj urządzenie](databox-online/azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [nawiązać połączenie z urządzeniem Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-connect.md).
