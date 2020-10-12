---
title: Jak działa Logowanie jednokrotne do zasobów lokalnych na urządzeniach przyłączonych do usługi Azure AD | Microsoft Docs
description: Dowiedz się, jak rozłożyć obsługę logowania jednokrotnego, konfigurując hybrydowe urządzenia dołączone Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba802cb86d68298cd4dfff94162069590744833c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91256466"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Jak działa logowanie jednokrotne do zasobów lokalnych na urządzeniach dołączonych do usługi Azure AD

Prawdopodobnie nie jest to nieoczekiwane, że urządzenie dołączone do usługi Azure Active Directory (Azure AD) udostępnia Logowanie jednokrotne do aplikacji w chmurze Twojej dzierżawy. Jeśli środowisko ma Active Directory lokalnego (AD), możesz je rozłożyć na te urządzenia do zasobów i aplikacji, które są zależne od lokalnych usług AD. 

W tym artykule wyjaśniono, jak to działa.

## <a name="prerequisites"></a>Wymagania wstępne

 Jeśli maszyny przyłączone do usługi Azure AD nie są połączone z siecią organizacji, wymagana jest sieć VPN lub inna infrastruktura sieciowa. Lokalne Logowanie jednokrotne wymaga komunikacji liniowej z lokalnymi kontrolerami domeny AD DS.

## <a name="how-it-works"></a>Jak to działa 

W przypadku urządzenia dołączonego do usługi Azure AD użytkownicy mają już środowisko logowania jednokrotnego w aplikacjach w chmurze w Twoim środowisku. Jeśli środowisko ma usługę Azure AD i lokalną usługi AD, możesz rozszerzyć zakres środowiska logowania jednokrotnego do lokalnych aplikacji biznesowych, udziałów plików i drukarek.

Urządzenia przyłączone do usługi Azure AD nie mają znajomości lokalnego środowiska usługi AD, ponieważ nie są do niego dołączone. Można jednak podać dodatkowe informacje o lokalnej usłudze AD na tych urządzeniach przy użyciu Azure AD Connect.

Środowisko, które ma zarówno usługi Azure AD, jak i lokalna usługa AD, jest również znane w środowisku hybrydowym. Jeśli masz środowisko hybrydowe, prawdopodobnie masz już Azure AD Connect wdrożone w celu zsynchronizowania informacji o tożsamości lokalnej z chmurą. W ramach procesu synchronizacji Azure AD Connect synchronizuje informacje o użytkowniku lokalnym z usługą Azure AD. Gdy użytkownik loguje się do urządzenia dołączonego do usługi Azure AD w środowisku hybrydowym:

1. Usługa Azure AD wysyła szczegóły domeny lokalnej użytkownika z powrotem do urządzenia wraz z [podstawowym tokenem odświeżania](concept-primary-refresh-token.md)
1. Usługa urząd zabezpieczeń lokalnych (LSA) umożliwia uwierzytelnianie Kerberos i NTLM na urządzeniu.

Podczas próby dostępu do zasobu żądającego protokołu Kerberos lub NTLM w środowisku lokalnym użytkownika urządzenie:

1. Wysyła informacje o domenie lokalnej i poświadczenia użytkownika do zlokalizowanego kontrolera domeny w celu uzyskania uwierzytelnienia użytkownika.
1. Odbiera [bilet uprawniający do przyznania biletu protokołu Kerberos (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) lub token NTLM oparty na protokole obsługiwanym przez lokalny zasób lub aplikację. Jeśli próba pobrania tokenu TGT protokołu Kerberos lub NTLM dla domeny nie powiedzie się (pokrewny limit czasu DCLocator może spowodować opóźnienie), podejmowane są próby wprowadzenia wpisów Menedżera poświadczeń lub użytkownik może otrzymać wyskakujące uwierzytelnienie żądające poświadczeń dla zasobu docelowego.

Wszystkie aplikacje, które są skonfigurowane pod kątem **uwierzytelniania zintegrowanego systemu Windows** , bezproblemowo otrzymują Logowanie jednokrotne, gdy użytkownik próbuje uzyskać do niego dostęp.

Funkcja Windows Hello dla firm wymaga dodatkowej konfiguracji umożliwiającej lokalne Logowanie jednokrotne z urządzenia dołączonego do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzeń przyłączonych do usługi Azure AD dla lokalnego Single-Sign przy użyciu funkcji Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Co otrzymujemy

Logowanie jednokrotne na urządzeniu dołączonym do usługi Azure AD można: 

- Dostęp do ścieżki UNC na serwerze członkowskim usługi AD
- Dostęp do serwera sieci Web członków usługi AD skonfigurowany pod kątem zabezpieczeń zintegrowanych z systemem Windows 

Jeśli chcesz zarządzać lokalną usługą AD z urządzenia z systemem Windows, zainstaluj [Narzędzia administracji zdalnej serwera dla systemu Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Możesz użyć:

- Przystawka Active Directory Użytkownicy i komputery (ADUC) do administrowania wszystkimi obiektami usługi AD. Należy jednak określić domenę, z którą chcesz nawiązać połączenie ręcznie.
- Przystawka DHCP do administrowania serwerem DHCP przyłączonym do usługi AD. Może jednak być konieczne określenie nazwy lub adresu serwera DHCP.
 
## <a name="what-you-should-know"></a>Co należy wiedzieć

Może być konieczne dostosowanie [filtrowania opartego na domenie](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) w Azure AD Connect, aby upewnić się, że dane dotyczące wymaganych domen są synchronizowane.

Aplikacje i zasoby, które są zależne od uwierzytelniania Active Directory komputera, nie działają, ponieważ urządzenia przyłączone do usługi Azure AD nie mają obiektu komputera w usłudze AD. 

Nie można udostępniać plików innym użytkownikom na urządzeniu przyłączonym do usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanie urządzeniami w Azure Active Directory?](overview.md) 
