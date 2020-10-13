---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu
description: W tym artykule dowiesz się, jak nawiązać połączenie z maszyną wirtualną platformy Azure z systemem Windows przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8b9653daf945b6a189bc528cd00de832ae97c03b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978141"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu

Korzystając z usługi Azure bastionu, możesz bezpiecznie i bezproblemowo łączyć się z maszynami wirtualnymi za pośrednictwem protokołu SSL bezpośrednio w Azure Portal. Gdy korzystasz z usługi Azure bastionu, maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. W tym artykule opisano sposób nawiązywania połączenia z maszynami wirtualnymi z systemem Windows. Aby uzyskać informacje o łączeniu się z maszyną wirtualną z systemem Linux, zobacz [nawiązywanie połączenia z maszyną wirtualną przy użyciu usługi Azure bastionu-Linux](bastion-connect-vm-ssh.md).

Usługa Azure bastionu zapewnia bezpieczną łączność ze wszystkimi maszynami wirtualnymi w sieci wirtualnej, w której została zainicjowana obsługa administracyjna. Korzystanie z usługi Azure bastionu chroni maszyny wirtualne przed udostępnieniem portów protokołu RDP/SSH na świecie zewnętrznym, zapewniając bezpieczny dostęp przy użyciu protokołu RDP/SSH. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](bastion-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

### <a name="install-the-bastion-host"></a>Instalowanie hosta bastionu

Upewnij się, że skonfigurowano hosta usługi Azure bastionu dla sieci wirtualnej, w której znajduje się maszyna wirtualna. Gdy usługa bastionu zostanie zainicjowana i wdrożona w sieci wirtualnej, można jej używać do łączenia się z dowolną MASZYNą wirtualną w sieci wirtualnej. Aby skonfigurować hosta usługi Azure bastionu, zobacz [Tworzenie hosta usługi Azure bastionu](bastion-create-host-portal.md).

### <a name="required-roles"></a>Wymagane role

Aby nawiązać połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika w zasobie Azure bastionu

### <a name="ports"></a>Porty

Aby nawiązać połączenie z maszyną wirtualną z systemem Windows, musisz otworzyć następujące porty na maszynie wirtualnej z systemem Windows:

* Porty przychodzące: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Połącz

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).
