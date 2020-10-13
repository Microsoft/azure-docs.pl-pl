---
title: Bezpieczny dostęp do hybrydowej usługi Azure AD | Microsoft Docs
description: W tym artykule opisano rozwiązania partnerskie służące do integrowania starszych aplikacji w chmurze lokalnej, publicznej lub prywatnej z usługą Azure AD. Zabezpiecz starsze aplikacje, łącząc kontrolery dostarczania aplikacji lub sieci z usługą Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087096"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Bezpieczny dostęp hybrydowy: Zabezpiecz starsze aplikacje za pomocą Azure Active Directory

Teraz możesz chronić starsze aplikacje do uwierzytelniania lokalnego i w chmurze, łącząc je z usługą Azure Active Directory (AD) z:

- [serwer proxy aplikacji usługi Azure AD platformy Azure](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Twoje istniejące kontrolery dostarczania aplikacji i sieci](#sha-through-networking-and-delivery-controllers)

- [Aplikacje wirtualnej sieci prywatnej (VPN) i Software-Defined obwodu (SDP)](#sha-through-vpn-and-sdp-applications)

Możesz przyciągnąć przerwy i wzmocnić stan zabezpieczeń dla wszystkich aplikacji, korzystając z funkcji usługi Azure AD, takich jak [dostęp warunkowy](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) usługi Azure AD i usługa Azure AD [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Bezpieczny dostęp hybrydowy (SHA) za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD
  
Za pomocą [serwera proxy aplikacji](https://aka.ms/whyappproxy) można zapewnić [bezpieczny dostęp zdalny](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) do lokalnych aplikacji sieci Web. Użytkownicy nie muszą korzystać z sieci VPN. Użytkownicy mogą łatwo łączyć się z aplikacjami z dowolnego urządzenia po [rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). Serwer proxy aplikacji zapewnia dostęp zdalny jako usługę i umożliwia [łatwe publikowanie aplikacji lokalnych](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) użytkownikom spoza sieci firmowej. Ułatwia to skalowanie zarządzania dostępem do chmury bez konieczności modyfikowania aplikacji lokalnych. [Zaplanuj wdrożenie usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) w następnym kroku.

## <a name="azure-ad-partner-integrations"></a>Integracje partnerów usługi Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>Algorytm SHA za poorednictwem sieci i kontrolerów dostarczania

Oprócz [usługi Azure serwer proxy aplikacji usługi Azure AD](https://aka.ms/whyappproxy), aby umożliwić korzystanie z [platformy zaufania zerowej](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), partnerzy firmy Microsoft z dostawcami innych firm. Możesz użyć istniejących kontrolerów sieci i dostarczania oraz łatwo chronić starsze aplikacje, które mają kluczowe znaczenie dla procesów firmy, ale nie możesz chronić przed usługą Azure AD. Prawdopodobnie masz już wszystko, czego potrzebujesz, aby rozpocząć ochronę tych aplikacji.

![Obraz pokazuje bezpieczny dostęp hybrydowy przy użyciu partnerów sieciowych i serwera proxy aplikacji](./media/secure-hybrid-access/secure-hybrid-access.png)

Następujący dostawcy sieci oferują wstępnie skompilowane rozwiązania i szczegółowe wskazówki dotyczące integracji z usługą Azure AD.

- [Akamai dostęp do aplikacji (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Kontroler dostarczania aplikacji Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>Algorytm SHA przez aplikacje sieci VPN i SDP

Korzystając z rozwiązań sieci VPN i SDP, można zapewnić bezpieczny dostęp do sieci przedsiębiorstwa z dowolnego urządzenia, w dowolnym momencie, w dowolnej lokalizacji, chroniąc dane organizacji. Mając usługę Azure AD jako dostawcę tożsamości (dostawcy tożsamości), możesz używać nowoczesnych metod uwierzytelniania i autoryzacji, takich jak [Logowanie](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) jednokrotne usługi Azure AD i [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) , aby zabezpieczyć lokalne starsze aplikacje.  

![Obraz pokazuje bezpieczny dostęp hybrydowy przy użyciu partnerów sieci VPN i serwera proxy aplikacji ](./media/secure-hybrid-access/app-proxy-vpn.png)

Następujący dostawcy sieci VPN i SDP nie oferują wstępnie utworzonych rozwiązań i szczegółowych wskazówek dotyczących integracji z usługą Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Rozwiązania Zscaler prywatny dostęp (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
