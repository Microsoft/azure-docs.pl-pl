---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu usługi Azure bastionu
description: W tym artykule dowiesz się, jak nawiązać połączenie z maszyną wirtualną z systemem Linux przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 5d61c2a1a0f5d7b26809621af6dfa88cf5080320
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518185"
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
1. Po wybraniu bastionu pojawi się pasek boczny, który ma trzy karty — RDP, SSH i bastionu. Jeśli Zainicjowano obsługę bastionu dla sieci wirtualnej, karta bastionu jest domyślnie aktywna. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Zrzut ekranu przedstawia okno dialogowe łączenie z maszyną wirtualną z wybraną pozycją BASTIONU":::
1. Wprowadź nazwę użytkownika i hasło dla SSH do maszyny wirtualnej.
1. Po wprowadzeniu klucza wybierz przycisk **Połącz** .

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connect: ręczne wprowadzanie klucza prywatnego

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Zrzut ekranu przedstawia przegląd maszyny wirtualnej w Azure Portal z wybranym połączeniem":::
1. Po wybraniu bastionu pojawi się pasek boczny, który ma trzy karty — RDP, SSH i bastionu. Jeśli Zainicjowano obsługę bastionu dla sieci wirtualnej, karta bastionu jest domyślnie aktywna. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Okno dialogowe łączenie z maszyną wirtualną z wybranym BASTIONU.":::
1. Wprowadź nazwę użytkownika i wybierz pozycję **prywatny klucz SSH**.
1. Wprowadź klucz prywatny do **prywatnego klucza SSH** obszaru tekstowego (lub wklej go bezpośrednio).
1. Po wprowadzeniu klucza wybierz przycisk **Połącz** .

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Łączenie: korzystanie z pliku klucza prywatnego

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Połącz wybrane":::
1. Po wybraniu bastionu pojawi się pasek boczny, który ma trzy karty — RDP, SSH i bastionu. Jeśli Zainicjowano obsługę bastionu dla sieci wirtualnej, karta bastionu jest domyślnie aktywna. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="BASTIONU zaznaczone.":::
1. Wprowadź nazwę użytkownika i wybierz opcję **klucz prywatny SSH z pliku lokalnego**.
1. Wybierz przycisk **przeglądania** (ikona folderu w pliku lokalnym).
1. Wyszukaj plik, a następnie wybierz pozycję **Otwórz**.
1. Wybierz pozycję **Połącz** , aby nawiązać połączenie z maszyną wirtualną. Po kliknięciu przycisku Połącz protokół SSH do tej maszyny wirtualnej zostanie bezpośrednio otwarty w Azure Portal. To połączenie jest ponad HTML5 przy użyciu portu 443 w usłudze bastionu przez prywatny adres IP maszyny wirtualnej.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Connect: używanie klucza prywatnego przechowywanego w Azure Key Vault

>[!NOTE]
>Aktualizacja portalu dla tej funkcji jest obecnie wprowadzana do regionów.
>

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie kliknij pozycję **Połącz** , a następnie wybierz pozycję **bastionu** z listy rozwijanej.
1. Po wybraniu bastionu pojawi się pasek boczny, który ma trzy karty — RDP, SSH i bastionu. Jeśli Zainicjowano obsługę bastionu dla sieci wirtualnej, karta bastionu jest domyślnie aktywna. Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, zobacz [Konfigurowanie bastionu](bastion-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Karta bastionu":::
1. Wprowadź nazwę użytkownika i wybierz opcję **klucz prywatny SSH z Azure Key Vault**.
1. Wybierz listę rozwijaną **Azure Key Vault** i wybierz zasób, w którym zapisano klucz prywatny SSH. Jeśli nie skonfigurowano zasobu Azure Key Vault, zobacz [Tworzenie magazynu kluczy](../key-vault/general/quick-create-portal.md) i przechowywanie klucza prywatnego SSH jako wartości nowego Key Vault klucza tajnego.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Usługa Azure Key Vault":::

Upewnij się, że masz **listę** i **Uzyskaj** dostęp do wpisów tajnych przechowywanych w zasobie Key Vault. Aby przypisać i zmodyfikować zasady dostępu dla zasobu Key Vault, zobacz [przypisywanie zasad dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md).

1. Wybierz listę rozwijaną **Azure Key Vault wpis tajny** i wybierz klucz tajny Key Vault zawierający wartość klucza prywatnego SSH.
1. Wybierz pozycję **Połącz** , aby nawiązać połączenie z maszyną wirtualną. Po kliknięciu przycisku Połącz protokół SSH do tej maszyny wirtualnej zostanie bezpośrednio otwarty w Azure Portal. To połączenie jest ponad HTML5 przy użyciu portu 443 w usłudze bastionu przez prywatny adres IP maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Przeczytaj [często zadawane pytania dotyczące bastionu](bastion-faq.md)
