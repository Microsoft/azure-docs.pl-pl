---
title: 'Generowanie i eksportowanie certyfikatów dla P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i Wygeneruj certyfikaty klienta dla P2S przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: c1c69b301199b054fe6b1ef42cfcf7878a7a161c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306688"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generowanie i eksportowanie certyfikatów dla połączeń punkt-lokacja przy użyciu programu PowerShell

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016. Jeśli szukasz różnych instrukcji dotyczących certyfikatów, zobacz [Certificates-Linux](vpn-gateway-certificates-point-to-site-linux.md) lub [Certificates-MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Kroki opisane w tym artykule należy wykonać na komputerze z systemem Windows 10 lub Windows Server 2016. Polecenia cmdlet programu PowerShell używane do generowania certyfikatów są częścią systemu operacyjnego i nie działają w innych wersjach systemu Windows. Komputer z systemem Windows 10 lub Windows Server 2016 jest wymagany tylko do wygenerowania certyfikatów. Po wygenerowaniu certyfikatów można je przekazać lub zainstalować w dowolnym obsługiwanym systemie operacyjnym klienta.

Jeśli nie masz dostępu do komputera z systemem Windows 10 lub Windows Server 2016, możesz użyć [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) do wygenerowania certyfikatów. Certyfikaty generowane przy użyciu jednej z tych metod można zainstalować w dowolnym [obsługiwanym](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) systemie operacyjnym klienta.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalowanie wyeksportowanego certyfikatu klienta

Każdy klient, który nawiązuje połączenie z siecią wirtualną za pośrednictwem połączenia P2S, wymaga, aby certyfikat klienta został zainstalowany lokalnie.

Aby zainstalować certyfikat klienta, zobacz [Instalowanie certyfikatu klienta dla połączeń punkt-lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Następne kroki

Kontynuuj pracę z konfiguracją punkt-lokacja.

* Aby uzyskać **Menedżer zasobów** kroki dotyczące modelu wdrażania, zobacz [Konfigurowanie P2S przy użyciu natywnego uwierzytelniania certyfikatu platformy Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Kroki **klasycznego** modelu wdrażania można znaleźć w temacie [Konfigurowanie połączenia sieci VPN typu punkt-lokacja z siecią wirtualną (klasyczną)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).