---
title: Jak korzystać z samoobsługowego dostępu do aplikacji | Microsoft Docs
description: Włącz dostęp do aplikacji samoobsługi, aby umożliwić użytkownikom znajdowanie własnych aplikacji
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55da8731855c8afda496edff33f3fbb7982cd44b
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "65784551"
---
# <a name="how-to-use-self-service-application-access"></a>Jak korzystać z samoobsługowego dostępu do aplikacji

Zanim użytkownicy będą mogli automatycznie wykrywać aplikacje z poziomu ich panelu dostępu, musisz włączyć **dostęp do aplikacji samoobsługowej** do wszystkich aplikacji, które mają pozwolić użytkownikom na samodzielne odnajdowanie i żądać dostępu do programu.

Ta funkcja to świetny sposób oszczędzania czasu i pieniędzy jako grupy IT i jest wysoce zalecany w ramach wdrożenia nowoczesnych aplikacji z Azure Active Directory.

Przy użyciu tej funkcji można:

-   Zezwól użytkownikom na samodzielne Odnajdowanie aplikacji z poziomu [panelu dostępu do aplikacji](https://myapps.microsoft.com/) bez BOTHERING grupy IT.

-   Dodaj tych użytkowników do wstępnie skonfigurowanej grupy, aby zobaczyć, kto zażądał dostępu, usunąć dostęp i zarządzać przypisanymi do nich rolami.

-   Opcjonalnie można zezwolić podmiotowi gospodarczemu na zatwierdzanie żądań dostępu do aplikacji, aby grupa IT nie miała do niej uprawnień.

-   Opcjonalnie można skonfigurować maksymalnie 10 osób, które mogą zatwierdzać dostęp do tej aplikacji.

-   Opcjonalnie Zezwól podmiotowi gospodarczemu na ustawienie haseł, których mogą używać użytkownicy do logowania się do aplikacji, bezpośrednio z poziomu [panelu dostępu do aplikacji](https://myapps.microsoft.com/)osoby zatwierdzającej.

-   Opcjonalnie można automatycznie przypisywać użytkownikom samoobsługi bezpośrednio przypisane do roli aplikacji.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włącz dostęp do aplikacji samoobsługi, aby umożliwić użytkownikom znajdowanie własnych aplikacji

Samoobsługowy dostęp do aplikacji to doskonały sposób na umożliwienie użytkownikom samodzielnego odnajdywania aplikacji, opcjonalnie Zezwalanie grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. Można zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników w celu logowania jednokrotnego w aplikacjach bezpośrednio z poziomu panelu dostępu.

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

    * Grupy nie są obsługiwane.

13. **Opcjonalne:** **w przypadku aplikacji, które uwidaczniają role**, aby przypisać użytkowników, którzy mają zostać przypisani do roli, kliknij selektor obok elementu, **do którego rola powinna być przypisana do tej aplikacji?** , aby wybrać rolę, do której mają być przypisani użytkownicy.

14. Kliknij przycisk **Zapisz** w górnej części bloku, aby zakończyć.

Po zakończeniu konfigurowania aplikacji samoobsługi użytkownicy mogą przechodzić do [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i klikać przycisk **+ Dodaj** , aby znaleźć aplikacje, dla których włączono funkcję samoobsługowego dostępu. Osoby zatwierdzające firmy widzą również powiadomienie w [panelu dostępu do aplikacji](https://myapps.microsoft.com/). Możesz włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażądał dostępu do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługują tylko jednorazowe przepływy pracy zatwierdzania, co oznacza, że jeśli określisz wiele osób zatwierdzających, każda osoba zatwierdzająca może zatwierdzić dostęp do aplikacji.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../users-groups-roles/groups-self-service-management.md)
