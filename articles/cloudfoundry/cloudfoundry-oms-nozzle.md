---
title: Wdrażanie usługi Azure Log Analytics dysz na potrzeby monitorowania Cloud Foundry
description: Wskazówki krok po kroku dotyczące wdrażania Cloud Foundry Loggregator dysz dla platformy Azure Log Analytics. Użyj dyszy do monitorowania metryk kondycji systemu Cloud Foundry i wydajności.
services: virtual-machines
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 54001c47d03b686a8e7c1f59f1e53d405e3bc506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557390"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Wdrażanie usługi Azure Log Analytics dysz dla monitorowania systemu Cloud Foundry

[Azure monitor](https://azure.microsoft.com/services/log-analytics/) to usługa platformy Azure. Ułatwia zbieranie i analizowanie danych generowanych na podstawie Twoich środowisk w chmurze i lokalnych.

Dysza Log Analytics (dysza) jest składnikiem Cloud Foundry (CF), który przekazuje metryki z [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose do dzienników Azure monitor. Za pomocą dysz można zbierać, wyświetlać i analizować metryki systemu CF oraz dane wydajności w wielu wdrożeniach.

W tym dokumencie dowiesz się, jak wdrożyć dyszę w środowisku CF, a następnie uzyskać dostęp do danych z konsoli Dzienniki Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania wstępne dotyczące wdrażania dysz.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Wdróż środowisko Cloud Foundry CF lub przestawne na platformie Azure

Możesz użyć dyszy z wdrożeniem CF typu "open source" lub wdrożeniem Cloud Foundry (PCF).

* [Wdrażanie rozwiązania Cloud Foundry na platformie Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Wdrażanie Cloud Foundry Pivot na platformie Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Zainstaluj narzędzia wiersza polecenia CF służące do wdrażania dysz

Dysza jest uruchamiana jako aplikacja w środowisku CF. Do wdrożenia aplikacji potrzebny jest interfejs wiersza polecenia CF.

Dysza wymaga również uprawnienia dostępu do Loggregator Firehose i kontrolera chmury. Aby można było utworzyć i skonfigurować użytkownika, potrzebna jest usługa konta użytkownika i uwierzytelniania (UAA).

* [Instalowanie interfejsu wiersza polecenia Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Zainstaluj klienta wiersza polecenia Cloud Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Przed skonfigurowaniem klienta wiersza polecenia UAA upewnij się, że zainstalowano RubyGems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Tworzenie obszaru roboczego Log Analytics na platformie Azure

Obszar roboczy Log Analytics można utworzyć ręcznie lub za pomocą szablonu. Szablon wdroży konfigurację wstępnie skonfigurowanych widoków i alertów KPI dla konsoli Dzienniki Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Aby ręcznie utworzyć obszar roboczy:

1. Na Azure Portal Przeszukaj listę usług w witrynie Azure Marketplace, a następnie wybierz pozycję Log Analytics obszary robocze.
2. Wybierz pozycję **Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * **Log Analytics obszar roboczy**: wpisz nazwę obszaru roboczego.
   * **Subskrypcja**: Jeśli masz wiele subskrypcji, wybierz tę, która jest taka sama jak w przypadku wdrożenia CF.
   * **Grupa zasobów**: można utworzyć nową grupę zasobów lub użyć tej samej grupy z wdrożeniem CF.
   * **Lokalizacja**: wprowadź lokalizację.
   * **Warstwa cenowa**: wybierz pozycję **OK** , aby zakończyć.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Aby utworzyć obszar roboczy Log Analytics przy użyciu szablonu monitorowania z usługi Azure Market:

1. Otwórz witrynę Azure Portal.
1. Kliknij znak "+" lub "Utwórz zasób" w lewym górnym rogu.
1. W oknie wyszukiwania wpisz "Cloud Foundry", wybierz pozycję "Cloud Foundry monitorowanie rozwiązania".
1. Zostanie załadowana strona frontonu szablonu rozwiązania monitorowania Cloud Foundry, kliknij przycisk "Utwórz", aby uruchomić blok szablonu.
1. Wprowadź wymagane parametry:
    * **Subskrypcja**: wybierz subskrypcję platformy Azure dla obszaru roboczego log Analytics, zazwyczaj tym samym w przypadku wdrożenia Cloud Foundry.
    * **Grupa zasobów**: wybierz istniejącą grupę zasobów lub Utwórz nową dla obszaru roboczego log Analytics.
    * **Lokalizacja grupy zasobów**: Wybierz lokalizację grupy zasobów.
    * **OMS_Workspace_Name**: Wprowadź nazwę obszaru roboczego, jeśli obszar roboczy nie istnieje, szablon utworzy nowy.
    * **OMS_Workspace_Region**: Wybierz lokalizację obszaru roboczego.
    * **OMS_Workspace_Pricing_Tier**: Wybierz jednostkę SKU log Analytics obszaru roboczego. Zapoznaj się ze [wskazówkami](https://azure.microsoft.com/pricing/details/log-analytics/) dotyczącymi cen.
    * **Postanowienia prawne**: kliknij pozycję postanowienia prawne, a następnie kliknij pozycję "Utwórz", aby zaakceptować termin prawny.
1. Po określeniu wszystkich parametrów kliknij przycisk "Utwórz", aby wdrożyć szablon. Po zakończeniu wdrażania stan zostanie wyświetlony na karcie powiadomienie.


## <a name="deploy-the-nozzle"></a>Wdróż dyszę

Istnieje kilka różnych sposobów wdrażania dysz: jako kafelka PCF lub aplikacja CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Wdrażanie dyszy jako kafelka Menedżera PCF Ops

Postępuj zgodnie z instrukcjami, aby [zainstalować i skonfigurować dyszę usługi Azure log Analytics w PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Jest to uproszczone podejście, kafelek PCF Ops Manager automatycznie skonfiguruje i wypycha dyszę. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Ręczne wdrażanie dyszy jako aplikacji CF

Jeśli nie używasz programu PCF Ops Manager, wdróż dyszę jako aplikację. W poniższych sekcjach opisano ten proces.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Logowanie do wdrożenia CF jako administrator za za poorednictwem interfejsu wiersza polecenia CF

Uruchom następujące polecenie:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" jest nazwą domeny CF. Można go pobrać, przeszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF. 

"CF_User" jest nazwą administratora CF. Nazwę i hasło można pobrać, przeszukując sekcję "Standard scim", szukając nazwy i "cf_admin_password" w pliku manifestu wdrożenia CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Tworzenie użytkownika CF i przyznawanie wymaganych uprawnień

Uruchom następujące polecenia:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" jest nazwą domeny CF. Można go pobrać, przeszukując "SYSTEM_DOMAIN" w pliku manifestu wdrożenia CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Pobierz najnowszą wersję Log Analytics z dyszą

Uruchom następujące polecenie:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Teraz można ustawić zmienne środowiskowe w pliku manifest. yml w bieżącym katalogu. Poniżej przedstawiono manifest aplikacji dla dysz. Zastąp wartości określonym Log Analytics informacjami o obszarze roboczym.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Wypychanie aplikacji z komputera deweloperskiego

Upewnij się, że jesteś w folderze OMS-log-Analytics-Firehose-dysz. Uruchom następujące polecenie:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Weryfikowanie instalacji dysz

### <a name="from-apps-manager-for-pcf"></a>Z Menedżera aplikacji (dla programu PCF)

1. Zaloguj się do programu Operations Manager i upewnij się, że kafelek jest wyświetlany na pulpicie nawigacyjnym instalacji.
2. Zaloguj się do programu Apps Manager, upewnij się, że miejsce utworzone dla dysz znajduje się na liście w raporcie użycia, i sprawdź, czy stan jest normalny.

### <a name="from-your-development-computer"></a>Na komputerze deweloperskim

W oknie interfejsu wiersza polecenia CF wpisz:
```
cf apps
```
Upewnij się, że aplikacja z dyszą pakietu OMS jest uruchomiona.

## <a name="view-the-data-in-the-azure-portal"></a>Wyświetlanie danych w Azure Portal

Jeśli wdrożono rozwiązanie do monitorowania za pomocą szablonu miejsca na rynku, przejdź do Azure Portal i Znajdź rozwiązanie. Rozwiązanie można znaleźć w grupie zasobów określonej w szablonie. Kliknij rozwiązanie, przejdź do "konsoli usługi log Analytics", wyświetlane są wstępnie skonfigurowane widoki z górnymi Cloud Foundry systemowymi wskaźnikami wydajności, danymi aplikacji, alertami i metrykami kondycji maszyny wirtualnej. 

Jeśli utworzono ręcznie obszar roboczy Log Analytics, wykonaj poniższe czynności, aby utworzyć widoki i alerty:

### <a name="1-import-the-oms-view"></a>1. Zaimportuj widok pakietu OMS

W portalu pakietu OMS przejdź do **widoku widok**  >  **importowania** projektanta  >  i wybierz jeden z plików omsview. Na przykład wybierz *Cloud Foundry. omsview* i Zapisz widok. Teraz kafelek zostanie wyświetlony na stronie **Przegląd** . Wybierz go, aby zobaczyć wizualizacje metryki.

Można dostosować te widoki lub utworzyć nowe widoki za poorednictwem **projektanta widoków**.

*"Cloud Foundry. omsview"* jest wersją zapoznawczą szablonu widoku OMS Cloud Foundry. Jest to w pełni skonfigurowany, szablon domyślny. Jeśli masz sugestie lub opinie na temat szablonu, wyślij je do [sekcji problemu](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Utwórz reguły alertów

Możesz [tworzyć alerty](../azure-monitor/alerts/alerts-overview.md)i dostosowywać zapytania i wartości progowe odpowiednio do wymagań. Zalecane są następujące alerty:

| Zapytanie wyszukiwania                                                                  | Generuj alert na podstawie | Opis                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type = CF_ValueMetric_CL Origin_s = BBS Name_s = "Domain. CF-Apps"                   | Liczba wyników < 1   | **BBS. Domain.cf — aplikacje** wskazuje, czy domena CF-Apps jest aktualna. Oznacza to, że żądania aplikacji CF z kontrolera chmury są synchronizowane z usługą BBS. LRPsDesired (Diego-żądana AIs) do wykonania. Żadne dane nie zostały odebrane oznacza, że domena CF nie jest aktualna w określonym przedziale czasu. |
| Type = CF_ValueMetric_CL Origin_s = przedstawiciel Name_s = UnhealthyCell Value_d>1            | Liczba wyników > 0   | W przypadku komórek Diego wartość 0 oznacza kondycję i 1 oznacza złą kondycję. Ustaw Alert, jeśli w określonym przedziale czasu zostanie wykryta wiele komórek Diego w złej kondycji. |
| Type = CF_ValueMetric_CL Origin_s = "Bosh-HM-Forwarder" Name_s = "System. zdrowy" Value_d = 0 | Liczba wyników > 0 | 1 oznacza, że system jest w dobrej kondycji i 0 oznacza, że system nie jest w dobrej kondycji. |
| Type = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d>0 | Liczba wyników > 0   | Consul emituje stan kondycji okresowo. 0 oznacza, że system jest w dobrej kondycji, a 1 oznacza, że nadajnik trasy wykryje, że Consul nie działa. |
| Type = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s = "TruncatingBuffer. DroppedMessages" lub Name_s = "Doppler. shedEnvelopes") Delta_d>0 | Liczba wyników > 0 | Różnicowa liczba komunikatów przypadkowo porzuconych przez Doppler ze względu na ciśnienie wsteczne. |
| Type = CF_LogMessage_CL SourceType_s = LGR MessageType_s = ERR                      | Liczba wyników > 0   | Loggregator emituje **LGR** w celu wskazywania problemów z procesem rejestrowania. Przykładem takiego problemu jest zbyt wysoka wartość wyjściowa komunikatu dziennika. |
| Type = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Liczba wyników > 0   | Gdy dysza odbiera powolnego alertu klienta z Loggregator, wysyła **slowConsumerAlert** ValueMetric do dzienników Azure monitor. |
| Type = CF_CounterEvent_CL Job_s = dysza Name_s eventsLost Delta_d>0              | Liczba wyników > 0   | Jeśli różnicowa liczba utraconych zdarzeń osiągnie próg, oznacza to, że dysza może mieć problem z uruchomieniem. |

## <a name="scale"></a>Skalowanie

Można skalować dyszę i Loggregator.

### <a name="scale-the-nozzle"></a>Skalowanie dysz

Należy zacząć od co najmniej dwóch wystąpień dyszy. Firehose dystrybuuje obciążenie na wszystkie wystąpienia dysz.
Aby upewnić się, że dysza może zachować ruch danych z Firehose, skonfiguruj alert **slowConsumerAlert** (wymieniony w poprzedniej sekcji "Tworzenie reguł alertów"). Po otrzymaniu alertu postępuj zgodnie ze [wskazówkami dotyczącymi powolnej dyszy](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) , aby określić, czy jest potrzebne skalowanie.
Aby skalować w górę dysz, użyj Menedżera aplikacji lub interfejsu wiersza polecenia CF, aby zwiększyć numery wystąpień lub zasoby pamięci lub dysku dla dysz.

### <a name="scale-the-loggregator"></a>Skalowanie Loggregator

Loggregator wysyła komunikat dziennika **LGR** w celu wskazania problemów z procesem rejestrowania. Można monitorować alert, aby określić, czy Loggregator musi być skalowane w górę.
Aby skalować w górę Loggregator, Zwiększ rozmiar buforu Doppler lub Dodaj kolejne wystąpienia serwera Doppler w manifeście CF. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące skalowania Loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualizacja

Aby zaktualizować dyszę przy użyciu nowszej wersji, Pobierz nową wersję z dyszą, wykonaj kroki opisane w poprzedniej sekcji "wdróż dyszę" i ponownie wypchnij aplikację.

### <a name="remove-the-nozzle-from-ops-manager"></a>Usuń dyszę z programu Operations Manager

1. Zaloguj się do programu Operations Manager.
2. Znajdź kafelek **Microsoft Azure log Analytics dysz for PCF** .
3. Wybierz ikonę elementów bezużytecznych i Potwierdź usunięcie.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Usuwanie dysz z komputera deweloperskiego

W oknie interfejsu wiersza polecenia CF wpisz:
```
cf delete <App Name> -r
```

Po usunięciu dysz dane w portalu pakietu OMS nie zostaną automatycznie usunięte. Wygasa to na podstawie ustawienia przechowywania dzienników Azure Monitor.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Usługa Azure Log Analytics dysza jest otwarta jako źródło. Wyślij swoje pytania i opinie do [sekcji GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Aby otworzyć żądanie pomocy technicznej platformy Azure, wybierz pozycję "maszyna wirtualna z systemem Cloud Foundry" jako kategorię usługi. 

## <a name="next-step"></a>Następny krok

W programie PCF 2.0 metryki wydajności maszyny wirtualnej są przesyłane do usługi Azure Log Analytics dyszy przez usługę przesyłania dalej metryk systemu i są zintegrowane z obszarem roboczym Log Analytics. Agent Log Analytics nie jest już potrzebny dla metryk wydajności maszyny wirtualnej. Można jednak nadal używać agenta Log Analytics do zbierania informacji dziennika systemu. Agent Log Analytics jest instalowany jako dodatek Bosh na maszynach wirtualnych CF. 

Aby uzyskać szczegółowe informacje, zobacz [wdrażanie agenta log Analytics w Cloud Foundry wdrożeniu](https://github.com/Azure/oms-agent-for-linux-boshrelease).