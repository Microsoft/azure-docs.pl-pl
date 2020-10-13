---
title: Tworzenie lub edytowanie grupy dynamicznej oraz pobieranie stanu — Azure AD | Microsoft Docs
description: Jak utworzyć lub zaktualizować regułę członkostwa w grupie w Azure Portal i sprawdzić jej stan przetwarzania.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94d1a4fa717bbee9974f7218e704ba06dad530d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531534"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Utwórz lub Zaktualizuj grupę dynamiczną w Azure Active Directory

W Azure Active Directory (Azure AD) można używać reguł do określania członkostwa w grupach na podstawie właściwości użytkownika lub urządzenia. W tym artykule opisano sposób konfigurowania reguły dla grupy dynamicznej w Azure Portal.
Dynamiczne członkostwo jest obsługiwane dla grup zabezpieczeń lub grup Microsoft 365. Po zastosowaniu reguły członkostwa w grupie atrybuty użytkownika i urządzenia są oceniane pod kątem zgodności z regułą członkostwa. Gdy atrybut zostanie zmieniony dla użytkownika lub urządzenia, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmiany członkostwa. Użytkownicy i urządzenia są dodawani lub usuwani, jeśli spełnią warunki dla grupy. Grupy zabezpieczeń mogą być używane dla urządzeń lub użytkowników, ale grupy Microsoft 365 mogą być tylko grupami użytkowników.

## <a name="rule-builder-in-the-azure-portal"></a>Konstruktor reguł w Azure Portal

Usługa Azure AD udostępnia Konstruktor reguł, który umożliwia szybsze tworzenie i aktualizowanie ważnych reguł. Konstruktor reguł obsługuje konstruowanie do pięciu wyrażeń. Konstruktor reguł ułatwia tworzenie reguły z kilkoma prostymi wyrażeniami, ale nie można ich użyć do odtworzenia każdej reguły. Jeśli Konstruktor reguł nie obsługuje reguły, którą chcesz utworzyć, możesz użyć pola tekstowego.

Poniżej przedstawiono kilka przykładów zaawansowanych reguł lub składni, dla których zalecamy utworzenie przy użyciu pola tekstowego:

- Reguła z więcej niż pięcioma wyrażeniami
- Reguła bezpośrednich podwładnych
- Ustawianie [pierwszeństwa operatorów](groups-dynamic-membership.md#operator-precedence)
- [Reguły z wyrażeniami złożonymi](groups-dynamic-membership.md#rules-with-complex-expressions); na przykład `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Konstruktor reguł może nie być w stanie wyświetlić niektórych reguł skonstruowanych w polu tekstowym. Gdy Konstruktor reguł nie może wyświetlić reguły, może zostać wyświetlony komunikat. Konstruktor reguł nie zmienia obsługiwanej składni, walidacji lub przetwarzania reguł grupy dynamicznej w dowolny sposób.

![Zrzut ekranu przedstawiający stronę "reguły członkostwa dynamicznego" z akcją "Dodaj wyrażenie" na karcie "Konfigurowanie reguł".](./media/groups-create-rule/update-dynamic-group-rule.png)

Przykłady składni, obsługiwane właściwości, operatory i wartości dla reguły członkostwa można znaleźć w temacie [dynamiczne reguły członkostwa dla grup w Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Aby utworzyć regułę członkostwa w grupie

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta należącego do roli Administrator globalny, administrator usługi Intune lub administrator użytkowników w organizacji Azure AD.
1. Wyszukaj i wybierz **grupy**.
1. Wybierz pozycję **wszystkie grupy**, a następnie wybierz pozycję **Nowa grupa**.

   ![Wybierz polecenie, aby dodać nową grupę](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na stronie **Grupa** wprowadź nazwę i opis dla nowej grupy. Wybierz **Typ członkostwa** dla użytkowników lub urządzeń, a następnie wybierz pozycję **Dodaj zapytanie dynamiczne**. Konstruktor reguł obsługuje maksymalnie pięć wyrażeń. Aby dodać więcej niż pięć wyrażeń, należy użyć pola tekstowego.

   ![Zrzut ekranu pokazujący stronę "wszystkie grupy" z wybraną akcją "Nowa grupa".](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Aby wyświetlić niestandardowe właściwości rozszerzenia dostępne dla kwerendy dotyczącej członkostwa:
   1. Wybierz pozycję **Pobierz niestandardowe właściwości rozszerzenia**
   1. Wprowadź identyfikator aplikacji, a następnie wybierz pozycję **Odśwież właściwości**.
1. Po utworzeniu reguły wybierz pozycję **Zapisz**.
1. Na stronie **Nowa grupa** wybierz pozycję **Utwórz** , aby utworzyć grupę.

Jeśli wprowadzona reguła jest nieprawidłowa, wyjaśnienie, dlaczego nie można przetworzyć reguły, jest wyświetlana w powiadomieniach platformy Azure w portalu. Przeczytaj uważnie, aby zrozumieć, jak naprawić regułę.

## <a name="to-update-an-existing-rule"></a>Aby zaktualizować istniejącą regułę

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta należącego do roli Administrator globalny, administrator grupy, administrator usługi Intune lub administrator użytkowników w organizacji Azure AD.
1. Wybierz kolejno pozycje **grupy**  >  **wszystkie grupy**.
1. Wybierz grupę, aby otworzyć jej profil.
1. Na stronie profil grupy wybierz pozycję **dynamiczne reguły członkostwa**. Konstruktor reguł obsługuje maksymalnie pięć wyrażeń. Aby dodać więcej niż pięć wyrażeń, należy użyć pola tekstowego.

   ![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Aby wyświetlić niestandardowe właściwości rozszerzenia dostępne dla reguły członkostwa:
   1. Wybierz pozycję **Pobierz niestandardowe właściwości rozszerzenia**
   1. Wprowadź identyfikator aplikacji, a następnie wybierz pozycję **Odśwież właściwości**.
1. Po zaktualizowaniu reguły wybierz pozycję **Zapisz**.

## <a name="turn-on-or-off-welcome-email"></a>Włączanie lub wyłączanie powitalnej poczty e-mail

Po utworzeniu nowej grupy Microsoft 365 zostanie wysłane powiadomienie powitalnej poczty e-mail użytkowników, którzy zostaną dodani do grupy. Później, jeśli jakiekolwiek atrybuty użytkownika lub urządzenia zostaną zmienione, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmian członkostwa. Dodani użytkownicy otrzymują również powiadomienie powitalne. To zachowanie można wyłączyć w programie [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Sprawdzanie stanu przetwarzania dla reguły

Na stronie **Przegląd** dla grupy można zobaczyć stan przetwarzania członkostwa i datę ostatniej aktualizacji.
  
  ![Wyświetlanie stanu grupy dynamicznej](./media/groups-create-rule/group-status.png)

Na potrzeby stanu **przetwarzania członkostwa** można wyświetlić następujące komunikaty o stanie:

- **Ocenianie**: Odebrano zmianę grupy, a aktualizacje są oceniane.
- **Przetwarzanie**: aktualizacje są przetwarzane.
- **Aktualizacja zakończona**: przetwarzanie zostało zakończone i wszystkie odpowiednie aktualizacje zostały wykonane.
- **Błąd przetwarzania**: nie można ukończyć przetwarzania z powodu błędu podczas oceniania reguły członkostwa.
- **Wstrzymano aktualizację**: aktualizacje reguł członkostwa dynamicznego zostały wstrzymane przez administratora. MembershipRuleProcessingState jest ustawiona na "wstrzymany".

W przypadku **ostatniej zaktualizowanego stanu członkostwa** można wyświetlić następujące komunikaty o stanie:

- &lt;**Data i godzina** &gt; : godzina ostatniej aktualizacji członkostwa.
- **W toku**: aktualizacje są obecnie w toku.
- **Nieznane**: nie można pobrać czasu ostatniego aktualizowania. Grupa może być nowa.

Jeśli wystąpi błąd podczas przetwarzania reguły członkostwa dla określonej grupy, w górnej części **strony Przegląd** dla grupy zostanie wyświetlony alert. Jeśli nie można przetworzyć oczekujących aktualizacji dynamicznego członkostwa dla wszystkich grup w organizacji przez ponad 24 godziny, w górnej części **wszystkich grup**zostanie wyświetlony alert.

![przetwarzanie alertów komunikatów o błędach](./media/groups-create-rule/processing-error.png)

Te artykuły zawierają dodatkowe informacje dotyczące grup w Azure Active Directory.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)