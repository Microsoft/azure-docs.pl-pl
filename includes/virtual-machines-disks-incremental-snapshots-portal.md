---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002526"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Dostępność regionalna
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal


1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do dysku, który ma być migawką.
1. Na dysku wybierz pozycję **Utwórz migawkę**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Zrzut ekranu. Blok dysku z programem * * + Tworzenie migawki * * wyróżniony, co to jest co należy wybrać.":::

1. Wybierz grupę zasobów, której chcesz użyć, a następnie wprowadź nazwę.
1. Wybierz pozycję **przyrostowe** i wybierz pozycję **Przegląd + Utwórz**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Zrzut ekranu. Utwórz blok migawki, wypełnij pola Nazwa i wybierz pozycję przyrostowe, a następnie utwórz migawkę.":::

1. Wybierz pozycję **Utwórz**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Zrzut ekranu. Strona walidacji dla migawki, Potwierdź wybrane opcje, a następnie utwórz migawkę.":::

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zobaczyć przykładowy kod pokazujący różnicowe możliwości migawek przyrostowych, korzystając z platformy .NET, zobacz [Kopiuj kopie zapasowe platformy Azure Managed disks do innego regionu z różnicową możliwością migawek przyrostowych](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
