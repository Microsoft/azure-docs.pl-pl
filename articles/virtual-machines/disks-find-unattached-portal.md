---
title: Zidentyfikuj niedołączone dyski platformy Azure — Azure Portal
description: Jak znaleźć niedołączone dyski wirtualne i niezarządzane (VHD/stronicowe obiekty blob) na platformie Azure za pomocą Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d31ce4b6086c44de913afd1083bae25fa3d44cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898159"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych platformy Azure — Azure Portal

Po usunięciu maszyny wirtualnej na platformie Azure domyślnie wszystkie dyski dołączone do maszyny wirtualnej nie zostaną usunięte. Pozwala to zapobiec utracie danych z powodu niezamierzonego usunięcia maszyn wirtualnych. Po usunięciu maszyny wirtualnej opłaty za dyski niedołączone będą kontynuowane. W tym artykule opisano sposób znajdowania i usuwania dowolnych niedołączonych dysków przy użyciu Azure Portal i zmniejszania niepotrzebnych kosztów. Usunięcia są trwałe, po usunięciu dysku nie będzie można odzyskać danych.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: Znajdź i Usuń niedołączone dyski

Jeśli masz niedołączone dyski zarządzane i nie są już potrzebne dane, w poniższym procesie wyjaśniono, jak można je znaleźć z Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wyszukaj i wybierz pozycję **dyski**.

    W bloku **dyski** zostanie wyświetlona lista wszystkich dysków. Dowolny dysk o postaci " **-** " w kolumnie **właściciel** jest niedołączonym dyskiem.

    [![Zrzut ekranu przedstawiający blok dyski zarządzane, jeśli dysk znajduje się w kolumnie Właściciel, jest niedołączonym dyskiem.](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Wybierz niedołączony dysk, który chcesz usunąć. spowoduje to otwarcie bloku dysku.
1. W bloku dysku możesz potwierdzić, że stan dysku jest niedołączony, a następnie wybierz pozycję **Usuń**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Zrzut ekranu przedstawiający blok poszczególnych dysków zarządzanych. Ten blok będzie widoczny w stanie dysku, jeśli jest niedołączony. Możesz usunąć ten dysk, jeśli nie musisz już zachować swoich danych.":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dyski niezarządzane: Znajdź i Usuń niedołączone dyski

Dyski niezarządzane to pliki VHD, które są przechowywane jako [stronicowe obiekty blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) na [kontach usługi Azure Storage](../storage/common/storage-account-overview.md).

Jeśli masz niezarządzane dyski, które nie są dołączone do maszyny wirtualnej, nie potrzebujesz już danych na nich i chcesz je usunąć, następujący proces wyjaśnia, jak to zrobić z Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wyszukaj i wybierz pozycję **dyski (klasyczne)**.

    Zostanie wyświetlona lista wszystkich dysków niezarządzanych. Dowolny dysk, który ma " **-** " w kolumnie **dołączone do** , jest niedołączonym dyskiem.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Zrzut ekranu przedstawiający blok dysków niezarządzanych. Dyski w tym bloku, które zostały dołączone do kolumny, są niedołączone.":::

1. Wybierz niedołączony dysk, który chcesz usunąć, spowoduje to wyświetlenie bloku dysku.

1. W bloku dysku można potwierdzić, że jest on niedołączony, ponieważ jest **dołączony do** nadal **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Zrzut ekranu przedstawiający blok pojedynczego dysku niezarządzanego. Jeśli jest on niedołączony, będzie miał jako wartość dołączona do. Jeśli dane dysków nie są już potrzebne, możesz je usunąć.":::

1. Wybierz pozycję **Usuń**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Zrzut ekranu przedstawiający blok pojedynczego dysku niezarządzanego, podświetl pozycję Usuń.":::

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz automatycznie znaleźć i usunąć niedołączone konta magazynu, zobacz nasze artykuły [interfejsu wiersza polecenia](linux/find-unattached-disks.md) lub [programu PowerShell](windows/find-unattached-disks.md) .

Aby uzyskać więcej informacji, zobacz [usuwanie konta magazynu](../storage/common/storage-account-create.md#delete-a-storage-account) i [Identyfikowanie dysków oddzielonych przy użyciu programu PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell) .
