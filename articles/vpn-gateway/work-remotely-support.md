---
title: 'Zdalna współpraca z P2S: Azure VPN Gateway'
description: Na tej stronie opisano, jak można wykorzystać VPN Gateway, aby umożliwić pracę zdalną z powodu COVID-19 Pandemic.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880088"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Zdalna współpraca przy użyciu usługi Azure VPN Gateway Point-to-site

>[!NOTE]
>W tym artykule opisano, jak korzystać z platformy Azure VPN Gateway, platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do zdalnego działania i rozwiązywania problemów z siecią, które są dostępne z powodu kryzysu COVID-19.
>

W tym artykule opisano opcje, które są dostępne dla organizacji w celu skonfigurowania dostępu zdalnego dla użytkowników lub uzupełniania istniejących rozwiązań o dodatkową pojemność podczas epidemii COVID-19.

Rozwiązanie Azure Point-to-site jest oparte na chmurze i może zostać szybko zainicjowane w celu zaspokojenia zwiększonych wymagań użytkowników do pracy z domu. Można łatwo skalować ją w górę i szybciej, gdy zwiększona pojemność nie jest już potrzebne.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Informacje o sieci VPN typu punkt-lokacja

Połączenie bramy VPN Gateway typu punkt-lokacja pozwala utworzyć bezpieczne połączenie z siecią wirtualną z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne w przypadku osób, które chcą łączyć się z usługą Azure sieci wirtualnych lub lokalnymi centrami danych z lokalizacji zdalnej, np. z domu lub z konferencji. W tym artykule opisano, jak umożliwić użytkownikom zdalne korzystanie z różnych scenariuszy.

W poniższej tabeli przedstawiono systemy operacyjne klienta i dostępne dla nich opcje uwierzytelniania. Warto wybrać metodę uwierzytelniania na podstawie systemu operacyjnego klienta, który jest już używany. Na przykład wybierz pozycję OpenVPN z uwierzytelnianiem opartym na certyfikatach, jeśli masz kombinację systemów operacyjnych klienta, które muszą nawiązać połączenie. Należy również pamiętać, że sieć VPN typu punkt-lokacja jest obsługiwana tylko na bramach sieci VPN opartych na trasach.

![Zrzut ekranu przedstawiający klienckie systemy operacyjne i dostępne opcje uwierzytelniania.](./media/working-remotely-support/ostable.png "System operacyjny")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenariusz 1 — użytkownicy potrzebują dostępu tylko do zasobów na platformie Azure

W tym scenariuszu użytkownicy zdalni muszą tylko uzyskać dostęp do zasobów znajdujących się na platformie Azure.

![Diagram pokazujący scenariusz punkt-lokacja dla użytkowników, którzy potrzebują dostępu do zasobów tylko na platformie Azure.](./media/working-remotely-support/scenario1.png "Scenariusz 1")

Na wysokim poziomie następujące kroki są niezbędne, aby umożliwić użytkownikom bezpieczne łączenie z zasobami platformy Azure:

1. Utwórz bramę sieci wirtualnej (jeśli taka nie istnieje).
2. Skonfiguruj sieć VPN typu punkt-lokacja na bramie.

   * W celu uwierzytelnienia certyfikatu Użyj [tego linku](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * W przypadku OpenVPN Użyj [tego linku](vpn-gateway-howto-openvpn.md).
   * W przypadku uwierzytelniania usługi Azure AD Użyj [tego linku](openvpn-azure-ad-tenant.md).
   * W przypadku rozwiązywania problemów dotyczących połączeń punkt-lokacja Użyj [tego linku](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Pobierz i Dystrybuuj konfigurację klienta sieci VPN.
4. Dystrybuuj certyfikaty (w przypadku wybrania uwierzytelniania certyfikatu) do klientów.
5. Nawiązywanie połączenia z siecią VPN platformy Azure.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenariusz 2 — użytkownicy potrzebują dostępu do zasobów na platformie Azure i/lub zasobach Premium

W tym scenariuszu użytkownicy zdalni muszą uzyskać dostęp do zasobów znajdujących się na platformie Azure i w lokalnych centrach danych.

![Diagram pokazujący scenariusz punkt-lokacja dla użytkowników, którzy potrzebują dostępu do zasobów na platformie Azure.](./media/working-remotely-support/scenario2.png "Scenariusz 2")

Na wysokim poziomie następujące kroki są niezbędne, aby umożliwić użytkownikom bezpieczne łączenie z zasobami platformy Azure:

1. Utwórz bramę sieci wirtualnej (jeśli taka nie istnieje).
2. Skonfiguruj sieć VPN typu punkt-lokacja na bramie (patrz [scenariusz 1](#scenario1)).
3. Skonfiguruj tunel typu lokacja-lokacja w bramie sieci wirtualnej platformy Azure z włączonym protokołem BGP.
4. Skonfiguruj urządzenie lokalne w celu nawiązania połączenia z bramą sieci wirtualnej platformy Azure.
5. Pobierz profil punkt-lokacja z Azure Portal i Dystrybuuj do klientów

Aby dowiedzieć się, jak skonfigurować tunel VPN typu lokacja-lokacja, zobacz [ten link](./tutorial-site-to-site-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Często zadawane pytania dotyczące natywnego uwierzytelniania certyfikatu platformy Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Często zadawane pytania dotyczące uwierzytelniania usługi RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie połączenia P2S — uwierzytelnianie w usłudze Azure AD](openvpn-azure-ad-tenant.md)

* [Konfigurowanie połączenia P2S — uwierzytelnianie usługi RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — natywne uwierzytelnianie certyfikatu platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**