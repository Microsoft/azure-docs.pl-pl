---
title: 'Generowanie i eksportowanie certyfikatów dla punktu do lokacji: Linux: CLI'
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i Wygeneruj certyfikaty klienta przy użyciu interfejsu wiersza polecenia systemu Linux (klient strongswan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: c55c304ae71cba396266c83bcaaa727351dcd677
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87064633"
---
# <a name="generate-and-export-certificates"></a>Generowanie i eksportowanie certyfikatów

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu interfejsu wiersza polecenia systemu Linux i klient strongswan. Jeśli szukasz różnych instrukcji dotyczących certyfikatów, zobacz artykuły dotyczące [programu PowerShell](vpn-gateway-certificates-point-to-site.md) lub [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) . Aby uzyskać informacje na temat sposobu instalowania klient strongswan przy użyciu graficznego interfejsu użytkownika zamiast interfejsu wiersza polecenia, zobacz kroki opisane w artykule dotyczącym [konfiguracji klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>Zainstaluj klient strongswan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generowanie i eksportowanie certyfikatów

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj konfigurację typu punkt-lokacja, aby [tworzyć i instalować pliki konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
