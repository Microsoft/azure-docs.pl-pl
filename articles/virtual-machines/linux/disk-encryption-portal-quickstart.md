---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu witryny Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak za pomocą Azure Portal utworzyć i zaszyfrować maszynę wirtualną z systemem Linux.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 188ba72f4da4e5a24554f895473f1c74e48d50fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558359"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Szybki Start: Tworzenie i szyfrowanie maszyny wirtualnej przy użyciu Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Witryna Azure Portal to oparty na przeglądarce interfejs użytkownika umożliwiający tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. W tym przewodniku szybki start użyjesz Azure Portal, aby wdrożyć maszynę wirtualną z systemem Linux, na której działa Ubuntu 18,04 LTS, utworzyć magazyn kluczy na potrzeby przechowywania kluczy szyfrowania i zaszyfrować maszynę wirtualną.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. Na stronie nowy w obszarze popularne wybierz pozycję **Ubuntu Server 18,04 LTS**.
1. Na karcie podstawy w obszarze Szczegóły projektu Sprawdź, czy wybrano poprawną subskrypcję.
1. W obszarze "Grupa zasobów" Wybierz pozycję **Utwórz nową**. Wprowadź nazwę *zasobu* , a następnie wybierz **przycisk OK**.
1. W obszarze **Nazwa maszyny wirtualnej** wprowadź *MyVM*.
1. W **obszarze region** wybierz pozycję *(US) Wschodnie stany USA*.
1. Upewnij się, że **rozmiar** jest *Standardowy D2s v3*.
1. W obszarze **konto administratora** wybierz opcję *hasło* jako **Typ uwierzytelniania**. Wprowadź nazwę użytkownika i hasło.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Ekran tworzenia maszyny wirtualnej z systemem Linux":::

    > [!WARNING]
    > Karta "dyski" zawiera pole "typ szyfrowania" w obszarze **Opcje dysków**. To pole służy do określania opcji szyfrowania dla [Managed disks](../managed-disks-overview.md) i CMK, a **nie** Azure Disk Encryption.
    >
    > Aby uniknąć nieporozumień, zalecamy całkowite pomijanie karty *dysków* podczas wykonywania tego samouczka.

1. Wybierz kartę Zarządzanie i sprawdź, czy masz konto magazynu diagnostyki. Jeśli nie masz żadnych kont magazynu, wybierz pozycję *Utwórz nową*, nazwij konto magazynu *mojekontomagazynu* i wybierz pozycję "OK".

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Ekran tworzenia z przetworzeniem zasobów":::

1. Kliknij pozycję "Przejrzyj + Utwórz".
1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

1. Po zakończeniu wdrożenia maszyny wirtualnej wybierz pozycję **Przejdź do zasobu**.
1. Na pasku bocznym po lewej stronie wybierz pozycję **dyski**.
1. Na górnym pasku wybierz pozycję **dodatkowe ustawienia** .
1. W obszarze **Ustawienia szyfrowania**  >  **dyski do zaszyfrowania** wybierz opcję **dyski systemu operacyjnego i danych**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Zrzut ekranu, który wyróżnia dyski systemu operacyjnego i danych.":::

1. W obszarze **Ustawienia szyfrowania** wybierz **pozycję Wybierz magazyn kluczy i klucz do szyfrowania**.
1. Na ekranie **Wybieranie klucza z Azure Key Vault** wybierz pozycję **Utwórz nowy**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Zrzut ekranu, który podświetla Utwórz nowy.":::

1. Z lewej strony **magazynu kluczy i klucza** wybierz **pozycję kliknij, aby wybrać klucz**.
1. Na stronie **Wybierz klucz z Azure Key Vault** w polu **Key Vault** wybierz pozycję **Utwórz nowy**.
1. Na ekranie **Tworzenie magazynu kluczy** upewnij się, że grupa zasobów jest grupą *zasobów, i* nadaj jej nazwę.  Każdy Magazyn kluczy na platformie Azure musi mieć unikatową nazwę.
1. Na karcie **zasady dostępu** sprawdź pole **Azure Disk Encryption do szyfrowania woluminów** .

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="wybór dysków i szyfrowania":::

1. Wybierz pozycję **Przejrzyj i utwórz**.  
1. Po przekazaniu walidacji magazynu kluczy wybierz pozycję **Utwórz**. Spowoduje to powrót do ekranu **wyboru z Azure Key Vault** .
1. Pozostaw pole **klucza** puste i wybierz **pozycję Wybierz**.
1. W górnej części ekranu szyfrowania kliknij przycisk **Zapisz**. W oknie podręcznym zostanie wyświetlone ostrzeżenie o ponownym uruchomieniu maszyny wirtualnej. Kliknij przycisk **Yes** (Tak).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję Usuń, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault, dla których włączono obsługę kluczy szyfrowania, utworzono maszynę wirtualną i włączono maszynę wirtualną do szyfrowania.  

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)