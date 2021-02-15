---
title: Zabezpieczanie autonomicznych zarządzanych kont usług | Azure Active Directory
description: Przewodnik dotyczący zabezpieczania autonomicznych zarządzanych kont usług.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a919338789a02d50cbb6976ee50b214cb0d612
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417473"
---
# <a name="securing-standalone-managed-service-accounts"></a>Zabezpieczanie autonomicznych zarządzanych kont usług

Autonomiczne zarządzane konta usług (sMSAs) są kontami domeny zarządzanej służącymi do zabezpieczenia co najmniej jednej usługi uruchomionej na serwerze. Nie mogą być ponownie używane na wielu serwerach. sMSAs zapewniają automatyczne zarządzanie hasłami, uproszczone zarządzanie nazwami główna usługi (SPN) i możliwość delegowania zarządzania do innych administratorów. 

W Active Directory sMSAs są powiązane z określonym serwerem, na którym działa usługa. Te konta można znaleźć na liście w przystawce Użytkownicy i komputery Active Directory programu Microsoft Management Console.

![Zrzut ekranu przedstawiający przystawkę Użytkownicy i komputery Active Directory z przystawką zarządzane konta usług.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Zarządzane konta usług zostały wprowadzone z systemem Windows Server 2008R2 Active Directory schemacie i wymagają minimalnego poziomu systemu operacyjnego Windows Server 2008R2. 

## <a name="benefits-of-using-smsas"></a>Zalety korzystania z usługi sMSAs

sMSAs oferują większe bezpieczeństwo niż konta użytkowników używane jako konta usług, jednocześnie zmniejszając koszty administracyjne, wykonując następujące czynności:

* Ustawianie silnych haseł. sMSAs używają losowo generowanych haseł złożonych o 240 bajtów. Złożoność i długość haseł autonomiczne minimalizują prawdopodobieństwo naruszenia przez atak z wykorzystaniem rozdzielania lub słowników.

* Regularne cykliczne hasła. System Windows automatycznie zmienia autonomiczne hasła co 30 dni. Administratorzy usługi i domeny nie muszą planować zmian haseł ani zarządzać związanym przestojem.

* Uproszczenie zarządzania nazwami SPN. Nazwy główne usługi są automatycznie aktualizowane, Jeśli poziomem funkcjonalności domeny (DFL) jest system Windows Server 2008 R2. Na przykład nazwa główna usługi jest automatycznie aktualizowana w następujących scenariuszach:

   * Zmieniono nazwę konta komputera hosta. 

   * Nazwa DNS komputera hosta jest zmieniana.

   * Podczas dodawania lub usuwania dodatkowych parametrów AccountName lub DNS-hostname przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-adserviceaccount?view=win10-ps)

## <a name="when-to-use-smsas"></a>Kiedy używać sMSAs

sMSAs może uprościć zadania związane z zarządzaniem i zabezpieczeniami. Użyj sMSAs, jeśli jedna lub więcej usług zostało wdrożonych na jednym serwerze i nie można użyć gMSA. 

> [!NOTE] 
> Chociaż można używać sMSAs dla więcej niż jednej usługi, zalecamy, aby każda usługa miała własną tożsamość na potrzeby inspekcji. 

Jeśli twórca oprogramowania nie powiedzie się, jeśli może korzystać z elementu MSA, należy przetestować aplikację. W tym celu należy utworzyć środowisko testowe i upewnić się, że będzie ono miało dostęp do wszystkich wymaganych zasobów. Zobacz [Tworzenie i Instalowanie autonomiczne](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) , aby uzyskać instrukcje krok po kroku.

### <a name="assess-security-posture-of-smsas"></a>Ocena zabezpieczeń stan sMSAs

sMSAs są z natury bezpieczniejsze niż konta użytkowników standardowych, które wymagają stałego zarządzania hasłami. Należy jednak wziąć pod uwagę zakres dostępu sMSAs "w ramach ogólnego stan zabezpieczeń.

W poniższej tabeli przedstawiono sposób ograniczania potencjalnych problemów z zabezpieczeniami powodowanych przez sMSAs.

| Problemy z zabezpieczeniami| Środki zaradcze |
| - | - |
| Autonomiczne jest członkiem uprzywilejowanych grup|Usuń autonomiczne z grup uprzywilejowanych z podwyższonym poziomem uprawnień (takich jak Administratorzy domeny). <br> Użyj najmniej uprzywilejowanego modelu i Udziel autonomiczne tylko praw i uprawnień wymaganych do uruchomienia jej usług. <br> Jeśli nie masz pewności, jakie są wymagane uprawnienia, zapoznaj się z twórcą usługi. |
| Autonomiczne ma dostęp do odczytu i zapisu do poufnych zasobów.|Inspekcja dostępu do poufnych zasobów. Archiwizuj dzienniki inspekcji w usłudze SIEM (Azure Log Analytics lub na platformie Azure wskaźnikowej) na potrzeby analizy. <br> Koryguj uprawnienia zasobów w przypadku wykrycia niepożądanego poziomu dostępu. |
| Domyślnie częstotliwość przerzucania haseł autonomiczne wynosi 30 dni| Zasady grupy mogą służyć do dostrajania czasu trwania w zależności od wymagań dotyczących zabezpieczeń przedsiębiorstwa. <br> * Limit czasu wygaśnięcia hasła można ustawić przy użyciu następującej ścieżki. <br>Computer komputera\Zasady\Ustawienia Windows\Ustawienia zabezpieczeń Options\Domain member: maksymalny wiek hasła konta komputera |



### <a name="challenges-with-smsas"></a>Wyzwania z sMSAs

Problemy związane z usługą sMSAs są następujące:

| Wyzwania| Środki zaradcze |
| - | - |
| Mogą one być używane na jednym serwerze.| Użyj kont gMSA, jeśli chcesz użyć konta na różnych serwerach. |
| Nie mogą być używane między domenami.| Użyj kont gMSA, jeśli chcesz użyć konta w różnych domenach. |
| Nie wszystkie aplikacje obsługują sMSAs.| Użyj kont gMSA, jeśli to możliwe. Jeśli nie należy używać konta użytkownika standardowego ani konta komputera zgodnie z zaleceniem twórcy aplikacji. |


## <a name="find-smsas"></a>Znajdź sMSAs

Na dowolnym kontrolerze domeny Uruchom program DSA. msc i rozwiń kontener zarządzane konta usług, aby wyświetlić wszystkie sMSAs. 

Następujące polecenie programu PowerShell zwraca wszystkie sMSAs i kont gMSA w domenie Active Directory. 

`Get-ADServiceAccount -Filter *`

Następujące polecenie zwraca tylko sMSAs w domenie Active Directory.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Zarządzanie sMSAs

Do zarządzania sMSAs można użyć Active Directory następujących poleceń cmdlet programu PowerShell:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Przenieś do sMSAs

Jeśli usługa aplikacji obsługuje autonomiczne, ale nie kont gMSA, i aktualnie używa konta użytkownika lub konta komputera dla kontekstu zabezpieczeń, [Utwórz i zainstaluj autonomiczne](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) na serwerze. 

W idealnym przypadku należy przenieść zasoby na platformę Azure i użyć tożsamości zarządzanych przez platformę Azure lub jednostek usługi.

 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania kont usługi

* [Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)

* [Bezpieczne konta usług zarządzane przez grupę](service-accounts-group-managed.md)

* [Zabezpieczone autonomicznie zarządzane konta usług](service-accounts-standalone-managed.md)

* [Bezpieczne konta komputerów](service-accounts-computer.md)

* [Zabezpieczanie kont użytkowników](service-accounts-user-on-premises.md)

* [Zarządzanie kontami lokalnych usług](service-accounts-govern-on-premises.md)

 
