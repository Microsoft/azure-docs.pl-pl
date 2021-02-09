---
title: Samouczek — eksportowanie danych i wizualizacja szczegółowych informacji na platformie Azure IoT Central
description: W tym samouczku dowiesz się, jak eksportować dane z IoT Central i wizualizować szczegółowe informacje na pulpicie nawigacyjnym Power BI.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 2695b34745ff02d55f18cebbe87a468f807ca77a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831692"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Samouczek: eksportowanie danych z platformy Azure IoT Central i wizualizacja szczegółowych informacji w programie Power BI


W dwóch poprzednich samouczkach utworzono i dostosowano aplikację IoT Central przy użyciu szablonu aplikacji do **analizy w sklepie — wyewidencjonowywanie** . W tym samouczku skonfigurujesz aplikację IoT Central do eksportowania danych telemetrycznych zebranych z urządzeń. Następnie użyj Power BI, aby utworzyć niestandardowy pulpit nawigacyjny dla Menedżera sklepu w celu wizualizowania szczegółowych informacji uzyskanych na podstawie danych telemetrycznych.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj aplikację IoT Central, aby wyeksportować dane telemetryczne do centrum zdarzeń.
> * Użyj Logic Apps, aby wysyłać dane z centrum zdarzeń do zestawu danych przesyłania strumieniowego Power BI.
> * Utwórz pulpit nawigacyjny Power BI, aby wizualizować dane w zestawie danych przesyłania strumieniowego.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Aby wykonać poprzednie dwa samouczki, [Utwórz aplikację analizy w sklepie na platformie azure IoT Central](./tutorial-in-store-analytics-create-app.md) i [Dostosuj pulpit nawigacyjny operatora i Zarządzaj urządzeniami w usłudze Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto usługi Power BI. Jeśli nie masz konta Power BI, przed rozpoczęciem Zarejestruj się, aby uzyskać [bezpłatną Power BI Pro wersję próbną](https://app.powerbi.com/signupredirect?pbi_source=web) .

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem centrum zdarzeń i aplikacji logiki należy utworzyć grupę zasobów, aby zarządzać nimi. Grupa zasobów powinna znajdować się w tej samej lokalizacji, w której znajduje się aplikacja **do analizy w sklepie — wyewidencjonowywanie** IoT Central. Aby utworzyć grupę zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów**. Następnie wybierz pozycję **Dodaj**.
1. W polu **subskrypcja** wybierz nazwę subskrypcji platformy Azure, która została użyta do utworzenia aplikacji IoT Central.
1. W polu Nazwa **grupy zasobów** wprowadź wartość _Retail-Store-Analysis_*.
1. Dla **regionu** wybierz ten sam region, który został wybrany dla aplikacji IoT Central.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Na stronie **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

Teraz masz grupę zasobów o nazwie **Retail-Store-Analysis** w Twojej subskrypcji.

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Aby można było skonfigurować aplikację do monitorowania handlu detalicznego w celu wyeksportowania danych telemetrycznych, należy utworzyć centrum zdarzeń, aby otrzymać wyeksportowane dane. Poniższe kroki pokazują, jak utworzyć centrum zdarzeń:

1. W Azure Portal wybierz pozycję **Utwórz zasób** w lewym górnym rogu ekranu.
1. W obszarze **Wyszukaj w portalu Marketplace** wprowadź _Event Hubs_, a następnie naciśnij klawisz **Enter**.
1. Na stronie **Event Hubs** wybierz pozycję **Utwórz**.
1. Na stronie **Tworzenie przestrzeni nazw** wykonaj następujące czynności:
    * Wprowadź unikatową nazwę przestrzeni nazw, takiej jak nazwa _użytkownika-sprzedaż-magazyn-analiza_. System sprawdza, czy ta nazwa jest dostępna.
    * Wybierz warstwę cenową **podstawowa** .
    * Wybierz tę samą **subskrypcję** , która została użyta do utworzenia aplikacji IoT Central.
    * Wybierz grupę zasobów **analizy handlu detalicznego** .
    * Wybierz tę samą lokalizację, która była używana dla aplikacji IoT Central.
    * Wybierz przycisk **Utwórz**. Może być konieczne odczekanie kilku minut, aż system udostępni Zasoby.
1. W portalu przejdź do grupy zasobów **analizy handlu detalicznego** . Zaczekaj na zakończenie wdrażania. Może być konieczne wybranie opcji **Odśwież** , aby zaktualizować stan wdrożenia. Możesz również sprawdzić stan tworzenia przestrzeni nazw centrum zdarzeń w **powiadomieniach**.
1. W grupie zasobów **analizy sprzedaży detalicznej** wybierz **Event Hubs przestrzeni nazw**. Zostanie wyświetlona strona główna **obszaru nazw Event Hubs** w portalu.

Teraz masz **Event Hubs przestrzeni nazw**, możesz utworzyć **centrum zdarzeń** do użycia z aplikacją IoT Central:

1. Na stronie głównej **obszaru nazw Event Hubs** w portalu wybierz pozycję **+ centrum zdarzeń**.
1. Na stronie **Tworzenie centrum zdarzeń** _wprowadź wartość w polu_ nazwa, a następnie wybierz pozycję **Utwórz**.

Masz teraz centrum zdarzeń, których można użyć podczas konfigurowania eksportu danych z aplikacji IoT Central:

![Centrum zdarzeń](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Konfigurowanie eksportu danych

Teraz masz centrum zdarzeń, możesz skonfigurować aplikację do **analizy i wyewidencjonowywania w sklepie** , aby wyeksportować dane telemetryczne z połączonych urządzeń. Poniższe kroki pokazują, jak skonfigurować eksport:

1. Zaloguj się do aplikacji do **analizy w sklepie — Wyewidencjonuj** IoT Central.
1. W lewym okienku wybierz pozycję **eksport danych** .
1. Wybierz pozycję **nowy > Azure Event Hubs**.
1. Wprowadź wartość w polu _eksport danych telemetrycznych_ jako **nazwę wyświetlaną**.
1. Wybierz **przestrzeń nazw Event Hubs**.
1. Wybierz centrum zdarzeń w **sklepie — dane telemetryczne** .
1. Wyłącz **urządzenia** i **Szablony urządzeń** w sekcji **dane do eksportowania** .
1. Wybierz pozycję **Zapisz**.

Eksportowanie danych telemetrycznych do centrum zdarzeń może potrwać kilka minut. Stan eksportu można sprawdzić na stronie Eksport **danych** :

![Ciągła konfiguracja eksportu danych](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Tworzenie zestawów danych Power BI

Pulpit nawigacyjny Power BI będzie wyświetlał dane z aplikacji do monitorowania handlu detalicznego. W tym rozwiązaniu można używać zestawów danych przesyłania strumieniowego Power BI jako źródła dla Power BI pulpitu nawigacyjnego. W tej sekcji zdefiniujesz Schemat zestawów danych przesyłania strumieniowego, dzięki czemu aplikacja logiki będzie mogła przekazywać dane z centrum zdarzeń. Poniższe kroki pokazują, jak utworzyć dwa zestawy danych przesyłania strumieniowego dla czujników środowiskowych i jeden zestaw danych przesyłania strumieniowego dla czujnika zajętości:

1. Zaloguj się do swojego konta usługi **Power BI**.
1. Wybierz pozycję **obszary robocze**, a następnie wybierz pozycję **Utwórz obszar roboczy**.
1. Na stronie **Tworzenie obszaru roboczego** wprowadź wartość _w polu analiza w sklepie — Wyewidencjonuj_ jako **nazwę obszaru roboczego**.
1. Przewiń do dołu strony **Witamy w obszarze roboczym analizy w sklepie — wyewidencjonowywanie** , a następnie wybierz pozycję **Pomiń**.
1. Na stronie obszar roboczy wybierz pozycję **Utwórz zestaw danych przesyłania strumieniowego >**.
1. Na stronie **Nowy zestaw danych przesyłania strumieniowego** wybierz pozycję **interfejs API**, a następnie wybierz przycisk **dalej**.
1. Wprowadź _Strefa 1 czujnik_ jako **nazwę zestawu danych**.
1. Wprowadź trzy **wartości ze strumienia** w poniższej tabeli:

    | Nazwa wartości  | Typ wartości |
    | ----------- | ---------- |
    | Znacznik czasu   | DateTime   |
    | Wilgotność    | Liczba     |
    | Temperatura | Liczba     |

1. Przełącz **analizę danych historycznych** .
1. Wybierz pozycję **Utwórz** , a następnie przycisk **gotowe**.
1. Utwórz inny zestaw danych przesyłania strumieniowego o nazwie **sensor strefa 2** z tym samym schematem i ustawieniami, co zestaw danych przesyłania strumieniowego **Strefa 1 czujnika** .

Masz teraz dwa zestawy danych przesyłania strumieniowego. Aplikacja logiki kieruje dane telemetryczne z dwóch czujników środowiska, podłączonych do aplikacji do **analizy w sklepie** , do tych dwóch zestawów danych:

![Zestawy danych strefy](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

To rozwiązanie używa jednego zestawu danych przesyłania strumieniowego dla każdego czujnika, ponieważ nie można zastosować filtrów do przesyłania strumieniowego danych w Power BI.

Wymagany jest również zestaw danych przesyłania strumieniowego dla telemetrii zajętości:

1. Na stronie obszar roboczy wybierz pozycję **Utwórz zestaw danych przesyłania strumieniowego >**.
1. Na stronie **Nowy zestaw danych przesyłania strumieniowego** wybierz pozycję **interfejs API**, a następnie wybierz przycisk **dalej**.
1. Wprowadź _czujnik zajętości_ jako **nazwę zestawu danych**.
1. Wprowadź pięć **wartości ze strumienia** w następującej tabeli:

    | Nazwa wartości     | Typ wartości |
    | -------------- | ---------- |
    | Znacznik czasu      | DateTime   |
    | Długość kolejki 1 | Liczba     |
    | Długość kolejki 2 | Liczba     |
    | Czas mieszkania 1   | Liczba     |
    | Czas mieszkania 2   | Liczba     |

1. Przełącz **analizę danych historycznych** .
1. Wybierz pozycję **Utwórz** , a następnie przycisk **gotowe**.

Masz teraz trzeci zestaw danych przesyłania strumieniowego, który przechowuje wartości z czujnika symulowanego zajętości. Ten czujnik zgłasza długość kolejki w dwóch wyewidencjonowaniu w sklepie i jak długo klienci oczekują w tych kolejkach:

![Zestaw danych użytkowania](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

W tym rozwiązaniu aplikacja logiki odczytuje dane telemetryczne z centrum zdarzeń, analizuje je, a następnie wysyła je do utworzonych zestawów danych przesyłania strumieniowego Power BI.

Przed utworzeniem aplikacji logiki potrzebne są identyfikatory urządzeń dwóch czujników RuuviTag podłączonych do aplikacji IoT Central w samouczku [Tworzenie aplikacji do analizy w sklepie na platformie Azure IoT Central](./tutorial-in-store-analytics-create-app.md) :

1. Zaloguj się do aplikacji do **analizy w sklepie — Wyewidencjonuj** IoT Central.
1. Wybierz pozycję **urządzenia** w lewym okienku. Następnie wybierz pozycję **RuuviTag**.
1. Zanotuj **identyfikatory urządzeń**. Na poniższym zrzucie ekranu identyfikatory to **f5dcf4ac32e8** i **e29ffc8d5326**:

    ![Identyfikatory urządzeń](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

Poniższe kroki pokazują, jak utworzyć aplikację logiki w Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób** w lewym górnym rogu ekranu.
1. W obszarze **Wyszukaj w portalu Marketplace wprowadź wartość** _aplikacja logiki_, a następnie naciśnij klawisz **Enter**.
1. Na stronie **aplikacja logiki** wybierz pozycję **Utwórz**.
1. Na stronie Tworzenie **aplikacji logiki** :
    * Wprowadź unikatową nazwę aplikacji logiki, taką jak nazwa _użytkownika-sprzedaż-magazyn-analiza_.
    * Wybierz tę samą **subskrypcję** , która została użyta do utworzenia aplikacji IoT Central.
    * Wybierz grupę zasobów **analizy handlu detalicznego** .
    * Wybierz tę samą lokalizację, która była używana dla aplikacji IoT Central.
    * Wybierz przycisk **Utwórz**. Może być konieczne odczekanie kilku minut, aż system udostępni Zasoby.
1. W Azure Portal przejdź do nowej aplikacji logiki.
1. Na stronie **projektanta Logic Apps** przewiń w dół i wybierz pozycję **pusta aplikacja logiki**.
1. W obszarze **Wyszukaj łączniki i wyzwalacze** wpisz _Event Hubs_.
1. W obszarze **wyzwalacze** wybierz opcję **gdy zdarzenia są dostępne w centrum zdarzeń**.
1. Wprowadź dane _telemetryczne sklepu_ jako **nazwę połączenia**, a następnie wybierz **przestrzeń nazw Event Hubs**.
1. Wybierz zasady **RootManageSharedAccess** i wybierz pozycję **Utwórz**.
1. W akcji **gdy zdarzenia są dostępne w centrum zdarzeń** :
    * W polu **nazwa centrum zdarzeń** wybierz pozycję **Magazyn-Telemetria**.
    * W polu **Typ zawartości** wybierz pozycję **Application/JSON**.
    * Ustaw **Interwał** na wartość 3 i **częstotliwość** na sekundy
1. Wybierz pozycję **Zapisz**, aby zapisać aplikację logiki.

Aby dodać logikę do projektu aplikacji logiki, wybierz pozycję **Widok kodu**:

1. Zamień `"actions": {},` na poniższy kod JSON. Zastąp dwa symbole zastępcze `[YOUR RUUVITAG DEVICE ID 1]` i `[YOUR RUUVITAG DEVICE ID 2]` identyfikatorami zanotowanymi dla dwóch urządzeń RuuviTag:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Wybierz pozycję **Zapisz** , a następnie wybierz pozycję **Projektant** , aby wyświetlić dodaną przez Ciebie wersję elementu logiki:

    ![Projekt aplikacji logiki](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Wybierz pozycję **Przełącz według** ID, aby rozwinąć akcję. Następnie wybierz pozycję **środowisko Strefa 1**, a następnie wybierz pozycję **Dodaj akcję**.
1. W obszarze **łączniki i akcje wyszukiwania** wprowadź **Power BI**, a następnie naciśnij klawisz **Enter**.
1. Wybierz akcję **Dodaj wiersze do zestawu danych (wersja zapoznawcza)** .
1. Wybierz pozycję **Zaloguj** i postępuj zgodnie z monitami, aby zalogować się do konta Power BI.
1. Po zakończeniu procesu logowania w akcji **Dodaj wiersze do zestawu danych** :
    * Wybierz pozycję **in-Store Analytics — wyewidencjonowywanie** jako obszar roboczy.
    * Wybierz **Strefa 1 czujnik** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz pozycję **Dodaj nowy parametr** , a następnie wybierz pola **sygnatury czasowej**, **wilgotności** i **temperatury** .
    * Zaznacz pole **sygnatura czasowa** , a następnie wybierz pozycję **x-opt-enqueuedtime** z listy **zawartość dynamiczna** .
    * Zaznacz pole **wilgotność** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **wilgotność**.
    * Wybierz pole **temperatura** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **temperatura**.
    * Wybierz przycisk **Zapisz**, aby zapisać zmiany. Akcja **środowiska Strefa 1** wygląda następująco: ![ Strefa 1 Environment](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Wybierz akcję **środowiska strefa 2** , a następnie wybierz pozycję **Dodaj akcję**.
1. W obszarze **łączniki i akcje wyszukiwania** wprowadź **Power BI**, a następnie naciśnij klawisz **Enter**.
1. Wybierz akcję **Dodaj wiersze do zestawu danych (wersja zapoznawcza)** .
1. W akcji **Dodaj wiersze do zestawu danych 2** :
    * Wybierz pozycję **in-Store Analytics — wyewidencjonowywanie** jako obszar roboczy.
    * Wybierz **strefa 2 Czujnik** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz pozycję **Dodaj nowy parametr** , a następnie wybierz pola **sygnatury czasowej**, **wilgotności** i **temperatury** .
    * Zaznacz pole **sygnatura czasowa** , a następnie wybierz pozycję **x-opt-enqueuedtime** z listy **zawartość dynamiczna** .
    * Zaznacz pole **wilgotność** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **wilgotność**.
    * Wybierz pole **temperatura** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **temperatura**.
    Wybierz przycisk **Zapisz**, aby zapisać zmiany.  Akcja **środowiska strefa 2** wygląda następująco: ![ strefa 2 Environment](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Wybierz akcję **zajętości** , a następnie wybierz akcję **przełączenia przez identyfikator interfejsu** .
1. Wybierz akcję **interfejs czasu mieszkania** i wybierz pozycję **Dodaj akcję**.
1. W obszarze **łączniki i akcje wyszukiwania** wprowadź **Power BI**, a następnie naciśnij klawisz **Enter**.
1. Wybierz akcję **Dodaj wiersze do zestawu danych (wersja zapoznawcza)** .
1. W akcji **Dodaj wiersze do zestawu danych** :
    * Wybierz pozycję **in-Store Analytics — wyewidencjonowywanie** jako obszar roboczy.
    * Wybierz **czujnik zajętości** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz pozycję **Dodaj nowy parametr** , a następnie wybierz pola **sygnatura** czasowa **1** i czas mieszkania oraz **2** .
    * Zaznacz pole **sygnatura czasowa** , a następnie wybierz pozycję **x-opt-enqueuedtime** z listy **zawartość dynamiczna** .
    * Wybierz pole **czas mieszkania 1** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **DwellTime1**.
    * Wybierz pole **czas mieszkania 2** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **DwellTime2**.
    * Wybierz przycisk **Zapisz**, aby zapisać zmiany. Akcja **interfejsu czasu mieszkania** wygląda jak na poniższym zrzucie ekranu: ![ zrzut ekranu przedstawiający akcję "interfejs czasu mieszkania".](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Wybierz akcję **Liczba osób** , a następnie wybierz pozycję **Dodaj akcję**.
1. W obszarze **łączniki i akcje wyszukiwania** wprowadź **Power BI**, a następnie naciśnij klawisz **Enter**.
1. Wybierz akcję **Dodaj wiersze do zestawu danych (wersja zapoznawcza)** .
1. W akcji **Dodaj wiersze do zestawu danych** :
    * Wybierz pozycję **in-Store Analytics — wyewidencjonowywanie** jako obszar roboczy.
    * Wybierz **czujnik zajętości** jako zestaw danych.
    * Wybierz **RealTimeData** jako tabelę.
    * Wybierz pozycję **Dodaj nowy parametr** , a następnie wybierz pola **sygnatura czasowa**, **Długość kolejki 1** i **Długość kolejki 2** .
    * Zaznacz pole **sygnatura czasowa** , a następnie wybierz pozycję **x-opt-enqueuedtime** z listy **zawartość dynamiczna** .
    * Zaznacz pole **Długość kolejki 1** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **count1**.
    * Wybierz pole **Długość kolejki 2** , a następnie wybierz pozycję **Zobacz więcej** obok pozycji **Analizuj dane telemetryczne**. Następnie wybierz pozycję **count2**.
    * Wybierz przycisk **Zapisz**, aby zapisać zmiany. Akcja **interfejsu liczba osób** wygląda jak na poniższym zrzucie ekranu: ![ Akcja zajętości](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

Aplikacja logiki zostanie uruchomiona automatycznie. Aby wyświetlić stan każdego przebiegu, przejdź do strony **Przegląd** dla aplikacji logiki w Azure Portal:

## <a name="create-a-power-bi-dashboard"></a>Tworzenie pulpitu nawigacyjnego usługi Power BI

Teraz dane telemetryczne są przesyłane z aplikacji IoT Central za pośrednictwem centrum zdarzeń. Następnie aplikacja logiki analizuje komunikaty centrum zdarzeń i dodaje je do Power BI zestawu danych przesyłania strumieniowego. Teraz możesz utworzyć pulpit nawigacyjny Power BI, aby wizualizował dane telemetryczne:

1. Zaloguj się do swojego konta usługi **Power BI**.
1. Wybierz **obszary robocze > analizy w sklepie — wyewidencjonowanie**.
1. Wybierz pozycję **Utwórz pulpit nawigacyjny >**.
1. Wprowadź wartość w polu **Analiza sklepu** jako nazwę pulpitu nawigacyjnego, a następnie wybierz pozycję **Utwórz**.

### <a name="add-line-charts"></a>Dodaj wykresy liniowe

Dodaj cztery kafelki wykresu liniowego, aby pokazać temperaturę i wilgotność z dwóch czujników środowiska. Użyj informacji podanych w poniższej tabeli, aby utworzyć kafelki. Aby dodać każdy kafelek, Zacznij od wybrania **... (Więcej opcji) > dodać kafelek**. Wybierz pozycję **niestandardowe dane przesyłane strumieniowo**, a następnie wybierz pozycję **dalej**:

| Ustawienie | #1 wykresu | #2 wykresu | #3 wykresu | #4 wykresu |
| ------- | -------- | -------- | -------- | -------- |
| Zestaw danych | Czujnik Strefa 1 | Czujnik Strefa 1 | Czujnik Strefa 2 | Czujnik Strefa 2 |
| Typ wizualizacji | Wykres liniowy | Wykres liniowy | Wykres liniowy | Wykres liniowy |
| Oś | Znacznik czasu | Znacznik czasu | Znacznik czasu | Znacznik czasu |
| Wartości | Temperatura | Wilgotność | Temperatura | Wilgotność |
| Przedział czasu | 60 minut | 60 minut | 60 minut | 60 minut |
| Tytuł | Temperatura (1 godzina) | Wilgotność (1 godzina) | Temperatura (1 godzina) | Wilgotność (1 godzina) |
| Podtytuł | Strefa 1 | Strefa 1 | Strefa 2 | Strefa 2 |

Poniższy zrzut ekranu przedstawia ustawienia pierwszego wykresu:

![Ustawienia wykresu liniowego](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Dodawanie kart do wyświetlania danych o środowisku

Dodaj cztery kafelki kart, aby wyświetlić najnowsze wartości temperatury i wilgotności z dwóch czujników środowiska. Użyj informacji podanych w poniższej tabeli, aby utworzyć kafelki. Aby dodać każdy kafelek, Zacznij od wybrania **... (Więcej opcji) > dodać kafelek**. Wybierz pozycję **niestandardowe dane przesyłane strumieniowo**, a następnie wybierz pozycję **dalej**:

| Ustawienie | #1 karty | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Zestaw danych | Czujnik Strefa 1 | Czujnik Strefa 1 | Czujnik Strefa 2 | Czujnik Strefa 2 |
| Typ wizualizacji | Karta | Karta | Karta | Karta |
| Pola | Temperatura | Wilgotność | Temperatura | Wilgotność |
| Tytuł | Temperatura (F) | Wilgotność (%) | Temperatura (F) | Wilgotność (%) |
| Podtytuł | Strefa 1 | Strefa 1 | Strefa 2 | Strefa 2 |

Poniższy zrzut ekranu przedstawia ustawienia pierwszej karty:

![Ustawienia karty](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Dodaj kafelki, aby pokazać dane dotyczące operacji wyewidencjonowania

Dodaj cztery kafelki kart, aby wyświetlić długość kolejki i czas mieszkania dla dwóch operacji wyewidencjonowania w sklepie. Użyj informacji podanych w poniższej tabeli, aby utworzyć kafelki. Aby dodać każdy kafelek, Zacznij od wybrania **... (Więcej opcji) > dodać kafelek**. Wybierz pozycję **niestandardowe dane przesyłane strumieniowo**, a następnie wybierz pozycję **dalej**:

| Ustawienie | #1 karty | #2 karty | #3 karty | #4 karty |
| ------- | ------- | ------- | ------- | ------- |
| Zestaw danych | Czujnik zajętości | Czujnik zajętości | Czujnik zajętości | Czujnik zajętości |
| Typ wizualizacji | Wykres kolumnowy grupowany | Wykres kolumnowy grupowany | Miernik | Miernik |
| Oś    | Znacznik czasu | Znacznik czasu | NIE DOTYCZY | NIE DOTYCZY |
| Wartość | Czas mieszkania 1 | Czas mieszkania 2 | Długość kolejki 1 | Długość kolejki 2 |
| Przedział czasu | 60 minut | 60 minut |  NIE DOTYCZY | NIE DOTYCZY |
| Tytuł | Czas mieszkania | Czas mieszkania | Długość kolejki | Długość kolejki |
| Podtytuł | Wyewidencjonowanie 1 | Wyewidencjonowanie 2 | Wyewidencjonowanie 1 | Wyewidencjonowanie 2 |

Zmień rozmiar i Rozmieść kafelki na pulpicie nawigacyjnym tak, aby wyglądały tak jak na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający pulpit nawigacyjny z możliwością zmiany rozmiaru i rozmieszczenia kafelków.](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Możesz dodać kilka dodatkowych zasobów graficznych, aby dodatkowo dostosować pulpit nawigacyjny:

![Pulpit nawigacyjny usługi Power BI](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zakończysz pracę z aplikacją IoT Central, możesz ją usunąć, logując się do aplikacji i przechodząc do strony **Ustawienia aplikacji** w sekcji **Administracja** .

Jeśli chcesz zachować aplikację, ale zmniejsz koszty związane z nią, wyłącz eksport danych, który wysyła dane telemetryczne do centrum zdarzeń.

Możesz usunąć centrum zdarzeń i aplikację logiki w Azure Portal, usuwając grupę zasobów o nazwie **Retail-Store-Analysis**.

Możesz usunąć Power BI zestawy danych i pulpit nawigacyjny, usuwając obszar roboczy ze strony Ustawienia Power BI dla obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Te trzy samouczki pokazują kompleksowe rozwiązanie, które korzysta z szablonu aplikacji **do analizy w sklepie — wyewidencjonowywanie** IoT Central. Urządzenia zostały podłączone do aplikacji, używane IoT Central do monitorowania urządzeń i używane Power BI do tworzenia pulpitu nawigacyjnego w celu wyświetlenia szczegółowych informacji na podstawie danych telemetrycznych urządzenia. Zalecanym następnym krokiem jest zapoznanie się z jednym z innych IoT Central szablonów aplikacji:

> [!div class="nextstepaction"]
> [Tworzenie rozwiązań dla branży energetycznej za pomocą usługi IoT Central](../energy/overview-iot-central-energy.md)
