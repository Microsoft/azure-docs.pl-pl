---
title: Zabezpieczanie aplikacji internetowych PaaS & mobilnych
titleSuffix: Azure App Service
description: 'Dowiedz się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń usługi Azure App Service dotyczących zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 49b51a5f14f305afa915ec1d0fae42ca9ded6b1e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461671"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Najważniejsze wskazówki dotyczące zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS przy użyciu usługi Azure App Service

W tym artykule omówimy kolekcję najlepszych rozwiązań dotyczących zabezpieczeń [usługi Azure App Service](/azure/app-service/overview) dotyczących zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS. Te najlepsze rozwiązania pochodzą z naszego środowiska z platformą Azure i doświadczeń klientów takich jak Ty.

Usługa Azure App Service to usługa platformy jako usługi (PaaS), która umożliwia tworzenie aplikacji sieci Web i aplikacji mobilnych dla dowolnej platformy lub urządzenia oraz łączenie się z danymi w dowolnym miejscu, w chmurze lub lokalnie. Usługa App Service zawiera funkcje sieci web i urządzeń przenośnych, które były wcześniej dostarczane oddzielnie jako witryny sieci Web platformy Azure i usługi azure mobile. Obejmuje ona także nowe funkcje automatyzacji procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa usługa App Service zapewnia bogaty zestaw funkcji do scenariuszy sieci Web, urządzeń mobilnych i integracji.

## <a name="authenticate-through-azure-active-directory-ad"></a>Uwierzytelnij się za pośrednictwem usługi Azure Active Directory (AD)
Usługa app service zapewnia usługę OAuth 2.0 dla dostawcy tożsamości. OAuth 2.0 koncentruje się na prostocie dewelopera klienta, zapewniając jednocześnie określone przepływy autoryzacji dla aplikacji sieci web, aplikacji komputerowych i telefonów komórkowych. Usługa Azure AD używa usługi OAuth 2.0, aby umożliwić autoryzowanie dostępu do aplikacji mobilnych i sieci Web. Aby dowiedzieć się więcej, zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Ograniczanie dostępu na podstawie roli
Ograniczenie dostępu jest konieczne dla organizacji, które chcą wymusić zasady zabezpieczeń dostępu do danych. Za pomocą kontroli dostępu opartej na rolach (RBAC) można przypisać uprawnienia użytkownikom, grupom i aplikacjom w określonym zakresie, na przykład zasadami zabezpieczeń o konieczności poznania i najmniejszych uprawnień. Aby dowiedzieć się więcej o przyznawaniu użytkownikom dostępu do aplikacji, zobacz [Co to jest kontrola dostępu oparta na rolach](/azure/role-based-access-control/overview).

## <a name="protect-your-keys"></a>Chroń swoje klucze
Nie ma znaczenia, jak dobre jest twoje bezpieczeństwo, jeśli utracisz klucze subskrypcji. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą usługi Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, . PFX i hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Za pomocą usługi Key Vault można również zarządzać certyfikatami TLS za pomocą automatycznego odnawiania. Zobacz [Co to jest usługa Azure Key Vault,](../../key-vault/general/overview.md) aby dowiedzieć się więcej.

## <a name="restrict-incoming-source-ip-addresses"></a>Ograniczanie przychodzących źródłowych adresów IP
[Środowiska usługi app service](../../app-service/environment/intro.md) ma funkcję integracji sieci wirtualnej, która pomaga ograniczyć przychodzące źródłowe adresy IP za pośrednictwem sieciowych grup zabezpieczeń (NSG). Jeśli nie znasz sieci wirtualnych platformy Azure (VNETs), jest to funkcja, która umożliwia umieszczenie wielu zasobów platformy Azure w sieci niekonkingowej, routable, do której można kontrolować dostęp. Aby dowiedzieć się więcej, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../../app-service/web-sites-integrate-with-vnet.md).

W usłudze App Service w systemie Windows można również dynamicznie ograniczać adresy IP, konfigurując plik web.config. Aby uzyskać więcej informacji, zobacz [Dynamiczne zabezpieczenia IP](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono kolekcję najlepszych rozwiązań dotyczących zabezpieczeń usługi App Service dotyczących zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS. Aby dowiedzieć się więcej o zabezpieczaniu wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](paas-deployments.md)
- [Zabezpieczanie baz danych PaaS na platformie Azure](paas-applications-using-sql.md)
