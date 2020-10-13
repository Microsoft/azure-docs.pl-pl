---
title: plik dołączania
description: plik dołączania
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812708"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Pobierz i zainstaluj klienta OpenVPN z oficjalnej witryny internetowej.
1. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie konfiguracje sieci VPN użytkownika w Azure Portal lub New-AzureRmVpnClientConfiguration w programie PowerShell.
1. Rozpakuj profil. Otwórz plik konfiguracji vpnconfig.ovpn z folderu OpenVPN w programie Notatnik.
1. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W formacie certyfikatu PEM można otworzyć plik. cer i skopiować go za pomocą klucza Base64 między nagłówkami certyfikatów. Aby uzyskać instrukcje, zobacz [Eksportowanie certyfikatu w celu uzyskania zakodowanego klucza publicznego.](../articles/virtual-wan/certificates-point-to-site.md)
1. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Aby uzyskać instrukcje, zobacz [Jak wyodrębnić klucz prywatny.](../articles/virtual-wan/howto-openvpn-clients.md#windows)
1. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
1. Skopiuj plik vpnconfig.ovpn do folderu C:\Program Files\OpenVPN\config.
1. Kliknij prawym przyciskiem myszy ikonę OpenVPN na pasku zadań i wybierz pozycję **Połącz**.

##### <a name="ikev2"></a>IKEv2

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”.
1. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zobaczysz okno podręczne SmartScreen, wybierz pozycję **więcej informacji**, a następnie **Uruchom mimo wszystko**.
1. Na komputerze klienckim przejdź do **ustawień sieci** i wybierz pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.
1. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure. Więcej informacji o generowaniu certyfikatów znajduje się w temacie [Generate Certificates](../articles/virtual-wan/certificates-point-to-site.md). Informacje o sposobie instalowania certyfikatu klienta znajdują się w temacie [Install a Client Certificate (Instalowanie certyfikatu klienta](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)).
