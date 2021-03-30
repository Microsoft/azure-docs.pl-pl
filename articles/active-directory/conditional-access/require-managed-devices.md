---
title: Dostęp warunkowy wymaga urządzenia zarządzanego — Azure Active Directory
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego oparte na urządzeniach Azure Active Directory (Azure AD), które wymagają zarządzanych urządzeń w celu uzyskania dostępu do aplikacji w chmurze.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077765"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Instrukcje: wymaganie zarządzanych urządzeń dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego

W świecie w chmurze na urządzeniach przenośnych Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do aplikacji i usług z dowolnego miejsca. Autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze z szeroką gamę urządzeń, w tym urządzeń przenośnych i osobistych. Jednak wiele środowisk ma co najmniej kilka aplikacji, do których mają dostęp tylko urządzenia spełniające standardy dotyczące zabezpieczeń i zgodności. Te urządzenia są również znane jako urządzenia zarządzane. 

W tym artykule wyjaśniono, jak można skonfigurować zasady dostępu warunkowego, które wymagają, aby zarządzane urządzenia mogły uzyskiwać dostęp do niektórych aplikacji w chmurze w danym środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

Wymaganie, aby zarządzane urządzenia dla dostępu do aplikacji w chmurze były połączone z **dostępem warunkowym usługi Azure AD** i **zarządzaniem urządzeniami usługi Azure AD** . Jeśli nie znasz jeszcze jednego z tych obszarów, najpierw Przeczytaj następujące tematy:

- **[Dostęp warunkowy w Azure Active Directory](./overview.md)** — ten artykuł zawiera omówienie pojęć dotyczących dostępu warunkowego i powiązanej terminologii.
- **[Wprowadzenie do zarządzania urządzeniami w Azure Active Directory](../devices/overview.md)** — ten artykuł zawiera omówienie różnych opcji, które należy wykonać, aby uzyskać dostęp do urządzeń objętych kontrolą organizacyjną. 
- Aby uzyskać pomoc techniczną dla programu Chrome w **aktualizacji systemu Windows 10 dla twórców (wersja 1703)** lub nowszą, zainstaluj [rozszerzenie konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). To rozszerzenie jest wymagane, gdy zasady dostępu warunkowego wymagają szczegółowych informacji dotyczących urządzenia.

>[!NOTE] 
> Zalecamy korzystanie z zasad dostępu warunkowego opartego na urządzeniach z usługą Azure AD w celu uzyskania najlepszego wymuszenia po początkowym uwierzytelnianiu urządzenia. Obejmuje to zamykanie sesji, jeśli urządzenie przestanie być zgodne i przepływ kodu urządzenia.


## <a name="scenario-description"></a>Opis scenariusza

Głównym kompromisem jest równowaga między bezpieczeństwem a produktywnością. Rozprzestrzenianie się obsługiwanych urządzeń w celu uzyskania dostępu do zasobów w chmurze ułatwia zwiększenie produktywności użytkowników. Na stronie odwracania prawdopodobnie nie chcesz, aby niektóre zasoby w danym środowisku były dostępne na urządzeniach z nieznanym poziomem ochrony. W przypadku zasobów, których to dotyczy, należy wymagać, aby użytkownicy mieli do nich dostęp tylko przy użyciu urządzenia zarządzanego. 

Za pomocą dostępu warunkowego usługi Azure AD można rozwiązać ten wymóg przy użyciu jednej zasady, która udziela dostępu:

- Do wybranych aplikacji w chmurze
- Dla wybranych użytkowników i grup
- Wymaganie urządzenia zarządzanego

## <a name="managed-devices"></a>Urządzenia zarządzane  

W przypadku prostych warunków zarządzanymi urządzeniami są urządzenia, które podlegają *pewnemu sortowaniu* kontroli organizacyjnej. W usłudze Azure AD wymaganie wstępne dla zarządzanego urządzenia polega na tym, że zostało ono zarejestrowane w usłudze Azure AD. Zarejestrowanie urządzenia tworzy tożsamość urządzenia w postaci obiektu urządzenia. Ten obiekt jest używany przez platformę Azure do śledzenia informacji o stanie urządzenia. Jako administrator usługi Azure AD można już używać tego obiektu do przełączania (włączania/wyłączania) stanu urządzenia.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Zrzut ekranu przedstawiający okienko urządzenia na platformie Azure A D. Włączanie i wyłączanie elementów jest wyróżniony." border="false":::

Aby uzyskać urządzenie zarejestrowane w usłudze Azure AD, masz trzy opcje: 

- **Zarejestrowane urządzenia usługi Azure AD** — Aby uzyskać osobiste urządzenie zarejestrowane w usłudze Azure AD
- **Urządzenia przyłączone do usługi Azure AD** — Aby uzyskać organizacyjne urządzenie z systemem Windows 10, które nie jest przyłączone do lokalnej usługi AD zarejestrowanej w usłudze Azure AD. 
- **Hybrydowe urządzenia dołączone do usługi Azure AD** — Aby uzyskać system Windows 10 lub obsługiwane urządzenie niskiego poziomu, które jest przyłączone do lokalnej usługi AD zarejestrowanej w usłudze Azure AD.

Te trzy opcje zostały omówione w artykule [co to jest tożsamość urządzenia?](../devices/overview.md)

Aby zostać urządzeniem zarządzanym, zarejestrowane urządzenie musi być **urządzeniem przyłączonym do hybrydowej usługi Azure AD** lub **urządzeniem, które zostało oznaczone jako zgodne**.  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Zrzut ekranu przedstawiający okienko dotacji na platformie Azure A D. Wybrano opcję Udziel dostępu, ponieważ są to pola wyboru dla urządzeń, które mają być zgodne, i hybrydowe przyłączone do platformy Azure A D." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Wymagaj hybrydowych urządzeń przyłączonych do usługi Azure AD

W zasadach dostępu warunkowego można wybrać opcję **Wymagaj urządzenia dołączonego do hybrydowej usługi Azure AD** , aby można było uzyskać dostęp do wybranych aplikacji w chmurze tylko przy użyciu urządzenia zarządzanego. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Zrzut ekranu przedstawiający okienko dotacji na platformie Azure A D. Wybrano udzielenie dostępu. Zaznaczone jest również pole wyboru, które wymaga, aby urządzenia były hybrydową platformą Azure A D." border="false":::

To ustawienie dotyczy tylko urządzeń z systemem Windows 10 lub niższego poziomu, takich jak Windows 7 lub Windows 8, które są przyłączone do lokalnej usługi AD. Te urządzenia można zarejestrować tylko w usłudze Azure AD przy użyciu hybrydowego sprzężenia usługi Azure AD, które jest [zautomatyzowanym procesem](../devices/hybrid-azuread-join-plan.md) uzyskiwania zarejestrowanego urządzenia z systemem Windows 10. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="Tabela zawierająca listę nazw, stan włączony, O S, wersję, typ sprzężenia, właściciela, M D M i zgodny stan urządzenia. Stan zgodności to nie." border="false":::

Co sprawia, że urządzenie dołączone do hybrydowej usługi Azure AD jest urządzeniem zarządzanym?  W przypadku urządzeń, które są przyłączone do lokalnej usługi AD, zakłada się, że kontrola nad tymi urządzeniami jest wymuszana przy użyciu rozwiązań do zarządzania, takich jak **Configuration Manager** lub **zasady grupy (GP)** w celu zarządzania nimi. Ponieważ nie ma metody usługi Azure AD w celu ustalenia, czy którakolwiek z tych metod została zastosowana do urządzenia, wymaganie hybrydowego urządzenia dołączonego do usługi Azure AD jest stosunkowo słabym mechanizmem wymaganym przez urządzenie zarządzane. Administratorzy mogą ocenić, czy metody, które są stosowane do lokalnych urządzeń przyłączonych do domeny, są wystarczająco duże, aby stanowiły urządzenie zarządzane, jeśli takie urządzenie jest również urządzeniem przyłączonym do hybrydowej usługi Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Opcja wymagania, aby *urządzenie było oznaczone jako zgodne* , jest najmocniejszą formą żądania urządzenia zarządzanego.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Zrzut ekranu przedstawiający okienko dotacji na platformie Azure A D. Wybrano udzielenie dostępu. Zaznaczone jest również pole wyboru, które wymaga, aby urządzenie było oznaczone jako zgodne." border="false":::

Ta opcja wymaga, aby urządzenie było zarejestrowane w usłudze Azure AD, a także oznaczone jako zgodne przez:
         
- Intune
- System zarządzania urządzeniami przenośnymi (MDM, Mobile Device Management) służący jako urządzenie z systemem Windows 10 w ramach integracji z usługą Azure AD. Systemy zarządzania urządzeniami przenośnymi innych firm dla typów systemów operacyjnych innych niż Windows 10 nie są obsługiwane.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="Tabela zawierająca listę nazw, stan włączony, O S, wersję, typ sprzężenia, właściciela, M D M i zgodny stan urządzenia. Stan zgodności jest wyróżniony." border="false":::

W przypadku urządzenia oznaczonego jako zgodnego można założyć, że: 

- Urządzenia przenośne używane przez pracowników do uzyskiwania dostępu do danych firmowych są zarządzane
- Zarządzanie aplikacjami mobilnymi używanymi przez pracowników
- Informacje o firmie są chronione przez pomoc w kontroli sposobu, w jaki pracownicy uzyskują do nich dostęp i udostępniają ją
- Urządzenie i jego aplikacje są zgodne z wymaganiami firmy dotyczącymi zabezpieczeń

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Scenariusz: wymaganie rejestracji urządzenia dla urządzeń z systemami iOS i Android

W tym scenariuszu firma Contoso zdecydowała się, że wszystkie dostępy mobilne do zasobów Microsoft 365 muszą używać zarejestrowanego urządzenia. Wszyscy użytkownicy logują się już przy użyciu poświadczeń usługi Azure AD i mają przypisane licencje, które obejmują Azure AD — wersja Premium P1 lub P2 i Microsoft Intune.

Aby wymagać korzystania z zarejestrowanego urządzenia przenośnego, organizacje muszą wykonać następujące czynności.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz opcję **Wszyscy użytkownicy** lub określeni **Użytkownicy i grupy** , do których chcesz zastosować te zasady. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**  >  **Uwzględnij** opcję **Office 365**.
1. W obszarze **warunki** wybierz pozycję **platformy urządzeń**.
   1. Ustaw **wartość** **tak**.
   1. Uwzględnij **systemy Android** i **iOS**.
1. W obszarze **kontrole dostępu**  >  **Udziel** wybierz następujące opcje:
   - **Wymagaj, aby urządzenie było oznaczone jako zgodne**
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć i włączyć zasady.

### <a name="known-behavior"></a>Znane zachowanie

W przypadku korzystania z [przepływu OAuth przy użyciu kodu urządzenia](../develop/v2-oauth2-device-code.md)nie jest obsługiwana kontrola Wymagaj zarządzanego urządzenia lub stanu urządzenia. Wynika to z faktu, że urządzenie wykonujące uwierzytelnianie nie może dostarczyć stanu urządzenia do urządzenia dostarczającego kod, a stan urządzenia w tokenie jest zablokowany na urządzeniu, na którym jest wykonywane uwierzytelnianie. Zamiast tego użyj kontrolki Wymagaj uwierzytelniania wieloskładnikowego.

W systemach Windows 7, iOS, Android, macOS i niektórych przeglądarkach sieci Web innych firm usługa Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest inicjowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD. Gdy użytkownik po raz pierwszy zaloguje się za pomocą przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik końcowy musi wybrać ten certyfikat, zanim będzie mógł kontynuować korzystanie z przeglądarki.

## <a name="next-steps"></a>Następne kroki

[Oceń wpływ zasad dostępu warunkowego przed udostępnieniem ich w trybie tylko do raportowania](concept-conditional-access-report-only.md).
