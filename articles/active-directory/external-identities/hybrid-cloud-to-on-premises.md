---
title: Przyznaj użytkownikom B2B dostęp do aplikacji lokalnych — Azure AD
description: Pokazuje, w jaki sposób udostępnić użytkownikom usługi Cloud B2B dostęp do aplikacji lokalnych przy użyciu funkcji współpracy B2B w usłudze Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 132711b4a3fc584261cd577b4b8f1d4fb13da09d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819381"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Przyznaj użytkownikom B2B dostęp do aplikacji lokalnych w usłudze Azure AD

Jako organizacja, która używa funkcji współpracy B2B Azure Active Directory (Azure AD), aby zapraszać użytkowników-Gości od organizacji partnerskich do usługi Azure AD, możesz teraz udostępnić tym użytkownikom B2B dostęp do aplikacji lokalnych. Te aplikacje lokalne mogą korzystać z uwierzytelniania opartego na języku SAML lub zintegrowanego uwierzytelniania systemu Windows (IWA) przy użyciu ograniczonego delegowania protokołu Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Dostęp do aplikacji SAML

Jeśli aplikacja lokalna korzysta z uwierzytelniania opartego na protokole SAML, możesz łatwo udostępnić te aplikacje użytkownikom współpracy B2B usługi Azure AD za pośrednictwem Azure Portal.

Należy wykonać obie następujące czynności:

- Zintegruj aplikację przy użyciu protokołu SAML zgodnie z opisem w temacie Konfigurowanie logowania jednokrotnego [opartego na protokole SAML](../manage-apps/configure-saml-single-sign-on.md). Zwróć uwagę na to, co jest używane dla wartości **adresu URL logowania** .
-  Użyj usługi Azure serwer proxy aplikacji usługi Azure AD, aby opublikować aplikację lokalną z **Azure Active Directory** skonfigurowanym jako źródło uwierzytelniania. Aby uzyskać instrukcje, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Podczas konfigurowania ustawienia **wewnętrznego adresu URL** należy użyć adresu URL logowania określonego w szablonie aplikacji spoza galerii. Dzięki temu użytkownicy będą mogli uzyskać dostęp do aplikacji spoza granicy organizacji. Serwer proxy aplikacji wykonuje Logowanie jednokrotne SAML dla aplikacji lokalnej.
 
   ![Pokazuje wewnętrzny adres URL i uwierzytelnianie lokalnych ustawień aplikacji](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Dostęp do aplikacji IWA i KCD

Aby zapewnić użytkownikom B2B dostęp do aplikacji lokalnych zabezpieczonych przy użyciu zintegrowanego uwierzytelniania systemu Windows i ograniczonego delegowania protokołu Kerberos, potrzebne są następujące składniki:

- **Uwierzytelnianie za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD**. Użytkownicy B2B muszą mieć możliwość uwierzytelnienia w aplikacji lokalnej. W tym celu należy opublikować aplikację lokalną za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z serwerem proxy aplikacji i Instalowanie łącznika](../manage-apps/application-proxy-enable.md) oraz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autoryzacja za pośrednictwem obiektu użytkownika B2B w katalogu lokalnym**. Aplikacja musi być w stanie wykonywać sprawdzenia dostępu użytkowników i udzielić dostępu do odpowiednich zasobów. IWA i KCD wymagają obiektu użytkownika w lokalnym systemie Windows Server Active Directory, aby ukończyć tę autoryzację. Zgodnie z opisem w temacie [jak działa Logowanie jednokrotne za pomocą KCD](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), serwer proxy aplikacji potrzebuje tego obiektu użytkownika, aby personifikować użytkownika i uzyskać token Kerberos do aplikacji. 

   > [!NOTE]
   > Podczas konfigurowania usługi Azure serwer proxy aplikacji usługi Azure AD upewnij się, że w polu **tożsamość logowania delegowanego** jest ustawiona wartość **główna nazwa użytkownika** (domyślnie) dla logowania jednokrotnego IWA.

   W przypadku scenariusza użytkownika B2B dostępne są dwie metody, których można użyć do utworzenia obiektów użytkownika-gościa, które są wymagane do autoryzacji w katalogu lokalnym:

   - Microsoft Identity Manager (MIM) i agent zarządzania programu MIM dla Microsoft Graph. 
   - [Skrypt programu PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Użycie skryptu jest bardziej lekkim rozwiązaniem, które nie wymaga programu MIM. 

Na poniższym diagramie przedstawiono ogólne omówienie sposobu, w jaki usługa Azure serwer proxy aplikacji usługi Azure AD i generowanie obiektu użytkownika B2B w katalogu lokalnym współpracują ze sobą, aby umożliwić użytkownikom B2B dostęp do lokalnych aplikacji IWA i KCD. Ponumerowane kroki opisano szczegółowo poniżej diagramu.

![Diagram rozwiązań skryptów programu MIM i B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Użytkownik z organizacji partnerskiej (dzierżawa firmy Fabrikam) jest zapraszany do dzierżawy firmy Contoso.
2.  Obiekt użytkownika-gościa jest tworzony w dzierżawie contoso (na przykład obiekt użytkownika z nazwą UPN guest_fabrikam. com # EXT # @contoso.onmicrosoft.com ).
3.  Gość Fabrikam jest zaimportowany z firmy Contoso za pośrednictwem programu MIM lub skryptu B2B.
4.  Reprezentacja lub "powierzchnia" obiektu użytkownika-gościa firmy Fabrikam (gość # EXT #) jest tworzona w katalogu lokalnym, Contoso.com, za pomocą programu MIM lub za pomocą skryptu B2B programu PowerShell.
5.  Użytkownik-Gość uzyskuje dostęp do aplikacji lokalnej, app.contoso.com.
6.  Żądanie uwierzytelnienia jest autoryzowane za pośrednictwem serwera proxy aplikacji przy użyciu ograniczonego delegowania protokołu Kerberos. 
7.  Ponieważ obiekt użytkownika-Gość istnieje lokalnie, uwierzytelnianie zakończyło się pomyślnie.

### <a name="lifecycle-management-policies"></a>Zasady zarządzania cyklem życia

Lokalnymi obiektami użytkownika B2B można zarządzać za pomocą zasad zarządzania cyklem życia. Na przykład:

- Można skonfigurować zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkownika-gościa, aby funkcja MFA była używana podczas uwierzytelniania serwera proxy aplikacji. Aby uzyskać więcej informacji, zobacz [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).
- Wszystkie sponsorzy, przeglądy dostępu, weryfikacje kont itp., które są wykonywane na użytkownikach w chmurze, mają zastosowanie do użytkowników lokalnych. Na przykład jeśli użytkownik chmury zostanie usunięty w ramach zasad zarządzania cyklem życia, użytkownik lokalny zostanie również usunięty przez program MIM Sync lub za pomocą synchronizacji Azure AD Connect. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem gościa za pomocą przeglądów dostępu w usłudze Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Tworzenie obiektów użytkownika gościa B2B za poorednictwem programu MIM

Informacje o sposobach korzystania z programu MIM 2016 z dodatkiem Service Pack 1 i agenta zarządzania programu MIM dla Microsoft Graph tworzenia obiektów użytkowników Gości w katalogu lokalnym można znaleźć w temacie [współpraca między firmami (B2B) w usłudze Azure AD z programem Microsoft Identity Manager (MIM) 2016 z dodatkiem SP1 przy użyciu serwera proxy aplikacji platformy Azure](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Tworzenie obiektów użytkownika gościa B2B za pomocą skryptu (wersja zapoznawcza)

Dostępny jest przykładowy skrypt programu PowerShell, którego można użyć jako punktu wyjścia do tworzenia obiektów użytkownika-Gości w lokalnym Active Directory.

Skrypt i plik Readme można pobrać z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=51495). Wybierz **skrypt i plik Readme, aby pobrać użytkowników B2B usługi Azure AD on-prem.zip** pliku.

Przed użyciem skryptu upewnij się, że zawarto przegląd wymagań wstępnych i ważnych zagadnień w skojarzonym pliku Readme. Należy również zrozumieć, że skrypt jest udostępniany tylko jako przykład. Twój zespół programistyczny lub partner musi dostosować i przejrzeć skrypt przed jego uruchomieniem.

## <a name="license-considerations"></a>Zagadnienia dotyczące licencji

Upewnij się, że masz odpowiednie licencje dostępu klienta (CAL) dla użytkowników zewnętrznych, którzy uzyskują dostęp do aplikacji lokalnych. Aby uzyskać więcej informacji, zobacz sekcję "łączniki zewnętrzne" [licencji dostępu klienta i licencji zarządzania](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Skontaktuj się z przedstawicielem firmy Microsoft lub lokalnym odsprzedawcą w odniesieniu do konkretnych potrzeb dotyczących licencjonowania.

## <a name="next-steps"></a>Następne kroki

- [Azure Active Directory współpracy B2B z organizacjami hybrydowymi](hybrid-organizations.md)

- Aby zapoznać się z omówieniem Azure AD Connect, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

