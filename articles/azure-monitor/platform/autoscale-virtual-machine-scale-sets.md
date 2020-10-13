---
title: Zaawansowane automatyczne skalowanie przy użyciu usługi Azure Virtual Machines
description: Używa Menedżer zasobów i zestawów skalowania maszyn wirtualnych z wieloma regułami i profilami, które wysyłają wiadomości e-mail i wywołują adresy URL elementu webhook z akcjami skalowania.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 96d8c32791bd968dd8740a2d4633496b42e9f3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360977"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Zaawansowana konfiguracja automatycznego skalowania przy użyciu Menedżer zasobów szablonów dla VM Scale Sets
Możesz skalować w poziomie i skalować w Virtual Machine Scale Sets na podstawie progów metryk wydajności, harmonogramu cyklicznego lub określonego dnia. Możesz również skonfigurować powiadomienia e-mail i elementy webhook dla akcji skalowania. W tym instruktażu przedstawiono przykład konfigurowania wszystkich tych obiektów przy użyciu szablonu Menedżer zasobów na zestawie skalowania maszyn wirtualnych.

> [!NOTE]
> W tym instruktażu wyjaśniono kroki VM Scale Sets, te same informacje dotyczą skalowania automatycznego [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service Web Apps](https://azure.microsoft.com/services/app-service/web/)i [API Management usług](../../api-management/api-management-key-concepts.md) dla prostego ustawienia skalowania w poziomie w zestawie skalowania maszyn wirtualnych na podstawie prostej METRYKI wydajności, takiej jak procesor CPU, można znaleźć w dokumentach [systemu](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) [Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) i Windows.
>
>

## <a name="walkthrough"></a>Przewodnik
W tym instruktażu użyjemy [Azure Resource Explorer](https://resources.azure.com/) , aby skonfigurować i zaktualizować ustawienia automatycznego skalowania dla zestawu skalowania. Azure Resource Explorer to prosty sposób na zarządzanie zasobami platformy Azure za pośrednictwem Menedżer zasobów szablonów. Jeśli jesteś nowym narzędziem Azure Resource Explorer, Przeczytaj [to wprowadzenie](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Wdróż nowy zestaw skalowania z podstawowym ustawieniem automatycznego skalowania. W tym artykule jest używany jeden z galerii szybkiego startu platformy Azure z zestawem skalowania systemu Windows z podstawowym szablonem skalowania automatycznego. Zestawy skalowania systemu Linux działają w ten sam sposób.
2. Po utworzeniu zestawu skalowania przejdź do zasobu zestawu skalowania z Azure Resource Explorer. W węźle Microsoft. Insights są wyświetlane następujące informacje:

    ![Eksplorator platformy Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Wykonanie szablonu spowodowało utworzenie domyślnego ustawienia automatycznego skalowania o nazwie **"autoscalewad"**. Po prawej stronie można wyświetlić pełną definicję tego ustawienia skalowania automatycznego. W takim przypadku domyślne ustawienie automatycznego skalowania jest zgodne z procesorem CPU% skalowalnym w poziomie i regułą skalowania w poziomie.  

3. Teraz można dodać więcej profilów i reguł na podstawie harmonogramu lub określonych wymagań. Utworzymy ustawienie skalowania automatycznego z trzema profilami. Aby zrozumieć profile i reguły w funkcji automatycznego skalowania, zapoznaj się z [najlepszymi rozwiązaniami skalowania automatycznego](autoscale-best-practices.md).  

    | Profile & reguł | Opis |
    |--- | --- |
    | **Profil** |**Wydajność/oparta na metrykach** |
    | Reguła |Liczba komunikatów w kolejce Service Bus > x |
    | Reguła |Liczba komunikatów w kolejce Service Bus < y |
    | Reguła |Procesor% > n |
    | Reguła |Procesor CPU% < p |
    | **Profil** |**Dni tygodnia rano (bez reguł)** |
    | **Profil** |**Dzień uruchamiania produktu (brak reguł)** |

4. Oto hipotetyczny scenariusz skalowania, który jest używany do tego przewodnika.

   * **Załaduj na** zewnątrz — chcę skalować w poziomie lub w oparciu o obciążenie aplikacji hostowanej w zestawie skalowania. *
   * **Rozmiar kolejki komunikatów** — używam kolejki Service Bus dla wiadomości przychodzących do mojej aplikacji. Użycie liczby komunikatów w kolejce i procesora CPU (%) i skonfigurowanie profilu domyślnego do wyzwalania akcji skalowania, jeśli liczba komunikatów lub procesor CPU trafi próg.\*
   * **Czas tygodnia i dzień** — chcę tygodniowo cyklicznie "godzina" profilu na podstawie "o nazwie" dzień tygodnia rano ". Na podstawie danych historycznych wiemy, że lepiej jest mieć pewną liczbę wystąpień maszyn wirtualnych do obsługi obciążenia aplikacji w tym czasie.\*
   * **Specjalne daty** — dodano profil "dzień uruchamiania produktu". Planuję w odniesieniu do określonych dat, aby moja aplikacja była gotowa do obsłużenia anonsów marketingu z załadowaniami i wprowadzenia nowego produktu w aplikacji.\*
   * *Ostatnie dwa profile mogą także mieć inne reguły oparte na metryki wydajności. W tym przypadku zdecydowałem się, że nie mam takiego i zamiast tego opierają się na domyślnych regułach opartych na metrykach wydajności. Reguły są opcjonalne dla profilów cyklicznych i opartych na dacie.*

     Priorytetyzacja profilów i reguł w aparacie skalowania automatycznego jest również przechwytywana w artykule [najlepsze rozwiązania dotyczące skalowania](autoscale-best-practices.md) automatycznego.
     Aby uzyskać listę typowych metryk skalowania automatycznego, zapoznaj się z [typowymi metrykami skalowania automatycznego](autoscale-common-metrics.md)

5. Upewnij się, że korzystasz z trybu **odczytu/zapisu** w Eksplorator zasobów

    ![Autoscalewad, domyślne ustawienie automatycznego skalowania](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Kliknij pozycję Edytuj. **Zastąp** element "Profile" w ustawieniu automatycznego skalowania następującym konfiguracją:

    ![Zrzut ekranu przedstawia element profile.](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Aby poznać obsługiwane pola i ich wartości, zobacz [dokumentację interfejsu API REST skalowania automatycznego](/rest/api/monitor/autoscalesettings). Teraz Twoje ustawienie skalowania automatycznego zawiera trzy profile opisane wcześniej.

7. Na koniec zapoznaj się z sekcją **powiadomienia** skalowania automatycznego. Powiadomienia skalowania automatycznego umożliwiają wykonywanie trzech czynności w przypadku pomyślnego wyzwolenia skalowania w poziomie lub akcji.
   - Powiadom administratora i współadministratora subskrypcji
   - Wyślij pocztą e-mail zestaw użytkowników
   - Wyzwalanie wywołania elementu webhook. Po uruchomieniu ten element webhook wysyła metadane dotyczące warunku skalowania automatycznego i zasobu zestawu skalowania. Aby dowiedzieć się więcej o ładunku elementu webhook automatycznego skalowania, zobacz [Konfigurowanie elementu webhook & powiadomienia e-mail dla skalowania automatycznego](autoscale-webhook-email.md).

   Dodaj następujący element do ustawienia skalowania automatycznego zamieniania elementu **powiadomienia** , którego wartość jest równa null.

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Przycisk **Put** trafij w Eksplorator zasobów, aby zaktualizować ustawienie skalowania automatycznego.

Zmieniono ustawienie skalowania automatycznego w zestawie skalowania maszyn wirtualnych w celu uwzględnienia wielu profilów skalowania i powiadomień dotyczących skalowania.

## <a name="next-steps"></a>Następne kroki
Skorzystaj z tych linków, aby dowiedzieć się więcej o automatycznym skalowaniu.

[Rozwiązywanie problemów z automatycznym skalowaniem za pomocą Virtual Machine Scale Sets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Typowe metryki automatycznego skalowania](autoscale-common-metrics.md)

[Najlepsze rozwiązania dotyczące automatycznego skalowania na platformie Azure](autoscale-best-practices.md)

[Zarządzanie automatycznym skalowaniem przy użyciu programu PowerShell](../samples/powershell-samples.md#create-and-manage-autoscale-settings)

[Zarządzanie funkcją automatycznego skalowania przy użyciu interfejsu wiersza polecenia](../samples/cli-samples.md#autoscale)

[Konfigurowanie elementu webhook & powiadomienia E-mail na potrzeby automatycznego skalowania](autoscale-webhook-email.md)

Dokumentacja szablonu [Microsoft. Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)
