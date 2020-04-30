---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu witryny Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak za pomocą Azure Portal utworzyć i zaszyfrować maszynę wirtualną z systemem Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 3a5fb354ab32dd560a94875dd84d891ce0b9d68c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82081748"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Szybki Start: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Witryna Azure Portal to oparty na przeglądarce interfejs użytkownika umożliwiający tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. W tym przewodniku szybki start użyjesz Azure Portal, aby wdrożyć maszynę wirtualną z systemem Windows w systemie Ubuntu 18,04 LTS, utworzyć magazyn kluczy na potrzeby przechowywania kluczy szyfrowania i zaszyfrować maszynę wirtualną.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. Na stronie nowy w obszarze popularne wybierz pozycję **Windows Server 2016 Datacenter**.
1. Na karcie podstawy w obszarze Szczegóły projektu upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową grupę zasobów**. Wprowadź nazwę *zasobu* .
1. W obszarze **Nazwa maszyny wirtualnej**wprowadź *MyVM*.
1. W polu **region**wybierz ten sam region, który został użyty podczas tworzenia magazynu kluczy (np. *Wschodnie stany USA*).
1. Upewnij się, że **rozmiar** jest *Standardowy D2s v3*.
1. W obszarze **konto administratora**wybierz pozycję **hasło**. Wprowadź nazwę użytkownika i hasło.
    ![Ekran tworzenia z przetworzeniem zasobów](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Wybierz kartę Zarządzanie i sprawdź, czy masz konto magazynu diagnostyki. Jeśli nie masz żadnych kont magazynu, wybierz pozycję "Utwórz nowe", Nadaj nowemu kontu nazwę i wybierz pozycję "OK" ![na ekranie tworzenia zasobów](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Kliknij pozycję "Przejrzyj + Utwórz".
1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

1. Po zakończeniu wdrożenia maszyny wirtualnej wybierz pozycję **Przejdź do zasobu**.
1. Na pasku bocznym po lewej stronie wybierz pozycję **dyski**.
1. Na ekranie dyski wybierz pozycję **szyfrowanie**. 

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na ekranie szyfrowanie w obszarze **dyski do zaszyfrowania**wybierz opcję **dyski systemu operacyjnego i danych**.
1. W obszarze **Ustawienia szyfrowania**wybierz **pozycję Wybierz magazyn kluczy i klucz do szyfrowania**.
1. Na ekranie **Wybieranie klucza z Azure Key Vault** wybierz pozycję **Utwórz nowy**.

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Na ekranie **Tworzenie magazynu kluczy** upewnij się, że grupa zasobów jest taka sama jak ta, która została użyta do utworzenia maszyny wirtualnej.
1. Podaj nazwę magazynu kluczy.  Każdy Magazyn kluczy na platformie Azure musi mieć unikatową nazwę.
1. Na karcie **zasady dostępu** sprawdź pole **Azure Disk Encryption do szyfrowania woluminów** .

    ![wybór dysków i szyfrowania](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Wybierz pozycję **Przegląd + utwórz**.  
1. Po przekazaniu walidacji magazynu kluczy wybierz pozycję **Utwórz**. Spowoduje to powrót do ekranu **wyboru z Azure Key Vault** .
1. Pozostaw pole **klucza** puste i wybierz **pozycję Wybierz**.
1. W górnej części ekranu szyfrowania kliknij przycisk **Zapisz**. W oknie podręcznym zostanie wyświetlone ostrzeżenie o ponownym uruchomieniu maszyny wirtualnej. Kliknij przycisk **tak**.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję Usuń, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault, które zostały włączone dla kluczy szyfrowania, utworzono maszynę wirtualną i włączono maszynę wirtualną w celu szyfrowania.  

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
