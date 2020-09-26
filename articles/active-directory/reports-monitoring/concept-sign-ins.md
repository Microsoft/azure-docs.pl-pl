---
title: Raporty dotyczące logowań w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących logowań w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2791b62d8abb1ade199ae3eb72deaf4db56f3725
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361300"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Architektura raportowania w Azure Active Directory (Azure AD) składa się z następujących składników:

- **Działanie** 
    - **Logowania** — informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - **Dzienniki inspekcji**  -  [Dzienniki inspekcji](concept-audit-logs.md) zapewniają informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzanych aplikacji i działań związanych z katalogiem.
- **Zabezpieczenia** 
    - **Ryzykowne logowania** — [ryzykowne logowanie](../identity-protection/overview-identity-protection.md) jest wskaźnikiem próby logowania przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani do ryzyka** — [ryzykowny użytkownik](../identity-protection/overview-identity-protection.md) jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Ten artykuł zawiera omówienie raportu logowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto może uzyskać dostęp do danych?

* Użytkownicy w roli administrator zabezpieczeń, czytelnik zabezpieczeń, czytelnik globalny i role czytelnika raportu
* Administratorzy globalni
* Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

Raport dotyczący działań związanych z logowaniem jest dostępny we [wszystkich wersjach usługi Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) i można go również uzyskać za pomocą interfejsu API Microsoft Graph.

## <a name="sign-ins-report"></a>Raport dotyczący logowań

Raport logowania użytkownika zawiera odpowiedzi na następujące pytania:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**lub wyszukaj i wybierz pozycję **Azure Active Directory** z dowolnej strony.

![Wybierz Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

W obszarze **monitorowanie**wybierz pozycję **logowania** , aby otworzyć [raport logowania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Aktywność logowania](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aktywność związana z logowaniem")

Wyświetlanie rekordów logowania w portalu może potrwać do dwóch godzin.

> [!IMPORTANT]
> Raport logowania zawiera tylko logowania **interaktywne** , czyli logowania, w przypadku których użytkownik ręcznie loguje się przy użyciu nazwy użytkownika i hasła. Nieinteraktywne logowania, takie jak uwierzytelnianie między usługami, nie są wyświetlane w raporcie logowania. 

Domyślny widok listy dziennika logowań pokazuje następujące dane:

- Data logowania
- Powiązany użytkownik
- Aplikacja, do której użytkownik zalogował się
- Stan logowania
- Stan wykrywania ryzyka
- Stan wymagania dotyczącego uwierzytelniania wieloskładnikowego (MFA)

![Aktywność logowania](./media/concept-sign-ins/sign-in-activity.png "Aktywność związana z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Aktywność logowania](./media/concept-sign-ins/19.png "Aktywność związana z logowaniem")

Okno dialogowe **kolumny** umożliwia dostęp do atrybutów, które można wybrać. W raporcie logowania nie można mieć pól, które mają więcej niż jedną wartość dla danego żądania logowania jako kolumny. Dotyczy to na przykład wartości true dla szczegółów uwierzytelniania, danych dostępu warunkowego i lokalizacji sieciowej.   

![Aktywność logowania](./media/concept-sign-ins/columns.png "Aktywność związana z logowaniem")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Aktywność logowania](./media/concept-sign-ins/basic-sign-in.png "Aktywność związana z logowaniem")

> [!NOTE]
> Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego przez wszystkie raporty logowania. Klikając kartę **dostęp warunkowy** dla rekordu logowania, klienci mogą sprawdzić stan dostępu warunkowego i szczegółowe w celu uzyskania szczegółowych informacji dotyczących zasad, które zastosowały się do logowania, oraz wyników dla każdej zasady.
> Aby uzyskać więcej informacji, zapoznaj [się z często zadawanymi pytaniami dotyczącymi informacji o urzędzie certyfikacji we wszystkich logowaniach](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Najpierw Zawężanie danych raportowanych do poziomu, który się do Ciebie sprawdza. Następnie należy odfiltrować dane logowania przy użyciu pola daty jako domyślnego filtru. Usługa Azure AD udostępnia szeroką gamę dodatkowych filtrów, które można ustawić:

![Aktywność logowania](./media/concept-sign-ins/04.png "Aktywność związana z logowaniem")

**Identyfikator żądania** — identyfikator żądanego obiektu.

**Użytkownik** — nazwa lub główna nazwa użytkownika (UPN) użytkownika, którego potrzebujesz.

**Aplikacja** — nazwa aplikacji docelowej.
 
**Status** — zapoznaj się z informacjami o stanie logowania:

- Powodzenie

- Niepowodzenie

- Działania


**Adres IP** — adres IP urządzenia służącego do nawiązywania połączenia z dzierżawcą.

**Lokalizacja** — lokalizacja, z której zainicjowano połączenie:

- City (Miasto)

- Województwo/Województwo

- Kraj/region


**Zasób** — nazwa usługi używanej do logowania.


**Identyfikator zasobu** — identyfikator usługi używanej do logowania.


**Aplikacja kliencka** — typ aplikacji klienckiej używanej do nawiązywania połączenia z dzierżawcą:

![Filtr aplikacji klienta](./media/concept-sign-ins/client-app-filter.png)


|Nazwa|Nowoczesne uwierzytelnianie|Opis|
|---|:-:|---|
|Uwierzytelniony protokół SMTP| |Używany przez POP i klienta IMAP do wysyłania wiadomości e-mail.|
|Automatyczne| |Używane przez klientów programu Outlook i EAS do znajdowania skrzynek pocztowych w usłudze Exchange Online i łączenia się z nimi.|
|Exchange ActiveSync| |Ten filtr przedstawia wszystkie próby logowania, w przypadku których podjęto próbę wykonania protokołu EAS.|
|Przeglądarka|![Niebieski znacznik wyboru.](./media/concept-sign-ins/check.png)|Pokazuje wszystkie próby logowania od użytkowników przy użyciu przeglądarek sieci Web|
|Exchange ActiveSync| | Pokazuje wszystkie próby logowania użytkowników z aplikacjami klienckimi przy użyciu programu Exchange ActiveSync w celu nawiązania połączenia z usługą Exchange Online|
|Exchange Online PowerShell| |Służy do nawiązywania połączenia z usługą Exchange Online przy użyciu zdalnego programu PowerShell. Jeśli zablokujesz uwierzytelnianie podstawowe dla programu Exchange Online PowerShell, musisz użyć modułu programu PowerShell w usłudze Exchange Online, aby nawiązać połączenie. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z programem Exchange Online PowerShell przy użyciu uwierzytelniania wieloskładnikowego](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange Web Services| |Interfejs programowania używany przez program Outlook, program Outlook dla komputerów Mac i aplikacje innych firm.|
|PROTOKOŁÓW| |Starsza wersja klienta poczty używającej protokołu IMAP do pobierania poczty e-mail.|
|Interfejs MAPI przez HTTP| |Używany przez program Outlook 2010 i nowsze.|
|Aplikacje mobilne i klienci stacjonarni|![Niebieski znacznik wyboru.](./media/concept-sign-ins/check.png)|Pokazuje wszystkie próby logowania użytkowników korzystających z aplikacji mobilnych i klientów stacjonarnych.|
|Książka adresowa w trybie offline| |Kopia kolekcji listy adresów, które są pobierane i używane przez program Outlook.|
|Outlook w dowolnym miejscu (RPC przez HTTP)| |Używane w programie Outlook 2016 i jego wcześniejszych wersjach.|
|Usługa programu Outlook| |Używany przez aplikację poczty i kalendarza dla systemu Windows 10.|
|POP3| |Starsza wersja klienta poczty używającej protokołu POP3 do pobierania poczty e-mail.|
|Usługi sieci Web raportowania| |Służy do pobierania danych raportu w usłudze Exchange Online.|
|Inni klienci| |Pokazuje wszystkie próby logowania od użytkowników, w których aplikacja kliencka nie jest uwzględniona lub nieznana.|



**System operacyjny** — system operacyjny uruchomiony na urządzeniu, na którym jest używane logowanie do dzierżawy. 


**Przeglądarka urządzenia** — Jeśli połączenie zostało zainicjowane z przeglądarki, to pole umożliwia filtrowanie według nazwy przeglądarki.


**Identyfikator korelacji** — identyfikator korelacji działania.




**Dostęp warunkowy** — stan zastosowanych reguł dostępu warunkowego

- **Nie zastosowano**: podczas logowania nie są stosowane żadne zasady dotyczące użytkownika i aplikacji.

- **Sukces**: co najmniej jedna zasada dostępu warunkowego stosowana do użytkownika i aplikacji (ale nie jest to inne warunki) podczas logowania. 

- **Niepowodzenie**: zalogowanie zostało spełnione warunek użytkownika i aplikacji co najmniej jednej zasady dostępu warunkowego, a kontrolki grantu nie są spełnione lub ustawione na zablokowanie dostępu.









## <a name="download-sign-in-activities"></a>Pobieranie działań związanych z logowaniem

Kliknij opcję **Pobierz** , aby utworzyć plik CSV lub kod JSON z najnowszych rekordów 250 000. Rozpocznij od [pobrania danych logowania,](quickstart-download-sign-in-report.md) Jeśli chcesz korzystać z nich poza Azure Portal.  

![Pobieranie](./media/concept-sign-ins/71.png "Pobierz")

> [!IMPORTANT]
> Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania raportów Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Skróty danych logowania

Usługa Azure AD i Azure Portal udostępniają dodatkowe punkty wejścia do danych logowania:

- Omówienie ochrony tożsamości
- Użytkownicy
- Grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Użytkownicy logują się do danych w usłudze Identity Security Protection

Na stronie Omówienie usługi **Identity Security Protection** Graf logowania użytkownika przedstawia tygodniowe agregacje logowania. Wartością domyślną okresu jest 30 dni.

![Aktywność logowania](./media/concept-sign-ins/06.png "Aktywność związana z logowaniem")

Po kliknięciu dnia na wykresie logowań zostanie wyświetlony przegląd działań logowania dla tego dnia.

Każdy wiersz na liście działań logowania określa następujące informacje:

* Kto się zalogował?
* Do której aplikacji się logowano?
* Jaki jest stan logowania?
* Jaki jest stan uwierzytelniania wieloskładnikowego dla logowania?

Klikając pozycję, można uzyskać więcej szczegółowych informacji na temat operacji logowania:

- Identyfikator użytkownika
- Użytkownik
- Nazwa użytkownika
- Identyfikator aplikacji
- Aplikacja
- Klient
- Lokalizacja
- Adres IP
- Data
- Wymagane uwierzytelnianie wieloskładnikowe
- Stan logowania

> [!NOTE]
> Adresy IP są wystawiane w taki sposób, że nie istnieje ostateczne połączenie między adresem IP i lokalizacją, w której komputer z tym adresem jest fizycznie zlokalizowany. Mapowanie adresów IP jest skomplikowane przez fakt, że dostawcy urządzeń przenośnych i sieci VPN wystawiają adresy IP z pul centralnych, które często są bardzo daleko od miejsca użycia urządzenia klienckiego. Obecnie w raportach usługi Azure AD konwertowanie adresu IP na lokalizację fizyczną jest najlepszym nakładem pracy na podstawie śladów, danych rejestru, wyszukiwania wstecznego i innych informacji.

Na stronie **Użytkownicy** znajduje się pełny przegląd wszystkich logowań użytkowników dostępny po kliknięciu pozycji **Logowania** w sekcji **Działanie**.

![Aktywność logowania](./media/concept-sign-ins/08.png "Aktywność związana z logowaniem")

## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Co to są trzy najpopularniejsze aplikacje w organizacji?
* Jak działa moja najnowsza aplikacja?

Punkt wejścia do tych danych to trzy pierwsze aplikacje w organizacji. Dane są zawarte w raporcie z ostatnich 30 dni w sekcji **Przegląd** w obszarze **aplikacje dla przedsiębiorstw**.

![Aktywność logowania](./media/concept-sign-ins/10.png "Aktywność związana z logowaniem")

Wykresy użycia aplikacji dotyczą tygodniowego agregacji logowań dla najpopularniejszych trzech aplikacji w danym okresie. Domyślny okres to 30 dni.

![Aktywność logowania](./media/concept-sign-ins/graph-chart.png "Aktywność związana z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.

![Raportowanie](./media/concept-sign-ins/single-app-usage-graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.

Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 dzienników aktywności

Microsoft 365 dzienników aktywności można wyświetlić w [centrum administracyjnym Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Należy wziąć pod uwagę, że Microsoft 365 aktywność i dzienniki aktywności usługi Azure AD dzielą znaczną liczbę zasobów katalogu. Tylko Microsoft 365 centrum administracyjnego zapewnia pełny wgląd w Microsoft 365 dzienników aktywności. 

Możesz również uzyskać dostęp do dzienników aktywności Microsoft 365 programowo przy użyciu [interfejsów API zarządzania pakietu Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Następne kroki

* [Kody błędów raportów działań związanych z logowaniem](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych usługi Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów usługi Azure AD](reference-reports-latencies.md)