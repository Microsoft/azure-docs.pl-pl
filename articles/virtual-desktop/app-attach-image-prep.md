---
title: Windows Virtual Desktop Prepare MSIX App Podgląd obrazu — Azure
description: Jak utworzyć obraz dołączania aplikacji MSIX dla puli hostów Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97426017"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Przygotowanie obrazu MSIX dla pulpitu wirtualnego systemu Windows

> [!IMPORTANT]
> Dołączenie do aplikacji MSIX jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dołączenie do aplikacji MSIX (wersja zapoznawcza) to rozwiązanie do tworzenia warstw aplikacji, które pozwala dynamicznie dołączać aplikacje z pakietu MSIX do sesji użytkownika. System pakietów MSIX oddziela aplikacje od systemu operacyjnego, ułatwiając tworzenie obrazów maszyn wirtualnych. Pakiety MSIX zapewniają również większą kontrolę nad aplikacjami, do których użytkownicy mogą uzyskiwać dostęp na ich maszynach wirtualnych. Możesz nawet oddzielić aplikacje od obrazu głównego i udostępnić je użytkownikom później.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Tworzenie pakietu dysku VHD lub VHDX dla MSIX

Pakiety MSIX muszą być w formacie VHD lub VHDX, aby działały prawidłowo. Oznacza to, że aby rozpocząć, musisz utworzyć pakiet VHD lub VHDX.

>[!NOTE]
>Jeśli jeszcze tego nie zrobiono, upewnij się, że włączona jest funkcja Hyper-V, postępując zgodnie z instrukcjami w temacie [Instalowanie funkcji Hyper-v w systemie Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Aby utworzyć pakiet VHD lub VHDX dla MSIX:

1. Najpierw otwórz program PowerShell.
2. Następnie uruchom następujące polecenie cmdlet, aby utworzyć dysk VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Upewnij się, że wirtualny dysk twardy jest wystarczająco duży, aby pomieścić rozwinięty pakiet MSIX.

3. Uruchom następujące polecenie cmdlet, aby zainstalować dysk VHD, który właśnie został utworzony:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Następnie Uruchom to polecenie cmdlet w celu zainicjowania zainstalowanego wirtualnego dysku twardego:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Następnie Uruchom to polecenie cmdlet, aby utworzyć nową partycję dla zainicjowanego wirtualnego dysku twardego:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Uruchom to polecenie cmdlet, aby sformatować partycję:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Na koniec Utwórz folder nadrzędny na zainstalowanym wirtualnym dysku twardym. Ten krok jest wymagany, ponieważ pakiet MSIX musi mieć folder nadrzędny, aby działał poprawnie. Nie ma znaczenia, jak nazywa się folder nadrzędny, tak długo, jak folder nadrzędny istnieje.

## <a name="expand-msix"></a>Rozwiń MSIX

Następnie należy rozszerzyć obraz MSIX przez "rozpakowywanie" plików do dysku VHD.

Aby rozwinąć obraz MSIX:

1. [Pobierz narzędzie msixmgr](https://aka.ms/msixmgr) i Zapisz folder. zip do folderu na maszynie wirtualnej hosta sesji.

2. Rozpakuj folder Narzędzia msixmgr. zip.

3. Umieść pakiet źródłowy MSIX w tym samym folderze, w którym zostało rozpakowane narzędzie msixmgr.

4. Otwórz wiersz polecenia jako administrator i przejdź do folderu, w którym zostało pobrane i rozpakowane narzędzie msixmgr.

5. Uruchom następujące polecenie cmdlet, aby rozpakować MSIX do dysku VHD utworzonego w poprzedniej sekcji.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Po zakończeniu rozpakowywania powinien zostać wyświetlony następujący komunikat:

    > Pomyślnie cofnięto pakowanie i zastosowane listy ACL dla pakietu: <package name> . msix

    >[!NOTE]
    > Jeśli używasz pakietów z Microsoft Store dla firmowych lub edukacyjnych w sieci lub na urządzeniach, które nie są połączone z Internetem, musisz pobrać i zainstalować licencje pakietów z Microsoft Store, aby uruchomić aplikacje. Aby uzyskać licencje, zobacz [Korzystanie z pakietów w trybie offline](app-attach.md#use-packages-offline).

6. Przejdź do zainstalowanego wirtualnego dysku twardego i Otwórz folder aplikacji, aby upewnić się, że zawartość pakietu jest tam dostępna.

7. Odinstaluj dysk VHD.

## <a name="upload-msix-image-to-share"></a>Przekaż obraz MSIX do udostępnienia

Po utworzeniu pakietu MSIX należy przekazać powstały plik VHD, VHDX lub pliku CIM do udziału, w którym użytkownicy mogą uzyskać do niego dostęp.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszymi pytaniami dotyczącymi tej funkcji w [TechCommunity pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii pulpitu wirtualnego systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Poniżej znajdują się inne artykuły, które mogą okazać się przydatne:

- [Dołączanie słownika aplikacji MSIX](app-attach-glossary.md)
- [Załączanie aplikacji MSIX — często zadawane pytania](app-attach-faq.md)