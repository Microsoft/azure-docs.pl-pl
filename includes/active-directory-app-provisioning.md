---
ms.openlocfilehash: c400856546142353a7294a03fce6bbff1c258cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557452"
---
W Azure Active Directory (Azure AD) termin **aprowizacji aplikacji** dotyczy automatycznego tworzenia tożsamości i ról użytkowników w aplikacjach w chmurze ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne Inicjowanie obsługi obejmuje konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról. Typowe scenariusze obejmują Inicjowanie obsługi użytkownika usługi Azure AD w aplikacjach takich jak [Dropbox](../articles/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../articles/active-directory/saas-apps/salesforce-provisioning-tutorial.md), [usługi ServiceNow](../articles/active-directory/saas-apps/servicenow-provisioning-tutorial.md)i inne.

![Diagram omówienia aprowizacji](./media/active-directory-app-provisioning/provisioning-overview.png)

Ta funkcja umożliwia:

- **Automatyzowanie aprowizacji**: automatyczne tworzenie nowych kont w odpowiednich systemach dla nowych osób po dołączeniu do zespołu lub organizacji.
- **Automatyzowanie anulowania aprowizacji:** Automatycznie Dezaktywuj konta w odpowiednich systemach, gdy ludzie opuszczają zespół lub organizację.
- **Synchronizuj dane między systemami:** Upewnij się, że tożsamości w Twoich aplikacjach i systemach są aktualne na podstawie zmian w katalogu lub w systemie zasobów ludzkich.
- **Grupy udostępniania:** Zainicjuj obsługę grup dla aplikacji, które je obsługują.
- **Zarządzanie dostępem:** Monitoruj i przeprowadzaj inspekcję, która została zainicjowana w aplikacjach.
- **Bezproblemowo Wdrażaj w scenariuszach brązowych pól:** Dopasowuje istniejące tożsamości między systemami i pozwala na łatwą integrację, nawet jeśli użytkownicy znajdują się już w systemie docelowym.
- **Użyj zaawansowanego dostosowywania:** Korzystaj z dostosowywalnych mapowań atrybutów, które definiują, jakie dane użytkownika powinny być przepływać z systemu źródłowego do systemu docelowego.
- **Otrzymywanie alertów dotyczących zdarzeń krytycznych:** Usługa aprowizacji udostępnia alerty dla zdarzeń krytycznych i umożliwia integrację Log Analytics, w której można zdefiniować niestandardowe alerty w celu określenia potrzeb firmy.

## <a name="benefits-of-automatic-provisioning"></a>Zalety automatycznej aprowizacji

Wraz ze wzrostem liczby aplikacji używanych w nowoczesnych organizacjach Administratorzy IT są zależne od zarządzania dostępem w odpowiedniej skali. Standardy, takie jak Security Assertions Markup Language (SAML) lub Open ID Connect (OIDC), umożliwiają administratorom szybkie konfigurowanie logowania jednokrotnego (SSO), ale dostęp wymaga również, aby użytkownicy mieli do nich możliwość aprowizacji. W przypadku wielu administratorów Inicjowanie obsługi administracyjnej oznacza ręczne tworzenie każdego konta użytkownika lub przekazywanie plików CSV w każdym tygodniu, ale te procesy są czasochłonne, kosztowne i podatne na błędy. Rozwiązania takie jak "JIT" (just-in-Time) zostały wdrożone w celu zautomatyzowania aprowizacji, ale przedsiębiorstwa również wymagają rozwiązania, aby anulować obsługę administracyjną użytkowników, którzy opuszczają organizację lub nie potrzebują już dostępu do niektórych aplikacji na podstawie zmiany roli.

Niektóre typowe motywacje dotyczące korzystania z automatycznej aprowizacji obejmują:

- Maksymalizacja wydajności i dokładności procesów aprowizacji.
- Oszczędności związane z hostingiem i utrzymywaniem niestandardowo opracowanych rozwiązań i skryptów aprowizacji.
- Zabezpieczanie organizacji przez natychmiastowe usuwanie tożsamości użytkowników z aplikacji Key SaaS, gdy opuszczają one organizację.
- Łatwo Importuj dużą liczbę użytkowników do określonej aplikacji lub systemu SaaS.
- Posiadanie jednego zestawu zasad, aby określić, kto jest zainicjowany i kto może logować się do aplikacji.

Inicjowanie obsługi użytkowników w usłudze Azure AD może pomóc rozwiązać te wyzwania. Aby dowiedzieć się więcej o tym, jak klienci korzystają z aprowizacji użytkowników usługi Azure AD, możesz przeczytać [analizę przypadku ASOs](https://aka.ms/asoscasestudy). Poniższe wideo zawiera omówienie aprowizacji użytkowników w usłudze Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jakie aplikacje i systemy mogą być używane przez automatyczne Inicjowanie obsługi użytkowników w usłudze Azure AD?

Funkcje usługi Azure AD są wstępnie zintegrowane z obsługą wielu popularnych aplikacji SaaS i systemów kadr oraz ogólnego wsparcia dla aplikacji, które implementują określone części [standardu standard scim 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Wstępnie zintegrowane aplikacje (aplikacje w galerii SaaS)**. Możesz znaleźć wszystkie aplikacje, dla których usługa Azure AD obsługuje wstępnie zintegrowany łącznik aprowizacji na [liście samouczków aplikacji na potrzeby aprowizacji użytkowników](../articles/active-directory/saas-apps/tutorial-list.md). Wstępnie zintegrowane aplikacje wymienione w galerii zwykle używają interfejsów API zarządzania użytkownikami opartymi na Standard scim 2,0 na potrzeby aprowizacji. 

   ![Logo usługi Salesforce](./media/active-directory-app-provisioning/gallery-app-logos.png)

   Jeśli chcesz zażądać nowej aplikacji do aprowizacji, możesz [poprosić o integrację aplikacji z naszą galerią aplikacji](../articles/active-directory/develop/v2-howto-app-gallery-listing.md). W przypadku żądania aprowizacji użytkowników wymagamy, aby aplikacja miała punkt końcowy zgodny z standard scim. Zażądaj, aby Dostawca aplikacji był zgodny ze standardem Standard scim, dzięki czemu możemy szybko dodać aplikację do naszej platformy.

* **Aplikacje obsługujące standard scim 2,0**. Aby uzyskać informacje na temat ogólnych połączeń aplikacji, które implementują interfejsy API zarządzania użytkownikami opartymi na systemie Standard scim 2,0, zobacz [Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji użytkowników](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Co to jest system do zarządzania tożsamościami między domenami (standard scim)?

Aby ułatwić automatyzację aprowizacji i anulowania obsługi, aplikacje uwidaczniają zastrzeżone interfejsy API użytkowników i grup. Jednak każda osoba, która podjęła próbę zarządzania użytkownikami w więcej niż jednej aplikacji, powiedzie, że każda aplikacja próbuje wykonać te same proste akcje, takie jak tworzenie lub aktualizowanie użytkowników, Dodawanie użytkowników do grup lub cofanie aprowizacji użytkowników. Jednak wszystkie te proste akcje są implementowane tylko nieco inaczej, przy użyciu różnych ścieżek punktów końcowych, różnych metod, aby określić informacje o użytkownikach i innych schematów do reprezentowania poszczególnych elementów informacji.

Aby rozwiązać te problemy, Specyfikacja Standard scim zapewnia wspólny schemat użytkownika, aby ułatwić użytkownikom przechodzenie do aplikacji, a także z nich. Standard scim staje się de facto standardem do aprowizacji i, w połączeniu z standardami Federacji, takimi jak SAML lub OpenID Connect Connect, zapewnia administratorom kompleksowe rozwiązanie do zarządzania dostępem oparte na standardach.

Aby uzyskać szczegółowe wskazówki dotyczące opracowywania punktu końcowego Standard scim w celu zautomatyzowania aprowizacji i anulowania obsługi użytkowników i grup w aplikacji, zobacz [Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji użytkowników](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md). W przypadku wstępnie zintegrowanych aplikacji w galerii (zapasowych, Azure Databricks, płatnej śniegu itp.) możesz pominąć dokumentację dla deweloperów i korzystać z samouczków udostępnionych w [tym miejscu](../articles/active-directory/saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Aprowizowanie automatyczne a ręczne

Aplikacje w galerii usługi Azure AD obsługują jeden z dwóch trybów aprowizacji:

* **Ręczna** obsługa administracyjna oznacza, że aplikacja nie ma jeszcze automatycznego łącznika aprowizacji usługi Azure AD. Konta użytkowników muszą zostać utworzone ręcznie, na przykład przez dodanie użytkowników bezpośrednio do portalu administracyjnego aplikacji lub przekazanie arkusza kalkulacyjnego z informacjami o koncie użytkownika. Zapoznaj się z dokumentacją dostarczoną przez aplikację lub skontaktuj się z deweloperem aplikacji, aby określić, jakie mechanizmy są dostępne.

* **Automatyczne** oznacza, że dla tej aplikacji opracowano łącznik aprowizacji usługi Azure AD. Należy postępować zgodnie z samouczkiem Instalatora specyficznym dla konfigurowania aprowizacji aplikacji. Samouczki aplikacji można znaleźć na [liście samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](../articles/active-directory/saas-apps/tutorial-list.md).

W galerii usługi Azure AD aplikacje obsługujące automatyczną obsługę administracyjną są oznaczone ikoną **aprowizacji** . Przejdź do nowego środowiska w wersji zapoznawczej galerii, aby wyświetlić te ikony (na transparencie w górnej części **strony Dodawanie aplikacji** wybierz link, który wskazuje na **kliknięcie tutaj, aby wypróbować nową i udoskonaloną galerię aplikacji**).

![Ikona aprowizacji w galerii aplikacji](./media/active-directory-app-provisioning/browse-gallery.png)

Tryb aprowizacji obsługiwany przez aplikację jest również widoczny na karcie **aprowizacji** po dodaniu aplikacji do aplikacji dla **przedsiębiorstw**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak mogę skonfigurować automatyczne Inicjowanie obsługi administracyjnej aplikacji?

W przypadku wstępnie zintegrowanych aplikacji wymienionych w galerii wskazówki krok po kroku są dostępne w celu skonfigurowania automatycznej aprowizacji. Zapoznaj się z [listą samouczków dotyczących zintegrowanych aplikacji galerii](../articles/active-directory/saas-apps/tutorial-list.md). Poniższy film wideo pokazuje, jak skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w usłudze SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

W przypadku innych aplikacji, które obsługują standard scim 2,0, wykonaj kroki opisane w artykule [Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji użytkowników](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).