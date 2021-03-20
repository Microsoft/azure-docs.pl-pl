---
title: 'VPN Gateway platformy Azure: Instalowanie certyfikatu klienta typu punkt-lokacja'
description: Zainstaluj certyfikat klienta na potrzeby uwierzytelniania certyfikatów P2S — Windows, Mac i Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 8c4d37f67d89ad1b7973a85fb6c2a9f7e818a028
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89421666"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalowanie certyfikatów klienta dla połączeń uwierzytelniania certyfikatu P2S

Wszyscy klienci łączący się z siecią wirtualną przy użyciu uwierzytelniania certyfikatu platformy Azure typu punkt-lokacja wymagają certyfikatu klienta. Ten artykuł pomaga zainstalować certyfikat klienta używany do uwierzytelniania podczas łączenia się z siecią wirtualną przy użyciu usługi P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Pozyskiwanie certyfikatu klienta

Niezależnie od systemu operacyjnego klienta, z którego chcesz się połączyć, musisz mieć certyfikat klienta. Można wygenerować certyfikat klienta na podstawie certyfikatu głównego wygenerowanego przy użyciu rozwiązania urzędu certyfikacji przedsiębiorstwa lub certyfikatu głównego z podpisem własnym. Zapoznaj się z instrukcjami dla programu [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)lub [Linux](vpn-gateway-certificates-point-to-site-linux.md) , aby poznać procedurę generowania certyfikatu klienta. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Komputery

>[!NOTE]
>Klienci sieci VPN Mac są obsługiwani tylko w ramach modelu wdrażania Menedżer zasobów. Nie są one obsługiwane dla klasycznego modelu wdrażania.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>System

Certyfikat klienta systemu Linux jest instalowany na kliencie w ramach konfiguracji klienta. Aby uzyskać instrukcje, zobacz [Konfiguracja klienta — Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) .

## <a name="next-steps"></a>Następne kroki

Wykonaj kroki konfiguracji punkt-lokacja, aby [utworzyć i zainstalować pliki konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md).
