---
title: Zarządzanie użytkownikami dla aplikacji dla przedsiębiorstw w usłudze Azure AD
description: Dowiedz się, jak zarządzać obsługą kont użytkowników w aplikacjach dla przedsiębiorstw przy użyciu Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 02/04/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 02d415bd957b0490857081b996c592f90365f031
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555628"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw w Azure Portal

W tym artykule opisano ogólne czynności związane z zarządzaniem automatycznym inicjowaniem obsługi kont użytkowników i anulowaniem aprowizacji dla aplikacji, które je obsługują. *Inicjowanie obsługi konta użytkownika* to czynność tworzenia, aktualizowania i/lub wyłączania rekordów kont użytkowników w lokalnym magazynie profilów użytkownika aplikacji. Większość aplikacji w chmurze i SaaS przechowuje rolę i uprawnienia użytkowników w lokalnym magazynie profilów użytkownika, a obecność takiego rekordu użytkownika w lokalnym magazynie użytkownika jest *wymagana* do logowania jednokrotnego i dostępu do pracy. Aby dowiedzieć się więcej o automatycznym inicjowaniu obsługi kont użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS przy użyciu Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które obsługują automatyczną obsługę administracyjną w usłudze Azure AD. Należy zacząć od znalezienia samouczka dotyczącego konfiguracji aprowizacji określonego dla aplikacji na [liście samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](../saas-apps/tutorial-list.md). Prawdopodobnie znajdziesz wskazówki krok po kroku dotyczące konfigurowania aplikacji i usługi Azure AD w celu utworzenia połączenia aprowizacji.

## <a name="finding-your-apps-in-the-portal"></a>Znajdowanie aplikacji w portalu

Za pomocą portalu Azure Active Directory można wyświetlać wszystkie aplikacje skonfigurowane do rejestracji jednokrotnej w katalogu i zarządzać nimi. Aplikacje dla przedsiębiorstw to aplikacje wdrożone i używane w organizacji. Wykonaj następujące kroki, aby wyświetlić aplikacje dla przedsiębiorstw i zarządzać nimi:

1. Otwórz [portal Azure Active Directory](https://aad.portal.azure.com).
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
1. Wybierz dowolną aplikację, aby załadować jej okienko zasobów, w której można wyświetlać raporty i zarządzać ustawieniami aplikacji.
1. Wybierz opcję **aprowizacji** , aby zarządzać ustawieniami aprowizacji konta użytkownika dla wybranej aplikacji.

   ![Ekran aprowizacji do zarządzania ustawieniami aprowizacji konta użytkownika](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Tryby aprowizacji

Okienko **aprowizacji** rozpoczyna się od menu **tryb** , w którym są wyświetlane tryby aprowizacji obsługiwane przez aplikację dla przedsiębiorstw i umożliwiają ich Konfigurowanie. Dostępne opcje to:

* **Automatyczne** — ta opcja jest wyświetlana, jeśli usługa Azure AD obsługuje automatyczną obsługę administracyjną opartą na interfejsie API lub cofanie aprowizacji kont użytkowników w tej aplikacji. Wybierz ten tryb, aby wyświetlić interfejs, który pomaga administratorom:

  * Konfigurowanie usługi Azure AD do nawiązywania połączenia z interfejsem API zarządzania użytkownikami aplikacji
  * Tworzenie mapowań kont i przepływów pracy definiujących sposób przepływu danych konta użytkownika między usługą Azure AD a aplikacją
  * Zarządzanie usługą Azure AD Provisioning

* **Ręczna** — ta opcja jest wyświetlana, jeśli usługa Azure AD nie obsługuje automatycznej aprowizacji kont użytkowników w tej aplikacji. W takim przypadku rekordy kont użytkowników przechowywane w aplikacji muszą być zarządzane przy użyciu procesu zewnętrznego w oparciu o możliwości zarządzania użytkownikami i aprowizacji udostępniane przez tę aplikację (co może obejmować Inicjowanie obsługi just in Time do protokołu SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi konta użytkownika

Wybierz opcję **Automatyczne** , aby określić ustawienia poświadczeń administratora, mapowania, uruchamianie i zatrzymywanie oraz synchronizację.

### <a name="admin-credentials"></a>Poświadczenia administratora

Rozwiń węzeł **poświadczenia administratora** , aby wprowadzić poświadczenia wymagane przez usługę Azure AD do łączenia się z interfejsem API zarządzania użytkownikami aplikacji. Wymagane dane wejściowe różnią się w zależności od aplikacji. Aby dowiedzieć się więcej o typach poświadczeń i wymaganiach dotyczących określonych aplikacji, zapoznaj się z [samouczkiem dotyczącym konfiguracji tej konkretnej aplikacji](user-provisioning.md).

Wybierz pozycję **Testuj połączenie** , aby przetestować poświadczenia, ponieważ usługa Azure AD podejmie próbę nawiązania połączenia z aplikacją aprowizacji aplikacji przy użyciu podanych poświadczeń.

### <a name="mappings"></a>Mapowania

Rozwiń węzeł **mapowania** , aby wyświetlić i edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową, gdy konta użytkowników są inicjowane lub aktualizowane.

Istnieje wstępnie skonfigurowany zestaw mapowań między obiektami użytkowników usługi Azure AD i obiektami użytkowników aplikacji SaaS. Niektóre aplikacje również zarządzają obiektami grup. Wybierz mapowanie w tabeli, aby otworzyć Edytor mapowania po prawej stronie, gdzie można je przeglądać i dostosowywać.

![Pokazuje ekran mapowania atrybutów](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Obsługiwane dostosowania obejmują:

* Włączanie i wyłączanie mapowań określonych obiektów, takich jak obiekt użytkownika usługi Azure AD, do obiektu użytkownika aplikacji SaaS.
* Edytowanie atrybutów przepływających z obiektu użytkownika usługi Azure AD do obiektu użytkownika aplikacji. Aby uzyskać więcej informacji na temat mapowania atrybutów, zobacz [Omówienie typów mapowania atrybutów](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrowanie akcji aprowizacji, które usługa Azure AD działa w aplikacji dostosowanej. Zamiast korzystać z w pełni zsynchronizowanych obiektów usługi Azure AD, można ograniczyć uruchamianie akcji.

  Na przykład wybierz pozycję **Aktualizacja** i usługa Azure AD tylko aktualizuje istniejące konta użytkowników w aplikacji, ale nie tworzy nowych. Tylko wybierz pozycję **Utwórz** i tylko platforma Azure tworzy tylko nowe konta użytkowników, ale nie aktualizuje istniejących. Ta funkcja umożliwia administratorom tworzenie różnych mapowań na potrzeby tworzenia kont i aktualizowania przepływów pracy.

* Dodawanie nowego mapowania atrybutów. Wybierz pozycję **Dodaj nowe mapowanie** w dolnej części okienka **Mapowanie atrybutu** . Wypełnij formularz **Edytuj atrybut** i wybierz **przycisk OK** , aby dodać nowe mapowanie do listy.

### <a name="settings"></a>Ustawienia

Możesz uruchomić i zatrzymać usługę Azure AD Provisioning dla wybranej aplikacji w obszarze **Ustawienia** na ekranie **aprowizacji** . Możesz również wyczyścić pamięć podręczną aprowizacji i ponownie uruchomić usługę.

Jeśli Inicjowanie obsługi jest włączane po raz pierwszy dla aplikacji, Włącz usługę, zmieniając **stan aprowizacji** na **włączone**. Ta zmiana powoduje, że usługa aprowizacji usługi Azure AD uruchamia cykl początkowy. Odczytuje użytkowników przypisanych w sekcji **Użytkownicy i grupy** , wysyła zapytanie do aplikacji docelowej, a następnie uruchamia akcje aprowizacji zdefiniowane w sekcji **mapowania** usługi Azure AD. W trakcie tego procesu usługa aprowizacji przechowuje buforowane dane dotyczące kont użytkowników, którymi zarządza, dlatego nie ma to wpływu na konta niezarządzane w aplikacjach docelowych, które nigdy nie są objęte zakresem przydziału. Po wstępnym cyklu usługa aprowizacji automatycznie synchronizuje obiekty użytkowników i grup w przedziale 40-minutowym.

Zmień **stan aprowizacji** na **wyłączony**  , aby wstrzymać usługę aprowizacji. W tym stanie platforma Azure nie tworzy, nie aktualizuje ani nie usuwa żadnych obiektów użytkowników ani grup w aplikacji. Zmień stan z powrotem na **włączony** , a usługa odbiera w miejscu, w którym została przerwana.

**Wyczyść bieżący stan i ponownie uruchom synchronizację** wyzwala cykl początkowy. Następnie usługa ponownie oceni wszystkich użytkowników w systemie źródłowym i określi, czy znajdują się one w zakresie aprowizacji. Może to być przydatne, gdy aplikacja jest obecnie w kwarantannie lub trzeba wprowadzić zmiany mapowań atrybutów. Należy zauważyć, że cykl początkowy trwa dłużej niż typowy przyrostowy cykl ze względu na liczbę obiektów, które należy oszacować. Więcej informacji o wydajności początkowych i przyrostowych cykli można znaleźć [tutaj](application-provisioning-when-will-provisioning-finish-specific-user.md).
