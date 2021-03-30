---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "67183055"
---
>[!NOTE]
>Od 1 lipca 2018 r. z usługi Azure VPN Gateway zostanie usunięta obsługa techniczna protokołów TLS 1.0 i 1.1. Usługa VPN Gateway będzie obsługiwać tylko protokół TLS 1.2. Aby zachować pomoc techniczną, zobacz [aktualizacje umożliwiające włączenie obsługi protokołu TLS 1.2](#tls1).

Ponadto następujące starsze algorytmy będą również przestarzałe w przypadku protokołu TLS 1 lipca 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Jak mogę włączyć obsługę protokołu TLS 1,2 w systemach Windows 7 i Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
