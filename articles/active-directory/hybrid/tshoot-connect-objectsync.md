---
title: 'Azure AD Connect: Rozwiązywanie problemów z synchronizacją obiektów | Microsoft Docs'
description: Ten temat zawiera instrukcje dotyczące rozwiązywania problemów z synchronizacją obiektów przy użyciu zadania rozwiązywania problemów.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85356206"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Troubleshoot object synchronization with Azure AD Connect sync (Rozwiązywanie problemów z synchronizacją obiektów w programie Azure AD Connect)
W tym artykule przedstawiono kroki rozwiązywania problemów z synchronizacją obiektów przy użyciu zadania rozwiązywania problemów. Aby dowiedzieć się, jak działa Rozwiązywanie problemów w programie Azure Active Directory (Azure AD) Connect, Obejrzyj [ten krótki film wideo](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Rozwiązywanie problemów, zadanie
W przypadku wdrożenia usługi Azure AD Connect w wersji 1.1.749.0 lub nowszej użyj zadania rozwiązywania problemów w kreatorze, aby rozwiązać problemy z synchronizacją obiektu. W przypadku wcześniejszych wersji należy ręcznie rozwiązywać problemy zgodnie z opisem w [tym miejscu](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Uruchamianie zadania rozwiązywania problemów w kreatorze
Aby uruchomić zadanie rozwiązywania problemów w kreatorze, wykonaj następujące czynności:

1.  Otwórz nową sesję środowiska Windows PowerShell na serwerze Azure AD Connect przy użyciu opcji Uruchom jako administrator.
2.  Uruchom system `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted` .
3.  Uruchom Kreatora Azure AD Connect.
4.  Przejdź do strony dodatkowe zadania, wybierz pozycję Rozwiązywanie problemów, a następnie kliknij przycisk Dalej.
5.  Na stronie Rozwiązywanie problemów kliknij przycisk Uruchom, aby uruchomić menu Rozwiązywanie problemów w programie PowerShell.
6.  W menu głównym wybierz polecenie Rozwiązywanie problemów z synchronizacją obiektów.
![Rozwiązywanie problemów z synchronizacją obiektów](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Rozwiązywanie problemów z parametrami wejściowymi
Następujące parametry wejściowe są zbędne przez zadanie rozwiązywania problemów:
1.  **Nazwa wyróżniająca obiektu** — jest to nazwa wyróżniająca obiektu, który wymaga rozwiązania do rozwiązywania problemów
2.  **Nazwa łącznika usługi AD** — jest to nazwa lasu usługi AD, w którym znajduje się powyższy obiekt.
3.  Poświadczenia administratora globalnego dzierżawy usługi Azure AD ![](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania rozwiązywania problemów
Zadanie rozwiązywania problemów wykonuje następujące sprawdzenia:

1.  Wykryj niezgodność nazwy UPN, jeśli obiekt jest synchronizowany do Azure Active Directory
2.  Sprawdź, czy obiekt jest filtrowany ze względu na filtrowanie domeny
3.  Sprawdź, czy obiekt jest filtrowany ze względu na filtrowanie jednostek organizacyjnych
4.  Sprawdź, czy synchronizacja obiektów została zablokowana z powodu połączonej skrzynki pocztowej
5. Sprawdź, czy obiekt jest dynamiczną grupą dystrybucyjną, która nie powinna być synchronizowana

W pozostałej części tej sekcji opisano konkretne wyniki, które są zwracane przez zadanie. W każdym przypadku zadanie zawiera analizę, a następnie zalecane akcje do rozwiązania problemu.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Wykryj niezgodność nazwy UPN, jeśli obiekt jest synchronizowany do Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Sufiks nazwy UPN nie został zweryfikowany przy użyciu dzierżawy usługi Azure AD
Gdy nie można zweryfikować sufiksu identyfikatora logowania/Alternate UserPrincipalName (UPN) w dzierżawie usługi Azure AD, a następnie Azure Active Directory zastępuje sufiksy nazw UPN domyślną nazwą domeny "onmicrosoft.com".

![Usługa Azure AD zastępuje nazwę UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Funkcja narzędzia DirSync dzierżawy usługi Azure AD "SynchronizeUpnForManagedUsers" jest wyłączona
Gdy funkcja DirSync "SynchronizeUpnForManagedUsers" dzierżawy usługi Azure AD jest wyłączona, Azure Active Directory nie zezwala na aktualizacje synchronizacji na wartość UserPrincipalName/alternatywny identyfikator logowania dla licencjonowanych kont użytkowników z uwierzytelnianiem zarządzanym.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Obiekt jest filtrowany ze względu na filtrowanie domen
### <a name="domain-is-not-configured-to-sync"></a>Domena nie jest skonfigurowana do synchronizacji
Obiekt jest poza zakresem, ponieważ nie skonfigurowano domeny. W poniższym przykładzie obiekt jest poza zakresem synchronizacji, ponieważ domena, do której należy, jest filtrowany z synchronizacji.

![Domena nie jest skonfigurowana do synchronizacji](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domena jest skonfigurowana do synchronizacji, ale brakuje profili uruchamiania/uruchamiania
Obiekt jest poza zakresem, ponieważ w domenie brakuje profili uruchamiania/uruchamiania. W poniższym przykładzie obiekt jest poza zakresem synchronizacji, ponieważ domena, do której należy, nie zawiera kroków uruchamiania dla pełnego profilu przebiegu importu.
![brakujące profile uruchamiania](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Obiekt jest filtrowany ze względu na filtrowanie jednostek organizacyjnych
Obiekt jest poza zakresem synchronizacji ze względu na konfigurację filtrowania jednostki organizacyjnej. W poniższym przykładzie obiekt należy do jednostki organizacyjnej = nosync, DC = bvtadwbackdc, DC = com.  Ta jednostka organizacyjna nie jest uwzględniona w zakresie synchronizacji.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problem z połączoną skrzynką pocztową
Połączona Skrzynka pocztowa powinna być skojarzona z zewnętrznym kontem głównym znajdującym się w innym lesie zaufanego konta. Jeśli nie ma takiego zewnętrznego konta głównego, Azure AD Connect nie zsynchronizuje konta użytkownika z połączoną skrzynką pocztową w lesie programu Exchange z dzierżawą usługi Azure AD.</br>
![Połączona Skrzynka pocztowa](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problem z dynamiczną grupą dystrybucyjną
Ze względu na różne różnice między lokalnym Active Directory i Azure Active Directory, Azure AD Connect nie synchronizuje dynamicznych grup dystrybucji z dzierżawą usługi Azure AD.

![Dynamiczna grupa dystrybucji](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Raport HTML
Oprócz analizowania obiektu zadanie rozwiązywania problemów generuje również raport HTML, który ma wszystkie znane informacje o obiekcie. Ten raport HTML może być współużytkowany z zespołem pomocy technicznej w celu przeprowadzenia dalszej procedury rozwiązywania problemów, jeśli jest to konieczne.

![Raport HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
