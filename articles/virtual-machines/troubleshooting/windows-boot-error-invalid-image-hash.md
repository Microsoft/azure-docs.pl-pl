---
title: Błąd Menedżera rozruchu systemu Windows — 0xC0000428 stanu nieprawidłowy skrót obrazu
titlesuffix: Azure Virtual Machines
description: W tym artykule przedstawiono kroki rozwiązywania problemów z użyciem obrazu podglądu oraz okres próbny, który uniemożliwia rozruch maszyny wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: 22db9c7966b6f988ca0ea799104275f3f86c77ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629576"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Błąd Menedżera rozruchu systemu Windows — 0xC0000428 stanu nieprawidłowy skrót obrazu

W tym artykule przedstawiono kroki rozwiązywania problemów z użyciem obrazu podglądu oraz okres próbny, który uniemożliwia rozruch maszyny wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla Menedżera rozruchu systemu Windows z komunikatem:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![Rysunek 1 przedstawia Menedżera rozruchu systemu Windows ze stanem "Ox0000428" i "informacje" system Windows nie może zweryfikować podpisu cyfrowego dla tego pliku.](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

lub komunikat:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![Rysunek 2 przedstawia Menedżera rozruchu systemu Windows o stanie "Ox0000428" i "informacje" nie można zweryfikować podpisu cyfrowego dla tego pliku.](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Przyczyna

Obraz użyty do skompilowania maszyny wirtualnej był obrazem w wersji zapoznawczej z datą wygaśnięcia, a nie z opcją RTM (wydanie do produkcji). 

Obrazy w wersji zapoznawczej mają wystawiony cykl życia, a wyświetlony zrzut ekranu jest wyświetlany po przejściu do daty wygaśnięcia, co oznacza, że wersja próbna obrazu jest powyżej.

### <a name="example-of-preview-images"></a>Przykład obrazów podglądu

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Nie można przesunąć daty wygaśnięcia obrazu w wersji zapoznawczej. Po wygaśnięciu wersji zapoznawczej nie można uruchamiać maszyny wirtualnej.

- W zależności od produktu okres próbny może się różnić. Na przykład obrazy w wersji zapoznawczej systemu Windows mają 180-dniowy okres próbny.

- Na platformie Azure wszystkie obrazy dla systemu Windows, które są wersjami w wersji zapoznawczej, zawierają informacje w opisie, że nie są przeznaczone do produkcji i są dostępne do użycia tylko w określonym okresie próbnym lub jako wersja zapoznawcza.

## <a name="solution"></a>Rozwiązanie

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

Jeśli obraz jest obrazem w wersji zapoznawczej, nie ma możliwości przeciągnięcia daty wygaśnięcia dla używanego obrazu, należy [wdrożyć nową maszynę wirtualną](../windows/quick-create-portal.md) przy użyciu obrazu bez podglądu. Poniższe kroki ułatwią ustalenie, czy używasz obrazu w wersji zapoznawczej, a także udostępnienie zasobów ułatwiających transfer danych z tej maszyny wirtualnej do nowej maszyny wirtualnej. W przypadku pozytywnego zidentyfikowania obrazu jako obrazu podglądu obraz nie jest możliwy do odzyskania, ponieważ już wygasł.

W zależności od preferencji można użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure w celu zbadania obrazu, aby określić, czy jest to obraz podglądu. Za pomocą tych poleceń można potwierdzić, że obraz jest obrazem podglądu.

### <a name="query-using-azure-powershell"></a>Zapytanie przy użyciu Azure PowerShell

1. Otwórz aplikację Windows PowerShell.
1. Uruchom następujące polecenia:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- W poprzednich poleceniach Zastąp `<LOCATION>` , `<PUBLISHER NAME>` , `<OFFER NAME>` , i `<YEAR WHEN THIS IMAGE WAS RELEASED>` z podanymi informacjami. Usuń również symbole "<" i ">".

  Zobacz poniższy przykład:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Zapytanie przy użyciu interfejsu wiersza polecenia platformy Azure

1. Jeśli jeszcze tego nie zrobiono, należy [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
1. Po pobraniu Użyj wiersza polecenia lub programu PowerShell, aby wprowadzić `az login` polecenie, a następnie zaloguj się przy użyciu poświadczeń konta.
1. Po zalogowaniu wprowadź następujące polecenia:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- W poprzednich poleceniach Zastąp `<LOCATION>` , `<PUBLISHER NAME>` , `<OFFER NAME>` , i `<YEAR WHEN THIS IMAGE WAS RELEASED>` z podanymi informacjami. Usuń również symbole "<" i ">".

  Zobacz poniższy przykład:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```