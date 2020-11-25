---
title: Logowanie przy użyciu klucza zabezpieczeń bezhasła — Azure Active Directory
description: Dowiedz się, jak włączyć logowanie za pomocą klucza zabezpieczeń bezhasłem w celu Azure Active Directory przy użyciu kluczy zabezpieczeń FIDO2 (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25c6ccb4661fde1efb088cd8535bf2759f2057b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95902764"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Włącz logowanie za pomocą klucza zabezpieczeń bez hasła na urządzeniach z systemem Windows 10 z Azure Active Directory (wersja zapoznawcza)

Ten dokument koncentruje się na włączaniu uwierzytelniania bezhaseł opartego na kluczu zabezpieczeń FIDO2 na urządzeniach z systemem Windows 10. Na końcu tego artykułu będzie można zalogować się zarówno do usługi Azure AD, jak i do hybrydowych urządzeń z systemem Windows 10 dołączonych do usługi Azure AD przy użyciu klucza zabezpieczeń FIDO2.

> [!NOTE]
> Klucze zabezpieczeń FIDO2 są publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Wymagania

| Typ urządzenia | Dołączone do usługi Azure AD | hybrydowym dołączonym do usługi Azure AD. |
| --- | --- | --- |
| [Multi-Factor Authentication usługi Azure AD](howto-mfa-getstarted.md) | X | X |
| [Wersja zapoznawcza rejestracji informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md) | X | X |
| Zgodne [FIDO2 klucze zabezpieczeń](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN wymaga systemu Windows 10 w wersji 1903 lub nowszej | X | X |
| [Urządzenia przyłączone do usługi Azure AD](../devices/concept-azure-ad-join.md) wymagają systemu Windows 10 w wersji 1909 lub nowszej | X |   |
| [Urządzenia dołączone do hybrydowej usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md) wymagają systemu Windows 10 w wersji 2004 lub nowszej |   | X |
| W pełni poprawione kontrolery domeny systemu Windows Server 2016/2019. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) w wersji 1.4.32.0 lub nowszej |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (opcjonalnie) | X | X |
| Pakiet aprowizacji (opcjonalnie) | X | X |
| Zasady grupy (opcjonalnie) |   | X |

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze nie są obsługiwane:

- Wdrożenie systemu Windows Server Active Directory Domain Services (AD DS) (urządzenia lokalne).
- Scenariusze dotyczące protokołu RDP, infrastruktury VDI i Citrix przy użyciu klucza zabezpieczeń.
- S/MIME przy użyciu klucza zabezpieczeń.
- "Uruchom jako" przy użyciu klucza zabezpieczeń.
- Zaloguj się do serwera przy użyciu klucza zabezpieczeń.
- Jeśli klucz zabezpieczeń nie został użyty do zalogowania się na urządzeniu w trybie online, nie można go użyć do zalogowania się lub odblokowania w trybie offline.
- Logowanie lub odblokowywanie urządzenia z systemem Windows 10 z kluczem zabezpieczeń zawierającym wiele kont usługi Azure AD. W tym scenariuszu jest używane ostatnie konto dodane do klucza zabezpieczeń. Funkcja WebAuthN umożliwia użytkownikom wybranie konta, które ma być używane.
- Odblokuj urządzenie z systemem Windows 10 w wersji 1809. Aby uzyskać najlepsze środowisko, Użyj systemu Windows 10 w wersji 1903 lub nowszej.

## <a name="prepare-devices-for-preview"></a>Przygotuj urządzenia do wersji zapoznawczej

Urządzenia przyłączone do usługi Azure AD, które są uruchamiane w wersji zapoznawczej funkcji, muszą mieć system Windows 10 w wersji 1909 lub nowszej.

Urządzenia dołączone do hybrydowej usługi Azure AD muszą mieć uruchomiony system Windows 10 w wersji 2004 lub nowszej.

## <a name="enable-security-keys-for-windows-sign-in"></a>Włącz klucze zabezpieczeń logowania systemu Windows

Organizacje mogą wybrać jedną lub więcej z poniższych metod, aby umożliwić korzystanie z kluczy zabezpieczeń logowania systemu Windows na podstawie wymagań organizacji:

- [Włącz w usłudze Intune](#enable-with-intune)
- [Planowane wdrożenie usługi Intune](#targeted-intune-deployment)
- [Włącz z pakietem aprowizacji](#enable-with-a-provisioning-package)
- [Włącz z zasady grupy (tylko urządzenia dołączone do hybrydowej usługi Azure AD)](#enable-with-group-policy)

> [!IMPORTANT]
> Organizacje z **urządzeniami dołączonymi do hybrydowej usługi Azure AD** muszą **również** wykonać kroki opisane w tym artykule, [Włącz uwierzytelnianie FIDO2 w zasobach lokalnych](howto-authentication-passwordless-security-key-on-premises.md) przed zastosowaniem uwierzytelniania klucza zabezpieczeń w systemie Windows 10 FIDO2.
>
> Organizacje z **urządzeniami przyłączonymi do usługi Azure AD** muszą wykonać te czynności, zanim ich urządzenia będą mogły uwierzytelniać się w zasobach lokalnych przy użyciu kluczy zabezpieczeń FIDO2.

### <a name="enable-with-intune"></a>Włącz w usłudze Intune

Aby włączyć korzystanie z kluczy zabezpieczeń w usłudze Intune, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Microsoft Intune**  >  **rejestracji urządzeń** Rejestracja  >  **Windows** Windows  >  **Hello dla firm**  >  **Właściwości**.
1. W obszarze **Ustawienia** ustaw opcję Zaloguj się przy **użyciu kluczy zabezpieczeń** do **włączenia**.

Konfiguracja kluczy zabezpieczeń dla logowania nie zależy od konfiguracji funkcji Windows Hello dla firm.

### <a name="targeted-intune-deployment"></a>Planowane wdrożenie usługi Intune

Aby włączyć dostawcę poświadczeń dla określonych grup urządzeń, użyj następujących ustawień niestandardowych za pośrednictwem usługi Intune:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Microsoft Intune**  >  **profil konfiguracji urządzeń**  >  **Profiles**  >  **Utwórz profil**.
1. Skonfiguruj nowy profil przy użyciu następujących ustawień:
   - Name: klucze zabezpieczeń dla systemu Windows Sign-In
   - Opis: włącza klucze zabezpieczeń FIDO, które mają być używane podczas logowania do systemu Windows
   - Platforma: Windows 10 lub nowszym
   - Typ profilu: niestandardowy
   - Niestandardowe ustawienia OMA-URI:
      - Nazwa: Włącz klucze zabezpieczeń FIDO dla systemu Windows Sign-In
      - OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Typ danych: liczba całkowita
      - Wartość: 1
1. Te zasady mogą być przypisane do określonych użytkowników, urządzeń lub grup. Aby uzyskać więcej informacji, zobacz [przypisywanie profilów użytkowników i urządzeń w Microsoft Intune](/intune/device-profile-assign).

![Tworzenie zasad niestandardowej konfiguracji urządzeń w usłudze Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Włącz z pakietem aprowizacji

W przypadku urządzeń, które nie są zarządzane przez usługę Intune, można zainstalować pakiet aprowizacji, aby włączyć tę funkcję. Aplikację Windows Configuration Designer można zainstalować z poziomu [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). Wykonaj następujące kroki, aby utworzyć pakiet aprowizacji:

1. Uruchom projektanta konfiguracji systemu Windows.
1. Wybierz pozycję **plik**  >  **Nowy projekt**.
1. Nadaj projektowi nazwę i Zanotuj ścieżkę, w której został utworzony projekt, a następnie wybierz przycisk **dalej**.
1. Pozostaw wybrany *pakiet aprowizacji* jako **przepływ pracy wybranego projektu** i wybierz pozycję **dalej**.
1. Wybierz *wszystkie wersje pulpitu systemu Windows* w obszarze **Wybierz ustawienia do wyświetlenia i skonfigurowania**, a następnie wybierz pozycję **dalej**.
1. Wybierz pozycję **Zakończ**.
1. W nowo utworzonym projekcie przejdź do **ustawień środowiska uruchomieniowego**  >  **WindowsHelloForBusiness**  >  **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Ustaw **UseSecurityKeyForSignIn** na *włączone*.
1. Wybierz **Export**  >  **pakiet aprowizacji** eksportu
1. Pozostaw wartości domyślne w oknie **kompilacja** w obszarze **opisz pakiet aprowizacji**, a następnie wybierz pozycję **dalej**.
1. Pozostaw wartości domyślne w oknie **kompilacja** w obszarze **Wybierz szczegóły zabezpieczeń dla pakietu aprowizacji** i wybierz pozycję **dalej**.
1. Zanotuj lub zmień ścieżkę w oknach **kompilacji** w obszarze **Wybierz lokalizację, w której ma zostać zapisany pakiet aprowizacji** , a następnie wybierz pozycję **dalej**.
1. Wybierz pozycję **kompilacja** na stronie **Kompiluj pakiet aprowizacji** .
1. Zapisz dwa pliki utworzone (*ppkg* i *Cat*) w lokalizacji, w której można je później zastosować do maszyn.
1. Aby zastosować utworzony pakiet aprowizacji, zobacz [stosowanie pakietu aprowizacji](/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> W przypadku urządzeń z systemem Windows 10 w wersji 1903 należy również włączyć tryb Shared PC (*EnableSharedPCMode*). Aby uzyskać więcej informacji na temat włączania tej funkcji, zobacz [Konfigurowanie komputera udostępnionego lub gościa w systemie Windows 10](/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Włącz z zasady grupy

W przypadku **urządzeń przyłączonych do hybrydowej usługi Azure AD** organizacje mogą konfigurować następujące ustawienia zasady grupy, aby włączyć logowanie za pomocą klucza zabezpieczeń Fido. Ustawienie to można znaleźć w obszarze **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **System**  >  **Logowanie** systemu Włącz logowanie przy  >  **użyciu klucza zabezpieczeń**:

- Ustawienie tych zasad na **włączone** umożliwia użytkownikom logowanie się przy użyciu kluczy zabezpieczeń.
- Ustawienie tych zasad na **wyłączone** lub **Nieskonfigurowane** uniemożliwia użytkownikom logowanie się przy użyciu kluczy zabezpieczeń.

To ustawienie zasady grupy wymaga zaktualizowanej wersji `CredentialProviders.admx` szablonu zasady grupy. Ten nowy szablon jest dostępny w następnej wersji systemu Windows Server i z systemem Windows 10 20H1. To ustawienie może być zarządzane przy użyciu urządzenia z jedną z tych nowszych wersji systemu Windows lub centralnie, postępując zgodnie ze wskazówkami w temacie dotyczącym pomocy technicznej, [jak utworzyć magazyn centralny dla zasady grupy Szablony administracyjne w systemie Windows i zarządzać](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)nim.

## <a name="sign-in-with-fido2-security-key"></a>Zaloguj się przy użyciu klucza zabezpieczeń FIDO2

W poniższym przykładzie użytkownik o nazwie Bala Sandhu już udostępnił swój klucz zabezpieczeń FIDO2 za pomocą kroków z poprzedniego artykułu, [włączając klucz zabezpieczeń bezhasłem](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). W przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD upewnij się, że [włączono również logowanie klucza zabezpieczeń bez hasła do zasobów lokalnych](howto-authentication-passwordless-security-key-on-premises.md). Bala może wybrać dostawcę poświadczeń klucza zabezpieczeń z ekranu blokady systemu Windows 10 i wstawić klucz zabezpieczeń, aby zalogować się do systemu Windows.

![Logowanie za pomocą klucza zabezpieczeń na ekranie blokady systemu Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Zarządzaj kluczem zabezpieczeń biometrycznym, numerem PIN lub resetowaniem klucza zabezpieczeń

* Windows 10 w wersji 1903 lub nowszej
   * Użytkownicy mogą otwierać **Ustawienia systemu Windows** na swoim **Accounts**  >  **kluczu zabezpieczeń** urządzeń > konta
   * Użytkownicy mogą zmieniać swój kod PIN, aktualizować biometria lub resetować swój klucz zabezpieczeń

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkać problemy podczas wyświetlania podglądu tej funkcji, Udostępnij za pośrednictwem aplikacji centrum opinii o systemie Windows, wykonując następujące czynności:

1. Uruchom **centrum opinii** i upewnij się, że użytkownik jest zalogowany.
1. Prześlij opinię poniżej następującej kategoryzacji:
   - Kategoria: zabezpieczenia i prywatność
   - Podkategoria: FIDO
1. Aby przechwytywać dzienniki, użyj opcji, aby **ponownie utworzyć mój problem**

## <a name="next-steps"></a>Następne kroki

[Zapewnianie dostępu do zasobów lokalnych w usłudze Azure AD i hybrydowych urządzeniach z usługą Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Dowiedz się więcej o rejestrowaniu urządzeń](../devices/overview.md)

[Dowiedz się więcej o usłudze Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)