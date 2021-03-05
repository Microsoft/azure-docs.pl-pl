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
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ed37b914c352d7162db7f8409ac09b7615da47
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174431"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Bezpieczny dostęp hybrydowy: Zabezpiecz starsze aplikacje za pomocą Azure Active Directory

Teraz możesz chronić starsze aplikacje do uwierzytelniania lokalnego i w chmurze, łącząc je z usługą Azure Active Directory (AD) z:

- [serwer proxy aplikacji usługi Azure AD platformy Azure](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Twoje istniejące kontrolery dostarczania aplikacji i sieci](#sha-through-networking-and-delivery-controllers)

- [Aplikacje wirtualnej sieci prywatnej (VPN) i Software-Defined obwodu (SDP)](#sha-through-vpn-and-sdp-applications)

Możesz przyciągnąć przerwy i wzmocnić stan zabezpieczeń dla wszystkich aplikacji, korzystając z funkcji usługi Azure AD, takich jak [dostęp warunkowy](../conditional-access/overview.md) usługi Azure AD i usługa Azure AD [Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Bezpieczny dostęp hybrydowy (SHA) za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD
  
Za pomocą [serwera proxy aplikacji](./what-is-application-proxy.md) można zapewnić [bezpieczny dostęp zdalny](./application-proxy.md) do lokalnych aplikacji sieci Web. Użytkownicy nie muszą korzystać z sieci VPN. Użytkownicy mogą łatwo łączyć się z aplikacjami z dowolnego urządzenia po [rejestracji jednokrotnej](./add-application-portal-setup-sso.md). Serwer proxy aplikacji zapewnia dostęp zdalny jako usługę i umożliwia [łatwe publikowanie aplikacji lokalnych](./application-proxy-add-on-premises-application.md) użytkownikom spoza sieci firmowej. Ułatwia to skalowanie zarządzania dostępem do chmury bez konieczności modyfikowania aplikacji lokalnych. [Zaplanuj wdrożenie usługi Azure serwer proxy aplikacji usługi Azure AD](./application-proxy-deployment-plan.md) w następnym kroku.

## <a name="azure-ad-partner-integrations"></a>Integracje partnerów usługi Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>Algorytm SHA za poorednictwem sieci i kontrolerów dostarczania

Oprócz [usługi Azure serwer proxy aplikacji usługi Azure AD](./what-is-application-proxy.md), aby umożliwić korzystanie z [platformy zaufania zerowej](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), partnerzy firmy Microsoft z dostawcami innych firm. Możesz użyć istniejących kontrolerów sieci i dostarczania oraz łatwo chronić starsze aplikacje, które mają kluczowe znaczenie dla procesów firmy, ale nie możesz chronić przed usługą Azure AD. Prawdopodobnie masz już wszystko, czego potrzebujesz, aby rozpocząć ochronę tych aplikacji.

![Obraz pokazuje bezpieczny dostęp hybrydowy przy użyciu partnerów sieciowych i serwera proxy aplikacji](./media/secure-hybrid-access/secure-hybrid-access.png)

Następujący dostawcy sieci oferują wstępnie skompilowane rozwiązania i szczegółowe wskazówki dotyczące integracji z usługą Azure AD.

- [Akamai dostęp do aplikacji (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Kontroler dostarczania aplikacji Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>Algorytm SHA przez aplikacje sieci VPN i SDP

Korzystając z rozwiązań sieci VPN i SDP, można zapewnić bezpieczny dostęp do sieci przedsiębiorstwa z dowolnego urządzenia, w dowolnym momencie, w dowolnej lokalizacji, chroniąc dane organizacji. Mając usługę Azure AD jako dostawcę tożsamości (dostawcy tożsamości), możesz używać nowoczesnych metod uwierzytelniania i autoryzacji, takich jak [Logowanie](./what-is-single-sign-on.md) jednokrotne usługi Azure AD i [uwierzytelnianie wieloskładnikowe](../authentication/concept-mfa-howitworks.md) , aby zabezpieczyć lokalne starsze aplikacje.  

![Obraz pokazuje bezpieczny dostęp hybrydowy przy użyciu partnerów sieci VPN i serwera proxy aplikacji ](./media/secure-hybrid-access/app-proxy-vpn.png)

Następujący dostawcy sieci VPN oferują wstępnie skompilowane rozwiązania i szczegółowe wskazówki dotyczące integracji z usługą Azure AD.

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Secure (komputery)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

Następujący dostawcy SDP zawierają wstępnie skompilowane rozwiązania i szczegółowe wskazówki dotyczące integracji z usługą Azure AD.

- [Broker dostępu Datawiza](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial#:~:text=For%20SSO%20to%20work,%20you%20need%20to%20establish,to%20test%20Azure%20AD%20single%20sign-on%20with%20B.Simon.)

- [Platforma uwierzytelniania Silverfort](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
