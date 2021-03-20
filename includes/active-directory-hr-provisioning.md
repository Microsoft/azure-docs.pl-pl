---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93135354"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Aplikacja usługi Cloud kadr do Azure Active Directory aprowizacji użytkowników

W przeszłości pracownicy IT korzystali z ręcznych metod tworzenia, aktualizowania i usuwania pracowników. Używają metod, takich jak przekazywanie plików CSV lub skryptów niestandardowych do synchronizowania danych pracownika. Te procesy aprowizacji są podatne na błędy, niezabezpieczone i trudne do zarządzania.

Aby zarządzać cyklem życia tożsamości pracowników, dostawców lub procesów roboczych, [Azure Active Directory (Azure AD) usługa aprowizacji użytkowników](../articles/active-directory/app-provisioning/user-provisioning.md) oferuje integrację z aplikacjami ludzkimi opartymi na chmurze (HR). Przykładami aplikacji są: Workday lub SuccessFactors.

Usługa Azure AD używa tej integracji w celu włączenia następujących przepływów pracy aplikacji w chmurze (aplikacji):

- **Udostępnianie użytkownikom Active Directory:** Zainicjuj obsługę wybranych zestawów użytkowników z poziomu aplikacji w chmurze w jednej lub kilku domenach Active Directory.
- **Udostępnianie użytkownikom tylko w chmurze usługi Azure AD:** W scenariuszach, w których Active Directory nie są używane, należy udostępnić użytkownikom bezpośrednio z aplikacji KADRowej w chmurze do usługi Azure AD.
- **Zapisuj z powrotem do aplikacji kadrowej w chmurze:** Napisz adresy e-mail i atrybuty nazwy użytkownika z usługi Azure AD z powrotem do aplikacji w chmurze.


## <a name="enabled-hr-scenarios"></a>Włączone scenariusze kadr

Usługa aprowizacji użytkowników w usłudze Azure AD umożliwia automatyzację następujących scenariuszy zarządzania cyklem życia tożsamości opartych na usłudze HR:

- **Nowe zatrudnienie pracownika:** Po dodaniu nowego pracownika do aplikacji w chmurze, konto użytkownika zostanie automatycznie utworzone w Active Directory i w usłudze Azure AD z opcją zapisania z powrotem adresu e-mail i atrybutów username do aplikacji w chmurze.
- **Aktualizacje atrybutu pracownika i profilu:** Gdy rekord pracownika, taki jak nazwa, tytuł lub Menedżer, zostanie zaktualizowany w aplikacji w chmurze, konto użytkownika zostanie automatycznie zaktualizowane w Active Directory i Azure AD.
- **Zakończenia pracowników:** Gdy pracownik zostanie zakończony w aplikacji KADRowej w chmurze, jego konto użytkownika zostanie automatycznie wyłączone w Active Directory i w usłudze Azure AD.
- **Pracownicy przezatrudnieni:** Gdy pracownik jest ponownie zatrudniany w aplikacji KADRowej w chmurze, jego stare konto może zostać automatycznie ponownie uaktywnione lub zainicjowane w celu Active Directory i usługi Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Dla kogo jest to integracja najlepiej dopasowanej?

Integracja aplikacji w chmurze z obsługą administracyjną użytkowników w usłudze Azure AD doskonale nadaje się dla organizacji, które:

- Potrzebujesz wstępnie skompilowanego rozwiązania opartego na chmurze do aprowizacji użytkowników w chmurze.
- Wymagaj bezpośredniej aprowizacji użytkowników z aplikacji w chmurze w celu Active Directory lub Azure AD.
- Wymaganie, aby użytkownicy mieli możliwość aprowizacji przy użyciu danych uzyskanych z aplikacji KADRowej w chmurze.
- Wymagaj synchronizacji, przeniesienia i opuszczenia użytkowników do co najmniej jednego Active Directory lasów, domen i jednostek organizacyjnych na podstawie informacji o zmianach wykrytych w aplikacji w chmurze.
- Użyj pakietu Office 365 do obsługi poczty e-mail.


### <a name="key-benefits"></a>Najważniejsze korzyści

Ta funkcja aprowizacji IT oparta na usłudze kadr oferuje następujące korzyści biznesowe:

- **Zwiększ produktywność:** Teraz można zautomatyzować przypisanie kont użytkowników i licencji pakietu Office 365 i zapewnić dostęp do grup kluczy. Automatyzacja przypisań pozwala nowym zatrudniać natychmiastowy dostęp do swoich narzędzi do zadań i zwiększa produktywność.
- **Zarządzanie ryzykiem:** Aby zwiększyć bezpieczeństwo, można zautomatyzować zmiany w zależności od stanu pracownika lub członkostwa w grupach przy użyciu danych przepływających z aplikacji w chmurze. Automatyzacja zmian gwarantuje, że tożsamości użytkowników i dostęp do najważniejszych aplikacji są aktualizowane automatycznie, gdy użytkownicy przechodzą lub opuściją organizację.
- **Zgodność i zarządzanie adresami:** Usługa Azure AD obsługuje natywne dzienniki inspekcji dla żądań aprowizacji użytkowników wykonywanych przez aplikacje obu systemów źródłowych i docelowych. Za pomocą inspekcji można śledzić, kto ma dostęp do aplikacji z jednego ekranu.
- **Zarządzaj kosztami:** Automatyczne Inicjowanie obsługi zmniejsza koszty, unikając nieefektywności i błędu ludzkiego związanego z obsługą ręczną. Pozwala to ograniczyć potrzebę tworzenia niestandardowo rozbudowanych rozwiązań użytkowników w czasie z użyciem starszych i nieaktualnych platform.
