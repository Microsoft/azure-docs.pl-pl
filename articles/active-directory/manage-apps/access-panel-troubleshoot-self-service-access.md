---
title: Problem z wykorzystaniem samoobsługowego dostępu do aplikacji | Microsoft Docs
description: Rozwiązywanie problemów związanych z dostępem do aplikacji samoobsługowych
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a8a748b11e695041aa6baece381bc8d7d068992
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760919"
---
# <a name="problem-using-self-service-application-access"></a>Problem z wykorzystaniem samoobsługowego dostępu do aplikacji

Samoobsługowy dostęp do aplikacji to doskonały sposób na umożliwienie użytkownikom samodzielnego odnajdywania aplikacji, opcjonalnie Zezwalanie grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. Można zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników w celu logowania jednokrotnego w aplikacjach bezpośrednio z poziomu panelu dostępu.

Zanim użytkownicy będą mogli automatycznie wykrywać aplikacje z poziomu ich panelu dostępu, musisz włączyć **dostęp do aplikacji samoobsługowej** do wszystkich aplikacji, które mają pozwolić użytkownikom na samodzielne odnajdowanie i żądać dostępu do programu.

## <a name="general-issues-to-check-first"></a>Ogólne problemy do sprawdzenia w pierwszej kolejności

-   Upewnij się, że samoobsługowy dostęp do aplikacji jest prawidłowo skonfigurowany. Zobacz sekcję "jak skonfigurować dostęp do aplikacji samoobsługowej".

-   Upewnij się, że użytkownik lub Grupa została włączona, aby zażądać dostępu do aplikacji samoobsługowej.

-   Upewnij się, że użytkownik jest w odpowiednim miejscu do samoobsługowego dostępu do aplikacji. Użytkownicy mogą przechodzić do [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i klikać przycisk **+ Dodaj** , aby znaleźć aplikacje, dla których włączono samoobsługowy dostęp.

-   Jeśli wcześniej skonfigurowano dostęp do aplikacji samoobsługi, spróbuj zalogować się i ponownie w panelu dostępu użytkownika po kilku minutach, aby sprawdzić, czy pojawiły się zmiany dostępu samoobsługi.

## <a name="how-to-configure-self-service-application-access"></a>Jak skonfigurować samoobsługowy dostęp do aplikacji

Aby włączyć samoobsługowy dostęp do aplikacji, wykonaj następujące czynności:

1. Otwórz [**Portal Azure**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, z której chcesz włączyć dostęp do funkcji samoobsługi z listy.

7. Po załadowaniu aplikacji kliknij pozycję **samoobsługa** w menu nawigacji po lewej stronie aplikacji.

8. Aby włączyć samoobsługowy dostęp do aplikacji dla tej aplikacji, Włącz opcję **zezwól użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz na **wartość tak.**

9. Aby wybrać grupę, do której mają zostać dodane użytkownicy żądający dostępu do tej aplikacji, kliknij selektor obok etykiety, **do której Grupa powinna zostać dodana** , a następnie wybierz grupę.

10. **Opcjonalne:** Jeśli chcesz wymagać zatwierdzenia biznesowego, zanim użytkownicy będą mieć dostęp, ustaw opcję **Wymagaj zatwierdzenia przed udzieleniem dostępu do tej aplikacji?** Przełącz na **wartość tak**.

11. **Opcjonalne: w przypadku aplikacji korzystających tylko z logowania** jednokrotnego, jeśli chcesz zezwolić tym osobom, aby osoby zatwierdzające firmy określiły hasła wysyłane do tej aplikacji dla zatwierdzonych użytkowników, ustaw opcję **Zezwalaj na osoby zatwierdzające, aby ustawić hasła użytkownika dla tej aplikacji?** Przełącz na **wartość tak**.

12. **Opcjonalne:** Aby określić osoby zatwierdzające w firmie, które mogą zatwierdzać dostęp do tej aplikacji, kliknij selektor obok etykiety, **która może zatwierdzić dostęp do tej aplikacji?** , aby wybrać maksymalnie 10 osób zatwierdzających w biznesie.

    >[!NOTE]
    > Grupy nie są obsługiwane.
    >
    >

13. **Opcjonalne:** **w przypadku aplikacji, które uwidaczniają role**, aby przypisać użytkowników, którzy mają zostać przypisani do roli, kliknij selektor obok elementu, **do którego rola powinna być przypisana do tej aplikacji?** , aby wybrać rolę, do której mają być przypisani użytkownicy.

14. Kliknij przycisk **Zapisz** w górnej części bloku, aby zakończyć.

Po zakończeniu konfigurowania aplikacji samoobsługi użytkownicy mogą przechodzić do [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i klikać przycisk **+ Dodaj** , aby znaleźć aplikacje, dla których włączono funkcję samoobsługowego dostępu. Osoby zatwierdzające firmy widzą również powiadomienie w [panelu dostępu do aplikacji](https://myapps.microsoft.com/). Możesz włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażądał dostępu do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługują tylko jednorazowe przepływy pracy zatwierdzania, co oznacza, że jeśli określisz wiele osób zatwierdzających, każda osoba zatwierdzająca może zatwierdzić dostęp do aplikacji.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu 

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   TenantID

-   Typ przeglądarki

-   Strefa czasowa i czas/przedział czasu w trakcie wystąpienia błędu

-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../users-groups-roles/groups-self-service-management.md)
