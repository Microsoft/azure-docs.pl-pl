---
title: Tworzenie oferty maszyny wirtualnej platformy Azure w witrynie Azure Marketplace przy użyciu własnego obrazu
description: Dowiedz się, jak opublikować ofertę maszyny wirtualnej w witrynie Azure Marketplace przy użyciu własnego obrazu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200461"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Jak utworzyć maszynę wirtualną przy użyciu własnego obrazu

W tym artykule opisano sposób tworzenia i wdrażania obrazu maszyny wirtualnej (VM) dostarczonej przez użytkownika.

> [!NOTE]
> Przed rozpoczęciem tej procedury zapoznaj się z [wymaganiami technicznymi](marketplace-virtual-machines.md#technical-requirements) dotyczącymi ofert maszyn wirtualnych platformy Azure, łącznie z wymaganiami dotyczącymi wirtualnego dysku twardego (VHD).

Aby zamiast tego użyć zatwierdzonego obrazu podstawowego, postępuj zgodnie z instrukcjami w temacie [Tworzenie obrazu maszyny wirtualnej na podstawie zatwierdzonej podstawy](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Konfigurowanie maszyny wirtualnej

W tej sekcji opisano, jak zmienić rozmiar, zaktualizować i uogólnić maszynę wirtualną platformy Azure. Te kroki są niezbędne, aby przygotować maszynę wirtualną do wdrożenia w portalu Azure Marketplace.

### <a name="size-the-vhds"></a>Rozmiar dysków VHD

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Zainstaluj najnowsze aktualizacje

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Wykonaj więcej kontroli zabezpieczeń

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie konfiguracji niestandardowej i zaplanowanych zadań

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Aby to osiągnąć, musi być uogólniony wirtualny dysk twardy systemu operacyjnego, operacja, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

## <a name="bring-your-image-into-azure"></a>Przenoszenie obrazu na platformę Azure

Istnieją trzy sposoby na umieszczenie obrazu na platformie Azure:

1. Przekaż wirtualny dysk twardy do galerii obrazów udostępnionych (SIG).
1. Przekaż wirtualny dysk twardy do konta usługi Azure Storage.
1. Wyodrębnij wirtualny dysk twardy z zarządzanego obrazu (w przypadku korzystania z usług kompilowania obrazów).

Poniższe trzy sekcje opisują te opcje.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Opcja 1. przekazywanie wirtualnego dysku twardego jako galerii obrazów udostępnionych

1. Przekaż wirtualne dyski twarde do konta magazynu.
2. Na Azure Portal Wyszukaj pozycję **Wdróż szablon niestandardowy**.
3. Wybierz opcję **Kompiluj własny szablon w edytorze**.
4. Skopiuj poniższy szablon Azure Resource Manager (ARM).

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
| sourceStorageAccountResourceId | Identyfikator zasobu źródłowego konta magazynu, w którym znajduje się wirtualny dysk twardy.<br><br>Aby uzyskać identyfikator zasobu, przejdź do **konta magazynu** na **Azure Portal**, przejdź do pozycji **Właściwości** i skopiuj wartość **ResourceID** . |
| sourceBlobUri | Identyfikator URI obiektu BLOB dysku VHD systemu operacyjnego (musi znajdować się w udostępnionym koncie magazynu).<br><br>Aby uzyskać adres URL obiektu BLOB, przejdź do swojego **konta magazynu** na **Azure Portal**, przejdź do **obiektu BLOB** i skopiuj wartość **adresu URL** . |
| sourceBlobDataDisk0Uri | Identyfikator URI obiektu BLOB dysku danych (musi znajdować się w udostępnionym koncie magazynu). Jeśli nie masz dysku z danymi, Usuń ten parametr z szablonu.<br><br>Aby uzyskać adres URL obiektu BLOB, przejdź do swojego **konta magazynu** na **Azure Portal**, przejdź do **obiektu BLOB** i skopiuj wartość **adresu URL** . |
| sourceBlobDataDisk1Uri | Identyfikator URI obiektu BLOB dodatkowego dysku danych (musi znajdować się w udostępnionym koncie magazynu). Jeśli nie masz dodatkowego dysku z danymi, Usuń ten parametr z szablonu.<br><br>Aby uzyskać adres URL obiektu BLOB, przejdź do swojego **konta magazynu** na **Azure Portal**, przejdź do **obiektu BLOB** i skopiuj wartość **adresu URL** . |
| Galeria | Nazwa galerii obrazów udostępnionych |
| galleryImageDefinitionName | Nazwa definicji obrazu |
| galleryImageVersionName | Nazwa wersji obrazu do utworzenia, w tym formacie: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Pokazuje okno wdrożenia niestandardowego.":::

8. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

> [!TIP]
> Konto wydawcy musi mieć dostęp "właściciel", aby opublikować obraz SIG. W razie potrzeby postępuj zgodnie z poniższymi krokami, aby udzielić dostępu:
>
> 1. Przejdź do galerii obrazów udostępnionych (SIG).
> 2. Na panelu po lewej stronie wybierz pozycję **Kontrola dostępu** (IAM).
> 3. Wybierz pozycję **Dodaj**, a następnie **Dodaj przypisanie roli**.
> 4. W obszarze **rola** wybierz pozycję **właściciel**.
> 5. Aby **przypisać dostęp do**, wybierz **użytkownika, grupę lub nazwę główną usługi**.
> 6. Wprowadź adres e-mail platformy Azure osoby, która będzie publikować obraz.
> 7. Wybierz pozycję **Zapisz**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Zostanie wyświetlone okno Dodawanie przypisania roli.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Opcja 2: przekazywanie wirtualnego dysku twardego do konta magazynu

Skonfiguruj i przygotuj maszynę wirtualną do przekazania zgodnie z opisem w artykule [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) lub [tworzenia i przekazywania dysku VHD z systemem Linux](../virtual-machines/linux/create-upload-generic.md).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Opcja 3: Wyodrębnienie wirtualnego dysku twardego z zarządzanego obrazu (w przypadku korzystania z usług kompilowania obrazów)

Jeśli używasz usługi tworzenia obrazów, takiej jak program [Packer](https://www.packer.io/), może być konieczne wyodrębnienie wirtualnego dysku twardego z obrazu. Nie ma żadnego bezpośredniego sposobu, aby to zrobić. Konieczne będzie utworzenie maszyny wirtualnej i wyodrębnienie wirtualnego dysku twardego z dysku maszyny wirtualnej.

## <a name="create-the-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej na Azure Portal

Wykonaj następujące kroki, aby utworzyć podstawowy obraz maszyny wirtualnej na [Azure Portal](https://ms.portal.azure.com/).

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).
2. Wybierz pozycję **Maszyny wirtualne**.
3. Wybierz pozycję **+ Dodaj** , aby otworzyć ekran **Utwórz maszynę wirtualną** .
4. Wybierz obraz z listy rozwijanej lub wybierz pozycję **Przeglądaj wszystkie obrazy publiczne i prywatne** , aby przeszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych.
5. Aby utworzyć maszynę wirtualną **generacji 2** , przejdź do karty **Zaawansowane** i wybierz opcję **generacji 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Wybierz zalecany rozmiar maszyny wirtualnej dla wybranego obrazu.":::

7. Podaj inne wymagane szczegóły, aby utworzyć maszynę wirtualną.
8. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Gdy zostanie wyświetlony komunikat **weryfikacja przebiegła pomyślnie** , wybierz pozycję **Utwórz**.

Platforma Azure rozpocznie Inicjowanie obsługi określonej maszyny wirtualnej. Śledź postęp, wybierając kartę **Virtual Machines** w menu po lewej stronie. Po utworzeniu stanu zmian maszyny wirtualnej do **uruchomienia**.

## <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Zapoznaj się z poniższą dokumentacją, aby nawiązać połączenie z maszyną wirtualną z [systemem Windows](../virtual-machines/windows/connect-logon.md) lub [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Następne kroki

- [Przetestuj obraz maszyny wirtualnej](azure-vm-image-test.md) , aby upewnić się, że spełnia on wymagania dotyczące publikowania w witrynie Azure Marketplace. Jest to opcjonalne.
- Jeśli nie chcesz testować obrazu maszyny wirtualnej, zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/) i Opublikuj obraz SIG (opcja #1).
- Jeśli obserwujesz opcję #2 lub #3, [Wygeneruj identyfikator URI sygnatury dostępu współdzielonego](azure-vm-get-sas-uri.md).
- Jeśli wystąpił problem podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [często zadawane pytania dotyczące maszyn wirtualnych dla witryny Azure Marketplace](azure-vm-create-faq.md).
