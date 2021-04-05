---
title: Często zadawane pytania dotyczące Azure Active Directory Connect Health — Azure | Microsoft Docs
description: Te często zadawane pytania zawierają odpowiedzi na pytania dotyczące Azure AD Connect Health. Wśród często zadawanych pytań znajdują się pytania dotyczące korzystania z samej usługi, w tym między innymi na temat modelu rozliczeń, możliwości, ograniczeń i pomocy technicznej.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368000"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące programu Azure Active Directory (Azure AD) Connect Health. W tych często zadawanych pytań zawarto informacje na temat sposobu korzystania z usługi, która obejmuje model rozliczeń, możliwości, ograniczenia i pomoc techniczną.

## <a name="general-questions"></a>Pytania ogólne
**P: zarządzamy wieloma katalogami usługi Azure AD. Jak mogę przełączać się do tego, który ma Azure Active Directory — wersja Premium?**

Aby przełączać się między różnymi dzierżawami usługi Azure AD, wybierz nazwę aktualnie zalogowanego **użytkownika** w prawym górnym rogu, a następnie wybierz odpowiednie konto. Jeśli konta nie ma na liście, wybierz pozycję **Wyloguj**, a następnie Użyj poświadczeń administratora globalnego katalogu, który ma Azure Active Directory — wersja Premium (P1 lub P2), aby się zalogować.

**P: Jakie wersje ról tożsamości są obsługiwane przez Azure AD Connect Health?**

W poniższej tabeli wymieniono role i obsługiwane wersje systemu operacyjnego.

|Rola| System operacyjny/wersja|
|--|--|
|Active Directory Federation Services (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 z dodatkiem R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | Wersja 1.0.9125 lub nowsza|
|Usługi domenowe Active Directory (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 z dodatkiem R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

Instalacje systemu Windows Server Core nie są obsługiwane.

Należy pamiętać, że funkcje udostępniane przez usługę mogą się różnić w zależności od roli i systemu operacyjnego. Innymi słowy, wszystkie funkcje mogą nie być dostępne dla wszystkich wersji systemu operacyjnego. Szczegółowe informacje można znaleźć w opisach funkcji.

**P: ile licencji muszę monitorować moją infrastrukturę?**

* Pierwszy Agent programu Connect Health wymaga co najmniej jednej licencji Azure AD — wersja Premium (P1 lub P2).
* Każdy dodatkowy zarejestrowany agent wymaga 25 dodatkowych licencji Azure AD — wersja Premium (P1 lub P2).
* Liczba agentów jest równoważna z łączną liczbą agentów zarejestrowanych we wszystkich monitorowanych rolach (AD FS, Azure AD Connect i/lub AD DS).
* Licencjonowanie programu AAD Connect Health nie wymaga przypisywania licencji do określonych użytkowników. Musisz mieć tylko wymaganą liczbę prawidłowych licencji.

Informacje o licencjonowaniu znajdują się również na [stronie cennika usługi Azure AD](https://aka.ms/aadpricing).

Przykład:

| Zarejestrowani agenci | Wymagana licencja | Przykładowa konfiguracja monitorowania |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect serwer |
| 2 | 26| 1 Azure AD Connect Server i 1 kontroler domeny |
| 3 | 51 | 1 Active Directory Federation Services (AD FS) serwer, 1 AD FS serwer proxy i 1 kontroler domeny |
| 4 | 76 | 1 AD FS Server, 1 AD FS proxy i 2 kontrolery domeny |
| 5 | 101 | 1 Azure AD Connect Server, 1 AD FS Server, 1 AD FS proxy i 2 kontrolery domeny |

**P: czy Azure AD Connect Health obsługiwać chmurę platformy Azure (Niemcy)?**

Azure AD Connect Health nie jest obsługiwana w chmurze w Niemczech, z wyjątkiem [funkcji raportu błędy synchronizacji](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Role | Funkcje | Obsługiwane w chmurze niemieckiej |
| ------ | --------------- | --- |
| Połącz kondycję z synchronizacją | Monitorowanie/wgląd/alerty/analiza | Nie |
|  | Raport o błędach synchronizacji | Tak |
| Connect Health dla usług AD FS | Monitorowanie/wgląd/alerty/analiza | Nie |
| Podłączanie kondycji dla Dodawanie | Monitorowanie/wgląd/alerty/analiza | Nie |

Aby zapewnić łączność agenta programu Connect Health w celu synchronizacji, należy odpowiednio skonfigurować [wymagania dotyczące instalacji](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) .

## <a name="installation-questions"></a>Pytania dotyczące instalacji

**P: jaki jest wpływ instalowania agenta Azure AD Connect Health na poszczególnych serwerach?**

W przypadku instalowania agenta programu Microsoft Azure AD Connect Health AD FS, serwerów proxy aplikacji sieci Web, serwerów Azure AD Connect (synchronizacji), kontrolery domeny jest minimalny w odniesieniu do procesora CPU, zużycia pamięci, przepustowości sieci i magazynu.

Następujące liczby są przybliżeniem:

* Użycie procesora CPU: ~ 1-5% wzrostu.
* Użycie pamięci: do 10% całkowitej pamięci systemowej.

> [!NOTE]
> Jeśli Agent nie może komunikować się z platformą Azure, Agent przechowuje dane lokalnie ze zdefiniowanym maksymalnym limitem. Agent zastępuje "buforowane" dane na "najmniej niedawno serwisowane".
>
>

* Magazyn lokalnych buforów dla agentów Azure AD Connect Health: ~ 20 MB.
* W przypadku serwerów AD FS zaleca się udostępnienie miejsca na dysku o rozmiarze 1 024 MB (1 GB) dla kanału inspekcji AD FS dla agentów Azure AD Connect Health, aby przetwarzać wszystkie dane inspekcji przed ich zastąpieniem.

**P: czy podczas instalacji agentów Azure AD Connect Health należy uruchomić ponownie moje serwery?**

Nie. Instalacja agentów nie wymaga ponownego uruchomienia serwera. Jednak instalacja niektórych kroków wymagań wstępnych może wymagać ponownego uruchomienia serwera.

Na przykład w systemie Windows Server 2008 R2 instalacja programu .NET 4,5 Framework wymaga ponownego uruchomienia serwera.

**P: czy Azure AD Connect Health pracy przez serwer proxy przekazywania HTTP?**

Tak. W przypadku bieżących operacji można skonfigurować agenta kondycji tak, aby korzystał z serwera proxy HTTP do przesyłania dalej wychodzących żądań HTTP.
Przeczytaj więcej [na temat konfigurowania serwera proxy HTTP dla agentów kondycji](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Jeśli musisz skonfigurować serwer proxy podczas rejestracji agenta, być może musisz wcześniej zmodyfikować ustawienia serwera proxy programu Internet Explorer.

1. Otwórz program Internet Explorer > **Ustawienia**  >  **Opcje internetowe**  >  **połączenia**  >  **sieci LAN**.
2. Wybierz opcję **Użyj serwera proxy dla sieci LAN**.
3. Wybierz opcję **Zaawansowane** , jeśli masz różne porty serwera proxy dla protokołów HTTP i https/Secure.

**P: czy Azure AD Connect Health obsługuje uwierzytelnianie podstawowe podczas nawiązywania połączenia z serwerami proxy HTTP?**

Nie. Mechanizm określania dowolnych nazw użytkowników i haseł uwierzytelniania podstawowego nie jest obecnie obsługiwany.

**P: jakie porty zapory muszę otworzyć, aby Agent Azure AD Connect Health działał prawidłowo?**

Zapoznaj się z [sekcją wymagania](how-to-connect-health-agent-install.md#requirements) , aby zapoznać się z listą portów zapory i innych wymagań dotyczących łączności.

**P: Dlaczego widzę dwa serwery o tej samej nazwie w portalu Azure AD Connect Health?**

Po usunięciu agenta z serwera serwer nie zostanie automatycznie usunięty z portalu Azure AD Connect Health. Po ręcznym usunięciu agenta z serwera lub usunięciu samego serwera należy ręcznie usunąć wpis serwera z portalu Azure AD Connect Health.

Możesz odtworzyć z obrazu serwer lub utworzyć nowy serwer z takimi samymi informacjami (jak nazwa komputera). Jeśli nie usunięto już zarejestrowanego serwera z portalu Azure AD Connect Health, a Agent programu został zainstalowany na nowym serwerze, mogą pojawić się dwa wpisy o tej samej nazwie.

W takim przypadku należy ręcznie usunąć wpis, który należy do starszego serwera. Dane dla tego serwera powinny być nieaktualne.

**P: Czy można zainstalować agenta Azure AD Connect Health w systemie Windows Server Core?**

Nie.  Instalacja w trybie Server Core nie jest obsługiwana.

## <a name="health-agent-registration-and-data-freshness"></a>Rejestracja agenta kondycji i aktualność danych

**P: Jakie są typowe przyczyny błędów rejestracji agenta kondycji i rozwiązywania problemów?**

Rejestracja agenta kondycji może zakończyć się niepowodzeniem ze względu na następujące możliwe przyczyny:

* Agent nie może komunikować się z wymaganymi punktami końcowymi, ponieważ Zapora blokuje ruch. Jest to szczególnie typowe w przypadku serwerów proxy aplikacji sieci Web. Upewnij się, że zezwolono na wychodzącą komunikację z wymaganymi punktami końcowymi i portami. Szczegółowe informacje znajdują się w [sekcji wymagania](how-to-connect-health-agent-install.md#requirements) .
* Komunikacja wychodząca podlega inspekcji protokołu TLS przez warstwę sieciową. Powoduje to, że certyfikat, którego używa Agent do zastąpienia przez serwer inspekcji/jednostkę, i kroki, które należy wykonać, aby zakończyć rejestrację agenta kończy się niepowodzeniem.
* Użytkownik nie ma dostępu do przeprowadzenia rejestracji agenta. Domyślnie Administratorzy globalni mają dostęp. Aby delegować dostęp do innych użytkowników, można użyć [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](how-to-connect-health-operations.md#manage-access-with-azure-rbac) .

**P: otrzymuję alerty informujące o tym, że "Usługa kondycji dane są nieaktualne". Jak mogę rozwiązać ten problem?**

Azure AD Connect Health generuje alert, jeśli nie otrzyma wszystkich punktów danych z serwera w ciągu ostatnich dwóch godzin. [Przeczytaj więcej](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Pytania dotyczące operacji
**P: Czy muszę włączyć inspekcję na serwerach proxy aplikacji sieci Web?**

Nie, nie trzeba włączać inspekcji na serwerach proxy aplikacji sieci Web.

**P: jak alerty programu Azure AD Connect Health zostać rozwiązane?**

Alerty Azure AD Connect Health są rozwiązywane po pomyślnym spełnieniu warunku. Azure AD Connect Health agenci wykrywają i raportują warunki sukcesu usługi okresowo. W przypadku kilku alertów pomijanie jest oparte na czasie. Innymi słowy, jeśli ten sam warunek błędu nie zostanie zaobserwowany w ciągu 72 godzin od wygenerowania alertu, alert zostanie automatycznie rozwiązany.

**Pytanie: otrzymuję alerty informujące o tym, że "testowe żądanie uwierzytelniania (transakcja syntetyczna" nie może uzyskać tokenu "). Jak mogę rozwiązać ten problem?**

Azure AD Connect Health AD FS generuje ten alert, gdy Agent kondycji zainstalowany na serwerze AD FS nie może uzyskać tokenu w ramach transakcji syntetycznej zainicjowanej przez agenta kondycji. Agent kondycji używa kontekstu systemu lokalnego i próbuje uzyskać token dla jednostki uzależnionej. Jest to test "catch-all", aby upewnić się, że AD FS jest w stanie wystawiania tokenów.

Najczęściej ten test nie powiedzie się, ponieważ agent kondycji nie może rozpoznać nazwy farmy AD FS. Może się tak zdarzyć, jeśli serwery AD FS znajdują się za usługą równoważenia obciążenia sieciowego, a żądanie zostanie zainicjowane z węzła, który znajduje się za modułem równoważenia obciążenia (w przeciwieństwie do zwykłego klienta, który jest przed modułem równoważenia obciążenia). Można to naprawić, aktualizując plik "hosts" znajdujący się w lokalizacji "C:\Windows\System32\drivers\etc" w celu uwzględnienia adresu IP serwera AD FS lub adresu IP sprzężenia zwrotnego (127.0.0.1) dla nazwy farmy AD FS (takiej jak sts.contoso.com). Dodanie pliku hosta spowoduje krótki obwód połączenia sieciowego, dzięki czemu Agent kondycji może uzyskać token.

**P: otrzymuję wiadomość e-mail z informacją o tym, że moje maszyny nie są zgodne z poprawkami dla ostatnich ataków z wykorzystaniem oprogramowania wymuszającego okup. Dlaczego otrzymuję tę wiadomość e-mail?**

Usługa Azure AD Connect Health przeskanował wszystkie monitorowane maszyny, aby upewnić się, że zostały zainstalowane wymagane poprawki. Wiadomość e-mail została wysłana do administratorów dzierżawy, jeśli co najmniej jedna maszyna nie ma krytycznych poprawek. W celu dokonania tego ustalenia użyto następującej logiki.
1. Znajdź wszystkie poprawki zainstalowane na komputerze.
2. Sprawdź, czy istnieje co najmniej jedna poprawka ze zdefiniowanej listy.
3. Jeśli tak, komputer jest chroniony. W przeciwnym razie komputer jest narażony na ataki.

Możesz użyć następującego skryptu programu PowerShell, aby przeprowadzić to sprawdzenie ręcznie. Implementuje powyższą logikę.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**P: Dlaczego polecenie cmdlet programu PowerShell <i>Get-MsolDirSyncProvisioningError</i> wyświetla mniejszą ilość błędów synchronizacji w wyniku?**

<i>Get-MsolDirSyncProvisioningError</i> zwróci tylko błędy aprowizacji narzędzia DirSync. Oprócz tego program Connect Health portal wyświetla również inne typy błędów synchronizacji, takie jak błędy eksportu. Jest to zgodne z wynikiem różnic Azure AD Connect. Przeczytaj więcej na temat [Azure AD Connect błędów synchronizacji](./tshoot-connect-sync-errors.md).

**P: Dlaczego nie są generowane żadne inspekcje usług ADFS?**

Użyj polecenia cmdlet <i>Get-ADFSProperties-AuditLevel</i> programu PowerShell, aby upewnić się, że dzienniki inspekcji nie są w stanie wyłączenia. Przeczytaj więcej na temat [dzienników inspekcji usług ADFS](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Zwróć uwagę, jeśli istnieją zaawansowane ustawienia inspekcji wypychane do serwera usług AD FS, wszelkie zmiany z auditpol.exe zostaną zastąpione (zdarzenie, jeśli wygenerowana aplikacja nie jest skonfigurowana). W takim przypadku należy ustawić zasady zabezpieczeń lokalnych, aby rejestrować błędy wygenerowane przez aplikację i powodzenie.

**P: Kiedy certyfikat agenta będzie automatycznie odnawiany przed wygaśnięciem?**
Certyfikacja agenta zostanie automatycznie odnowiona na **sześć miesięcy** przed datą wygaśnięcia. Jeśli nie, upewnij się, że połączenie sieciowe agenta jest stabilne. Należy ponownie uruchomić usługi agenta lub zaktualizować do najnowszej wersji, aby rozwiązać problem.



## <a name="related-links"></a>Linki pokrewne
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalacja agenta Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operacje Azure AD Connect Health](how-to-connect-health-operations.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
