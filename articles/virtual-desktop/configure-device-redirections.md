---
title: Konfigurowanie przekierowań urządzeń — Azure
description: Jak skonfigurować przekierowania urządzeń dla Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 37ecd06c4e3e71234e8fb1b6bad0cd05482dd31b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727853"
---
# <a name="configure-device-redirections"></a>Konfigurowanie przekierowań urządzeń

Konfigurowanie przekierowań urządzeń dla środowiska Windows Virtual Desktop umożliwia korzystanie z drukarek, urządzeń USB, mikrofonów i innych urządzeń peryferyjnych w sesji zdalnej. Niektóre przekierowania urządzeń wymagają zmian Remote Desktop Protocol (RDP) i ustawień zasady grupy użytkownika.

## <a name="supported-device-redirections"></a>Obsługiwane przekierowania urządzeń

Każdy klient obsługuje różne przekierowania urządzeń. Zapoznaj się [z tematem Porównanie klientów,](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) aby uzyskać pełną listę obsługiwanych przekierowań urządzeń dla każdego klienta.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Dostosowywanie właściwości protokołu RDP dla puli hostów

Aby dowiedzieć się więcej na temat dostosowywania właściwości protokołu RDP dla puli hostów przy użyciu programu PowerShell lub Azure Portal, zapoznaj się z [właściwościami protokołu RDP.](customize-rdp-properties.md) Aby uzyskać pełną listę obsługiwanych właściwości protokołu RDP, zobacz [Obsługiwane ustawienia pliku RDP.](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)

## <a name="setup-device-redirections"></a>Konfigurowanie przekierowań urządzeń

Następujące właściwości protokołu RDP i ustawienia zasady grupy skonfigurować przekierowywania urządzeń.

### <a name="audio-input-microphone-redirection"></a>Przekierowywanie danych wejściowych audio (mikrofon)

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie danych wejściowych audio:

- `audiocapturemode:i:1` umożliwia przekierowywanie danych wejściowych audio.
- `audiocapturemode:i:0` wyłącza przekierowywanie danych wejściowych audio.

### <a name="audio-output-speaker-redirection"></a>Przekierowywanie danych wyjściowych audio (osób mówiących)

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie danych wyjściowych audio:

- `audiomode:i:0` umożliwia przekierowywanie danych wyjściowych audio.
- `audiomode:i:1` lub `audiomode:i:2` wyłącz przekierowywanie danych wyjściowych audio.

### <a name="camera-redirection"></a>Przekierowywanie aparatu

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie aparatu:

- `camerastoredirect:s:*` przekierowuje wszystkie kamery.
- `camerastoredirect:s:` wyłącza przekierowywanie aparatu.

>[!NOTE]
>Nawet jeśli właściwość jest wyłączona, lokalne `camerastoredirect:s:` kamery mogą być przekierowywany za pośrednictwem `devicestoredirect:s:` właściwości. Aby całkowicie wyłączyć zestaw przekierowania aparatu i ustawić lub zdefiniować pewien podzbiór urządzeń `camerastoredirect:s:` `devicestoredirect:s:` Plug and Play, które nie zawierają żadnego aparatu fotograficznego.

Można również przekierować określone kamery przy użyciu rozdzielanej średnikami listy KSCATEGORY_VIDEO_CAMERA interfejsów, takich jak `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` .

### <a name="clipboard-redirection"></a>Przekierowywanie schowka

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie schowka:

- `redirectclipboard:i:1` umożliwia przekierowywanie schowka.
- `redirectclipboard:i:0` wyłącza przekierowywanie schowka.

### <a name="com-port-redirections"></a>Przekierowania portów COM

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie portów COM:

- `redirectcomports:i:1` umożliwia przekierowywanie portów COM.
- `redirectcomports:i:0` Wyłącza przekierowywanie portów COM.

### <a name="usb-redirection"></a>Przekierowywanie USB

Najpierw ustaw następującą właściwość protokołu RDP, aby włączyć przekierowywanie urządzeń USB:

- `usbdevicestoredirect:s:*` włącza przekierowywanie urządzeń USB.
- `usbdevicestoredirect:s:` wyłącza przekierowywanie urządzeń USB.

Po drugie ustaw następujące zasady grupy na urządzeniu lokalnym użytkownika:

- Przejdź do **opcji Zasady konfiguracji**  >   >  **komputera Szablony administracyjne** systemu Windows  >  **Usługi pulpitu zdalnego** Podłączanie pulpitu zdalnego  >    >  **klienta**  >  **RemoteFX przekierowania urządzenia USB.**
- Wybierz opcję Zezwala na przekierowywanie protokołu **RDP innych obsługiwanych urządzeń USB RemoteFX z tego komputera.**
- Wybierz opcję **Włączone,** a następnie wybierz opcję Administratorzy i użytkownicy w prawa dostępu przekierowania **USB RemoteFX.**
- Wybierz przycisk **OK**.

### <a name="plug-and-play-device-redirection"></a>Przekierowywanie urządzeń plug and play

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie urządzeń plug and play:

- `devicestoredirect:s:*` umożliwia przekierowywanie wszystkich urządzeń plug and play.
- `devicestoredirect:s:` wyłącza przekierowywanie urządzeń Plug and Play.

Możesz również wybrać określone urządzenia plug and play przy użyciu listy rozdzielanej średnikami, takiej jak `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Przekierowanie dysku lokalnego

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie dysków lokalnych:

- `drivestoredirect:s:*` umożliwia przekierowywanie wszystkich dysków.
- `drivestoredirect:s:` Wyłącza przekierowanie dysku lokalnego.

Możesz również wybrać określone dyski przy użyciu listy rozdzielanej średnikami, takiej jak `drivestoredirect:s:C:;E:;` .

Aby skonfigurować transfer plików klienta sieci Web, ustaw wartość `drivestoredirect:s:*` .

### <a name="printer-redirection"></a>Przekierowanie drukarki

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowanie drukarki:

- `redirectprinters:i:1` umożliwia przekierowywanie drukarek.
- `redirectprinters:i:0` wyłącza przekierowanie drukarki.

### <a name="smart-card-redirection"></a>Przekierowywanie kart inteligentnych

Ustaw następującą właściwość protokołu RDP, aby skonfigurować przekierowywanie kart inteligentnych:

- `redirectsmartcards:i:1` umożliwia przekierowywanie kart inteligentnych.
- `redirectsmartcards:i:0` wyłącza przekierowywanie kart inteligentnych.
