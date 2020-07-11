---
title: Azure Portal — Włącz klucze zarządzane przez klienta przy użyciu dysków zarządzanych przez funkcję SSE
description: Włącz klucze zarządzane przez klienta na dyskach zarządzanych za pomocą Azure Portal.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86236143"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Włącz klucze zarządzane przez klienta za pomocą dysków zarządzanych przy użyciu szyfrowania po stronie serwera — Portal

Azure Disk Storage pozwala zarządzać własnymi kluczami podczas korzystania z szyfrowania po stronie serwera (SSE) dla dysków zarządzanych, jeśli wybierzesz opcję. Aby uzyskać informacje koncepcyjne na temat instrukcji SSE z kluczami zarządzanymi przez klienta, a także innych typów szyfrowania dysków zarządzanych, zobacz sekcję [klucze zarządzane przez klienta](disk-encryption.md#customer-managed-keys) w artykule dotyczącym szyfrowania dysku.

## <a name="restrictions"></a>Ograniczenia

Na razie klucze zarządzane przez klienta mają następujące ograniczenia:

- Jeśli ta funkcja jest włączona dla danego dysku, nie można jej wyłączyć.
    Jeśli zachodzi potrzeba obejścia tego problemu, należy [skopiować wszystkie dane](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) na całkowicie inny dysk zarządzany, który nie korzysta z kluczy zarządzanych przez klienta.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Następne kroki

- [Poznaj szablony Azure Resource Manager tworzenia szyfrowanych dysków przy użyciu kluczy zarządzanych przez klienta](https://github.com/ramankumarlive/manageddiskscmkpreview))
- [Replikowanie maszyn z włączonymi kluczami zarządzanymi przez klienta](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Konfigurowanie odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu programu PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
