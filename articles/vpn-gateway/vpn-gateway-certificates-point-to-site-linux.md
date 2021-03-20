---
title: 'Generowanie i eksportowanie certyfikatów dla punktu do lokacji: Linux: CLI'
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i Wygeneruj certyfikaty klienta przy użyciu interfejsu wiersza polecenia systemu Linux (klient strongswan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: alzam
ms.openlocfilehash: 0be5bb042649b0fe425077b5b3feb3cea728218c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89394010"
---
# <a name="generate-and-export-certificates"></a>Generowanie i eksportowanie certyfikatów

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu interfejsu wiersza polecenia systemu Linux i klient strongswan. Jeśli szukasz różnych instrukcji dotyczących certyfikatów, zobacz artykuły dotyczące [programu PowerShell](vpn-gateway-certificates-point-to-site.md) lub [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) . Aby uzyskać informacje na temat sposobu instalowania klient strongswan przy użyciu graficznego interfejsu użytkownika zamiast interfejsu wiersza polecenia, zobacz kroki opisane w artykule dotyczącym [konfiguracji klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>Zainstaluj klient strongswan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generowanie i eksportowanie certyfikatów

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj konfigurację typu punkt-lokacja, aby [tworzyć i instalować pliki konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
