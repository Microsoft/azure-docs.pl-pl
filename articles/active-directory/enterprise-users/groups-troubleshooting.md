---
title: Rozwiązywanie problemów z członkostwem w grupie dynamicznej — usługa Azure AD | Microsoft Docs
description: Wskazówki dotyczące rozwiązywania problemów z członkostwem w grupie dynamicznej w Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8730ac8aa6a6056db67613f2ac8decf11740c467
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376692"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Rozwiązywanie problemów dotyczących grup

## <a name="troubleshooting-group-creation-issues"></a>Rozwiązywanie problemów z tworzeniem grup

**W Azure Portal wyłączono Tworzenie grupy zabezpieczeń, ale grupy można nadal tworzyć za pomocą programu PowerShell** **Użytkownik może utworzyć grupy zabezpieczeń w portalu Azure Portal** w Azure Portal kontroluje, czy użytkownicy niebędący administratorami mogą tworzyć grupy zabezpieczeń w panelu dostępu czy Azure Portal. Nie steruje tworzeniem grup zabezpieczeń za pośrednictwem programu PowerShell.

Aby wyłączyć tworzenie grupy dla użytkowników niebędących administratorami w programie PowerShell:
1. Sprawdź, czy użytkownicy niebędący administratorami mogą tworzyć grupy:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Jeśli zwróci wartość `UsersPermissionToCreateGroupsEnabled : True` , użytkownicy niebędący administratorami mogą tworzyć grupy. Aby wyłączyć tę funkcję:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Podczas próby utworzenia grupy dynamicznej w programie PowerShell otrzymałem maksymalną dozwoloną liczbę grup.**<br/>
Jeśli zostanie wyświetlony komunikat w programie PowerShell wskazujący, że _dynamiczna grupa zasad grupy osiągnęła maksymalną dozwoloną liczbę grup_, oznacza to, że osiągnięto maksymalny limit grup dynamicznych w organizacji. Maksymalna liczba grup dynamicznych na organizację to 5 000.

Aby utworzyć nowe grupy dynamiczne, należy najpierw usunąć niektóre istniejące grupy dynamiczne. Nie ma sposobu na zwiększenie limitu.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Rozwiązywanie problemów z członkostwem dynamicznym w grupach

**Zasada została skonfigurowana w grupie, ale w grupie nie ma żadnych członkostw do zaktualizowania**<br/>
1. Sprawdź wartości atrybutów użytkownika lub urządzenia w regule. Upewnij się, że istnieją użytkownicy, którzy spełniają tę zasadę. W przypadku urządzeń sprawdź właściwości urządzenia, aby upewnić się, że wszystkie zsynchronizowane atrybuty zawierają oczekiwane wartości.<br/>
2. Sprawdź stan przetwarzania członkostwa, aby upewnić się, czy został ukończony. Można sprawdzić [stan przetwarzania członkostwa](groups-create-rule.md#check-processing-status-for-a-rule) i datę ostatniej aktualizacji na stronie **Przegląd** dla grupy.

Jeśli wszystko wygląda dobrze, poczekaj chwilę, aż grupa zostanie wypełniona. W zależności od rozmiaru organizacji usługi Azure AD po raz pierwszy lub po zmianie reguły może minąć do 24 godzin.

**Zasada została skonfigurowana, ale teraz istniejące elementy członkowskie reguły są usuwane**<br/>Jest to oczekiwane zachowanie. Istniejące elementy członkowskie grupy są usuwane, gdy reguła jest włączona lub zmieniona. Użytkownicy, którzy otrzymali ocenę reguły, są dodawani jako członkowie do grupy.

**Nie widzę natychmiastowej zmiany członkostwa po dodaniu lub zmianie reguły, dlaczego nie?**<br/>Ocenianie dedykowanego członkostwa jest wykonywane okresowo w procesie asynchronicznym w tle. Czas trwania tego procesu zależy od liczby użytkowników w katalogu i rozmiaru grupy utworzonej w wyniku reguły. Zazwyczaj katalogi z małą liczbą użytkowników będą widzieć zmiany członkostwa w grupie w mniej niż kilka minut. W przypadku katalogów z dużą liczbą użytkowników może upłynąć 30 minut lub dłużej.

**Jak można wymusić przetworzenie grupy teraz?**<br/>
Obecnie nie ma możliwości automatycznego wyzwalania grupy do przetworzenia na żądanie. Można jednak ręcznie wyzwolić ponowne przetwarzanie przez zaktualizowanie reguły członkostwa w celu dodania odstępu na końcu.  

**Wystąpił błąd przetwarzania reguły**<br/>W poniższej tabeli wymieniono typowe błędy reguł członkostwa dynamicznego i sposoby ich korygowania.

| Błąd analizatora reguł | Błąd użycia | Skorygowane użycie |
| --- | --- | --- |
| Błąd: atrybut nie jest obsługiwany. |(User. invalidProperty-EQ "wartość") |(User. Department-EQ "wartość")<br/><br/>Upewnij się, że atrybut znajduje się na [liście obsługiwanych właściwości](groups-dynamic-membership.md#supported-properties). |
| Błąd: operator nie jest obsługiwany dla atrybutu. |(User. accountEnabled-zawiera wartość true) |(User. accountEnabled-EQ true)<br/><br/>Używany operator nie jest obsługiwany w przypadku typu właściwości (w tym przykładzie nie można używać typu-Contains w typie Boolean). Użyj poprawnych operatorów dla typu właściwości. |
| Błąd: błąd kompilacji zapytania. | 1. (User. Department-EQ "Sales") (User. Department-EQ "Marketing")<br>2. (User. userPrincipalName-Match "* @domain.ext ") | 1. brak operatora. Predykaty use-and lub or dwa sprzężenia<br>(User. Department-EQ "Sales") — lub (User. Department-EQ "Marketing")<br>2. błąd w wyrażeniu regularnym używanym z parametrem-Match<br>(User. userPrincipalName-Match ". * @domain.ext ")<br>lub alternatywnie: (User. userPrincipalName-Match " @domain.ext $") |

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)