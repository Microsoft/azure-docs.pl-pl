---
title: Przypisywanie licencji do grupy Azure Active Directory | Microsoft Docs
description: Jak przypisać licencje użytkownikom przy użyciu licencjonowania grup Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e1b61ca00b83d4c6b43fb5ade68dfb1228f0e0d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376596"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Przypisywanie licencji użytkownikom według członkostwa w grupie w Azure Active Directory

W tym artykule przedstawiono sposób przypisywania licencji produktów do grupy użytkowników i weryfikowania, czy są one prawidłowo licencjonowane w usłudze Azure Active Directory (Azure AD).

W tym przykładzie organizacja usługi Azure AD zawiera grupę zabezpieczeń o nazwie **Dział kadr**. Ta grupa obejmuje wszystkich członków działu kadr (około 1 000 użytkowników). Chcesz przypisać licencje Office 365 Enterprise E3 do całego działu. Usługa Yammer Enterprise, która jest dołączona do produktu, musi być tymczasowo wyłączona do momentu, aż dział zacznie korzystać z niego. Należy również wdrożyć licencje Enterprise Mobility + Security dla tej samej grupy użytkowników.

> [!NOTE]
> Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić właściwość lokalizacja użycia dla użytkownika.
>
> W przypadku przypisania licencji grupy Wszyscy użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu. Jeśli masz użytkowników w wielu lokalizacjach, zalecamy, aby zawsze ustawiać lokalizację użycia w ramach przepływu tworzenia użytkownika w usłudze Azure AD (np. za pośrednictwem konfiguracji programu AAD Connect), co gwarantuje, że wynik przypisywania licencji jest zawsze prawidłowy, a użytkownicy nie otrzymują usług w lokalizacjach, które są niedozwolone.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1. Przypisywanie wymaganych licencji

1. Zaloguj się do [**Centrum administracyjnego usługi Azure AD**](https://aad.portal.azure.com) przy użyciu konta administratora licencji. Aby zarządzać licencjami, konto musi być administratorem licencji, administratorem użytkowników lub administratorem globalnym.

1. Wybierz pozycję **licencje** , aby otworzyć stronę, na której można wyświetlić wszystkie produkty objętego w organizacji i zarządzać nimi.

1. W obszarze **wszystkie produkty**wybierz pozycję Office 365 Enterprise E5 i Enterprise Mobility + Security E3, wybierając pozycję nazwy produktów. Aby rozpocząć przypisanie, wybierz pozycję **Przypisz** w górnej części strony.

   ![Wybierz produkty, aby przypisać licencje](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Na stronie **Przypisywanie licencji** wybierz pozycję **Użytkownicy i grupy** , aby otworzyć listę użytkowników i grup.

1. Wybierz użytkownika lub grupę, a następnie użyj przycisku **Wybierz** u dołu strony, aby potwierdzić wybór.

1. Na stronie **Przypisywanie licencji** kliknij pozycję **Opcje przypisywania**, aby wyświetlić wszystkie plany usług dołączone do wcześniej wybranych produktów. Znajdź w usłudze **Yammer Enterprise** i wyłącz ją **, aby wyłączyć** tę usługę z licencji produktu. Potwierdź, klikając przycisk **OK** w dolnej części **opcji licencji**.

   ![Wybierz plany usługi dla licencji](./media/licensing-groups-assign/assignment-options.png)
  
1. Aby zakończyć przypisanie, na stronie **Przypisywanie licencji** kliknij przycisk **Przypisz** w dolnej części strony.

1. W prawym górnym rogu zostanie wyświetlone powiadomienie z informacją o stanie i wyniku procesu. Jeśli nie można ukończyć przypisywania do grupy (na przykład ze względu na istniejące licencje w grupie), kliknij powiadomienie, aby wyświetlić szczegóły błędu.

W przypadku przypisywania licencji do grupy usługa Azure AD przetwarza wszystkie istniejące elementy członkowskie tej grupy. Ten proces może zająć trochę czasu, w zależności od rozmiaru grupy. W następnym kroku opisano, jak sprawdzić, czy proces został ukończony, i określić, czy do rozwiązywania problemów należy zwrócić uwagę.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2. sprawdzenie, czy początkowe przypisanie zostało zakończone

1. Przejdź do **Azure Active Directory**  >  **grup**Azure Active Directory. Wybierz grupę, do której przypisano licencje.

1. Na stronie Grupa wybierz pozycję **licencje**. Dzięki temu można szybko potwierdzić, czy licencje zostały w pełni przypisane do użytkowników i czy występują błędy, które należy wziąć pod uwagę. Dostępne są następujące informacje:

   - Licencje usługi, które są obecnie przypisane do grupy. Wybierz wpis, aby wyświetlić konkretne usługi, które zostały włączone i wprowadzić zmiany.

   - Aktualizacje stanu ostatnich zmian licencji, które są dostępne, jeśli zmiany są przetwarzane lub zakończenie przetwarzania dla wszystkich członków użytkownika.

   - Informacje o przypisaniach licencji użytkowników, które są w stanie błędu.

   ![Błędy licencjonowania i stan licencji](./media/licensing-groups-assign/assignment-errors.png)

1. Zobacz więcej szczegółowych informacji na temat przetwarzania licencji w obszarze **Azure Active Directory**  >  **Użytkownicy i grupy**  >  *Nazwa grupy*  >  **inspekcji**. Sprawdź następujące działania:

   - Działanie: `Start applying group based license to users` . Jest to rejestrowane, gdy system pobiera zmiany przypisania licencji do grupy i zaczyna stosować ją do wszystkich członków użytkowników. Zawiera informacje o wprowadzonej zmianie.

   - Działanie: `Finish applying group based license to users` . Jest to rejestrowane, gdy system zakończy przetwarzanie wszystkich użytkowników w grupie. Zawiera podsumowanie liczby pomyślnie przetworzonych użytkowników i liczby użytkowników, którym nie można przypisać licencji grupy.

   [Przeczytaj tę sekcję](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) , aby dowiedzieć się więcej o tym, jak można użyć dzienników inspekcji do analizowania zmian wprowadzonych w ramach licencjonowania opartego na grupach.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3. sprawdzenie, czy występują problemy z licencją i rozwiązywanie problemów

1. Przejdź do **Azure Active Directory**  >  **grup**Azure Active Directory i Znajdź grupę, do której zostały przypisane licencje.
1. Na stronie Grupa wybierz pozycję **licencje**. Powiadomienie na stronie zawiera 10 użytkowników, do których nie można przypisać licencji. Otwórz go, aby wyświetlić listę wszystkich użytkowników w stanie błąd licencjonowania dla tej grupy.
1. Kolumna **przydziałów zakończonych niepowodzeniem** informuje nas, że nie można przypisać do użytkowników obu licencji na produkt. **Górna przyczyna kolumny niepowodzenie** zawiera przyczynę niepowodzenia. W tym przypadku jest to **sprzeczne plany usługi**.

   ![licencje, których nie można przypisać](./media/licensing-groups-assign/failed-assignments.png)

1. Wybierz użytkownika, aby otworzyć stronę **licencje** użytkownika. Ta strona zawiera wszystkie licencje, które są aktualnie przypisane do użytkownika. W tym przykładzie użytkownik ma licencję pakietu Office 365 Enterprise E1, która była dziedziczona z grupy **Użytkownicy kiosku** . Powoduje to konflikt z licencją E3, którą system próbował zastosować z grupy **działu kadr** . W efekcie żadna z licencji z tej grupy nie została przypisana do użytkownika.

   ![Wyświetl wszystkie konflikty licencji dla użytkownika](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Aby rozwiązać ten konflikt, Usuń użytkownika z grupy **Użytkownicy Kiosk** . Gdy usługa Azure AD przetwarza zmiany, licencje **działu kadr** są poprawnie przypisane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zestawu funkcji dla przypisania licencji przy użyciu grup, zobacz następujące artykuły:

- [Co to jest Licencjonowanie oparte na grupach w Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
- [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
- [Jak migrować użytkowników między licencjami produktu przy użyciu licencjonowania opartego na grupach w programie Azure Active Directory](licensing-groups-change-licenses.md)
- [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-group-advanced.md)
- [Przykłady programu PowerShell dla licencjonowania opartego na grupach w Azure Active Directory](licensing-ps-examples.md)