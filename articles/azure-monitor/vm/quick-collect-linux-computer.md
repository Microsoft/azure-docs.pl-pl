---
title: 'Szybki Start: zbieranie danych z hybrydowego komputera z systemem Linux za pomocą Azure Monitor'
description: W tym przewodniku szybki start dowiesz się, jak wdrożyć agenta Log Analytics dla komputerów z systemem Linux działających poza platformą Azure i włączyć zbieranie danych przy użyciu dzienników Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: dbe51930ec92ec4f89738dc5d543003f45acebf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733826"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Szybki Start: zbieranie danych z komputera z systemem Linux w środowisku hybrydowym z Azure Monitor

[Azure monitor](../overview.md) może zbierać dane bezpośrednio z fizycznych lub wirtualnych komputerów z systemem Linux w środowisku do log Analytics obszaru roboczego w celu uzyskania szczegółowej analizy i korelacji. Zainstalowanie [agenta log Analytics](../agents/log-analytics-agent.md) pozwala Azure monitor zbierać dane z centrum danych lub innego środowiska chmury. Ten przewodnik Szybki Start przedstawia sposób konfigurowania i zbierania danych z serwera z systemem Linux przy użyciu kilku prostych kroków. Aby uzyskać informacje o maszynach wirtualnych z systemem Linux systemu Azure, zobacz [zbieranie danych o maszynach wirtualnych platformy Azure](./quick-collect-azurevm.md).  

Aby zrozumieć obsługiwaną konfigurację, zobacz [obsługiwane systemy operacyjne](../agents/agents-overview.md#supported-operating-systems) i [Konfiguracja zapory sieciowej](../agents/log-analytics-agent.md#network-requirements).
 
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.

    ![Znajdowanie obszaru roboczego Log Analytics w Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Wybierz pozycję **Utwórz**, a następnie wybierz opcje dla następujących elementów:

   * Podaj nazwę nowego **obszaru roboczego log Analytics**, na przykład *DefaultLAWorkspace*.  
   * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
   * W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów, która zawiera co najmniej jedną maszynę wirtualną platformy Azure.  
   * W polu **Lokalizacja** wybierz lokalizację, w której wdrożono maszyny wirtualne.  Aby uzyskać dodatkowe informacje, sprawdź, w których [regionach jest dostępna usługa Log Analytics](https://azure.microsoft.com/regions/services/).
   * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  W przypadku tworzenia obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją umowy EA wybierz preferowaną warstwę cenową.  Aby uzyskać dodatkowe informacje dotyczące konkretnej warstwy, zobacz [Log Analytics — cennik](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Tworzenie obszaru roboczego Log Analytics w Azure Portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Po podania wymaganych informacji w okienku **obszaru roboczego log Analytics** wybierz pozycję **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego

Przed zainstalowaniem agenta usługi Log Analytics dla systemu Linux potrzebne są identyfikator i klucz obszaru roboczego usługi Log Analytics. Te informacje są wymagane przez skrypt otoki agenta w celu poprawnego skonfigurowania agenta i upewnienia się, że może on pomyślnie komunikować się z Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. W lewym górnym rogu Azure Portal wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź **log Analytics**. Podczas wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz **log Analytics obszary robocze**.

2. Na liście obszarów roboczych Log Analytics wybierz utworzony wcześniej obszar roboczy. (Może być nazwany IT **DefaultLAWorkspace**).

3. Wybierz pozycję **Zarządzanie agentami**:
 
4. Następnie wybierz pozycję **serwery z systemem Linux**.

5. Wartość z prawej strony **identyfikatora obszaru roboczego** i **klucza podstawowego**. Skopiuj i wklej obie wartości do ulubionego edytora.

## <a name="install-the-agent-for-linux"></a>Instalacja agenta dla systemu Linux

Następujące kroki umożliwiają konfigurację agenta usługi Log Analytics na platformie Azure i w chmurze Azure Government.  

>[!NOTE]
>Agenta usługi Log Analytics dla systemu Linux nie można skonfigurować w taki sposób, aby przesyłał raporty do więcej niż jednego obszaru roboczego usługi Log Analytics.  

Jeśli komputer z systemem Linux musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, konfigurację serwera proxy można określić w wierszu polecenia przez dołączenie elementu `-p [protocol://][user:password@]proxyhost[:port]`.  Właściwość *proxyhost* akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP serwera proxy. 

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

1. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym Log Analytics, uruchom następujące polecenie, podając wcześniej skopiowany identyfikator obszaru roboczego i klucz podstawowy. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Następujące polecenie zawiera `-p` parametr proxy i składnię przykładową, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Aby skonfigurować komputer z systemem Linux do łączenia się z obszarem roboczym Log Analytics w Azure Government chmurze, uruchom następujące polecenie, podając wcześniej skopiowany identyfikator obszaru roboczego i klucz podstawowy. To polecenie pobiera agenta, weryfikuje jego sumę kontrolną i instaluje go. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Następujące polecenie zawiera `-p` parametr proxy i składnię przykładową, gdy uwierzytelnianie jest wymagane przez serwer proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Uruchom ponownie agenta, uruchamiając następujące polecenie: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Zbieranie danych zdarzeń i wydajności

Azure Monitor może zbierać zdarzenia z dzienników systemu Linux i liczników wydajności określonych na potrzeby analizy i raportowania w dłuższym czasie. Może również podejmować działania po wykryciu określonego warunku. Wykonaj następujące kroki, aby skonfigurować zbieranie zdarzeń z dziennika Syslog systemu Linux oraz na początek z kilku typowych liczników wydajności.  

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz Log Analytics. Podczas wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz **log Analytics obszary robocze** i na liście log Analytics obszarów roboczych wybierz obszar roboczy, którego szukasz, a następnie wybierz pozycję **Ustawienia zaawansowane** w obszarze roboczym **log Analytics** .

2. Wybierz pozycję **dane**, a następnie wybierz pozycję **Dziennik** systemowy.  

3. Aby dodać dziennik systemowy, wpisz nazwę dziennika. Wprowadź **Dziennik** systemowy, a następnie wybierz znak plus **+** .  

4. W tabeli usuń zaznaczenie ważności **Informacja**, **Powiadomienie** i **Debugowanie**. 

5. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać konfigurację.

6. Wybierz pozycję **Dane wydajności systemu Linux**, aby włączyć zbieranie liczników wydajności na komputerze z systemem Linux. 

7. Podczas pierwszej konfiguracji liczników wydajności systemu Linux dla nowego obszaru roboczego usługi Log Analytics można szybko utworzyć kilka typowych liczników. Na liście obok każdego z nich znajduje się pole wyboru.

    ![Domyślne liczniki wydajności systemu Linux wybrane w Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Wybierz pozycję **Zastosuj poniższą konfigurację do moich maszyn** , a następnie wybierz pozycję **Dodaj wybrane liczniki wydajności**. Są one dodawane i ustawiane wstępnie z dziesięciosekundowym interwałem próbkowania kolekcji.  

8. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać konfigurację.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych

Teraz, po włączeniu zbierania danych, uruchomimy proste przykładowe wyszukiwanie w dziennikach, aby wyświetlić dane z docelowego komputera.  

1. W wybranym obszarze roboczym w okienku po lewej stronie wybierz pozycję **dzienniki**.

2. Na stronie kwerendy dzienników wpisz `Perf` w edytorze zapytań i wybierz polecenie **Uruchom**.
 
    ![Log Analytics przeszukiwania dzienników](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    Na przykład zapytanie na poniższej ilustracji zwróciło 10 000 rekordów wydajności. W Twoim przypadku wyników będzie znacznie mniej.

    ![Wynik przeszukiwania dzienników w usłudze Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebny, można usunąć agenta z komputera z systemem Linux i usunąć obszar roboczy usługi Log Analytics.  

Aby usunąć agenta, uruchom poniższe polecenie na komputerze z systemem Linux. Argument *--purge* powoduje całkowite usunięcie agenta i jego konfiguracji.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Aby usunąć obszar roboczy, wybierz utworzony wcześniej obszar roboczy Log Analytics i na stronie zasób wybierz pozycję **Usuń**.

![Usuwanie zasobu usługi Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy są zbierane dane operacyjne i dane wydajności z lokalnego komputera z systemem Linux, możesz w prosty sposób rozpocząć eksplorowanie i analizowanie danych oraz wykonywanie działań na *bezpłatnie* zebranych danych.  

Aby dowiedzieć się, jak wyświetlać i analizować dane, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wyświetlanie i analizowanie danych w usłudze Log Analytics](../logs/log-analytics-tutorial.md)