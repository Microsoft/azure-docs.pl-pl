---
title: Bezpieczny dostęp hybrydowy usługi Azure AD za pomocą klawisza F5 VPN | Microsoft Docs
description: Samouczek dotyczący Azure Active Directory integracji z logowaniem jednokrotnym (SSO) z F5 BIG-IP dla sieci VPN bez hasła
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730891"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Samouczek dotyczący Azure Active Directory integracji z logowaniem jednokrotnym przy użyciu protokołu F5 BIG-IP dla sieci VPN bez hasła

W tym samouczku dowiesz się, jak zintegrować rozwiązanie F5's BIG-IP oparty na protokole SSL (Secure Private Network VPN) z usługą Azure Active Directory (AD) dla bezpiecznego dostępu hybrydowego (SHA).

Integracja Połączenie SSL z adresu IP sieci VPN z usługą Azure AD zapewnia [wiele najważniejszych korzyści](f5-aad-integration.md), w tym:

- Udoskonalone zarządzanie zaufaniem bez nadzoru przy użyciu [uwierzytelniania wstępnego i autoryzacji usługi Azure AD](../../app-service/overview-authentication-authorization.md)

- [Uwierzytelnianie bez hasła do usługi sieci VPN](https://www.microsoft.com/security/business/identity/passwordless)

- Zarządzanie tożsamościami i dostępem z pojedynczej płaszczyzny kontroli — [Azure Portal](https://portal.azure.com/#home)

Pomimo tego, że ta świetna wartość nie zwiększa się, klasyczna sieć VPN pozostaje niezależna od koncepcji obwodu sieci, w którym zaufana jest na zewnątrz i niezaufana. Ten model nie jest już efektywny dla osiągania prawdziwych stan zaufania, ponieważ zasoby firmowe nie są już ograniczone do ścian centrum danych przedsiębiorstwa, ale raczej w środowiskach wielu chmur bez stałych granic. Z tego powodu zachęcamy naszych klientów do rozważania przechodzenia do większej liczby rozwiązań opartych na tożsamości na potrzeby zarządzania [dostępem do poszczególnych aplikacji](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md).

## <a name="scenario-description"></a>Opis scenariusza

W tym scenariuszu wystąpienie usługi SSL typu BIG-IP jest konfigurowane jako dostawca usługi SAML (SP), a usługa Azure AD będzie dostawcy tożsamości zaufanego elementu SAML, zapewniając wstępne uwierzytelnianie. Logowanie jednokrotne (SSO) z usługi Azure AD jest następnie zapewniane za pośrednictwem uwierzytelniania opartego na oświadczeniach dla środowiska APM BIG-IP, co zapewnia bezproblemowe dostęp do sieci VPN.

![Obraz przedstawia architekturę SSL-VPN](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Wszystkie przykładowe ciągi lub wartości, do których odwołuje się w tym przewodniku, powinny być zastępowane dla rzeczywistego środowiska.

## <a name="prerequisites"></a>Wymagania wstępne

Wcześniejsze środowisko lub znajomość F5 BIG-IP nie jest konieczne, jednak będą potrzebne:

- [Bezpłatna subskrypcja](https://azure.microsoft.com/trial/get-started-active-directory/) usługi Azure AD lub nowsza

- Tożsamości użytkowników powinny być [synchronizowane z katalogu lokalnego](../hybrid/how-to-connect-sync-whatis.md) do usługi Azure AD.

- Konto z [uprawnieniami](../roles/permissions-reference.md#application-administrator) administratora aplikacji usługi Azure AD

- Istniejąca infrastruktura BIG-IP z routingiem ruchu klienckiego do i z BIG-IP lub do [wdrożenia wersji zwirtualizowanej Big-IP na platformie Azure](f5-bigip-deployment-guide.md).

- Rekord dla usługi publikowania w sieci VPN BIG-IP musi istnieć w publicznej systemie DNS lub pliku localhost klienta testowego podczas testowania

- Należy zainicjować obsługę BIG-IP przy użyciu wymaganych certyfikatów SSL na potrzeby publikowania usług za pośrednictwem protokołu HTTPS.

Zaznajomienie się z [terminologią F5 Big-IP](https://www.f5.com/services/resources/glossary) pomoże również zrozumieć różne składniki, do których odwołuje się samouczek.

>[!NOTE]
>Platforma Azure ciągle się zmienia, dlatego nie należy przeszukiwać żadnych wszystkie szczegóły między instrukcjami zawartymi w tym przewodniku i elementami widocznymi w Azure Portal. Zrzuty ekranu pochodzą z 15ów BIG-IP, jednak pozostają stosunkowo podobne od 13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Dodawanie F5 BIG-IP z galerii usługi Azure AD

Skonfigurowanie zaufania SAML federacji między BIG-IP umożliwia usłudze Azure AD BIG-IP przekazanie wstępnego uwierzytelniania i [dostępu warunkowego](../conditional-access/overview.md) do usługi Azure AD przed udzieleniem dostępu do opublikowanej usługi sieci VPN.

1. Zaloguj się do portalu usługi Azure AD przy użyciu konta z prawami administratora aplikacji

2. W okienku nawigacji po lewej stronie wybierz **usługę Azure Active Directory**

3. Przejdź do pozycji **aplikacje dla przedsiębiorstw** i na górze wstążki wybierz pozycję **Nowa aplikacja**.

4. Wyszukaj F5 w galerii i wybierz pozycję **F5 Big-IP APM Azure AD Integration**.

5. Podaj nazwę aplikacji, a następnie **Dodaj/Utwórz** , aby została dodana do dzierżawy. Użytkownik może zobaczyć nazwę jako ikonę w portalach aplikacji platformy Azure i pakietu Office 365. Nazwa powinna odzwierciedlać tę konkretną usługę. Na przykład sieć VPN.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

1. Przy użyciu nowych właściwości aplikacji F5 w widoku przejdź do pozycji **Zarządzaj**  >  **logowaniem jednokrotnym**

2. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**. Pomiń monit, aby zapisać ustawienia logowania jednokrotnego **, wybierając pozycję nie, zapiszę się później**.

3. Na stronie **Konfigurowanie logowania** jednokrotnego przy użyciu protokołu SAML wybierz ikonę pióra dla **podstawowej konfiguracji języka SAML** , aby podać następujące informacje:

   - Zastąp wstępnie zdefiniowany **adres URL identyfikatora** adresem URL dla usługi opublikowanej Big-IP. Na przykład `https://ssl-vpn.contoso.com`

   - Wykonaj te same czynności z polem tekstowym **adres URL odpowiedzi** , w tym ze ścieżką punktu końcowego SAML. Na przykład `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - W tej konfiguracji sama aplikacja będzie działać w trybie inicjalizacji dostawcy tożsamości, w którym usługa Azure AD wystawia użytkownikowi potwierdzenie SAML przed przekierowaniem do usługi protokołu SAML BIG-IP. W przypadku aplikacji, które nie obsługują trybu zainicjowanego dostawcy tożsamości, określ **adres URL logowania** dla usługi protokołu SAML Big-IP. Na przykład `https://ssl-vpn.contoso.com`.

   - W polu adres URL wylogowania wprowadź wartość "pre-IP APM Single wylogowania (SLO)", która jest wstępnie zainicjowana przez nagłówek hosta usługi, która jest publikowana. Na przykład `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   Dostarczenie adresu URL SLO gwarantuje, że sesja użytkownika zostanie zakończona w obu końcach — BIG-IP i Azure AD po wylogowaniu się użytkownika. Usługa APM BIG-IP udostępnia również [opcję](https://support.f5.com/csp/article/K12056) kończenia wszystkich sesji podczas wywoływania określonego adresu URL aplikacji.

![Obraz przedstawia podstawową konfigurację języka SAML](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>Z TMOS v16 punkt końcowy SLO protokołu SAML został zmieniony na/SAML/Sp/profile/redirect/SLO

4. Wybierz pozycję **Zapisz** przed wyjściem z menu konfiguracji SAML i Pomiń monit testu logowania jednokrotnego.

Zwróć uwagę na właściwości **atrybutów użytkownika & oświadczenia** , ponieważ usługa Azure AD będzie je wystawiał użytkownikom na potrzeby uwierzytelniania w ramach usługi Big-IP APM.

![Obraz przedstawia oświadczenia atrybutów użytkownika](media/f5-sso-vpn/user-attributes-claims.png)

Możesz dodać wszelkie inne konkretne oświadczenia, które może oczekiwać usługa opublikowana przez duże adresy IP, a jednocześnie zwracając uwagę, że wszelkie oświadczenia zdefiniowane jako uzupełnienie do zestawu domyślnego będą wystawiane tylko wtedy, gdy istnieją w usłudze Azure AD, jako wypełnione atrybuty. W ten sam sposób role usługi [lub](../hybrid/how-to-connect-fed-group-claims.md) członkostwa w grupach muszą również być definiowane względem obiektu użytkownika w usłudze Azure AD, zanim będzie można je wystawić jako zgłoszenie.

![Obraz przedstawiający łącze pobierania metadanych Federacji](media/f5-sso-vpn/saml-signing-certificate.png)

Certyfikaty podpisywania SAML utworzone przez usługę Azure AD mają cykl życia trzy lata, więc będą potrzebne do zarządzania przy użyciu opublikowanych wytycznych usługi Azure AD.

### <a name="azure-ad-authorization"></a>Autoryzacja usługi Azure AD

Domyślnie usługa Azure AD wystawia tokeny dla użytkowników, którym udzielono dostępu do usługi.

1. Nadal w widoku konfiguracji aplikacji wybierz pozycję **Użytkownicy i grupy**

2. Wybierz pozycję **+ Dodaj użytkownika** , a następnie w menu Dodaj przypisanie wybierz pozycję **Użytkownicy i grupy** .

3. W oknie dialogowym **Użytkownicy i grupy** Dodaj grupy użytkowników, którzy mają uprawnienia dostępu do sieci VPN, a następnie **Wybierz pozycję**  >  **Przypisz**

![Obraz przedstawiający Dodawanie linku użytkownika ](media/f5-sso-vpn/add-user-link.png)

4. Spowoduje to zakończenie części usługi Azure AD zaufania federacji SAML. Można teraz skonfigurować usługę BIG-IP APM w celu opublikowania usługi SSL-VPN i skonfigurowania jej z odpowiednim zestawem właściwości do ukończenia zaufania dla wstępnego uwierzytelniania SAML.

## <a name="big-ip-apm-configuration"></a>Konfiguracja protokołu APM BIG-IP

### <a name="saml-federation"></a>Federacja SAML

W poniższej sekcji jest tworzony Dostawca usługi i odpowiednie obiekty SAML dostawcy tożsamości, które są wymagane do ukończenia federowanie usługi sieci VPN z usługą Azure AD.

1. Przejdź do pozycji **dostęp**  >  **Federacji**  >  **usługi SAML Service Provider**  >  **Local Sp usługi** i wybierz pozycję **Utwórz**

![Obraz przedstawia konfigurację protokołu SAML BIG-IP](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Wprowadź **nazwę** i tę samą wartość **identyfikatora jednostki** , którą zdefiniowano w usłudze Azure AD wcześniej, oraz nazwę FQDN hosta, która będzie używana do nawiązywania połączenia z aplikacją

![Obraz przedstawiający tworzenie nowej usługi SAML SP](media/f5-sso-vpn/create-new-saml-sp.png)

   Ustawienia **nazw** Sp są wymagane tylko wtedy, gdy identyfikator jednostki nie jest dokładnym dopasowaniem części nazwy hosta w OPUBLIKOWANYm adresie URL lub jeśli nie jest w zwykłym formacie adresu URL opartego na nazwie hosta. Podaj zewnętrzny schemat i nazwę hosta publikowanej aplikacji, jeśli identyfikator jednostki to `urn:ssl-vpn:contosoonline` .

3. Przewiń w dół, aby wybrać nowy **obiekt SAML Sp** i wybierz **Łącznik powiązania/usuwania powiązania dostawcy tożsamości**.

![Obraz przedstawiający tworzenie federacji z lokalną usługą SP](media/f5-sso-vpn/federation-local-sp-service.png)

4. Wybierz pozycję **Utwórz nowy łącznik dostawcy tożsamości** i z menu rozwijanego wybierz pozycję **z metadanych** .

![Obraz przedstawiający tworzenie nowego łącznika dostawcy tożsamości](media/f5-sso-vpn/create-new-idp-connector.png)

5. Przejdź do pliku XML metadanych Federacji, który został pobrany wcześniej, i podaj **nazwę dostawcy tożsamości** dla obiektu APM, który będzie reprezentować zewnętrzne dostawcy tożsamości SAML

6. Wybierz pozycję **Dodaj nowy wiersz** , aby wybrać nowy łącznik zewnętrznego dostawcy tożsamości usługi Azure AD.

![Obraz przedstawia zewnętrzny łącznik dostawcy tożsamości](media/f5-sso-vpn/external-idp-connector.png)

7. Wybierz pozycję **Aktualizuj** , aby POWIĄZAĆ obiekt SAML Sp z obiektem SAML dostawcy tożsamości, a następnie wybierz przycisk **OK**.

![Obraz przedstawia dostawcy tożsamości języka SAML przy użyciu programu SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Konfiguracja Webtop

Poniższe kroki umożliwiają umożliwienie oferowania połączeń SSL dla użytkowników za pośrednictwem portalu sieci Web BIG-IP.

1. Przejdź do   >    >  **listy Webtop** i wybierz pozycję **Utwórz**.

2. Nadaj nazwę portalowi i ustaw dla niego typ **Full**. Na przykład `Contoso_webtop`.

3. Dostosuj pozostałe Preferencje, a następnie wybierz pozycję **gotowe**.

![Obraz przedstawia konfigurację Webtop](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>Konfiguracja sieci VPN

Funkcja sieci VPN składa się z kilku elementów, z których każdy kontroluje różne aspekty ogólnej usługi.

1. Przejdź do pozycji **dostęp**  >  **łączność/sieci VPN**  >  **dostęp sieciowy (VPN)**  >   , a następnie wybierz pozycję **Utwórz**.

2. Podaj nazwę puli adresów IP przypisywanych do klientów sieci VPN. Na przykład Contoso_vpn_pool

3. Ustaw typ na **zakres adresów IP** i podaj początkowy i końcowy adres IP, a następnie pozycję **Dodaj** i **Zakończ**.

![Obraz przedstawia konfigurację sieci VPN](media/f5-sso-vpn/vpn-configuration.png)

Lista dostępu do sieci udostępnia usługę z ustawieniami adresów IP i DNS z puli sieci VPN, uprawnieniami do routingu użytkowników, a także może uruchamiać aplikacje w razie potrzeby.

1. Przejdź do pozycji **dostęp** do  >  **łączności/sieci VPN: dostęp do sieci (VPN)**  >   , a następnie wybierz pozycję **Utwórz**.

2. Podaj nazwę dla listy dostępu do sieci VPN i podpis, na przykład contoso-VPN, po **zakończeniu**.

![Obraz przedstawia konfigurację sieci VPN na liście dostępu do sieci](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Na górnej wstążce wybierz pozycję **Ustawienia sieciowe** i Dodaj poniższe ustawienia:

   • **Obsługiwana wersja protokołu IP**: IPv4

   • **Pula dzierżaw IPv4**: Wybierz utworzoną wcześniej pulę sieci VPN, na przykład Contoso_vpn_pool

![Obraz przedstawia pulę sieci VPN firmy Contoso](media/f5-sso-vpn/contoso-vpn-pool.png)

   Opcji ustawień klienta można użyć, aby wymusić ograniczenia dotyczące sposobu, w jaki ruch klienta jest kierowany po ustanowieniu sieci VPN.

4. Wybierz pozycję **gotowe** i przejdź do karty DNS/hosty, aby dodać ustawienia:

   • **Podstawowy serwer nazw IPv4**: adres IP serwera DNS środowiska

   • **Domyślny sufiks domeny DNS**: sufiks domeny dla tego konkretnego połączenia sieci VPN. Na przykład contoso.com

![Obraz przedstawia domyślny sufiks domeny](media/f5-sso-vpn/domain-suffix.png)

[Artykuł F5](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) zawiera szczegółowe informacje dotyczące dostosowywania pozostałych ustawień zgodnie z preferencjami.

Profil połączenia BIG-IP jest teraz wymagany do skonfigurowania ustawień dla każdego typu klienta sieci VPN, który musi obsługiwać usługa sieci VPN. Na przykład Windows, OSX i Android.

1. Przejdź do pozycji **dostęp** do  >  **połączeń/**  >  **profilów łączności** sieci VPN  >   i wybierz pozycję **Dodaj**.

2. Podaj nazwę profilu i ustaw dla profilu nadrzędnego wartość **/Common/Connectivity**, na przykład Contoso_VPN_Profile.

![Obraz przedstawiający tworzenie nowego profilu łączności](media/f5-sso-vpn/create-connectivity-profile.png)

[Dokumentacja](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) F5's zawiera więcej informacji na temat obsługi klienta.

## <a name="access-profile-configuration"></a>Konfiguracja profilu dostępu

Po skonfigurowaniu obiektów sieci VPN zasady dostępu są wymagane do włączenia usługi do uwierzytelniania SAML.

1. Przejdź do **pozycji profile dostępu**  >  **/zasady**  >  **dostęp do profilów (zasady dla sesji)** i wybierz pozycję **Utwórz** .

2. Podaj nazwę profilu i typ profilu wybierz **wszystkie**, na przykład Contoso_network_access

3. Przewiń w dół, aby dodać co najmniej jeden język do listy **akceptowanych języków** i wybierz pozycję **gotowe**

![Obraz przedstawia właściwości ogólne](media/f5-sso-vpn/general-properties.png)

4. Wybierz pozycję **Edytuj** w polu zasady Per-Session nowego profilu dostępu, aby Edytor zasad wizualnych mógł zostać uruchomiony na osobnej karcie przeglądarki.

![Obraz przedstawia zasady dotyczące sesji](media/f5-sso-vpn/per-session-policy.png)

5. Wybierz **+** znak i w oknie podręcznym wybierz pozycję **uwierzytelnianie**  >  **SAML** uwierzytelniania  >  **Dodaj element**.

6. W obszarze Konfiguracja uwierzytelniania SAML SP wybierz utworzony wcześniej obiekt SAML dla sieci VPN, a następnie **Zapisz**.

![Obraz przedstawia uwierzytelnianie SAML](media/f5-sso-vpn/saml-authentication.png)

7. Wybierz **+** , aby pomyślnie rozgałęziać uwierzytelnianie SAML.

8. Z poziomu karty przypisanie wybierz pozycję **Zaawansowane przypisanie zasobów** , a następnie **Dodaj element**

![Obraz przedstawiający zaawansowane przypisanie zasobów](media/f5-sso-vpn/advance-resource-assign.png)

9. W oknie podręcznym wybierz pozycję **Nowy wpis** , a następnie **Dodaj/Usuń**.

10. W oknie podrzędnym wybierz pozycję **dostęp do sieci** , a następnie wybierz utworzony wcześniej Profil dostępu do sieci

![Obraz przedstawiający Dodawanie nowego wpisu dostępu do sieci](media/f5-sso-vpn/add-new-entry.png)

11. Przejdź do karty **Webtop** i Dodaj utworzony wcześniej obiekt Webtop.

![Obraz przedstawiający Dodawanie obiektu Webtop](media/f5-sso-vpn/add-webtop-object.png)

12. Wybierz pozycję **Aktualizuj** , a następnie **Zapisz**.

13. Wybierz łącze w górnym polu Odmów, aby zmienić gałąź pomyślnie, aby **zezwolić** na **Zapisywanie**.

![Obraz przedstawia nowy edytor zasad wizualnych](media/f5-sso-vpn/vizual-policy-editor.png)

14. Zatwierdź te ustawienia, wybierając pozycję **Zastosuj zasady dostępu** i Zamknij kartę Edytor zasad wizualnych.

![Obraz przedstawia nowy Menedżer zasad dostępu](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Publikowanie usługi sieci VPN

W przypadku wszystkich ustawień program APM wymaga teraz serwera wirtualnego frontonu do nasłuchiwania klientów łączących się z siecią VPN.

1. Wybierz pozycję serwer wirtualny **ruchu lokalnego**  >  **serwery** wirtualne  >  **listy** i wybierz pozycję **Utwórz**.

2. Podaj **nazwę** serwera wirtualnego sieci VPN, na przykład **VPN_Listener**.

3. Podaj serwer wirtualny z nieużywanym **adresem docelowym IP** , który ma Routing w miejscu do odbierania ruchu klienckiego

4. Ustaw port usługi na **443 https** i upewnij się, że stan jest **włączony**

![Obraz przedstawia nowy serwer wirtualny](media/f5-sso-vpn/new-virtual-server.png)

5. Ustaw **profil http** na http i Dodaj profil SSL (klienta) dla publicznego certyfikatu SSL, który został zainicjowany w ramach wymagań wstępnych.

![Obraz przedstawia profil SSL](media/f5-sso-vpn/ssl-profile.png)

6. W obszarze zasady dostępu Ustaw **Profil dostępu** i **profil łączności** tak, aby korzystał z utworzonych obiektów sieci VPN.

![Obraz przedstawia zasady dostępu](media/f5-sso-vpn/access-policy.png)

7. Po zakończeniu wybierz pozycję **gotowe** .

8.  Usługa SSL-VPN jest teraz publikowana i dostępna za pośrednictwem algorytmu SHA, bezpośrednio za pośrednictwem adresu URL lub portali aplikacji firmy Microsoft.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Koniec hasła, przejdź bezhasło](https://www.microsoft.com/security/business/identity/passwordless)

- [Co to jest dostęp warunkowy?](../conditional-access/overview.md)

- [Platforma zaufania zero firmy Microsoft, która umożliwia zdalne działanie](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Pięć kroków związanych z pełną integracją aplikacji z usługą Azure AD](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Następne kroki

Otwórz przeglądarkę na zdalnym kliencie systemu Windows i przejdź do adresu URL **usługi sieci VPN Big-IP**. Po uwierzytelnieniu w usłudze Azure AD zobaczysz Portal Webtop i sieci VPN.

![Obraz przedstawiający uruchamianie sieci VPN](media/f5-sso-vpn/vpn-launcher.png)

Wybranie kafelka sieci VPN spowoduje zainstalowanie klienta usługi BIG-IP Edge i ustanowienie połączenia sieci VPN skonfigurowanego dla algorytmu SHA.
Aplikacja F5 VPN powinna być również widoczna jako zasób docelowy w ramach dostępu warunkowego usługi Azure AD. Zapoznaj się z naszymi [wskazówkami](../conditional-access/concept-conditional-access-policies.md) dotyczącymi tworzenia zasad dostępu warunkowego, a także umożliwiających użytkownikom [uwierzytelnianie bez hasła](https://www.microsoft.com/security/business/identity/passwordless)usługi Azure AD.