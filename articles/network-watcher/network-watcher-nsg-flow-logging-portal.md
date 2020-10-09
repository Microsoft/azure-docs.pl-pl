---
title: Rejestrowanie przepływu ruchu sieciowego do i z maszyny wirtualnej — samouczek — Azure Portal | Microsoft Docs
description: Dowiedz się, jak rejestrować przepływ ruchu sieciowego do i z maszyny wirtualnej przy użyciu możliwości dzienników przepływu sieciowej grupy zabezpieczeń usługi Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 89258b05831170ff502cde80577f3a6851659bf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986314"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Samouczek: rejestrowanie przepływu ruchu sieciowego do i z maszyny wirtualnej przy użyciu witryny Azure Portal

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)

Sieciowa grupa zabezpieczeń umożliwia filtrowanie ruchu przychodzącego do i wychodzącego z maszyny wirtualnej. Możesz rejestrować ruch sieciowy, który przepływa przez sieciową grupę zabezpieczeń z możliwością rejestrowania dziennika przepływu sieciowej grupy zabezpieczeń usługi Network Watcher. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej przy użyciu sieciowej grupy zabezpieczeń
> * Włączanie usługi Network Watcher i rejestrowanie dostawcy Microsoft.Insights
> * Włączanie dziennika przepływu ruchu dla sieciowej grupy zabezpieczeń przy użyciu możliwości dziennika przepływu sieciowej grupy zabezpieczeń usługi Network Watcher
> * Pobieranie zarejestrowanych danych
> * Wyświetlanie zarejestrowanych danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Compute**, a następnie wybierz pozycję **Windows Server 2016 Datacenter** lub wersję **Ubuntu Server**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myVm|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę **myResourceGroup**.|
    |Lokalizacja| Wybierz **Wschodnie stany USA**|

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **Ustawienia** zaakceptuj wszystkie wartości domyślne i wybierz przycisk **OK**.
6. W obszarze **Utwórz** na stronie **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej. Wdrożenie maszyny wirtualnej potrwa kilka minut. Zanim przejdziesz do pozostałych kroków, poczekaj na zakończenie wdrażania maszyny wirtualnej.

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie wykonuj pozostałych kroków do momentu zakończenia tworzenia maszyny wirtualnej. Gdy portal tworzy maszynę wirtualną, tworzy również sieciową grupę zabezpieczeń o nazwie **myVm-nsg** i kojarzy ją z interfejsem sieciowym maszyny wirtualnej.

## <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączoną usługę Network Watcher w regionie Wschodnie stany USA, przejdź do sekcji [Rejestrowanie dostawcy usługi Insights](#register-insights-provider).

1. W portalu wybierz pozycję **Wszystkie usługi**. W **polu filtru** wprowadź ciąg *Network Watcher*. Gdy **Network Watcher** pojawi się w wynikach, wybierz ją.
2. Wybierz węzeł **Regiony**, aby go rozwinąć, a następnie wybierz symbol **...** z prawej strony pozycji **Wschodnie stany USA**, jak pokazano na poniższej ilustracji:

    ![Włączanie usługi Network Watcher](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Wybierz pozycję **Włącz usługę Network Watcher**.

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Rejestrowanie przepływu sieciowej grupy zabezpieczeń wymaga dostawcy **Microsoft.Insights**. Aby zarejestrować dostawcę, wykonaj następujące kroki:

1. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi**. W polu Filtr wpisz ciąg *Subskrypcje*. Gdy pozycja **Subskrypcje** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Z listy subskrypcji wybierz subskrypcję, dla której chcesz włączyć dostawcę.
3. Wybierz pozycję **Dostawcy zasobów** w obszarze **USTAWIENIA**.
4. Sprawdź, czy pozycja **STAN** dla dostawcy **microsoft.insights** ma wartość **Zarejestrowany**, jak pokazano na rysunku poniżej. Jeśli stan ma wartość **Niezarejestrowany**, wybierz pozycję **Zarejestruj** z prawej strony dostawcy.

    ![Rejestrowanie dostawcy](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Włączanie dziennika przepływu sieciowej grupy zabezpieczeń

1. Dane dziennika przepływu sieciowej grupy zabezpieczeń są zapisywane na koncie usługi Azure Storage. Aby utworzyć konto usługi Azure Storage, wybierz pozycję **+ Utwórz zasób** w lewym górnym rogu portalu.
2. Wybierz pozycję **Storage**, a następnie wybierz pozycję **Konto usługi Storage — Blob, File, Table, Queue**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Utwórz**.

    | Ustawienie        | Wartość                                                        |
    | ---            | ---   |
    | Nazwa           | Od 3 do 24 znaków, może zawierać tylko małe litery i cyfry i musi być unikatowa dla wszystkich kont usługi Azure Storage.                                                               |
    | Lokalizacja       | Wybierz **Wschodnie stany USA**                                           |
    | Grupa zasobów | Wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję Moja **resourceName** . |

    Tworzenie konta usługi Storage może potrwać około minuty. Nie wykonuj pozostałych kroków, dopóki konto usługi Storage nie zostanie utworzone. We wszystkich przypadkach konto magazynu musi znajdować się w tym samym regionie co sieciowej grupy zabezpieczeń.
4. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi**. W polu **Filtr** wpisz ciąg *Network Watcher*. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
5. W obszarze **DZIENNIKI** wybierz pozycję **Dzienniki przepływu sieciowej grupy zabezpieczeń**, jak pokazano na poniższej ilustracji:

    ![Zrzut ekranu przedstawia dzienniki przepływu Network Watcher sieciowej grupy zabezpieczeń.](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. Z listy sieciowych grup zabezpieczeń wybierz grupę o nazwie **myVm-nsg**.
7. W obszarze **Ustawienia dzienników przepływu** wybierz pozycję **Wł.**
8. Wybierz wersję dzienników przepływu do rejestrowania. Wersja 2 zawiera statystykę przepływu/sesji (bajty i pakiety)

   ![Wybieranie wersji dzienników przepływu](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. Wybierz konto usługi Storage utworzone w kroku 3.
   > [!NOTE]
   > Dzienniki przepływu sieciowej grupy zabezpieczeń nie działają w przypadku kont magazynu, które mają włączoną [hierarchiczną przestrzeń nazw](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) .
1. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi**. W polu **Filtr** wpisz ciąg *Network Watcher*. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
10. Ustaw pozycję **Przechowywanie (dni)** na 5, a następnie wybierz pozycję **Zapisz**.

## <a name="download-flow-log"></a>Pobieranie dziennika przepływu

1. Z poziomu usługi Network Watcher w portalu wybierz pozycję **Dzienniki przepływów sieciowych grup zabezpieczeń** w obszarze **DZIENNIKI**.
2. Wybierz pozycję **Możesz pobierać dzienniki przepływu ze skonfigurowanych kont usługi Storage**, jak pokazano na poniższej ilustracji:

   ![Pobieranie dzienników przepływu](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Wybierz konto usługi Storage skonfigurowane w kroku 2 sekcji [Włączanie dziennika przepływu sieciowej grupy zabezpieczeń](#enable-nsg-flow-log).
4. W obszarze **BLOB Service**wybierz pozycję **kontenery**, a następnie wybierz kontener **Insights-Logs-networksecuritygroupflowevent** .
5. W kontenerze przejdź do hierarchii folderów do momentu uzyskania PT1H.jsw pliku, jak pokazano na poniższej ilustracji. Pliki dziennika są zapisywane w hierarchii folderów, która następuje po następującej konwencji nazewnictwa: https://{storageAccountName}. blob. Core. Windows. NET/Insights-Logs-networksecuritygroupflowevent/resourceId =/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y = {Year}/m = {miesiąc}/d = {Day}/h = {Hour}/m = 00/macAddress = {macAddress}/PT1H.json

   ![Dziennik przepływu](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. Wybierz symbol **...** z prawej strony pliku PT1H.json i wybierz pozycję **Pobierz**.

## <a name="view-flow-log"></a>Wyświetlanie dziennika przepływu

Poniższy kod JSON to przykład kodu widocznego w pliku PT1H.json dla każdego przepływu, w którym są rejestrowane dane:

### <a name="version-1-flow-log-event"></a>Zdarzenie dziennika przepływu w wersji 1
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>Zdarzenie dziennika przepływu w wersji 2
```json
{
    "time": "2018-11-13T12:00:35.3899262Z",
    "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 2,
        "flows": [
            {
                "rule": "DefaultRule_DenyAllInBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                            "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                            "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                        ]
                    }
                ]
            },
            {
                "rule": "DefaultRule_AllowInternetOutBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                            "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                            "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                            "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                        ]
                    }
                ]
            }
        ]
    }
}
```

Wartość elementu **mac** w poprzednich danych wyjściowych to adres MAC interfejsu sieciowego, który został utworzony podczas tworzenia maszyny wirtualnej. Informacje rozdzielone przecinkami dotyczące elementu **flowTuples** są następujące:

| Przykładowe dane | Co reprezentują dane   | Objaśnienie                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | Znacznik czasu             | Znacznik czasu wystąpienia przepływu w formacie EPOCH systemu UNIX. W poprzednim przykładzie data zmienia się na 1 maja 2018 o 14:59:05 GMT.                                                                                    |
| 10.0.0.4  | Źródłowy adres IP      | Źródłowy adres IP, z którego był zainicjowany przepływ. 10.0.0.4 to prywatny adres IP maszyny wirtualnej utworzonej w sekcji [Tworzenie maszyny wirtualnej](#create-a-vm).
| 13.67.143.118     | Docelowy adres IP | Docelowy adres IP, do którego był skierowany przepływ.                                                                                  |
| 44931        | Port źródłowy            | Port źródłowy, z którego pochodził przepływ.                                           |
| 443         | Port docelowy       | Port docelowy, do którego był skierowany przepływ. Ponieważ ruch był przeznaczony do portu 443, reguła o nazwie **UserRule_default-allow-rdp** w pliku dziennika przetworzyła przepływ.                                                |
| T            | Protokół               | Określa rodzaj protokołu przepływu: TCP (T) lub UDP (U).                                  |
| O            | Kierunek              | Określa, czy ruch był przychodzący (I), czy wychodzący (O).                                     |
| A            | Akcja                 | Określa, czy ruch był dozwolony (A), czy odrzucony (D).  
| C            | Stan przepływu **tylko w wersji 2** | Rejestruje stan przepływu. Możliwe stany to **B**: początek — gdy zostanie utworzony przepływ. Statystyki nie są podawane. **C**: kontynuacja — dotyczy ciągłego przepływu. Statystyki są podawane w 5-minutowych odstępach. **E**: koniec — gdy przepływ zostanie zakończony. Statystyki są podawane. |
| 30 | Wysłane pakiety — ze źródła do miejsca docelowego **tylko w wersji 2** | Całkowita liczba pakietów TCP lub UDP przesłanych ze źródła do miejsca docelowego od czasu ostatniej aktualizacji. |
| 16978 | Wysłane bajty — ze źródła do miejsca docelowego **tylko w wersji 2** | Całkowita liczba bajtów pakietów TCP lub UDP przesłanych ze źródła do miejsca docelowego od czasu ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu. |
| 24 | Wysłane pakiety — z miejsca docelowego do źródła **tylko w wersji 2** | Całkowita liczba pakietów TCP lub UDP przesłanych z miejsca docelowego do źródła od czasu ostatniej aktualizacji. |
| 14008| Wysłane bajty — z miejsca docelowego do źródła **tylko w wersji 2** | Całkowita liczba bajtów pakietów TCP i UDP przesłanych z miejsca docelowego do źródła od czasu ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.|

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób włączania rejestrowania przepływu sieciowej grupy zabezpieczeń dla danej grupy. Przedstawiono również sposób pobierania i wyświetlania danych rejestrowanych w pliku. Nieprzetworzone dane w pliku JSON mogą być trudne do zinterpretowania. Aby wizualizować dane dzienników przepływów, możesz użyć [platformy Azure Analiza ruchu](traffic-analytics.md), [programu Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)i innych narzędzi. Możesz wypróbować alternatywne metody włączania dzienników przepływu sieciowej grupy zabezpieczeń, takich jak [PowerShell](network-watcher-nsg-flow-logging-powershell.md), interfejs [wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md), interfejsy [API REST](network-watcher-nsg-flow-logging-rest.md) i [Szablony ARM](network-watcher-nsg-flow-logging-azure-resource-manager.md).
