---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu witryny Azure Portal
description: W tym przewodniku Szybki start dowiesz się, jak tworzyć i szyfrować maszynę wirtualną systemu Windows za pomocą portalu Azure.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 3a5fb354ab32dd560a94875dd84d891ce0b9d68c
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081748"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Szybki start: tworzenie i szyfrowanie maszyny wirtualnej systemu Windows za pomocą portalu Azure

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Witryna Azure Portal to oparty na przeglądarce interfejs użytkownika umożliwiający tworzenie maszyn wirtualnych i powiązanych z nimi zasobów. W tym przewodniku Szybki start użyjesz portalu Azure do wdrożenia maszyny wirtualnej systemu Windows (VM) z uruchomionym Ubuntu 18.04 LTS, utworzenia magazynu kluczy do przechowywania kluczy szyfrowania i szyfrowania maszyny wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. Na stronie Nowy w obszarze Popularne wybierz pozycję **Centrum danych systemu Windows Server 2016**.
1. Na karcie Podstawy w obszarze Szczegóły projektu upewnij się, że jest zaznaczona poprawna subskrypcja, a następnie wybierz pozycję **Utwórz nową grupę zasobów**. Wprowadź *myResourceGroup* jako nazwę.
1. W przypadku **nazwy maszyny wirtualnej**wprowadź *myvm*.
1. W przypadku **regionu**wybierz ten sam region, który został użyty podczas tworzenia przechowalni kluczy powyżej (np. *wschodnie stany USA).*
1. Upewnij się, że **rozmiar** to *Standard D2s v3*.
1. W obszarze **Konto administratora**wybierz pozycję **Hasło**. Wprowadź nazwę użytkownika i hasło.
    ![Ekran tworzenia grupy zasobów](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Wybierz kartę "Zarządzanie" i sprawdź, czy masz konto przechowywania diagnostyki. Jeśli nie masz kont magazynu, wybierz opcję "Utwórz nowy", nadaj nowemu kontu nazwę i wybierz ekran tworzenia grupy zasobów "Ok" ![](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Kliknij "Recenzja + Utwórz".
1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

1. Po zakończeniu wdrażania maszyny Wirtualnej wybierz pozycję **Przejdź do zasobu**.
1. Na lewym pasku bocznym wybierz pozycję **Dyski**.
1. Na ekranie Dyski wybierz pozycję **Szyfrowanie**. 

    ![dyski i wybór szyfrowania](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na ekranie szyfrowania w obszarze **Dyski do szyfrowania**wybierz pozycję **OS i dyski danych**.
1. W obszarze **Ustawienia szyfrowania**wybierz pozycję **Wybierz przechowalnię kluczy i klucz do szyfrowania**.
1. Na ekranie **Wybierz klucz z usługi Azure Key Vault** wybierz pozycję **Utwórz nowy**.

    ![dyski i wybór szyfrowania](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Na ekranie **Tworzenie magazynu kluczy** upewnij się, że grupa zasobów jest taka sama, jak grupa użyta do utworzenia maszyny Wirtualnej.
1. Nadaj magazynowi kluczy nazwę.  Każdy magazyn kluczy na platformie Azure musi mieć unikatową nazwę.
1. Na karcie **Zasady dostępu** sprawdź pole **Szyfrowanie dysków platformy Azure pod kątem szyfrowania woluminów.**

    ![dyski i wybór szyfrowania](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Wybierz pozycję **Przegląd + utwórz**.  
1. Po przejściu weryfikacji magazynu kluczy wybierz pozycję **Utwórz**. Spowoduje to powrót do **wybierz klucz z ekranu usługi Azure Key Vault.**
1. Pozostaw pole **Klucz** puste i wybierz pozycję **Wybierz**.
1. U góry ekranu szyfrowania kliknij pozycję **Zapisz**. Wyskakujące okienko ostrzega, że maszyna wirtualna zostanie ponownie uruchomiona. Kliknij **przycisk Tak**.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję Usuń, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, który był włączony dla kluczy szyfrowania, utworzono maszynę wirtualną i włączono maszynę wirtualną do szyfrowania.  

> [!div class="nextstepaction"]
> [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)
