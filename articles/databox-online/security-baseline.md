---
title: Linia bazowa zabezpieczeń Azure dla Azure Stack Edge
description: Linia bazowa zabezpieczeń Azure Stack Edge zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 460fd66ed4651248639334caa55eb8facbce866d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452377"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Linia bazowa zabezpieczeń Azure dla Azure Stack Edge

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Microsoft Azure Stack Edge. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Stack Edge. **Kontrolki** niemające zastosowania do Azure Stack krawędzi zostały wykluczone.

Aby zobaczyć, jak usługa Azure Stack Edge całkowicie mapuje na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny Azure Stack pliku mapowania linii bazowej zabezpieczeń Edge](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: klienci wdrażają w swojej sieci prywatnej urządzenie z systemem Azure Stack, które jest używane przez firmę Microsoft, na potrzeby dostępu wewnętrznego, a także mają możliwość zapewnienia dalszej ochrony. Na przykład urządzenie Azure Stack Edge jest dostępne za pośrednictwem sieci wewnętrznej klienta i wymaga adresu IP skonfigurowanego przez klienta. Ponadto w celu uzyskania dostępu do interfejsu użytkownika urządzenia jest wybierane hasło dostępu. 

Ruch wewnętrzny jest bardziej zabezpieczony przez:

- Transport Layer Security (TLS) w wersji 1,2 jest wymagana do zarządzania Azure Portal i zestawu SDK urządzenia Azure Stack Edge.

- Każdy dostęp klienta do urządzenia odbywa się za pośrednictwem lokalnego interfejsu użytkownika sieci Web przy użyciu standardu TLS 1,2 jako domyślnego protokołu SSL.

- Dołączenie do usługi Azure Stack Edge, która ma zostać utworzona przez klienta w ramach subskrypcji platformy Azure, jest dozwolone tylko w przypadku autoryzowanego urządzenia Azure Stack Edge.

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.
 
- [Konfigurowanie protokołu TLS 1,2 na klientach z systemem Windows uzyskujących dostęp do urządzenia Azure Stack Edge](azure-stack-edge-j-series-configure-tls-settings.md)

- [Szybki Start — wprowadzenie do Azure Stack EDGE Pro z procesorem GPU](azure-stack-edge-gpu-quickstart.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem

**Wskazówki**: klienci mogą wybrać wirtualną sieć prywatną (VPN) typu punkt-lokacja, aby połączyć urządzenie z systemem Azure Stack Edge z lokalnej sieci prywatnej z siecią platformy Azure. Sieć VPN zapewnia drugą warstwę szyfrowania na potrzeby ruchu danych w warstwie transportowej z urządzenia klienta na platformę Azure. 

Klienci mogą skonfigurować wirtualną sieć prywatną na swoich Azure Stack urządzeniach brzegowych za pośrednictwem Azure Portal lub za pośrednictwem Azure PowerShell.

- [Konfigurowanie sieci VPN platformy Azure za pośrednictwem skryptu Azure PowerShell dla Azure Stack brzeg Pro R i Azure Stack Edge mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Konfigurowanie protokołu TLS 1,2 na klientach z systemem Windows uzyskujących dostęp do urządzenia Azure Stack Edge](azure-stack-edge-j-series-configure-tls-settings.md)

- [Samouczek: Konfigurowanie certyfikatów dla Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Ustanów dostęp w sieci prywatnej do usług platformy Azure

**Wskazówki**: klienci mogą wybrać wirtualną sieć prywatną (VPN) typu punkt-lokacja, aby połączyć urządzenie z systemem Azure Stack Edge z lokalnej sieci prywatnej z siecią platformy Azure. Sieć VPN zapewnia drugą warstwę szyfrowania na potrzeby ruchu danych w warstwie transportowej z urządzenia klienta na platformę Azure. 

- [Konfigurowanie sieci VPN platformy Azure za pośrednictwem skryptu Azure PowerShell dla Azure Stack brzeg Pro R i Azure Stack Edge mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Konfigurowanie protokołu TLS 1,2 na klientach z systemem Windows uzyskujących dostęp do urządzenia Azure Stack Edge](azure-stack-edge-j-series-configure-tls-settings.md)

- [Samouczek: Konfigurowanie certyfikatów dla Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

**Wskazówki**: urządzenie z Azure Stack Edge obejmuje standardowe funkcje ochrony sieci systemu Windows Server, które nie są konfigurowane przez klientów.

Klienci mogą zdecydować się na zabezpieczenie swojej sieci prywatnej połączonej z urządzeniem Azure Stack Edge przed atakami zewnętrznymi przy użyciu sieciowego urządzenia wirtualnego, takiego jak Zapora z zaawansowanymi zabezpieczeniami rozproszonymi typu "odmowa usługi" (DDoS).

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki**: punkty końcowe używane przez Azure Stack urządzenia brzegowego są zarządzane przez firmę Microsoft. Klienci są odpowiedzialni za wszelkie dodatkowe kontrole, które chcą wdrożyć w swoich systemach lokalnych.

Urządzenie Azure Stack Edge używa własnych funkcji wykrywania wtargnięcia do ochrony usługi. 

- [Informacje o zabezpieczeniach Azure Stack Edge](azure-stack-edge-security.md)

- [Informacje o porcie dla Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md)

- [Pobieranie dzienników wykrywania wtargnięcia sprzętu i oprogramowania](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

**Wskazówki**: wszystkie Azure Stack urządzenia brzegowe automatycznie mają tożsamość zarządzaną przypisaną przez system w Azure Active Directory (Azure AD). Obecnie zarządzana tożsamość służy do zarządzania chmurą maszyn wirtualnych hostowanych na Azure Stack Edge.

Azure Stack urządzenia brzegowe uruchamiają się w stanie zablokowanym na potrzeby dostępu lokalnego. W przypadku konta administratora urządzenia lokalnego należy nawiązać połączenie z urządzeniem za pomocą lokalnego interfejsu użytkownika sieci Web lub interfejsu programu PowerShell i ustawić silne hasło. Przechowuj poświadczenia administratora urządzenia i zarządzaj nimi w bezpiecznej lokalizacji, takiej jak Azure Key Vault, i obróć hasło administratora zgodnie ze standardami organizacji.

- [Ochrona Azure Stack urządzenia brzegowego za pomocą hasła](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: Logowanie jednokrotne nie jest obsługiwane w przypadku urządzeń z punktami końcowymi Azure Stack Edge. Można jednak włączyć logowanie jednokrotne w standardowym Azure Active Directory (Azure AD), aby zabezpieczyć dostęp do zasobów w chmurze platformy Azure.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: uwierzytelnianie wieloskładnikowe jest silną kontrolą uwierzytelniania, ale funkcją wyboru dla użytkowników usługi Azure Stack Edge. Można go wykorzystać w przypadku obsługiwanych scenariuszy, takich jak zarządzanie urządzeniami brzegowymi Azure Stack Edge na Azure Portal. Należy zauważyć, że lokalny dostęp do Azure Stack urządzeń brzegowych nie obsługuje uwierzytelniania wieloskładnikowego.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: Włącz Azure monitor, aby zebrać dzienniki urządzeń lub kontenerów dla usługi Azure Stack Edge. Monitoruj kontenery, takie jak uruchamianie wielu aplikacji obliczeniowych w klastrze Kubernetes na urządzeniu.

Ponadto pakiet pomocy technicznej, który obejmuje dzienniki inspekcji, można zbierać przy użyciu lokalnego interfejsu użytkownika sieci Web urządzenia brzegowego Azure Stack. Należy pamiętać, że pakiet pomocy technicznej nie jest dostępny w Azure Portal.
 
- [Włączanie Azure Monitor na urządzeniu z Azure Stack EDGE Pro](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Zbierz pakiet pomocy technicznej](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: usługa Azure Active Directory (Azure AD) dostęp warunkowy to funkcja opcjonalna do uwierzytelniania w usłudze Azure Stack Edge. Usługa Edge Azure Stack jest zasobem w Azure Portal, który umożliwia zarządzanie urządzeniem z systemem Azure Stack Edge w różnych lokalizacjach geograficznych. 

- [Co to jest dostęp warunkowy](../active-directory/conditional-access/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminowanie niezamierzonego ujawnienia poświadczeń

**Wskazówki**: Postępuj zgodnie z najlepszymi rozwiązaniami w zakresie ochrony poświadczeń, takich jak:

- Klucz aktywacji używany do aktywowania urządzenia przy użyciu zasobu brzegowego Azure Stack na platformie Azure.
- Poświadczenia logowania w celu uzyskania dostępu do urządzenia brzegowego Azure Stack.
- Pliki kluczy, które mogą ułatwić odzyskiwanie Azure Stack urządzeniach brzegowych.
- Klucz szyfrowania kanału

Regularnie Obróć i Synchronizuj klucze konta magazynu, aby chronić konto magazynu przed nieautoryzowanymi użytkownikami.

- [Azure Stack brzeg Pro zabezpieczenia i ochrona danych](azure-stack-edge-security.md)

- [Synchronizowanie kluczy magazynu](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: Azure Stack rozwiązanie brzegowe ma kilka składników z silnymi kontrolami dostępu, aby ograniczyć dostęp do urządzeń o krytycznym znaczeniu dla firmy. Aby można było skonfigurować urządzenie i zarządzać nim, organizacja będzie potrzebować Enterprise Agreement (EA) lub dostawcy rozwiązań w chmurze (CSP) lub subskrypcji Dostęp sponsorowany Microsoft Azure: 

Usługa Edge Azure Stack jest chroniona przez funkcje zabezpieczeń platformy Azure jako usługa zarządzania hostowana na platformie Azure. Klucz szyfrowania zasobu można uzyskać we właściwościach urządzenia dla każdej operacji zarządzania zestawem SDK, ale można wyświetlić klucz szyfrowania tylko wtedy, gdy masz odpowiednie uprawnienia do zasobu interfejs API programu Graph.

Urządzenie Azure Stack EDGE Pro to urządzenie lokalne, które pomaga przekształcić dane, przetwarzając je lokalnie, a następnie wysyłając je na platformę Azure. Twoje urządzenie:

- potrzebuje klucza aktywacji, aby uzyskać dostęp do usługi Azure Stack Edge.
- jest chronione przez cały czas przy użyciu hasła urządzenia.
- ma włączony bezpieczny rozruch.

- jest urządzeniem zablokowanym. Kontroler zarządzania płytą główną (BMC) urządzenia i system BIOS są chronione hasłem. System BIOS jest chroniony przez ograniczony dostęp użytkownika.
- uruchamia funkcję Windows Defender Device Guard. Funkcja Device Guard pozwala uruchamiać tylko zaufane aplikacje zdefiniowane w ramach zasad integralności kodu.

Postępuj zgodnie z najlepszymi rozwiązaniami, aby chronić wszystkie poświadczenia i wpisy tajne używane do uzyskiwania dostępu do Azure Stack Edge. 

- [Najlepsze rozwiązania dotyczące haseł](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki**: Azure Stack Edge ma użytkownika o nazwie "EdgeUser", który może skonfigurować urządzenie. Ma także Azure Resource Manager użytkownika "EdgeArmUser" dla funkcji lokalnych Azure Resource Manager na urządzeniu. 

Należy przestrzegać najlepszych rozwiązań związanych z ochroną:

- Poświadczenia używane do uzyskiwania dostępu do urządzenia lokalnego

- Poświadczenia udziału SMB.

- Dostęp do systemów klienckich, które zostały skonfigurowane do używania udziałów NFS.

- Klucze konta magazynu lokalnego używane do uzyskiwania dostępu do kont magazynu lokalnego podczas korzystania z interfejsu API REST usługi BLOB.

Dodatkowe informacje są dostępne w przywoływanym łączu.

- [Informacje na temat zabezpieczeń urządzeń brzegowych Azure Stack](azure-stack-edge-security.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

**Wskazówki**: zabezpieczone i izolowane stacje robocze są krytycznie ważne dla bezpieczeństwa poufnych ról, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. Używaj wysoce zabezpieczonych stacji roboczych użytkowników z usługą Azure bastionu lub bez niej na potrzeby zadań administracyjnych. Użyj Azure Active Directory (Azure AD), usługi Microsoft Defender Advanced Threat Protection (ATP) i Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. 

Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe punkty odniesienia, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](/security/compass/privileged-access-deployment)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

**Wskazówki**: Azure Stack Edge traktuje wszystkie dane interaktywne jako poufne tylko dla autoryzowanych użytkowników mających dostęp do tych danych. Należy przestrzegać najlepszych rozwiązań, aby chronić poświadczenia używane do uzyskiwania dostępu do usługi Azure Stack Edge.

- [Azure Stack brzeg Pro zabezpieczenia i ochrona danych](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfrowanie poufnych informacji podczas przesyłania

**Wskazówki**: Azure Stack Edge używa bezpiecznego kanału do danych w locie. Są to:

- Standard TLS 1,2 służy do przesyłania danych między urządzeniem a chmurą platformy Azure. Nie istnieje powrót do protokołu TLS 1,1 i jego wcześniejszych wersji. Komunikacja agentów zostanie zablokowana, jeśli protokół TLS 1,2 nie jest obsługiwany. Protokół TLS 1,2 jest również wymagany w przypadku zarządzania Azure Portal i zestawu SDK.

- Gdy klienci uzyskują dostęp do urządzenia za pomocą lokalnego interfejsu użytkownika sieci Web w przeglądarce, standard TLS 1,2 jest używany jako domyślny protokół Secure

Najlepszym rozwiązaniem jest skonfigurowanie przeglądarki do korzystania z protokołu TLS 1,2. Użyj protokołu SMB 3,0 z szyfrowaniem, aby chronić dane podczas kopiowania ich z serwerów danych.

- [Najlepsze rozwiązania w zakresie ochrony danych w locie](azure-stack-edge-security.md#protect-data-in-flight)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Należy pamiętać, że do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: tylko autoryzowani użytkownicy mogą na przykład uzyskać dostęp do Azure Stack interfejsów API urządzenia brzegowego za pośrednictwem Azure Resource Manager lokalnego. Hasła kont użytkowników można zarządzać tylko w Azure Portal. 

- [Ustawianie hasła usługi Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Używaj tylko zatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: można wprowadzić własne aplikacje do uruchamiania na wszystkich lokalnie utworzonych maszynach wirtualnych. Użyj skryptów programu PowerShell do tworzenia lokalnych maszyn wirtualnych obliczeniowych na urządzeniu brzegowym stosu. Zdecydowanie zalecamy, aby można było uruchamiać tylko zaufane aplikacje na lokalnych maszynach wirtualnych. 

- [Jak kontrolować wykonywanie skryptu programu PowerShell w środowisku systemu Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: Azure Stack Edge nie oferuje możliwości wykrywania zagrożeń. Klienci mogą jednak zbierać dzienniki inspekcji w pakiecie pomocy technicznej w celu wykrywania i analizowania zagrożeń w trybie offline. 

- [Zbierz pakiet pomocy technicznej dla Azure Stack urządzenia brzegowego](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania działań sieci platformy Azure

**Wskazówki**: Azure Stack Edge ma włączone dzienniki inspekcji sieci w ramach pakietu pomocy do pobrania. Te dzienniki można analizować w celu zaimplementowania monitorowania w czasie rzeczywistym dla Azure Stack urządzeń brzegowych.

- [Azure Stack Edge Zbierz pakiet pomocy technicznej](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włącz rejestrowanie zasobów platformy Azure

**Wskazówki**: monitorowanie w czasie rzeczywistym za pomocą dzienników nie jest obecnie obsługiwane w przypadku Azure Stack Edge. Istnieje możliwość zebrania pakietu dla pomocy technicznej, który umożliwia analizowanie różnych zawartych w nim dzienników, takich jak zapora, oprogramowanie, nieautoryzowanie sprzętowe i dzienniki zdarzeń systemu na potrzeby Azure Stack urządzenia brzegowego Pro. Zwróć uwagę na to, że dla ruchu przychodzącego i wychodzącego są zbierane programowe lub domyślne Dzienniki zapory.

- [Zbierz pakiet pomocy technicznej dla Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: monitorowanie w czasie rzeczywistym za pomocą dzienników nie jest obecnie obsługiwane w przypadku Azure Stack Edge. Można jednak zebrać pakiet pomocy technicznej, który umożliwia analizowanie różnych uwzględnionych w niej dzienników.  Pakiet pomocy technicznej jest kompresowany i pobierany do wybranej ścieżki. Rozpakuj pakiet i Wyświetl znajdujące się w nim pliki dziennika systemowego. Możesz również wysłać te dzienniki do narzędzia do zarządzania zdarzeniami informacji o zabezpieczeniach lub innej centralnej lokalizacji magazynu na potrzeby analizy.

- [Zbierz pakiet pomocy technicznej dla Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: nie można zmienić okresu przechowywania magazynu dzienników na urządzeniu brzegowym Azure Stack. Starsze dzienniki są przeczyszczane w razie konieczności. Można zbierać pakiety pomocy technicznej z urządzenia w okresowych odstępach czasu dla każdego wymagania, aby zachować dzienniki przez dłuższy czas. 

- [Zbierz pakiet pomocy technicznej dla Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: korzystanie ze źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Azure Stack Edge używa Time.Windows.com, serwera dostawcy czasu sieciowego przez firmę Microsoft.  Azure Stack Edge również pozwala klientowi skonfigurować serwer protokołu czasu sieciowego do wyboru.

- [Konfigurowanie ustawień czasu urządzenia Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Przygotowanie — aktualizowanie procesu reagowania na zdarzenia na platformie Azure

**Wskazówki**: Upewnij się, że plan reagowania na zdarzenia organizacyjne obejmuje procesy: 

- reagowanie na zdarzenia związane z bezpieczeństwem

- które zostały zaktualizowane w chmurze platformy Azure
 
- są regularnie wykonywane w celu zapewnienia gotowości

Zalecane jest zaimplementowanie zabezpieczeń przy użyciu standardowych procesów reagowania na zdarzenia w środowisku przedsiębiorstwa.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik po odpowiedziach na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Przygotowanie — powiadomienie o zdarzeniu konfiguracji

**Wskazówka**: Skonfiguruj informacje kontaktowe dotyczące zdarzenia zabezpieczeń w usłudze Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nieuprawnioną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt ds. zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości 

**Wskazówki**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia ich jakości. Dzięki temu można nauczyć się lekcji od przeszłych zdarzeń i określić priorytety alertów dla analityków, aby zapobiec przetwarzaniu fałszywych alertów pozytywnych. Twórz alerty wysokiej jakości na podstawie Twoich doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności i narzędzi przeznaczonych do generowania i czyszczenia alertów z rejestrowaniem i korelacją dla różnorodnych źródeł alertów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Możesz użyć łącznika danych Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. Utwórz zaawansowane reguły alertów za pomocą funkcji wskaźnikowej platformy Azure, aby generować automatyczne zdarzenia w celu zbadania problemu. 

Eksportuj alerty Security Center i zalecenia do usługi Azure wskaźnikowej przy użyciu funkcji eksportowania, aby pomóc w zidentyfikowaniu ryzyka związanego z zasobami platformy Azure. Zaleca się utworzenie procesu do eksportowania alertów i zaleceń w zautomatyzowany sposób na potrzeby ciągłego zabezpieczenia.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Wykrywanie i analiza — badanie zdarzenia

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i korzystać z różnych źródeł danych w celu utworzenia pełnego widoku działania, które wystąpiło podczas badania potencjalnych zdarzeń. Należy zebrać różne typy dzienników, aby śledzić działania potencjalnej osoby atakującej w łańcuchu kasowania, aby uniknąć niewidomych plamek.  Upewnij się również, że szczegółowe informacje i analizy są przechwytywane w celu uzyskania referencyjnego odwołania.  

Źródła danych do badania obejmują scentralizowane źródła dzienników, które są już zbierane w ramach usług i działających systemów, ale mogą również obejmować:

- Dane sieciowe — użyj dzienników przepływów sieciowych grup zabezpieczeń, usługi Azure Network Watcher i usługi Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Wybierz opcję zrzut pamięci w systemie operacyjnym, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji Snapshot w ramach usług platformy Azure lub funkcji oprogramowania innych firm, aby utworzyć migawki uruchomionych systemów.

Usługa Azure Sentinel zapewnia szeroką analizę danych dla praktycznie każdego źródła dzienników i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje analityczne podczas badania mogą być powiązane ze zdarzeniami pod kątem śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informacje diagnostyczne pomocy technicznej oraz kolekcja zrzutów pamięci platformy Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Badanie zdarzeń za pomocą usługi Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówka**: Zapewnij kontekst analitykom, na których zdarzenia mają się koncentrować na początku, na podstawie ważności alertów i poufności elementów zawartości. Użyj przypisanej ważności do każdego alertu z Azure Security Center, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Poziom ważności bazuje na ocenie pewności usługi Security Center względem ustalenia lub danych analitycznych użytych do wygenerowania alertu, a także poziomu pewności, że za działaniem, które doprowadziło do alertu, stał złośliwy zamiar.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zawieranie, eliminowanie i odzyskiwanie — automatyzacja obsługi zdarzeń

**Wskazówka**: Automatyzuj ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę zdarzeń, które może obsłużyć analityk. Zadania wykonywane ręcznie zwiększają również zmęczenie analityków, co zwiększa ryzyko wystąpienia błędu ludzkiego, który powoduje opóźnienia, a także pogarsza zdolność analityków do skoncentrowania się na złożonych zadaniach. Korzystając z funkcji automatyzacji przepływów pracy w usłudze Azure Security Center i Azure Sentinel, można automatycznie wyzwalać akcje lub uruchamiać element playbook w odpowiedzi na przychodzące alerty zabezpieczeń. Element playbook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w usłudze Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="posture-and-vulnerability-management"></a>Stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>WA-3: Ustanów bezpieczne konfiguracje dla zasobów obliczeniowych

**Wskazówki**: Azure Stack Edge oferuje obsługę tworzenia bezpiecznych konfiguracji dla lokalnych maszyn wirtualnych tworzonych przez klientów. Zdecydowanie zaleca się używanie wytycznych firmy Microsoft w celu ustanowienia linii bazowych zabezpieczeń podczas tworzenia lokalnych maszyn wirtualnych.

- [Pobierz linie bazowe zabezpieczeń zawarte w zestawie narzędzi zgodności zabezpieczeń](/windows/security/threat-protection/windows-security-baselines)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: utrzymywanie bezpiecznych konfiguracji dla zasobów obliczeniowych

**Wskazówki**: Azure Stack Edge nie oferuje pomocy technicznej w celu utrzymania bezpiecznych konfiguracji lokalnych maszyn wirtualnych, które zostały utworzone przez klientów. Zdecydowanie zaleca się, aby klienci korzystali z zestawów narzędzi zgodności zabezpieczeń (SCT), aby pomóc w utrzymaniu bezpiecznych konfiguracji zasobów obliczeniowych.

System operacyjny hosta i maszyny wirtualne zarządzane przez program Azure Stack Edge utrzymują konfiguracje zabezpieczeń. 

- [Linie bazowe zabezpieczeń systemu Windows](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: bezpieczne przechowywanie niestandardowych obrazów systemów operacyjnych i kontenerów

**Wskazówki**: systemy operacyjne hosta i maszyny wirtualne zarządzane przez program Azure Stack Edge są bezpiecznie przechowywane. 

Klienci mogą przenosić własne obrazy maszyn wirtualnych i kontenerów oraz być odpowiedzialne za bezpieczne zarządzanie.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>SL-7: szybkie i automatyczne korygowanie luk w zabezpieczeniach oprogramowania

**Wskazówki**: Azure Stack Edge zawiera regularne aktualizacje poprawek i alerty klientów, gdy takie aktualizacje są dostępne do korygowania luk w zabezpieczeniach. Klient jest odpowiedzialny za zapewnienie aktualności swoich urządzeń i maszyn wirtualnych (tworzonych przez nich) przy użyciu najnowszych poprawek.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówka**: W razie potrzeby przeprowadź test penetracyjny lub działania typu „red team” na zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami testowania penetracji w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Testy penetracyjne na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="endpoint-security"></a>Zabezpieczenia punktu końcowego

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: zabezpieczenia punktu końcowego](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: korzystanie z wykrywania i odpowiedzi punktów końcowych (EDR)

**Wskazówki**: Azure Stack Edge nie obsługuje bezpośrednio funkcji wykrywania i reagowania punktów końcowych (EDR). Można jednak zebrać pakiet pomocy technicznej i pobrać dzienniki inspekcji. Następnie można analizować te dzienniki w celu sprawdzenia nietypowych działań. 

- [Zbierz pakiet pomocy technicznej dla Azure Stack urządzenia brzegowego](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Użyj centralnie zarządzanych nowoczesnych programów chroniących przed złośliwym oprogramowaniem

**Wskazówki**: Azure Stack Edge używa kontroli aplikacji Windows Defender (WDAC) z ścisłymi zasadami integralności kodu, które zezwalają na uruchamianie wstępnie określonych aplikacji i skryptów. Ochrona przed złośliwym oprogramowaniem w usłudze Windows Defender jest również włączona. Klient może zdecydować się na uruchomienie programu chroniącego przed złośliwym oprogramowaniem w maszynach wirtualnych obliczeniowych tworzonych do uruchamiania lokalnego na Azure Stack urządzeniach brzegowych.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="backup-and-recovery"></a>Kopia zapasowa i odzyskiwanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: tworzenie i przywracanie kopii zapasowych](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zapewnianie zwykłych zautomatyzowanych kopii zapasowych

**Wskazówki**: zalecane jest okresowe wykonywanie kopii zapasowych urządzenia Azure Stack Edge i można je wykonać przy użyciu Azure Backup i innych rozwiązań do ochrony danych innych firm, aby chronić dane zawarte w maszynach wirtualnych wdrożonych na urządzeniu. 

Rozwiązania do ochrony danych innych firm, takie jak Cohesity, CommVault i Veritas, mogą również udostępniać rozwiązanie do tworzenia kopii zapasowych danych w lokalnych udziałach SMB lub NFS. 

Dodatkowe informacje są dostępne w linkach, do których istnieją odwołania.

- [Przygotowanie do awarii urządzenia](azure-stack-edge-gpu-prepare-device-failure.md)

- [Ochrona plików i folderów na maszynach wirtualnych](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="br-2-encrypt-backup-data"></a>BR-2: Szyfruj dane kopii zapasowej

**Wskazówki**: Upewnij się, że kopie zapasowe są chronione przed atakami. Powinno to obejmować szyfrowanie kopii zapasowych w celu ochrony przed utratą poufności.  Aby uzyskać więcej informacji, zobacz wybrane rozwiązanie do tworzenia kopii zapasowych.

- [Ochrona danych w udziałach lokalnych Edge](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Ochrona plików i folderów na maszynach wirtualnych](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Weryfikowanie wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki**: okresowe wykonywanie przywracania danych kopii zapasowych. 

- [Procedury odzyskiwania dla Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmniejszanie ryzyka utraty kluczy

**Wskazówki**: Upewnij się, że wszystkie kopie zapasowe Azure Stack Edge, łącznie z kluczami zarządzanymi przez klienta, są chronione zgodnie z najlepszymi rozwiązaniami organizacji. Urządzenie brzegowe Azure Stack jest skojarzone z kontem usługi Azure Storage, używanym jako repozytorium docelowe dla danych na platformie Azure. 

Dostęp do konta usługi Azure Storage jest kontrolowany przez subskrypcje platformy Azure i 2 512-bitowe klucze dostępu do magazynu skojarzone z tym kontem magazynu. Jeden z kluczy dostępu jest używany na potrzeby uwierzytelniania, gdy urządzenie Azure Stack Edge uzyskuje dostęp do konta magazynu. Drugi klucz jest zastrzeżony na potrzeby okresowego rotacji kluczy. Upewnij się, że najlepsze rozwiązania w zakresie zabezpieczeń są używane do rotacji kluczy.

- [Ochrona danych urządzenia Azure Stack Edge na kontach usługi Azure Storage](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ład i strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania elementami zawartości i ochrony danych 

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię monitorowania i ochrony systemów i danych. Ustalaj priorytety odnajdywania, oceny, ochrony i monitorowania danych oraz systemów o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Wgląd organizacji zabezpieczeń w czynniki ryzyka i spis elementów zawartości 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo elementów zawartości w całym cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z natywnych i zewnętrznych funkcji ochrony danych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie przesyłania i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](../security/benchmarks/security-controls-v2-asset-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówka**: Ustanów strategię całego przedsiębiorstwa w zakresie segmentacji dostępu do elementów zawartości przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych mechanizmów kontroli.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie dla różnych typów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, a także modeli uprawnień/dostępu i procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównywanie segmentacji sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stanem zabezpieczeń

**Wskazówka**: Nieustannie mierz i ograniczaj ryzyko dla poszczególnych elementów zawartości i środowiska, w którym są hostowane. Ustalaj priorytety elementów zawartości o wysokiej wartości i wysoce narażonych na ataki obszarów, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Dopasuj role organizacji, obowiązki i odpowiedzialność

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu wyraźnego określenia odpowiedzialności za decyzje dotyczące zabezpieczeń, informując wszystkie osoby o współużytkowanym modelu odpowiedzialności i informując zespoły techniczne o technologii do zabezpieczania chmury.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 1 — ludzie: informowanie zespołów o podróży zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: informowanie zespołów o technologii zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówka**: Ustanów metodę zabezpieczeń sieci platformy Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej zgodny ze strategią segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca brzegowego ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i środowisku lokalnym

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, architektura sieci referencyjnej)

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](../security/benchmarks/index.yml)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dotyczącej tożsamości i dostępu uprzywilejowanego

**Wskazówka**: Ustanów metody obsługi tożsamości i dostępu uprzywilejowanego na platformie Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](../security/benchmarks/security-controls-v2-identity-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../security/fundamentals/identity-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówka**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i eliminować zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analitykom alerty wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na integracji i ręcznych działaniach. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji w zakresie operacji zabezpieczeń (SecOps) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia zgodnie z NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu rozwiązania SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i publicznymi zainteresowanymi stronami

-   Używanie natywnych i zewnętrznych platform Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, postępowania dowodowe oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](../security/benchmarks/security-controls-v2-incident-response.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
