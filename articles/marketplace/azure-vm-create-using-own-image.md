---
title: Tworzenie oferty maszyny wirtualnej platformy Azure na maszynie Azure Marketplace użyciu własnego obrazu
description: Dowiedz się, jak opublikować ofertę maszyny wirtualnej Azure Marketplace użyciu własnego obrazu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 47fe7b42b68ae42f74a74e5fc69c8d1041d3bf8d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727128"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Jak utworzyć maszynę wirtualną przy użyciu własnego obrazu

W tym artykule opisano sposób tworzenia i wdrażania obrazu maszyny wirtualnej dostarczonej przez użytkownika.

> [!NOTE]
> Przed rozpoczęciem tej procedury [](marketplace-virtual-machines.md#technical-requirements) przejrzyj wymagania techniczne dotyczące ofert maszyn wirtualnych platformy Azure, w tym wymagania dotyczące wirtualnego dysku twardego (VHD).

Aby zamiast tego użyć zatwierdzonego obrazu podstawowego, postępuj zgodnie z instrukcjami w te tematu Create a VM image from an approved base (Tworzenie obrazu maszyny [wirtualnej na podstawie zatwierdzonego obrazu podstawowego).](azure-vm-create-using-approved-base.md)

## <a name="configure-the-vm"></a>Konfigurowanie maszyny wirtualnej

W tej sekcji opisano, jak rozmiarować, aktualizować i uogólniać maszynę wirtualną platformy Azure. Te kroki są niezbędne do przygotowania maszyny wirtualnej do wdrożenia na Azure Marketplace.

### <a name="size-the-vhds"></a>Rozmiar dysków VHD

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Instalowanie najbardziej bieżących aktualizacji

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Przeprowadzanie większej liczby kontroli zabezpieczeń

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie niestandardowej konfiguracji i zaplanowanych zadań

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Uogólnij obraz

Wszystkie obrazy w Azure Marketplace muszą być wielokrotnego użytku w sposób ogólny. Aby to osiągnąć, należy uogólnić wirtualny dysk twardy systemu operacyjnego, czyli operację, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

## <a name="bring-your-image-into-azure"></a>Korzystanie z obrazu na platformie Azure

> [!NOTE]
> Subskrypcja platformy Azure zawierająca usługę SIG musi znajdować się w tej samej dzierżawie co konto wydawcy, aby można było ją opublikować. Ponadto konto wydawcy musi mieć co najmniej dostęp współautora do subskrypcji zawierającej usługę SIG.

Istnieją trzy sposoby wykorzystania obrazu na platformie Azure:

1. Przekaż wirtualny dysk twardy do Shared Image Gallery (SIG).
1. Przekaż wirtualny dysk twardy na konto usługi Azure Storage.
1. Wyodrębnij wirtualny dysk twardy z obrazu zarządzanego (w przypadku korzystania z usług tworzenia obrazów).

W poniższych trzech sekcjach opisano te opcje.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Opcja 1. Przekaż dysk VHD jako Shared Image Gallery

1. Przekaż wirtualne dyski twarde na konto magazynu.
2. Na stronie Azure Portal wyszukaj szablon **niestandardowy Deploy a custom (Wdrażanie szablonu niestandardowego).**
3. Wybierz **pozycję Build your own template in the editor (Skompilowanie własnego szablonu w edytorze).**
4. Skopiuj następujący szablon Azure Resource Manager (ARM).

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Wklej szablon do edytora.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Przykładowy ekran kodu dla maszyny wirtualnej.":::

1. Wybierz pozycję **Zapisz**.
1. Użyj parametrów w tej tabeli, aby wypełnić pola na ekranie poniżej.

| Parametry | Opis |
| --- | --- |
| sourceStorageAccountResourceId | Identyfikator zasobu źródłowego konta magazynu, w którym znajduje się dysk vhd obiektu blob.<br><br>Aby uzyskać identyfikator zasobu,  przejdź do konta magazynu na stronie **Azure Portal**, przejdź do właściwości **i** skopiuj **wartość ResourceID.** |
| sourceBlobUri | Uri obiektu blob dysku systemu operacyjnego vhd blob (musi znajdować się na dostarczonym koncie magazynu).<br><br>Aby uzyskać adres URL obiektu  blob, przejdź do konta magazynu na stronie **Azure Portal**, przejdź do obiektu **blob** i skopiuj **wartość adresu URL.** |
| sourceBlobDataDisk0Uri | Uri obiektu blob dysku danych vhd blob (musi znajdować się na dostarczonym koncie magazynu). Jeśli nie masz dysku danych, usuń ten parametr z szablonu.<br><br>Aby uzyskać adres URL obiektu  blob, przejdź do konta magazynu na stronie **Azure Portal**, przejdź do obiektu **blob** i skopiuj **wartość adresu URL.** |
| sourceBlobDataDisk1Uri | Uri obiektu blob dodatkowego dysku danych vhd blob (musi znajdować się na dostarczonym koncie magazynu). Jeśli nie masz dodatkowego dysku danych, usuń ten parametr z szablonu.<br><br>Aby uzyskać adres URL obiektu  blob, przejdź do konta magazynu na stronie **Azure Portal**, przejdź do obiektu **blob** i skopiuj **wartość adresu URL.** |
| galleryName (nazwa galerii) | Nazwa Shared Image Gallery |
| galleryImageDefinitionName | Nazwa definicji obrazu |
| galleryImageVersionName | Nazwa wersji obrazu do utworzenia w tym formacie: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Pokazuje okno wdrożenia niestandardowego.":::

8. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

> [!TIP]
> Konto wydawcy musi mieć dostęp "Właściciel", aby opublikować obraz SIG. W razie potrzeby wykonaj poniższe kroki, aby udzielić dostępu:
>
> 1. Przejdź do Shared Image Gallery (SIG).
> 2. Wybierz **pozycję Kontrola dostępu** (IAM) w panelu po lewej stronie.
> 3. Wybierz **pozycję Dodaj**, a następnie pozycję Dodaj **przypisanie roli.**
> 4. W **przypadku roli** wybierz pozycję **Właściciel**.
> 5. W **przypadku przypisywania dostępu do** wybierz pozycję **Użytkownik, grupa lub nazwa główna usługi.**
> 6. Wprowadź adres e-mail platformy Azure osoby, która opublikuje obraz.
> 7. Wybierz pozycję **Zapisz**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Zostanie wyświetlone okno dodawania przypisania roli.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Opcja 2. Przekazywanie wirtualnego dysku twardego na konto magazynu

Skonfiguruj i przygotuj maszynę wirtualną do przekazania zgodnie z opisem w tesłudze [Prepare a Windows VHD or VHDX to upload to Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) (Przygotowywanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure) lub Create and Upload a Linux VHD (Tworzenie i przekazywanie wirtualnego dysku [twardego systemu Linux).](../virtual-machines/linux/create-upload-generic.md)

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Opcja 3. Wyodrębnianie wirtualnego dysku twardego z obrazu zarządzanego (w przypadku korzystania z usług tworzenia obrazów)

Jeśli używasz usługi tworzenia obrazów, np. [Packer,](https://www.packer.io/)może być konieczne wyodrębnienie dysku VHD z obrazu. Nie ma bezpośredniego sposobu, aby to zrobić. Musisz utworzyć maszynę wirtualną i wyodrębnić wirtualny dysk twardy z dysku maszyny wirtualnej.

## <a name="create-the-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej na Azure Portal

Wykonaj następujące kroki, aby utworzyć podstawowy obraz maszyny wirtualnej na [Azure Portal](https://ms.portal.azure.com/).

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).
2. Wybierz pozycję **Maszyny wirtualne**.
3. Wybierz **pozycję + Dodaj,** aby otworzyć **ekran Tworzenie maszyny wirtualnej.**
4. Wybierz obraz z listy rozwijanej lub wybierz pozycję **Przeglądaj** wszystkie obrazy publiczne i prywatne, aby wyszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych.
5. Aby utworzyć **maszynę wirtualną gen 2,** przejdź do karty **Zaawansowane** i wybierz opcję **Gen 2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Wybierz zalecany rozmiar maszyny wirtualnej dla wybranego obrazu.":::

7. Podaj inne wymagane szczegóły, aby utworzyć maszynę wirtualną.
8. Wybierz **pozycję Przejrzyj i utwórz,** aby przejrzeć wybrane opcje. Gdy pojawi **się komunikat Weryfikacja przekazana,** wybierz pozycję **Utwórz**.

Platforma Azure rozpoczyna aprowizowanie określonej maszyny wirtualnej. Śledź postęp, wybierając **kartę Virtual Machines** w menu po lewej stronie. Po utworzeniu stan maszyny wirtualnej zmieni się **na** Uruchomiona.

## <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Zapoznaj się z następującą dokumentacją, aby nawiązać połączenie z maszyną [wirtualną z systemem Windows](../virtual-machines/windows/connect-logon.md) [lub Linux.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Następne kroki

- [Przetestuj obraz maszyny wirtualnej,](azure-vm-image-test.md) aby upewnić się, że Azure Marketplace wymagania dotyczące publikowania. Jest to opcjonalne.
- Jeśli nie chcesz testować obrazu maszyny wirtualnej, zaloguj się do usługi [Partner Center](https://partner.microsoft.com/) i opublikuj obraz SIG (opcja #1).
- Jeśli opcja jest #2 lub #3, [wygeneruj sygnaturę dostępu współdzielonego URI](azure-vm-get-sas-uri.md).
- Jeśli napotkano trudności podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz często zadawane pytania dotyczące maszyn [wirtualnych, aby uzyskać Azure Marketplace](azure-vm-create-faq.md).
