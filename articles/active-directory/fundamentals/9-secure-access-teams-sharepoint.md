---
title: Zabezpiecz zewnętrzny dostęp do usług Microsoft Teams, SharePoint i OneDrive przy użyciu Azure Active Directory
description: Bezpieczny dostęp do usług Microsoft 365ch w ramach ogólnych zabezpieczeń dostępu zewnętrznego.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64a5bd409a29643c1a51630d6f029da7c90d29ad
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744111"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Bezpieczny dostęp zewnętrzny do usług Microsoft Teams, SharePoint i OneDrive dla firm 

Microsoft Teams, SharePoint i OneDrive dla firm to trzy z najczęściej używanych metod współpracy z użytkownikami zewnętrznymi i udostępniania zawartości. Jeśli metody "zatwierdzone" są zbyt restrykcyjne, użytkownicy przechodzą poza zatwierdzone metody, wysyłając wiadomość e-mail lub konfigurując niezabezpieczone procesy zewnętrzne i aplikacje, takie jak Skrzynka osobista lub usługa OneDrive. Celem jest zrównoważenie potrzeb związanych z bezpieczeństwem dzięki łatwej współpracy.

W tym artykule opisano sposób określania i konfigurowania współpracy zewnętrznej w celu spełnienia celów firmy przy użyciu zespołów Microsoft Teams i SharePoint.

## <a name="governance-begins-in-azure-active-directory"></a>Zarządzanie rozpoczyna się w Azure Active Directory

Udostępnianie w Microsoft 365 jest częścią zarządzaną [tożsamościami zewnętrznymi | Ustawienia współpracy zewnętrznej](https://aad.portal.azure.com/) w Azure Active Directory (Azure AD). Jeśli udostępnianie zewnętrzne jest wyłączone lub ograniczone w usłudze Azure AD, zastępuje wszystkie ustawienia udostępniania skonfigurowane w Microsoft 365. Wyjątkiem jest to, że jeśli integracja z usługą Azure AD B2B nie jest włączona, program SharePoint i usługa OneDrive można skonfigurować do obsługi udostępniania ad hoc za pośrednictwem jednorazowych kodów dostępu (OTP).

![Zrzut ekranu ustawień współpracy zewnętrznej](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Dostęp użytkowników-gości

Dostępne są trzy opcje dostępu gościa, które kontrolują, które użytkownicy-Goście mogą zobaczyć po zaproszeniu. 

Aby uniemożliwić użytkownikom-Gościom Wyświetlanie szczegółowych informacji o innych użytkowników-Gości i możliwość wyliczania członkostwa w grupie, wybierz opcję Użytkownicy-Goście mają ograniczony dostęp do właściwości i członkostw obiektów katalogu.

### <a name="guest-invite-settings"></a>Ustawienia zapraszania gościa

Te ustawienia określają, kto może zapraszać Gości i jak można zapraszać tych Gości. Te ustawienia są włączone tylko wtedy, gdy integracja z B2B jest włączona.

Zalecamy włączenie dla administratorów i użytkowników roli zapraszania gościa. To ustawienie umożliwia skonfigurowanie kontrolowanych procesów współpracy, jak w poniższym przykładzie.

* Właściciel zespołu przesyła bilet do przypisany do roli zapraszania gościa.

   * Jest odpowiedzialny za wszystkie zaproszenia gościa.

   * Wyraża zgodę na niebezpośrednie dodanie użytkowników do podstawowego programu SharePoint

   * Jest odpowiedzialny za regularne przeglądy dostępu i odwoływanie dostępu zgodnie z potrzebami.

* Centralna usługa wykonuje następujące czynności

   * Włącza udostępnianie zewnętrzne, przyznając żądaną rolę po zakończeniu szkolenia.

   * Przypisuje licencję usługi Azure AD P2 do właściciela grupy Microsoft 365, aby umożliwić przeglądy dostępu.
   * Tworzy przegląd dostępu do grupy Microsoft 365.

   * Potwierdza, że występują przeglądy dostępu.

   * Usuwa użytkowników bezpośrednio dodanych do podstawowego programu SharePoint.

 Ustaw opcję **Włącz jednorazowe kody dostępu wiadomości e-mail dla Gości (wersja zapoznawcza) i Włącz funkcję samoobsługowego podpisywania gościa za pośrednictwem przepływów użytkownika** na **wartość tak**. To ustawienie wykorzystuje integrację z ustawieniami współpracy zewnętrznej usługi Azure AD.

### <a name="collaboration-restrictions"></a>Ograniczenia współpracy

Istnieją trzy opcje związane z ograniczeniami współpracy. Twoje wymagania biznesowe określają, które z nich będziesz wybierać.

* **Zezwalaj na wysyłanie zaproszeń do dowolnej domeny** oznacza, że każdy użytkownik może zostać zaproszony do współpracy.

* **Odmów zaproszenia do określonych domen** oznacza, że każdy użytkownik spoza nich może zostać zaproszony do współpracy.

* **Zezwalaj na zaproszenia tylko do określonych domen** oznacza, że żaden użytkownik poza tymi określonymi domenami nie może zostać zaproszony. 

## <a name="govern-access-in-teams"></a>Zarządzanie dostępem w zespołach

[Zespoły różnią się między użytkownikami zewnętrznymi (poza organizacją) i użytkownikami-gość (z kontami gościa)](https://docs.microsoft.com/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH). Zarządzanie ustawieniami współpracy w [portalu administratora zespołów](https://admin.teams.microsoft.com/company-wide-settings/external-communications) w obszarze Ustawienia organizacyjne. 

> [!NOTE]
> Ustawienia współpracy z tożsamościami zewnętrznymi w Azure Active Directory kontrolują czynne uprawnienia. Można zwiększyć ograniczenia w zespołach, ale nie zmniejszać ich od tego, co jest ustawione w usłudze Azure AD.

* **Ustawienia dostępu zewnętrznego**. Domyślnie zespoły umożliwiają dostęp zewnętrzny, co oznacza, że organizacja może komunikować się ze wszystkimi domenami zewnętrznymi. Jeśli chcesz ograniczyć lub zezwolić na określone domeny tylko dla zespołów, możesz to zrobić tutaj.

* **Dostęp gościa**. Funkcja dostępu gościa kontroluje, co użytkownicy-Goście mogą robić w zespołach.

Aby dowiedzieć się więcej o zarządzaniu dostępem zewnętrznym w zespołach, zobacz następujące zasoby.

* [Zarządzanie dostępem zewnętrznym w usłudze Microsoft Teams](https://docs.microsoft.com/microsoftteams/manage-external-access)

* [Microsoft 365 modele tożsamości i Azure Active Directory](https://docs.microsoft.com/microsoft-365/enterprise/about-microsoft-365-identity?view=o365-worldwide)

* [Modele tożsamości i uwierzytelnianie dla zespołów Microsoft Teams](https://docs.microsoft.com/MicrosoftTeams/identify-models-authentication)

* [Etykiety czułości dla zespołów Microsoft Teams](https://docs.microsoft.com/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Zarządzanie dostępem w programie SharePoint i usłudze OneDrive

Administratorzy programu SharePoint mają wiele ustawień do współpracy. Ustawienia całej organizacji są zarządzane za pomocą Centrum administracyjnego programu SharePoint. Ustawienia można dostosować dla każdej witryny programu SharePoint. Zalecamy, aby ustawienia całej organizacji były na minimalnym wymaganym poziomie zabezpieczeń, a w razie potrzeby zwiększają zabezpieczenia w określonych lokacjach. Na przykład w przypadku projektu o wysokim ryzyku możesz chcieć ograniczyć użytkowników do określonych domen i wyłączyć możliwość zapraszania Gości.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Integrowanie programu SharePoint i jednej stacji z usługą Azure AD B2B

W ramach ogólnej strategii zarządzania zewnętrznie zalecamy [włączenie obsługi wersji zapoznawczej programu SharePoint i usługi OneDrive przy użyciu funkcji B2B usługi Azure AD](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview) .

Usługa Azure AD B2B umożliwia uwierzytelnianie użytkowników-Gości i zarządzanie nimi. Dzięki integracji z usługami SharePoint i OneDrive usługa [Azure AD B2B umożliwia jednorazowe Używanie kodów dostępu](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode) do zewnętrznego udostępniania plików, folderów, elementów listy, bibliotek dokumentów i witryn. Ta funkcja zapewnia uaktualnione środowisko z istniejącego [środowiska odbiorcy udostępniania zewnętrznego](https://docs.microsoft.com/sharepoint/what-s-new-in-sharing-in-targeted-release).

> [!NOTE]
> Po włączeniu integracji z usługą Azure AD B2B w wersji zapoznawczej, udostępnianie programów SharePoint i OneDrive podlega ustawieniom organizacji usługi Azure AD, takich jak **Członkowie mogą zapraszać** i **Goście mogą zaprosić**.

### <a name="sharing-policies"></a>Zasady udostępniania

*Udostępnianie zewnętrzne* można ustawić dla programu SharePoint i usługi OneDrive. Ograniczenia usługi OneDrive nie mogą być bardziej ograniczające niż ustawienia programu SharePoint.

 ![Zrzut ekranu ustawień udostępniania zewnętrznego w programie SharePoint i usłudze OneDrive](media/secure-external-access/9-sharepoint-settings.png)

Integracja programu SharePoint z usługą Azure AD B2B zmiany sposobu działania formantów z kontami.

* **Każdy**. Niezalecane

   * Bez względu na stan integracji włączenie wszystkich linków oznacza, że żadne zasady platformy Azure nie będą stosowane w przypadku użycia tego typu łącza. 

   * W scenariuszu współpracy zarządzanej nie należy włączać tej funkcji. 
   > [!NOTE]
   > Możesz znaleźć scenariusz, w którym należy włączyć to ustawienie dla określonej lokacji, w tym przypadku należy włączyć ją w tym miejscu i ustawić większe ograniczenia w poszczególnych lokacjach.

* **Nowi i istniejący Goście**. Zalecane, jeśli włączono integrację.

   * **Dzięki integracji z usługą Azure AD B2B** nowi i istniejący Goście będą mieć konto gościa usługi Azure AD B2B, które może być zarządzane za pomocą zasad usługi Azure AD.

   * **Bez włączonej integracji z usługą Azure AD B2B** nowi Goście nie będą mieć utworzonego konta B2B usługi Azure AD i nie mogą być zarządzane przez usługę Azure AD. Czy istniejący Goście mają konto B2B usługi Azure AD, zależy od tego, jak gość został utworzony.

* **Istniejący Goście**. Zalecane, jeśli integracja nie jest włączona.

   * Dzięki temu włączeniu użytkownicy mogą udostępniać tylko innym użytkownikom znajdującym się już w katalogu.

* **Tylko osoby w Twojej organizacji**. Niezalecane w przypadku konieczności współpracy z użytkownikami zewnętrznymi.

   * Bez względu na stan integracji użytkownicy będą mogli udostępniać dostęp tylko użytkownikom w organizacji. 

* **Ogranicz udostępnianie zewnętrzne według domeny**. Domyślnie program SharePoint zezwala na dostęp zewnętrzny, co oznacza, że udostępnianie jest dozwolone ze wszystkimi domenami zewnętrznymi. Jeśli chcesz ograniczyć lub zezwolić na określone domeny tylko dla programu SharePoint, możesz to zrobić tutaj.

* **Zezwól na zewnętrzne udostępnianie tylko użytkownikom z określonych grup zabezpieczeń**.  To ustawienie ogranicza osoby, które mogą udostępniać zawartość w SharePoint i OneDrive, podczas gdy ustawienie w usłudze Azure AD ma zastosowanie do wszystkich aplikacji. Ograniczanie osoby, która może udostępniać, może być przydatne, jeśli chcesz, aby użytkownicy musieli przeprowadzić szkolenia dotyczące bezpiecznego udostępniania, a następnie po zakończeniu Dodaj je do zatwierdzonej grupy zabezpieczeń udostępniania. Jeśli to ustawienie jest zaznaczone, a użytkownicy nie mają możliwości uzyskania dostępu do "zatwierdzonego współużytkującego", mogą zamiast tego znaleźć niezatwierdzone sposoby udostępniania. 

* **Zezwól Gościom na udostępnianie nieposiadanych elementów**. Zalecamy pozostawienie tego wyłączenia.

* **Osoby korzystające z kodu weryfikacyjnego muszą ponownie przeprowadzić uwierzytelnienie po upływie następującej liczby dni (wartość domyślna to 30)**. Zalecamy włączenie tego ustawienia.

### <a name="access-controls"></a>Kontrole dostępu

Ustawienie kontroli dostępu będzie miało wpływ na wszystkich użytkowników w organizacji. Z uwagi na to, że możesz nie być w stanie kontrolować, czy użytkownicy zewnętrzni mają zgodne urządzenia, nie będą tutaj rozwiązywać tych kontroli. 

* **Wylogowywanie bezczynnej sesji**. Zalecamy włączenie tej kontrolki, która pozwala na ostrzeganie i wylogowywanie użytkowników z niezarządzanych urządzeń po okresie braku aktywności. Możesz skonfigurować okres nieaktywności i ostrzeżenie. 

* **Lokalizacja sieciowa**. Ustawienie tej kontrolki oznacza, że można zezwolić na dostęp tylko do adresów IP należących do organizacji. W przypadku zewnętrznych scenariuszy współpracy Ustaw tę opcję tylko wtedy, gdy wszyscy partnerzy zewnętrzni będą uzyskiwać dostęp do zasobów tylko w sieci lub za pośrednictwem sieci VPN.

### <a name="file-and-folder-links"></a>Linki plików i folderów

W centrum administracyjnym programu SharePoint można również ustawić, jak łącza do plików i folderów są udostępniane. Te ustawienia można również skonfigurować dla każdej lokacji. 

 ![Zrzut ekranu ustawień łącza plików i folderów](media/secure-external-access/9-file-folder-links.png)

Jeśli włączono integrację z usługą Azure AD B2B, udostępnianie plików i folderów poza organizacją spowoduje utworzenie użytkownika B2B podczas udostępniania plików i folderów.

Zalecamy ustawienie domyślnego typu łącza **tylko do osób w organizacji** oraz domyślnych uprawnień do **edycji**. To gwarantuje, że elementy są udostępnione Thoughtfully. Następnie można dostosować to ustawienie dla poszczególnych lokacji domyślnych, które spełniają określone wymagania dotyczące współpracy.

### <a name="anyone-links"></a>Wszystkie linki

Nie zaleca się włączania linków dla wszystkich osób. Jeśli to zrobisz, zalecamy ustawienie czasu wygaśnięcia i rozważ ograniczenie ich do wyświetlania uprawnień. Jeśli wybierzesz opcję Wyświetl tylko uprawnienia dla plików lub folderów, użytkownicy nie będą mogli zmienić linków do dołączenia do edycji.

Aby dowiedzieć się więcej na temat zarządzania dostępem zewnętrznym do programu SharePoint, zobacz następujące tematy:

* [Omówienie udostępniania zewnętrznego programu SharePoint](https://docs.microsoft.com/sharepoint/external-sharing-overview)

* [Integracja programów SharePoint i OneDrive z usługą Azure AD B2B](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określanie stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md) (Jesteś tutaj).