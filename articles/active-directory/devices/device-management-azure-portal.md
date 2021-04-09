---
title: Jak zarządzać urządzeniami za pomocą Azure Portal | Microsoft Docs
description: Dowiedz się, jak za pomocą Azure Portal zarządzać urządzeniami.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b43a99eb561cbfa340e0b3f318782bef2ca17c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023439"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure Portal

Usługa Azure AD udostępnia centralne miejsce do zarządzania tożsamościami urządzeń.

Strona **wszystkie urządzenia** umożliwia:

- Zidentyfikuj urządzenia, w tym:
   - Urządzenia, które zostały dołączone lub zarejestrowane w usłudze Azure AD.
   - Urządzenia wdrożone przy użyciu funkcji [autopilotażu systemu Windows](/windows/deployment/windows-autopilot/windows-autopilot).
   - Drukarki korzystające z funkcji [drukowania uniwersalnego](/universal-print/fundamentals/universal-print-getting-started)
- Wykonywanie zadań zarządzania tożsamościami urządzeń, takich jak Włączanie, wyłączanie, usuwanie i zarządzanie.
   - [Drukarki](/universal-print/fundamentals/) i urządzenia z [systemem Windows](/windows/deployment/windows-autopilot/windows-autopilot) , które mają ograniczoną opcje zarządzania w usłudze Azure AD. Muszą one być zarządzane z odpowiednich interfejsów administratora.
- Skonfiguruj ustawienia tożsamości urządzenia.
- Włączać lub wyłączać Enterprise State Roaming.
- Przeglądanie dzienników inspekcji związanych z urządzeniem
- Pobierz urządzenia (wersja zapoznawcza)

[![Widok wszystkie urządzenia w Azure Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Dostęp do portalu urządzeń można uzyskać, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Azure Active Directory**  >  **urządzeń**.

## <a name="manage-devices"></a>Zarządzanie urządzeniami

Istnieją dwie lokalizacje zarządzania urządzeniami w usłudze Azure AD:

- **Azure Portal**  >  **Azure Active Directory**  >  **Urządzenia**
- **Azure Portal**  >  **Azure Active Directory**  >  **Użytkownicy** > wybieranie **urządzeń** > użytkownika

Obie opcje umożliwiają administratorom:

- Wyszukaj urządzenia.
- Zobacz szczegóły urządzenia, w tym:
    - Nazwa urządzenia
    - Identyfikator urządzenia
    - System operacyjny i wersja
    - Typ sprzężenia
    - Właściciel
    - Zarządzanie urządzeniami przenośnymi i ich zgodność
    - Klucz odzyskiwania funkcji BitLocker
- Wykonywanie zadań zarządzania tożsamościami urządzeń, takich jak, Włączanie, wyłączanie, usuwanie i zarządzanie.
   - [Drukarki](/universal-print/fundamentals/) i urządzenia z [systemem Windows](/windows/deployment/windows-autopilot/windows-autopilot) , które mają ograniczoną opcje zarządzania w usłudze Azure AD. Muszą one być zarządzane z odpowiednich interfejsów administratora.

> [!TIP]
> - Hybrydowe urządzenia z systemem Windows 10 połączone z usługą Azure AD nie mają właściciela. Jeśli szukasz urządzenia według właściciela i nie znajdziesz go, wyszukaj identyfikator urządzenia.
>
> - Jeśli zostanie wyświetlone urządzenie z informacją o stanie "oczekujące" w ramach zarejestrowanej kolumny, oznacza to, że urządzenie zostało zsynchronizowane z programu Azure AD Connect i oczekuje na ukończenie rejestracji z poziomu klienta. Przeczytaj więcej na temat [planowania implementacji hybrydowego dołączania do usługi Azure AD](hybrid-azuread-join-plan.md). Dodatkowe informacje znajdują się w artykule [często zadawane pytania dotyczące urządzeń](faq.md).
>
> - W przypadku niektórych urządzeń z systemem iOS nazwy urządzeń zawierające apostrofy mogą potencjalnie używać różnych znaków, które wyglądają jak apostrofy. Dlatego wyszukiwanie takich urządzeń jest nieco trudne — jeśli wyniki wyszukiwania nie są prawidłowo wyświetlane, upewnij się, że ciąg wyszukiwania zawiera pasujący znak apostrofu.

### <a name="manage-an-intune-device"></a>Zarządzanie urządzeniem usługi Intune

Jeśli jesteś administratorem usługi Intune, możesz zarządzać urządzeniami, na których jest oznaczony jako MDM **Microsoft Intune**. Jeśli urządzenie nie jest zarejestrowane w Microsoft Intune, opcja "Zarządzaj" będzie wyszarzona.

### <a name="enable-or-disable-an-azure-ad-device"></a>Włączanie lub wyłączanie urządzenia usługi Azure AD

Aby włączyć lub wyłączyć urządzenia, dostępne są dwie opcje:

- Pasek narzędzi na stronie **wszystkie urządzenia** po wybraniu jednego lub kilku urządzeń.
- Pasek narzędzi po przeprowadzeniu przechodzenia do określonego urządzenia.

> [!IMPORTANT]
> - Aby włączyć lub wyłączyć urządzenie, użytkownik musi być administratorem globalnym lub administratorem urządzenia w chmurze w usłudze Azure AD. 
> - Wyłączenie urządzenia zapobiega pomyślnym uwierzytelnieniu urządzenia w usłudze Azure AD, uniemożliwiając dostęp urządzenia do zasobów usługi Azure AD chronionych przez dostęp warunkowy oparty na urządzeniach lub przy użyciu poświadczeń funkcji Windows Hello dla firm.
> - Wyłączenie urządzenia spowoduje odwołanie podstawowego tokenu odświeżania (PRT) oraz wszystkich tokenów odświeżania (RT) na urządzeniu.
> - Drukarek nie można włączać ani wyłączać w usłudze Azure AD.

### <a name="delete-an-azure-ad-device"></a>Usuwanie urządzenia usługi Azure AD

Aby usunąć urządzenie, dostępne są dwie opcje:

- Pasek narzędzi na stronie **wszystkie urządzenia** po wybraniu jednego lub kilku urządzeń.
- Pasek narzędzi po przeprowadzeniu przechodzenia do określonego urządzenia.

> [!IMPORTANT]
> - Aby usunąć urządzenie, użytkownik musi mieć przypisaną rolę administratora urządzenia w chmurze, administratora usługi Intune lub administratora globalnego w usłudze Azure AD.
> - Nie można usunąć drukarek i urządzeń z systemem Windows w usłudze Azure AD
> - Usuwanie urządzenia:
>    - Uniemożliwia urządzeniu dostęp do zasobów usługi Azure AD.
>    - Usuwa wszystkie szczegóły, które są dołączone do urządzenia, na przykład klucze funkcji BitLocker dla urządzeń z systemem Windows.  
>    - Reprezentuje nieodzyskiwalne działanie i nie jest zalecane, chyba że jest to wymagane.

Jeśli urządzenie jest zarządzane przez inny urząd zarządzania (na przykład Microsoft Intune), upewnij się, że urządzenie zostało wyczyszczone/wycofane przed usunięciem urządzenia w usłudze Azure AD. Zapoznaj się z tematem jak [zarządzać przestarzałymi urządzeniami](manage-stale-devices.md) przed usunięciem wszystkich urządzeń.

### <a name="view-or-copy-device-id"></a>Wyświetl lub Skopiuj identyfikator urządzenia

IDENTYFIKATORA urządzenia można użyć do sprawdzenia szczegółów identyfikatora urządzenia na urządzeniu lub przy rozwiązywaniu problemów przy użyciu programu PowerShell. Aby uzyskać dostęp do opcji kopiowania, kliknij urządzenie.

![Wyświetlanie identyfikatora urządzenia](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Wyświetl lub Skopiuj klucze funkcji BitLocker

Można wyświetlać i kopiować klucze funkcji BitLocker, aby umożliwić użytkownikom odzyskiwanie szyfrowanych dysków. Te klucze są dostępne tylko dla urządzeń z systemem Windows, które są szyfrowane i mają klucze przechowywane w usłudze Azure AD. Te klucze można znaleźć podczas uzyskiwania dostępu do szczegółów urządzenia, wybierając pozycję **Pokaż klucz odzyskiwania**. Wybranie pozycji **Pokaż klucz odzyskiwania** spowoduje wygenerowanie dziennika inspekcji, który można znaleźć w `KeyManagement` kategorii.

![Wyświetl klucze funkcji BitLocker](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Aby wyświetlić lub skopiować klucze funkcji BitLocker, musisz być właścicielem urządzenia lub użytkownikiem, który ma co najmniej jedną z następujących ról:

- Administrator urządzenia w chmurze
- Administrator globalny
- Administrator pomocy technicznej
- Administrator usługi Intune
- Administrator zabezpieczeń
- Czytelnik zabezpieczeń

### <a name="device-list-filtering-preview"></a>Filtrowanie listy urządzeń (wersja zapoznawcza)

Wcześniej można było filtrować tylko listę urządzeń według działania i stanu włączenia. Ta wersja zapoznawcza umożliwia teraz filtrowanie listy urządzeń przez następujące atrybuty na urządzeniu:

- Włączony stan
- Stan zgodności
- Typ sprzężenia (przyłączony do usługi Azure AD, dołączona hybrydowa usługa Azure AD, rejestracja usługi Azure AD)
- Znacznik czasu aktywności
- System operacyjny
- Typ urządzenia (drukarki, bezpieczne maszyny wirtualne, urządzenia udostępnione, zarejestrowane urządzenia)

Aby włączyć funkcję filtrowania wersji zapoznawczej w widoku **wszystkie urządzenia** :

![Włącz funkcję filtrowania filtru](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Azure Active Directory**  >  **urządzeń**.
1. Wybierz baner, który brzmi, **Wypróbuj ulepszenia filtrowania nowych urządzeń. Kliknij, aby włączyć podgląd.**

Teraz można **dodawać filtry** do widoku **wszystkie urządzenia** .

### <a name="download-devices-preview"></a>Pobierz urządzenia (wersja zapoznawcza)

Administratorzy urządzeń w chmurze, Administratorzy usługi Intune i Administratorzy globalni mogą korzystać z opcji **Pobierz urządzenia (wersja zapoznawcza)** , aby wyeksportować plik CSV urządzeń na podstawie wszelkich zastosowanych filtrów. Jeśli na liście nie zastosowano żadnych filtrów, wszystkie urządzenia zostaną wyeksportowane. Eksport może być uruchomiony przez okres maksymalnie godzinę, w zależności od 

Wyeksportowana lista zawiera następujące atrybuty tożsamości urządzenia:

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Konfiguruj ustawienia urządzenia

Aby zarządzać tożsamościami urządzeń za pomocą portalu usługi Azure AD, te urządzenia muszą być [zarejestrowane lub dołączone](overview.md) do usługi Azure AD. Jako administrator można kontrolować proces rejestrowania i dołączania urządzeń przez skonfigurowanie następujących ustawień urządzenia.

Do wyświetlania ustawień urządzenia i zarządzania nimi w Azure Portal należy przypisać jedną z następujących ról:

- Administrator globalny
- Administrator urządzenia w chmurze
- Czytnik globalny
- Czytnik katalogów

![Ustawienia urządzenia związane z usługą Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Użytkownicy mogą dołączać urządzenia do usługi Azure AD** — to ustawienie umożliwia wybranie użytkowników, którzy mogą rejestrować swoje urządzenia jako urządzenia przyłączone do usługi Azure AD. Wartością domyślną jest **wszystkich**.

> [!NOTE]
> **Użytkownicy mogą dołączać urządzenia do ustawienia usługi Azure AD** mają zastosowanie tylko do usługi Azure AD JOIN w systemie Windows 10. To ustawienie nie ma zastosowania do hybrydowych urządzeń przyłączonych do usługi Azure AD, [przyłączonych do usługi Azure AD maszyn wirtualnych na platformie Azure i w](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) usłudze Azure AD przyłączonych za pomocą [trybu samodzielnego wdrażania systemu Windows](/mem/autopilot/self-deploying) , ponieważ te metody działają w kontekście bez użytkownika.

- **Dodatkowi Administratorzy lokalni na urządzeniach dołączonych do usługi Azure AD** — możesz wybrać użytkowników, którym udzielono uprawnień administratora lokalnego na urządzeniu. Ci użytkownicy są dodawani do roli *Administratorzy urządzenia* w usłudze Azure AD. Administratorzy globalni w usłudze Azure AD i właściciele urządzeń mają domyślnie przyznane prawa administratora lokalnego. Ta opcja to funkcja wersji Premium dostępna za pomocą produktów, takich jak Azure AD — wersja Premium lub pakiet Enterprise Mobility Suite (EMS).
- **Użytkownicy mogą zarejestrować swoje urządzenia w usłudze Azure AD** — należy skonfigurować to ustawienie, aby zezwolić na rejestrację urządzeń z systemem Windows 10 Personal, iOS, Android i macOS w usłudze Azure AD. W przypadku wybrania opcji **Brak** urządzenia nie mogą zarejestrować się w usłudze Azure AD. Rejestracja przy użyciu Microsoft Intune lub zarządzania urządzeniami przenośnymi (MDM) dla Microsoft 365 wymaga rejestracji. W przypadku skonfigurowania jednej z tych usług jest zaznaczona opcja **wszystkie** nie **jest dostępna** .
- **Urządzenia, które mają być przyłączone do usługi Azure AD lub zarejestrowane w usłudze Azure AD wymagane Multi-Factor Authentication** — możesz zdecydować, czy użytkownicy są zobowiązani do zapewnienia dodatkowego czynnika uwierzytelniania w celu przyłączenia lub zarejestrowania urządzenia w usłudze Azure AD. Wartość domyślna to **nie**. Zalecamy wymaganie uwierzytelniania wieloskładnikowego podczas rejestrowania lub przyłączania urządzenia. Przed włączeniem uwierzytelniania wieloskładnikowego dla tej usługi należy się upewnić, że uwierzytelnianie wieloskładnikowe jest skonfigurowane dla użytkowników, którzy rejestrują swoje urządzenia. Aby uzyskać więcej informacji na temat różnych usług Multi-Factor Authentication usługi Azure AD, zobacz Rozpoczynanie [pracy z usługą Azure ad Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> **Urządzenia, które mają być przyłączone do usługi Azure AD lub zarejestrowane w usłudze Azure AD ustawienie wymagaj Multi-Factor Authentication** ma zastosowanie do urządzeń, które są dołączone do usługi Azure AD (z pewnymi wyjątkami) lub zarejestrowane w usłudze Azure AD To ustawienie nie dotyczy urządzeń przyłączonych do hybrydowej usługi Azure AD, [przyłączonych do usługi Azure AD maszyn wirtualnych na platformie Azure i w](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) usłudze Azure AD przyłączonych w trybie samoobsługowego [wdrażania systemu Windows](/mem/autopilot/self-deploying).

> [!IMPORTANT]
> - Zalecamy użycie [akcji użytkownika "Zarejestruj lub Dołącz urządzenia"](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) w dostęp warunkowy w celu wymuszenia uwierzytelniania wieloskładnikowego w celu dołączenia lub zarejestrowania urządzenia. 
> - Należy ustawić wartość **nie** , jeśli używasz zasad dostępu warunkowego, aby wymagać authencation wieloskładnikowego. 

- **Maksymalna liczba urządzeń** — to ustawienie pozwala wybrać maksymalną liczbę przyłączonych do usługi Azure AD lub zarejestrowanych urządzeń usługi Azure AD, które użytkownik może mieć w usłudze Azure AD. Jeśli użytkownik osiągnie ten limit przydziału, nie będzie mógł dodać dodatkowych urządzeń, dopóki nie zostaną usunięte co najmniej jedno z istniejących urządzeń. Wartość domyślna to **50**. Można zwiększyć wartość do 100, a jeśli wprowadzisz wartość powyżej 100, usługa Azure AD ustawi ją na 100. Możesz również użyć wartości nieograniczonej, aby wymusić brak limitu przydziału poza istniejącymi limitami.

> [!NOTE]
> Ustawienie **Maksymalna liczba urządzeń** ma zastosowanie do urządzeń, które są przyłączone do usługi Azure AD lub zarejestrowane w usłudze Azure AD. To ustawienie nie dotyczy urządzeń przyłączonych do hybrydowej usługi Azure AD.

- [Roaming stanu dla przedsiębiorstw](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Dzienniki inspekcji

Działania dotyczące urządzeń są dostępne za pomocą dzienników aktywności. Te dzienniki obejmują działania wyzwalane przez usługę rejestracji urządzeń i użytkowników:

- Tworzenie urządzeń i Dodawanie właścicieli/użytkowników na urządzeniu
- Zmiany ustawień urządzenia
- Operacje na urządzeniach, takie jak usuwanie lub aktualizowanie urządzenia

Punktem wejścia do danych inspekcji są **dzienniki inspekcji** w sekcji **aktywność** na stronie **urządzenia** .

Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- Data i godzina wystąpienia
- Elementy docelowe
- Inicjator/aktor (kto) działania
- Działanie (co)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Zrzut ekranu przedstawiający tabelę w sekcji działanie na stronie urządzenia, która wyświetla datę, cel, aktora i aktywność dla czterech dzienników inspekcji." border="false":::

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Zrzut ekranu przedstawiający pasek narzędzi strony urządzenia. Element Columns jest wyróżniony." border="false":::

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz odfiltrować dane inspekcji przy użyciu następujących pól:

- Kategoria
- Typ zasobu działania
- Działanie
- Zakres dat
- Cel
- Zainicjowane przez (aktor)

Oprócz filtrów można wyszukiwać określone wpisy.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Zrzut ekranu kontrolek filtru danych inspekcji, z kategorią, typem zasobu działania, działaniem, zakresem dat, elementem docelowym oraz aktorem i polem wyszukiwania." border="false":::

## <a name="next-steps"></a>Następne kroki

[Jak zarządzać przestarzałymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)

[Roaming stanu dla przedsiębiorstw](enterprise-state-roaming-overview.md)
