---
title: Zarządzanie chmurą Azure AD zarządzaną w przypadku obciążeń lokalnych — Azure
description: W tym temacie opisano zarządzanie zarządzane przez chmurę dla obciążeń lokalnych.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b4d1041b9d330227fadf31f6afc1804174ea2ad
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340853"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Jak usługa Azure AD zapewnia zarządzanie zarządzane przez chmurę dla obciążeń lokalnych

Azure Active Directory (Azure AD) to kompleksowe rozwiązanie jako usługa (IDaaS) używane przez miliony organizacji, które obejmują wszystkie aspekty tożsamości, zarządzania dostępem i bezpieczeństwa. Usługa Azure AD zawiera więcej niż miliard tożsamości użytkowników i ułatwia użytkownikom logowanie się i bezpieczny dostęp do obu:

* Zasoby zewnętrzne, takie jak Microsoft 365, Azure Portal i tysiące innych aplikacji typu "oprogramowanie jako usługa" (SaaS).
* Zasoby wewnętrzne, takie jak aplikacje w sieci firmowej i intranecie firmy, wraz z wszystkimi aplikacjami w chmurze opracowanymi przez tę organizację.

Organizacje mogą używać usługi Azure AD, jeśli są "czystą chmurą" lub jako wdrożenie "hybrydowe", jeśli są one obciążeniami lokalnymi. Wdrożenie hybrydowe usługi Azure AD może być częścią strategii organizacji służącej do migrowania zasobów IT do chmury lub do dalszej integracji istniejącej infrastruktury lokalnej wraz z nowymi usługami w chmurze.

W przeszłości organizacje hybrydowe widziały usługę Azure AD jako rozszerzenie istniejącej infrastruktury lokalnej. W tych wdrożeniach lokalne Administrowanie zarządzaniem tożsamościami, system Windows Server Active Directory lub inne systemy katalogów w domu, są punktami kontrolnymi, a użytkownicy i grupy są synchronizowane z tych systemów do katalogu w chmurze, takiego jak Azure AD. Gdy te tożsamości znajdują się w chmurze, można je udostępnić do Microsoft 365, platformy Azure i innych aplikacji.

![Cykl życia tożsamości](media/cloud-governed-management-for-on-premises//image1.png)

Ponieważ organizacje przechodzą większą część infrastruktury IT wraz z ich aplikacjami do chmury, wiele poszukuje ulepszonych zabezpieczeń i uproszczonych funkcji zarządzania tożsamościami jako usługi. Funkcje IDaaS w chmurze w usłudze Azure AD przyspieszają przejście do zarządzania przez chmurę dzięki udostępnieniu rozwiązań i możliwości, które umożliwiają organizacjom szybkie wdrażanie i przenoszenie większej ilości zarządzania tożsamościami z tradycyjnych systemów lokalnych do usługi Azure AD, przy jednoczesnym zachowaniu obsługi istniejących oraz nowych aplikacji.

Ten dokument przedstawia strategię firmy Microsoft dotyczącą IDaaS hybrydowych i opisuje sposób, w jaki organizacje mogą używać usługi Azure AD do istniejących aplikacji.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Podejście usługi Azure AD do zarządzania tożsamościami zarządzanymi przez chmurę

W miarę jak organizacje przechodzą na chmurę, muszą mieć pewność, że mają kontrolę nad ich pełnym środowiskiem — większym wpływem na działania, które są obsługiwane przez automatyzację i proaktywne szczegółowe informacje. "**Zarządzanie zarządzane przez chmurę**" opisuje, w jaki sposób organizacje zarządzają użytkownikami, aplikacjami, grupami i urządzeniami w chmurze oraz zarządza nimi.

W tym nowoczesnym świecie organizacje muszą mieć możliwość efektywnego zarządzania na dużą skalę z powodu rozprzestrzeniania się aplikacji SaaS oraz rosnącej roli współpracy i tożsamości zewnętrznych. Nowe ryzyko dotyczące krajobrazu w chmurze oznacza, że organizacja musi być wydajniejsza — złośliwy aktor, który zagraża użytkownikowi chmury, może mieć wpływ na aplikacje w chmurze i lokalne.

W szczególności organizacje hybrydowe muszą mieć możliwość delegowania i automatyzowania zadań, które w przeszłości zakończyły się ręcznie. Aby zautomatyzować zadania, potrzebne są interfejsy API i procesy, które organizują cykl życia różnych zasobów związanych z tożsamościami (użytkowników, grup, aplikacji, urządzeń), dzięki czemu mogą delegować bieżące zarządzanie tymi zasobami do większej liczby osób poza rdzeniem działu IT. Usługa Azure AD rozwiązuje te wymagania za pomocą zarządzania kontami użytkowników i natywnego uwierzytelniania użytkowników, którzy nie wymagają lokalnej infrastruktury tożsamości. Nietworzenie infrastruktury lokalnej może przynieść korzyści dla organizacji, które mają nowe społeczności użytkowników, takie jak partnerzy biznesowi, które nie zostały utworzone w katalogu lokalnym, ale których zarządzanie dostępem ma kluczowe znaczenie dla osiągnięcia wyników firmy.

Ponadto zarządzanie nie zostało ukończone bez nadzoru---i zarządzania w tym nowym świecie jest zintegrowaną częścią systemu tożsamości, a nie z dodatkiem. Zarządzanie tożsamościami zapewnia organizacjom możliwość zarządzania cyklem życia tożsamości i dostępu między pracownikami, partnerami biznesowymi i dostawcami oraz usługami i aplikacjami.

Wraz z zarządzaniem tożsamościami ułatwia ona przechodzenie przez organizację do zarządzania objętego chmurą, a także pozwala na skalowanie nowych wyzwań z Gośćmi oraz zapewnia dokładniejsze informacje i automatyzację niż w przypadku klientów korzystających z infrastruktury lokalnej. Zarządzanie w tym nowym świecie oznacza możliwość, aby organizacja miała przejrzystość, widoczność i odpowiednie środki kontroli dostępu do zasobów w organizacji. W usłudze Azure AD zespoły ds. zabezpieczeń i inspekcji mają wgląd w to, kto ma---i kto powinien mieć dostęp do zasobów w organizacji (na jakich urządzeniach), do czego służą użytkownicy, oraz czy organizacja ma i używa odpowiednich kontroli do usuwania lub ograniczania dostępu zgodnie z zasadami firmy lub przepisami.

Nowy model zarządzania przynosi korzyści organizacjom z SaaS i aplikacjami biznesowymi (LOB), ponieważ ułatwiają one zarządzanie dostępem do tych aplikacji i ich Zabezpieczanie. Dzięki integracji aplikacji z usługą Azure AD organizacje będą mogły spójnie korzystać z i zarządzać dostępem zarówno w chmurze, jak i lokalnie. Zarządzanie cyklem życia aplikacji jest bardziej zautomatyzowane, a usługa Azure AD zapewnia rozbudowane informacje o użyciu aplikacji, które nie zostały łatwo osiągalne w lokalnym zarządzaniu tożsamościami. Za pomocą funkcji samoobsługowych grup Microsoft 365 i zespołów, organizacje mogą łatwo tworzyć grupy do zarządzania dostępem i współpracy oraz dodawać i usuwać użytkowników w chmurze, aby umożliwić zarządzanie wymaganiami dotyczącymi współpracy i dostępu.

Wybór odpowiednich funkcji usługi Azure AD do zarządzania objętego chmurą zależy od aplikacji, które mają być używane, oraz sposobu integracji tych aplikacji z usługą Azure AD. W poniższych sekcjach opisano podejścia, które należy wykonać w przypadku aplikacji zintegrowanych z usługą AD, oraz aplikacji korzystających z protokołów federacyjnych (na przykład SAML, OAuth lub OpenID Connect Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Zarządzane przez chmurę Zarządzanie aplikacjami zintegrowanymi z usługą AD

Usługa Azure AD usprawnia zarządzanie aplikacjami zintegrowanymi Active Directory organizacji przez bezpieczny dostęp zdalny i dostęp warunkowy do tych aplikacji. Ponadto usługa Azure AD zapewnia również zarządzanie cyklem życia konta i zarządzanie poświadczeniami dla istniejących kont usługi AD użytkownika, w tym:

* **Bezpieczny dostęp zdalny i dostęp warunkowy do aplikacji lokalnych**

W przypadku wielu organizacji pierwszy krok zarządzania dostępem z chmury do lokalnych aplikacji sieci Web i pulpitu zdalnego zintegrowany z usługą AD to wdrożenie [serwera proxy aplikacji](../manage-apps/application-proxy.md) przed tymi aplikacjami w celu zapewnienia bezpiecznego dostępu zdalnego.

Po jednokrotnym zalogowaniu się do usługi Azure AD użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze i lokalnych przy użyciu zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji zapewnia dostęp zdalny i logowanie jednokrotne do Pulpit zdalny, SharePoint, a także aplikacji, takich jak Tableau i Qlik, oraz aplikacji biznesowych. Ponadto zasady dostępu warunkowego mogą zawierać [warunki użytkowania](../conditional-access/terms-of-use.md) i [upewnić się, że użytkownik wyraził zgodę na](../conditional-access/require-tou.md) dostęp do aplikacji.

![Architektura serwera proxy aplikacji](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatyczne zarządzanie cyklem życia dla kont Active Directory**

Zarządzanie tożsamościami pomaga organizacjom w osiągnięciu równowagi między *produktywnością* ---, jak szybko można uzyskać dostęp do zasobów, których potrzebują, na przykład w przypadku dołączenia do organizacji? ---i *zabezpieczenia* ---, w jaki sposób zmiana dostępu ma się zmieniać wraz z upływem czasu, na przykład zmiany stanu zatrudnienia osoby? Zarządzanie cyklem życia tożsamości jest podstawą do zarządzania tożsamościami, a skuteczne zarządzanie na dużą skalę wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji.

W przypadku wielu organizacji cykl życia tożsamości dla pracowników jest powiązany z reprezentacją tego użytkownika w systemie zarządzania stolicą (HCM). W przypadku organizacji korzystających z produktu Workday jako systemu HCM usługa Azure AD może zapewnić, że konta użytkowników w usłudze AD są [automatycznie inicjowane i niezainicjowane dla pracowników w dniach roboczych](../saas-apps/workday-inbound-tutorial.md). Prowadzi to do zwiększenia produktywności użytkowników dzięki automatyzacji kont Birthright i zarządza ryzykiem przez zapewnienie, że dostęp do aplikacji zostanie automatycznie zaktualizowany, gdy użytkownik zmieni role lub opuści organizację. [Plan wdrożenia](https://aka.ms/WorkdayDeploymentPlan) aprowizacji użytkowników opartych na produkcie Workday to przewodnik krok po kroku, który prowadzi organizacje przez implementację najlepszych rozwiązań w dniach roboczych w celu Active Directory rozwiązania do aprowizacji użytkowników w procesie pięciu kroków.

Azure AD — wersja Premium obejmuje również Microsoft Identity Manager, które mogą importować rekordy z innych lokalnych systemów HCM, w tym SAP, Oracle eBusiness i Oracle PeopleSoft.

Współpraca między firmami wymaga bardziej udzielenia dostępu osobom spoza organizacji. Współpraca [B2B w usłudze Azure AD](/azure/active-directory/b2b/) umożliwia organizacjom bezpieczne udostępnianie aplikacji i usług użytkownikom-Gościom i partnerom zewnętrznym przy zachowaniu kontroli nad własnymi danymi firmowymi.

Usługa Azure AD może [automatycznie tworzyć konta w usłudze AD dla użytkowników-Gości](../external-identities/hybrid-cloud-to-on-premises.md) zgodnie z potrzebami, umożliwiając gościom biznesowym dostęp do lokalnych aplikacji zintegrowanych z usługą AD bez konieczności stosowania innego hasła. Organizacje mogą konfigurować [zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkowników-Gości](../external-identities/conditional-access.md), więc testy MFA są wykonywane podczas uwierzytelniania serwera proxy aplikacji. Ponadto wszystkie [przeglądy dostępu](../governance/manage-guest-access-with-access-reviews.md) wykonywane przez użytkowników usługi Cloud B2B dotyczą użytkowników lokalnych. Jeśli na przykład użytkownik w chmurze zostanie usunięty za pomocą zasad zarządzania cyklem życia, użytkownik lokalny również zostanie usunięty.

**Zarządzanie poświadczeniami dla kont Active Directory** Funkcja samoobsługowego resetowania haseł w usłudze Azure AD umożliwia użytkownikom, którzy zapominali, że hasła są ponownie uwierzytelniane i resetowanie haseł, ze zmienionymi hasłami [zapisanymi w Active Directory lokalnym](../authentication/concept-sspr-writeback.md). Proces resetowania hasła może również korzystać z lokalnych zasad haseł Active Directory: gdy użytkownik resetuje hasło, sprawdza się, czy przed zatwierdzeniem go do tego katalogu jest zgodna z lokalnymi zasadami Active Directory. [Plan wdrożenia](../authentication/howto-sspr-deployment.md) samoobsługowego resetowania hasła przedstawia najlepsze rozwiązania w zakresie wdrażania funkcji samoobsługowego resetowania haseł do użytkowników za pośrednictwem sieci Web i zintegrowanych z systemem Windows.

![Architektura usługi Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Na koniec w przypadku organizacji, które umożliwiają użytkownikom zmianę haseł w usłudze AD, usługi AD można skonfigurować tak, aby korzystały z tych samych zasad haseł, które są używane w usłudze Azure AD w ramach [funkcji ochrony hasłem usługi Azure AD](../authentication/concept-password-ban-bad-on-premises.md)w publicznej wersji zapoznawczej.

Gdy organizacja jest gotowa do przenoszenia aplikacji zintegrowanej z usługą AD do chmury przez przeniesienie systemu operacyjnego obsługującego aplikację na platformę Azure, [Azure AD Domain Services](../../active-directory-domain-services/overview.md) zapewnia usługi domenowe zgodne z usługami AD (takie jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM). Azure AD Domain Services integruje się z istniejącą dzierżawą usługi Azure AD w organizacji, co umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych. Ponadto istniejące grupy i konta użytkowników mogą służyć do zabezpieczania dostępu do zasobów, co zapewnia płynne "podnoszenia i przesunięcia" zasobów lokalnych do usług infrastruktury platformy Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Zarządzanie zarządzane przez chmurę dla lokalnych aplikacji opartych na Federacji

W przypadku organizacji, która korzysta już z lokalnego dostawcy tożsamości, przeniesienie aplikacji do usługi Azure AD umożliwia bezpieczniejsze dostęp oraz łatwiejsze środowisko administracyjne do zarządzania Federacją. Usługa Azure AD umożliwia konfigurowanie szczegółowych kontroli dostępu dla poszczególnych aplikacji, w tym Multi-Factor Authentication usługi Azure AD, przy użyciu dostępu warunkowego usługi Azure AD. Usługa Azure AD obsługuje więcej możliwości, w tym certyfikaty podpisywania tokenów specyficznych dla aplikacji oraz konfigurowalne daty wygaśnięcia certyfikatu. Te funkcje, narzędzia i wskazówki umożliwiają organizacjom wycofanie lokalnych dostawców tożsamości. Firma Microsoft może na przykład przenieść aplikacje 17 987 z wewnętrznego Active Directory Federation Services firmy Microsoft (AD FS) do usługi Azure AD.

![Ewolucja usługi Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Aby rozpocząć Migrowanie aplikacji federacyjnych do usługi Azure AD jako dostawcy tożsamości, zapoznaj się z tematem https://aka.ms/migrateapps zawierającym linki do:

* Oficjalny dokument [dotyczący migrowania aplikacji do Azure Active Directory](https://aka.ms/migrateapps/whitepaper), który przedstawia zalety migracji i opisuje sposób planowania migracji w czterech jasno dostępnych fazach: odnajdywanie, klasyfikacja, migracja i bieżące zarządzanie. Zapoznaj się z informacjami o tym, jak myśleć o procesie i podzielić projekt na łatwe w użyciu elementy. W dokumencie znajdują się linki do ważnych zasobów, które pomogą Ci w tym czasie.

* Przewodnik po rozwiązaniach [Migrowanie uwierzytelniania aplikacji z programu Active Directory Federation Services do Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) Eksploruje bardziej szczegółowo te same cztery etapy planowania i wykonywania projektu migracji aplikacji. W tym przewodniku dowiesz się, jak zastosować te fazy do określonego celu przeniesienia aplikacji z Active Directory Federation Services (AD FS) do usługi Azure AD.

* [Skrypt gotowości do migracji Active Directory Federation Services](https://aka.ms/migrateapps/adfstools) można uruchomić na istniejących serwerach Active Directory Federation Services lokalnych (AD FS), aby określić gotowość aplikacji do migracji do usługi Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Ciągłe zarządzanie dostępem w aplikacjach w chmurze i lokalnych

Organizacje muszą mieć proces zarządzania dostępem, który jest skalowalny. Użytkownicy będą nadal zbierać prawa dostępu i kończyć się poza zastrzeżonym dla nich zainicjowaniem. Ponadto organizacje korporacyjne muszą być w stanie wydajnie skalować w celu ciągłego opracowywania i wymuszania zasad dostępu i kontroli.

Zwykle deleguje decyzje dotyczące zatwierdzenia dostępu do podmiotów podejmujących decyzje biznesowe. Ponadto może to dotyczyć samych użytkowników. Na przykład użytkownicy, którzy uzyskują dostęp do poufnych danych klienta w aplikacji marketingowej firmy w Europie, muszą znać zasady firmy. Użytkownicy-Goście mogą również nie wiedzieć, jakie są wymagania dotyczące obsługi danych w organizacji, do której zostały zaproszone.

Organizacje mogą zautomatyzować proces cyklu życia dostępu za pomocą technologii, takich jak [grupy dynamiczne](../enterprise-users/groups-dynamic-membership.md), w połączeniu z obsługą użytkowników do [aplikacji SaaS](../saas-apps/tutorial-list.md)lub [aplikacjami zintegrowanymi przy użyciu systemu do zarządzania tożsamościami między domenami (standard scim](../app-provisioning/use-scim-to-provision-users-and-groups.md)) standard. Organizacje mogą również kontrolować, którzy [Użytkownicy-Goście mają dostęp do aplikacji lokalnych](../external-identities/hybrid-cloud-to-on-premises.md). Te prawa dostępu można następnie regularnie przeglądać przy użyciu cyklicznych [przeglądów dostępu do usługi Azure AD](../governance/access-reviews-overview.md).

## <a name="future-directions"></a>Przyszłe instrukcje

W środowiskach hybrydowych, strategia firmy Microsoft polega na włączeniu wdrożeń, w których **chmura jest płaszczyzną kontroli tożsamości**, a lokalnymi katalogami i innymi systemami tożsamości, takimi jak Active Directory i inne aplikacje lokalne, są celem aprowizacji użytkowników z dostępem. Ta strategia będzie nadal zapewniać prawa, tożsamości i dostęp do tych aplikacji oraz obciążeń, które są na nich zależne. W tym stanie organizacje będą mogły zwiększyć produktywność użytkowników końcowych w całości z chmury.

![Architektura usługi Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozpoczynania tej podróży, zobacz Plany wdrażania usługi Azure AD znajdujące się w temacie <https://aka.ms/deploymentplans> . Zapewniają kompleksowe wskazówki dotyczące wdrażania możliwości usługi Azure Active Directory (Azure AD). Każdy plan wyjaśnia wartość biznesową, zagadnienia związane z planowaniem, projektowanie i procedury operacyjne, które są konieczne do pomyślnego przeprowadzenia wspólnych możliwości usługi Azure AD. Firma Microsoft stale aktualizuje plany wdrażania, korzystając z najlepszych rozwiązań uzyskanych na podstawie wdrożeń klientów i innych informacji zwrotnych, gdy dodamy nowe możliwości zarządzania z chmury za pomocą usługi Azure AD.