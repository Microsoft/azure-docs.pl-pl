---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu
description: W tym artykule dowiesz się, jak nawiązać połączenie z maszyną wirtualną platformy Azure z systemem Windows przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744327"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu usługi Azure bastionu

Korzystając z usługi Azure bastionu, możesz bezpiecznie i bezproblemowo łączyć się z maszynami wirtualnymi za pośrednictwem protokołu SSL bezpośrednio w Azure Portal. Gdy korzystasz z usługi Azure bastionu, maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. W tym artykule opisano sposób nawiązywania połączenia z maszynami wirtualnymi z systemem Windows. Aby uzyskać informacje o łączeniu się z maszyną wirtualną z systemem Linux, zobacz [nawiązywanie połączenia z maszyną wirtualną przy użyciu usługi Azure bastionu-Linux](bastion-connect-vm-ssh.md).

Usługa Azure bastionu zapewnia bezpieczną łączność ze wszystkimi maszynami wirtualnymi w sieci wirtualnej, w której została zainicjowana obsługa administracyjna. Korzystanie z usługi Azure bastionu chroni maszyny wirtualne przed udostępnieniem portów protokołu RDP/SSH na świecie zewnętrznym, zapewniając bezpieczny dostęp przy użyciu protokołu RDP/SSH. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](bastion-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że skonfigurowano hosta usługi Azure bastionu dla sieci wirtualnej, w której znajduje się maszyna wirtualna. Gdy usługa bastionu zostanie zainicjowana i wdrożona w sieci wirtualnej, można jej używać do łączenia się z dowolną MASZYNą wirtualną w sieci wirtualnej. Aby skonfigurować hosta usługi Azure bastionu, zobacz [Tworzenie hosta usługi Azure bastionu](bastion-create-host-portal.md).

### <a name="required-roles"></a>Wymagane role

Aby nawiązać połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika w zasobie Azure bastionu

### <a name="ports"></a>Porty

Aby nawiązać połączenie z maszyną wirtualną z systemem Windows, musisz otworzyć następujące porty na maszynie wirtualnej z systemem Windows:

* Porty przychodzące: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Łączone

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.

   ![Połączenie maszyny wirtualnej](./media/bastion-connect-vm-rdp/connect.png)
1. Po kliknięciu przycisku bastionu pojawi się pasek boczny z trzema kartami — RDP, SSH i bastionu. Jeśli Zainicjowano obsługę bastionu dla sieci wirtualnej, karta bastionu jest domyślnie aktywna. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, możesz kliknąć link, aby skonfigurować bastionu. Instrukcje dotyczące konfiguracji znajdują się w temacie [Configure bastionu](bastion-create-host-portal.md).

   ![Karta bastionu](./media/bastion-connect-vm-rdp/bastion.png)
1. Na karcie bastionu wprowadź nazwę użytkownika i hasło dla maszyny wirtualnej, a następnie kliknij przycisk **Połącz**. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w Azure Portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

   ![Połączenie RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Następne kroki

Przeczytaj [często zadawane pytania dotyczące bastionu](bastion-faq.md)
