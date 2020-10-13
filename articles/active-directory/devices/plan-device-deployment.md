---
title: Planowanie wdrożenia urządzenia Azure Active Directory
description: Wybierz strategie integracji urządzeń z usługą Azure AD, które spełniają Twoje potrzeby organizacyjne.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d3e3c174ad9a3372df084cac9eb67270779298
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563900"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Planowanie wdrożenia urządzenia Azure Active Directory

Ten artykuł pomaga w ocenie metod integrowania urządzenia z usługą Azure AD, wybierania planu implementacji i zapewniania najważniejszych linków do obsługiwanych narzędzi do zarządzania urządzeniami.

Poziomy urządzeń, z których logują się użytkownicy, rozwijają się. Organizacje mogą udostępniać komputery stacjonarne, laptopy, telefony, tablety i inne urządzenia. Użytkownicy mogą przenieść swoją własną tablicę urządzeń i uzyskać dostęp do informacji z różnych lokalizacji. W tym środowisku Twoje zadanie jako administrator ma zapewnić bezpieczeństwo zasobów organizacji na wszystkich urządzeniach.

Azure Active Directory (Azure AD) umożliwia organizacji spełnienie tych celów przy użyciu usługi zarządzania tożsamościami urządzeń. Teraz możesz uzyskać dostęp do urządzeń w usłudze Azure AD i kontrolować je z centralnej lokalizacji w [Azure Portal](https://portal.azure.com/). Zapewnia to ujednolicone środowisko, ulepszone zabezpieczenia i skraca czas wymagany do skonfigurowania nowego urządzenia.

Istnieje wiele metod integrowania urządzeń z usługą Azure AD:

* Możesz [zarejestrować urządzenia](concept-azure-ad-register.md) w usłudze Azure AD

* [Dołączanie urządzeń](concept-azure-ad-join.md) do usługi Azure AD (tylko w chmurze) lub

* [Utwórz hybrydowe sprzężenie usługi Azure AD](concept-azure-ad-join-hybrid.md) między urządzeniami w lokalnym Active Directory i usłudze Azure AD. 

## <a name="learn"></a>Learn

Przed rozpoczęciem upewnij się, że znasz już [Omówienie zarządzania tożsamościami urządzeń](overview.md).

### <a name="benefits"></a>Korzyści

Najważniejsze zalety nadawania urządzeniom tożsamości usługi Azure AD:

* Zwiększ produktywność — dzięki usłudze Azure AD użytkownicy mogą [bezproblemowo zalogować](./azuread-join-sso.md) się do zasobów lokalnych i w chmurze, co umożliwia ich produktywność wszędzie tam, gdzie się znajdują.

* Zwiększ bezpieczeństwo — urządzenia usługi Azure AD umożliwiają stosowanie [zasad dostępu warunkowego (CA)](../conditional-access/require-managed-devices.md) do zasobów na podstawie tożsamości urządzenia lub użytkownika. Zasady urzędu certyfikacji mogą oferować dodatkową ochronę przy użyciu [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md). Przyłączanie urządzenia do usługi Azure AD jest wymaganiem wstępnym w celu zwiększenia bezpieczeństwa dzięki strategii [uwierzytelniania bez hasła](../authentication/concept-authentication-passwordless.md) .

* Ulepsz środowisko użytkownika — z tożsamościami urządzeń w usłudze Azure AD możesz zapewnić użytkownikom łatwy dostęp do zasobów w chmurze w organizacji zarówno z urządzeń osobistych, jak i firmowych. Administratorzy mogą włączać [Enterprise State roaming](enterprise-state-roaming-overview.md) w celu zapewnienia ujednoliconego środowiska na wszystkich urządzeniach z systemem Windows.

* Uproszczenie wdrażania i zarządzania — Zarządzanie tożsamościami urządzeń upraszcza proces przenoszenia urządzeń do usługi Azure AD przy użyciu funkcji automatycznego [pilotażu systemu Windows](/windows/deployment/windows-autopilot/windows-10-autopilot), [aprowizacji zbiorczego](/mem/intune/enrollment/windows-bulk-enroll)i samoobsługowego [korzystania z usługi Box (OOBE)](../user-help/user-help-join-device-on-network.md). Tymi urządzeniami można zarządzać przy użyciu narzędzi do zarządzania urządzeniami przenośnymi (MDM), takich jak [Microsoft Intune](/mem/intune/fundamentals/what-is-intune), oraz ich tożsamości w [Azure Portal](https://portal.azure.com/).

### <a name="training-resources"></a>Zasoby szkoleniowe

Wideo:  [dostęp warunkowy z kontrolkami urządzeń](https://youtu.be/NcONUf-jeS4)

Często zadawane pytania: NZP i [Ustawienia](enterprise-state-roaming-faqs.md) [usługi Azure AD Device Management](faq.md) 

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożenia

Podczas określania strategii tego wdrożenia w danym środowisku należy wziąć pod uwagę potrzeby organizacyjne.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnością oczekiwań, rezultatów i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że masz odpowiednie osoby zainteresowane](https://aka.ms/deploymentplans) i że role udziałowców w projekcie są dobrze zrozumiałe. 

Dla tego planu Dodaj następujące osoby zainteresowane do listy:

| Rola| Opis |
| - | - |
| Administrator urządzenia| Przedstawiciel zespołu urządzeń, który może sprawdzić, czy plan spełni wymagania organizacji. |
| Administrator sieci| Przedstawiciel zespołu sieci, który może upewnić się, że spełnia wymagania sieci. |
| Zespół zarządzający urządzeniami| Zespół Zarządzający spisem urządzeń. |
| Zespoły administratorów specyficzne dla systemu operacyjnego| Zespoły, które obsługują i zarządzają określonymi wersjami systemów operacyjnych. Na przykład może istnieć skoncentrowany zespół Mac lub iOS. |

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Proaktywnie Komunikuj się z użytkownikami, w jaki sposób ich środowisko zostanie zmienione, gdy ulegnie zmianie, i jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Zalecamy, aby początkowa konfiguracja metody integracji była w środowisku testowym lub małą grupą urządzeń testowych. Zapoznaj [się z najlepszymi rozwiązaniami dla pilotażu](../fundamentals/active-directory-deployment-plans.md).

Wdrożenie hybrydowego połączenia z usługą Azure AD jest proste i jest 100% zadania administratora, bez konieczności wykonywania czynności przez użytkownika końcowego. Możesz chcieć przeprowadzić [kontrolowane sprawdzenie poprawności hybrydowego sprzężenia usługi Azure AD](hybrid-azuread-join-control.md) przed włączeniem jej w całej organizacji.

## <a name="choose-your-integration-methods"></a>Wybierz metody integracji

Organizacja może używać wielu metod integracji urządzeń w jednej dzierżawie usługi Azure AD. Celem jest wybranie metod, które są odpowiednie do bezpiecznego zarządzania urządzeniami w usłudze Azure AD. Istnieje wiele parametrów, które obejmują tę decyzję, w tym własność, typy urządzeń, odbiorców podstawowych i infrastrukturę organizacji.

Poniższe informacje mogą pomóc zdecydować, które metody integracji mają być używane.

### <a name="decision-tree-for-devices-integration"></a>Drzewo decyzyjne dla integracji urządzeń

To drzewo służy do określania opcji dla urządzeń należących do organizacji. 

> [!NOTE]
> Na tym diagramie nie są zgodne żadne scenariusze dotyczące urządzeń osobistych (BYOD). Zawsze są wynikiem rejestracji w usłudze Azure AD.

 ![Drzewo decyzyjne](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Macierz porównawcza

urządzenia z systemami iOS i Android mogą być zarejestrowane wyłącznie w usłudze Azure AD. W poniższej tabeli przedstawiono zagadnienia dotyczące wysokiego poziomu dla urządzeń klienckich z systemem Windows. Użyj jej jako przeglądu, a następnie szczegółowo zapoznaj się z różnymi metodami integracji.

| Kwestie do rozważenia | Zarejestrowane w usłudze Azure AD| Dołączenie do usługi Azure AD| Dołączenie hybrydowe do usługi Azure AD |
| - | - | - | - |
| **Klienckie systemy operacyjne**| | |  |
| Urządzenia z systemem Windows 10| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Urządzenia niskiego poziomu systemu Windows (Windows 8.1 lub Windows 7)| | | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
|**Opcje logowania**| | |  |
| Poświadczenia lokalne użytkownika końcowego| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| |  |
| Hasło| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Numer PIN urządzenia| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello for Business| | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Klucze zabezpieczeń FIDO 2,0| | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Aplikacja Microsoft Authenticator (bezhaseł)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
|**Najważniejsze możliwości**| | |  |
| Logowanie jednokrotne do zasobów w chmurze| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Logowanie jednokrotne do zasobów lokalnych| | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Dostęp warunkowy <br> (Wymagaj, aby urządzenia były oznaczone jako zgodne) <br> (Musi być zarządzany przez MDM)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)|![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
Dostęp warunkowy <br>(Wymagaj hybrydowych urządzeń przyłączonych do usługi Azure AD)| | | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)
| Samoobsługowe resetowanie haseł z ekranu logowania systemu Windows| | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Resetowanie numeru PIN systemu Windows Hello| | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |
| Roaming stanu przedsiębiorstwa między urządzeniami| | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Rejestracja w usłudze Azure AD 

Zarejestrowane urządzenia są często zarządzane przy użyciu [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Urządzenia są zarejestrowane w usłudze Intune na wiele sposobów, w zależności od systemu operacyjnego. 

Zarejestrowane urządzenia usługi Azure AD zapewniają obsługę logowania do zasobów w chmurze na urządzeniach z systemem (BYOD) i należących do firmy. Dostęp do zasobów jest oparty na [zasadach urzędu certyfikacji](../conditional-access/require-managed-devices.md) usługi Azure AD stosowanych do urządzenia i użytkownika.

### <a name="registering-devices"></a>Rejestrowanie urządzeń

Zarejestrowane urządzenia są często zarządzane przy użyciu [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Urządzenia są zarejestrowane w usłudze Intune na wiele sposobów, w zależności od systemu operacyjnego. 

Urządzenia przenośne BYOD i należące do firmy są rejestrowane przez użytkowników instalujących aplikację Portal firmy.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Jeśli zarejestrowanie urządzeń jest najlepszą opcją dla Twojej organizacji, zobacz następujące zasoby:

* To omówienie [zarejestrowanych urządzeń usługi Azure AD](concept-azure-ad-register.md).

* Ta dokumentacja użytkownika końcowego dotycząca [rejestrowania urządzenia osobistego w sieci organizacji](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Dołączenie do usługi Azure AD

Usługa Azure AD Join umożliwia przejście do modelu w chmurze z systemem Windows. Zapewnia to doskonałą podstawę, jeśli planujesz modernizację zarządzania urządzeniami i obniżyć koszty IT związane z urządzeniami. Usługa Azure AD Join współpracuje tylko z urządzeniami z systemem Windows 10. Weź pod uwagę pierwszy wybór dla nowych urządzeń.

Jednak [urządzenia przyłączone do usługi Azure AD mogą być logowaniem JEDNOkrotnym do zasobów lokalnych](azuread-join-sso.md) , gdy znajdują się w sieci organizacji, mogą uwierzytelniać się na serwerach lokalnych, takich jak pliki, drukowanie i inne aplikacje.

Jeśli jest to najlepsza opcja dla Twojej organizacji, zobacz następujące zasoby:

* Ten przegląd [urządzeń przyłączonych do usługi Azure AD](concept-azure-ad-join.md).

* Zapoznaj się z [planem wdrożenia usługi Azure AD Join](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Inicjowanie połączenia usługi Azure AD z urządzeniami

W celu aprowizacji usługi Azure AD Join masz następujące podejścia:

* Samoobsługa: [pierwsze uruchomienie systemu Windows 10](azuread-joined-devices-frx.md)

Jeśli masz system Windows 10 Professional lub Windows 10 Enterprise zainstalowany na urządzeniu, środowisko użytkownika domyślnie rozpocznie proces konfiguracji urządzeń firmowych.

* [Środowisko Windows out of Box (OOBE) lub ustawienia systemu Windows](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Rejestracja Zbiorcza](/mem/intune/enrollment/windows-bulk-enroll)

Po dokładnym [porównaniu z tymi podejściami](azureadjoin-plan.md)wybierz swoją procedurę wdrażania.

Możesz określić, że usługa Azure AD Join jest najlepszym rozwiązaniem dla urządzenia, a to urządzenie może być w innym stanie. Poniżej przedstawiono zagadnienia dotyczące uaktualniania.

| Bieżący stan urządzenia| Żądany stan urządzenia| Porady |
| - | - | - |
| Lokalna domena przyłączona| Sprzężenia Azure AD| Odłączanie urządzenia od domeny lokalnej przed przystąpieniem do usługi Azure AD |
| Sprzężenie hybrydowe usługi Azure AD| Sprzężenia Azure AD| Odłączanie urządzenia od domeny lokalnej i z usługi Azure AD przed przystąpieniem do usługi Azure AD |
| Zarejestrowane w usłudze Azure AD| Sprzężenia Azure AD| Wyrejestruj urządzenie przed przystąpieniem do usługi Azure AD |


## <a name="hybrid-azure-ad-join"></a>Dołączenie hybrydowe do usługi Azure AD

Jeśli masz lokalne środowisko Active Directory i chcesz dołączyć komputery przyłączone do domeny usługi Active Directory do usługi Azure AD, możesz to zrobić za pomocą sprzężenia hybrydowego usługi Azure AD. Obsługuje [szeroką gamę urządzeń z systemem Windows](hybrid-azuread-join-plan.md), w tym zarówno urządzenia Windows Current, jak i Windows niższego poziomu.

Większość organizacji dysponuje już urządzeniami przyłączonymi do domeny i zarządza nimi za pośrednictwem zasady grupy lub System Center Configuration Manager (SCCM). W takim przypadku zalecamy skonfigurowanie funkcji dołączania hybrydowej usługi Azure AD, aby zacząć uzyskiwać korzyści, wykorzystując istniejące inwestycje.

Jeśli dołączenie do hybrydowej usługi Azure AD jest najlepszą opcją dla Twojej organizacji, zobacz następujące zasoby:

* To omówienie [hybrydowych urządzeń przyłączonych do usługi Azure AD](concept-azure-ad-join-hybrid.md).

* Zapoznaj się z planem [implementacji hybrydowego dołączania do usługi Azure AD](hybrid-azuread-join-plan.md) .

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Inicjowanie obsługi hybrydowej funkcji dołączania usługi Azure AD na urządzeniach

[Zapoznaj się z infrastrukturą tożsamości](hybrid-azuread-join-plan.md). Azure AD Connect udostępnia kreatora umożliwiającego skonfigurowanie hybrydowego sprzężenia usługi Azure AD dla:

* [Domeny federacyjne](hybrid-azuread-join-federated-domains.md)

* [Domeny zarządzane](hybrid-azuread-join-managed-domains.md)

Jeśli instalacja wymaganej wersji Azure AD Connect nie jest opcją dla Ciebie, zobacz [Ręczne konfigurowanie hybrydowego przyłączania do usługi Azure AD](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> Lokalne przyłączone do domeny urządzenie z systemem Windows 10 próbuje nawiązać połączenie z usługą Azure AD, aby domyślnie przyłączyć się do hybrydowej usługi Azure AD. Będzie to możliwe tylko wtedy, gdy skonfigurowano odpowiednie środowisko. 

Możesz określić, że usługa Azure AD Join jest najlepszym rozwiązaniem dla urządzenia, a to urządzenie może być w innym stanie. Poniżej przedstawiono zagadnienia dotyczące uaktualniania.

| Bieżący stan urządzenia| Żądany stan urządzenia| Porady |
| - | - | - |
| Lokalne przyłączanie do domeny| Sprzężenie hybrydowe usługi Azure AD| Używanie programu Azure AD Connect lub AD FS do przyłączania do platformy Azure |
| Lokalna grupa robocza dołączona lub Nowa| Sprzężenie hybrydowe usługi Azure AD| Obsługiwane w przypadku używania [autopilotażu systemu Windows](/windows/deployment/windows-autopilot/windows-autopilot). W przeciwnym razie urządzenie musi być domeną lokalną przyłączoną przed dołączeniem do hybrydowej usługi Azure AD |
| Dołączone do usługi Azure AD| Sprzężenie hybrydowe usługi Azure AD| Odłączanie od usługi Azure AD, która umieszcza ją w lokalnej grupie roboczej lub nowym stanie. |
| Rejestracja w usłudze Azure AD| Sprzężenie hybrydowe usługi Azure AD| Zależy od wersji systemu Windows. [Zobacz te zagadnienia](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Zarządzanie urządzeniami

Po zarejestrowaniu lub dołączeniu urządzeń do usługi Azure AD Użyj [Azure Portal](https://portal.azure.com/) jako centralnej lokalizacji, aby zarządzać tożsamościami urządzeń. Strona Azure Active Directory urządzeń umożliwia:

* [Konfigurowanie ustawień urządzenia](device-management-azure-portal.md#configure-device-settings)
* Aby zarządzać urządzeniami z systemem Windows, musisz być administratorem lokalnym. [Usługa Azure AD aktualizuje te członkostwo dla urządzeń przyłączonych do usługi Azure AD](assign-local-admin.md), automatycznie dodając je do roli Menedżera urządzeń jako administratorów do wszystkich dołączonych urządzeń.

Upewnij się, że środowisko jest oczyszczane przez [Zarządzanie przestarzałymi urządzeniami](manage-stale-devices.md)i skoncentrowanie się na zasobach związanych z zarządzaniem bieżącymi urządzeniami.

* [Przeglądanie dzienników inspekcji związanych z urządzeniem](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Obsługiwane narzędzia do zarządzania urządzeniami

Administratorzy mogą bezpiecznie kontrolować te zarejestrowane i przyłączone urządzenia przy użyciu dodatkowych narzędzi do zarządzania urządzeniami. Dzięki tym narzędziom można wymusić konfiguracje wymagane przez organizację, takie jak wymaganie zaszyfrowania magazynu, złożoność haseł, instalacje oprogramowania i aktualizacje oprogramowania. 

Przejrzyj obsługiwane i nieobsługiwane platformy dla urządzeń zintegrowanych:

| Narzędzia do zarządzania urządzeniami| Zarejestrowane w usłudze Azure AD| Dołączenie do usługi Azure AD| Dołączenie hybrydowe do usługi Azure AD|
| - | - | - | - |
| [Zarządzanie urządzeniami przenośnymi (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Przykład: Microsoft Intune| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)|  |
| [Zarządzanie przy użyciu Microsoft Intune i Microsoft Endpoint Configuration Manager](/mem/configmgr/comanage/overview) <br>(System Windows 10 i nowsze)| | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)| ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)|  |
| [Zasady grupy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Tylko system Windows)| | | ![Zaznacz te wartości.](./media/plan-device-deployment/check.png)|  |



 Zalecamy rozważenie [Microsoft Intune zarządzania aplikacjami mobilnymi (mam)](/mem/intune/apps/app-management) z zarządzaniem urządzeniami z systemem iOS lub Android lub bez niego.

 Administratorzy mogą również [wdrażać platformy infrastruktury pulpitu wirtualnego (VDI)](howto-device-identity-virtual-desktop-infrastructure.md) , które obsługują systemy operacyjne Windows w swoich organizacjach, aby usprawnić zarządzanie i ograniczyć koszty dzięki konsolidacji i scentralizowaniu zasobów. 

### <a name="troubleshoot-device-identities"></a>Rozwiązywanie problemów z tożsamościami urządzeń

* [Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd](troubleshoot-device-dsregcmd.md)

* [Rozwiązywanie problemów Enterprise State Roaming ustawień w Azure Active Directory](enterprise-state-roaming-troubleshooting.md)

Jeśli wystąpią problemy z ukończeniem hybrydowego przyłączania do usługi Azure AD dla przyłączonych do domeny urządzeń z systemem Windows, zobacz:

* [Rozwiązywanie problemów z funkcją hybrydowego przyłączania do usługi Azure AD](troubleshoot-hybrid-join-windows-current.md)

* [Rozwiązywanie problemów z funkcją hybrydowego sprzężenia usługi Azure AD na urządzeniach z systemem Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Następne kroki

* [Planowanie wdrożenia usługi Azure AD Join](azureadjoin-plan.md)
* [Planowanie implementacji hybrydowego dołączania do usługi Azure AD](hybrid-azuread-join-plan.md)
* [Zarządzanie tożsamościami urządzeń](device-management-azure-portal.md)