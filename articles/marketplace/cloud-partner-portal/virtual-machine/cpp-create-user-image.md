---
title: Tworzenie obrazu maszyny wirtualnej użytkownika dla portalu Azure Marketplace
description: Zawiera listę czynności i odwołań wymaganych do utworzenia obrazu maszyny wirtualnej użytkownika.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9d82d50769925480d461c122096c3919d7e8940d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146572"
---
# <a name="create-a-user-vm-image"></a>Tworzenie obrazu maszyny wirtualnej użytkownika

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie zasobów technicznych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) , aby zarządzać migrowanymi ofertami.

W tym artykule opisano dwa ogólne kroki wymagane do utworzenia obrazu niezarządzanego na podstawie uogólnionego wirtualnego dysku twardego.  Podano informacje, które przeprowadzą Cię przez poszczególne kroki: Przechwytywanie obrazu i uogólnianie obrazu.


## <a name="capture-the-vm-image"></a>Przechwytywanie obrazu maszyny wirtualnej

Instrukcje zawarte w następującym artykule dotyczą przechwytywania maszyny wirtualnej, która odnosi się do podejścia dostępu:

-  PowerShell: [Tworzenie niezarządzanego obrazu maszyny wirtualnej na podstawie maszyny wirtualnej platformy Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interfejs wiersza polecenia platformy Azure: [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../../virtual-machines/linux/capture-image.md)
-  Interfejs API: [przechwytywanie Virtual Machines](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Uogólnianie obrazu maszyny wirtualnej

Ponieważ obraz użytkownika został wygenerowany z wcześniej uogólnionego wirtualnego dysku twardego, powinien również być uogólniony.  Ponownie wybierz następujący artykuł, który odnosi się do mechanizmu dostępu.  (Być może dysk został już uogólniony podczas przechwycenia).

-  PowerShell: [uogólnianie maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfejs wiersza polecenia platformy Azure: [krok 2. Tworzenie obrazu maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  Interfejs API: [Virtual Machines-generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Następne kroki

Następnie utworzysz [certyfikat](cpp-create-key-vault-cert.md) i zapiszesz go w nowym Azure Key Vault.  Ten certyfikat jest wymagany do nawiązania bezpiecznego połączenia usługi WinRM z maszyną wirtualną.
