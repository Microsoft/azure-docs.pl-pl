---
title: Jak zarządzać urządzeniami za pomocą Azure Portal | Microsoft Docs
description: Dowiedz się, jak za pomocą Azure Portal zarządzać urządzeniami.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262245"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Zarządzanie tożsamościami urządzeń przy użyciu Azure Portal

Za pomocą zarządzania tożsamościami urządzeń w usłudze Azure Active Directory (Azure AD) możesz zapewnić użytkownikom dostęp do zasobów z urządzeń spełniających Twoje standardy zabezpieczeń i zgodności.

W tym artykule:

- Przyjęto założenie, że wiesz już, jak zapoznać się z [wprowadzeniem do zarządzania tożsamościami urządzeń w Azure Active Directory](overview.md)
- Zawiera informacje dotyczące zarządzania tożsamościami urządzeń przy użyciu portalu usługi Azure AD

## <a name="manage-device-identities"></a>Zarządzanie tożsamościami urządzeń

Portal usługi Azure AD udostępnia centralne miejsce do zarządzania tożsamościami urządzeń. Możesz to zrobić za pomocą [linku bezpośredniego](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) lub:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Azure Active Directory** > **urządzeń**.

Na stronie **urządzenia** można:

- Konfigurowanie ustawień urządzenia
- Lokalizowanie urządzeń
- Wykonywanie zadań zarządzania tożsamościami urządzeń
- Przeglądanie dzienników inspekcji związanych z urządzeniem  
  
## <a name="configure-device-settings"></a>Konfiguruj ustawienia urządzenia

Aby zarządzać tożsamościami urządzeń za pomocą portalu usługi Azure AD, urządzenia muszą być [zarejestrowane lub dołączone](overview.md) do usługi Azure AD. Jako administrator możesz dostosować proces rejestrowania i dołączania urządzeń przez skonfigurowanie ustawień urządzenia.

Strona Ustawienia urządzenia umożliwia konfigurowanie ustawień związanych z tożsamościami urządzeń:

![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/21.png)

- **Użytkownicy mogą dołączać urządzenia do usługi Azure AD** — to ustawienie umożliwia wybranie użytkowników, którzy mogą rejestrować swoje urządzenia jako urządzenia przyłączone do usługi Azure AD. Wartością domyślną jest **wszystkich**.

> [!NOTE]
> **Użytkownicy mogą dołączać urządzenia do ustawienia usługi Azure AD** mają zastosowanie tylko do usługi Azure AD JOIN w systemie Windows 10.

- **Dodatkowi Administratorzy lokalni na urządzeniach dołączonych do usługi Azure AD** — możesz wybrać użytkowników, którym udzielono uprawnień administratora lokalnego na urządzeniu. Użytkownicy dodani w tym miejscu zostaną dodani do roli *Administratorzy urządzenia* w usłudze Azure AD. Administratorzy globalni w usłudze Azure AD i właściciele urządzeń mają domyślnie przyznane prawa administratora lokalnego. Ta opcja to funkcja wersji Premium dostępna za pomocą produktów, takich jak Azure AD — wersja Premium lub pakiet Enterprise Mobility Suite (EMS).
- **Użytkownicy mogą zarejestrować swoje urządzenia w usłudze Azure AD** — należy skonfigurować to ustawienie, aby zezwolić na rejestrację urządzeń z systemem Windows 10 Personal, iOS, Android i macOs w usłudze Azure AD. W przypadku wybrania opcji **Brak**urządzenia nie mogą zarejestrować się w usłudze Azure AD. Rejestracja przy użyciu Microsoft Intune lub zarządzania urządzeniami przenośnymi (MDM) dla pakietu Office 365 wymaga rejestracji. W przypadku skonfigurowania jednej z tych usług jest zaznaczona opcja **wszystkie** nie **jest dostępna** .
- **Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń** — możesz określić, czy użytkownicy muszą podać dodatkowy czynnik uwierzytelniania, aby przyłączyć urządzenie do usługi Azure AD. Wartość domyślna to **nie**. Zalecamy wymaganie uwierzytelniania wieloskładnikowego podczas rejestrowania urządzenia. Przed włączeniem uwierzytelniania wieloskładnikowego dla tej usługi należy się upewnić, że uwierzytelnianie wieloskładnikowe jest skonfigurowane dla użytkowników, którzy rejestrują swoje urządzenia. Aby uzyskać więcej informacji na temat różnych usług Azure wieloskładnikowe Authentication, zobacz [wprowadzenie do usługi Azure](../authentication/concept-mfa-whichversion.md)MFA. 

> [!NOTE]
> Ustawienie **Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń** ma zastosowanie do urządzeń, które są dołączone do usługi Azure AD lub zarejestrowane w usłudze Azure AD. To ustawienie nie dotyczy urządzeń przyłączonych do hybrydowej usługi Azure AD.

- **Maksymalna liczba urządzeń** — to ustawienie pozwala wybrać maksymalną liczbę przyłączonych do usługi Azure AD lub zarejestrowanych urządzeń usługi Azure AD, które użytkownik może mieć w usłudze Azure AD. Jeśli użytkownik osiągnie ten limit przydziału, nie będzie mógł dodać dodatkowych urządzeń, dopóki nie zostaną usunięte co najmniej jedno z istniejących urządzeń. Wartość domyślna to **20**.

> [!NOTE]
> Ustawienie **Maksymalna liczba urządzeń** ma zastosowanie do urządzeń, które są przyłączone do usługi Azure AD lub zarejestrowane w usłudze Azure AD. To ustawienie nie dotyczy urządzeń przyłączonych do hybrydowej usługi Azure AD.

- **Użytkownicy mogą synchronizować ustawienia i dane aplikacji między urządzeniami** — domyślnie to ustawienie ma wartość **Brak**. Wybranie określonych użytkowników lub grup lub wszystkie zezwala na synchronizację ustawień i danych aplikacji na urządzeniach z systemem Windows 10. Dowiedz się więcej o tym, jak działa synchronizacja w systemie Windows 10.
Ta opcja to funkcja Premium dostępna za pomocą produktów, takich jak Azure AD — wersja Premium lub pakiet Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Lokalizowanie urządzeń

Dostępne są dwie opcje lokalizowania zarejestrowanych i dołączonych urządzeń:

- **Wszystkie urządzenia** w sekcji **Zarządzanie** na stronie **urządzenia**  
- **Urządzenia** w sekcji **Zarządzanie** na stronie **użytkownika**

W przypadku obu opcji można przejść do widoku, który:

- Umożliwia wyszukiwanie urządzeń przy użyciu nazwy wyświetlanej lub identyfikatora urządzenia jako filtru.
- Zawiera szczegółowy przegląd zarejestrowanych i dołączonych urządzeń
- Umożliwia wykonywanie typowych zadań związanych z zarządzaniem urządzeniami

![Wszystkie urządzenia](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Jeśli zostanie wyświetlone urządzenie z informacją o stanie "oczekujące" w ramach zarejestrowanej kolumny, oznacza to, że urządzenie zostało zsynchronizowane z programu Azure AD Connect i oczekuje na ukończenie rejestracji z poziomu klienta. Przeczytaj więcej na temat [planowania implementacji hybrydowego dołączania do usługi Azure AD](hybrid-azuread-join-plan.md). Dodatkowe informacje znajdują się w artykule [często zadawane pytania dotyczące urządzeń](faq.md).
>
>   ![Urządzenia oczekujące](./media/device-management-azure-portal/75.png)
>
>* W przypadku niektórych urządzeń z systemem iOS nazwy urządzeń zawierające apostrofy mogą potencjalnie używać różnych znaków, które wyglądają jak apostrofy. Dlatego wyszukiwanie takich urządzeń jest nieco trudne — jeśli wyniki wyszukiwania nie są prawidłowo wyświetlane, upewnij się, że ciąg wyszukiwania zawiera pasujący znak apostrofu.

## <a name="device-identity-management-tasks"></a>Zadania zarządzania tożsamościami urządzeń

Jako Administrator globalny lub administrator urządzenia w chmurze możesz zarządzać zarejestrowanymi lub przyłączonymi urządzeniami. Administratorzy usługi Intune mogą:

- Aktualizacja urządzeń — przykłady to codzienne operacje, takie jak Włączanie/wyłączanie urządzeń
- Usuwanie urządzeń — Jeśli urządzenie zostało wycofane i powinno zostać usunięte w usłudze Azure AD

Ta sekcja zawiera informacje o typowych zadaniach związanych z zarządzaniem tożsamościami urządzeń.

### <a name="manage-an-intune-device"></a>Zarządzanie urządzeniem usługi Intune

Jeśli jesteś administratorem usługi Intune, możesz zarządzać urządzeniami oznaczonymi jako **Microsoft Intune**. Jeśli urządzenie nie jest zarejestrowane w Microsoft Intune opcja "Zarządzaj" będzie wyszarzona.

![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Włączanie/wyłączanie urządzenia usługi Azure AD

Aby włączyć/wyłączyć urządzenie, dostępne są dwie opcje:

- Menu zadania ("...") na stronie **wszystkie urządzenia**

   ![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/71.png)

- Pasek narzędzi na stronie **urządzenia**

   ![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/32.png)

**Uwagi**

- Aby włączyć/wyłączyć urządzenie, musisz być administratorem globalnym lub administratorem urządzenia w chmurze w usłudze Azure AD. 
- Wyłączenie urządzenia zapobiega pomyślnym uwierzytelnieniu urządzenia w usłudze Azure AD, uniemożliwiając dostęp urządzenia do zasobów usługi Azure AD chronionych przez urząd certyfikacji urządzenia lub przy użyciu poświadczeń WH4B.
- Wyłączenie urządzenia spowoduje odwołanie podstawowego tokenu odświeżania (PRT) oraz wszystkich tokenów odświeżania (RT) na urządzeniu.

### <a name="delete-an-azure-ad-device"></a>Usuwanie urządzenia usługi Azure AD

Aby usunąć urządzenie, dostępne są dwie opcje:

- Menu zadania ("...") na stronie **wszystkie urządzenia**

   ![Zarządzanie urządzeniem usługi Intune](./media/device-management-azure-portal/72.png)

- Pasek narzędzi na stronie **urządzenia**

   ![Usuwanie urządzenia](./media/device-management-azure-portal/34.png)

**Uwagi**

- Aby usunąć urządzenie, musisz być administratorem globalnym lub administratorem usługi Intune w usłudze Azure AD.
- Usuwanie urządzenia:
   - Uniemożliwia urządzeniu dostęp do zasobów usługi Azure AD.
   - Usuwa wszystkie szczegóły, które są dołączone do urządzenia, na przykład klucze funkcji BitLocker dla urządzeń z systemem Windows.  
   - Reprezentuje nieodzyskiwalne działanie i nie jest zalecane, chyba że jest to wymagane.

Jeśli urządzenie jest zarządzane przez inny urząd zarządzania (na przykład Microsoft Intune), upewnij się, że urządzenie zostało wyczyszczone/wycofane przed usunięciem urządzenia w usłudze Azure AD. Zapoznaj się z tematem jak [zarządzać przestarzałymi urządzeniami](device-management-azure-portal.md) przed usunięciem wszystkich urządzeń.

### <a name="view-or-copy-device-id"></a>Wyświetl lub Skopiuj identyfikator urządzenia

IDENTYFIKATORA urządzenia można użyć do sprawdzenia szczegółów identyfikatora urządzenia na urządzeniu lub przy rozwiązywaniu problemów przy użyciu programu PowerShell. Aby uzyskać dostęp do opcji kopiowania, kliknij urządzenie.

![Wyświetlanie identyfikatora urządzenia](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Wyświetl lub Skopiuj klucze funkcji BitLocker

Można wyświetlać i kopiować klucze funkcji BitLocker, aby ułatwić użytkownikom Odzyskiwanie zaszyfrowanego dysku. Te klucze są dostępne tylko dla urządzeń z systemem Windows, które są szyfrowane i mają klucze przechowywane w usłudze Azure AD. Te klucze można skopiować podczas uzyskiwania dostępu do szczegółów urządzenia.

![Wyświetl klucze funkcji BitLocker](./media/device-management-azure-portal/36.png)

Aby wyświetlić lub skopiować klucze funkcji BitLocker, musisz być właścicielem urządzenia lub użytkownikiem, który ma co najmniej jedną z następujących ról:

- Administrator urządzenia w chmurze
- Administrator globalny
- Administrator pomocy technicznej
- Administrator usługi Intune
- Administrator zabezpieczeń
- Czytelnik zabezpieczeń

> [!NOTE]
> Hybrydowe urządzenia z systemem Windows 10 połączone z usługą Azure AD nie mają właściciela. Jeśli więc szukasz urządzenia według właściciela i nie znajdziesz go, wyszukaj identyfikator urządzenia.

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

![Dzienniki inspekcji](./media/device-management-azure-portal/63.png)

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Dzienniki inspekcji](./media/device-management-azure-portal/64.png)

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz odfiltrować dane inspekcji przy użyciu następujących pól:

- Kategoria
- Typ zasobu działania
- Działanie
- Zakres dat
- Środowisko docelowe
- Zainicjowane przez (aktor)

Oprócz filtrów można wyszukiwać określone wpisy.

![Dzienniki inspekcji](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Następne kroki

[Jak zarządzać przestarzałymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
