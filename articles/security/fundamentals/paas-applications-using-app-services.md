---
title: Zabezpieczanie aplikacji PaaS Web & Mobile
titleSuffix: Azure App Service
description: 'Dowiedz się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń Azure App Service w zakresie zabezpieczania aplikacji internetowych i mobilnych PaaS. '
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
ms.openlocfilehash: 849743dd756eff27ec0670cc39d2419c60538183
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701018"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Najlepsze rozwiązania dotyczące zabezpieczania aplikacji PaaS Web i Mobile przy użyciu Azure App Service

W tym artykule omówiono zbieranie [Azure App Service](../../app-service/overview.md) najlepszych rozwiązań z zakresu bezpieczeństwa związanych z zabezpieczaniem aplikacji internetowych i mobilnych PaaS. Te najlepsze rozwiązania wynikają z naszych rozwiązań związanych z platformą Azure i klientami.

Azure App Service to oferta typu "platforma jako usługa" (PaaS), która umożliwia tworzenie aplikacji sieci Web i mobilnych dla dowolnej platformy lub urządzenia oraz łączenie się z danymi gdziekolwiek w chmurze lub lokalnie. App Service obejmuje możliwości sieci Web i urządzeń przenośnych, które zostały wcześniej dostarczone osobno jako usługi Azure Websites i Azure Mobile Services. Obejmuje ona także nowe funkcje automatyzacji procesów biznesowych i hostowania interfejsów API w chmurze. Jako pojedyncza zintegrowana usługa App Service oferuje bogaty zestaw funkcji dla scenariuszy sieci Web, mobilnych i integracji.

## <a name="authenticate-through-azure-active-directory-ad"></a>Uwierzytelnianie za Azure Active Directory (AD)
App Service udostępnia usługę OAuth 2,0 dla dostawcy tożsamości. Uwierzytelnianie OAuth 2,0 koncentruje się na prostotie deweloperów klienta, a jednocześnie udostępnia określone przepływy autoryzacji dla aplikacji sieci Web, aplikacji klasycznych i telefonów komórkowych. Usługa Azure AD korzysta z protokołu OAuth 2,0, aby umożliwić autoryzowanie dostępu do aplikacji mobilnych i sieci Web. Aby dowiedzieć się więcej, zobacz temat [uwierzytelnianie i autoryzacja w Azure App Service](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Ograniczanie dostępu na podstawie roli
Ograniczanie dostępu jest przeznaczone dla organizacji, które chcą wymusić zasady zabezpieczeń na potrzeby dostępu do danych. Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można przypisywać uprawnienia użytkownikom, grupom i aplikacjom w określonym zakresie, takich jak konieczność znajomości i najniższych zasad zabezpieczeń. Aby dowiedzieć się więcej o udzielaniu użytkownikom dostępu do aplikacji, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Ochrona kluczy
Nie ma znaczenia, jak dobre zabezpieczenia to utrata kluczy subskrypcji. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych). Pliki i hasła PFX) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Za pomocą Key Vault można także zarządzać certyfikatami TLS przy użyciu autoodnawiania. Aby dowiedzieć się więcej, zobacz [co to jest Azure Key Vault](../../key-vault/general/overview.md) .

## <a name="restrict-incoming-source-ip-addresses"></a>Ogranicz przychodzące źródłowe adresy IP
[Środowiska App Service](../../app-service/environment/intro.md) zawierają funkcję integracji sieci wirtualnej, która pomaga ograniczyć przychodzące źródłowe adresy IP za pomocą sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Jeśli nie znasz usługi Azure Virtual Networks (sieci wirtualnych), jest to funkcja, która umożliwia umieszczanie wielu zasobów platformy Azure w nieinternetowej, rutowanej sieci, do której można kontrolować dostęp. Aby dowiedzieć się więcej, zobacz [Integrowanie aplikacji z usługą Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).

Aby uzyskać App Service w systemie Windows, można również dynamicznie ograniczyć adresy IP, konfigurując web.config. Aby uzyskać więcej informacji, zobacz [dynamiczne zabezpieczenia adresów IP](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzono do App Service kolekcji najlepszych rozwiązań dotyczących zabezpieczeń w zakresie zabezpieczania aplikacji PaaS Web i Mobile. Aby dowiedzieć się więcej o zabezpieczaniu wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](paas-deployments.md)
- [Zabezpieczanie baz danych PaaS na platformie Azure](paas-applications-using-sql.md)