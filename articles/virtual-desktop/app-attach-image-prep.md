---
title: Windows Virtual Desktop przygotowanie obrazu dołączania aplikacji MSIX — Azure
description: Jak utworzyć obraz dołączania aplikacji MSIX dla Windows Virtual Desktop puli hostów.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 443f117907381862639564dfbf9752562f4a3564
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363669"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Przygotowywanie obrazu MSIX do Windows Virtual Desktop

Dołączanie aplikacji MSIX to rozwiązanie do warstw aplikacji, które umożliwia dynamiczne dołączanie aplikacji z pakietu MSIX do sesji użytkownika. System pakietów MSIX oddziela aplikacje od systemu operacyjnego, co ułatwia tworzenie obrazów dla maszyn wirtualnych. Pakiety MSIX zapewniają również większą kontrolę nad aplikacjami, do których użytkownicy mogą uzyskać dostęp na swoich maszynach wirtualnych. Możesz nawet oddzielić aplikacje od obrazu głównego i przekazać je użytkownikom później.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Tworzenie pakietu VHD lub VHDX dla MSIX

Pakiety MSIX muszą mieć format VHD lub VHDX, aby działały prawidłowo. Oznacza to, że aby rozpocząć pracę, musisz utworzyć pakiet VHD lub VHDX.

>[!NOTE]
>Jeśli funkcja Hyper-V nie została jeszcze włączyć, należy wykonać instrukcje z tematu Instalowanie funkcji [Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)na Windows 10 .

Aby utworzyć pakiet VHD lub VHDX dla msix:

1. Najpierw otwórz program PowerShell.
2. Następnie uruchom następujące polecenie cmdlet, aby utworzyć dysk VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Upewnij się, że wirtualny dysk twardy jest wystarczająco duży, aby pomieścić rozwinięty pakiet MSIX.

3. Uruchom następujące polecenie cmdlet, aby zainstalować właśnie utworzony wirtualny dysk twardy:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Następnie uruchom to polecenie cmdlet, aby zainicjować zainstalowany dysk VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Następnie uruchom to polecenie cmdlet, aby utworzyć nową partycję dla zainicjowanych dysków VHD:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Uruchom to polecenie cmdlet, aby sformatować partycję:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Na koniec utwórz folder nadrzędny na zainstalowanym dysku VHD. Ten krok jest wymagany, ponieważ pakiet MSIX musi mieć folder nadrzędny, aby działał prawidłowo. Nazwa folderu nadrzędnego nie ma znaczenia, o ile folder nadrzędny istnieje.

## <a name="expand-msix"></a>Rozwiń rozszerzenie MSIX

Następnie należy rozwinąć obraz MSIX przez "rozpakowanie" jego plików do dysku VHD.

Aby rozwinąć obraz MSIX:

1. [Pobierz narzędzie msixmgr i](https://aka.ms/msixmgr) zapisz folder zip w folderze na maszynie wirtualnej hosta sesji.

2. Rozpakować folder zip narzędzia msixmgr.

3. Umieść źródłowy pakiet MSIX w tym samym folderze, w którym rozpakowane jest narzędzie msixmgr.

4. Otwórz wiersz polecenia jako Administrator i przejdź do folderu, do którego pobrano i rozpakowane narzędzie msixmgr.

5. Uruchom następujące polecenie cmdlet, aby rozpakować plik MSIX do wirtualnego dysku twardego utworzonego w poprzedniej sekcji.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Po zakończeniu rozpakowywania powinien zostać wyświetlony następujący komunikat:

    > Pomyślnie rozpakowane i zastosowane adresy ACL dla pakietu: <package name> msix

    >[!NOTE]
    > Jeśli używasz pakietów z usługi Microsoft Store dla Firm lub Education w sieci lub na urządzeniach, które nie są połączone z Internetem, musisz pobrać i zainstalować licencje na pakiety z usługi Microsoft Store, aby uruchamiać aplikacje. Aby uzyskać licencje, zobacz Use [packages offline (Używanie pakietów w trybie offline).](app-attach.md#use-packages-offline)

6. Przejdź do zainstalowanego wirtualnego dysku twardego i otwórz folder app, aby upewnić się, że znajduje się tam zawartość pakietu.

7. Odinstaluj dysk VHD.

## <a name="upload-msix-image-to-share"></a>Przekazywanie obrazu MSIX do udostępnienia

Po utworzeniu pakietu MSIX należy przekazać wynikowy plik VHD, VHDX lub CIM do udziału, do którego mają dostęp maszyny wirtualne użytkowników.

## <a name="next-steps"></a>Następne kroki

Zadaj pytania społeczności dotyczące tej funkcji na stronie Windows Virtual Desktop [TechCo w witrynie](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Opinię na ten temat możesz również Windows Virtual Desktop w [centrum Windows Virtual Desktop opinii.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Oto kilka innych artykułów, które mogą okazać się przydatne:

- [Słownik dołączania aplikacji MSIX](app-attach-glossary.md)
- [Dołączanie aplikacji MSIX — często zadawane pytania](app-attach-faq.md)