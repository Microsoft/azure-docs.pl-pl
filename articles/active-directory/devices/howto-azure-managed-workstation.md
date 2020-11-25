---
title: Wdrażanie stacji roboczych zarządzanych przez platformę Azure — Azure Active Directory
description: Dowiedz się, jak wdrażać bezpieczne, zarządzane stacje robocze platformy Azure, aby zmniejszyć ryzyko naruszenia z powodu nieodpowiedniej konfiguracji lub złamania zabezpieczeń.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56cd23494f65b1c74e44868496855c6e4a32bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95974089"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Wdrażanie bezpiecznej, zarządzanej na platformie Azure stacji roboczej

Teraz, gdy [rozumiesz bezpieczne stacje robocze](concept-azure-managed-workstation.md), zaczniesz rozpocząć proces wdrażania. Te wskazówki umożliwiają użycie zdefiniowanych profilów w celu zapewnienia bezpieczniejszej stacji roboczej od początku.

![Wdrożenie zabezpieczonej stacji roboczej](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Wybierz profil przed wdrożeniem rozwiązania. Można użyć wielu profilów jednocześnie we wdrożeniu i przypisać je do tagów lub grup.

> [!NOTE]
> Zastosuj dowolne profile zgodnie z wymaganiami. Możesz przenieść do innego profilu, przypisując go w Microsoft Intune.

| Profil | Niski | Rozszerzone | Wysoki | Wyspecjalizowany | Zabezpieczone | Izolowana |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Użytkownik w usłudze Azure AD | Tak | Tak | Tak | Tak | Tak | Tak |
| Usługa Intune — zarządzana | Tak | Tak | Tak | Tak | Tak | Tak |
| Urządzenie — zarejestrowano usługę Azure AD | Tak |  |  |  |  | |   |
| Urządzenie — przyłączone do usługi Azure AD |   | Tak | Tak | Tak | Tak | Tak |
| Zastosowano linię bazową zabezpieczeń usługi Intune |   | Tak <br> Usprawnion | Tak <br> (HighSecurity) | Tak <br> (NCSC) | Tak <br> Secure | Nie dotyczy |
| Sprzęt spełnia bezpieczne standardy systemu Windows 10 |   | Tak | Tak | Tak | Tak | Tak |
| Usługa Microsoft Defender ATP została włączona |   | Tak  | Tak | Tak | Tak | Tak |
| Usuwanie praw administratora |   |   | Tak  | Tak | Tak | Tak |
| Wdrażanie przy użyciu programu Microsoft autopilotaż |   |   | Tak  | Tak | Tak | Tak |
| Aplikacje zainstalowane tylko przez usługę Intune |   |   |   | Tak | Tak |Tak |
| Adresy URL ograniczone do zatwierdzonej listy |   |   |   | Tak | Tak |Tak |
| Zablokowany przez Internet (przychodzący/wychodzący) |   |   |   |  |  |Tak |

> [!NOTE]
> Na **stronie wskazówki dotyczące** bezpiecznych stacji roboczych zostaną przypisane profile i zasady. Użytkownicy nie będą mogli bezpośrednio stosować zasad, co pozwala na korzystanie z udostępniania urządzenia (urządzenia współużytkowane). Jeśli bezpieczna stacja robocza nie jest udostępniona w danym wdrożeniu lub są konieczne zasady poszczególnych użytkowników, przypisanie profilów zasad użytkownika można przypisać do użytkownika i urządzenia. 

## <a name="license-requirements"></a>Wymagania licencyjne

Pojęcia omówione w tym przewodniku założono, że masz Microsoft 365 Enterprise E5 lub równoważną jednostkę SKU. Niektóre zalecenia zawarte w tym przewodniku można zaimplementować przy użyciu mniejszych jednostek SKU. Aby uzyskać więcej informacji, zobacz [Microsoft 365 Enterprise Licencjonowanie](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

W celu zautomatyzowania aprowizacji licencji należy rozważyć [Licencjonowanie oparte na grupach](../enterprise-users/licensing-groups-assign.md) dla użytkowników.

## <a name="azure-active-directory-configuration"></a>Konfiguracja Azure Active Directory

Azure Active Directory (Azure AD) zarządza użytkownikami, grupami i urządzeniami dla stacji roboczych administratora. Włącz usługi tożsamości i funkcje przy użyciu [konta administratora](../roles/permissions-reference.md).

Podczas tworzenia konta administratora zabezpieczonej stacji roboczej należy uwidocznić konto na bieżącej stacji roboczej. Upewnij się, że używasz znanego bezpiecznego urządzenia, aby wykonać tę konfigurację początkową i wszystkie konfiguracje globalne. Aby zmniejszyć narażenie na ataki podczas pierwszego środowiska, należy wziąć pod uwagę [wskazówki, aby zapobiec infekcjom złośliwego oprogramowania](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Wymagaj uwierzytelniania wieloskładnikowego, co najmniej dla administratorów. Zobacz [wdrażanie usługi MFA opartej na chmurze](../authentication/howto-mfa-getstarted.md) w celu uzyskania wskazówek dotyczących implementacji.

### <a name="azure-ad-users-and-groups"></a>Użytkownicy i grupy usługi Azure AD

1. W Azure Portal przejdź do **Azure Active Directory**  >  **użytkowników**  >  **nowy użytkownik**.
1. Aby utworzyć administratora urządzenia, wykonaj czynności opisane w [samouczku Tworzenie użytkownika](/Intune/quickstart-create-user).
1. Wprowadź:

   * **Nazwa** — administrator bezpiecznego stacji roboczej
   * **Nazwa użytkownika** - `secure-ws-admin@identityitpro.com`
   * **Rola katalogu**  -  **Ograniczony administrator** i wybierz rolę **administratora usługi Intune** .

1. Wybierz pozycję **Utwórz**.

Następnie utworzysz dwie grupy: Użytkownicy stacji roboczej i urządzenia stacji roboczej.

W Azure Portal przejdź do **Azure Active Directory**  >  **grup**  >  **Nowa grupa**.

1. W przypadku grupy Użytkownicy stacji roboczej możesz chcieć skonfigurować [Licencjonowanie oparte na grupach](../enterprise-users/licensing-groups-assign.md) w celu zautomatyzowania aprowizacji licencji dla użytkowników.
1. Dla grupy Użytkownicy stacji roboczej wpisz:

   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — bezpieczna użytkownicy stacji roboczej
   * Przypisany **Typ członkostwa**

1. Dodaj użytkownika administratora bezpiecznego stacji roboczej: `secure-ws-admin@identityitpro.com`
1. Można dodać innych użytkowników, którzy będą zarządzać bezpiecznymi stacjami roboczymi.
1. Wybierz pozycję **Utwórz**.
1. Dla grupy urządzenia stacji roboczej wpisz:

   * **Typ grupy** — zabezpieczenia
   * **Nazwa grupy** — bezpieczne stacje robocze
   * Przypisany **Typ członkostwa**

1. Wybierz pozycję **Utwórz**.

### <a name="azure-ad-device-configuration"></a>Konfiguracja urządzenia usługi Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Określanie osób, które mogą dołączać urządzenia do usługi Azure AD

Skonfiguruj ustawienia urządzeń w Active Directory, aby umożliwić grupie zabezpieczeń administracyjnych dołączanie urządzeń do domeny. Aby skonfigurować to ustawienie na podstawie Azure Portal:

1. Przejdź do obszaru **Azure Active Directory** > **Urządzenia** > **Ustawienia urządzeń**.
1. Wybierz opcję **wybrane** w obszarze **Użytkownicy mogą dołączać urządzenia do usługi Azure AD**, a następnie wybierz grupę "Bezpieczna użytkownicy stacji roboczych".

#### <a name="removal-of-local-admin-rights"></a>Usuwanie praw administratora lokalnego

Ta metoda wymaga, aby użytkownicy adresów VIP, DevOps i stacji roboczych na poziomie bezpiecznym nie mieli uprawnień administratora na swoich komputerach. Aby skonfigurować to ustawienie na podstawie Azure Portal:

1. Przejdź do obszaru **Azure Active Directory** > **Urządzenia** > **Ustawienia urządzeń**.
1. Wybierz pozycję **Brak** w obszarze **dodatkowi Administratorzy lokalni na urządzeniach dołączonych do usługi Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Wymagaj uwierzytelniania wieloskładnikowego w celu dołączania urządzeń

Aby bardziej zwiększyć proces dołączania urządzeń do usługi Azure AD:

1. Przejdź do obszaru **Azure Active Directory** > **Urządzenia** > **Ustawienia urządzeń**.
1. Wybierz pozycję **tak** w obszarze **Wymagaj uwierzytelniania wieloskładnikowego, aby dołączać urządzenia**.
1. Wybierz pozycję **Zapisz**.

#### <a name="configure-mobile-device-management"></a>Konfigurowanie zarządzania urządzeniami przenośnymi

Z witryny Azure Portal:

1. Przejdź do **Microsoft Intune Azure Active Directory**  >  **Mobility (MDM i mam)**  >  **Microsoft Intune**.
1. Zmień ustawienie **zakresu użytkownika MDM** na **wszystkie**.
1. Wybierz pozycję **Zapisz**.

Te kroki umożliwiają zarządzanie dowolnym urządzeniem za pomocą usługi Intune. Aby uzyskać więcej informacji, zobacz [Przewodnik Szybki Start dotyczący usługi Intune: Konfigurowanie automatycznego rejestrowania dla urządzeń z systemem Windows 10](/Intune/quickstart-setup-auto-enrollment). W przyszłości utworzysz konfigurację i zasady zgodności usługi Intune.

#### <a name="azure-ad-conditional-access"></a>Dostęp warunkowy w usłudze Azure AD

Dostęp warunkowy usługi Azure AD może pomóc w ograniczeniu uprzywilejowanych zadań administracyjnych do zgodnych urządzeń. Wstępnie zdefiniowane elementy członkowskie grupy **bezpiecznych użytkowników stacji roboczej** są wymagane do przeprowadzenia uwierzytelniania wieloskładnikowego podczas logowania do aplikacji w chmurze. Najlepszym rozwiązaniem jest wykluczenie kont dostępu awaryjnego z zasad. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md).

## <a name="intune-configuration"></a>Konfiguracja usługi Intune

### <a name="configure-enrollment-status"></a>Konfigurowanie stanu rejestracji

Ważne jest, aby upewnić się, że bezpieczna stacja robocza jest zaufanym czystym urządzeniem. Podczas kupowania nowych urządzeń można określić, że są one fabrycznie ustawione na [system Windows 10 Pro w trybie S](/Windows/deployment/Windows-10-pro-in-s-mode), które ograniczają narażenie na luki w zabezpieczeniach podczas zarządzania łańcuchem dostaw. Po otrzymaniu urządzenia od dostawcy możesz użyć programu autopilotażu, aby zmienić go z trybu S. Poniższe wskazówki zawierają szczegółowe informacje dotyczące stosowania procesu transformacji.

Aby zapewnić, że urządzenia są w pełni skonfigurowane przed użyciem, usługa Intune udostępnia metodę **blokowania użycia urządzenia do momentu zainstalowania wszystkich aplikacji i profilów**.

Z **Azure Portal**:

1. Przejdź do pozycji **Microsoft Intune**  >  **rejestracji urządzeń** Rejestracja  >  **systemu Windows** rejestrowanie  >  **Enrollment Status Page**  >  **Default**  >  **ustawień domyślnych ustawienia** strony.
1. Ustaw opcję **Pokaż postęp instalacji profilu aplikacji** na **wartość tak**.
1. Ustaw opcję **Blokuj użycie urządzenia, dopóki wszystkie aplikacje i profile nie zostaną zainstalowane** na **tak**.

### <a name="create-an-autopilot-deployment-profile"></a>Tworzenie profilu wdrażania rozwiązania Autopilot

Po utworzeniu grupy urządzeń należy utworzyć profil wdrożenia, aby skonfigurować urządzenia autopilotażowe.

W usłudze Intune w Azure Portal:

1. Wybierz pozycję **Rejestrowanie urządzenia**  >  Profile wdrożenia **Rejestracja systemu Windows**  >  **Deployment Profiles**  >  **Utwórz profil**.
1. Wprowadź:

   * Nazwa — **bezpieczny profil wdrożenia stacji roboczej**.
   * Opis — **wdrażanie bezpiecznych stacji roboczych**.
   * Ustaw pozycję **Konwertuj wszystkie urządzenia docelowe na rozwiązanie Autopilot** na **Tak**. To ustawienie gwarantuje, że wszystkie urządzenia na liście zostaną zarejestrowane przy użyciu usługi wdrażania rozwiązania Autopilot. Przetwarzanie rejestracji może potrwać do 48 godzin.

1. Wybierz pozycję **Dalej**.

   * W obszarze **Tryb wdrożenia** wybierz opcję **samodzielne wdrażanie (wersja zapoznawcza)**. Urządzenia z tym profilem są skojarzone z użytkownikiem, który zarejestrował urządzenie. Poświadczenia użytkownika są wymagane do rejestracji urządzenia. Należy pamiętać, że wdrożenie urządzenia w trybie **samodzielnego wdrażania** umożliwi wdrożenie laptopów w modelu udostępnionym. Przypisanie użytkownika nie zostanie wykonane, dopóki urządzenie nie zostanie przypisane do użytkownika po raz pierwszy. W związku z tym wszelkie zasady użytkownika, takie jak funkcja BitLocker, nie będą włączane do momentu ukończenia przypisania użytkownika. Aby uzyskać więcej informacji na temat sposobu logowania się na zabezpieczonym urządzeniu, zobacz [wybrane profile](/intune/device-profile-assign).
   * Pole **Dołącz do usługi Azure AD jako** powinno zawierać **przyłączone do usługi Azure AD** i być wyszarzone.
   * Wybierz język (region), typ konta użytkownika **Standard**. 

1. Wybierz pozycję **Dalej**.

   * Wybierz tag zakresu, jeśli został wstępnie skonfigurowany.

1. Wybierz pozycję **Dalej**.
1. Wybierz pozycję **przypisania**  >  **Przypisz do**  >  **wybranych grup**. W obszarze **Wybierz grupy do dołączenia** wybierz pozycję **bezpieczne stacje robocze**.
1. Wybierz pozycję **Dalej**.
1. Wybierz pozycję **Utwórz**, aby utworzyć profil. Profil wdrażania rozwiązania Autopilot jest teraz dostępny do przypisania do urządzeń.

Rejestracja urządzeń w programie autopilotaż zapewnia różne środowisko użytkownika w oparciu o typ i rolę urządzenia. W naszym przykładowym wdrożeniu przedstawiono model, w którym są wdrażane zbiorczo dane zabezpieczone i można je udostępnić, ale gdy jest używany po raz pierwszy, urządzenie jest przypisane do użytkownika. Aby uzyskać więcej informacji, zobacz [rejestracja urządzeń autopilotażowego usługi Intune](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Konfigurowanie Windows Update

Zachowywanie aktualności systemu Windows 10 jest jedną z najważniejszych rzeczy, które można wykonać. Aby zapewnić obsługę systemu Windows w bezpiecznym stanie, należy wdrożyć pierścień aktualizacji w celu zarządzania tym, w jaki sposób aktualizacje są stosowane do stacji roboczych. 

W tych wskazówkach zaleca się utworzenie nowego pierścienia aktualizacji i zmianę następujących ustawień domyślnych:

W witrynie Azure Portal:

1. Przejdź do **Microsoft Intune**  >  **aktualizacje oprogramowania**—  >  **pierścienie aktualizacji systemu Windows 10**.
1. Wprowadź:

   * Nazwa — **aktualizacje stacji roboczej zarządzanej przez platformę Azure**
   * Kanał obsługi — **niejawny tester systemu Windows — Fast**
   * Odroczenie aktualizacji jakości (dni) — **3**
   * Okres odroczenia aktualizacji funkcji (w dniach) — **3**
   * Zachowanie automatycznej aktualizacji — **automatyczne instalowanie i ponowne uruchamianie bez kontroli użytkownika końcowego**
   * Zablokuj użytkownikowi możliwość wstrzymywania aktualizacji systemu Windows — **blok**
   * Wymagaj zatwierdzenia użytkownika w celu ponownego uruchomienia poza godzinami pracy — **wymagane**
   * Zezwalaj użytkownikowi na ponowne uruchomienie (Zaangażuj ponownie) — **wymagane**
   * Przejście użytkowników do ponownego uruchomienia po ponownym uruchomieniu (dni) — **3**
   * Odłożenie przypomnienia o ponownym uruchomieniu (dni) — **3**
   * Ustaw termin dla oczekujących ponownych uruchomień (w dniach) — **3**

1. Wybierz pozycję **Utwórz**.
1. Na karcie **przypisania** Dodaj grupę **bezpiecznych stacji roboczych** .

Aby uzyskać więcej informacji na temat zasad Windows Update, zobacz [Policy CSP-Update](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integracja usługi Windows Defender ATP w usłudze Intune

Usługa Windows Defender ATP i Microsoft Intune współdziałają ze sobą, aby zapobiec naruszeniom zabezpieczeń. Mogą również ograniczyć wpływ naruszeń. Funkcje ATP zapewniają wykrywanie zagrożeń w czasie rzeczywistym oraz umożliwiają przeprowadzanie obszernej inspekcji i rejestrowania urządzeń końcowych.

Aby skonfigurować integrację usługi Windows Defender ATP i usługi Intune, przejdź do Azure Portal.

1. Przejdź do **Microsoft Intune**  >  **zgodności urządzeń** z usługą  >  **Windows Defender ATP**.
1. W kroku 1 w obszarze **Konfigurowanie programu Windows Defender ATP** wybierz pozycję **Połącz usługę Windows Defender atp, aby Microsoft Intune w Security Center Windows Defender**.
1. W usłudze Windows Defender Security Center:

   1. Wybierz pozycję **Ustawienia** > **Funkcje zaawansowane**.
   1. W obszarze **połączenie Microsoft Intune** wybierz pozycję **włączone**.
   1. Wybierz pozycję **Zapisz preferencje**.

1. Po nawiązaniu połączenia Wróć do usługi Intune i wybierz pozycję **Odśwież** w górnej części.
1. Ustaw pozycję **Połącz urządzenia systemu Windows w wersji 10.0.15063 lub nowszej z usługą Windows Defender ATP** na **Wł.**
1. Wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji, zobacz Usługa [Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Zakończenie ograniczania funkcjonalności profilu stacji roboczej

Aby pomyślnie ukończyć wzmacnianie rozwiązania, Pobierz i wykonaj odpowiedni skrypt. Znajdź linki do pobrania dla żądanego **poziomu profilu**:

| Profil | Lokalizacja pobierania | Nazwa pliku |
| --- | --- | --- |
| Niski poziom zabezpieczeń | NIE DOTYCZY | NIE DOTYCZY |
| Ulepszone zabezpieczenia | https://aka.ms/securedworkstationgit | Ulepszona stacja robocza-Windows10-(1809). ps1 |
| Wysoki poziom zabezpieczeń | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809). ps1 |
| Wyspecjalizowany | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline.ps1 |
| Specjalna zgodność * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10 (1803). ps1 |
| Zabezpieczone | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809) -SecurityBaseline.ps1 |

\* Wyspecjalizowana zgodność to skrypt, który wymusza wyspecjalizowaną konfigurację podaną w NCSC Windows10 SecurityBaseline.

Po pomyślnym wykonaniu skryptu można wprowadzać aktualizacje profilów i zasad w usłudze Intune. Skrypty rozszerzonego i bezpiecznego profilu tworzą zasady i profile, ale należy je przypisać do grupy urządzeń **bezpiecznych stacji roboczych** .

* Poniżej znajdują się informacje o profilach konfiguracji urządzeń w usłudze Intune tworzonych przez skrypty: **Azure Portal**  >  **Microsoft Intune**  >  **profile konfiguracji urządzeń**  >  **Profiles**.
* Tutaj można znaleźć zasady zgodności urządzeń w usłudze Intune utworzone przez skrypty: **Azure Portal**  >  **Microsoft Intune**  >  **zasady zgodności urządzeń**  >  **Policies**.

Aby zapoznać się ze zmianami wprowadzonymi przez skrypty, można wyeksportować profile. W ten sposób można określić dodatkowe zabezpieczenia, które mogą być wymagane, jak opisano w [dokumentacji SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Uruchom skrypt eksportu danych usługi Intune `DeviceConfiguration_Export.ps1` z [repozytorium DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) , aby wyeksportować wszystkie bieżące profile usługi Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Dodatkowe konfiguracje i Ograniczanie funkcjonalności do rozważenia

Postępując zgodnie ze wskazówkami w tym miejscu, wdrożono bezpieczną stację roboczą. Należy jednak również rozważyć dodatkowe kontrole. Na przykład:

* Ograniczanie dostępu do przeglądarek alternatywnych
* Zezwalaj na wychodzące HTTP
* Blokuj Wybieranie witryn sieci Web
* Ustawianie uprawnień do uruchamiania niestandardowych skryptów programu PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ustawianie reguł w dostawcy usługi konfiguracji zapory (CSP)

Możesz wprowadzić dodatkowe zmiany w zakresie zarządzania regułami ruchu przychodzącego i wychodzącego zgodnie z potrzebami dla dozwolonych i zablokowanych punktów końcowych. W miarę zwiększania funkcjonalności bezpiecznej stacji roboczej można zmniejszyć ograniczenie, które odmówi cały ruch przychodzący i wychodzący. Możesz dodać dozwolone Lokacje wychodzące w celu uwzględnienia wspólnych i zaufanych witryn sieci Web. Aby uzyskać więcej informacji, zobacz [usługa konfiguracji zapory](/Windows/client-management/mdm/firewall-csp).

Ograniczenia dotyczące zarządzania ruchem URL obejmują:

* Odrzuć cały ruch przychodzący w skrypcie profilu zabezpieczonej stacji roboczej.
* Odrzuć wszystkie wychodzące z wyjątkiem wybranych usług Azure i Microsoft, w tym Azure Cloud Shell i możliwość zezwalania na Resetowanie hasła platformy Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Jeśli chcesz zapewnić większy stopień szczegółowości reguł blokowania, możesz odwołać się do projektu Spamhaus, który utrzymuje [listę zablokowanych domen (DWUrzędnych)](https://www.spamhaus.org/dbl/): dobry zasób do użycia jako zaawansowany zestaw reguł do zaimplementowania w celu blokowania lokacji.

### <a name="manage-local-applications"></a>Zarządzanie aplikacjami lokalnymi

Bezpieczna stacja robocza przechodzi do naprawdę zaostrzonego stanu podczas usuwania aplikacji lokalnych, w tym aplikacji biurowych. W tym miejscu możesz dodać program Chrome jako domyślną przeglądarkę i użyć usługi Intune w celu ograniczenia możliwości modyfikowania przeglądarki i jej wtyczek przez użytkownika.

#### <a name="deploy-applications-using-intune"></a>Wdrażanie aplikacji przy użyciu usługi Intune

W niektórych sytuacjach aplikacje takie jak przeglądarka Google Chrome są wymagane na zabezpieczonej stacji roboczej. Poniższy przykład zawiera instrukcje dotyczące instalowania programu Chrome na urządzeniach z **bezpiecznymi stacjami roboczymi** grupy zabezpieczeń.

1. Pobierz pakiet Chrome Instalatora w trybie offline [dla systemu Windows 64-bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Wyodrębnij pliki i zanotuj lokalizację `GoogleChromeStandaloneEnterprise64.msi` pliku.
1. W **Azure Portal** przejdź do **Microsoft Intune**  >  **Client apps**  >  **aplikacje** klienckie aplikacje  >  **Dodaj**.
1. W obszarze **Typ aplikacji** wybierz **pozycję branżowe**.
1. W obszarze **plik pakietu aplikacji** wybierz `GoogleChromeStandaloneEnterprise64.msi` plik z wyodrębnionej lokalizacji i wybierz **przycisk OK**.
1. W obszarze **Informacje o aplikacji** Podaj opis i wydawcę. Wybierz pozycję **OK**.
1. Wybierz pozycję **Dodaj**.
1. Na karcie **przypisania** wybierz pozycję **dostępne dla zarejestrowanych urządzeń** w obszarze **Typ przypisania**.
1. W obszarze **dołączone grupy** Dodaj grupę **bezpiecznych stacji roboczych** .
1. Wybierz przycisk **OK**, a następnie wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat konfigurowania ustawień programu Chrome, zobacz [Zarządzanie przeglądarką Chrome przy użyciu Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurowanie portalu firmy dla aplikacji niestandardowych

W trybie zabezpieczonym instalacja aplikacji jest ograniczona do portalu firmy usługi Intune. Jednak zainstalowanie portalu wymaga dostępu do Microsoft Store. W zabezpieczonym rozwiązaniu Portal firmy można udostępnić wszystkim urządzeniom w trybie offline.

Kopia [Portal firmy](/Intune/store-apps-company-portal-app) zarządzana przez usługę Intune zapewnia dostęp do dodatkowych narzędzi, które można wypchnąć do użytkowników bezpiecznych stacji roboczych.

Może być konieczne zainstalowanie aplikacji systemu Windows 32-bit lub innych aplikacji, których wdrożenie wymaga specjalnych przygotowań. W takich przypadkach [Narzędzie przygotowywania zawartości Win32 firmy Microsoft](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) może dostarczyć gotowy do użycia `.intunewin` plik formatu na potrzeby instalacji.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Dostęp warunkowy umożliwiający dostęp do Azure Portal tylko zabezpieczonej stacji roboczej.

Usługa Azure AD oferuje możliwość zarządzania i ograniczania, kto i co może uzyskać dostęp do portalu zarządzania chmurą platformy Azure. Włączenie [dostępu warunkowego](../conditional-access/overview.md) gwarantuje, że tylko bezpieczna stacja robocza będzie mogła zarządzać zasobami lub zmieniać je. Należy pamiętać, że podczas wdrażania tej funkcji należy wziąć pod uwagę, jeśli funkcja [dostępu awaryjnego](../roles/security-emergency-access.md) może lub powinna być używana tylko w przypadku ekstremalnych przypadków i konta zarządzanego za pomocą zasad.

> [!NOTE]
> Należy utworzyć grupę użytkowników i dołączyć użytkownika awaryjnego, który będzie mógł obejść zasady dostępu warunkowego. Naszym Przykładem mamy grupę zabezpieczeń o nazwie **BreakGlass awaryjne**

1. Przejdź do **Azure Portal**  >  **Microsoft Intune**  >  **zasady dostępu warunkowego —**  >  **nowe zasady**.
1. Podaj **nazwę** zasad.
1. Wybierz **użytkowników i grupy**  >  **Wybierz pozycję Użytkownicy i** grupy 
1. Wybierz pozycję **Uwzględnij**  >  **role katalogu** > wybierz role > Administrator globalny, administrator ról uprzywilejowanych, administrator uwierzytelniania uprzywilejowanego, administrator zabezpieczeń, administrator zgodności, administrator dostępu warunkowego, administrator aplikacji, administrator aplikacji w chmurze, administrator usługi Intune
1. Wybierz pozycję **Wyklucz** > wybierz pozycję **użytkownicy i grupy** > wybierz pozycję **wybierz wykluczonych użytkowników** , > wybierz grupę **BreakGlass awaryjnych** .
1. Wybierz **aplikacje w chmurze lub akcje** > wybierz **wszystkie aplikacje w chmurze**
1. Wybierz pozycję **warunki** > wybierz pozycję **platformy urządzeń** > wybierz pozycję Konfiguruj **tak** > wybierz pozycję **Wybierz platformy urządzeń** wybierz pozycję **Windows** .
1. Wybierz pozycję **Kontrola dostępu** > wybierz pozycję **udziel dostępu** **tak** > wybierz pozycję **Wymagaj, aby urządzenie było oznaczone jako zgodne**. 
1. Wybierz pozycję **Włącz zasady**  >  **na**
 
Ten zestaw zasad pozwala upewnić się, że administratorzy muszą korzystać z zgodnego urządzenia z systemem Windows, które jest ustawiane przez usługę Intune i WDATP. 

Organizacje powinny również rozważyć zablokowanie starszych protokołów uwierzytelniania w swoich środowiskach. Istnieje wiele sposobów wykonania tego zadania. Aby uzyskać więcej informacji na temat blokowania starszych protokołów uwierzytelniania, zobacz artykuł [Jak zablokować starsze uwierzytelnianie w usłudze Azure AD przy użyciu dostępu warunkowego](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Tworzenie ustawień niestandardowych przy użyciu programu PowerShell

Możesz również użyć programu PowerShell, aby zwiększyć możliwości zarządzania hostami. Ten przykładowy skrypt konfiguruje domyślne tło na hoście. Jest to możliwość dostępna również za pomocą Azure Portal i profilów. Przykładowy skrypt służy tylko do zilustrowania funkcjonalności programu PowerShell.

Może być konieczne skonfigurowanie niektórych niestandardowych kontrolek i ustawień na bezpiecznych stacjach roboczych. Ten przykład zmienia tło stacji roboczej za pomocą programu PowerShell w celu łatwego identyfikowania urządzenia jako gotowej do użycia, bezpiecznej stacji roboczej.

Skrypt [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z centrum skryptów firmy Microsoft umożliwia systemowi Windows załadowanie tego [bezpłatnego, ogólnego obrazu tła](https://i.imgur.com/OAJ28zO.png) podczas uruchamiania.

1. Pobierz skrypt na urządzenie lokalne.
1. Zaktualizuj customerXXXX i lokalizację pobierania obrazu tła. W naszym przykładzie zamienimy customerXXXX na tło.
1. Przejdź do **Azure Portal**  >  **Microsoft Intune**  >  **Device configuration**  >  **skrypt programu PowerShell** Konfiguracja urządzenia  >  **Add**.
1. Podaj **nazwę** skryptu i określ **lokalizację skryptu**.
1. Wybierz pozycję **Konfiguruj**.
   1. Ustaw opcję **Uruchom ten skrypt przy użyciu poświadczeń zalogowanych** na **wartość tak**.
   1. Wybierz pozycję **OK**.
1. Wybierz pozycję **Utwórz**.
1. Wybierz pozycję **przypisania**  >  **Wybierz pozycję grupy**.
   1. Dodaj do grupy zabezpieczeń **bezpieczne stacje robocze**.
   1. Wybierz pozycję **Zapisz**.

## <a name="enroll-and-validate-your-first-device"></a>Rejestrowanie i weryfikowanie pierwszego urządzenia

1. Aby zarejestrować urządzenie, potrzebne są następujące informacje:
   * **Numer seryjny** — znaleziono w obudowie urządzenia.
   * **Identyfikator produktu systemu Windows** — znajduje się w obszarze **system**  >  **— informacje** z menu Ustawienia systemu Windows.
   * Można uruchomić [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) , aby pobrać plik skrótu CSV ze wszystkimi wymaganymi informacjami na potrzeby rejestracji urządzeń.
   
     Uruchom, `Get-WindowsAutoPilotInfo – outputfile device1.csv` Aby uzyskać informacje wyjściowe w postaci pliku CSV, który można zaimportować do usługi Intune.

     > [!NOTE]
     > Skrypt wymaga podniesionych uprawnień. Jest on uruchamiany jako element zdalny ze znakiem. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`Polecenie umożliwia poprawne działanie skryptu.

   * Te informacje można zebrać, logując się do urządzenia z systemem Windows 10 w wersji 1809 lub nowszej. Odsprzedawca sprzętu może również dostarczyć te informacje.
1. W **Azure Portal** przejdź do pozycji **Microsoft Intune**  >  **Rejestrowanie urządzenia**  >  **Rejestracja systemu Windows**  >  **urządzenia — zarządzanie urządzeniami z systemem Windows**.
1. Wybierz pozycję **Importuj** i wybierz plik CSV.
1. Dodaj urządzenie do grupy zabezpieczeń **bezpieczne stacje robocze** .
1. Na urządzeniu z systemem Windows 10, które chcesz skonfigurować, przejdź do pozycji **Ustawienia systemu Windows**  >  **Aktualizacja & zabezpieczenia**  >  **Recovery**.
   1. Wybierz pozycję **Rozpocznij** w obszarze **Zresetuj ten komputer**.
   1. Postępuj zgodnie z monitami, aby zresetować i zmienić konfigurację urządzenia przy użyciu skonfigurowanych profilów i zasad zgodności.

Po skonfigurowaniu urządzenia wykonaj przegląd i sprawdź konfigurację. Przed kontynuowaniem wdrażania upewnij się, że pierwsze urządzenie jest skonfigurowane prawidłowo.

## <a name="assign-devices"></a>Przypisywanie urządzeń

Aby przypisać urządzenia i użytkowników, należy zmapować [wybrane profile](/intune/device-profile-assign) do grupy zabezpieczeń. Wszyscy nowi użytkownicy, którzy wymagają uprawnień do usługi, muszą zostać dodani również do grupy zabezpieczeń.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Monitorowanie i reagowanie na zdarzenia związane z bezpieczeństwem za pomocą programu wskaźnikowego i usługi Windows Defender ATP

Monitorowanie wdrożenia bezpiecznej stacji roboczej można osiągnąć, włączając [Wskaźnikowanie] i wykorzystując ochronę przed [zagrożeniami i lukami w zabezpieczeniach](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) , wskazówki nie zapewniają pełnego polowania zagrożeń, ale zapewniają dobrą częstość monitorowania i reagowania na potencjalne zdarzenia związane z bezpieczeństwem.

Będziemy używać **platformy Azure** , aby: 

* Zbieranie danych z usług Intune, Azure Portal i Azure AD na potrzeby monitorowania użytkowników i urządzeń
* Pomóż zbadać podejrzane działania związane z konfiguracją użytkowników i urządzeń
* I umożliwianie eksplorowania dochodzeń w zakresie zabezpieczeń przy użyciu WDATP

Monitorowanie wskaźnikowe wymaga skonfigurowania łączników ze źródłami danych, takimi jak Azure AD.

1. W **Azure Portal** przejdź do **usługi Azure wskaźnikowej (wersja zapoznawcza)** > wybierz pozycję **Dodaj** .
1. W **obszarze Wybierz obszar roboczy do dodania do platformy Azure wskaźnik** wyboru **Utwórz nowy obszar roboczy**
1. Wprowadź:
   * **Log Analytics obszar roboczy** — "bezpieczne monitorowanie stacji roboczej"
   * **Subskrypcja** — wybierz aktywną subskrypcję
   * **Grupa zasobów** — wybierz pozycję * * Utwórz nową * * > Secure Workstation RG > **OK**
   * **Lokalizacja** — wybierz lokalizację geograficzną najlepiej dopasowaną do wdrożenia
   * **Warstwa cenowa** — wybór **za GB (2018)**
1. Wybierz przycisk **OK**.

Następnie będziemy łączyć dostępne źródła danych bezpiecznych stacji roboczych z monitorowaniem.

1. W **Azure Portal** przejdź do **obszaru roboczego wskaźnik platformy Azure** , > wybierz pozycję **bezpieczna stacja robocza monitorowanie** obszaru roboczego
1. Wybieranie **łączników danych**
1. Po przejrzeniu wymagań wstępnych wybierz **Azure Active Directory** > Otwórz stronę łącznika >. W obszarze Konfiguracja wybierz opcję **Połącz** zarówno w przypadku dzienników logowania usługi Azure AD, jak i dzienników inspekcji usługi Azure AD.
1. Wybierz pozycję **Azure Activity** > Otwórz stronę łącznika > po przejrzeniu wymagania wstępnego. Aby skonfigurować dzienniki aktywności platformy Azure, > wybierz subskrypcję > wybierz pozycję **Połącz** .

Jako że dane są zbierane przez wskaźnik kontrolny, można obserwować aktywność, wybierając pozycję **Azure Portal**, przejdź do **omówienia usługi Azure wskaźnikowej** 

Będziemy używać programu **Windows Defender ATP (WDATP)** do:

* Ciągle Obserwuj i monitoruj luki w zabezpieczeniach oraz nieprzerwane konfiguracje
* Użyj WDATP, aby określić priorytety dynamicznych zagrożeń w dziku
* Stacja korelacji luk w zabezpieczeniach z alertami wykrywania i reagowania punktów końcowych (EDR)
* Korzystanie z pulpitu nawigacyjnego do identyfikowania luk w zabezpieczeniach na poziomie komputera podczas badań
* Wypychanie korygowania do usługi Intune

Skonfiguruj [pulpit nawigacyjny programu Defender ATP](https://securitycenter.windows.com/machines). Korzystanie z wskazówek na temat [& pulpitu nawigacyjnego zarządzania lukami w zabezpieczeniach](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitorowanie aktywności aplikacji przy użyciu Microsoft Monitoring Agent (MMA)
Począwszy od wyspecjalizowanej stacji roboczej, funkcja blokowania aplikacji umożliwia monitorowanie aktywności aplikacji na stacji roboczej. Aby można było zintegrować monitorowanie w obszarze roboczym Log Analytics, należy postępować zgodnie z MMA agentem i konfiguracją. 

> [!NOTE]
> Profil wyspecjalizowanej stacji roboczej zawiera zasady monitorowania funkcji AppLocker. Wdrożenie zasad jest wymagane do monitorowania aktywności aplikacji na kliencie

Wdrażanie agenta MMA przy użyciu skryptu programu PowerShell usługi Intune

1. Pobierz skrypt instalacyjny [na urządzenie lokalne](https://aka.ms/securedworkstationgit).
1. Zaktualizuj parametry, **$WorkSpaceID** i **$WorkSpaceKey**
1. Przejdź do **Azure Portal**  >  **Microsoft Intune**  >  **Device configuration**  >  **skrypt programu PowerShell** Konfiguracja urządzenia  >  **Add**.
1. Podaj **nazwę** skryptu i określ **lokalizację skryptu**.
1. Wybierz pozycję **Konfiguruj**.
   1. Ustaw opcję **Uruchom ten skrypt przy użyciu poświadczeń zalogowanych** na **wartość tak**.
   1. Wybierz pozycję **OK**.
1. Wybierz pozycję **Utwórz**.
1. Wybierz pozycję **przypisania**  >  **Wybierz pozycję grupy**.
   1. Dodaj do grupy zabezpieczeń **bezpieczne stacje robocze**.
   1. Wybierz pozycję **Zapisz**.

Następnie należy skonfigurować Log Analytics, aby otrzymywać nowe dzienniki
1. W **Azure Portal** przejdź do **log Analytics obszarze roboczym** > wybierz pozycję "monitorowanie bezpiecznych stacji roboczych"
1. Wybieranie **ustawień zaawansowanych**  >  **dane**  >  **dzienników zdarzeń systemu Windows**
1. W obszarze **Zbierz zdarzenia z następujących dzienników zdarzeń** 
1. Wprowadź:
   * "Microsoft-Windows-AppLocker/EXE i DLL" **> usunąć zaznaczenia**
   * "Microsoft-Windows-AppLocker/MSI i skrypt" > nie wybierać **informacji**
   * "Microsoft-Windows-AppLocker/spakowane App-Deployment" > nie wybierać **informacji**
   * "Microsoft-Windows-AppLocker/spakowane App-Execution" **> usunąć zaznaczenia**
1. Wybierz pozycję **Zapisz**

Rejestrowanie aplikacji będzie dostępne w wybranym obszarze roboczym Log Analytics.

## <a name="monitoring"></a>Monitorowanie

* Dowiedz się, jak [wykrywać zagrożenia za pomocą platformy Azure — wskaźnik](../../sentinel/tutorial-detect-threats-built-in.md)
* [Zbadaj zdarzenia za pomocą platformy Azure — wskaźnik](../../sentinel/tutorial-investigate-cases.md)
* [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](../../sentinel/tutorial-respond-threats-playbook.md)
* Informacje o przeglądaniu [oceny ekspozycji](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Przejrzyj [rekomendacje dotyczące zabezpieczeń](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Zarządzanie [korygowaniem](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) zabezpieczeń
* Zarządzanie [wykrywaniem i odpowiedzią punktu końcowego](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitoruj profile przy użyciu [monitorowania profilu usługi Intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Microsoft Intune](/intune/index).
* Informacje o [usłudze Azure AD](../index.yml).
* Korzystanie z [zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Odkryj [wskaźnik na platformie Azure](../../sentinel/index.yml)