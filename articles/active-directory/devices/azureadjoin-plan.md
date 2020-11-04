---
title: Planowanie wdrożenia Azure Active Directory Join
description: Wyjaśnia kroki, które są wymagane do zaimplementowania urządzeń przyłączonych do usługi Azure AD w danym środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3587ef6be9d6c9969dff5d1af2181ed51aea7d29
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308287"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Instrukcje: Planowanie wdrożenia usługi Azure AD Join

Usługa Azure AD Join umożliwia dołączanie urządzeń bezpośrednio do usługi Azure AD bez konieczności przyłączania do Active Directory lokalnych, zapewniając jednocześnie produktywność i bezpieczeństwo użytkowników. Usługa Azure AD Join jest gotowa do wdrożenia w przedsiębiorstwie zarówno w przypadku wdrożeń w skali, jak i w zakresie.   

Ten artykuł zawiera informacje potrzebne do zaplanowania wdrożenia usługi Azure AD Join.
 
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz już [wprowadzenie do zarządzania urządzeniami w Azure Active Directory](./overview.md).

## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować wdrożenie usługi Azure AD join, zapoznaj się z tematem:

> [!div class="checklist"]
> - Przejrzyj scenariusze
> - Przegląd infrastruktury tożsamości
> - Ocenianie zarządzania urządzeniami
> - Zrozumienie zagadnień dotyczących aplikacji i zasobów
> - Zapoznaj się z opcjami aprowizacji
> - Konfigurowanie roamingu stanu przedsiębiorstwa
> - Konfigurowanie dostępu warunkowego

## <a name="review-your-scenarios"></a>Przejrzyj scenariusze 

Chociaż hybrydowe dołączenie usługi Azure AD może być preferowane w niektórych scenariuszach, usługa Azure AD Join umożliwia przejście do modelu w chmurze z systemem Windows. Jeśli planujesz modernizację zarządzania urządzeniami i obniżasz koszty IT związane z urządzeniami, usługa Azure AD Join zapewnia doskonałą podstawę do osiągnięcia tych celów.  
 
Jeśli cele są zgodne z następującymi kryteriami, należy wziąć pod uwagę usługę Azure AD Join:

- Przyjmujesz Microsoft 365 jako pakiet produktywności dla użytkowników.
- Chcesz zarządzać urządzeniami za pomocą rozwiązania do zarządzania urządzeniami w chmurze.
- Chcesz uprościć Inicjowanie obsługi urządzeń dla użytkowników rozproszonych geograficznie.
- Planujesz modernizację infrastruktury aplikacji.

## <a name="review-your-identity-infrastructure"></a>Przegląd infrastruktury tożsamości  

Usługa Azure AD Join współpracuje z obydwoma środowiskami, zarządzanymi i federacyjnymi.  

### <a name="managed-environment"></a>Środowisko zarządzane

Środowisko zarządzane można wdrożyć za pomocą [synchronizacji skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md) lub [przekazywania uwierzytelniania](../hybrid/how-to-connect-pta-quick-start.md) przy użyciu bezproblemowego logowania jednokrotnego.

Te scenariusze nie wymagają konfigurowania serwera federacyjnego na potrzeby uwierzytelniania.

### <a name="federated-environment"></a>Środowisko federacyjne

Środowisko federacyjne powinno mieć dostawcę tożsamości, który obsługuje zarówno WS-Trust jak i WS-Fed protokołów:

- **Usługa WS-karmione:** Ten protokół jest wymagany do przyłączania urządzenia do usługi Azure AD.
- **Usługa WS-Trust:** Ten protokół jest wymagany do zalogowania się do urządzenia dołączonego do usługi Azure AD.

W przypadku korzystania z AD FS należy włączyć następujące punkty końcowe WS-Trust: `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Jeśli dostawca tożsamości nie obsługuje tych protokołów, usługa Azure AD Join nie działa w sposób natywny. 

>[!NOTE]
> Obecnie usługa Azure AD Join nie działa w przypadku [AD FS 2019 skonfigurowanego z zewnętrznymi dostawcami uwierzytelniania jako podstawową metodą uwierzytelniania](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Usługa Azure AD Join domyślnie przyjmuje uwierzytelnianie hasła jako podstawową metodę, która powoduje błędy uwierzytelniania w tym scenariuszu


### <a name="smartcards-and-certificate-based-authentication"></a>Karty inteligentne i uwierzytelnianie oparte na certyfikatach

Do dołączania urządzeń do usługi Azure AD nie można używać kart inteligentnych ani uwierzytelniania opartego na certyfikatach. Można jednak użyć kart inteligentnych do logowania się do urządzeń przyłączonych do usługi Azure AD, jeśli skonfigurowano AD FS.

**Zalecenie:** Zaimplementuj usługi Windows Hello dla firm, aby zapewnić silne uwierzytelnianie bez hasła na urządzeniach z systemem Windows 10.

### <a name="user-configuration"></a>Konfiguracja użytkownika

Jeśli tworzysz użytkowników w:

- **Active Directory lokalnych** , należy zsynchronizować je z usługą Azure AD przy użyciu [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md). 
- **Azure AD** , nie jest wymagana żadna dodatkowa konfiguracja.

Lokalne nazwy UPN, które różnią się od nazw UPN usługi Azure AD, nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Jeśli użytkownicy korzystają z lokalnej nazwy UPN, należy zaplanować przełączenie do korzystania z podstawowej nazwy UPN w usłudze Azure AD.

## <a name="assess-your-device-management"></a>Ocenianie zarządzania urządzeniami

### <a name="supported-devices"></a>Obsługiwane urządzenia

Azure AD Join:

- Dotyczy tylko urządzeń z systemem Windows 10. 
- Nie ma zastosowania do poprzednich wersji systemu Windows lub innych systemów operacyjnych. W przypadku urządzeń z systemem Windows 7/8.1 należy przeprowadzić uaktualnienie do systemu Windows 10 w celu wdrożenia usługi Azure AD Join.
- Jest obsługiwany w przypadku modułu TPM zgodnego ze standardem FIPS 2,0, ale nie jest obsługiwany w przypadku modułu TPM 1,2. Jeśli na urządzeniach jest używany moduł TPM zgodny ze standardem FIPS 1,2, należy je wyłączyć przed przejściem do usługi Azure AD Join. Firma Microsoft nie udostępnia żadnych narzędzi do wyłączania trybu FIPS dla moduły TPM, ponieważ jest on zależny od producenta modułu TPM. Aby uzyskać pomoc techniczną, skontaktuj się z producentem OEM.
 
**Zalecenie:** Zawsze używaj najnowszej wersji systemu Windows 10, aby korzystać z zaktualizowanych funkcji.

### <a name="management-platform"></a>Platforma zarządzania

Zarządzanie urządzeniami dla urządzeń przyłączonych do usługi Azure AD jest oparte na platformie MDM, takiej jak usługa Intune, oraz dostawcy usług MDM. System Windows 10 ma wbudowanego agenta MDM, który współpracuje ze wszystkimi zgodnymi rozwiązaniami MDM.

> [!NOTE]
> Zasady grupy nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD, ponieważ nie są połączone z lokalnymi Active Directory. Zarządzanie urządzeniami przyłączonymi do usługi Azure AD jest możliwe tylko za pomocą rozwiązania MDM

Istnieją dwa podejścia do zarządzania urządzeniami przyłączonymi do usługi Azure AD:

- **Tylko MDM** — urządzenie jest zarządzane wyłącznie przez dostawcę zarządzania urządzeniami przenośnymi, takie jak usługa Intune. Wszystkie zasady są dostarczane w ramach procesu rejestracji MDM. W przypadku klientów Azure AD — wersja Premium lub pakietu EMS Rejestracja w usłudze MDM to zautomatyzowany krok, który jest częścią funkcji dołączania do usługi Azure AD.
- **Współzarządzanie** — urządzenie jest zarządzane przez dostawcę zarządzania urządzeniami przenośnymi i program SCCM. W tym podejściu Agent SCCM jest instalowany na urządzeniu zarządzanym przez rozwiązanie MDM w celu administrowania określonymi aspektami.

Jeśli korzystasz z zasad grupy, Oceń zasady grupy i reguły zarządzania urządzeniami przenośnymi przy użyciu usługi [zasady grupy Analytics](/mem/intune/configuration/group-policy-analytics) w programie Microsoft Endpoint Manager. 

Przejrzyj obsługiwane i nieobsługiwane zasady, aby określić, czy można użyć rozwiązania MDM zamiast zasad grupy. W przypadku nieobsługiwanych zasad należy wziąć pod uwagę następujące kwestie:

- Czy są to nieobsługiwane zasady niezbędne dla urządzeń lub użytkowników przyłączonych do usługi Azure AD?
- Czy nieobsługiwane zasady mają zastosowanie w przypadku wdrożenia opartego na chmurze?

Jeśli rozwiązanie MDM nie jest dostępne za pomocą galerii aplikacji usługi Azure AD, możesz je dodać zgodnie z procesem opisanym w [Azure Active Directory integracji z urządzeniami przenośnymi](/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Za pomocą współzarządzania można zarządzać pewnymi aspektami urządzeń za pomocą programu SCCM, a zasady są dostarczane za pomocą platformy MDM. Microsoft Intune umożliwia współzarządzanie z programem SCCM. Aby uzyskać więcej informacji na temat współzarządzania dla urządzeń z systemem Windows 10, zobacz [co to jest współzarządzanie?](/configmgr/core/clients/manage/co-management-overview). Jeśli używasz produktu MDM innego niż usługa Intune, skontaktuj się z dostawcą MDM w sprawie odpowiednich scenariuszy współzarządzania.

**Zalecenie:** Rozważ zarządzanie tylko urządzeniami PRZENOŚNYmi dla urządzeń przyłączonych do usługi Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Zrozumienie zagadnień dotyczących aplikacji i zasobów

Zalecamy Migrowanie aplikacji ze środowiska lokalnego do chmury w celu lepszego działania użytkowników i kontroli dostępu. Jednak urządzenia przyłączone do usługi Azure AD mogą bezproblemowo zapewnić dostęp do aplikacji lokalnych i w chmurze. Aby uzyskać więcej informacji, zobacz [jak działa logowanie JEDNOkrotne do zasobów lokalnych na urządzeniach dołączonych do usługi Azure AD](azuread-join-sso.md).

W poniższych sekcjach przedstawiono zagadnienia dotyczące różnych typów aplikacji i zasobów.

### <a name="cloud-based-applications"></a>Aplikacje oparte na chmurze

Jeśli aplikacja zostanie dodana do galerii aplikacji usługi Azure AD, użytkownicy otrzymają Logowanie jednokrotne za pomocą urządzeń przyłączonych do usługi Azure AD. Żadna dodatkowa konfiguracja nie jest wymagana. Użytkownicy odbierają Logowanie jednokrotne w przeglądarkach Microsoft Edge i Chrome. Dla programu Chrome należy wdrożyć [rozszerzenie konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Wszystkie aplikacje Win32, które:

- Zapoznaj się z menedżerem kont w sieci Web (WAM) dla żądań tokenów, które również mają rejestrację jednokrotną na urządzeniach dołączonych 
- Nie należy polegać na tym, że użytkownik może monitować użytkowników o uwierzytelnienie. 

### <a name="on-premises-web-applications"></a>Lokalne aplikacje sieci Web

Jeśli Twoje aplikacje są niestandardowymi kompilacjami i/lub hostowanymi lokalnie, należy dodać je do zaufanych witryn w przeglądarce, aby:

- Włączenie zintegrowanego uwierzytelniania systemu Windows 
- Zapewnij użytkownikom bezpłatne środowisko logowania jednokrotnego. 

Jeśli używasz AD FS, zobacz [weryfikacja i zarządzanie logowaniem jednokrotnym przy użyciu AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Zalecenie:** Weź pod uwagę hosting w chmurze (na przykład Azure) i integrację z usługą Azure AD w celu lepszego środowiska.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Aplikacje lokalne korzystające ze starszych protokołów

Użytkownicy odbierają Logowanie jednokrotne z urządzeń przyłączonych do usługi Azure AD, jeśli urządzenie ma dostęp do kontrolera domeny. 

**Zalecenie:** Wdróż [aplikacja usługi Azure AD serwer proxy](../manage-apps/application-proxy.md) , aby zapewnić bezpieczny dostęp do tych aplikacji.

### <a name="on-premises-network-shares"></a>Lokalne udziały sieciowe

Użytkownicy mają Logowanie jednokrotne z urządzeń przyłączonych do usługi Azure AD, gdy urządzenie ma dostęp do lokalnego kontrolera domeny.

### <a name="printers"></a>Drukarki

W przypadku drukarek należy wdrożyć drukowanie w [chmurze hybrydowej](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) w celu odnajdywania drukarek na urządzeniach dołączonych do usługi Azure AD. 

Mimo że drukarki nie mogą być automatycznie odnajdywane w środowisku tylko w chmurze, użytkownicy mogą również użyć ścieżki UNC drukarki, aby dodać je bezpośrednio. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplikacje lokalne polegające na uwierzytelnianiu komputera

Urządzenia przyłączone do usługi Azure AD nie obsługują aplikacji lokalnych korzystających z uwierzytelniania maszynowego. 

**Zalecenie:** Rozważ wycofanie tych aplikacji i przechodzenie do ich nowoczesnych wariantów.

### <a name="remote-desktop-services"></a>Usługi pulpitu zdalnego

Podłączanie pulpitu zdalnego do urządzeń przyłączonych do usługi Azure AD wymaga, aby maszyna hosta była przyłączona do usługi Azure AD lub dołączona do hybrydowej usługi Azure AD. Pulpit zdalny z przyłączonych lub nienależących do systemu Windows urządzeń nie jest obsługiwany. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z zdalnym komputerem przyłączonym do usługi Azure AD](/windows/client-management/connect-to-remote-aadj-pc)

Począwszy od systemu Windows 10 2004 Update, użytkownicy mogą również używać pulpitu zdalnego z zarejestrowanego urządzenia z systemem Windows 10 w usłudze Azure AD do urządzenia dołączonego do usługi Azure Active Directory. 

## <a name="understand-your-provisioning-options"></a>Zapoznaj się z opcjami aprowizacji
**Uwaga** : urządzeń przyłączonych do usługi Azure AD nie można wdrażać przy użyciu narzędzia do przygotowywania systemu (Sysprep) ani podobnych narzędzi do tworzenia obrazu

Usługę Azure AD Join można zainicjować przy użyciu następujących metod:

- Samoobsługowe środowisko **OOBE/Settings** — w trybie samoobsługowym użytkownicy przechodzą przez proces dołączania usługi Azure AD podczas pracy z systemem Windows (OOBE) lub z ustawień systemu Windows. Aby uzyskać więcej informacji, zobacz [dołączanie urządzenia służbowego do sieci organizacji](../user-help/user-help-join-device-on-network.md). 
- **Windows autopilotaż** — system Windows autopilotaż umożliwia wstępne konfigurowanie urządzeń w celu zapewnienia sprawnego działania w języku OOBE w celu wykonania sprzężenia usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie funkcji autopilotażu systemu Windows](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Rejestracja Zbiorcza** — Rejestracja Zbiorcza umożliwia przełączenie usługi Azure AD sterowanej przez administratora przy użyciu narzędzia udostępniania zbiorczego w celu skonfigurowania urządzeń. Aby uzyskać więcej informacji, zobacz [Rejestrowanie zbiorcze dla urządzeń z systemem Windows](/intune/windows-bulk-enroll).
 
Poniżej przedstawiono porównanie tych trzech metod 
 
| Element | Konfiguracja samoobsługowa | Windows Autopilot | Rejestrowanie zbiorcze |
| --- | --- | --- | --- |
| Wymagaj interakcji z użytkownikiem w celu skonfigurowania | Tak | Tak | Nie |
| Wymaganie nakładu pracy IT | Nie | Tak | Tak |
| Stosowane przepływy | Ustawienia & OOBE | Tylko środowisko OOBE | Tylko środowisko OOBE |
| Uprawnienia administratora lokalnego do użytkownika podstawowego | Tak, domyślnie | Konfigurowalne | Nie |
| Wymagaj obsługi OEM urządzenia | Nie | Tak | Nie |
| Obsługiwane wersje | 1511 + | 1709 + | 1703 + |
 
Wybierz podejście do wdrożenia lub podejścia, przeglądając powyższą tabelę i zapoznaj się z poniższymi uwagami dotyczącymi przyjęcia obu rozwiązań:  

- Czy użytkownicy świadomeą się nad instalacją? 
   - Samoobsługowe działania mogą być najlepszym rozwiązaniem dla tych użytkowników. Aby ulepszyć środowisko użytkownika, należy rozważyć użycie funkcji autopilotażu systemu Windows.  
- Czy użytkownicy są zdalni lub w środowisku firmowym? 
   - Funkcja samoobsługowa lub automatyczna pilotażowa najlepiej jest Najlepsza dla użytkowników zdalnych w celu nieodpłatnej instalacji. 
- Czy preferujesz konfigurację zarządzaną przez użytkownika, czy przez administratora? 
   - Rejestracja Zbiorcza działa lepiej w przypadku wdrożenia sterowanego przez administratora w celu skonfigurowania urządzeń przed przekazaniem ich użytkownikom.     
- Czy kupujesz urządzenia od 1-2 producentów OEM czy masz szeroką dystrybucję urządzeń OEM?  
   - Jeśli kupujesz od ograniczonych producentów OEM, którzy obsługują również program autopilotaż, możesz skorzystać z ściślejszej integracji z programem autopilotaż. 

## <a name="configure-your-device-settings"></a>Konfigurowanie ustawień urządzenia

Azure Portal pozwala kontrolować wdrażanie urządzeń przyłączonych do usługi Azure AD w organizacji. Aby skonfigurować ustawienia pokrewne, na **stronie Azure Active Directory** wybierz opcję `Devices > Device settings` .

### <a name="users-may-join-devices-to-azure-ad"></a>Użytkownicy mogą dołączać urządzenia do usługi Azure AD

Ustaw tę opcję na wartość **wszystkie** lub **wybraną** na podstawie zakresu wdrożenia oraz tego, kto ma zezwalać na Konfigurowanie urządzenia dołączonego do usługi Azure AD. 

![Użytkownicy mogą dołączać urządzenia do usługi Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Dodatkowi administratorzy lokalni na urządzeniach dołączonych do usługi Azure AD

Wybierz pozycję **wybrane** i wybierz użytkowników, których chcesz dodać do grupy Administratorzy lokalni na wszystkich urządzeniach dołączonych do usługi Azure AD. 

![Dodatkowi administratorzy lokalni na urządzeniach dołączonych do usługi Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Wymagaj uwierzytelniania wieloskładnikowego w celu dołączania urządzeń

Wybierz pozycję **"tak"** , jeśli chcesz, aby użytkownicy korzystali z uwierzytelniania MFA podczas dołączania urządzeń do usługi Azure AD. Aby użytkownicy mogli przyłączać urządzenia do usługi Azure AD przy użyciu uwierzytelniania wieloskładnikowego, samo urządzenie będzie miało drugi czynnik.

![Wymagaj uwierzytelniania wieloskładnikowego w celu dołączania urządzeń](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Skonfiguruj ustawienia mobilności

Aby można było skonfigurować ustawienia mobilności, można najpierw dodać dostawcę MDM.

**Aby dodać dostawcę zarządzania urządzeniami przenośnymi** :

1. Na **stronie Azure Active Directory** w sekcji **Zarządzanie** kliknij pozycję `Mobility (MDM and MAM)` . 
1. Kliknij pozycję **Dodaj aplikację**.
1. Wybierz z listy dostawcę MDM.

   :::image type="content" source="./media/azureadjoin-plan/04.png" alt-text="Zrzut ekranu przedstawiający Azure Active Directory dodanie strony aplikacji. Poniżej wymieniono kilku dostawców M D M." border="false":::

Wybierz dostawcę MDM, aby skonfigurować powiązane ustawienia. 

### <a name="mdm-user-scope"></a>Zakres użytkownika oprogramowania MDM

Wybierz **niektóre** lub **wszystkie** w zależności od zakresu wdrożenia. 

![Zakres użytkownika oprogramowania MDM](./media/azureadjoin-plan/05.png)

Na podstawie Twojego zakresu występuje jedna z następujących sytuacji: 

- **Użytkownik jest w zakresie zarządzania urządzeniami przenośnymi** : Jeśli masz subskrypcję Azure AD — wersja Premium, rejestracja w usłudze MDM jest zautomatyzowana wraz z funkcją Azure AD Join. Wszyscy użytkownicy z zakresem muszą mieć odpowiednią licencję na zarządzanie urządzeniami przenośnymi. Jeśli w tym scenariuszu Rejestracja w usłudze MDM nie powiedzie się, usługa Azure AD Join również zostanie wycofana.
- **Użytkownik nie znajduje się w zakresie zarządzania urządzeniami przenośnymi** : Jeśli użytkownicy nie znajdują się w zakresie MDM, usługa Azure AD Join zakończy działanie bez rejestracji MDM. Powoduje to niezarządzane urządzenie.

### <a name="mdm-urls"></a>Adresy URL funkcji zarządzania urządzeniami przenośnymi

Istnieją trzy adresy URL powiązane z konfiguracją rozwiązania MDM:

- Adres URL warunków użytkowania zarządzania urządzeniami przenośnymi
- Adres URL odnajdywania zarządzania urządzeniami przenośnymi 
- Adres URL zgodności zarządzania urządzeniami przenośnymi

:::image type="content" source="./media/azureadjoin-plan/06.png" alt-text="Zrzut ekranu przedstawiający część sekcji konfiguracyjnej &quot;Azure Active Directory M D&quot; oraz pola U R L dotyczące warunków użytkowania, odnajdywania i zgodności M D." border="false":::

Każdy adres URL ma wstępnie zdefiniowaną wartość domyślną. Jeśli te pola są puste, skontaktuj się z dostawcą MDM, aby uzyskać więcej informacji.

### <a name="mam-settings"></a>Ustawienia funkcji MAM

MAM nie ma zastosowania do usługi Azure AD Join. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurowanie roamingu stanu przedsiębiorstwa

Jeśli chcesz włączyć roaming stanu do usługi Azure AD, aby użytkownicy mogli synchronizować swoje ustawienia na różnych urządzeniach, zobacz [włączanie Enterprise State roaming w Azure Active Directory](enterprise-state-roaming-enable.md). 

**Zalecenie** : Włącz to ustawienie nawet dla urządzeń przyłączonych do hybrydowej usługi Azure AD.

## <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

Jeśli masz skonfigurowanego dostawcę MDM dla urządzeń przyłączonych do usługi Azure AD, dostawca flaguje urządzenie jako zgodne, gdy tylko urządzenie jest objęte zarządzaniem. 

![Zgodne urządzenie](./media/azureadjoin-plan/46.png)

Za pomocą tej implementacji można wymagać, aby [zarządzane urządzenia dla dostępu do aplikacji w chmurze miały dostęp warunkowy](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dołącz nowe urządzenie z systemem Windows 10 przy użyciu usługi Azure AD podczas pierwszego uruchomienia](azuread-joined-devices-frx.md) 
>  [Przyłącz urządzenie służbowe do sieci organizacji](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
