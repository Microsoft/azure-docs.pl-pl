---
title: Korzystanie z okna Azure Cloud Shell | Microsoft Docs
description: Omówienie korzystania z okna Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70860320"
---
# <a name="using-the-azure-cloud-shell-window"></a>Korzystanie z okna Azure Cloud Shell

W tym dokumencie wyjaśniono, jak używać okna Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Wymiana między środowiskami bash i PowerShell

Użyj selektora środowiska na pasku narzędzi Cloud Shell, aby zamienić środowiska bash i PowerShell.  
![Wybieranie środowiska](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Uruchom ponownie Cloud Shell
Kliknij ikonę ponownego uruchamiania na pasku narzędzi Cloud Shell, aby zresetować stan komputera.  
![Uruchom ponownie Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Ponowne uruchomienie Cloud Shell spowoduje zresetowanie stanu komputera, a wszystkie pliki, które nie są utrwalane przez udział plików platformy Azure, zostaną utracone.

## <a name="change-the-text-size"></a>Zmień rozmiar tekstu
Kliknij ikonę ustawienia w lewym górnym rogu okna, a następnie umieść kursor nad opcją "rozmiar tekstu" i wybierz żądany rozmiar tekstu. Wybór zostanie utrwalony między sesjami.
![Rozmiar tekstu](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Zmień czcionkę
Kliknij ikonę ustawienia w lewym górnym rogu okna, a następnie umieść kursor nad opcją "Font" (czcionka) i wybierz żądaną czcionkę.  Wybór zostanie utrwalony między sesjami.
![Czcionka](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Przekazywanie i pobieranie plików
Kliknij ikonę Przekaż/Pobierz pliki w lewym górnym rogu okna, a następnie wybierz pozycję Przekaż lub Pobierz.  
![Przekazywanie/pobieranie plików](media/using-the-shell-window/uploaddownload.png)
* W przypadku przekazywania plików użyj okna podręcznego, aby przejść do pliku na komputerze lokalnym, wybierz odpowiedni plik, a następnie kliknij przycisk "Otwórz".  Plik zostanie przekazany do `/home/user` katalogu.
* W przypadku pobierania pliku wprowadź w pełni kwalifikowaną ścieżkę pliku do okna podręcznego (tj. ścieżka w `/home/user` katalogu, która jest domyślnie wyświetlana), a następnie wybierz przycisk "Pobierz".  
> [!NOTE] 
> W przypadku plików i ścieżek plików rozróżniana jest wielkość liter w Cloud Shell. Dokładnie sprawdź wielkość liter w ścieżce pliku.

## <a name="open-another-cloud-shell-window"></a>Otwórz inne okno Cloud Shell
Cloud Shell włącza wiele współbieżnych sesji na kartach przeglądarki, umożliwiając każdej sesji istnieje jako oddzielny proces.
Jeśli kończysz sesję, pamiętaj, aby wyjść z każdego okna sesji, ponieważ każdy proces działa niezależnie, mimo że są uruchamiane na tym samym komputerze.  
Kliknij ikonę Otwórz nową sesję w lewym górnym rogu okna. Zostanie otwarta nowa karta z inną sesją połączoną z istniejącym kontenerem.
![Otwórz nową sesję](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Edytor Cloud Shell
* Zapoznaj się z tematem [Korzystanie z edytora Azure Cloud Shell](using-cloud-shell-editor.md) .

## <a name="web-preview"></a>Podgląd sieci Web
Kliknij ikonę Web Preview w lewym górnym rogu okna, wybierz pozycję "Konfiguruj", określ żądany port do otwarcia.  Wybierz pozycję "Otwórz port", aby otworzyć tylko port, lub pozycję "Otwórz i Przeglądaj", aby otworzyć port i wyświetlić podgląd portu na nowej karcie.  
![Podgląd sieci Web](media/using-the-shell-window/preview.png)  
<br>
![Skonfiguruj port](media/using-the-shell-window/preview-configure.png)  
Kliknij ikonę Web Preview w lewym górnym rogu okna, wybierz pozycję "Podgląd portu..." Aby wyświetlić podgląd otwartego portu na nowej karcie. Kliknij ikonę Web Preview w lewym górnym rogu okna, wybierz pozycję "Zamknij port..." , aby zamknąć otwarty port.  
![Podgląd/zamknięcie portu](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizuj & Maksymalizuj Cloud Shell okno
Kliknij ikonę Minimalizuj w prawym górnym rogu okna, aby je ukryć. Kliknij ponownie ikonę Cloud Shell, aby odkryć.
Kliknij ikonę Maksymalizuj, aby ustawić maksymalną wysokość okna. Aby przywrócić poprzedni rozmiar okna, kliknij przycisk Przywróć.  
![Minimalizuj lub Maksymalizuj okno](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiowanie i wklejanie
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Zmień rozmiar okna Cloud Shell
Kliknij i przeciągnij górną krawędź paska narzędzi w górę lub w dół, aby zmienić rozmiar okna Cloud Shell.

## <a name="scrolling-text-display"></a>Przewijane wyświetlanie tekstu
Przewiń myszą lub płytkę dotykową, aby przenieść tekst terminalu.

## <a name="exit-command"></a>Exit — polecenie
`exit`Przerwanie działania aktywnej sesji. To zachowanie jest domyślnie wykonywane po 20 minutach bez interakcji.

## <a name="next-steps"></a>Następne kroki

[Bash w Cloud Shell Szybki Start](quickstart.md) <br>
[Program PowerShell w Cloud Shell Szybki Start](quickstart-powershell.md)
