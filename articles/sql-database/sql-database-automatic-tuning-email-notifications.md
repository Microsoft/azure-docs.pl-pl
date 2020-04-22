---
title: Przewodnik po automatycznym dostrajaniu powiadomień e-mail
description: Włącz powiadomienia e-mail dla automatycznego dostrajania zapytań usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b3b235833e794e48ae655d184bf938effc0d7ac0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768375"
---
# <a name="email-notifications-for-automatic-tuning"></a>Powiadomienia e-mail do automatycznego dostrajania

Zalecenia dotyczące dostrajania bazy danych SQL są generowane przez [automatyczne dostrajanie](sql-database-automatic-tuning.md)bazy danych SQL Azure . To rozwiązanie stale monitoruje i analizuje obciążenia baz danych SQL, zapewniając dostosowane zalecenia dotyczące dostrajania dla każdej pojedynczej bazy danych związane z tworzeniem indeksu, usuwaniem indeksu i optymalizacją planów wykonywania zapytań.

Zalecenia dotyczące automatycznego dostrajania bazy danych SQL można wyświetlać w [portalu Azure](sql-database-advisor-portal.md)portal , pobieranym za pomocą wywołań interfejsu [API REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) lub przy użyciu poleceń [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) i [PowerShell.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) Ten artykuł jest oparty na użyciu skryptu programu PowerShell do pobierania zaleceń automatycznego dostrajania.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatyzacja powiadomień e-mail w celu uzyskania rekomendacji automatycznego dostrajania

Poniższe rozwiązanie automatyzuje wysyłanie powiadomień e-mail zawierających zalecenia dotyczące automatycznego dostrajania. Opisane rozwiązanie polega na automatyzacji wykonywania skryptu programu PowerShell w celu pobierania zaleceń dotyczących dostrajania przy użyciu [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)oraz automatyzacji planowania zadania dostarczania wiadomości e-mail przy użyciu usługi Microsoft [Flow.](https://flow.microsoft.com)

## <a name="create-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Aby użyć usługi Azure Automation, pierwszym krokiem jest utworzenie konta automatyzacji i skonfigurowanie go z zasobami platformy Azure do użycia do wykonania skryptu programu PowerShell. Aby dowiedzieć się więcej o usłudze Azure Automation i jej możliwościach, zobacz [Wprowadzenie do automatyzacji platformy Azure.](https://docs.microsoft.com/azure/automation/automation-offering-get-started)

Wykonaj następujące kroki, aby utworzyć konto usługi Azure Automation za pomocą metody wybierania i konfigurowania aplikacji automatyzacji z portalu Marketplace:

1. Zaloguj się do witryny Azure Portal.
1. Kliknij "**+ Utwórz zasób**" w lewym górnym rogu.
1. Wyszukaj "**Automatyzacja**" (naciśnij enter).
1. Kliknij aplikację Automatyzacja w wynikach wyszukiwania.

    ![Dodawanie automatyzacji platformy Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

1. Po wejściu do okienka "Utwórz konto automatyzacji" kliknij "**Utwórz**".
1. Wypełnij wymagane informacje: wprowadź nazwę dla tego konta automatyzacji, wybierz identyfikator subskrypcji platformy Azure i zasoby platformy Azure, które mają być używane do wykonywania skryptu programu PowerShell.
1. W przypadku opcji **"Utwórz konto azure uruchom jako**" wybierz opcję **Tak,** aby skonfigurować typ konta, na którym skrypt programu PowerShell jest uruchamiany za pomocą usługi Azure Automation. Aby dowiedzieć się więcej o typach kont, zobacz [Uruchamianie jako konto](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
1. Zawarcie utworzenia konta automatyzacji przez kliknięcie **przycisku Utwórz**.

> [!TIP]
> Zapisz nazwę konta usługi Azure Automation, identyfikator subskrypcji i zasoby (takie jak kopiuj i wklej do notatnika) dokładnie tak, jak wprowadzono podczas tworzenia aplikacji automatyzacji. Te informacje są potrzebne później.
>

Jeśli masz kilka subskrypcji platformy Azure, dla których chcesz utworzyć tę samą automatyzację, musisz powtórzyć ten proces dla innych subskrypcji.

## <a name="update-azure-automation-modules"></a>Aktualizowanie modułów usługi Azure Automation

Skrypt programu PowerShell do pobierania zalecenia automatycznego dostrajania używa [get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) i [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) polecenia, dla których moduł Azure w wersji 4 i powyżej jest wymagane.

- Jeśli moduły platformy Azure wymagają aktualizacji, zobacz [obsługę modułów Az w usłudze Azure Automation.](../automation/shared-resources/modules.md)

## <a name="create-azure-automation-runbook"></a>Tworzenie systemu ceń automatyzacji platformy Azure

Następnym krokiem jest utworzenie likmy w usłudze Azure Automation, wewnątrz której znajduje się skrypt programu PowerShell do pobierania zaleceń dostrajania.

Wykonaj następujące kroki, aby utworzyć nowy projekt owy usługi Azure Automation:

1. Uzyskaj dostęp do konta usługi Azure Automation utworzonego w poprzednim kroku.
1. Po wejściu do okienka konta automatyzacji kliknij element menu **"Runbooks"** po lewej stronie, aby utworzyć nowy element runbook usługi Azure Automation ze skryptem programu PowerShell. Aby dowiedzieć się więcej o tworzeniu śmięty automatyzacji, zobacz [Tworzenie nowego uruchomieniu .](../automation/manage-runbooks.md#creating-a-runbook)
1. Aby dodać nowy element runbook, kliknij opcję menu "**+Dodaj element runbook**", a następnie kliknij przycisk "**Szybkie tworzenie — utwórz nowy element runbook**"..
1. W okienku Runbook wpisz nazwę przewodniczącego (na potrzeby tego przykładu jest używana "**AutomaticTuningEmailAutomation"),** wybierz typ przewodniczącego jako **program PowerShell** i napisz opis tego żyjącego łańca, aby opisać jego przeznaczenie.
1. Kliknij przycisk **Utwórz,** aby zakończyć tworzenie nowego eksmisji.

    ![Dodawanie podręcznika automatyzacji platformy Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Wykonaj następujące kroki, aby załadować skrypt programu PowerShell wewnątrz utworzonego życiówki:

1. Wewnątrz okienka "**Edytuj runbook programu PowerShell**" wybierz "**RUNBOOKS**" w drzewie menu i rozwiń widok, aż zobaczysz nazwę żyjącego (w tym przykładzie "**AutomaticTuningEmailAutomation**"). Wybierz ten projekt eks-owy.
1. W pierwszym wierszu "Edytuj runbook programu PowerShell" (począwszy od numeru 1), kopiuj i wklej następujący kod skryptu programu PowerShell. Ten skrypt programu PowerShell jest dostarczany w stanie takim, w jakim można rozpocząć pracę. Zmodyfikuj skrypt, aby dochowywać.

W nagłówku dostarczonego skryptu programu PowerShell należy zastąpić `<SUBSCRIPTION_ID_WITH_DATABASES>` identyfikatorem subskrypcji platformy Azure. Aby dowiedzieć się, jak pobrać identyfikator subskrypcji platformy Azure, zobacz [Pobieranie identyfikatora GUID subskrypcji platformy Azure.](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/)

W przypadku kilku subskrypcji można dodać je jako przecinki rozdzielone do właściwości "$subscriptions" w nagłówku skryptu.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Kliknij przycisk **"Zapisz"** w prawym górnym rogu, aby zapisać skrypt. Po zakończeniu pracy ze skryptem kliknij przycisk "**Publikuj**", aby opublikować ten projekt runbook.

W głównym okienku runbooka możesz kliknąć przycisk "**Start**", aby **przetestować** skrypt. Kliknij na **"Dane wyjściowe",** aby wyświetlić wyniki wykonanego skryptu. Ten wynik będzie zawartość wiadomości e-mail. Przykładowe dane wyjściowe ze skryptu można zobaczyć na poniższym zrzucie ekranu.

![Uruchamianie zaleceń dotyczących automatycznego dostrajania widoku za pomocą usługi Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Upewnij się, aby dostosować zawartość, dostosowując skrypt programu PowerShell do swoich potrzeb.

W powyższych krokach skrypt programu PowerShell do pobierania zaleceń automatycznego dostrajania jest ładowany w usłudze Azure Automation. Następnym krokiem jest zautomatyzowanie i zaplanowanie zadania dostarczania wiadomości e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatyzacja zadań poczty e-mail za pomocą usługi Microsoft Flow

Aby ukończyć rozwiązanie, jako ostatni krok, utwórz przepływ automatyzacji w usłudze Microsoft Flow składający się z trzech akcji (zadań):

 - "Usługa**Azure Automation — tworzenie zadania"**— służy do wykonywania skryptu programu PowerShell w celu pobrania zaleceń dotyczących automatycznego dostrajania w bielińszku usługi Azure Automation.
 - "Usługa**Azure Automation — pobierz dane wyjściowe zadania"**— służy do pobierania danych wyjściowych z wykonanego skryptu programu PowerShell.
 - "**Office 365 Outlook — Wyślij wiadomość e-mail**" — służy do wysyłania wiadomości e-mail. Wiadomości e-mail są wysyłane przy użyciu konta usługi Office 365 osoby tworzącej przepływ.

Aby dowiedzieć się więcej o funkcjach usługi Microsoft Flow, zobacz [Wprowadzenie do usługi Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Warunkiem wstępnym tego kroku jest utworzenie konta [Usługi Microsoft Flow](https://flow.microsoft.com) i zalogowanie się. Po wejściu do roztworu wykonaj następujące kroki, aby skonfigurować **nowy przepływ:**

1. Dostęp do pozycji menu **"Moje przepływy".**
1. W obszarze Moje przepływy wybierz link "**+Utwórz z pustego**miejsca " u góry strony.
1. Kliknij link "**Wyszukaj setki łączników i wyzwalaczy**" u dołu strony.
1. W polu wyszukiwania wpisz "**recurrence**" i wybierz "**Harmonogram - Cykl**" z wyników wyszukiwania, aby zaplanować uruchomienie zadania dostarczania wiadomości e-mail.
1. W okienku Cykl w polu Częstotliwość wybierz częstotliwość planowania dla tego przepływu, na przykład wysyłanie automatycznej wiadomości e-mail co minutę, godzinę, dzień, tydzień itp.

Następnym krokiem jest dodanie trzech zadań (tworzenie, pobierz dane wyjściowe i wyślij wiadomość e-mail) do nowo utworzonego przepływu cyklicznego. Aby wykonać dodawanie wymaganych zadań do przepływu, wykonaj następujące kroki:

1. Tworzenie akcji w celu wykonania skryptu programu PowerShell w celu pobrania zaleceń dotyczących dostrajania

   - Wybierz "**+Nowy krok**", a następnie "**Dodaj akcję**" w okienku przepływu cyklu.
   - W polu wyszukiwania wpisz "**automatyzacja**" i wybierz "**Usługa Azure Automation – Tworzenie zadania**" z wyników wyszukiwania.
   - W okienku Tworzenie zadania skonfiguruj właściwości zadania. W przypadku tej konfiguracji potrzebne będą szczegółowe informacje o identyfikatorze subskrypcji platformy Azure, grupie zasobów i koncie automatyzacji **wcześniej zarejestrowanym** w **okienku Konto automatyzacji**. Aby dowiedzieć się więcej o opcjach dostępnych w tej sekcji, zobacz [Usługa Azure Automation — Tworzenie zadania](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Ukończ tworzenie tej akcji, klikając "**Zapisz przepływ**".

2. Tworzenie akcji w celu pobrania danych wyjściowych z wykonanego skryptu programu PowerShell

   - Wybierz "**+Nowy krok**", a następnie "**Dodaj akcję**" w okienku przepływu cyklu
   - W wyszukiwaniu typu "**automation**" i wybierz "**Usługa Azure Automation — Pobierz dane wyjściowe zadania**" z wyników wyszukiwania. Aby dowiedzieć się więcej o opcjach dostępnych w tej sekcji, zobacz [Usługa Azure Automation — pobierz dane wyjściowe zadania.](https://docs.microsoft.com/connectors/azureautomation/#get-job-output)
   - Wypełnianie pól wymaganych (podobnie jak tworzenie poprzedniego zadania) — wypełnianie identyfikatora subskrypcji platformy Azure, grupy zasobów i konta automatyzacji (wprowadzonego w okienku Konto automatyzacji).
   - Kliknij wewnątrz pola "**Identyfikator zadania**" dla menu "**Zawartość dynamiczna**", aby się wyświetlić. W tym menu wybierz opcję "**Identyfikator zadania**".
   - Ukończ tworzenie tej akcji, klikając "**Zapisz przepływ**".

3. Tworzenie akcji do wysyłania wiadomości e-mail przy użyciu integracji z usłudze Office 365

   - Wybierz "**+Nowy krok**", a następnie "**Dodaj akcję**" w okienku przepływu cyklu.
   - W wpisowym wyszukiwaniu wpisz "**wyślij wiadomość e-mail**" i wybierz "**Office 365 Outlook – Wyślij wiadomość e-mail**" z wyników wyszukiwania.
   - W polu Wpisz pole "**Do"** w adresie e-mail, na który musisz wysłać wiadomość e-mail z powiadomieniem.
   - W polu "**Temat**" wpisz temat wiadomości e-mail, na przykład "Automatyczne dostrajanie rekomendacji powiadomienia e-mail".
   - Kliknij wewnątrz pola "**Body**" dla menu "**Zawartość dynamiczna**", aby się wyświetlić. Z tego menu w obszarze "**Pobierz dane wyjściowe zadania**", wybierz "**Content**".
   - Ukończ tworzenie tej akcji, klikając "**Zapisz przepływ**".

> [!TIP]
> Aby wysyłać automatyczne wiadomości e-mail do różnych adresatów, należy utworzyć oddzielne przepływy. W tych dodatkowych przepływach zmień adres e-mail adresata w polu "Do" oraz wiersz tematu wiadomości e-mail w polu "Temat". Tworzenie nowych niestandardowych niestandardowych niestandardowych niestandardowych skryptów programu PowerShell (na przykład ze zmianą identyfikatora subskrypcji platformy Azure) umożliwia dalsze dostosowywanie zautomatyzowanych scenariuszy, takich jak na przykład wysyłanie wiadomości e-mail oddzielnych adresatów w zaleceniach automatycznego dostrajania dla oddzielnych subskrypcji.
>

Powyższe określa kroki wymagane do skonfigurowania przepływu pracy zadania dostarczania poczty e-mail. Cały przepływ składający się z trzech zbudowanych akcji jest pokazany na poniższej ilustracji.

![Wyświetlanie przepływu powiadomień e-mail do automatycznego dostrajania](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Aby przetestować przepływ, kliknij przycisk "**Uruchom teraz**" w prawym górnym rogu wewnątrz okienka przepływu.

Statystyki uruchamiania zautomatyzowanych zadań, pokazujące powodzenie wysyłanych powiadomień e-mail, można zobaczyć w okienku analizy przepływu.

![Przepływ uruchamiania dla automatycznego dostrajania powiadomień e-mail](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Analiza przepływu jest przydatna do monitorowania powodzenia wykonywania zadań i w razie potrzeby do rozwiązywania problemów.  W przypadku rozwiązywania problemów można również sprawdzić dziennik wykonywania skryptu programu PowerShell dostępny za pośrednictwem aplikacji Usługi Azure Automation.

Ostateczne dane wyjściowe automatycznej wiadomości e-mail wyglądają podobnie do następującej wiadomości e-mail otrzymanej po zbudowaniu i uruchomieniu tego rozwiązania:

![Przykładowe dane wyjściowe wiadomości e-mail z automatycznego dostrajania powiadomień e-mail](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Dostosowując skrypt programu PowerShell, można dostosować dane wyjściowe i formatowanie automatycznej poczty e-mail do swoich potrzeb.

Można dodatkowo dostosować rozwiązanie do tworzenia powiadomień e-mail na podstawie określonego zdarzenia dostrajania i wielu adresatów, dla wielu subskrypcji lub baz danych, w zależności od scenariuszy niestandardowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o tym, jak automatyczne dostrajanie może pomóc w zwiększce wydajności bazy danych, zobacz [Automatyczne dostrajanie w bazie danych SQL azure](sql-database-automatic-tuning.md).
- Aby włączyć automatyczne dostrajanie w usłudze Azure SQL Database w celu zarządzania obciążeniem, zobacz [Włączanie dostrajania automatycznego](sql-database-automatic-tuning-enable.md).
- Aby ręcznie przejrzeć i zastosować zalecenia dotyczące automatycznego dostrajania, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](sql-database-advisor-portal.md).
