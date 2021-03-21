---
title: Automatyczne dostrajanie powiadomień e-mail — Przewodnik
description: Włącz powiadomienia e-mail dla Azure SQL Database automatycznego dostrajania zapytań.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500958"
---
# <a name="email-notifications-for-automatic-tuning"></a>Powiadomienia e-mail dotyczące dostrajania automatycznego
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Zalecenia dotyczące dostrajania Azure SQL Database są generowane przez Azure SQL Database [dostrajania automatycznego](automatic-tuning-overview.md). To rozwiązanie nieustannie monitoruje i analizuje obciążenia baz danych, zapewniając dostosowane zalecenia dotyczące dostrajania dla poszczególnych baz danych związanych z tworzeniem indeksu, usuwaniem indeksu i optymalizacją planów wykonywania zapytań.

Azure SQL Database zalecenia dotyczące dostrajania automatycznego można wyświetlać w [Azure Portal](database-advisor-find-recommendations-portal.md), pobierane z WYWOŁANIAMI [interfejsu API REST](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) lub przy użyciu poleceń [języka T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) i [programu PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) . Ten artykuł jest oparty na użyciu skryptu programu PowerShell w celu pobrania zaleceń dotyczących dostrajania automatycznego.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatyzuj powiadomienia e-mail dotyczące zaleceń dotyczących dostrajania automatycznego

Poniższe Rozwiązanie automatyzuje wysyłanie powiadomień e-mail zawierających zalecenia dotyczące dostrajania automatycznego. Opisane rozwiązanie składa się z automatyzacji wykonywania skryptu programu PowerShell na potrzeby pobierania zaleceń dostrajania przy użyciu [Azure Automation](../../automation/automation-intro.md)i automatyzacji zadania dostarczania w ramach planowania poczty e-mail przy użyciu [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Utwórz konto Azure Automation

Aby skorzystać z Azure Automation, pierwszym krokiem jest utworzenie konta usługi Automation i skonfigurowanie go przy użyciu zasobów platformy Azure w celu wykonania skryptu programu PowerShell. Aby dowiedzieć się więcej o Azure Automation i jej możliwościach, zobacz [Rozpoczynanie pracy z usługą Azure Automation](../../automation/index.yml).

Wykonaj następujące kroki, aby utworzyć konto Azure Automation za pomocą metody wybierania i konfigurowania aplikacji usługi Automation w witrynie Azure Marketplace:

1. Zaloguj się do witryny Azure Portal.
1. Kliknij pozycję "**+ Utwórz zasób**" w lewym górnym rogu.
1. Wyszukaj ciąg "**Automation**" (naciśnij klawisz ENTER).
1. Kliknij aplikację Automation w wynikach wyszukiwania.

    ![Dodawanie usługi Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. W okienku "Tworzenie konta usługi Automation" kliknij pozycję "**Utwórz**".
1. Wypełnij wymagane informacje: Wprowadź nazwę dla tego konta usługi Automation, wybierz identyfikator subskrypcji platformy Azure i zasoby platformy Azure, które mają być używane na potrzeby wykonywania skryptu programu PowerShell.
1. Dla opcji "**Utwórz konto Uruchom jako platformy Azure**" Wybierz opcję **tak** , aby skonfigurować typ konta, w ramach którego działa skrypt programu PowerShell z pomocą Azure Automation. Aby dowiedzieć się więcej na temat typów kont, zobacz [konto Uruchom jako](../../automation/manage-runas-account.md).
1. Aby zakończyć tworzenie konta usługi Automation, kliknij pozycję **Utwórz**.

> [!TIP]
> Zapisz nazwę konta Azure Automation, Identyfikator subskrypcji i zasoby (takie jak kopiowanie i wklejanie do Notatnika) dokładnie tak jak w przypadku tworzenia aplikacji automatyzacji. Te informacje będą potrzebne później.

Jeśli masz kilka subskrypcji platformy Azure, dla których chcesz skompilować tę samą automatyzację, musisz powtórzyć ten proces dla innych subskrypcji.

## <a name="update-azure-automation-modules"></a>Aktualizowanie modułów Azure Automation

Skrypt programu PowerShell umożliwiający pobranie zalecenia dotyczącego dostrajania automatycznego używa poleceń [Get-AzResource](/powershell/module/az.Resources/Get-azResource) i [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) , dla których wymagany jest moduł platformy Azure w wersji 4 lub nowszej.

- Jeśli Twoje moduły platformy Azure wymagają aktualizacji, zobacz [AZ module Support in Azure Automation](../../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Tworzenie elementu Runbook Azure Automation

Następnym krokiem jest utworzenie elementu Runbook w Azure Automation, w którym znajduje się skrypt programu PowerShell służący do pobierania zaleceń dostrajania.

Wykonaj następujące kroki, aby utworzyć nowy element Runbook Azure Automation:

1. Uzyskaj dostęp do konta Azure Automation utworzonego w poprzednim kroku.
1. Raz w okienku konto usługi Automation kliknij element menu "**elementy Runbook**" po lewej stronie, aby utworzyć nowy element Runbook Azure Automation przy użyciu skryptu programu PowerShell. Aby dowiedzieć się więcej na temat tworzenia elementów Runbook usługi Automation, zobacz [Tworzenie nowego elementu Runbook](../../automation/manage-runbooks.md#create-a-runbook).
1. Aby dodać nowy element Runbook, kliknij opcję menu "**+ Dodaj element Runbook**", a następnie kliknij pozycję "**szybkie tworzenie — tworzenie nowego elementu Runbook**".
1. W okienku elementu Runbook wpisz nazwę elementu Runbook (na potrzeby tego przykładu zostanie użyta wartość "**AutomaticTuningEmailAutomation**"), wybierz typ elementu Runbook jako program **PowerShell** i napisz opis tego elementu Runbook, aby opisać jego przeznaczenie.
1. Kliknij przycisk **Utwórz** , aby zakończyć tworzenie nowego elementu Runbook.

    ![Dodawanie elementu Runbook usługi Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Wykonaj następujące kroki, aby załadować skrypt programu PowerShell wewnątrz utworzonego elementu Runbook:

1. W okienku "**Edytuj element Runbook programu PowerShell**" Wybierz pozycję "**elementy Runbook**" w drzewie menu i rozwiń widok, dopóki nie zostanie wyświetlona nazwa elementu Runbook (w tym przykładzie "**AutomaticTuningEmailAutomation**"). Wybierz ten element Runbook.
1. W pierwszym wierszu "Edytuj element Runbook programu PowerShell" (rozpoczynając od numeru 1) Skopiuj poniższy kod skryptu programu PowerShell. Ten skrypt programu PowerShell jest dostępny z chwilą rozpoczęcia pracy. Zmodyfikuj skrypt, aby wymusić Twoje potrzeby.

W nagłówku podanego skryptu programu PowerShell należy zastąpić `<SUBSCRIPTION_ID_WITH_DATABASES>` identyfikatorem subskrypcji platformy Azure. Aby dowiedzieć się, jak pobrać identyfikator subskrypcji platformy Azure, zobacz temat [Pobieranie identyfikatora GUID subskrypcji platformy Azure](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal).

W przypadku kilku subskrypcji można je dodać jako rozdzielane przecinkami do właściwości "$subscriptions" w nagłówku skryptu.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
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

                # Loop through all automatic tuning recommendation types
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

Kliknij przycisk "**Zapisz**" w prawym górnym rogu, aby zapisać skrypt. Gdy skrypt jest zadowalający, kliknij przycisk "**Opublikuj**", aby opublikować ten element Runbook.

W głównym okienku elementu Runbook możesz kliknąć przycisk "**Start**", aby **przetestować** skrypt. Kliknij "**dane wyjściowe**", aby wyświetlić wyniki wykonywanego skryptu. Dane wyjściowe będą stanowić zawartość wiadomości e-mail. Przykładowe dane wyjściowe skryptu można zobaczyć na poniższym zrzucie ekranu.

![Uruchamianie widoku rekomendacje automatycznego dostrajania przy użyciu Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Upewnij się, że zawartość jest dostosowywana przez dostosowanie skryptu programu PowerShell do Twoich potrzeb.

W powyższych krokach skrypt programu PowerShell służący do pobierania zaleceń dostrajania automatycznego jest ładowany w Azure Automation. Następnym krokiem jest Automatyzacja i zaplanowanie zadania dostarczania poczty e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatyzowanie zadań poczty e-mail za pomocą Microsoft Flow

Aby zakończyć rozwiązanie, w ostatnim kroku Utwórz przepływ automatyzacji w Microsoft Flow zawierający trzy akcje (zadania):

- "**Azure Automation-Create Job**" — służy do wykonywania skryptu programu PowerShell w celu pobrania zaleceń dostrajania automatycznego w elemencie Runbook Azure Automation.
- "**Azure Automation — Pobierz dane wyjściowe zadania**" — służy do pobierania danych wyjściowych z wykonanego skryptu programu PowerShell.
- "**Office 365 Outlook — Wyślij wiadomość e-mail**" — służy do wysyłania wiadomości e-mail. Wiadomości e-mail są wysyłane przy użyciu konta służbowego indywidualnego tworzenia przepływu.

Aby dowiedzieć się więcej o możliwościach Microsoft Flow, zobacz [wprowadzenie do Microsoft Flow](/flow/getting-started).

Wymaganie wstępne dla tego kroku polega na zarejestrowaniu się w celu uzyskania konta [Microsoft Flow](https://flow.microsoft.com) i zalogowaniu się. Po umieszczeniu w rozwiązaniu wykonaj następujące kroki, aby skonfigurować **Nowy przepływ**:

1. Uzyskaj dostęp do elementu menu "**Moje przepływy**".
1. W obszarze Moje przepływy wybierz link "**+ Utwórz z pustego**" w górnej części strony.
1. Kliknij link "**Wyszukaj setki łączników i wyzwalaczy**" w dolnej części strony.
1. W polu wyszukiwania wpisz "**cykl**" i wybierz pozycję "**harmonogram-cykl**" z wyników wyszukiwania, aby zaplanować uruchamianie zadania dostarczania poczty e-mail.
1. W okienku cykl w polu Częstotliwość wybierz częstotliwość planowania dla tego przepływu, na przykład Wysyłaj automatyczne wiadomości e-mail co minutę, godzinę, dzień, tydzień itd.

Następnym krokiem jest dodanie trzech zadań (tworzenie, pobieranie danych wyjściowych i wysyłanie wiadomości e-mail) do nowo utworzonego przepływu cyklicznego. Aby wykonać Dodawanie wymaganych zadań do przepływu, wykonaj następujące kroki:

1. Utwórz akcję wykonywania skryptu programu PowerShell w celu pobrania zaleceń dotyczących dostrajania

   - Wybierz pozycję "**+ nowy krok**", a następnie pozycję "**Dodaj akcję**" w okienku przepływ cyklu.
   - W polu wyszukiwania wpisz "**Automatyzacja**" i wybierz pozycję "**Azure Automation — Utwórz zadanie**" z wyników wyszukiwania.
   - W okienku Tworzenie zadania skonfiguruj właściwości zadania. W przypadku tej konfiguracji potrzebne będą szczegóły dotyczące identyfikatora subskrypcji platformy Azure, grupy zasobów i konta usługi Automation, które **zostały wcześniej zarejestrowane** w **okienku konto usługi Automation**. Aby dowiedzieć się więcej o opcjach dostępnych w tej sekcji, zobacz [Azure Automation-Create Job](/connectors/azureautomation/#create-job).
   - Aby ukończyć tworzenie tej akcji, kliknij pozycję "**Zapisz przepływ**".

2. Utwórz akcję w celu pobrania danych wyjściowych z wykonanego skryptu programu PowerShell

   - Wybierz pozycję "**+ nowy krok**", a następnie pozycję "**Dodaj akcję**" w okienku przepływ cyklu.
   - W polu wyszukiwania wpisz "**Automatyzacja**" i wybierz pozycję "**Azure Automation — Pobierz dane wyjściowe zadania**" z wyników wyszukiwania. Aby dowiedzieć się więcej o opcjach dostępnych w tej sekcji, zobacz [Azure Automation – pobieranie danych wyjściowych zadania](/connectors/azureautomation/#get-job-output).
   - Wypełnij pola wymagane (podobne do tworzenia poprzedniego zadania) — Wypełnij Identyfikator subskrypcji platformy Azure, grupę zasobów i konto usługi Automation (zgodnie z definicją w okienku konto usługi Automation).
   - Kliknij wewnątrz pola "**Identyfikator zadania**", aby wyświetlić menu "**zawartość dynamiczna**". Z poziomu tego menu wybierz opcję "**Identyfikator zadania**".
   - Aby ukończyć tworzenie tej akcji, kliknij pozycję "**Zapisz przepływ**".

3. Utwórz akcję do wysłania wiadomości e-mail przy użyciu integracji z pakietem Office 365

   - Wybierz pozycję "**+ nowy krok**", a następnie pozycję "**Dodaj akcję**" w okienku przepływ cyklu.
   - W polu wyszukiwania wpisz "**Wyślij wiadomość e-mail**" i wybierz pozycję "**Office 365 Outlook — Wyślij wiadomość e-mail**" z wyników wyszukiwania.
   - W polu "**do**" wpisz adres e-mail, na który ma być wysyłana wiadomość e-mail z powiadomieniem.
   - W polu "**podmiot**" wpisz w temacie wiadomości e-mail, na przykład "powiadomienie dotyczące automatycznego dostrajania powiadomień e-mail".
   - Kliknij wewnątrz pola "**treść**", aby wyświetlić menu "**zawartość dynamiczna**". W tym menu w obszarze "**Pobierz dane wyjściowe zadania**" Wybierz pozycję "**zawartość**".
   - Aby ukończyć tworzenie tej akcji, kliknij pozycję "**Zapisz przepływ**".

> [!TIP]
> Aby wysyłać automatyczne wiadomości e-mail do różnych adresatów, Utwórz oddzielne przepływy. W tych dodatkowych przepływach Zmień adres e-mail adresata w polu "do" i wiersz tematu wiadomości e-mail w polu "podmiot". Tworzenie nowych elementów Runbook w Azure Automation przy użyciu dostosowanych skryptów programu PowerShell (na przykład zmiana identyfikatora subskrypcji platformy Azure) umożliwia dalsze dostosowanie zautomatyzowanych scenariuszy, takich jak wysyłanie wiadomości e-mail do oddzielnych odbiorców na zaleceniach dotyczących automatycznego dostrajania dla osobnych subskrypcji.

Powyższe zawiera kroki wymagane do skonfigurowania przepływu pracy dla zadania dostarczania poczty e-mail. Na poniższej ilustracji przedstawiono cały przepływ składający się z trzech utworzonych akcji.

![Wyświetlanie przepływu powiadomień e-mail dotyczących dostrajania automatycznego](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Aby przetestować przepływ, kliknij pozycję "**Uruchom teraz**" w prawym górnym rogu w okienku przepływ.

Statystyka uruchamiania zautomatyzowanych zadań pokazująca pomyślne wysłanie powiadomień e-mail, które można zobaczyć z okienka analiza przepływu.

![Uruchamianie przepływu dla powiadomień e-mail dotyczących dostrajania automatycznego](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

Okienko analiza przepływu ułatwia monitorowanie sukcesu wykonywania zadań i, jeśli jest to wymagane w celu rozwiązywania problemów.  W przypadku rozwiązywania problemów warto również zapoznać się z dziennikiem wykonywania skryptu programu PowerShell dostępnym za pomocą aplikacji Azure Automation.

Końcowe dane wyjściowe zautomatyzowanej wiadomości e-mail wyglądają podobnie do następującej wiadomości e-mail otrzymanej po skompilowaniu i uruchomieniu tego rozwiązania:

![Przykładowe dane wyjściowe wiadomości e-mail z powiadomień dotyczących automatycznego dostrajania wiadomości e-mail](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

Przez dostosowanie skryptu programu PowerShell możesz dostosować dane wyjściowe i formatowanie zautomatyzowanej wiadomości e-mail do swoich potrzeb.

Możesz dodatkowo dostosować rozwiązanie do kompilowania powiadomień e-mail na podstawie konkretnego zdarzenia dostrajania oraz wielu odbiorców dla wielu subskrypcji lub baz danych, w zależności od scenariuszy niestandardowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o tym, jak dostrajanie automatyczne może pomóc w ulepszaniu wydajności bazy danych, zobacz [dostrajanie automatyczne w Azure SQL Database](automatic-tuning-overview.md).
- Aby włączyć dostrajanie automatyczne w Azure SQL Database do zarządzania obciążeniem, zobacz [Włączanie dostrajania automatycznego](automatic-tuning-enable.md).
- Aby ręcznie przejrzeć i zastosować zalecenia dotyczące dostrajania automatycznego, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](database-advisor-find-recommendations-portal.md).