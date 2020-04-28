---
title: Jak otworzyć porty zapory wymagane dla aplikacji serwera proxy aplikacji
description: Dowiedz się, które porty należy otworzyć, aby usługa Azure serwer proxy aplikacji usługi Azure AD działała poprawnie
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275550"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Jak otworzyć porty zapory wymagane dla aplikacji serwera proxy aplikacji

Aby zapoznać się z pełną listą wymaganych portów i funkcji poszczególnych portów, zobacz sekcję wymagania wstępne w [dokumentacji serwera proxy aplikacji](application-proxy-add-on-premises-application.md). Należy zauważyć, że serwer proxy aplikacji korzysta tylko z portów wychodzących.

Możesz również sprawdzić, czy masz otwarte wszystkie wymagane porty, otwierając [Narzędzie Test Ports łącznika](https://aadap-portcheck.connectorporttest.msappproxy.net/) z sieci lokalnej. Więcej zielony znacznik wyboru oznacza większą odporność. 

## <a name="app-proxy-regions"></a>Regiony serwera proxy aplikacji

Pracujemy nad sposobem, aby wiedzieć, który z tych regionów musi być zielony. Na razie upewnij się, że wszystkie są. Środkowe stany USA są również wymagane niezależnie od regionu, w którym się znajdują.

Aby upewnić się, że narzędzie daje odpowiednie wyniki, upewnij się, że:

-   Otwórz narzędzie w przeglądarce z serwera, na którym został zainstalowany łącznik.

-   Upewnij się, że wszystkie serwery proxy lub zapory odpowiednie dla Twojego łącznika są również stosowane na tej stronie. Można to zrobić w programie Internet Explorer, przechodząc do **ustawień**  - &gt; **Opcje**  - &gt; internetowe **połączenia**  - &gt; **sieci LAN**. Na tej stronie zobaczysz pole "Użyj serwera proxy dla sieci LAN". Zaznacz to pole i umieść adres serwera proxy w polu "Address" (adres).

## <a name="next-steps"></a>Następne kroki
[Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
