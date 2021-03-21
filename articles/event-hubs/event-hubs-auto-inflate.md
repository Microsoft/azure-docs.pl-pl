---
title: Automatyczne skalowanie jednostek przepływności — Event Hubs platformy Azure | Microsoft Docs
description: Włącz automatyczne podwyższenie poziomu w przestrzeni nazw, aby automatycznie skalować jednostki przepływności.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020810"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatyczne skalowanie jednostek przepływności usługi Azure Event Hubs
Azure Event Hubs to wysoce skalowalna platforma przesyłania strumieniowego danych. W związku z tym Event Hubs użycie często rośnie po rozpoczęciu korzystania z usługi. Takie użycie wymaga zwiększenia wstępnie [zdefiniowanych jednostek przepływności](event-hubs-scalability.md#throughput-units) do skalowania Event Hubs i obsłużenia większych szybkości transferu. Funkcja **automatycznego** rozszerzania Event Hubs automatycznie skaluje się w górę przez zwiększenie liczby jednostek przepływności w celu spełnienia wymagań dotyczących użycia. Zwiększenie jednostek przepływności uniemożliwia scenariusze ograniczania, w którym:

* Szybkość transferu danych przychodzących przekracza ustaloną liczbę jednostek przepływności.
* Szybkości żądań danych wychodzących przekraczają ustawioną jednostkę przepływności.

Usługa Event Hubs zwiększa przepływność, gdy obciążenie przekracza minimalny próg, bez żądań zakończonych niepowodzeniem z błędami ServerBusy.

## <a name="how-auto-inflate-works"></a>Jak działa funkcja autodostrajania

Ruch Event Hubs jest kontrolowany przez [jednostki przepływności](event-hubs-scalability.md#throughput-units). Pojedyncza jednostka przepływności zezwala na 1 MB na sekundę i dwa razy większą ilość danych wychodzących. Standardowe Centra zdarzeń można skonfigurować przy użyciu jednostek przepływności 1-20. Funkcja autodostrajania pozwala rozpocząć mały wybór minimalnej wymaganej liczby jednostek przepływności. Funkcja zostanie następnie automatycznie skalowana do maksymalnego limitu jednostek przepływności, które są potrzebne, w zależności od wzrostu ruchu. Funkcja autodostrajania zapewnia następujące korzyści:

- Wydajny mechanizm skalowania, który umożliwia uruchamianie małych i skalowalnych w górę.
- Automatycznie Skaluj do określonego górnego limitu bez ograniczania problemów.
- Większa kontrola nad skalowaniem, ponieważ pozwala kontrolować czas i szybkość skalowania.

## <a name="enable-auto-inflate-on-a-namespace"></a>Włącz funkcję autodostrajania dla przestrzeni nazw

Można włączać lub wyłączać funkcję autodostrajania w warstwie Standardowa Event Hubs przestrzeni nazw za pomocą jednej z następujących metod:

- [Azure Portal](https://portal.azure.com).
- [Szablon Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Przestrzenie nazw Event Hubs warstwy Podstawowa nie obsługują funkcji autodostrajania.

### <a name="enable-auto-inflate-through-the-portal"></a>Włącz funkcję autodostrajania za pomocą portalu


#### <a name="enable-at-the-time-of-creation"></a>Włącz w czasie tworzenia 
Funkcję Autostart można włączyć **podczas tworzenia przestrzeni nazw Event Hubs**:
 
![Włącz funkcję autodostrajania podczas tworzenia centrum zdarzeń](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Gdy ta opcja jest włączona, możesz zacząć od małych do jednostek przepływności i skalować w górę w miarę wzrostu użycia. Górny limit inflacji nie ma natychmiast wpływu na ceny, które są zależne od liczby jednostek przepływności używanych na godzinę.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Włącz funkcję autodostrajania dla istniejącego centrum zdarzeń
Możesz również włączyć funkcję autostartu i zmodyfikować jej ustawienia, wykonując następujące instrukcje: 
 
1. Na stronie **obszar nazw Event Hubs** wybierz pozycję **wyłączone** w obszarze **jednostki przepływności autodostrajania**.  

    ![Wybierz jednostki przepływności na stronie przestrzeni nazw Event Hubs](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Na stronie **ustawienia skalowania** zaznacz pole wyboru **Włącz** (Jeśli funkcja skalowania automatycznego nie została włączona).

    ![Wybierz pozycję Włącz](./media/event-hubs-auto-inflate/scale-settings.png)
3. Wprowadź **maksymalną** liczbę jednostek przepływności lub Użyj paska przewijania, aby ustawić wartość. 
4. obowiązkowe Zaktualizuj **minimalną** liczbę jednostek przepływności w górnej części tej strony. 


> [!NOTE]
> Po zastosowaniu automatycznej konfiguracji w celu zwiększenia liczby jednostek przepływności usługa Event Hubs emituje dzienniki diagnostyczne, które zawierają informacje o tym, dlaczego i kiedy przepływność rośnie. Aby włączyć rejestrowanie diagnostyczne dla centrum zdarzeń, wybierz pozycję **Ustawienia diagnostyczne** w menu po lewej stronie centrum zdarzeń w Azure Portal. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dzienników diagnostycznych dla centrum zdarzeń platformy Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Włącz funkcję autodostrajania przy użyciu szablonu Azure Resource Manager

Podczas wdrażania szablonu Azure Resource Manager można włączyć funkcję autodostrajania. Na przykład ustaw `isAutoInflateEnabled` Właściwość na **true** , a następnie ustaw wartość `maximumThroughputUnits` 10. Na przykład:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Aby uzyskać pełny szablon, zobacz [przestrzeń nazw Create Event Hubs i Włącz szablon rozdęcie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) w serwisie GitHub.


## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](./event-hubs-about.md)
