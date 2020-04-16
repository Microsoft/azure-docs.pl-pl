---
title: 'Usługa Azure AD Connect: rozwiązywanie problemów z synchronizacją obiektów | Dokumenty firmy Microsoft'
description: W tym temacie przedstawiono kroki dotyczące rozwiązywania problemów z synchronizacją obiektów przy użyciu zadania rozwiązywania problemów.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e10d00ed90248319801974c7c1e7fadf835024b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407016"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Troubleshoot object synchronization with Azure AD Connect sync (Rozwiązywanie problemów z synchronizacją obiektów w programie Azure AD Connect)
Ten artykuł zawiera kroki rozwiązywania problemów z synchronizacją obiektów przy użyciu zadania rozwiązywania problemów. Aby zobaczyć, jak działa rozwiązywanie problemów w usłudze Azure Active Directory (Azure AD) Connect, obejrzyj [ten krótki klip wideo](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Rozwiązywanie problemów z zadaniem
W przypadku wdrożenia usługi Azure AD Connect w wersji 1.1.749.0 lub nowszej użyj zadania rozwiązywania problemów w kreatorze, aby rozwiązać problemy z synchronizacją obiektu. W przypadku wcześniejszych wersji należy rozwiązywać problemy ręcznie, jak opisano [w tym miejscu.](tshoot-connect-object-not-syncing.md)

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Uruchamianie zadania rozwiązywania problemów w kreatorze
Aby uruchomić zadanie rozwiązywania problemów w kreatorze, wykonaj następujące czynności:

1.  Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect z opcją Uruchom jako administrator.
2.  Uruchom `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`lub .
3.  Uruchom kreatora usługi Azure AD Connect.
4.  Przejdź do strony Zadania dodatkowe, wybierz pozycję Rozwiązywanie problemów i kliknij przycisk Dalej.
5.  Na stronie Rozwiązywanie problemów kliknij przycisk Uruchom, aby uruchomić menu rozwiązywania problemów w programie PowerShell.
6.  W menu głównym wybierz pozycję Rozwiązywanie problemów z synchronizacją obiektów.
![Rozwiązywanie problemów z synchronizacją obiektów](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Rozwiązywanie problemów z parametrami wejściowymi
Zadanie rozwiązywania problemów wymaga następujących parametrów wejściowych:
1.  **Nazwa wyróżniająca obiekt** — jest to nazwa wyróżniająca obiektu, który wymaga rozwiązywania problemów
2.  **Nazwa łącznika usługi AD** — jest to nazwa lasu usługi AD, w którym znajduje się powyższy obiekt.
3.  Poświadczeń administratora globalnego dzierżawy ![usługi Azure AD poświadczenia administratora globalnego](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Opis wyników zadania rozwiązywania problemów
Zadanie rozwiązywania problemów wykonuje następujące kontrole:

1.  Wykrywanie niezgodności z usługą UPN, jeśli obiekt jest synchronizowany z usługą Azure Active Directory
2.  Sprawdzanie, czy obiekt jest filtrowany z powodu filtrowania domeny
3.  Sprawdź, czy obiekt jest filtrowany z powodu filtrowania ou
4.  Sprawdzanie, czy synchronizacja obiektów jest zablokowana z powodu połączonej skrzynki pocztowej
5. Sprawdź, czy obiekt jest dynamiczną grupą dystrybucyjną, która nie powinna być synchronizowana

W pozostałej części tej sekcji opisano konkretne wyniki, które są zwracane przez zadanie. W każdym przypadku zadanie zawiera analizę, a następnie zalecane akcje, aby rozwiązać problem.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Wykrywanie niezgodności z usługą UPN, jeśli obiekt jest synchronizowany z usługą Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Sufiks upn nie jest weryfikowany za pomocą dzierżawy usługi Azure AD
Gdy sufiks identyfikatora logowania użytkownika (UPN)/alternatywnego identyfikatora logowania nie jest weryfikowany za pomocą dzierżawy usługi Azure AD, usługa Azure Active Directory zastępuje sufiksy nazwy UPN domyślną nazwą domeny "onmicrosoft.com".

![Usługa Azure AD zastępuje usługę UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Funkcja dirsync dzierżawy usługi Azure AD "SynchronizeUpnForManagedUsers" jest wyłączona
Gdy funkcja dirsync dzierżawy usługi Azure AD "SynchronizeUpnForManagedUsers" jest wyłączona, usługa Azure Active Directory nie zezwala na aktualizacje synchronizacji identyfikatora userPrincipalName/Alternate Login ID dla licencjonowanych kont użytkowników z uwierzytelnianiem zarządzanym.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Obiekt jest filtrowany z powodu filtrowania domeny
### <a name="domain-is-not-configured-to-sync"></a>Domena nie jest skonfigurowana do synchronizacji
Obiekt jest poza zakresem z powodu domeny nie jest skonfigurowany. W poniższym przykładzie obiekt jest poza zakresem synchronizacji, ponieważ domena, do której należy, jest filtrowana z synchronizacji.

![Domena nie jest skonfigurowana do synchronizacji](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domena jest skonfigurowana do synchronizacji, ale brakuje profili uruchamiania/wykonywania kroków
Obiekt jest poza zakresem, ponieważ w domenie brakuje uruchamiania profilów/kroków uruchamiania. W poniższym przykładzie obiekt jest poza zakresem synchronizacji, ponieważ domeny, do której należy, brakuje kroków uruchamiania dla profilu pełnego importu.
![brak profili przebiegu](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Obiekt jest filtrowany z powodu filtrowania ou
Obiekt jest poza zakresem synchronizacji ze względu na konfigurację filtrowania o uj. W poniższym przykładzie obiekt należy do OU=NoSync,DC=bvtadwbackdc,DC=com.  Ta ou nie jest uwzględniona w zakresie synchronizacji.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problem z połączonym skrzynką pocztową
Połączona skrzynka pocztowa ma być skojarzona z zewnętrznym kontem głównym znajdującym się w innym lesie zaufanych kont. Jeśli nie ma takiego zewnętrznego konta głównego, usługa Azure AD Connect nie zsynchronizuje konta użytkownika odpowiadającego połączonej skrzynce pocztowej w lesie programu Exchange dzierżawie usługi Azure AD.</br>
![Połączona skrzynka pocztowa](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problem dynamicznej grupy dystrybucyjnej
Ze względu na różne różnice między lokalną usługą Active Directory a usługą Azure Active Directory usługa Azure AD Connect nie synchronizuje dynamicznych grup dystrybucyjnych z dzierżawą usługi Azure AD.

![Dynamiczna grupa dystrybucyjna](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Raport HTML
Oprócz analizowania obiektu zadanie rozwiązywania problemów generuje również raport HTML, który zawiera wszystko, co wiadomo o obiekcie. Ten raport HTML można udostępnić zespołowi pomocy technicznej w celu dalszego rozwiązywania problemów, jeśli to konieczne.

![Raport HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
