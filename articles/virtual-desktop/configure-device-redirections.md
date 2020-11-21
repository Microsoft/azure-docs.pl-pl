---
title: Konfigurowanie przekierowań urządzeń — Azure
description: Jak skonfigurować przekierowania urządzeń dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018379"
---
# <a name="configure-device-redirections"></a>Konfigurowanie przekierowań urządzeń

Skonfigurowanie przekierowań urządzeń dla środowiska pulpitu wirtualnego systemu Windows umożliwia korzystanie z drukarek, urządzeń USB, mikrofonów i innych urządzeń peryferyjnych w sesji zdalnej. Niektóre przekierowania urządzeń wymagają zmian właściwości Remote Desktop Protocol (RDP) i zasady grupy ustawień.

## <a name="supported-device-redirections"></a>Obsługiwane przekierowania urządzeń

Każdy klient obsługuje inne przekierowania urządzeń. Zapoznaj się z tematem [porównanie klientów](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) , aby uzyskać pełną listę obsługiwanych przekierowań urządzeń dla każdego klienta.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Dostosowywanie właściwości protokołu RDP dla puli hostów

Aby dowiedzieć się więcej o dostosowywaniu właściwości protokołu RDP dla puli hostów przy użyciu programu PowerShell lub Azure Portal, zapoznaj się z [właściwościami protokołu RDP](customize-rdp-properties.md). Aby uzyskać pełną listę obsługiwanych właściwości protokołu RDP, zobacz [obsługiwane ustawienia plików RDP](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Konfigurowanie przekierowań urządzeń

Aby skonfigurować przekierowania urządzeń, można użyć następujących właściwości protokołu RDP i ustawień zasady grupy.

### <a name="audio-input-microphone-redirection"></a>Przekierowanie Audio Input (mikrofon)

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowywanie danych wejściowych audio:

- `audiocapturemode:i:1` Włącza przekierowywanie wejścia audio.
- `audiocapturemode:i:0` wyłącza przekierowywanie wejścia audio.

### <a name="audio-output-speaker-redirection"></a>Przekierowywanie danych wyjściowych audio (głośników)

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowywanie danych wyjściowych audio:

- `audiomode:i:0` Włącza przekierowywanie danych wyjściowych audio.
- `audiomode:i:1` lub `audiomode:i:2` Wyłącz przekierowywanie danych wyjściowych audio.

### <a name="camera-redirection"></a>Przekierowanie aparatu fotograficznego

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowanie aparatu:

- `camerastoredirect:s:*` przekierowuje wszystkie kamery.
- `camerastoredirect:s:` wyłącza przekierowywanie aparatu.

>[!NOTE]
>Nawet jeśli `camerastoredirect:s:` Właściwość jest wyłączona, lokalne aparaty fotograficzne mogą zostać przekierowane przez `devicestoredirect:s:` Właściwość. Aby całkowicie wyłączyć zestaw przekierowań aparatu `camerastoredirect:s:` i ustawić `devicestoredirect:s:` lub zdefiniować podzestaw urządzeń typu Plug and Play, które nie zawierają żadnego aparatu.

Można również przekierować określone aparaty przy użyciu rozdzielanej średnikami listy interfejsów KSCATEGORY_VIDEO_CAMERA, takich jak `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>Przekierowanie schowka

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowanie schowka:

- `redirectclipboard:i:1` Włącza przekierowywanie Schowka.
- `redirectclipboard:i:0` wyłącza przekierowywanie Schowka.

### <a name="com-port-redirections"></a>Przekierowania portów COM

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowywanie portów COM:

- `redirectcomports:i:1` Włącza przekierowywanie portów COM.
- `redirectcomports:i:0` wyłącza przekierowywanie portów COM.

### <a name="usb-redirection"></a>Przekierowywanie USB

Najpierw Ustaw następującą Właściwość RDP, aby włączyć przekierowywanie urządzeń USB:

- `usbdevicestoredirect:s:*` Włącza przekierowywanie urządzeń USB.
- `usbdevicestoredirect:s:` wyłącza przekierowywanie urządzeń USB.

Następnie ustaw następujące zasady grupy na urządzeniu lokalnym użytkownika:

- Przejdź do **Computer Configuration** węzła  >  **zasady** konfiguracji >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **Podłączanie pulpitu zdalnego**  >  **przekierowania urządzenia USB RemoteFX**.
- Wybierz opcję **Zezwalaj na przekierowywanie protokołu RDP innych obsługiwanych urządzeń USB z funkcją RemoteFX z tego komputera**.
- Wybierz opcję **włączone** , a następnie wybierz opcję **Administratorzy i użytkownicy w polu prawa dostępu przekierowania USB RemoteFX** .
- Wybierz przycisk **OK**.

### <a name="plug-and-play-device-redirection"></a>Przekierowywanie urządzeń Plug and Play

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowywanie urządzeń typu Plug and Play:

- `devicestoredirect:s:*` Włącza przekierowywanie wszystkich urządzeń typu Plug and Play.
- `devicestoredirect:s:` wyłącza przekierowywanie urządzeń typu Plug and Play.

Możesz również wybrać określone urządzenia typu Plug and Play przy użyciu listy rozdzielanej średnikami, na przykład `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Przekierowanie dysku lokalnego

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowywanie dysku lokalnego:

- `drivestoredirect:s:*` Włącza przekierowywanie wszystkich stacji dysków.
- `drivestoredirect:s:` wyłącza przekierowywanie dysku lokalnego.

Możesz również wybrać określone dyski przy użyciu listy rozdzielanej średnikami, na przykład `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>Przekierowanie drukarki

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowanie drukarki:

- `redirectprinters:i:1` Włącza przekierowywanie drukarek.
- `redirectprinters:i:0` wyłącza Przekierowywanie drukarki.

### <a name="smart-card-redirection"></a>Przekierowanie karty inteligentnej

Ustaw następującą Właściwość RDP, aby skonfigurować przekierowywanie kart inteligentnych:

- `redirectsmartcards:i:1` Włącza przekierowywanie kart inteligentnych.
- `redirectsmartcards:i:0` wyłącza przekierowywanie kart inteligentnych.