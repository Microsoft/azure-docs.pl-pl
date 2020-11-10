---
title: 'Azure AD Connect: konto usługi ADSync | Microsoft Docs'
description: W tym temacie opisano konto usługi ADSync i przedstawiono najlepsze rozwiązania dotyczące konta.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410899"
---
# <a name="adsync-service-account"></a>Konto usługi ADSync
Azure AD Connect instaluje usługę lokalną, która organizuje synchronizację między Active Directory i Azure Active Directory.  Usługa synchronizacji synchronizacji Microsoft Azure AD (ADSync) jest uruchamiana na serwerze w środowisku lokalnym.  Poświadczenia usługi są domyślnie ustawiane w instalacjach ekspresowych, ale mogą być dostosowane do wymagań bezpieczeństwa organizacji.  Te poświadczenia nie są używane do nawiązywania połączeń z lokalnymi lasami lub Azure Active Directory.

Wybranie konta usługi ADSync to ważna decyzja dotycząca planowania przed zainstalowaniem Azure AD Connect.  Każda próba zmiany poświadczeń po zakończeniu instalacji spowoduje niepowodzenie uruchomienia usługi, utratę dostępu do bazy danych synchronizacji i niepowodzenie uwierzytelniania przy użyciu podłączonych katalogów (platformy Azure i AD DS).  Żadna synchronizacja nie zostanie wykonana do momentu przywrócenia oryginalnych poświadczeń.

## <a name="the-default-adsync-service-account"></a>Domyślne konto usługi ADSync

W przypadku uruchamiania na serwerze członkowskim usługa AdSync jest uruchamiana w kontekście konta usługi wirtualnej (VSA).  Ze względu na ograniczenie produktu podczas instalacji na kontrolerze domeny jest tworzone niestandardowe konto usługi.  Jeśli konto usługi ustawień ekspresowych nie spełnia wymagań zabezpieczeń organizacji, wdróż Azure AD Connect, wybierając opcję Dostosuj.  Następnie wybierz opcję konto usługi, która spełnia wymagania organizacji.

>[!NOTE]
>Domyślne konto usługi zainstalowane na kontrolerze domeny ma postać domena \ AAD_InstallationIdentifier.  Hasło dla tego konta jest generowana losowo i przedstawia istotne wyzwania związane z odzyskiwaniem i rotacją haseł.  Firma Microsoft zaleca dostosowanie konta usługi podczas instalacji początkowej na kontrolerze domeny w celu korzystania z konta usługi zarządzanej autonomicznej lub grupy (autonomiczne/gMSA).

|Lokalizacja Azure AD Connect|Utworzono konto usługi|
|-----|-----|
|Serwer członkowski|SERVICE\ADSync NT|
|Kontroler domeny|Domena \ AAD_74dc30c01e80 (patrz Uwaga)|

## <a name="custom-adsync-service-accounts"></a>Niestandardowe konta usług ADSync
Firma Microsoft zaleca uruchomienie usługi ADSync w kontekście konta usługi wirtualnej lub konta usługi zarządzanej autonomicznej lub grupy.  Administrator domeny może również utworzyć konto usługi, które jest obsługiwane w celu spełnienia określonych wymagań w zakresie zabezpieczeń organizacji.   Aby dostosować konto usługi używane podczas instalacji, wybierz opcję Dostosuj na stronie Ustawienia ekspresowe poniżej.   Dostępne są następujące opcje:

- konto domyślne — Azure AD Connect będzie obsługiwać konto usługi zgodnie z powyższym opisem
- zarządzane konto usługi — Użyj autonomicznej lub współdzielonej grupy MSA obsługiwanej przez administratora
- konto domeny — Użyj konta usługi domeny obsługiwanego przez administratora

![Zrzut ekranu przedstawiający stronę ustawień programu Azure AD Connect Express z przyciskami opcji "Dostosuj" lub "Użyj ustawień ekspresowych".](media/concept-adsync-service-account/adsync1.png)

![Zrzut ekranu przedstawiający stronę Azure AD Connect "Zainstaluj składniki wymagane" z opcją używania istniejącego konta usługi zarządzanej.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnozowanie zmian konta usługi ADSync
Zmiana poświadczeń dla usługi ADSync po zakończeniu instalacji spowoduje, że usługa nie zostanie uruchomiona, utraci dostęp do bazy danych synchronizacji i nie będzie można uwierzytelnić się przy użyciu podłączonych katalogów (platformy Azure i AD DS).  Przyznanie dostępu do bazy danych do nowego konta usługi ADSync jest niewystarczające do odzyskania sprawności po tym problemie. Żadna synchronizacja nie zostanie wykonana do momentu przywrócenia oryginalnych poświadczeń.

Usługa ADSync będzie wystawiał komunikat poziomu błędu w dzienniku zdarzeń, gdy nie można go uruchomić.  Zawartość wiadomości będzie się różnić w zależności od tego, czy jest używana wbudowana baza danych (LocalDB), czy pełna SQL.  Poniżej przedstawiono przykłady wpisów dziennika zdarzeń, które mogą być obecne.

### <a name="example-1"></a>Przykład 1

Nie można odnaleźć kluczy szyfrowania usługi AdSync i zostały one ponownie utworzone.  Synchronizacja nie zostanie przeprowadzona, dopóki ten problem nie zostanie rozwiązany.

Rozwiązywanie problemów z tym problemem, Microsoft Azure AD klucze szyfrowania synchronizacji staną się niedostępne, jeśli zostaną zmienione poświadczenia logowania usługi AdSync.  Jeśli poświadczenia zostały zmienione, użyj aplikacji usług, aby zmienić pierwotne konto logowania z powrotem na jego oryginalnie skonfigurowaną wartość (np. NT SERVICE\AdSync) i uruchom ponownie usługę.  Spowoduje to natychmiastowe przywrócenie prawidłowej operacji usługi AdSync.

Więcej informacji można znaleźć w następującym [artykule](./whatis-hybrid-identity.md) .

### <a name="example-2"></a>Przykład 2

Nie można uruchomić usługi, ponieważ nie można nawiązać połączenia z lokalną bazą danych (LocalDB).

Rozwiązywanie problemów z tym problemem usługa synchronizacji Microsoft Azure AD utraci uprawnienia dostępu do lokalnego dostawcy bazy danych w przypadku zmiany poświadczeń logowania usługi AdSync.  Jeśli poświadczenia zostały zmienione, użyj aplikacji usług, aby zmienić konto logowania z powrotem na oryginalnie skonfigurowaną wartość (np. NT SERVICE\AdSync) i uruchom ponownie usługę.  Spowoduje to natychmiastowe przywrócenie prawidłowej operacji usługi AdSync.

Więcej informacji można znaleźć w następującym [artykule](./whatis-hybrid-identity.md) .

Dostawca zwrócił więcej szczegółowych informacji o następujących informacjach o błędzie:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).