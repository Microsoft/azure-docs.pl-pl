---
title: Ograniczenia Azure Cloud Shell | Microsoft Docs
description: Omówienie ograniczeń Azure Cloud Shell
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
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74951483"
---
# <a name="limitations-of-azure-cloud-shell"></a>Ograniczenia Azure Cloud Shell

Azure Cloud Shell ma następujące znane ograniczenia:

## <a name="general-limitations"></a>Ogólne ograniczenia

### <a name="system-state-and-persistence"></a>Stan i trwałość systemu

Komputer, na którym jest dostępna sesja Cloud Shell, jest tymczasowy i jest odtwarzany po upływie 20 minut aktywności. Cloud Shell wymaga zainstalowania udziału plików platformy Azure. W związku z tym subskrypcja musi mieć możliwość skonfigurowania zasobów magazynu w celu uzyskania dostępu do Cloud Shell. Inne zagadnienia obejmują:

* W przypadku zainstalowanego magazynu utrwalane są tylko modyfikacje znajdujące `$Home` się w katalogu.
* Udziały plików platformy Azure można instalować tylko z poziomu [przypisanego regionu](persisting-shell-storage.md#mount-a-new-clouddrive).
  * W programie bash Uruchom polecenie, `env` Aby znaleźć swój region ustawiony jako `ACC_LOCATION` .

### <a name="browser-support"></a>Obsługa przeglądarki

Cloud Shell obsługuje najnowsze wersje programu Microsoft Edge, programu Microsoft Internet Explorer, Google Chrome, Mozilla Firefox i Apple Safari. Przeglądarka Safari w trybie prywatnym nie jest obsługiwana.

### <a name="copy-and-paste"></a>Kopiowanie i wklejanie

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Dla danego użytkownika można uaktywnić tylko jedną powłokę.

Użytkownicy mogą uruchamiać tylko jeden typ powłoki naraz, **bash** lub **PowerShell**. Może jednak istnieć wiele wystąpień bash lub PowerShell uruchomionych jednocześnie. Zamiana między bash lub PowerShell przy użyciu menu powoduje ponowne uruchomienie Cloud Shell, co kończy istniejące sesje. Alternatywnie można uruchomić bash wewnątrz programu PowerShell `bash` , wpisując polecenie, a następnie można uruchomić program PowerShell wewnątrz bash, wpisując `pwsh` .

### <a name="usage-limits"></a>Limity użycia

Cloud Shell jest przeznaczony dla interaktywnych przypadków użycia. W efekcie wszystkie długotrwałe sesje nieinterakcyjne zostaną zakończone bez ostrzeżenia.

## <a name="bash-limitations"></a>Ograniczenia bash

### <a name="user-permissions"></a>Uprawnienia użytkowników

Uprawnienia są ustawiane jako regularni użytkownicy bez dostępu sudo. Jakakolwiek instalacja poza `$Home` katalogiem nie jest utrwalona.

### <a name="editing-bashrc-or-profile"></a>Edytowanie. bashrc lub $PROFILE

Należy zachować ostrożność podczas edycji pliku. bashrc lub $PROFILE programu PowerShell, dzięki czemu może to spowodować nieoczekiwane błędy w Cloud Shell.

## <a name="powershell-limitations"></a>Ograniczenia programu PowerShell

### <a name="azuread-module-name"></a>`AzureAD` Nazwa modułu

`AzureAD`Nazwa modułu jest obecnie w `AzureAD.Standard.Preview` tej samej funkcji.

### <a name="sqlserver-module-functionality"></a>`SqlServer` funkcje modułu

`SqlServer`Moduł zawarty w Cloud Shell ma tylko wstępne wsparcie dla programu PowerShell Core. W szczególności `Invoke-SqlCmd` nie jest jeszcze dostępna.

### <a name="default-file-location-when-created-from-azure-drive"></a>Domyślna lokalizacja pliku podczas tworzenia z dysku platformy Azure:

Przy użyciu poleceń cmdlet programu PowerShell użytkownicy nie mogą tworzyć plików na dysku platformy Azure:. Gdy użytkownicy tworzą nowe pliki przy użyciu innych narzędzi, takich jak vim lub nano, pliki są zapisywane `$HOME` domyślnie. 

### <a name="gui-applications-are-not-supported"></a>Aplikacje graficznego interfejsu użytkownika nie są obsługiwane

Jeśli użytkownik uruchamia polecenie, które może utworzyć okno dialogowe systemu Windows, zostanie wyświetlony komunikat o błędzie, taki jak: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` .

### <a name="large-gap-after-displaying-progress-bar"></a>Duża przerwa po wyświetleniu paska postępu

Jeśli użytkownik wykonuje akcję, która wyświetla pasek postępu, na przykład kartę kończącą się na `Azure:` dysku, możliwe jest, że kursor nie jest prawidłowo ustawiony i pojawia się przerwy, gdzie pasek postępu był wcześniej.

## <a name="next-steps"></a>Następne kroki

[Cloud Shell rozwiązywania problemów](troubleshooting.md) <br>
[Przewodnik Szybki start po powłoce Bash](quickstart.md) <br>
[Przewodnik Szybki start po programie PowerShell](quickstart-powershell.md)
