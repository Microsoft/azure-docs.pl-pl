---
title: Zaawansowane zabezpieczenia danych dla IaaS w Azure Security Center | Microsoft Docs
description: " Dowiedz się, jak włączyć zaawansowane zabezpieczenia danych dla IaaS w Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282733"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines (wersja zapoznawcza)
Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines to ujednolicony pakiet dla zaawansowanych funkcji zabezpieczeń SQL. Ta funkcja w wersji zapoznawczej obejmuje funkcje identyfikacji i łagodzenia potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenia dla bazy danych. 

Ta oferta zabezpieczeń dla serwerów SQL maszyn wirtualnych platformy Azure jest oparta na tej samej podstawowej technologii, która jest używana w [pakiecie Azure SQL Database Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Omówienie

Zaawansowane zabezpieczenia danych zapewniają zestaw zaawansowanych funkcji zabezpieczeń SQL, które obejmują ocenę luk w zabezpieczeniach i zaawansowaną ochronę przed zagrożeniami.

* [Ocena luk w zabezpieczeniach](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) to prosta konfiguracja usługi, która umożliwia odnajdywanie, śledzenie i rozwiązywanie problemów z potencjalnymi lukami w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i zawiera kroki rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.
* [Zaawansowana ochrona przed zagrożeniami](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do programu SQL Server lub korzystania z niego. Nieustannie monitoruje bazę danych pod kątem podejrzanych działań i zapewnia zorientowane na działania alerty zabezpieczeń dotyczące nietypowych wzorców dostępu do bazy danych. Te alerty zawierają szczegóły podejrzanych działań i zalecane akcje do zbadania i ograniczenia zagrożenia.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Wprowadzenie do zaawansowanych zabezpieczeń danych SQL na maszynach wirtualnych platformy Azure

Poniższe kroki ułatwiają rozpoczęcie pracy z zaawansowanymi zabezpieczeniami danych SQL na maszynach wirtualnych platformy Azure w publicznej wersji zapoznawczej.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Konfigurowanie zaawansowanych zabezpieczeń danych SQL na maszynach wirtualnych platformy Azure

Włącz zaawansowane zabezpieczenia danych dla serwerów SQL w Virtual Machines na poziomie subskrypcji/obszaru roboczego:
 
1. Na pasku bocznym Security Center Otwórz stronę **ustawienia & cennika** .

1. Wybierz subskrypcję lub obszar roboczy, dla którego chcesz włączyć zaawansowane zabezpieczenia danych SQL na maszynach wirtualnych platformy Azure.

1. Przełącz opcję dla **serwerów SQL na maszynie wirtualnej (wersja zapoznawcza)** na wartość włączone. 

    (Kliknij zrzut ekranu, aby rozwinąć)

    [![Security Center zalecenia i alerty wyświetlane w centrum administracyjnym systemu Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Zaawansowane zabezpieczenia danych dla serwerów SQL zostaną włączone na wszystkich serwerach SQL połączonych z wybranym obszarem roboczym lub domyślnym obszarze roboczym wybranej subskrypcji.

    >[!NOTE]
    > Rozwiązanie będzie w pełni aktywne po pierwszym ponownym uruchomieniu SQL Server. 

Aby utworzyć nowy obszar roboczy, postępuj zgodnie z instrukcjami w temacie [tworzenie log Analytics obszaru roboczego](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Aby połączyć hosta SQL Server z obszarem roboczym, postępuj zgodnie z instrukcjami podanymi w temacie [łączenie komputerów z systemem Windows, aby Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>Konfigurowanie powiadomień e-mail dla alertów zabezpieczeń 

Po wygenerowaniu alertów Security Center można ustawić listę adresatów, którzy otrzymają powiadomienie e-mail. Wiadomość e-mail zawiera bezpośredni link do alertu w Azure Security Center ze wszystkimi odpowiednimi szczegółami. 

1. Przejdź do **Security Center** > **Cennik & ustawienia** i kliknij odpowiednią subskrypcję

    ![Ustawienia subskrypcji](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. W menu Ustawienia kliknij pozycję **powiadomienia e-mail**. 
1. W polu tekstowym **adres e-mail** wprowadź adresy e-mail, na które mają być wysyłane powiadomienia. Można wprowadzić więcej niż jeden adres e-mail, oddzielając adresy e-mail przecinkami (,).  Na przykład admin1@mycompany.comadmin2@mycompany.com,admin3@mycompany.com

    ![Ustawienia poczty e-mail](./media/security-center-advanced-iaas-data/email-settings.png)

1. W ustawieniach **powiadomień e-mail** ustaw następujące opcje:
  
    * **Wysyłaj powiadomienia e-mail o alertach o wysokiej ważności**: zamiast wysyłać wiadomości e-mail dla wszystkich alertów, wysyłaj je tylko do alertów o wysokiej ważności.
    * **Wysyłaj również powiadomienia e-mail do właścicieli subskrypcji**: Wyślij powiadomienia do właścicieli subskrypcji.

1. W górnej części ekranu **powiadomienia e-mail** kliknij pozycję **Zapisz**.

  > [!NOTE]
  > Pamiętaj, aby kliknąć przycisk **Zapisz** przed zamknięciem okna lub nowe ustawienia **powiadomień e-mail** nie zostaną zapisane.

## <a name="explore-vulnerability-assessment-reports"></a>Poznaj raporty oceny luk w zabezpieczeniach

Pulpit nawigacyjny oceny luk w zabezpieczeniach zawiera przegląd wyników oceny dla wszystkich baz danych. Można wyświetlić dystrybucję baz danych zgodnie z wersją SQL Server, wraz z podsumowaniem niepowodzenia i przekazywania baz danych oraz ogólnym podsumowaniem niepowodzenia kontroli w zależności od dystrybucji ryzyka.

Wyniki oceny luk w zabezpieczeniach można wyświetlić bezpośrednio w Security Center.

1. Na pasku bocznym Security Center w obszarze HIGIENa zabezpieczeń zasobów wybierz pozycję **dane & Storage**.

1. Wybierz luki w **zabezpieczeniach dotyczące baz danych SQL w maszynach wirtualnych, które powinny zostać skorygowane (wersja zapoznawcza)**. Aby uzyskać więcej informacji, zobacz [Security Center zalecenia](security-center-recommendations.md). 

    [![* * Luki w zabezpieczeniach baz danych SQL na maszynach wirtualnych należy skorygować (wersja zapoznawcza) * * zalecenie](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Zostanie wyświetlony szczegółowy widok tego zalecenia.

    [![Szczegółowy widok dla * * luk w zabezpieczeniach baz danych SQL na maszynach wirtualnych powinien zostać skorygowany (wersja zapoznawcza) * * zalecenie](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Aby przejść do szczegółów, aby uzyskać więcej szczegółów:

    * Aby zapoznać się z omówieniem skanowanych zasobów (bazy danych) i listę testowanych testów zabezpieczeń, kliknij odpowiedni serwer.
    [![Luki w zabezpieczeniach pogrupowane według programu SQL Server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Aby zapoznać się z omówieniem luk w zabezpieczeniach pogrupowanych według konkretnej bazy danych SQL, kliknij interesującą bazę danych.
    [![Luki w zabezpieczeniach pogrupowane według programu SQL Server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    W każdym widoku sprawdzanie zabezpieczeń jest sortowane według **ważności**. Kliknij określone sprawdzanie zabezpieczeń, aby wyświetlić okienko szczegółów z **opisem**, jak **rozwiązać ten** problem, oraz inne powiązane informacje, takie jak **wpływ** lub **test porównawczy**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Zaawansowana ochrona przed zagrożeniami dla serwerów SQL na maszynach wirtualnych platformy Azure
Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do serwerów SQL lub korzystania z nich. Zdarzenia te mogą wyzwalać następujące alerty:

### <a name="anomalous-access-pattern-alerts-preview"></a>Alerty nietypowego wzorca dostępu (wersja zapoznawcza)

* **Dostęp z nietypowej lokalizacji:** Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do programu SQL Server, w którym ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. Możliwe przyczyny:
    * Osoba atakująca lub poprzednie złośliwe oprogramowanie uzyskało dostęp do SQL Server.
    * Uprawniony użytkownik uzyskał dostęp do SQL Server z nowej lokalizacji.
* **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. Możliwe przyczyny:
    * Osoba atakująca próbująca naruszyć kod SQL przy użyciu popularnych narzędzi ataku.
    * W działaniu legalne testowanie penetracji.
* **Dostęp z nieznanego podmiotu zabezpieczeń**: Ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera SQL, gdy ktoś zalogował się do serwera SQL za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). Możliwe przyczyny:
    * Osoba atakująca lub poprzednie złośliwe oprogramowanie uzyskało dostęp do SQL Server. 
    * Uprawniony użytkownik uzyskał dostęp do SQL Server z nowego podmiotu zabezpieczeń.
* **Atak siłowy na poświadczenia SQL**: Ten alert jest wyzwalany w przypadku nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. Możliwe przyczyny:
    * Osoba atakująca próbująca naruszyć kod SQL przy użyciu funkcji pełnego wymuszania.
    * W działaniu legalne testowanie penetracji.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potencjalne ataki z iniekcją SQL (obsługiwane w SQL Server 2019)

* **Podatność na iniekcję SQL**: ten alert jest wyzwalany, gdy aplikacja generuje błędną instrukcję SQL w bazie danych. który może oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez iniekcję kodu SQL. Możliwe przyczyny:
    * Usterka w kodzie aplikacji powoduje utworzenie błędnej instrukcji SQL
    * Kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas tworzenia błędnej instrukcji SQL, co można wykorzystać do iniekcji SQL
* **Potencjalna iniekcja SQL**: Ten alert jest wyzwalany w przypadku aktywnego wykorzystania zidentyfikowanej luki umożliwiającej iniekcję SQL w aplikacji. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z podatnego kodu aplikacji lub procedur składowanych.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Niebezpieczne polecenie (obsługiwane w SQL Server 2019)

* **Potencjalnie niebezpieczna akcja**: ten alert jest wyzwalany, gdy jest wykonywane wysoce uprzywilejowane i potencjalnie niebezpieczne polecenie. Możliwe przyczyny:
    * Polecenie, które zaleca się wyłączenie dla lepszego stan zabezpieczeń, jest włączone.
    * Osoba atakująca próbuje wykorzystać dostęp do programu SQL Server lub eskalować uprawnienia.   


## <a name="explore-and-investigate-security-alerts"></a>Eksplorowanie i badanie alertów zabezpieczeń

Alerty zabezpieczeń danych są dostępne na stronie Alerty Security Center, na karcie Zabezpieczenia zasobu lub za pośrednictwem bezpośredniego linku w wiadomościach e-mail dotyczących alertów.

1. Aby wyświetlić alerty:

    * W obszarze Security Center kliknij pozycję **alerty zabezpieczeń** na pasku bocznym i wybierz alert.
    * W zakresie zasobów — Otwórz odpowiednią stronę zasobów, a następnie z paska bocznego kliknij pozycję **zabezpieczenia**. 

1. Alerty zostały zaprojektowane jako samodzielne, z szczegółowymi krokami korygującymi i informacjami o badaniu w każdej z nich. Można dokładniej badać przy użyciu innych Azure Security Center i możliwości funkcji badania platformy Azure w szerszym widoku:

    * Włącz funkcję inspekcji SQL Server na potrzeby dalszych badań. Jeśli jesteś użytkownikiem ze wskaźnikiem wydajności platformy Azure, możesz przekazać dzienniki inspekcji SQL z zdarzeń dziennika zabezpieczeń systemu Windows do badania wskaźnikowego i cieszyć się bogatym doświadczeniem.
    * Aby ulepszyć stan zabezpieczeń, użyj zaleceń Security Center dla maszyny hosta wskazanej w każdym alercie. Pozwoli to zmniejszyć ryzyko ataków na ataki w przyszłości. 


## <a name="next-steps"></a>Następne kroki

W przypadku pokrewnego materiału zapoznaj się z następującym artykułem:

- [Jak skorygować zalecenia](security-center-remediate-recommendations.md)