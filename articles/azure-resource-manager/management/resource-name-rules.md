---
title: Ograniczenia nazewnictwa zasobów
description: Pokazuje reguły i ograniczenia dotyczące nazewnictwa zasobów platformy Azure.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: fab1ab2bb779b3826c852e49da7970030d34594d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086389"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Reguły i ograniczenia nazewnictwa zasobów platformy Azure

W tym artykule podsumowano reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure. Aby uzyskać zalecenia dotyczące nazywania zasobów, zobacz [Zalecane konwencje nazewnictwa i tagowania](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Nazwy zasobów są bez uwzględniania wielkości liter, chyba że wyraźnie zaznaczono w kolumnie prawidłowe znaki.

W poniższych tabelach termin alfanumeryczny odnosi się do:

* **a** przez **z** (małe litery)
* **Od A** do **Z** (wielkie litery)
* **Od 0** do **9** (numery)

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | grupa zasobów | 3-63 | Małe litery i cyfry.<br><br>Zacznij od małych liter. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | usługa | global | 1-50 | Alfanumeryczna.<br><br>Zacznij od litery. |
> | serwis / apis | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / apis / problemy | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / apis / problemy / załączniki | Problem | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / apis / problemy / komentarze | Problem | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / apis / operacje | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / apis / operacje / tagi | Operacji | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / apis / wydania | api | 1-80 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Rozpocznij i zakończ alfanumerycznym lub podkreśleniem. |
> | usługa / apis / schematy | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / apis / tagDeskrypty | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / apis / tagi | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / zestawy api-version | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / autoryzacjaSerery | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługi / zaplecze | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / certyfikaty | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / diagnostyka | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / grupy | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / grupy / użytkownicy | group | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / tożsamośćProviders | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / rejestratory | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / powiadomienia | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / powiadomienia / odbiorcaUmaile | powiadomienie | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / openidConnectProviders | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługi / polisy | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / produkty | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / produkty / apis | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / produkty / grupy | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | serwis / produkty / tagi | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / właściwości | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / subskrypcje | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / tagi | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / szablony | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługi / użytkownicy | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Konfiguracja aplikacji Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | konfiguracjaSklepy | grupa zasobów | 5-50 | Alfanumeryczne, podkreślenia i łączniki. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Blokad | zakres cesji | 1-90 | Alfanumeryka, okresy, podkreślenia, łączniki i nawiasy.<br><br>Nie można zakończyć w okresie. |
> | zmiany w polityce | zakres cesji | 1-128 nazwa wyświetlana<br><br>1-260 nazwa zasobu | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie `%` może zawierać i nie może kończyć się kropką lub spacją. |
> | Policydefinitions | zakres definicji | 1-128 nazwa wyświetlana<br><br>1-260 nazwa zasobu | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie `%` może zawierać i nie może kończyć się kropką lub spacją. |
> | zasadySetDefinitions | zakres definicji | 1-128 nazwa wyświetlana<br><br>1-260 nazwa zasobu | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie `%` może zawierać i nie może kończyć się kropką lub spacją.  |

## <a name="microsoftautomation"></a>Automatyzacja firmy Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | automationKonta | grupa zasobów | 6-50 | Alfanumeryka i łączniki.<br><br>Zacznij od litery, a koniec alfanumerycznym. |
> | automationKonta / certyfikaty | konto automatyzacji | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można skończyć z miejsca.  |
> | automationKonta / połączenia | konto automatyzacji | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można skończyć z miejsca. |
> | automationKonta / poświadczenia | konto automatyzacji | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można skończyć z miejsca. |
> | automationKonty / elementy runbook | konto automatyzacji | 1-63 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij od litery.  |
> | automationKonta / harmonogramy | konto automatyzacji | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można skończyć z miejsca. |
> | automationKony / zmienne | konto automatyzacji | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można skończyć z miejsca. |
> | automationKonta / obserwatorzy | konto automatyzacji | 1-63 |  Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij od litery. |
> | automationKony / elementy elementów webhook | konto automatyzacji | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można skończyć z miejsca. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | konta batchAccounts | Region | 3-24 | Małe litery i cyfry. |
> | batchKonta / aplikacje | konto wsadowe | 1-64 | Alfanumeryczne, podkreślenia i łączniki. |
> | batchKonta / certyfikaty | konto wsadowe | 5-45 | Alfanumeryczne, podkreślenia i łączniki. |
> | batchKonta / pule | konto wsadowe | 1-64 | Alfanumeryczne, podkreślenia i łączniki. |

## <a name="microsoftblockchain"></a>Łańcuch bloków firmy Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | członkowie łańcucha bloków | global | 2-20 | Małe litery i cyfry.<br><br>Zacznij od małych liter. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | botUsługi | global | 2-64 |  Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. |
> | botSługi / kanały | usługa bot | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. |
> | botSługi / Połączenia | usługa bot | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. |
> | przedsiębiorstwoKanclerze | grupa zasobów | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. |

## <a name="microsoftcache"></a>Pamięć podręczna firmy Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. Kolejne łączniki nie są dozwolone. |
> | Redis / firewallRules | Redis | 1-256 | Alfanumeryka |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Profile | grupa zasobów | 1-260 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | profile / punkty końcowe | global | 1-50 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |

## <a name="microsoftcertificateregistration"></a>Rejestracja certyfikatów firmy Microsoft.Certificate

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | CertyfikatY | grupa zasobów | 3-30 | Alfanumeryczna. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | accounts | grupa zasobów | 2-64 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | availabilitySets | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Zakończ alfanumerycznym lub podkreśleniem. |
> | zestawy descryption | grupa zasobów | 1-80 | Alfanumeryczne i podkreślenia. |
> | Dysków | grupa zasobów | 1-80 | Alfanumeryczne i podkreślenia. |
> | Galerie | grupa zasobów | 1-80 | Alfanumeryka i okresy.<br><br>Zacznij i zakończ alfanumerycznym. |
> | galerie / aplikacje | galeria | 1-80 | Alfanumeryka, łączniki i kropki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | galerie / aplikacje/wersje | aplikacja | 32-bitowa całkowitej liczby | Liczby i okresy. |
> | galerie / zdjęcia | galeria | 1-80 | Alfanumeryka, łączniki i kropki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | galerie / obrazy / wersje | image | 32-bitowa całkowitej liczby | Liczby i okresy. |
> | images | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Zakończ alfanumerycznym lub podkreśleniem. |
> | Migawki | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Zakończ alfanumerycznym lub podkreśleniem. |
> | wirtualnematyny | grupa zasobów | 1-15 (Windows)<br>1-64 (Linux)<br><br>Patrz uwaga poniżej. | Nie można użyć:<br> `\/""[]:|<>+=;,?*@&`<br><br>Nie można zacząć od podkreślenia. Nie można zakończyć kropką lub łącznikiem. |
> | zestawy virtualMachineScaleSets | grupa zasobów | 1-15 (Windows)<br>1-64 (Linux)<br><br>Patrz uwaga poniżej. | Nie można użyć:<br> `\/""[]:|<>+=;,?*@&`<br><br>Nie można zacząć od podkreślenia. Nie można zakończyć kropką lub łącznikiem. |

> [!NOTE]
> Maszyny wirtualne platformy Azure mają dwie odrębne nazwy: nazwę zasobu i nazwę hosta. Podczas tworzenia maszyny wirtualnej w portalu, ta sama wartość jest używana dla obu nazw. Ograniczenia w powyższej tabeli są dla nazwy hosta. Rzeczywista nazwa zasobu może mieć długość maksymalnie 64 znaków.

## <a name="microsoftcontainerinstance"></a>Insstancja kontenera firmy Microsoft

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | kontenerGrupy | grupa zasobów | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. Następujące po sobie łączniki nie są dozwolone. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Rejestrów | global | 5-50 | Alfanumeryczna. |
> | rejestry / budowanieZadań | registry | 5-50 | Alfanumeryczna. |
> | rejestry / buildTasks/steps | tworzenie zadania | 5-50 | Alfanumeryczna. |
> | rejestry / replikacje | registry | 5-50 | Alfanumeryczna. |
> | rejestry / zakresMapy | registry | 5-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | rejestry / zadania | registry | 5-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | rejestry / żetony | registry | 5-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | rejestry / hook | registry | 5-50 | Alfanumeryczna. |

## <a name="microsoftcontainerservice"></a>Usługa kontenera firmy Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | zarządzaneKlustery | grupa zasobów | 1-63 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | openShiftManagedClusters | grupa zasobów | 1-30 | Alfanumeryczna. |

## <a name="microsoftcustomerinsights"></a>Informacje o klientach firmy Microsoft.Customer

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | koncentratory, | grupa zasobów | 1-64 | Alfanumeryczna.<br><br>Zacznij od litery.  |
> | koncentratory / autoryzacjaPolityka | centrum | 1-50 | Alfanumeryczne, podkreślenia i okresy.<br><br>Zacznij i zakończ alfanumerycznym. |
> | piasty / złącza | centrum | 1-128 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / złącza/mapowania | łącznik | 1-128 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / interakcje | centrum | 1-128 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / kpi | centrum | 1-512 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / linki | centrum | 1-512 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | piasty / prognozy | centrum | 1-512 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | piasty / profile | centrum | 1-128 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / relationshipLinks | centrum | 1-512 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / relacje | centrum | 1-512 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / roleAssignments | centrum | 1-128 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory / widoki | centrum | 1-512 | Alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |

## <a name="microsoftcustomproviders"></a>Usługi niestandardowe firmy Microsoft.Custom

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Stowarzyszenia | grupa zasobów | 1-180 | Nie można użyć:<br>`%&\\?/`<br><br>Nie można zakończyć kropką ani spacją. |
> | resourceProviders (Dostawcy zasobów) | grupa zasobów | 3-64 | Nie można użyć:<br>`%&\\?/`<br><br>Nie można zakończyć kropką ani spacją. |

## <a name="microsoftdatabox"></a>Skrzynka z danymi firmy Microsoft

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Zadania | grupa zasobów | 3-24 | Alfanumeryka, łączniki, podkreślenia i kropki. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | obszary robocze | grupa zasobów | 3-30 | Alfanumeryka, podkreślenia i łączniki |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Fabryk | global | 3-63 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | fabryki / przepływy danych | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumerycznego. |
> | fabryki / zestawy danych | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumerycznego. |
> | fabryki / integracjaRuntimes | Fabryki | 3-63 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | fabryki / linkedservices | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumerycznego. |
> | fabryki / rurociągi | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumerycznego. |
> | fabryki / wyzwalacze | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumerycznego. |
> | fabryki / wyzwalacze / rerunTriggers | Wyzwalacz | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumerycznego. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Małe litery i cyfry. |
> | konta / computePolicies | account | 3-60 | Alfanumeryczne, łączniki i podkreślenia. |
> | konta / dataLakeStoreKony | account | 3-24 | Małe litery i cyfry. |
> | konta / firewallRules | account | 3-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | konta / magazynKonta | account | 3-60 | Alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Małe litery i cyfry. |
> | konta / firewallRules | account | 3-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | konta / virtualNetworkRules | account | 3-50 | Alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | services | grupa zasobów | 2-62 | Alfanumeryka, łączniki, kropki i podkreślenia.<br><br>Zacznij od alfanumerycznego. |
> | usługi / projekty | usługa | 2-57 | Alfanumeryka, łączniki, kropki i podkreślenia.<br><br>Zacznij od alfanumerycznego. |

## <a name="microsoftdbformariadb"></a>Baza danych Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | serwery / bazy danych | Serwerów | 1-63 | Alfanumeryka i łączniki. |
> | serwery / firewallRules | Serwerów | 1-128 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery / virtualNetworkRules | Serwerów | 1-128 | Alfanumeryka i łączniki. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | serwery / bazy danych | Serwerów | 1-63 | Alfanumeryka i łączniki. |
> | serwery / firewallRules | Serwerów | 1-128 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery / virtualNetworkRules | Serwerów | 1-128 | Alfanumeryka i łączniki. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | serwery / bazy danych | Serwerów | 1-63 | Alfanumeryka i łączniki. |
> | serwery / firewallRules | Serwerów | 1-128 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery / virtualNetworkRules | Serwerów | 1-128 | Alfanumeryka i łączniki. |

## <a name="microsoftdevices"></a>Urządzenia firmy Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | IotHubs ( IotHubs ) | global | 3-50 | Alfanumeryka i łączniki.<br><br>Nie można zakończyć łącznikiem. |
> | IotHubs / certyfikaty | Iot | 1-64 | Alfanumeryka, łączniki, kropki i podkreślenia. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumeryka, łączniki, kropki i podkreślenia. |
> | inicjowanie obsługi administracyjnej Usługi | grupa zasobów | 3-64 | Alfanumeryka i łączniki.<br><br>Zakończ alfanumerycznym. |
> | inicjowanie obsługi administracyjnejUsługi / certyfikatów | inicjowanie obsługi administracyjnej Usługi | 1-64 | Alfanumeryka, łączniki, kropki i podkreślenia. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Labs | grupa zasobów | 1-50 | Alfanumeryczne, podkreślenia i łączniki. |
> | laboratoria / customimages | Lab | 1-80 | Alfanumeryczne, podkreślenia, łączniki i nawiasy. |
> | laboratoria / formuły | Lab | 1-80 | Alfanumeryczne, podkreślenia, łączniki i nawiasy. |
> | laboratoria / maszyny wirtualne | Lab | 1-15 (Windows)<br>1-64 (Linux) | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. Nie może być wszystkie liczby. |

## <a name="microsoftdocumentdb"></a>Baza danych microsoft.documentdb

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | konta danych | global | 3-31 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małych liter lub cyfr. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Domen | grupa zasobów | 3-50 | Alfanumeryka i łączniki. |
> | domeny / tematy | domena | 3-50 | Alfanumeryka i łączniki. |
> | eventSubscriptions | grupa zasobów | 3-64 | Alfanumeryka i łączniki. |
> | Tematy | grupa zasobów | 3-50 | Alfanumeryka i łączniki. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | grupa zasobów | 6-50 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. Zakończ literą lub cyfrą. |
> | Obszary nazw | global | 6-50 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. Zakończ literą lub cyfrą. |
> | przestrzenie nazw / AuthorizationRules | namespace | 1-50 | Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ literą lub cyfrą. |
> | przestrzenie nazw / disasterRecoveryConfigs | namespace | 1-50 | Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ literą lub cyfrą. |
> | przestrzenie nazw / eventhubs | namespace | 1-50 | Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ literą lub cyfrą. |
> | przestrzenie nazw / eventhubs / authorizationRules | centrum zdarzeń | 1-50 | Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ literą lub cyfrą. |
> | przestrzenie nazw / eventhubs / grupy konsumentów | centrum zdarzeń | 1-50 | Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ literą lub cyfrą. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | global | 3-59 | Alfanumeryka i łączniki<br><br>Zacznij i zakończ literą lub cyfrą. |

## <a name="microsoftimportexport"></a>Microsoft.ImportEksport

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Zadania | grupa zasobów | 2-64 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Składniki | grupa zasobów | 1-260 | Nie można użyć:<br>`%&\?/` <br><br>Nie można zakończyć spacją lub kropką.  |

## <a name="microsoftiotcentral"></a>Centrum IoT Firmy Microsoft

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małych liter lub cyfr. |

## <a name="microsoftkeyvault"></a>Usługa Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Sklepienia | global | 3-24 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. Zakończ literą lub cyfrą. Nie może zawierać kolejnych łączników. |
> | skarbce / sekrety | Magazyn | 1-127 | Alfanumeryka i łączniki. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | global | 4-22 | Małe litery i cyfry.<br><br>Zacznij od litery. |
> | /klastry / bazy danych | cluster | 1-260 | Alfanumeryka, łączniki, spacje i kropki. |
> | /klastry / bazy danych / dataConnections | database | 1-40 | Alfanumeryka, łączniki, spacje i kropki. |
> | /klastry / bazy danych / eventhubconnections | database | 1-40 | Alfanumeryka, łączniki, spacje i kropki. |

## <a name="microsoftlogic"></a>Logika firmy Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | integrationKontacje | grupa zasobów | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationKonta /zgromadzenia | łączniki | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationKonta /batchKonfiguracje | łączniki | 1-20 | Alfanumeryczna. |
> | integrationKonta /certyfikaty | łączniki | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationKonta /mapy | łączniki | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integracjaKonta / partnerzy | łączniki | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationKonfiguracje kont / rosettanetprocessconfigurations | łączniki | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationKonta /schematy | łączniki | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationKonta / sesje | łączniki | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |
> | integracjaServiceEnvironments | grupa zasobów | 1-80 | Alfanumeryka, łączniki, kropki i podkreślenia. |
> | integrationServiceEnvironments / managedApis | środowisko usług integracyjnych | 1-80 | Alfanumeryka, łączniki, kropki i podkreślenia. |
> | Przepływy pracy | grupa zasobów | 1-80 | Alfanumeryka, łączniki, podkreślenia, kropki i nawiasy. |

## <a name="microsoftmachinelearning"></a>Uczenie się maszyn firmy Microsoft.Machine

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | zobowiązaniePlany | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie można skończyć z spacją. |
> | Webservices | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie można skończyć z spacją. |
> | obszary robocze | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie można skończyć z spacją. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | obszary robocze | grupa zasobów | 3-33 | Alfanumeryka i łączniki. |
> | obszary robocze / obliczenia | obszar roboczy | 2-16 | Alfanumeryka i łączniki. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | użytkownikAssignedIdentities | grupa zasobów | 3-128 | Alfanumeryka, łączniki i podkreślenia<br><br>Zacznij od litery lub cyfry. |

## <a name="microsoftmaps"></a>Microsoft.Maps (Mapy firmy Microsoft)

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | accounts | grupa zasobów | 1-98 (dla nazwy grupy zasobów i nazwy konta) | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | usługi mediasce | grupa zasobów | 3-24 | Małe litery i cyfry. |
> | mediaservices / liveEvents | Obsługa multimediów | 1-32 | Alfanumeryka i łączniki.<br><br>Zacznij od alfanumerycznego. |
> | mediaservices / liveEvents / liveOutputs | Wydarzenie na żywo | 1-256 | Alfanumeryka i łączniki.<br><br>Zacznij od alfanumerycznego. |
> | mediaservices / streamingEndpoints | Obsługa multimediów | 1-24 | Alfanumeryka i łączniki.<br><br>Zacznij od alfanumerycznego. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | applicationGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | applicationSecurityGroups | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | azureFirewalls | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Zakończ alfanumerycznym lub podkreśleniem. |
> | bastionHosts | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | Połączenia | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | strefa dns | grupa zasobów | 1-63 znaki<br><br>Od 2 do 34 etykiet<br><br>Każda etykieta jest zbiorem znaków oddzielonych kropką. Na przykład **contoso.com** ma 2 etykiety. | Każda etykieta może zawierać alfanumeryczne, podkreślenia i łączniki.<br><br>Każda etykieta jest oddzielona kropką. |
> | expressRouteCircuits | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | firewallPolicies | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | firewallPolicies / ruleGrupy | zasady zapory | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | drzwi frontdoors | global | 5-64 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | obciążeniaBalancery | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | loadBalancers / inboundNatRules | moduł równoważenia obciążenia | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | localNetworkGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | siećPowierzchnie | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | networkSecurityGroups | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | networkSecurityGroups / securityRules | grupa zabezpieczeń sieci | 1-80 |  Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | networkWatchers (obserwatorzy sieci) | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | prywatneDnsZones | grupa zasobów | 1-63 znaki<br><br>Od 2 do 34 etykiet<br><br>Każda etykieta jest zbiorem znaków oddzielonych kropką. Na przykład **contoso.com** ma 2 etykiety. | Każda etykieta może zawierać alfanumeryczne, podkreślenia i łączniki.<br><br>Każda etykieta jest oddzielona kropką. |
> | privateDnsZones / virtualNetworkLinks | prywatna strefa DNS | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | publicYSadresy | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | publicIPPrefixes | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | routeFiltry | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | routeFiltry / routeFilterRules | filtr trasy | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | routeTables (routetables) | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | routeTables / trasy | tabela tras | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | serviceEndpointPolicies | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | profile trafficmanager | global | 1-63 | Alfanumeryka, łączniki i kropki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | virtualNetworkGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | wirtualne sieci | grupa zasobów | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | sieci wirtualne / podsieci | sieć wirtualna | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | virtualNetworks / virtualNetworkPeerings | sieć wirtualna | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | wirtualne wich | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | vpnGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | vpnGateways / vpnConnections | Brama sieci VPN | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |
> | vpnWitryny | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumerycznego. Koniec alfanumeryczny lub podkreślenia. |

## <a name="microsoftnotificationhubs"></a>Witryna Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Obszary nazw | global | 6-50 | Alfanumeryka i łączniki<br><br>Zacznij i zakończ alfanumerycznym. |
> | przestrzenie nazw / AuthorizationRules | namespace | 1-256 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Uruchom alfanumeryczne. |
> | przestrzenie nazw / notificationHubs | namespace | 1-260 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Uruchom alfanumeryczne. |
> | przestrzenie nazw / notificationHubs / AuthorizationRules | centrum powiadomień | 1-256 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Uruchom alfanumeryczne. |

## <a name="microsoftoperationalinsights"></a>Dane operacyjne firmy Microsoft.Operational

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | grupa zasobów | 4-63 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | obszary robocze | grupa zasobów | 4-63 | Alfanumeryka i łączniki.<br><br>Zacznij i zakończ alfanumerycznym. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | rozwiązania | obszar roboczy | Nie dotyczy | W przypadku rozwiązań autorstwa firmy Microsoft nazwa musi być we wzorcu:<br>`SolutionType(WorkspaceName)`<br><br>W przypadku rozwiązań autorstwa osób trzecich nazwa musi być we wzorze:<br>`SolutionType[WorkspaceName]`<br><br>Na przykład prawidłowa nazwa to:<br>`AntiMalware(contoso-IT)`<br><br>W typie rozwiązania jest rozróżniana wielkość liter. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | pulpity nawigacyjne | grupa zasobów | 3-160 | Alfanumeryka i łączniki.<br><br>Aby użyć znaków z ograniczeniami, dodaj znacznik o nazwie **ukryty tytuł** o nazwie pulpitu nawigacyjnego, której chcesz użyć. Portal wyświetla tę nazwę podczas wyświetlania pulpitu nawigacyjnego. |

## <a name="microsoftpowerbi"></a>Program Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | workspaceKolekcje | region | 3-63 | Alfanumeryka i łączniki.<br><br>Nie można rozpocząć od łącznika. Nie można używać kolejnych łączników. |

## <a name="microsoftpowerbidedicated"></a>Program Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Pojemności | region | 3-63 | Małe litery lub cyfry<br><br>Zacznij od małych liter. |

## <a name="microsoftrecoveryservices"></a>Usługi Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Sklepienia | grupa zasobów | 2-50 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. |
> | skarbce / backupPolicies | magazyn | 3-150 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. Nie można zakończyć łącznikiem. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Obszary nazw | global | 6-50 | Alfanumeryka i łączniki.<br><br>Wpisywanie zacznij od litery. Zakończ literą lub cyfrą. |
> | przestrzenie nazw / AuthorizationRules | namespace | 1-50 |  Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ alfanumerycznym. |
> | przestrzenie nazw / HybridConnections | namespace | 1-260 | Alfanumeryka, okresy, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | przestrzenie nazw / HybridConnections/authorizationRules | połączenie hybrydowe | 1-50 | Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ alfanumerycznym. |
> | przestrzenie nazw / WcfRelays | namespace | 1-260 | Alfanumeryka, okresy, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | przestrzenie nazw / WcfRelays / authorizationRules | Przekaźnik Wcf | 1-50 | Alfanumeryka, okresy, łączniki i podkreślenia.<br><br>Zacznij i zakończ alfanumerycznym. |

## <a name="microsoftresources"></a>Zasoby firmy Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Wdrożeń | grupa zasobów | 1-64 | Alfanumeryka, podkreślenia, nawiasy, łączniki i kropki. |
> | grupy zasobów | subskrypcja | 1-90 | Znaki alfanumeryczne, podkreślenia, nawiasy, łączniki, kropki i znaki unicode pasujące do [dokumentacji wyrażenia regularnego.](/rest/api/resources/resourcegroups/createorupdate)<br><br>Nie można zakończyć kropką. |
> | Nazwy tagów | zasób | 1-512 | Nie można użyć:<br>`<>%&\?/` |
> | nazwy tagów / tagValues | nazwa tagu | 1-256 | Wszystkie postacie. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Obszary nazw | global | 6-50 | Alfanumeryka i łączniki.<br><br>Wpisywanie zacznij od litery. Zakończ literą lub cyfrą.<br><br>Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru nazw](/rest/api/servicebus/create-namespace). |
> | przestrzenie nazw / AuthorizationRules | namespace | 1-50 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Zacznij i zakończ alpniejskiem. |
> | przestrzenie nazw / disasterRecoveryConfigs | global | 6-50 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. Zakończ alfanumerycznym. |
> | przestrzenie nazw / migrationConfigurations | namespace |  | Zawsze powinien być **$default**. |
> | przestrzenie nazw / kolejki | namespace | 1-260 | Alfanumeryka, okresy, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | przestrzenie nazw / kolejki / autoryzacjaRule | kolejka | 1-50 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Zacznij i zakończ alpniejskiem. |
> | przestrzenie nazw / tematy | namespace | 1-260 | Alfanumeryka, okresy, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i zakończ alfanumerycznym. |
> | przestrzenie nazw / tematy / autoryzacjaRule | temat | 1-50 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Zacznij i zakończ alpniejskiem. |
> | przestrzenie nazw / tematy / subskrypcje | temat | 1-50 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Zacznij i zakończ alpniejskiem. |
> | przestrzenie nazw / tematy / subskrypcje / zasady | subskrypcja | 1-50 | Alfanumeryka, kropki, łączniki i podkreślenia.<br><br>Zacznij i zakończ alpniejskiem. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | region | 4-23 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małych liter. Zakończ pisem małe lub cyfrą. |

## <a name="microsoftsignalrservice"></a>Usługa Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Signalr | global | 3-63 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. Zakończ literą lub cyfrą.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | zarządzaneInstności | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | Serwerów | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | serwery / administratorzy | serwer |  | Musi `ActiveDirectory`być . |
> | serwery / bazy danych | serwer | 1-128 | Nie można użyć:<br>`<>*%&:\/?`<br><br>Nie można zakończyć kropką ani spacją. |
> | serwery / bazy danych / syncGroups | database | 1-150 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery / elastyczne Poole | serwer | 1-128 | Nie można użyć:<br>`<>*%&:\/?`<br><br>Nie można zakończyć kropką ani spacją. |
> | serwery / failoverGroups | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | serwery / firewallRules | serwer | 1-128 | Nie można użyć:<br>`<>*%&:;\/?`<br><br>Nie można zakończyć kropką. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | konta przechowywania | global | 3-24 | Małe litery i cyfry. |
> | storageKonta / blobSługi | konto magazynu |  | Musi `default`być . |
> | storageKonta / blobSługi / kontenery | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małych liter lub cyfr. Nie można używać kolejnych łączników. |
> | storageKonta / fileServices | konto magazynu |  | Musi `default`być . |
> | storageKonta / fileSługi / udziały | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. Nie można używać kolejnych łączników. |
> | storageKonta / zarządzaniePolicys | konto magazynu |  | Musi `default`być . |
> | blob | kontener | 1-1024 | Wszystkie znaki adresu URL z uwzględnieniem wielkości liter |
> | kolejka | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. Nie można używać kolejnych łączników. |
> | tabela | konto magazynu | 3-63 | Alfanumeryczna.<br><br>Zacznij od litery. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | usługi storageSyncServices | grupa zasobów | 1-260 | Alfanumeryka, spacje, kropki, łączniki i podkreślenia.<br><br>Nie można zakończyć kropką ani spacją. |
> | storageSyncServices / syncGroups | usługa synchronizacji pamięci masowej | 1-260 | Alfanumeryka, spacje, kropki, łączniki i podkreślenia.<br><br>Nie można zakończyć kropką ani spacją. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Menedżerów | grupa zasobów | 2-50 | Alfanumeryka i łączniki.<br><br>Zacznij od litery. Zakończ alfanumerycznym. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalityka

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | streamingjobs | grupa zasobów | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs / funkcje | praca przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs / wejścia | praca przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs / wyjścia | praca przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs / przekształcenia | praca przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Środowiskach | grupa zasobów | 1-90 | Nie można użyć:<br>`'<>%&:\?/#` |
> | środowiska / accessPolicies | environment | 1-90 | Nie można użyć:<br> `'<>%&:\?/#` |
> | środowiska / eventSources | environment | 1-90 | Nie można użyć:<br>`'<>%&:\?/#` |
> | środowiska / referenceDataSets | environment | 3-63 | Alfanumeryka |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | farmy serwerowe | grupa zasobów | 1-40 | Alfanumeryka i łączniki. |
> | lokacje | global | 2-60 | Zawiera alfanumeryczne i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | witryny / szczeliny | lokacja | 2-59 | Alfanumeryka i łączniki. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać zalecenia dotyczące nazywania zasobów, zobacz [Gotowe: Zalecane konwencje nazewnictwa i znakowania](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
