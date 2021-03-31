---
title: Dostosowywanie właściwości RDP przy użyciu programu PowerShell — Azure
description: Jak dostosować właściwości protokołu RDP dla pulpitu wirtualnego systemu Windows przy użyciu poleceń cmdlet programu PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc3a08f383368b189e41bebd204707f2483e77c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95018311"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Dostosowywanie właściwości Remote Desktop Protocol (RDP) dla puli hostów

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).

Dostosowanie właściwości Remote Desktop Protocol puli hostów (RDP), takich jak środowisko monitorowania i przekierowania audio, zapewnia optymalne środowisko dla użytkowników zgodnie z ich potrzebami. Właściwości protokołu RDP można dostosować na pulpicie wirtualnym systemu Windows przy użyciu Azure Portal lub za pomocą parametru *-CustomRdpProperty* w poleceniu cmdlet **Update-AzWvdHostPool** .

Zobacz [obsługiwane ustawienia plików RDP](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) , aby uzyskać pełną listę obsługiwanych właściwości i ich wartości domyślne.

## <a name="default-rdp-file-properties"></a>Domyślne właściwości pliku RDP

Domyślnie pliki RDP mają następujące właściwości:

|Właściwość RDP|Na pulpicie|Jako usługi RemoteApp|
|---|---|---|
|Tryb z obsługą kilku monitorów|Disabled|Enabled (Włączony)|
|Przekierowania dysków włączone|Dyski, schowek, drukarki, porty COM i karty inteligentne|Dyski, schowek i drukarki|
|Tryb zdalny audio|Odtwórz lokalnie|Odtwórz lokalnie|

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem postępuj zgodnie z instrukcjami w temacie [Konfigurowanie modułu PowerShell pulpitu wirtualnego systemu Windows](powershell-module.md) w celu skonfigurowania modułu programu PowerShell i zalogowania się na platformie Azure.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Konfigurowanie właściwości RDP w Azure Portal

Aby skonfigurować właściwości RDP w Azure Portal:

1. Zaloguj się do platformy Azure pod adresem <https://portal.azure.com> .
2. Na pasku wyszukiwania wprowadź **pulpit wirtualny systemu Windows** .
3. W obszarze usługi wybierz pozycję **pulpit wirtualny systemu Windows**.
4. Na stronie pulpit wirtualny systemu Windows wybierz pozycję **Pule hostów** w menu po lewej stronie ekranu.
5. Wybierz **nazwę puli hostów** , którą chcesz zaktualizować.
6. Wybierz pozycję **Właściwości RDP** w menu po lewej stronie ekranu.
7. Ustaw żądaną właściwość.
   - Alternatywnie możesz otworzyć kartę **Zaawansowane** i dodać właściwości RDP w formacie rozdzielonym średnikami, jak przykłady programu PowerShell w poniższych sekcjach.
8. Gdy skończysz, wybierz pozycję **Zapisz** , aby zapisać zmiany.

W następnych sekcjach opisano, jak ręcznie edytować niestandardowe właściwości protokołu RDP w programie PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Dodaj lub Edytuj pojedynczą niestandardową Właściwość RDP

Aby dodać lub edytować pojedynczą niestandardową Właściwość RDP, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Aby sprawdzić, czy polecenie cmdlet właśnie uruchomiło aktualizację, Uruchom to polecenie cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Jeśli na przykład sprawdzisz Właściwość "audiocapturemode" w puli hostów o nazwie 0301HP, wprowadzisz następujące polecenie cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Dodawanie lub Edytowanie wielu niestandardowych właściwości RDP

Aby dodać lub edytować wiele niestandardowych właściwości RDP, uruchom następujące polecenia cmdlet programu PowerShell, dostarczając niestandardowe właściwości protokołu RDP jako ciąg rozdzielony średnikami:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Aby upewnić się, że Właściwość RDP została dodana, możesz uruchomić następujące polecenie cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

W zależności od wcześniejszego przykładu poleceń cmdlet w przypadku skonfigurowania wielu właściwości RDP w puli hostów 0301HP polecenie cmdlet będzie wyglądać następująco:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Zresetuj wszystkie niestandardowe właściwości RDP

Możesz zresetować pojedyncze niestandardowe właściwości protokołu RDP do wartości domyślnych, postępując zgodnie z instrukcjami w temacie [Dodawanie lub edytowanie pojedynczej niestandardowej właściwości RDP](#add-or-edit-a-single-custom-rdp-property), albo Zresetuj wszystkie niestandardowe właściwości protokołu RDP dla puli hostów, uruchamiając następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Aby upewnić się, że to ustawienie zostało pomyślnie usunięte, wprowadź następujące polecenie cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Następne kroki

Teraz, po dostosowaniu właściwości RDP dla danej puli hostów, można zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować je w ramach sesji użytkownika. W poniższych następnych przewodnikach przedstawiono sposób nawiązywania połączenia z sesją przy użyciu wybranego przez siebie klienta:

- [Łączenie się z klientem klasycznym systemu Windows](connect-windows-7-10.md)
- [Łączenie się z klientem internetowym](connect-web.md)
- [Łączenie się z klientem systemu Android](connect-android.md)
- [Nawiązywanie połączenia z klientem systemu macOS](connect-macos.md)
- [Nawiązywanie połączenia z klientem systemu iOS](connect-ios.md)