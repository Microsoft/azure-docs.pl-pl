---
title: Jak sklonować usługę Azure IoT Hub
description: Jak sklonować usługę Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 370ea2f16632ae18142f0770742e5a52d3cabae0
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151657"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Jak sklonować usługę Azure IoT Hub do innego regionu

W tym artykule przedstawiono sposoby klonowania IoT Hub i podano pytania, które należy odpowiedzieć przed rozpoczęciem. Oto kilka powodów, dla których warto sklonować Centrum IoT:
 
* Przenosisz firmę z jednego regionu do innego, na przykład z Europy do Ameryka Północna (lub na odwrót), i chcesz, aby zasoby i dane były geograficznie blisko nowej lokalizacji, więc musisz przenieść centrum.

* Konfigurujesz centrum dla środowiska deweloperskiego i produkcyjnego.

* Chcesz wykonać niestandardową implementację wysokiej dostępności na wiele centrów. Aby uzyskać więcej informacji, zobacz [sekcję jak osiągnąć międzyregionową HA IoT Hub wysokiej dostępności i odzyskiwania po awarii](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Chcesz zwiększyć liczbę [partycji](iot-hub-scaling.md#partitions) skonfigurowanych dla centrum. Ta wartość jest ustawiana podczas pierwszego tworzenia centrum i nie można jej zmienić. Korzystając z informacji podanych w tym artykule, można sklonować centrum i po utworzeniu klonu zwiększyć liczbę partycji.

Aby sklonować centrum, potrzebna jest subskrypcja z dostępem administracyjnym do oryginalnego centrum. Nowe centrum można umieścić w nowej grupie zasobów i regionie, w tej samej subskrypcji co oryginalne centrum, a nawet w nowej subskrypcji. Nie można użyć tej samej nazwy, ponieważ nazwa centrum musi być globalnie unikatowa.

> [!NOTE]
> Obecnie nie jest dostępna funkcja klonowania Centrum IoT Hub automatycznie. Jest to przede wszystkim proces ręczny i dlatego jest dość podatne na błędy. Złożoność klonowania centrum jest bezpośrednio proporcjonalna do złożoności centrum. Na przykład klonowanie Centrum IoT bez routingu wiadomości jest dość proste. Po dodaniu routingu wiadomości jako tylko jednej złożoności klonowanie centrum będzie miało co najmniej kolejność bardziej skomplikowane. Jeśli również przeniesiesz zasoby używane dla punktów końcowych routingu, jest to kolejna kolejność magniture bardziej skomplikowane. 

## <a name="things-to-consider"></a>Rzeczy do rozważenia

Przed sklonowaniem Centrum IoT należy wziąć pod uwagę kilka kwestii.

* Upewnij się, że wszystkie funkcje dostępne w oryginalnej lokalizacji są również dostępne w nowej lokalizacji. Niektóre usługi są w wersji zapoznawczej, a nie wszystkie funkcje są dostępne wszędzie.

* Nie usuwaj oryginalnych zasobów przed utworzeniem i zweryfikowaniem sklonowanej wersji. Po usunięciu centrum zostanie ono usunięte w nieskończoność i nie ma możliwości odzyskania go, aby sprawdzić ustawienia lub dane, aby upewnić się, że koncentrator jest replikowany prawidłowo.

* Wiele zasobów wymaga globalnie unikatowych nazw, dlatego należy użyć różnych nazw dla sklonowanych wersji. Należy również użyć innej nazwy dla grupy zasobów, do której należy sklonowany centrum. 

* Nie przeprowadzono migracji danych dla oryginalnego Centrum IoT Hub. Dotyczy to komunikatów telemetrycznych, poleceń z chmury do urządzeń (C2D) i informacji związanych z zadaniami, takich jak harmonogramy i historia. Nie są również migrowane wyniki pomiarów i rejestrowania. 

* W przypadku danych lub komunikatów kierowanych do usługi Azure Storage można pozostawić dane na oryginalnym koncie magazynu, przenieść je na nowe konto magazynu w nowym regionie lub pozostawić stare dane oraz utworzyć nowe konto magazynu w nowej lokalizacji dla nowych danych. Aby uzyskać więcej informacji na temat przemieszczania danych w usłudze BLOB Storage, zobacz Wprowadzenie [do AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Nie można migrować danych dla Event Hubs i dla Service Bus tematów i kolejek. Jest to dane wskazywane w czasie i nie są przechowywane po przetworzeniu komunikatów.

* Należy zaplanować przestój dla migracji. Trwa klonowanie urządzeń do nowego centrum. Jeśli używasz metody Import/Export, testowanie testów porównawczych wykazało, że może upłynąć około dwóch godzin, aby przenieść 500 000 urządzeń i cztery godziny na przeniesienie milionów urządzeń. 

* Urządzenia można skopiować do nowego centrum bez zamykania lub zmiany urządzeń. 

    * Jeśli urządzenia zostały pierwotnie zainicjowane przy użyciu usługi DPS, ponowne Inicjowanie obsługi administracyjnej aktualizuje informacje o połączeniu przechowywane na poszczególnych urządzeniach. 
    
    * W przeciwnym razie należy użyć metody Import/Export w celu przeniesienia urządzeń, a następnie należy zmodyfikować urządzenia, aby korzystały z nowego centrum. Na przykład można skonfigurować urządzenie, aby korzystało z nazwy hosta IoT Hub z żądanych właściwości z przędzą. Urządzenie podejmie IoT Hub nazwę hosta, odłączy urządzenie ze starego centrum i ponownie nawiąże połączenie z nowym.
    
* Należy zaktualizować wszystkie używane certyfikaty, aby można było używać ich z nowymi zasobami. Istnieje również możliwość, że centrum zostało zdefiniowane w tabeli DNS w innym miejscu — należy zaktualizować te informacje DNS.

## <a name="methodology"></a>Metodologia

Jest to ogólna Metoda, która zalecamy przeniesienie Centrum IoT z jednego regionu do innego. W przypadku routingu wiadomości zakłada się, że zasoby nie są przenoszone do nowego regionu. Aby uzyskać więcej informacji, zobacz [sekcję dotyczącą routingu komunikatów](#how-to-handle-message-routing).

   1. Wyeksportuj centrum i jego ustawienia do szablonu Menedżer zasobów. 
   
   1. Wprowadź niezbędne zmiany w szablonie, takie jak aktualizowanie wszystkich wystąpień nazwy i lokalizacji sklonowanego centrum. W przypadku wszystkich zasobów w szablonie używanym do punktów końcowych routingu wiadomości zaktualizuj klucz w szablonie dla tego zasobu.
   
   1. Zaimportuj szablon do nowej grupy zasobów w nowej lokalizacji. Spowoduje to utworzenie klonu.

   1. Debuguj zgodnie z wymaganiami. 
   
   1. Dodaj wszystkie elementy, które nie zostały wyeksportowane do szablonu. 
   
       Na przykład grupy konsumentów nie są eksportowane do szablonu. Musisz ręcznie dodać grupy konsumentów do szablonu lub użyć [Azure Portal](https://portal.azure.com) po utworzeniu centrum. Istnieje przykład dodawania jednej grupy odbiorców do szablonu w artykule [Użyj szablonu Azure Resource Manager, aby skonfigurować IoT Hub Routing komunikatów](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Skopiuj urządzenia z oryginalnego centrum do klonowania. Zostało to omówione w sekcji [Zarządzanie urządzeniami zarejestrowanymi w usłudze IoT Hub](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Jak obsługiwać routing wiadomości

Jeśli koncentrator używa [niestandardowego routingu](iot-hub-devguide-messages-read-custom.md), wyeksportowanie szablonu centrum obejmuje konfigurację routingu, ale nie obejmuje samych zasobów. Musisz wybrać, czy chcesz przenieść zasoby routingu do nowej lokalizacji, czy pozostawić je w miejscu i kontynuować ich używanie "zgodnie z oczekiwaniami". 

Załóżmy na przykład, że masz centrum w regionie zachodnie stany USA, które rozsyła komunikaty do konta magazynu (również w zachodnich Stanach Zjednoczonych) i chcesz przenieść centrum do regionu Wschodnie stany USA. Możesz przenieść centrum i nadal kierować komunikaty do konta magazynu w regionie zachodnie stany USA lub przenieść centrum, a także przenieść konto magazynu. W innym regionie może wystąpić niewielka wydajność komunikatów routingu do zasobów punktów końcowych.

Centrum, które korzysta z routingu wiadomości, można łatwo przenieść, jeśli nie będzie też można przenieść zasobów używanych dla punktów końcowych routingu. 

Jeśli centrum używa routingu komunikatów, dostępne są dwie opcje. 

1. Przenieś zasoby używane dla punktów końcowych routingu do nowej lokalizacji.

    * Nowe zasoby należy tworzyć ręcznie w [Azure Portal](https://portal.azure.com) lub przy użyciu szablonów Menedżer zasobów. 

    * Należy zmienić nazwy wszystkich zasobów podczas ich tworzenia w nowej lokalizacji, ponieważ mają one unikatowe nazwy. 
     
    * Przed utworzeniem nowego centrum należy zaktualizować nazwy zasobów i klucze zasobów w szablonie nowego centrum. Zasoby powinny być obecne podczas tworzenia nowego centrum.

1. Nie przenoś zasobów używanych dla punktów końcowych routingu. Użyj ich "w miejscu".

   * W kroku, w którym edytujesz szablon, musisz pobrać klucze dla każdego zasobu routingu i umieścić je w szablonie przed utworzeniem nowego centrum. 

   * Centrum nadal odwołuje się do oryginalnych zasobów routingu i kieruje do nich komunikaty zgodnie z konfiguracją.

   * Zostanie osiągnięta Mała wydajność, ponieważ zasoby centrum i routingu nie znajdują się w tej samej lokalizacji.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Przygotowanie do migracji centrum do innego regionu

Ta sekcja zawiera szczegółowe instrukcje dotyczące migracji centrum.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Znajdź oryginalny centrum i wyeksportuj go do szablonu zasobu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Przejdź do pozycji **grupy zasobów** i wybierz grupę zasobów zawierającą centrum, które chcesz przenieść. Możesz również przejść do **zasobów** i znaleźć centrum. Wybierz centrum.

1. Wybierz pozycję **Eksportuj szablon** z listy właściwości i ustawień centrum. 

   ![Zrzut ekranu przedstawiający polecenie eksportowania szablonu dla IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Wybierz pozycję **Pobierz** , aby pobrać szablon. Zapisz plik w miejscu, w którym można go znaleźć ponownie. 

   ![Zrzut ekranu przedstawiający polecenie pobierania szablonu dla IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Wyświetlanie szablonu 

1. Przejdź do folderu pliki do pobrania (lub do folderu, który został użyty podczas eksportowania szablonu) i Znajdź plik zip. Otwórz plik zip i Znajdź plik o nazwie `template.json` . Wybierz go, a następnie naciśnij klawisze CTRL + C, aby skopiować szablon. Przejdź do innego folderu, który nie znajduje się w pliku zip, i wklej plik (Ctrl + V). Teraz można go edytować.
 
    Poniższy przykład dotyczy centrum ogólnego bez konfiguracji routingu. Jest to koncentrator warstwy S1 (z 1 jednostką) o nazwie **ContosoTestHub29358** w regionie **zachodnie**. Oto wyeksportowany szablon.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Edytowanie szablonu 

Musisz wprowadzić pewne zmiany, zanim będzie można użyć szablonu do utworzenia nowego centrum w nowym regionie. Aby edytować szablon, użyj [vs Code](https://code.visualstudio.com) lub edytora tekstów.

#### <a name="edit-the-hub-name-and-location"></a>Edytuj nazwę i lokalizację centrum

1. Usuń sekcję parametrów u góry — znacznie łatwiej jest użyć nazwy centrum, ponieważ nie będziemy mieć wielu parametrów. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Zmień nazwę, tak aby używała rzeczywistej nazwy (nowej), zamiast pobierać ją z parametru (który został usunięty w poprzednim kroku). 

    Dla nowego centrum Użyj nazwy oryginalnego centrum oraz *klona* ciągu, aby utworzyć nową nazwę. Zacznij od oczyszczenia nazwy centrum i lokalizacji.
    
    Stara wersja:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nowa wersja: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Następnie sprawdź, czy wartości **Path** zawierają starą nazwę centrum. Zmień je, aby użyć nowej. Są to wartości ścieżki w obszarze **eventHubEndpoints** o nazwie **Events** i **OperationsMonitoringEvents**.

    Gdy skończysz, sekcja punkty końcowe centrum zdarzeń powinna wyglądać następująco:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Aktualizowanie kluczy dla zasobów routingu, które nie są przenoszone

Po wyeksportowaniu szablonu Menedżer zasobów dla centrum z skonfigurowanym routingiem zobaczysz, że klucze tych zasobów nie są dostarczane w wyeksportowanym szablonie — ich umieszczenie jest wskazywane przez gwiazdki. Należy je wypełnić, przechodząc do tych zasobów w portalu i pobierając klucze **przed** zaimportowaniem szablonu nowego centrum i utworzeniem centrum. 

1. Pobierz klucze wymagane dla dowolnego z zasobów routingu i umieść je w szablonie. Możesz pobrać klucze z zasobu w [Azure Portal](https://portal.azure.com). 

   Na przykład, jeśli przesyłasz komunikaty do kontenera magazynu, Znajdź konto magazynu w portalu. W sekcji Ustawienia wybierz pozycję **klucze dostępu**, a następnie skopiuj jeden z kluczy. Oto, jak wygląda klucz podczas pierwszego eksportowania szablonu:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Po pobraniu klucza konta dla konta magazynu umieść go w szablonie w klauzuli `AccountKey=****` w miejscu gwiazdki. 

1. W przypadku kolejek usługi Service Bus Pobierz klucz dostępu współdzielonego pasujący do SharedAccessKeyName. Oto klucz i `SharedAccessKeyName` kod JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. To samo dotyczy tematów Service Bus i połączeń centrów zdarzeń.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Utwórz nowe zasoby routingu w nowej lokalizacji

Ta sekcja ma zastosowanie tylko w przypadku przesuwania zasobów używanych przez centrum dla punktów końcowych routingu.

Jeśli chcesz przenieść zasoby routingu, musisz ręcznie skonfigurować zasoby w nowej lokalizacji. Możesz utworzyć zasoby routingu za pomocą [Azure Portal](https://portal.azure.com)lub wyeksportować szablon Menedżer zasobów dla każdego z zasobów używanych przez routing wiadomości, edytując je i importując. Po skonfigurowaniu zasobów można zaimportować szablon centrum (obejmujący konfigurację routingu).

1. Utwórz każdy zasób używany przez Routing. Można to zrobić ręcznie przy użyciu [Azure Portal](https://portal.azure.com)lub utworzyć zasoby przy użyciu szablonów Menedżer zasobów. Jeśli chcesz używać szablonów, wykonaj następujące czynności:

    1. Dla każdego zasobu używanego w ramach routingu wyeksportuj go do szablonu Menedżer zasobów.
    
    1. Zaktualizuj nazwę i lokalizację zasobu. 

    1. Zaktualizuj wszystkie odwołania krzyżowe do zasobów. Na przykład jeśli utworzysz szablon dla nowego konta magazynu, musisz zaktualizować nazwę konta magazynu w tym szablonie i dowolny inny szablon, który odwołuje się do niego. W większości przypadków sekcja Routing w szablonie centrum jest jedynym innym szablonem, który odwołuje się do zasobu. 

    1. Zaimportuj każdy z szablonów, który wdraża każdy zasób.

    Po skonfigurowaniu i uruchomieniu zasobów używanych przez usługę Routing można kontynuować.

1. W szablonie Centrum IoT Zmień nazwę każdego z zasobów routingu na nową nazwę i w razie potrzeby zaktualizuj lokalizację. 

Teraz masz szablon, który spowoduje utworzenie nowego centrum, które będzie wyglądało niemal dokładnie tak samo jak w przypadku starego centrum, w zależności od tego, jak postanowiono obsługiwać Routing.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Przenieś — Utwórz nowe centrum w nowym regionie, ładując szablon

Utwórz nowe centrum w nowej lokalizacji przy użyciu szablonu. W przypadku zasobów routingu, które mają zostać przeniesione, zasoby powinny zostać skonfigurowane w nowej lokalizacji, a odwołania w szablonie zaktualizowane do dopasowania. Jeśli nie przenosisz zasobów routingu, powinny one znajdować się w szablonie z zaktualizowanymi kluczami.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**. 

1. W polu wyszukiwania wprowadź ciąg "wdrożenie szablonu" i wybierz polecenie wprowadź.

1. Wybierz pozycję **wdrożenie szablonu (Wdróż przy użyciu szablonów niestandardowych)**. Spowoduje to przejście do ekranu dla Template deployment. Wybierz pozycję **Utwórz**. Zostanie wyświetlony następujący ekran:

   ![Zrzut ekranu przedstawiający polecenie tworzenia własnego szablonu](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Wybierz opcję **Kompiluj własny szablon w edytorze**, który umożliwia przekazywanie szablonu z pliku. 

1. Wybierz pozycję **Załaduj plik**. 

   ![Zrzut ekranu przedstawiający polecenie przekazania pliku szablonu](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Wyszukaj nowy edytowany szablon i zaznacz go, a następnie wybierz pozycję **Otwórz**. Ładuje szablon w oknie edycji. Wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu przedstawiający ładowanie szablonu](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Wypełnij poniższe pola.

   **Subskrypcja**: wybierz subskrypcję do użycia.

   **Grupa zasobów**: Utwórz nową grupę zasobów w nowej lokalizacji. Jeśli masz już nową konfigurację, możesz ją wybrać zamiast tworzyć nowe.

   **Lokalizacja**: w przypadku wybrania istniejącej grupy zasobów jest ona wypełniana w celu dopasowania do lokalizacji grupy zasobów. Jeśli została utworzona nowa grupa zasobów, będzie ona jej lokalizacją.

   **Zgadzam się pole wyboru**: oznacza to, że wyrażasz zgodę na zapłacenie za tworzone zasoby.

1. Wybierz przycisk **Kup** .

Portal teraz sprawdza poprawność szablonu i wdraża sklonowane centrum. Jeśli masz dane konfiguracji routingu, zostaną one uwzględnione w nowym centrum, ale będą wskazywały zasoby w poprzedniej lokalizacji.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Zarządzanie urządzeniami zarejestrowanymi w usłudze IoT Hub

Po ukończeniu klonowania i uruchomienia należy skopiować wszystkie urządzenia z oryginalnego centrum do klona. 

Istnieje wiele sposobów osiągnięcia tego celu. Pierwotnie była używana [Usługa Device Provisioning (DPS)](../iot-dps/about-iot-dps.md)do udostępniania urządzeń lub nie została ona zastosowana. Jeśli tak, to nie jest trudne. Jeśli tego nie zrobiono, może to być bardzo skomplikowane. 

Jeśli nie korzystasz z usługi DPS do aprowizacji urządzeń, możesz pominąć następną sekcję i rozpocząć korzystanie z usługi [Import/Export w celu przeniesienia urządzeń do nowego centrum](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Ponowne Inicjowanie obsługi administracyjnej urządzeń w nowym centrum przy użyciu usługi DPS

Aby użyć funkcji DPS do przenoszenia urządzeń do nowej lokalizacji, zobacz [jak ponownie zainicjować obsługę administracyjną urządzeń](../iot-dps/how-to-reprovision.md). Po zakończeniu możesz wyświetlić urządzenia w [Azure Portal](https://portal.azure.com) i sprawdzić, czy znajdują się one w nowej lokalizacji.

Przejdź do nowego centrum przy użyciu [Azure Portal](https://portal.azure.com). Wybierz centrum, a następnie wybierz pozycję **urządzenia IoT**. Na sklonowanym centrum są wyświetlane urządzenia, które zostały wstępnie zainicjowane. Możesz również wyświetlić właściwości sklonowanego centrum. 

Jeśli wdrożono usługę Routing, przetestuj ją i upewnij się, że komunikaty są poprawnie kierowane do zasobów.

### <a name="committing-the-changes-after-using-dps"></a>Zatwierdzanie zmian po użyciu usługi DPS

Ta zmiana została zatwierdzona przez usługę DPS.

### <a name="rolling-back-the-changes-after-using-dps"></a>Wycofywanie zmian po użyciu usługi DPS. 

Aby wycofać zmiany, należy ponownie zainicjować obsługę administracyjną urządzeń z nowego centrum.

Teraz zakończono Migrowanie centrum i jego urządzeń. Możesz pominąć, aby [oczyścić](#clean-up).

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Przenoszenie urządzeń do nowego centrum przy użyciu Import-Export

Aplikacja jest przeznaczona dla platformy .NET Core, więc można ją uruchomić w systemie Windows lub Linux. Możesz pobrać przykład, pobrać parametry połączenia, ustawić flagi, dla których mają być uruchamiane usługi, i uruchomić je. Można to zrobić bez konieczności otwierania kodu.

### <a name="downloading-the-sample"></a>Pobieranie przykładu

1. Na tej stronie są używane przykłady IoT C#: [przykłady usługi Azure IoT dla języka c#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Pobierz plik zip i rozpakuj go na komputerze. 

1. Odpowiedni kod jest w./iot-hub/Samples/service/ImportExportDevicesSample. Nie musisz wyświetlać ani edytować kodu w celu uruchomienia aplikacji.

1. Aby uruchomić aplikację, określ trzy parametry połączenia i pięć opcji. Te dane są przekazywane jako argumenty wiersza polecenia lub używane są zmienne środowiskowe lub używają kombinacji dwóch. Będziemy przekazywać opcje w jako argumenty wiersza polecenia i parametry połączenia jako zmienne środowiskowe. 

   Przyczyną tego problemu jest to, że parametry połączenia są długie i nieuzasadnione i nie można ich zmienić, ale może zajść potrzeba zmiany opcji i uruchomienia aplikacji więcej niż raz. Aby zmienić wartość zmiennej środowiskowej, należy zamknąć okno poleceń i program Visual Studio lub VS Code, w zależności od tego, co jest używane. 

### <a name="options"></a>Opcje

Poniżej przedstawiono pięć opcji, które można określić podczas uruchamiania aplikacji. Umieścimy je w wierszu polecenia w ciągu minuty.

*   adddevices (argument 1) — Ustaw tę wartość na **true (prawda** ), jeśli chcesz dodać urządzenia wirtualne, które są dla Ciebie generowane. Są one dodawane do centrum źródłowego. Ponadto ustaw **numToAdd** (argument 2), aby określić liczbę urządzeń, które chcesz dodać. Maksymalna liczba urządzeń, które można zarejestrować w centrum, to 1 000 000. Celem tej opcji jest testowanie — można wygenerować określoną liczbę urządzeń, a następnie skopiować je do innego centrum.

*   **copyDevices** (argument 3) — Ustaw tę wartość na true, aby skopiować urządzenia z jednego koncentratora do drugiego. 

*   **deleteSourceDevices** (argument 4) — Ustaw tę wartość na true, aby usunąć wszystkie urządzenia zarejestrowane w centrum źródłowym. Zalecamy oczekiwanie na przekazanie wszystkich urządzeń przed jej uruchomieniem. Po usunięciu urządzeń nie można ich odzyskać.

*   **deleteDestDevices** (argument 5) — Ustaw tę wartość na true, aby usunąć wszystkie urządzenia zarejestrowane w centrum docelowym (klon). Można to zrobić, jeśli chcesz skopiować urządzenia więcej niż raz. 

Polecenie podstawowe zostanie uruchomione w środowisku *dotnet* -— spowoduje to nakazanie .NET do utworzenia lokalnego pliku csproj, a następnie uruchomienie go. Przed uruchomieniem należy dodać do końca argumenty wiersza polecenia. 

Wiersz polecenia będzie wyglądać następująco:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Używanie zmiennych środowiskowych dla parametrów połączenia

1. Aby uruchomić przykład, potrzebne są parametry połączenia z starymi i nowymi centrami IoT oraz do konta magazynu, którego można użyć dla tymczasowych plików roboczych. Te wartości są przechowywane w zmiennych środowiskowych.

1. Aby uzyskać wartości parametrów połączenia, zaloguj się do [Azure Portal](https://portal.azure.com). 

1. Umieść parametry połączenia w miejscu, w którym można je pobrać, takich jak Notatnik. W przypadku skopiowania następujących danych można wkleić parametry połączenia bezpośrednio tam, gdzie się znajdują. Nie dodawaj spacji wokół znaku równości lub zmienia nazwę zmiennej. Ponadto nie są wymagane podwójne cudzysłowy wokół parametrów połączenia. Jeśli umieścisz cudzysłowy wokół parametrów połączenia konta magazynu, nie będzie to możliwe.

   W przypadku systemu Windows jest to sposób ustawiania zmiennych środowiskowych:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   W systemie Linux jest to sposób definiowania zmiennych środowiskowych:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. W przypadku parametrów połączenia usługi IoT Hub przejdź do poszczególnych centrów w portalu. Można wyszukiwać **zasoby** centrum. Jeśli znasz grupę zasobów, możesz przejść do pozycji **grupy zasobów**, wybrać grupę zasobów, a następnie wybrać centrum z listy zasobów należących do tej grupy zasobów. 

1. Wybierz pozycję **zasady dostępu współdzielonego** z ustawień centrum, a następnie wybierz pozycję **iothubowner** i skopiuj jeden z parametrów połączenia. Wykonaj te same czynności dla koncentratora docelowego. Dodaj je do odpowiednich poleceń SET.

1. W polu Parametry połączenia konta magazynu Znajdź konto magazynu w obszarze **zasoby** lub w **grupie zasobów** , a następnie otwórz je. 
   
1. W sekcji Ustawienia wybierz pozycję **klucze dostępu** i skopiuj jeden z parametrów połączenia. Umieść parametry połączenia w pliku tekstowym dla odpowiedniego polecenia SET. 

Teraz masz zmienne środowiskowe w pliku z poleceniami SET i wiesz, co to są argumenty wiersza polecenia. Uruchommy przykład.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Uruchamianie przykładowej aplikacji i używanie argumentów wiersza polecenia

1. Otwórz okno wiersza polecenia. Wybierz pozycję Windows i wpisz `command prompt` , aby wyświetlić okno wiersza polecenia.

1. Skopiuj polecenia, które ustawiają zmienne środowiskowe, pojedynczo i wklej je do okna wiersza polecenia, a następnie wybierz klawisz ENTER. Po zakończeniu wpisz `SET` w oknie wiersza polecenia, aby wyświetlić zmienne środowiskowe i ich wartości. Po skopiowaniu ich do okna wiersza polecenia nie trzeba ich kopiować ponownie, chyba że zostanie otwarte nowe okno wiersza polecenia.

1. W oknie wiersza polecenia Zmień katalogi do momentu, w którym znajduje się w./ImportExportDevicesSample (gdzie istnieje plik ImportExportDevicesSample. csproj). Następnie wpisz następujące polecenie i Uwzględnij argumenty wiersza polecenia.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Polecenie dotnet kompiluje i uruchamia aplikację. Ponieważ podczas uruchamiania aplikacji nastąpi przekazanie opcji, możesz zmienić wartości przy każdym uruchomieniu aplikacji. Możesz na przykład uruchomić go raz i utworzyć nowe urządzenia, a następnie uruchomić go ponownie i skopiować te urządzenia do nowego centrum i tak dalej. Możesz również wykonać wszystkie kroki w tym samym przebiegu, chociaż nie zalecamy usuwania żadnych urządzeń, dopóki nie masz pewności, że zakończysz klonowanie. Oto przykład, który tworzy 1000 urządzeń, a następnie kopiuje je do innego centrum.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Po sprawdzeniu, czy urządzenia zostały pomyślnie skopiowane, można je usunąć z centrum źródłowego w następujący sposób:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Uruchamianie przykładowej aplikacji przy użyciu programu Visual Studio

1. Jeśli chcesz uruchomić aplikację w programie Visual Studio, Zmień bieżący katalog na folder, w którym znajduje się plik IoTHubServiceSamples. sln. Następnie Uruchom to polecenie w oknie wiersza polecenia, aby otworzyć rozwiązanie w programie Visual Studio. Należy to zrobić w tym samym oknie poleceń, w którym są ustawiane zmienne środowiskowe, więc te zmienne są znane.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Kliknij prawym przyciskiem myszy projekt *ImportExportDevicesSample* i wybierz pozycję **Ustaw jako projekt startowy**.    
    
1. Ustaw zmienne w górnej części elementu Program.cs w folderze ImportExportDevicesSample, aby wyświetlić pięć opcji.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Wybierz klawisz F5, aby uruchomić aplikację. Po zakończeniu działania można wyświetlić wyniki.

### <a name="view-the-results"></a>Wyświetlanie wyników 

Możesz wyświetlić urządzenia w [Azure Portal](https://portal.azure.com) i sprawdzić, czy znajdują się one w nowej lokalizacji.

1. Przejdź do nowego centrum przy użyciu [Azure Portal](https://portal.azure.com). Wybierz centrum, a następnie wybierz pozycję **urządzenia IoT**. Na sklonowanym centrum są widoczne właśnie skopiowane urządzenia ze starego centrum. Możesz również wyświetlić właściwości sklonowanego centrum. 

1. Sprawdź błędy importowania/eksportowania, przechodząc do konta usługi Azure Storage w [Azure Portal](https://portal.azure.com) i szukając `devicefiles` kontenera `ImportErrors.log` . Jeśli ten plik jest pusty (rozmiar wynosi 0), nie wystąpiły żadne błędy. Próba zaimportowania tego samego urządzenia więcej niż raz powoduje odrzucanie urządzenia po raz drugi i dodanie komunikatu o błędzie do pliku dziennika.

### <a name="committing-the-changes"></a>Zatwierdzanie zmian 

W tym momencie masz skopiowane centrum do nowej lokalizacji i zmigrowane urządzenia do nowego klonu. Teraz musisz wprowadzić zmiany, aby urządzenia działały ze sklonowanym centrum.

Aby zatwierdzić zmiany, poniżej przedstawiono kroki, które należy wykonać: 

* Zaktualizuj każde urządzenie, aby zmienić nazwę hosta IoT Hub tak, aby wskazywała nazwę hosta IoT Hub do nowego centrum. Należy to zrobić przy użyciu tej samej metody, która została użyta podczas pierwszej aprowizacji urządzenia.

* Zmień wszystkie aplikacje odwołujące się do starego centrum, aby wskazywały nowe centrum.

* Po zakończeniu nowe centrum powinno działać i działać. Stare centrum nie powinno mieć aktywnych urządzeń i znajdować się w stanie odłączonym. 

### <a name="rolling-back-the-changes"></a>Wycofywanie zmian

Jeśli zdecydujesz się wycofać zmiany, poniżej przedstawiono kroki, które należy wykonać:

* Zaktualizuj każde urządzenie, aby zmienić nazwę hosta IoT Hub, aby wskazywała nazwę hosta IoT Hub dla starego centrum. Należy to zrobić przy użyciu tej samej metody, która została użyta podczas pierwszej aprowizacji urządzenia.

* Zmień wszystkie aplikacje odwołujące się do nowego centrum, aby wskazywały na stare centrum. Na przykład jeśli korzystasz z usługi Azure Analytics, może być konieczne ponowne skonfigurowanie [danych wejściowych Azure Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Usuń nowe centrum. 

* Jeśli masz zasoby routingu, konfiguracja w starym centrum powinna nadal wskazywać poprawną konfigurację routingu i powinna współpracować z tymi zasobami po ponownym uruchomieniu centrum.

### <a name="checking-the-results"></a>Sprawdzanie wyników 

Aby sprawdzić wyniki, Zmień rozwiązanie IoT w taki sposób, aby wskazywało centrum w nowej lokalizacji, i uruchom je. Innymi słowy należy wykonać te same czynności z nowym koncentratorem, który został wykonany przy użyciu poprzedniego centrum i upewnić się, że działają prawidłowo. 

Jeśli wdrożono usługę Routing, przetestuj ją i upewnij się, że komunikaty są poprawnie kierowane do zasobów.

## <a name="clean-up"></a>Czyszczenie

Nie czyść do momentu, gdy nie jest to naprawdę konieczne, aby nowe centrum było uruchomione i działały prawidłowo. Pamiętaj również, aby przetestować Routing, jeśli używasz tej funkcji. Gdy wszystko będzie gotowe, Wyczyść stare zasoby, wykonując następujące czynności:

* Jeśli jeszcze tego nie zrobiono, usuń stare centrum. Spowoduje to usunięcie wszystkich aktywnych urządzeń z centrum.

* Jeśli masz zasoby routingu, które zostały przeniesione do nowej lokalizacji, możesz usunąć ze starych zasobów routingu.

## <a name="next-steps"></a>Następne kroki

Usunięto Centrum IoT Hub do nowego centrum w nowym regionie, dokończ pracę z urządzeniami. Aby uzyskać więcej informacji na temat wykonywania operacji zbiorczych w rejestrze tożsamości w IoT Hub, zobacz artykuł [Importowanie i eksportowanie IoT Hub tożsamości urządzeń zbiorczo](iot-hub-bulk-identity-mgmt.md).

Aby uzyskać więcej informacji na temat IoT Hub i programowania dla centrum, zobacz następujące artykuły.

* [Przewodnik dewelopera IoT Hub](iot-hub-devguide.md)

* [Samouczek routingu IoT Hub](tutorial-routing.md)

* [IoT Hub zarządzanie urządzeniami — Omówienie](iot-hub-device-management-overview.md)

* Jeśli chcesz wdrożyć przykładową aplikację, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/index).