---
title: Zarządzanie kontem magazynu Azure Stack Edge | Microsoft Docs
description: Opisuje sposób używania Azure Portal do zarządzania kontem magazynu na Azure Stack brzegowej.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 12/11/2019
ms.author: alkohli
ms.openlocfilehash: 01148fabd6fb5df791ac09090304feee8c72a2d1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086619"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge"></a>Użyj Azure Portal, aby zarządzać kontami magazynu Edge na Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym artykule opisano sposób zarządzania kontami magazynu brzegowego na Azure Stackej krawędzi. Możesz zarządzać Azure Stack krawędzią za pośrednictwem Azure Portal lub za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal, aby dodać lub usunąć konta magazynu brzegowego na urządzeniu.

## <a name="about-edge-storage-accounts"></a>Informacje o kontach magazynu Edge

Dane z urządzenia brzegowego Azure Stack można przenieść za pośrednictwem protokołów SMB, NFS lub REST. Aby przesłać dane do usługi BLOB Storage przy użyciu interfejsów API REST, należy utworzyć konta magazynu brzegowego na Azure Stack Edge. 

Konta magazynu brzegowego dodawane na Azure Stack urządzeniu brzegowym są mapowane na konta usługi Azure Storage. Wszystkie dane zapisywane na kontach magazynu brzegowego są automatycznie wypychane do chmury.

Poniżej przedstawiono diagram przedstawiający dwa typy kont i sposób przepływu danych z każdego z tych kont na platformie Azure:

![Diagram dla kont usługi BLOB Storage](media/azure-stack-edge-j-series-manage-storage-accounts/ase-blob-storage.svg)

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie konta magazynu brzegowego
> * Usuwanie konta magazynu brzegowego


## <a name="add-an-edge-storage-account"></a>Dodawanie konta magazynu brzegowego

Aby utworzyć konto magazynu brzegowego, wykonaj następującą procedurę:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Usuwanie konta magazynu brzegowego

Wykonaj poniższe czynności, aby usunąć konto magazynu Edge.

1. Przejdź do pozycji **Configuration > Storage accounts** (zasoby). Z listy kont magazynu wybierz konto magazynu, które chcesz usunąć. Na górnym pasku poleceń wybierz pozycję **Usuń konto magazynu**.

    ![Przejdź do listy kont magazynu](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-1.png)

2. W bloku **usuwanie konta magazynu** Potwierdź konto magazynu, które chcesz usunąć, a następnie wybierz pozycję **Usuń**.

    ![Potwierdź i Usuń konto magazynu](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-2.png)

Lista kont magazynu jest aktualizowana w celu odzwierciedlenia usunięcia.


## <a name="add-delete-a-container"></a>Dodawanie, usuwanie kontenera

Można również dodawać lub usuwać kontenery dla tych kont magazynu.

Aby dodać kontener, wykonaj następujące czynności:

1. Wybierz konto magazynu, którym chcesz zarządzać. Na górnym pasku poleceń wybierz pozycję **+ Dodaj kontener**.

    ![Wybierz konto magazynu, aby dodać kontener](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-1.png)

2. Podaj nazwę kontenera. Ten kontener jest tworzony w ramach konta magazynu brzegowego, a także konta usługi Azure Storage zamapowanego na to konto. 

    ![Dodawanie kontenera krawędzi](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-2.png)

Lista kontenerów jest aktualizowana w celu odzwierciedlenia nowo dodanego kontenera.

![Zaktualizowana lista kontenerów](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-4.png)

Teraz można wybrać kontener z tej listy i wybrać pozycję **+ Usuń kontener** z górnego paska poleceń. 

![Usuwanie kontenera](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Synchronizowanie kluczy magazynu

Klucze dostępu można synchronizować z lokalnymi kontami magazynu na urządzeniu. 

Aby zsynchronizować klucz dostępu do konta magazynu, wykonaj następujące czynności:

1. W swoim zasobie wybierz konto magazynu, którym chcesz zarządzać. Na górnym pasku poleceń wybierz pozycję **Synchronizuj klucz magazynu**.

    ![Wybierz pozycję Synchronizuj klucz magazynu](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-1.png)

2. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

    ![Wybierz pozycję Synchronizuj klucz magazynu](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](azure-stack-edge-j-series-manage-users.md).
