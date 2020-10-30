---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041579"
---
Uzyskaj plik CER dla certyfikatu głównego. Można użyć certyfikatu głównego wygenerowanego w ramach rozwiązania Enterprise (zalecane) lub wygenerować certyfikat z podpisem własnym. Po utworzeniu certyfikatu głównego wyeksportuj dane certyfikatu publicznego (nie klucz prywatny) jako plik cer X.509 z kodowaniem Base64. Ten plik zostanie przekazany później do platformy Azure.

* **Certyfikat przedsiębiorstwa:** Jeśli używasz rozwiązania Enterprise, możesz użyć istniejącego łańcucha certyfikatów. Uzyskaj plik cer dla certyfikatu głównego, którego chcesz użyć.
* **Certyfikat główny z podpisem własnym:** Jeśli nie używasz rozwiązania z certyfikatem przedsiębiorstwa, Utwórz certyfikat główny z podpisem własnym. W przeciwnym razie utworzone przez Ciebie certyfikaty nie będą zgodne z połączeniami punkt-lokacja i będzie wyświetlany błąd połączenia podczas próby nawiązania połączenia. Możesz użyć programu Azure PowerShell, MakeCert lub protokołu OpenSSL. W poniższych artykułach opisano sposób generowania zgodnego certyfikatu głównego z podpisem własnym:

  * [Instrukcje programu Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): w przypadku tych instrukcji do generowania certyfikatów wymagany jest system Windows 10 i program PowerShell. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Użyj MakeCert, jeśli nie masz dostępu do komputera z systemem Windows 10 w celu wygenerowania certyfikatów. Mimo iż narzędzie MakeCert jest przestarzałe, przy jego użyciu można nadal generować certyfikaty. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje dotyczące systemu Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).