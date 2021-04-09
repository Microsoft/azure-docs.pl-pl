---
title: Włączanie dostępu zdalnego do Power BI przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD
description: Obejmuje podstawowe informacje na temat sposobu integrowania Power BI lokalnego z usługą Azure serwer proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9b0cce11abe1076d26ac8d4c4dc57c9b57c4737
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625585"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Włączanie dostępu zdalnego do usługi Power BI dla urządzeń przenośnych przy użyciu serwera proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD w celu włączenia Power BI aplikacji mobilnej do programu Serwer raportów usługi Power BI (PBIRS) i SQL Server Reporting Services (SSRS) 2016 i nowszych. Dzięki tej integracji użytkownicy, którzy znajdują się poza siecią firmową, mogą uzyskiwać dostęp do raportów Power BI z aplikacji mobilnej Power BI i być chroniona za pomocą uwierzytelniania usługi Azure AD. Ta ochrona obejmuje [korzyści z zabezpieczeń](application-proxy-security.md#security-benefits) , takie jak dostęp warunkowy i uwierzytelnianie wieloskładnikowe.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że już wdrożono usługi raportów i [włączono serwer proxy aplikacji](application-proxy-add-on-premises-application.md).

- Włączenie serwera proxy aplikacji wymaga zainstalowania łącznika w systemie Windows Server i wypełnienia jego [wymagań wstępnych](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) , aby łącznik mógł komunikować się z usługami Azure AD.
- W przypadku publikowania Power BI zalecamy używanie tych samych wewnętrznych i zewnętrznych domen. Aby dowiedzieć się więcej o domenach niestandardowych, zobacz [Praca z domenami niestandardowymi w serwerze proxy aplikacji](./application-proxy-configure-custom-domain.md).
- Ta integracja jest dostępna dla aplikacji **Power BI Mobile dla systemów iOS i Android** .

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1. Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD)

W przypadku aplikacji lokalnych, które używają uwierzytelniania systemu Windows, można uzyskać Logowanie jednokrotne z użyciem protokołu uwierzytelniania Kerberos i funkcji o nazwie ograniczone delegowanie protokołu Kerberos (KCD). Po skonfigurowaniu KCD umożliwia łącznikowi serwera proxy aplikacji uzyskanie tokenu systemu Windows dla użytkownika, nawet jeśli użytkownik nie zalogował się bezpośrednio do systemu Windows. Aby dowiedzieć się więcej o KCD, zobacz [Omówienie delegowania ograniczonego protokołu Kerberos](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) i [ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego do aplikacji przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

W zakresie usług Reporting Services nie trzeba wykonywać wielu działań konfiguracyjnych. Upewnij się, że masz prawidłową główną nazwę usługi (SPN), aby umożliwić prawidłowe uwierzytelnianie Kerberos. Upewnij się również, że serwer usług Reporting Services obsługuje uwierzytelnianie negocjowane.

Aby skonfigurować program KCD dla usług Reporting Services, wykonaj następujące czynności.

### <a name="configure-the-service-principal-name-spn"></a>Konfigurowanie głównej nazwy usługi (SPN)

Główna nazwa usługi jest unikatowym identyfikatorem usługi, która korzysta z uwierzytelniania Kerberos. Musisz upewnić się, że masz prawidłową nazwę SPN protokołu HTTP obecną dla serwera raportów. Aby uzyskać informacje na temat konfigurowania odpowiedniej głównej nazwy usługi dla serwera raportów, zobacz [Rejestrowanie głównej nazwy usługi dla serwera raportów](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server).
Aby sprawdzić, czy nazwa SPN została dodana, można uruchomić polecenie Setspn z opcją-L. Aby dowiedzieć się więcej na temat tego polecenia, zobacz [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Włącz uwierzytelnianie negocjowane

Aby umożliwić serwerowi raportów używanie uwierzytelniania Kerberos, należy skonfigurować typ uwierzytelniania serwera raportów jako RSWindowsNegotiate. Skonfiguruj to ustawienie za pomocą pliku rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Aby uzyskać więcej informacji, zobacz [Modyfikowanie pliku konfiguracji usług Reporting Services](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) i [Konfigurowanie uwierzytelniania systemu Windows na serwerze raportów](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Upewnij się, że łącznik jest zaufany do delegowania do nazwy SPN dodanej do konta puli aplikacji usług Reporting Services
Skonfiguruj KCD tak, aby usługa Azure serwer proxy aplikacji usługi Azure AD mogła delegować tożsamości użytkowników do konta puli aplikacji usług Reporting Services. Skonfiguruj delegowanie KCD przez włączenie łącznika serwera proxy aplikacji w celu pobrania biletów Kerberos dla użytkowników uwierzytelnionych w usłudze Azure AD. Następnie serwer przekazuje kontekst do aplikacji docelowej lub usług Reporting Services w tym przypadku.

Aby skonfigurować KCD, powtórz następujące kroki dla każdej maszyny łącznika:

1. Zaloguj się do kontrolera domeny jako administrator domeny, a następnie otwórz **Active Directory Użytkownicy i komputery**.
2. Znajdź komputer, na którym działa łącznik.
3. Kliknij dwukrotnie komputer, a następnie wybierz kartę **Delegowanie**.
4. Wybierz następujące ustawienia delegowania: **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. Następnie wybierz opcję **Użyj dowolnego protokołu uwierzytelniania**.
5. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Użytkownicy lub komputery**.
6. Wprowadź konto usługi używane dla usług Reporting Services. Jest to konto, do którego została dodana główna nazwa usługi w ramach konfiguracji usług Reporting Services.
7. Kliknij przycisk **OK**. Kliknij ponownie przycisk **OK**, aby zapisać zmiany.

Aby uzyskać więcej informacji, zobacz [ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego do aplikacji przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Krok 2. Publikowanie usług raportów za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD

Teraz wszystko jest gotowe do skonfigurowania serwera proxy aplikacji usługi Azure AD.

1. Publikowanie usług raportów za pomocą serwera proxy aplikacji z następującymi ustawieniami. Aby uzyskać instrukcje krok po kroku dotyczące publikowania aplikacji za pomocą serwera proxy aplikacji, zobacz temat dotyczący [publikowania aplikacji za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Wewnętrzny adres URL**: wprowadź adres URL serwera raportów, który łącznik może dotrzeć do sieci firmowej. Upewnij się, że ten adres URL jest dostępny z serwera, na którym zainstalowano łącznik. Najlepszym rozwiązaniem jest użycie domeny najwyższego poziomu, takiej jak `https://servername/`, aby uniknąć problemów z ścieżkami podrzędnymi opublikowanymi za pośrednictwem serwera proxy aplikacji. Użyj na przykład domeny `https://servername/`, a nie `https://servername/reports/` lub `https://servername/reportserver/`.
     > [!NOTE]
     > Zalecamy używanie bezpiecznego połączenia HTTPS z serwerem raportów. Aby uzyskać odpowiednie instrukcje, zobacz temat [Configure SSL connections on a native mode report server](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server) (Konfigurowanie połączeń SSL na serwerze raportów w trybie natywnym).
   - **Zewnętrzny adres URL**: wprowadź publiczny adres URL, z którym zostanie nawiązane połączenie Power BI aplikacji mobilnej. Na przykład może wyglądać tak jak następujący `https://reports.contoso.com`, jeśli jest używana domena niestandardowa. Aby użyć domeny niestandardowej, przekaż certyfikat dla domeny i wskaż rekord DNS jako domyślną domenę msappproxy.net dla aplikacji. Aby uzyskać szczegółowe instrukcje, zobacz temat [Praca z domenami niestandardowymi na serwerze proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

   - **Metoda wstępnego uwierzytelniania**: Azure Active Directory

2. Po opublikowaniu aplikacji skonfiguruj ustawienia logowania jednokrotnego, wykonując następujące kroki:

   a. Na stronie aplikacji w portalu wybierz pozycję **Logowanie jednokrotne**.

   b. W obszarze **trybu logowania jednokrotnego** wybierz pozycję **Zintegrowane uwierzytelnianie systemu Windows**.

   c. Ustaw **główną nazwę usługi aplikacji wewnętrznej** na ustawioną wcześniej wartość.

   d. Wybierz pozycję **Delegowana tożsamość logowania**, aby używać łącznika w imieniu użytkowników. Aby uzyskać więcej informacji, zobacz sekcję [Praca z różnymi tożsamościami lokalnymi i w chmurze](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

Aby zakończyć konfigurowanie aplikacji, przejdź do sekcji **Użytkownicy i grupy** i przypisz użytkownikom dostęp do tej aplikacji.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Krok 3. Modyfikowanie identyfikatora URI odpowiedzi dla aplikacji

Aby aplikacja mobilna Power BI mogła łączyć się z usługami raportów i uzyskiwać do nich dostęp, musisz skonfigurować rejestrację aplikacji, która została automatycznie utworzona w kroku 2.

1. Na stronie **omówienia** aplikacji Azure Active Directory wybierz pozycję **Rejestracje aplikacji**.
2. Na karcie **wszystkie aplikacje** Wyszukaj aplikację utworzoną w kroku 2.
3. Wybierz aplikację, a następnie wybierz pozycję **Uwierzytelnianie**.
4. Dodaj następujące identyfikatory URI przekierowania na podstawie używanej platformy.

   Podczas konfigurowania aplikacji dla Power BI Mobile **iOS** Dodaj następujące identyfikatory URI przekierowania typu klient publiczny (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Podczas konfigurowania aplikacji dla Power BI Mobile **Android** Dodaj następujące identyfikatory URI przekierowania typu klient publiczny (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Aby aplikacja działała poprawnie, należy dodać identyfikatory URI przekierowania. W przypadku konfigurowania aplikacji do Power BI Mobile iOS i Android Dodaj następujący identyfikator URI przekierowania typu klient publiczny (Mobile & Desktop) do listy identyfikatorów URI przekierowania skonfigurowanych dla systemu iOS: `urn:ietf:wg:oauth:2.0:oob` .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Krok 4. nawiązanie połączenia z aplikacją mobilną Power BI

1. W aplikacji mobilnej Power BI Nawiąż połączenie z wystąpieniem usług Reporting Services. W tym celu wprowadź **zewnętrzny adres URL** aplikacji opublikowanej za pomocą serwera proxy aplikacji.

   ![Power BI aplikacji mobilnej z zewnętrznym adresem URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Wybierz pozycję **Połącz**. Nastąpi przekierowanie do Azure Active Directory stronie logowania.

3. Wprowadź prawidłowe poświadczenia dla użytkownika, a następnie wybierz pozycję **Zaloguj się**. Zobaczysz elementy z serwera usług Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Krok 5. Konfigurowanie zasad usługi Intune dla urządzeń zarządzanych (opcjonalnie)

Za pomocą Microsoft Intune można zarządzać aplikacjami klienckimi używanymi przez pracowników firmy. Usługa Intune umożliwia korzystanie z takich funkcji, jak szyfrowanie danych i dodatkowe wymagania dotyczące dostępu. Aby dowiedzieć się więcej o zarządzaniu aplikacjami za poorednictwem usługi Intune, zobacz Zarządzanie aplikacjami w usłudze Intune. Aby umożliwić aplikacji mobilnej Power BI współpracują z zasadami usługi Intune, wykonaj następujące czynności.

1. Przejdź do **Azure Active Directory** , a następnie pozycję **rejestracje aplikacji**.
2. Wybierz aplikację skonfigurowaną w kroku 3 podczas rejestrowania natywnej aplikacji klienckiej.
3. Na stronie aplikacji wybierz pozycję **uprawnienia interfejsu API**.
4. Kliknij przycisk **Dodaj uprawnienie**.
5. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację**, wyszukaj frazę "Zarządzanie aplikacjami mobilnymi firmy Microsoft" i wybierz ją.
6. Dodaj uprawnienie **DeviceManagementManagedApps. ReadWrite** do aplikacji
7. Kliknij pozycję **Udziel zgody administratora** , aby udzielić uprawnienia dostępu do aplikacji.
8. Skonfiguruj odpowiednie zasady usługi Intune, odwołując się do [sposobu tworzenia i przypisywania zasad ochrony aplikacji](/intune/app-protection-policies).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli aplikacja zwróci stronę błędu po próbie załadowania raportu przez więcej niż kilka minut, może zajść potrzeba zmiany ustawienia limitu czasu. Domyślnie serwer proxy aplikacji obsługuje aplikacje, które w odpowiedzi na żądanie zajmują do 85 sekund. Aby wydłużyć to ustawienie na 180 sekund, wybierz limit czasu zaplecza na **stronie** ustawienia serwera proxy aplikacji dla aplikacji. Porady dotyczące tworzenia szybkich i niezawodnych raportów można znaleźć w temacie [Power BI raporty najlepsze rozwiązania](/power-bi/power-bi-reports-performance).

Za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD, aby umożliwić aplikacji mobilnej Power BI łączenie się z lokalnymi Serwer raportów usługi Power BI nie jest obsługiwana w przypadku zasad dostępu warunkowego, które wymagają aplikacji Power BI firmy Microsoft jako zatwierdzonej aplikacji klienckiej.

## <a name="next-steps"></a>Następne kroki

- [Włącz natywne aplikacje klienckie do współpracy z aplikacjami proxy](application-proxy-configure-native-client-application.md)
- [Wyświetlanie lokalnych raportów serwera raportów i wskaźników KPI w aplikacjach mobilnych usługi Power BI](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
