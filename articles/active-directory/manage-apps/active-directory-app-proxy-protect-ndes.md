---
title: Integracja z usługą serwer proxy aplikacji usługi Azure AD na serwerze usługi NDES
titleSuffix: Azure Active Directory
description: Wskazówki dotyczące wdrażania serwer proxy aplikacji usługi Azure Active Directory w celu ochrony serwera usługi NDES.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 69193296069765ae6e94ffe97913c136a0d033d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257900"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integracja z usługą Azure serwer proxy aplikacji usługi Azure AD na serwerze usługi rejestracji urządzeń sieciowych (NDES)

Serwer proxy aplikacji Azure Active Directory (AD) umożliwia publikowanie aplikacji w sieci. Aplikacje te są takie jak witryny programu SharePoint, aplikacja internetowa Microsoft Outlook i inne aplikacje sieci Web. Zapewnia również bezpieczny dostęp do użytkowników spoza sieci za pośrednictwem platformy Azure.

Jeśli dopiero zaczynasz korzystać z usługi Azure serwer proxy aplikacji usługi Azure AD i chcesz dowiedzieć się więcej, zobacz [zdalny dostęp do aplikacji lokalnych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy.md).

Usługa Azure serwer proxy aplikacji usługi Azure AD jest oparta na platformie Azure. Zapewnia ogromną przepustowość sieci i infrastrukturę serwerów w celu zapewnienia lepszej ochrony przed atakami typu "odmowa usługi" (DDOS) i doskonałymi dostępnością. Ponadto nie ma potrzeby otwierania zewnętrznych portów zapory w sieci lokalnej i nie jest wymagany żaden serwer DMZ. Cały ruch pochodzi z ruchu przychodzącego. Aby uzyskać pełną listę portów wychodzących, zobacz [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego za pomocą serwera proxy aplikacji w Azure Active Directory](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> Azure serwer proxy aplikacji usługi Azure AD to funkcja, która jest dostępna tylko w przypadku korzystania z wersji Premium lub Basic systemu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Jeśli masz Licencje pakietu Enterprise Mobility Suite (EMS), możesz korzystać z tego rozwiązania.
> Łącznik usługi Azure serwer proxy aplikacji usługi Azure AD jest instalowany tylko w systemie Windows Server 2012 R2 lub nowszym. Jest to również wymagane przez serwer usługi NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instalowanie i rejestrowanie łącznika na serwerze usługi NDES

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator aplikacji w katalogu, w którym jest używany serwer proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias administratora w tej domenie.
1. Wybierz swoją nazwę użytkownika w prawym górnym rogu. Upewnij się, że logujesz się do katalogu, który używa serwera proxy aplikacji. Jeśli musisz zmienić katalogi, wybierz pozycję **Przełącz katalog** i wybierz katalog, w którym jest używany serwer proxy aplikacji.
1. W lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzanie** wybierz pozycję **serwer proxy aplikacji**.
1. Wybierz pozycję **Pobierz usługę łącznika**.

    ![Pobierz usługę łącznika, aby zobaczyć warunki korzystania z usługi](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Zapoznaj się z warunkami użytkowania usługi. Gdy wszystko będzie gotowe, wybierz pozycję **Akceptuj postanowienia & Pobierz**.
1. Skopiuj plik instalacyjny programu Azure serwer proxy aplikacji usługi Azure AD Connector na serwer usługi NDES. 
   > Łącznik można zainstalować na dowolnym serwerze w sieci firmowej przy użyciu dostępu do usługi NDES. Nie musisz instalować go na serwerze usługi NDES.
1. Uruchom plik instalacyjny, taki jak *AADApplicationProxyConnectorInstaller.exe*. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania.
1. Podczas instalacji zostanie wyświetlony monit o zarejestrowanie łącznika przy użyciu serwera proxy aplikacji w katalogu usługi Azure AD.
   * Podaj poświadczenia dla globalnego lub administratora aplikacji w katalogu usługi Azure AD. Poświadczenia globalne lub administratora aplikacji usługi Azure AD mogą różnić się od poświadczeń platformy Azure w portalu.

        > [!NOTE]
        > Globalne lub konto administratora aplikacji używane do rejestrowania łącznika musi należeć do tego samego katalogu, w którym jest włączona usługa serwera proxy aplikacji.
        >
        > Jeśli na przykład domena usługi Azure AD to *contoso.com*, Administrator globalny/aplikacja powinna mieć lub mieć `admin@contoso.com` inny prawidłowy alias w tej domenie.

   * Jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona dla serwera, na którym jest instalowany łącznik, ekran rejestracji może zostać zablokowany. Aby zezwolić na dostęp, postępuj zgodnie z instrukcjami w komunikacie o błędzie lub wyłącz zaawansowane zabezpieczenia programu Internet Explorer podczas procesu instalacji.
   * Jeśli rejestracja łącznika nie powiedzie się, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md).
1. Po zakończeniu instalacji należy wyświetlić uwagę dla środowisk z serwerem proxy wychodzącego. Aby skonfigurować łącznik usługi Azure serwer proxy aplikacji usługi Azure AD do pracy przez wychodzący serwer proxy, uruchom dostarczony skrypt, taki jak `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Na stronie serwer proxy aplikacji w Azure Portal nowy łącznik zostanie wyświetlony ze stanem *aktywne*, jak pokazano w następującym przykładzie:

    ![Nowy łącznik usługi Azure serwer proxy aplikacji usługi Azure AD wyświetlany jako aktywny w Azure Portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Aby zapewnić wysoką dostępność aplikacji uwierzytelnianych za pośrednictwem usługi Azure serwer proxy aplikacji usługi Azure AD, można zainstalować łączniki na wielu maszynach wirtualnych. Powtórz te same kroki opisane w poprzedniej sekcji, aby zainstalować łącznik na innych serwerach przyłączonych do domeny zarządzanej AD DS platformy Azure.

1. Po pomyślnej instalacji Wróć do Azure Portal.

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**.

   ![Upewnij się, że interesują Cię odpowiednie osoby zainteresowane](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Wybierz pozycję **+ Nowa aplikacja**, a następnie wybierz pozycję **aplikacja lokalna**. 

1. Na stronie **Dodawanie własnej aplikacji lokalnej** skonfiguruj następujące pola:

   * **Nazwa**: Wprowadź nazwę aplikacji.
   * **Wewnętrzny adres URL**: wprowadź wewnętrzny adres URL/Nazwa FQDN serwera usługi NDES, na którym został zainstalowany łącznik.
   * **Wstępne uwierzytelnianie**: wybierz pozycję **przekazywanie**. Nie jest możliwe używanie żadnej formy uwierzytelniania wstępnego. Protokół używany na potrzeby żądań certyfikatów (SCEP) nie zapewnia tej opcji.
   * Skopiuj podany **zewnętrzny adres URL** do Schowka.

1. Wybierz pozycję **+ Dodaj** , aby zapisać aplikację.

1. Sprawdź, czy możesz uzyskać dostęp do serwera usługi NDES za pośrednictwem serwera proxy aplikacji Azure AD, wklejając link skopiowany w kroku 15 do przeglądarki. Powinna zostać wyświetlona domyślna strona powitalna usług IIS.

1. Jako test końcowy Dodaj ścieżkę *mscep.dll* do istniejącego adresu URL wklejonego w poprzednim kroku:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Powinien zostać wyświetlony **komunikat o błędzie HTTP 403 — Dostęp zabroniony** .

1. Zmień podany adres URL usługi NDES (za pośrednictwem Microsoft Intune) na urządzenia. Ta zmiana może być w programie Microsoft Endpoint Configuration Manager lub centrum administracyjnym programu Microsoft Endpoint Manager.

   * W przypadku Configuration Manager przejdź do punktu rejestracji certyfikatu i Dostosuj adres URL. Ten adres URL wskazuje, które urządzenia są wywoływane, i zaprezentowanie ich wyzwania.
   * W przypadku autonomicznej usługi Intune Edytuj lub Utwórz nowe zasady SCEP i Dodaj nowy adres URL.

## <a name="next-steps"></a>Następne kroki

Dzięki usłudze Azure serwer proxy aplikacji usługi Azure AD zintegrowanej z usługą NDES można publikować aplikacje dla użytkowników. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](./application-proxy-add-on-premises-application.md).
