---
title: Korzystanie z narzędzia MSIXMGR — Azure
description: Jak używać narzędzia MSIXMGR dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b2cab56fb99bda7da361cc2068396e53e794501d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448342"
---
# <a name="using-the-msixmgr-tool"></a>Korzystanie z narzędzia MSIXMGR

Narzędzie MSIXMGR umożliwia rozszerzanie spakowanych aplikacji MSIX na obrazy MSIX. Narzędzie przyjmuje aplikację MSIX (. MSIX) i rozszerza je do pliku VHD, VHDx lub modelu CIM. Utworzony obraz MSIX jest przechowywany na koncie usługi Azure Storage, którego używa wdrożenie pulpitu wirtualnego systemu Windows. W tym artykule przedstawiono sposób korzystania z narzędzia MSIXMGR.

>[!NOTE]
>Aby zagwarantować zgodność, upewnij się, że CIMs przechowujący obrazy MSIX są generowane w wersji systemu operacyjnego, która jest uruchamiana w pulach hosta pulpitu wirtualnego systemu Windows. MSIXMGR może tworzyć pliki CIM, ale można używać tych plików tylko z pulą hostów z systemem Windows 10 20H2.

## <a name="requirements"></a>Wymagania

Zanim będzie można wykonać instrukcje podane w tym artykule, należy wykonać następujące czynności:

- [Pobierz narzędzie MSIXMGR](https://aka.ms/msixmgr)
- Pobierz spakowaną aplikację MSIX (. Plik MSIX)
- Uzyskaj uprawnienia administracyjne na komputerze, na którym utworzysz obraz MSIX

## <a name="create-an-msix-image"></a>Tworzenie obrazu MSIX

Rozszerzanie to proces tworzenia spakowanej aplikacji MSIX (. MSIX) i rozpakowywanie go do obrazu MSIX (. Dysk VHD (x) lub. Plik CIM).

Aby rozwinąć plik MSIX:

1. [Pobierz narzędzie MSIXMGR,](https://aka.ms/msixmgr) Jeśli jeszcze tego nie zrobiono.

2. Rozpakuj MSIXMGR.zip do folderu lokalnego.

3. Otwórz wiersz polecenia w trybie podniesionych uprawnień.

4. Znajdź folder lokalny w kroku 2.

5. Uruchom następujące polecenie w wierszu polecenia, aby utworzyć obraz MSIX.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Pamiętaj, aby zastąpić wartości symboli zastępczych odpowiednimi wartościami. Na przykład:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Po utworzeniu obrazu przejdź do folderu docelowego i upewnij się, że utworzono pomyślnie obraz MSIX (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Tworzenie obrazu MSIX w pliku CIM

Możesz również użyć polecenia w [kroku 5](#create-an-msix-image) , aby utworzyć pliki CIM i VHDX, zastępując typ pliku i ścieżkę docelową.

Na przykład poniżej przedstawiono sposób użycia tego polecenia do tworzenia pliku CIM:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Oto jak używać tego polecenia do tworzenia pliku VHDX:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat dołączania do aplikacji MSIX, co [to jest aplikacja MSIX?](what-is-app-attach.md)

Aby dowiedzieć się, jak skonfigurować dołączenie do aplikacji, zapoznaj się z następującymi artykułami:

- [Konfigurowanie dołączania aplikacji MSIX przy użyciu witryny Azure Portal](app-attach-azure-portal.md)
- [Konfigurowanie dołączania aplikacji MSIX przy użyciu programu PowerShell](app-attach-powershell.md)
- [Tworzenie skryptów programu PowerShell dla dołączania aplikacji MSIX](app-attach.md)
- [Przygotowanie obrazu MSIX dla pulpitu wirtualnego systemu Windows](app-attach-image-prep.md)
- [Skonfiguruj udział plików do dołączenia do aplikacji MSIX](app-attach-file-share.md)

Jeśli masz pytania na temat dołączania do aplikacji MSIX, zapoznaj się z tematem [załączanie często zadawanych pytań](app-attach-faq.md) i [słowników](app-attach-glossary.md)
