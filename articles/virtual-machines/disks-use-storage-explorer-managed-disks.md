---
title: Zarządzanie usługą Azure Managed disks za pomocą Eksplorator usługi Azure Storage
description: Dowiedz się, jak przekazywać, pobierać i migrować dysk zarządzany przez platformę Azure w różnych regionach i utworzyć migawkę dysku zarządzanego przy użyciu Eksplorator usługi Azure Storage.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88749374"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Zarządzanie usługą Azure Managed disks za pomocą Eksplorator usługi Azure Storage

Eksplorator usługi Storage 1.10.0 umożliwia użytkownikom przekazywanie, pobieranie i kopiowanie dysków zarządzanych, a także tworzenie migawek. Ze względu na te dodatkowe możliwości można używać Eksplorator usługi Storage do migrowania danych z lokalnego na platformę Azure oraz migrowania danych między regionami platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące elementy:
- Subskrypcja platformy Azure
- Co najmniej jeden dysk zarządzany przez platformę Azure
- Najnowsza wersja [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

Jeśli Eksplorator usługi Storage nie jest połączony z platformą Azure, nie będzie można używać jej do zarządzania zasobami. Ta sekcja przechodzi przez połączenie z kontem platformy Azure, dzięki czemu można zarządzać zasobami przy użyciu Eksplorator usługi Storage.

1. Uruchom Eksplorator usługi Azure Storage i kliknij ikonę **wtyczki** po lewej stronie.

    ![Kliknij ikonę wtyczki](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie kliknij przycisk **dalej**.

    ![Dodawanie konta platformy Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. W oknie dialogowym **Logowanie do platformy Azure** wprowadź swoje poświadczenia platformy Azure.

    ![Okno dialogowe logowania do platformy Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Wybierz subskrypcję z listy, a następnie kliknij przycisk **Zastosuj**.

    ![Wybierz swoją subskrypcję](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Przekaż dysk zarządzany na podstawie Premium wirtualnego dysku twardego

1. W okienku po lewej stronie rozwiń pozycję **dyski** i wybierz grupę zasobów, do której chcesz przekazać dysk.

    ![Wybierz grupę zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Wybierz pozycję **Przekaż**.

    ![Wybieranie pozycji Przekaż](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. W obszarze **przekazywanie wirtualnego dysku twardego** Określ źródłowy wirtualny dysk twardy, nazwę dysku, typ systemu operacyjnego, region, do którego chcesz przekazać dysk, a także typ konta. W niektórych regionach są obsługiwane strefy dostępności dla tych regionów. możesz wybrać wybraną strefę.
1. Wybierz pozycję **Utwórz** , aby rozpocząć przekazywanie dysku.

    ![Okno dialogowe przekazywania wirtualnego dysku twardego](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Stan przekazywania będzie teraz wyświetlany w obszarze **działania**.

    ![Stan przekazywania](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Jeśli przekazywanie zakończyło się i nie widzisz dysku w prawym okienku, wybierz pozycję **Odśwież**.

## <a name="download-a-managed-disk"></a>Pobierz dysk zarządzany

Poniższe kroki wyjaśniają, jak pobrać dysk zarządzany do dysku VHD Premium. Aby można było pobrać stan dysku, należy go **odłączyć** . nie można pobrać **dołączonego** dysku.

1. W okienku po lewej stronie, jeśli nie jest jeszcze rozwinięte, rozwiń pozycję **dyski** i wybierz grupę zasobów, z której chcesz pobrać dysk.

    ![Wybierz grupę zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. W prawym okienku wybierz dysk, który chcesz pobrać.
1. Wybierz pozycję **Pobierz** , a następnie wybierz lokalizację, w której chcesz zapisać dysk.

    ![Pobierz dysk zarządzany](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Wybierz pozycję **Zapisz** , a dysk rozpocznie pobieranie. Stan pobierania będzie wyświetlany w obszarze **działania**.

    ![Stan pobierania](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Za pomocą Eksplorator usługi Storage można skopiować dysk z zarządzaniem do wewnątrz lub między regionami. Aby skopiować dysk:

1. Z listy rozwijanej **dyski** po lewej stronie wybierz grupę zasobów zawierającą dysk, który chcesz skopiować.

    ![Wybierz grupę zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. W okienku po prawej stronie wybierz dysk, który chcesz skopiować, a następnie wybierz pozycję **Kopiuj**.

    ![Kopiowanie dysku zarządzanego](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. W lewym okienku wybierz grupę zasobów, w której chcesz wkleić dysk.

    ![Wybierz grupę zasobów 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. W okienku po prawej stronie wybierz pozycję **Wklej** .

    ![Wklej dysk zarządzany](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. W oknie dialogowym **Wklej dysk** Wypełnij wartości. Można również określić strefę dostępności w obszarze Obsługiwane regiony.

    ![Okno dialogowe wklejania dysku](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Wybierz pozycję **Wklej** , a dysk rozpocznie kopiowanie, a stan zostanie wyświetlony w obszarze **działania**.

    ![Kopiuj stan wklejania](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Tworzenie migawki

1. Z listy rozwijanej **dyski** po lewej stronie wybierz grupę zasobów zawierającą dysk, który ma być migawką.

    ![Wybierz grupę zasobów 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Po prawej stronie wybierz dysk, który chcesz utworzyć migawek, a następnie wybierz pozycję **Utwórz migawkę**.

    ![Tworzenie migawki](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. W obszarze **Utwórz migawkę** Określ nazwę migawki oraz grupę zasobów, w której chcesz ją utworzyć. Następnie wybierz pozycję **Utwórz**.

    ![Okno dialogowe Tworzenie migawki](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Po utworzeniu migawki możesz wybrać polecenie **Otwórz w portalu** w obszarze **działania** , aby wyświetlić migawkę w Azure Portal.

    ![Otwórz migawkę w portalu](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Następne kroki


Dowiedz się [, jak utworzyć maszynę wirtualną na podstawie dysku VHD przy użyciu Azure Portal](windows/create-vm-specialized-portal.md).

Dowiedz się [, jak dołączyć zarządzany dysk danych do maszyny wirtualnej z systemem Windows przy użyciu Azure Portal](windows/attach-managed-disk-portal.md).