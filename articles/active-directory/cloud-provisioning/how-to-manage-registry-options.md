---
title: 'Azure AD Connect agenta aprowizacji w chmurze: Zarządzanie opcjami rejestru | Microsoft Docs'
description: W tym artykule opisano sposób zarządzania opcjami rejestru w Azure AD Connect agenta aprowizacji w chmurze.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371137"
---
# <a name="manage-agent-registry-options"></a>Zarządzanie opcjami rejestru agentów

W tej sekcji opisano opcje rejestru, które można ustawić, aby kontrolować zachowanie przetwarzania w czasie wykonywania Azure AD Connect agenta aprowizacji. 

## <a name="configure-ldap-connection-timeout"></a>Skonfiguruj limit czasu połączenia LDAP
Podczas wykonywania operacji LDAP na skonfigurowanych Active Directory kontrolerach domeny domyślnie Agent aprowizacji używa domyślnej wartości limitu czasu połączenia wynoszącej 30 sekund. Jeśli Twój kontroler domeny potrzebuje więcej czasu na odpowiedź, w pliku dziennika agenta może zostać wyświetlony następujący komunikat o błędzie: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Operacje wyszukiwania LDAP mogą trwać dłużej, jeśli atrybut Search nie jest indeksowany. W pierwszym kroku, jeśli zostanie pobrany powyższy błąd, należy najpierw sprawdzić, czy atrybut Search/Lookup jest [indeksowany](https://docs.microsoft.com/windows/win32/ad/indexed-attributes). Jeśli atrybuty wyszukiwania są indeksowane, a błąd będzie nadal występował, można zwiększyć limit czasu połączenia LDAP, wykonując następujące czynności: 

1. Zaloguj się jako administrator na serwerze z systemem Windows, na którym jest uruchomiony agent aprowizacji Azure AD Connect.
1. Użyj elementu menu *Uruchom* , aby otworzyć Edytor rejestru (regedit.exe) 
1. Zlokalizuj folder klucza **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Kliknij prawym przyciskiem myszy i wybierz pozycję "Nowa > wartość ciągu".
1. Podaj nazwę: `LdapConnectionTimeoutInMilliseconds`
1. Kliknij dwukrotnie **nazwę wartości** , a następnie wprowadź dane wartości jako `60000` milisekundy.
    > [!div class="mx-imgBorder"]
    > ![Limit czasu połączenia LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Uruchom ponownie usługę aprowizacji Azure AD Connect z poziomu konsoli *usługi* .
1. Jeśli wdrożono wielu agentów aprowizacji, Zastosuj tę zmianę w rejestrze do wszystkich agentów w celu zapewnienia spójności. 

## <a name="configure-referral-chasing"></a>Konfigurowanie kartach odwołań
Domyślnie agent aprowizacji Azure AD Connect nie ma [odwołań](https://docs.microsoft.com/windows/win32/ad/referrals). Możesz włączyć kartach odwołań, aby obsługiwać niektóre scenariusze aprowizacji ruchu przychodzącego w usłudze HR, takie jak: 
* Sprawdzanie unikatowości nazw UPN w wielu domenach
* Rozpoznawanie odwołań między różnymi domenami

Aby włączyć kartach odwołań, wykonaj następujące kroki:

1. Zaloguj się jako administrator na serwerze z systemem Windows, na którym jest uruchomiony agent aprowizacji Azure AD Connect.
1. Użyj elementu menu *Uruchom* , aby otworzyć Edytor rejestru (regedit.exe) 
1. Zlokalizuj folder klucza **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Kliknij prawym przyciskiem myszy i wybierz pozycję "Nowa > wartość ciągu".
1. Podaj nazwę: `ReferralChasingOptions`
1. Kliknij dwukrotnie **nazwę wartości** , a następnie wprowadź dane wartości jako `96` . Ta wartość odnosi się do stałej wartości dla `ReferralChasingOptions.All` i określa, że zarówno w poddrzewach, jak i na poziomie podstawowym następuje Agent. 
    > [!div class="mx-imgBorder"]
    > ![Kartach odwołań](media/how-to-manage-registry-options/referral-chasing.png)
1. Uruchom ponownie usługę aprowizacji Azure AD Connect z poziomu konsoli *usługi* .
1. Jeśli wdrożono wielu agentów aprowizacji, Zastosuj tę zmianę w rejestrze do wszystkich agentów w celu zapewnienia spójności.

> [!NOTE]
> Można potwierdzić, że opcje rejestru zostały ustawione przez włączenie [pełnego rejestrowania](how-to-troubleshoot.md#log-files). Dzienniki emitowane podczas uruchamiania agenta będą wyświetlały wartości konfiguracyjne pobrane z rejestru. 

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)

