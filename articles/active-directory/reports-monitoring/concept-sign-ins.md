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
ms.date: 04/16/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f1f27cb087dc83295dddade4c0fca551a0d9c9
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589690"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Portal Azure Active Directory zapewnia dostęp do trzech dzienników aktywności:

- **Logowania — informacje** na temat logowania i sposobu, w jaki zasoby są używane przez użytkowników.
- **[Inspekcja](concept-audit-logs.md)** — informacje o zmianach zastosowanych do dzierżawy, takich jak zarządzanie użytkownikami i grupą lub aktualizacje zastosowane do zasobów dzierżawy.
- **[Provisioning](concept-provisioning-logs.md)** — działania wykonywane przez usługę aprowizowania, takie jak tworzenie grupy w usłudze ServiceNow lub użytkownik zaimportowany z usługi Workday.

Ten artykuł zawiera omówienie raportu logowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto może uzyskać dostęp do danych?

* Użytkownicy z rolami Administrator zabezpieczeń, Czytelnik zabezpieczeń, Czytelnik globalny i Czytelnik raportów
* Administratorzy globalni
* Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

Raport działań logowania jest dostępny we wszystkich [wersjach](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) usługi Azure AD i można do niego uzyskać dostęp za pośrednictwem Microsoft Graph API.

## <a name="sign-ins-report"></a>Raport dotyczący logowań

Raport logowania użytkownika zawiera odpowiedzi na następujące pytania:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

W [menu Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory** lub wyszukaj i **wybierz Azure Active Directory** na dowolnej stronie.

![Wybierz Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

W **obszarze** Monitorowanie wybierz **pozycję Logowania,** aby otworzyć raport [Logowania.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

![Zrzut ekranu przedstawia logowania wybrane z menu Monitorowanie.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aktywność związana z logowaniem")

Wyświetlanie niektórych rekordów logowania w portalu może potrwać do dwóch godzin.

> [!IMPORTANT]
> Raport logowania zawiera tylko logowania  interakcyjne, czyli logowania, w których użytkownik loguje się ręcznie przy użyciu nazwy użytkownika i hasła. Logowania nieinterakcyjne, takie jak uwierzytelnianie typu usługa-usługa, nie są wyświetlane w raporcie logowania. 

Domyślny widok listy dziennika logowań pokazuje następujące dane:

- Data logowania
- Powiązany użytkownik
- Aplikacja, do których zalogował się użytkownik
- Stan logowania
- Stan wykrywania ryzyka
- Stan wymagania dotyczącego uwierzytelniania wieloskładnikowego (MFA)

![Zrzut ekranu przedstawia logowania do usługi SharePoint Online w usłudze Office 365.](./media/concept-sign-ins/sign-in-activity.png "Aktywność związana z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Zrzut ekranu przedstawia opcję Kolumny na stronie Logowania.](./media/concept-sign-ins/19.png "Aktywność związana z logowaniem")

Okno **dialogowe** Kolumny zapewnia dostęp do atrybutów, które można wybrać. W raporcie logowania nie można mieć w kolumnie pól, które mają więcej niż jedną wartość dla danego żądania logowania. Dotyczy to na przykład szczegółów uwierzytelniania, danych dostępu warunkowego i lokalizacji sieciowej.   

![Zrzut ekranu przedstawia okno dialogowe Kolumny, w którym można wybrać atrybuty.](./media/concept-sign-ins/columns.png "Aktywność związana z logowaniem")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Zrzut ekranu przedstawia szczegółowy widok informacji.](./media/concept-sign-ins/basic-sign-in.png "Aktywność związana z logowaniem")

> [!NOTE]
> Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego za pośrednictwem wszystkich raportów logowania. Klikając kartę **Dostęp** warunkowy dla rekordu logowania, klienci mogą przejrzeć stan dostępu warunkowego i zapoznać się ze szczegółami zasad zastosowanymi do logowania oraz wynikami poszczególnych zasad.
> Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące informacji o urzędu [certyfikacji we wszystkich logowaniach.](reports-faq.md#conditional-access)


## <a name="sign-in-error-code"></a>Kod błędu logowania

Jeśli logowanie nie powiodło się, możesz uzyskać więcej informacji na temat przyczyny w sekcji Podstawowe **informacje** powiązanego elementu dziennika. 

![kod błędu logowania](./media/concept-all-sign-ins/error-code.png)
 
Chociaż element dziennika zawiera przyczynę niepowodzenia, istnieją przypadki, w których można uzyskać więcej informacji za pomocą narzędzia wyszukiwania błędów [logowania](https://login.microsoftonline.com/error). Na przykład jeśli jest dostępne, to narzędzie udostępnia kroki korygowania.  

![Narzędzie wyszukiwania kodu błędu](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities&quot;></a>Filtrowanie działań związanych z logowaniem

Najpierw zawęzij zgłoszone dane do poziomu, który będzie dla Ciebie pasować. Po drugie przefiltruj dane logowania przy użyciu pola daty jako filtru domyślnego. Usługa Azure AD udostępnia szeroką gamę dodatkowych filtrów, które można ustawić:

![Zrzut ekranu przedstawia opcję Dodaj filtry.](./media/concept-sign-ins/04.png &quot;Aktywność związana z logowaniem")

**Identyfikator żądania** — identyfikator żądania, które Cię zależy.

**Użytkownik** — nazwa lub główna nazwa użytkownika (UPN) dla Ciebie.

**Aplikacja** — nazwa aplikacji docelowej.
 
**Stan** — stan logowania, który Cię zależy:

- Powodzenie

- Niepowodzenie

- Przerwane


**Adres IP** — adres IP urządzenia używanego do nawiązywania połączenia z dzierżawą.

Lokalizacja **—** lokalizacja, z którego zostało zainicjowane połączenie:

- City (Miasto)

- Stan/prowincja

- Kraj/region


**Zasób** — nazwa usługi używanej do logowania.


**Identyfikator zasobu** — identyfikator usługi używanej do logowania.


**Aplikacja klienca** — typ aplikacji klienckiej używanej do nawiązywania połączenia z dzierżawą:

![Filtr aplikacji klienckiej](./media/concept-sign-ins/client-app-filter.png)


|Nazwa|Nowoczesne uwierzytelnianie|Opis|
|---|:-:|---|
|Uwierzytelniony protokół SMTP| |Używany przez klienta POP i IMAP do wysyłania wiadomości e-mail.|
|Automatycznego wykrywania| |Używane przez klientów programu Outlook i programu EAS do znajdowanie skrzynek pocztowych i łączenie się z nie w usłudze Exchange Online.|
|Exchange ActiveSync| |Ten filtr pokazuje wszystkie próby logowania, w przypadku których podjęto próbę protokołu EAS.|
|Przeglądarka|![Niebieski znacznik wyboru.](./media/concept-sign-ins/check.png)|Przedstawia wszystkie próby logowania użytkowników korzystających z przeglądarek internetowych|
|Exchange ActiveSync| | Przedstawia wszystkie próby logowania użytkowników z aplikacjami klienckimi korzystającymi z programu Exchange ActiveSync w celu nawiązania połączenia z usługą Exchange Online|
|Exchange Online PowerShell| |Służy do nawiązywania połączenia z usługą Exchange Online za pomocą zdalnego programu PowerShell. Jeśli zablokujesz uwierzytelnianie podstawowe dla programu PowerShell usługi Exchange Online, musisz użyć modułu Programu PowerShell usługi Exchange Online, aby nawiązać połączenie. Aby uzyskać instrukcje, zobacz [Connect to Exchange Online PowerShell using multi-factor authentication (Nawiązywanie połączenia z programem PowerShell usługi Exchange Online przy użyciu uwierzytelniania wieloskładnikowego).](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)|
|Exchange Web Services| |Interfejs programowania używany przez aplikacje Outlook, Outlook dla komputerów Mac i aplikacje innych firm.|
|IMAP4| |Starszy klient poczty korzystający z protokołu IMAP do pobierania wiadomości e-mail.|
|MAPI za pośrednictwem protokołu HTTP| |Używany przez program Outlook 2010 i nowsze.|
|Aplikacje mobilne i klienci desktopowi|![Niebieski znacznik wyboru.](./media/concept-sign-ins/check.png)|Przedstawia wszystkie próby logowania użytkowników korzystających z aplikacji mobilnych i klientów klasycznych.|
|Tryb offline Książka adresowa| |Kopia kolekcji listy adresów, które są pobierane i używane przez program Outlook.|
|Outlook Anywhere (RPC przez HTTP)| |Używany przez program Outlook 2016 i starsze.|
|Usługa Outlook| |Używany przez aplikację Poczta i kalendarz na Windows 10.|
|POP3| |Starszy klient poczty korzystający z pop3 do pobierania wiadomości e-mail.|
|Usługi sieci Web raportowania| |Służy do pobierania danych raportu w u usługi Exchange Online.|
|Inni klienci| |Przedstawia wszystkie próby logowania od użytkowników, dla których aplikacja klienutowa nie została uwzględniona lub nie jest znana.|



**System operacyjny** — system operacyjny uruchomiony na urządzeniu użył logowania do dzierżawy. 


**Przeglądarka urządzeń** — jeśli połączenie zostało zainicjowane z przeglądarki, to pole umożliwia filtrowanie według nazwy przeglądarki.


**Identyfikator korelacji** — identyfikator korelacji działania.




**Dostęp warunkowy** — stan zastosowanych reguł dostępu warunkowego

- **Nie zastosowano:** podczas logowania nie zastosowano żadnych zasad do użytkownika i aplikacji.

- **Powodzenie:** co najmniej jedna zasady dostępu warunkowego stosowane do użytkownika i aplikacji (ale niekoniecznie innych warunków) podczas logowania. 

- **Błąd:** Logowanie spełniało warunek użytkownika i aplikacji dla co najmniej jednej zasady dostępu warunkowego i kontroli udzielania nie jest spełniony lub ustawiono opcję blokowania dostępu.









## <a name="download-sign-in-activities"></a>Pobieranie działań związanych z logowaniem

Kliknij opcję **Pobierz,** aby utworzyć plik CSV lub JSON zawierający 250 000 najnowszych rekordów. Zacznij od [pobrania danych logowania,](quickstart-download-sign-in-report.md) jeśli chcesz pracować z danymi spoza Azure Portal.  

![Pobieranie](./media/concept-sign-ins/71.png "Pobierz")

> [!IMPORTANT]
> Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania Azure Active Directory raportu.](reference-reports-data-retention.md)  


## <a name="sign-ins-data-shortcuts"></a>Skróty danych logowania

Usługa Azure AD i Azure Portal zapewniają dodatkowe punkty wejścia do danych logowania:

- Omówienie ochrony zabezpieczeń tożsamości
- Użytkownicy
- Grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dane logowania użytkowników w programie Identity Security Protection

Wykres logowania użytkownika na stronie Przeglądu zabezpieczeń **tożsamości** zawiera cotygodniowe agregacje logowania. Wartość domyślna dla tego okresu wynosi 30 dni.

![Zrzut ekranu przedstawia wykres logów w ciągu miesiąca.](./media/concept-sign-ins/06.png "Aktywność związana z logowaniem")

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
- Date (Data)
- Wymagane uwierzytelnianie wieloskładnikowe
- Stan logowania

> [!NOTE]
> Adresy IP są wystawiane w taki sposób, że nie istnieje ostateczne połączenie między adresem IP a komputerem z tym adresem fizycznie zlokalizowanym. Mapowanie adresów IP jest skomplikowane przez fakt, że dostawcy urządzeń przenośnych i sieci VPN wystawiają adresy IP z pul centralnych, które często znajdują się bardzo daleko od miejsca, w którym faktycznie używane jest urządzenie klienckie. Obecnie w raportach usługi Azure AD konwersja adresu IP na lokalizację fizyczną jest najlepszym rozwiązaniem na podstawie śladów, danych rejestru, wyszukiwania wstecznego i innych informacji.

Na stronie **Użytkownicy** znajduje się pełny przegląd wszystkich logowań użytkowników dostępny po kliknięciu pozycji **Logowania** w sekcji **Działanie**.

![Zrzut ekranu przedstawia sekcję Działanie, w której można wybrać pozycję Logowania.](./media/concept-sign-ins/08.png "Aktywność związana z logowaniem")

## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Jakie są trzy najważniejsze aplikacje w organizacji?
* Jak działa moja najnowsza aplikacja?

Punktem wejścia do tych danych są trzy najważniejsze aplikacje w organizacji. Dane są zawarte w raporcie z ostatnich 30 dni w sekcji **Przegląd** w obszarze **Aplikacje dla przedsiębiorstw.**

![Zrzut ekranu przedstawiający miejsce, w którym można wybrać pozycję Przegląd.](./media/concept-sign-ins/10.png "Aktywność związana z logowaniem")

Wykres użycia aplikacji przedstawia tygodniowe agregacje logów dla trzech najoczywniej podanych aplikacji w danym okresie. Domyślny okres to 30 dni.

![Zrzut ekranu przedstawia użycie aplikacji w okresie jednego miesiąca.](./media/concept-sign-ins/graph-chart.png "Aktywność związana z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.

![Raportowanie](./media/concept-sign-ins/single-app-usage-graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.

Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 aktywności

Dzienniki aktywności Microsoft 365 można wyświetlić z centrum administracyjne platformy Microsoft 365 [.](/office365/admin/admin-overview/about-the-admin-center) Należy wziąć pod uwagę punkt, Microsoft 365 aktywności i dzienniki aktywności usługi Azure AD współdzielą znaczną liczbę zasobów katalogu. Tylko centrum administracyjne platformy Microsoft 365 zapewnia pełny widok dzienników Microsoft 365 aktywności. 

Dostęp do dzienników aktywności Microsoft 365 uzyskać programowo przy użyciu interfejsów API zarządzania usługi [Office 365.](/office/office-365-management-api/office-365-management-apis-overview)

## <a name="next-steps"></a>Następne kroki

* [Kody błędów raportu działań logowania](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych usługi Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów usługi Azure AD](reference-reports-latencies.md)