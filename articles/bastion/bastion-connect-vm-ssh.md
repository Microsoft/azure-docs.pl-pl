---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu usługi Azure bastionu
description: W tym artykule dowiesz się, jak nawiązać połączenie z maszyną wirtualną z systemem Linux przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: a5cda5d4d447bc04f853ea4a9abd15be75e7e177
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588680"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Nawiązywanie połączenia przy użyciu protokołu SSH z maszyną wirtualną z systemem Linux przy użyciu usługi Azure bastionu

W tym artykule pokazano, jak bezpiecznie i bezproblemowo SSH z maszynami wirtualnymi z systemem Linux w sieci wirtualnej platformy Azure. Możesz połączyć się z maszyną wirtualną bezpośrednio z Azure Portal. W przypadku korzystania z usługi Azure bastionu maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [Omówienie](bastion-overview.md).

Za pomocą usługi Azure bastionu można nawiązać połączenie z maszyną wirtualną z systemem Linux przy użyciu protokołu SSH. Do uwierzytelniania można użyć zarówno nazwy użytkownika, jak i klucza SSH. Można nawiązać połączenie z maszyną wirtualną za pomocą kluczy SSH przy użyciu jednej z:

* Klucz prywatny ręcznie wprowadzony przez użytkownika
* Plik zawierający informacje o kluczu prywatnym

Klucz prywatny SSH musi mieć format zaczynający się od  `"-----BEGIN RSA PRIVATE KEY-----"` i kończąc na `"-----END RSA PRIVATE KEY-----"` .

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że skonfigurowano hosta usługi Azure bastionu dla sieci wirtualnej, w której znajduje się maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [Tworzenie hosta usługi Azure bastionu](./tutorial-create-host-portal.md). Gdy usługa bastionu zostanie zainicjowana i wdrożona w sieci wirtualnej, możesz użyć jej do łączenia się z dowolną MASZYNą wirtualną w tej sieci wirtualnej. 

W przypadku korzystania z programu bastionu do nawiązywania połączenia zakłada się, że używasz protokołu RDP, aby nawiązać połączenie z maszyną wirtualną z systemem Windows, i SSH do łączenia się z maszynami wirtualnymi z systemem Linux Aby uzyskać informacje na temat nawiązywania połączenia z maszyną wirtualną z systemem Windows, zobacz [nawiązywanie połączenia z maszyną wirtualną — Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Wymagane role

Aby można było nawiązać połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika na zasobie usługi Azure Bastion

### <a name="ports"></a>Porty

Aby można było nawiązać połączenie z maszyną wirtualną z systemem Linux za pośrednictwem protokołu SSH, na maszynie wirtualnej muszą być otwarte następujące porty:

* Port wejściowy: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Połącz: przy użyciu nazwy użytkownika i hasła

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Zrzut ekranu przedstawia przegląd maszyny wirtualnej w Azure Portal z wybranym połączeniem":::
1. Po wybraniu opcji bastionu kliknij pozycję **Użyj bastionu**. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](./quickstart-host-portal.md).
1. Na stronie **nawiązywanie połączenia przy użyciu usługi Azure bastionu** wprowadź **nazwę użytkownika** i **hasło**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="Uwierzytelnianie hasłem":::
1. Wybierz pozycję **Połącz** , aby nawiązać połączenie z maszyną wirtualną.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connect: ręczne wprowadzanie klucza prywatnego

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Zrzut ekranu przedstawia przegląd maszyny wirtualnej w Azure Portal z wybranym połączeniem":::
1. Po wybraniu opcji bastionu kliknij pozycję **Użyj bastionu**. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](./quickstart-host-portal.md).
1. Na stronie **nawiązywanie połączenia przy użyciu usługi Azure bastionu** wprowadź **nazwę użytkownika** i **klucz prywatny SSH**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="Uwierzytelnianie klucza prywatnego SSH":::
1. Wprowadź klucz prywatny do **prywatnego klucza SSH** obszaru tekstowego (lub wklej go bezpośrednio).
1. Wybierz pozycję **Połącz** , aby nawiązać połączenie z maszyną wirtualną.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Łączenie: korzystanie z pliku klucza prywatnego

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Zrzut ekranu przedstawia przegląd maszyny wirtualnej w Azure Portal z wybranym połączeniem":::
1. Po wybraniu opcji bastionu kliknij pozycję **Użyj bastionu**. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](./quickstart-host-portal.md).
1. Na stronie **nawiązywanie połączenia przy użyciu usługi Azure bastionu** wprowadź **nazwę użytkownika** i **klucz prywatny SSH z pliku lokalnego**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="Plik prywatnego klucza SSH":::

1. Wyszukaj plik, a następnie wybierz pozycję **Otwórz**.
1. Wybierz pozycję **Połącz** , aby nawiązać połączenie z maszyną wirtualną. Po kliknięciu przycisku Połącz protokół SSH do tej maszyny wirtualnej zostanie bezpośrednio otwarty w Azure Portal. To połączenie jest ponad HTML5 przy użyciu portu 443 w usłudze bastionu przez prywatny adres IP maszyny wirtualnej.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Connect: używanie klucza prywatnego przechowywanego w Azure Key Vault

>[!NOTE]
>Aktualizacja portalu dla tej funkcji jest obecnie wprowadzana do regionów.
>

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Zrzut ekranu przedstawia przegląd maszyny wirtualnej w Azure Portal z wybranym połączeniem":::
1. Po wybraniu opcji bastionu kliknij pozycję **Użyj bastionu**. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](./quickstart-host-portal.md).
1. Na stronie **nawiązywanie połączenia przy użyciu usługi Azure bastionu** wprowadź **nazwę użytkownika** i wybierz opcję **klucz prywatny SSH z Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Klucz prywatny SSH z Azure Key Vault":::
1. Wybierz listę rozwijaną **Azure Key Vault** i wybierz zasób, w którym zapisano klucz prywatny SSH. Jeśli nie skonfigurowano zasobu Azure Key Vault, zobacz [Tworzenie magazynu kluczy](../key-vault/general/quick-create-portal.md) i przechowywanie klucza prywatnego SSH jako wartości nowego Key Vault klucza tajnego.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Usługa Azure Key Vault":::

   Upewnij się, że masz **listę** i **Uzyskaj** dostęp do wpisów tajnych przechowywanych w zasobie Key Vault. Aby przypisać i zmodyfikować zasady dostępu dla zasobu Key Vault, zobacz [przypisywanie zasad dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md).
1. Wybierz listę rozwijaną **Azure Key Vault wpis tajny** i wybierz klucz tajny Key Vault zawierający wartość klucza prywatnego SSH.
1. Wybierz pozycję **Połącz** , aby nawiązać połączenie z maszyną wirtualną. Po kliknięciu przycisku **Połącz** protokół SSH do tej maszyny wirtualnej zostanie bezpośrednio otwarty w Azure Portal. To połączenie jest ponad HTML5 przy użyciu portu 443 w usłudze bastionu przez prywatny adres IP maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o usłudze Azure bastionu, zobacz [często zadawane pytania](bastion-faq.md)dotyczące usługi bastionu. 
