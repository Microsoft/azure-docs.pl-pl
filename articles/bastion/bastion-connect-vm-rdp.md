---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu
description: W tym artykule dowiesz się, jak nawiązać połączenie z maszyną wirtualną platformy Azure z systemem Windows przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521542"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu

Korzystając z usługi Azure bastionu, możesz bezpiecznie i bezproblemowo łączyć się z maszynami wirtualnymi za pośrednictwem protokołu SSL bezpośrednio w Azure Portal. Gdy korzystasz z usługi Azure bastionu, maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. W tym artykule opisano sposób nawiązywania połączenia z maszynami wirtualnymi z systemem Windows. Aby uzyskać informacje o łączeniu się z maszyną wirtualną z systemem Linux, zobacz [nawiązywanie połączenia z maszyną wirtualną z systemem Linux](bastion-connect-vm-ssh.md).

Usługa Azure bastionu zapewnia bezpieczną łączność ze wszystkimi maszynami wirtualnymi w sieci wirtualnej, w której została zainicjowana obsługa administracyjna. Korzystanie z usługi Azure bastionu chroni maszyny wirtualne przed udostępnieniem portów protokołu RDP/SSH na świecie zewnętrznym, zapewniając bezpieczny dostęp przy użyciu protokołu RDP/SSH. Aby uzyskać więcej informacji, zobacz artykuł [co to jest Azure bastionu?](bastion-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące kryteria:

* Sieć wirtualna z hostem bastionu jest już zainstalowana.

   Upewnij się, że skonfigurowano hosta usługi Azure bastionu dla sieci wirtualnej, w której znajduje się maszyna wirtualna. Gdy usługa bastionu zostanie zainicjowana i wdrożona w sieci wirtualnej, można jej używać do łączenia się z dowolną MASZYNą wirtualną w sieci wirtualnej. Aby skonfigurować hosta usługi Azure bastionu, zobacz [Tworzenie hosta bastionu](tutorial-create-host-portal.md#createhost).
* Maszyna wirtualna z systemem Windows w sieci wirtualnej.
* Następujące wymagane role:
  * Rola czytelnika na maszynie wirtualnej.
  * Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej.
  * Rola czytelnika w zasobie Azure bastionu.
* Porty: Aby nawiązać połączenie z maszyną wirtualną z systemem Windows, należy otworzyć następujące porty na maszynie wirtualnej z systemem Windows:
  * Porty przychodzące: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Connect

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).