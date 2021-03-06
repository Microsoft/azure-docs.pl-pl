---
title: Jak zarządzać administratorami lokalnymi na urządzeniach przyłączone do usługi Azure AD
description: Dowiedz się, jak przypisać role platformy Azure do lokalnej grupy administratorów urządzenia z systemem Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 806ff92fcf75ff8d1c8e092d7ff4435751a9e7db
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529898"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Jak zarządzać lokalną grupą administratorów na urządzeniach przyłączone do usługi Azure AD

Aby zarządzać urządzeniem z systemem Windows, musisz być członkiem lokalnej grupy administratorów. W ramach procesu dołączania Azure Active Directory (Azure AD) usługa Azure AD aktualizuje członkostwo tej grupy na urządzeniu. Aktualizację członkostwa można dostosować w celu spełnienia wymagań biznesowych. Aktualizacja członkostwa jest na przykład przydatna, jeśli chcesz umożliwić pracownikom działu pomocy technicznej wykonywanie zadań wymagających uprawnień administratora na urządzeniu.

W tym artykule wyjaśniono, jak działa aktualizacja członkostwa administratorów lokalnych i jak można ją dostosować podczas dołączania do usługi Azure AD. Zawartość tego artykułu nie ma zastosowania do urządzeń przyłączony do hybrydowej **usługi Azure AD.**

## <a name="how-it-works"></a>Jak to działa

Po połączeniu urządzenia z systemem Windows z usługą Azure AD przy użyciu dołączania do usługi Azure AD usługa Azure AD dodaje następujące podmioty zabezpieczeń do lokalnej grupy administratorów na urządzeniu:

- Rola administratora globalnego usługi Azure AD
- Rola administratora urządzeń usługi Azure AD 
- Użytkownik wykonujący dołączanie do usługi Azure AD   

Dodając role usługi Azure AD do lokalnej grupy administratorów, możesz aktualizować użytkowników, którzy mogą zarządzać urządzeniem w dowolnym momencie w usłudze Azure AD bez konieczności modyfikowania czegokolwiek na urządzeniu. Usługa Azure AD dodaje również rolę administratora urządzeń usługi Azure AD do lokalnej grupy administratorów w celu obsługi zasady najmniejszych uprawnień (PoLP). Oprócz administratorów globalnych można również włączyć zarządzanie  urządzeniem dla użytkowników, którzy mają przypisaną rolę administratora urządzeń. 

## <a name="manage-the-global-administrators-role"></a>Zarządzanie rolą administratorów globalnych

Aby wyświetlić i zaktualizować członkostwo roli administratora globalnego, zobacz:

- [Wyświetl wszystko członków roli administratora w Azure Active Directory](../roles/manage-roles-portal.md)
- [Przypisywanie użytkownika do ról administratorów w usłudze Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Zarządzanie rolą administratora urządzeń 

W Azure Portal można zarządzać rolą administratora urządzenia na **stronie** Urządzenia. Aby otworzyć **stronę Urządzenia:**

1. Zaloguj się do [swojego Azure Portal](https://portal.azure.com) jako administrator globalny.
1. Wyszukaj i wybierz pozycję *Azure Active Directory*.
1. W sekcji **Zarządzanie** kliknij pozycję **Urządzenia.**
1. Na stronie **Urządzenia** kliknij pozycję **Ustawienia urządzenia.**

Aby zmodyfikować rolę administratora urządzeń, skonfiguruj ustawienia Dodatkowi **administratorzy lokalni na urządzeniach przyłączonych do usługi Azure AD.**  

![Dodatkowi administratorzy lokalni](./media/assign-local-admin/10.png)

>[!NOTE]
> Ta opcja wymaga dzierżawy Azure AD — wersja Premium dzierżawy. 

administratorzy urządzeń są przypisane do wszystkich urządzeń przyłączone do usługi Azure AD. Zakres administratorów urządzeń nie może być ograniczony do określonego zestawu urządzeń. Zaktualizowanie roli administratora urządzenia nie musi mieć natychmiastowego wpływu na użytkowników, których dotyczy problem. Na urządzeniach, na których użytkownik jest już  zalogowany, podniesienie uprawnień ma miejsce, gdy zajdą obie poniższe akcje:

- Do wydania nowego podstawowego tokenu odświeżania z odpowiednimi uprawnieniami usłudze Azure AD upłynęło do 4 godzin. 
- Użytkownik wyeczętowuje się i ponownie się nie blokuje/odblokowywa, aby odświeżyć swój profil.

>[!NOTE]
> Powyższe akcje nie mają zastosowania do użytkowników, którzy wcześniej nie zalogowali się na odpowiednim urządzeniu. W takim przypadku uprawnienia administratora są stosowane natychmiast po pierwszym zalogowaniu się na urządzeniu. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Zarządzanie uprawnieniami administratora przy użyciu grup usługi Azure AD (wersja zapoznawcza)

Od wersji Windows 10 2004 można używać grup usługi Azure AD do zarządzania uprawnieniami administratora na urządzeniach przyłączone do usługi Azure AD przy użyciu zasad [zarządzania](/windows/client-management/mdm/policy-csp-restrictedgroups) urządzeniami przenośnymi grup z ograniczeniami. Te zasady umożliwiają przypisywanie poszczególnych użytkowników lub grup usługi Azure AD do lokalnej grupy administratorów na urządzeniu przyłączonym do usługi Azure AD, co zapewnia poziom szczegółowości konfigurowania odrębnych administratorów dla różnych grup urządzeń. 

>[!NOTE]
> Od Windows 10 20H2 zalecamy używanie zasad [](/windows/client-management/mdm/policy-csp-localusersandgroups) Użytkownicy i grupy lokalne zamiast zasad grupy z ograniczeniami


Obecnie w usłudze Intune nie ma interfejsu użytkownika do zarządzania tymi zasadami i należy je skonfigurować przy użyciu niestandardowych [ustawień OMA-URI.](/mem/intune/configuration/custom-settings-windows-10) Kilka kwestii do rozważenia przy użyciu jednej z tych zasad: 

- Dodawanie grup usługi Azure AD za pomocą zasad wymaga identyfikatora SID grupy, który można uzyskać, wykonując interfejs API Microsoft Graph [dla grup.](/graph/api/resources/group) Identyfikator SID jest definiowany przez właściwość `securityIdentifier` w odpowiedzi interfejsu API.
- Gdy zasady grupy z ograniczeniami są wymuszane, każdy bieżący członek grupy, który nie znajduje się na liście członków, jest usuwany. Dlatego wymuszanie tych zasad przy użyciu nowych członków lub grup spowoduje usunięcie z urządzenia istniejących administratorów, a mianowicie użytkownika, który dołączył do urządzenia, administrator globalny rolę administratora urządzenia. Aby uniknąć usuwania istniejących członków, należy je skonfigurować jako część listy Członkowie w zasadach Grupy z ograniczeniami. To ograniczenie jest rozwiązane, jeśli używasz zasad Użytkownicy i grupy lokalne, które umożliwiają aktualizacje przyrostowe członkostwa w grupie
- Uprawnienia administratora korzystające z obu zasad są oceniane tylko dla następujących dobrze znanych grup na urządzeniu z systemem Windows 10 — Administratorzy, Użytkownicy, Goście, Użytkownicy Pulpit zdalny użytkownicy i użytkownicy zarządzania zdalnego. 
- Zarządzanie administratorami lokalnymi przy użyciu grup usługi Azure AD nie ma zastosowania do urządzeń przyłączone hybrydową do usługi Azure AD ani urządzeń zarejestrowanych w usłudze Azure AD.
- Chociaż zasady grupy z ograniczeniami istniały przed wersją Windows 10 2004, nie obsługiły grup usługi Azure AD jako członków lokalnej grupy administratorów urządzenia. 

## <a name="manage-regular-users"></a>Zarządzanie zwykłymi użytkownikami

Domyślnie usługa Azure AD dodaje użytkownika wykonującego dołączanie do usługi Azure AD do grupy administratorów na urządzeniu. Jeśli chcesz uniemożliwić zwykłym użytkownikom stanie się administratorami lokalnymi, masz następujące opcje:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) — Windows Autopilot udostępnia opcję uniemożliwić użytkownikowi podstawowemu wykonującemu dołączanie zostać administratorem lokalnym. W tym celu można [utworzyć profil rozwiązania Autopilot.](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)
- [Rejestracja zbiorcza](/intune/windows-bulk-enroll) — dołączanie do usługi Azure AD wykonywane w kontekście rejestracji zbiorczej odbywa się w kontekście automatycznie utworzonego użytkownika. Użytkownicy logujący się po dołączeniu urządzenia nie są dodawana do grupy administratorów.   

## <a name="manually-elevate-a-user-on-a-device"></a>Ręczne podnieść uprawnienia użytkownika na urządzeniu 

Oprócz korzystania z procesu dołączania do usługi Azure AD można również ręcznie podnieść uprawnienia zwykłego użytkownika, aby zostać administratorem lokalnym na jednym konkretnym urządzeniu. Ten krok wymaga, aby użytkownik był już członkiem lokalnej grupy administratorów. 

Począwszy od **wersji Windows 10 1709,** można wykonać to zadanie z Ustawienia **-> Konta -> innych użytkowników.** Wybierz **pozycję Dodaj użytkownika służbowego,** wprowadź nazwę UPN użytkownika w obszarze **Konto** użytkownika i wybierz *pozycję Administrator* w obszarze Typ **konta**  
 
Ponadto można również dodawać użytkowników przy użyciu wiersza polecenia:

- Jeśli użytkownicy dzierżawy są synchronizowane z lokalna usługa Active Directory, użyj programu `net localgroup administrators /add "Contoso\username"` .
- Jeśli użytkownicy dzierżawy są utworzeni w usłudze Azure AD, użyj `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Zagadnienia do rozważenia 

Grup nie można przypisać do roli administratora urządzeń. Mogą to być tylko poszczegieni użytkownicy.

administratorzy urządzeń są przypisane do wszystkich urządzeń przyłączone do usługi Azure AD. Zakres tych urządzeń nie może być ograniczony do określonego zestawu urządzeń.

Po usunięciu użytkowników z roli administratora urządzeń nadal mają oni uprawnienia administratora lokalnego na urządzeniu, o ile są na nim zalogowani. Uprawnienie zostanie odwołane podczas następnego logowania po wystawieniu nowego podstawowego tokenu odświeżania. Odwołanie to, podobnie jak podniesienie uprawnień, może potrwać do 4 godzin.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)
- Aby dowiedzieć się więcej na temat dostępu warunkowego opartego na urządzeniach, zobacz Azure Active Directory zasad dostępu [warunkowego opartego na urządzeniach.](../conditional-access/require-managed-devices.md)
