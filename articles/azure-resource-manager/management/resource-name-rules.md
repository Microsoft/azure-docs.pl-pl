---
title: Ograniczenia dotyczące nazw zasobów
description: Pokazuje reguły i ograniczenia dotyczące nazewnictwa zasobów platformy Azure.
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: bd8ba72d43b7e3e5a9145d756741436f0a49e18b
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762485"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Reguły i ograniczenia nazewnictwa zasobów platformy Azure

Ten artykuł podsumowuje reguły nazewnictwa i ograniczenia dotyczące zasobów platformy Azure. Aby zapoznać się z zaleceniami dotyczącymi nazw zasobów, zobacz [zalecane konwencje nazewnictwa i tagowania](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

W tym artykule wymieniono zasoby według przestrzeni nazw dostawcy zasobów. Aby uzyskać listę sposobu, w jaki dostawcy zasobów są zgodni z usługami platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).

W nazwach zasobów nie jest rozróżniana wielkość liter, o ile nie zaznaczono inaczej w kolumnie nieprawidłowe znaki.

W poniższych tabelach termin alfanumeryczny odnosi się do:

* **od a** do **z** (małe litery)
* **Od A** do **z** (wielkie litery)
* **od 0** do **9** (liczba)

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | serwerem | grupa zasobów | 3-63 | Małe litery i cyfry.<br><br>Zacznij od małej litery. |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | usługa | global | 1-50 | Znaki alfanumeryczne i łączniki.<br><br>Zacznij od litery i kończyć się znakiem alfanumerycznym. |
> | Usługa/interfejsy API | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/problemy | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/problemy/załączniki | issue | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/problemy/Komentarze | issue | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/operacje | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/operacje/Tagi | operation | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/wersje | api | 1-80 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym lub podkreśleniem. |
> | Usługa/interfejsy API/schematy | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/tagDescriptions | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/interfejsy API/Tagi | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Service/API-Version-Sets | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/authorizationServers | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/nadkończenie | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/certyfikaty | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/Diagnostyka | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/grupy | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/grupy/Użytkownicy | group | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/skojarzeni | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/rejestratory | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/powiadomienia | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/powiadomienia/recipientEmails | powiadomienie | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/openidConnectProviders | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/Zasady | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/produkty | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/produkty/interfejsy API | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/produkty/grupy | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/produkty/Tagi | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/właściwości | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/subskrypcje | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/Tagi | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/szablony | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | Usługa/użytkownicy | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | configurationStores | grupa zasobów | 5-50 | Alfanumeryczne, podkreślenia i łączniki. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Zamki | zakres przypisania | 1-90 | Alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy.<br><br>Nie można zakończyć w okresie. |
> | policyAssignments | zakres przypisania | 1-128 wyświetlana nazwa<br><br>Nazwa zasobu 1-64<br><br>1-24 nazwa zasobu w zakresie grupy zarządzania | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie może zawierać `%` i nie może kończyć się kropką ani spacją. |
> | policyDefinitions | zakres definicji | 1-128 wyświetlana nazwa<br><br>Nazwa zasobu 1-64 | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie może zawierać `%` i nie może kończyć się kropką ani spacją. |
> | policySetDefinitions | zakres definicji | 1-128 wyświetlana nazwa<br><br>Nazwa zasobu 1-64<br><br>1-24 nazwa zasobu w zakresie grupy zarządzania | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie może zawierać `%` i nie może kończyć się kropką ani spacją.  |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | automationAccounts | grupa zasobów | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery i kończyć się znakiem alfanumerycznym. |
> | automationAccounts/certyfikaty | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można kończyć się spacją.  |
> | automationAccounts/połączenia | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można kończyć się spacją. |
> | automationAccounts/poświadczenia | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można kończyć się spacją. |
> | automationAccounts/elementy Runbook | konto usługi Automation | 1-63 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij od litery.  |
> | automationAccounts/harmonogramy | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można kończyć się spacją. |
> | automationAccounts/zmienne | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można kończyć się spacją. |
> | automationAccounts/obserwatorzy | konto usługi Automation | 1-63 |  Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij od litery. |
> | automationAccounts/elementy webhook | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie można kończyć się spacją. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | batchAccounts | Region (Region) | 3-24 | Małe litery i cyfry. |
> | batchAccounts/aplikacje | konto wsadowe | 1-64 | Alfanumeryczne, podkreślenia i łączniki. |
> | batchAccounts/certyfikaty | konto wsadowe | 5-45 | Alfanumeryczne, podkreślenia i łączniki. |
> | batchAccounts/pule | konto wsadowe | 1-64 | Alfanumeryczne, podkreślenia i łączniki. |

## <a name="microsoftblockchain"></a>Microsoft. łańcucha bloków

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2-20 | Małe litery i cyfry.<br><br>Zacznij od małej litery. |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |
> | botServices/kanały | Usługa bot | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |
> | botServices/połączenia | Usługa bot | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |
> | enterpriseChannels | grupa zasobów | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. Kolejne łączniki są niedozwolone. |
> | Redis/firewallRules | Redis | 1-256 | Znaki alfanumeryczne |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Profil | grupa zasobów | 1-260 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | Profile/punkty końcowe | global | 1-50 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | certificateOrders | grupa zasobów | 3-30 | Znaki alfanumeryczne. |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | grupa zasobów | 2-64 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | availabilitySets | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | diskEncryptionSets | grupa zasobów | 1-80 | Znaki alfanumeryczne i znaki podkreślenia. |
> | dysku | grupa zasobów | 1-80 | Znaki alfanumeryczne i znaki podkreślenia. |
> | znajduj | grupa zasobów | 1-80 | Alfanumeryczne i kropki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | Galerie/aplikacje | galeria | 1-80 | Alfanumeryczne, łączniki i kropki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | Galerie/aplikacje/wersje | aplikacja | 32-bitowa liczba całkowita | Liczby i kropki. |
> | Galerie/obrazy | galeria | 1-80 | Alfanumeryczne, podkreślenia, łączniki i kropki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | Galerie/obrazy/wersje | image (obraz) | 32-bitowa liczba całkowita | Liczby i kropki. |
> | images | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | Migawki | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | virtualMachines | grupa zasobów | 1-15 (system Windows)<br>1-64 (Linux)<br><br>Zobacz uwagę poniżej. | Nie można użyć spacji ani następujących znaków:<br> `\/"'[]:|<>+=;,?*@&_`<br><br>Maszyny wirtualne z systemem Windows nie mogą zawierać kropki ani kończyć się łącznikiem.<br><br>Maszyny wirtualne z systemem Linux nie mogą kończyć się kropką ani łącznikiem. |
> | virtualMachineScaleSets | grupa zasobów | 1-15 (system Windows)<br>1-64 (Linux)<br><br>Zobacz uwagę poniżej. | Nie można użyć spacji ani następujących znaków:<br> `\/"'[]:|<>+=;,?*@&`<br><br>Nie można rozpocząć od znaku podkreślenia. Nie może kończyć się kropką ani łącznikiem. |

> [!NOTE]
> Maszyny wirtualne platformy Azure mają dwie odrębne nazwy: nazwę zasobu i nazwę hosta. Podczas tworzenia maszyny wirtualnej w portalu ta sama wartość jest używana dla obu nazw. Ograniczenia w powyższej tabeli dotyczą nazwy hosta. Rzeczywista nazwa zasobu może mieć długość maksymalnie 64 znaków.

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | containerGroups | grupa zasobów | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. Następujące po sobie łączniki nie są dozwolone. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | wołuje | global | 5-50 | Znaki alfanumeryczne. |
> | rejestry/buildTasks | registry | 5-50 | Znaki alfanumeryczne. |
> | rejestry/buildTasks/kroki | zadanie kompilacji | 5-50 | Znaki alfanumeryczne. |
> | rejestry/replikacje | registry | 5-50 | Znaki alfanumeryczne. |
> | rejestry/scopeMaps | registry | 5-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | rejestry/zadania | registry | 5-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | rejestry/tokeny | registry | 5-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | rejestry/elementy webhook | registry | 5-50 | Znaki alfanumeryczne. |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | managedClusters | grupa zasobów | 1-63 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | openShiftManagedClusters | grupa zasobów | 1-30 | Znaki alfanumeryczne. |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | koncentratory, | grupa zasobów | 1-64 | Znaki alfanumeryczne.<br><br>Zacznij od litery.  |
> | Centra/authorizationPolicies | centrum | 1-50 | Alfanumeryczne, podkreślenia i kropki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | Centra/łączniki | centrum | 1-128 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/łączniki/mapowania | łącznik | 1-128 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/interakcje | centrum | 1-128 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/KWW | centrum | 1-512 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/linki | centrum | 1-512 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/przewidywania | centrum | 1-512 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/profile | centrum | 1-128 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/relationshipLinks | centrum | 1-512 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/relacje | centrum | 1-512 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/roleAssignments | centrum | 1-128 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |
> | Centra/widoki | centrum | 1-512 | Znaki alfanumeryczne i znaki podkreślenia.<br><br>Zacznij od litery. |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | związku | grupa zasobów | 1-180 | Nie można użyć:<br>`%&\\?/`<br><br>Nie może kończyć się kropką ani spacją. |
> | resourceProviders | grupa zasobów | 3-64 | Nie można użyć:<br>`%&\\?/`<br><br>Nie może kończyć się kropką ani spacją. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | zadania | grupa zasobów | 3-24 | Alfanumeryczne, łączniki, podkreślenia i kropki. |

## <a name="microsoftdatabricks"></a>Microsoft. datacegły

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | obszary robocze | grupa zasobów | 3-30 | Alfanumeryczne, podkreślenia i łączniki |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | fabryki | global | 3-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | fabryki/dataflows | indywidual | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od znaków alfanumerycznych. |
> | fabryki/zestawy danych | indywidual | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od znaków alfanumerycznych. |
> | fabryki/integrationRuntimes | indywidual | 3-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | fabryki/linkedservices | indywidual | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od znaków alfanumerycznych. |
> | fabryki/potoki | indywidual | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od znaków alfanumerycznych. |
> | fabryki/wyzwalacze | indywidual | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od znaków alfanumerycznych. |
> | fabryki/wyzwalacze/rerunTriggers | uruchamiać | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od znaków alfanumerycznych. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | global | 3-24 | Małe litery i cyfry. |
> | konta/computePolicies | account | 3-60 | Alfanumeryczne, łączniki i podkreślenia. |
> | konta/dataLakeStoreAccounts | account | 3-24 | Małe litery i cyfry. |
> | konta/firewallRules | account | 3-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | konta/storageAccounts | account | 3-60 | Alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsoftdatalakestore"></a>Microsoft. kontach datalakestore

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | global | 3-24 | Małe litery i cyfry. |
> | konta/firewallRules | account | 3-50 | Alfanumeryczne, łączniki i podkreślenia. |
> | konta/virtualNetworkRules | account | 3-50 | Alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsoftdatamigration"></a>Migracja Microsoft.

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | services | grupa zasobów | 2-62 | Alfanumeryczne, łączniki, kropki i znaki podkreślenia.<br><br>Zacznij od znaków alfanumerycznych. |
> | usługi/projekty | usługa | 2-57 | Alfanumeryczne, łączniki, kropki i znaki podkreślenia.<br><br>Zacznij od znaków alfanumerycznych. |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | serwerem | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. |
> | Serwery/bazy danych | serwerem | 1-63 | Alfanumeryczne i łączniki. |
> | serwery/firewallRules | serwerem | 1-128 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery/virtualNetworkRules | serwerem | 1-128 | Alfanumeryczne i łączniki. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | serwerem | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. |
> | Serwery/bazy danych | serwerem | 1-63 | Alfanumeryczne i łączniki. |
> | serwery/firewallRules | serwerem | 1-128 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery/virtualNetworkRules | serwerem | 1-128 | Alfanumeryczne i łączniki. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | serwerem | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. |
> | Serwery/bazy danych | serwerem | 1-63 | Alfanumeryczne i łączniki. |
> | serwery/firewallRules | serwerem | 1-128 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery/virtualNetworkRules | serwerem | 1-128 | Alfanumeryczne i łączniki. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanumeryczne i łączniki.<br><br>Nie można kończyć się łącznikiem. |
> | IotHubs/certyfikaty | Centrum IoT | 1-64 | Alfanumeryczne, łączniki, kropki i znaki podkreślenia. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumeryczne, łączniki, kropki i znaki podkreślenia. |
> | provisioningServices | grupa zasobów | 3-64 | Alfanumeryczne i łączniki.<br><br>Kończy się znakiem alfanumerycznym. |
> | provisioningServices/certyfikaty | provisioningServices | 1-64 | Alfanumeryczne, łączniki, kropki i znaki podkreślenia. |

## <a name="microsoftdevtestlab"></a>Microsoft. wspólny

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Labs | grupa zasobów | 1-50 | Alfanumeryczne, podkreślenia i łączniki. |
> | Labs/customimages | trybie | 1-80 | Alfanumeryczne, podkreślenia, łączniki i nawiasy. |
> | laboratoria/formuły | trybie | 1-80 | Alfanumeryczne, podkreślenia, łączniki i nawiasy. |
> | Labs/virtualmachines | trybie | 1-15 (system Windows)<br>1-64 (Linux) | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. Nie mogą być wszystkie cyfry. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-44 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery lub cyfry. |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | 3.x | grupa zasobów | 3-50 | Alfanumeryczne i łączniki. |
> | domeny/tematy | domena | 3-50 | Alfanumeryczne i łączniki. |
> | eventSubscriptions | grupa zasobów | 3-64 | Alfanumeryczne i łączniki. |
> | opisano | grupa zasobów | 3-50 | Alfanumeryczne i łączniki. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | oparty | grupa zasobów | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub cyfrą. |
> | przestrzeni | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub cyfrą. |
> | przestrzenie nazw/reguł autoryzacji | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Początek i koniec z literą lub cyfrą. |
> | przestrzenie nazw/disasterRecoveryConfigs | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Początek i koniec z literą lub cyfrą. |
> | przestrzenie nazw/eventhubs | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Początek i koniec z literą lub cyfrą. |
> | przestrzenie nazw/eventhubs/reguł autoryzacji | centrum zdarzeń | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Początek i koniec z literą lub cyfrą. |
> | przestrzenie nazw/eventhubs/consumergroups | centrum zdarzeń | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Początek i koniec z literą lub cyfrą. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | oparty | global | 3-59 | Alfanumeryczne i łączniki<br><br>Początek i koniec z literą lub cyfrą. |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | zadania | grupa zasobów | 2-64 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | actionGroups | grupa zasobów | 1-260 | Nie można użyć:<br>`/&%\?` <br><br>Nie można kończyć się spacją ani kropką.  |
> | komponentów | grupa zasobów | 1-260 | Nie można użyć:<br>`%&\?/` <br><br>Nie można kończyć się spacją ani kropką.  |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery lub cyfry. |

## <a name="microsoftkeyvault"></a>Microsoft. kluczy — magazyn

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | magazynów | global | 3-24 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub cyfrą. Nie może zawierać kolejnych łączników. |
> | magazyny/wpisy tajne | Magazyn | 1-127 | Alfanumeryczne i łączniki. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | oparty | global | 4-22 | Małe litery i cyfry.<br><br>Zacznij od litery. |
> | /Clusters/bazy danych | cluster | 1-260 | Alfanumeryczne, łączniki, spacje i kropki. |
> | /Clusters/Databases/dataconnections | database | 1-40 | Alfanumeryczne, łączniki, spacje i kropki. |
> | /Clusters/Databases/eventhubconnections | database | 1-40 | Alfanumeryczne, łączniki, spacje i kropki. |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | integrationAccounts | grupa zasobów | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts/zestawy | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts / batchConfigurations | łączniki | 1-20 | Znaki alfanumeryczne. |
> | integrationAccounts/certyfikaty | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts/Maps | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts/partnerzy | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts / rosettanetprocessconfigurations | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts/schematy | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts/sesje | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationServiceEnvironments | grupa zasobów | 1-80 | Alfanumeryczne, łączniki, kropki i znaki podkreślenia. |
> | integrationServiceEnvironments/managedApis | środowisko usługi integracji | 1-80 | Alfanumeryczne, łączniki, kropki i znaki podkreślenia. |
> | przebieg | grupa zasobów | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | commitmentPlans | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie może kończyć się spacją. |
> | webServices | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie może kończyć się spacją. |
> | obszary robocze | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie może kończyć się spacją. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | obszary robocze | grupa zasobów | 3-33 | Alfanumeryczne i łączniki. |
> | obszary robocze/obliczenia | obszar roboczy | 2–16 | Alfanumeryczne i łączniki. |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Resourceidentity | grupa zasobów | 3-128 | Alfanumeryczne, łączniki i podkreślenia<br><br>Zacznij od litery lub cyfry. |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | grupa zasobów | 1-98 (dla nazwy grupy zasobów i nazwy konta) | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | MediaServices | grupa zasobów | 3-24 | Małe litery i cyfry. |
> | MediaServices/liveEvents | Usługa multimediów | 1-32 | Alfanumeryczne i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |
> | MediaServices/liveEvents/liveOutputs | Wydarzenie na żywo | 1-256 | Alfanumeryczne i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |
> | MediaServices/streamingEndpoints | Usługa multimediów | 1-24 | Alfanumeryczne i łączniki.<br><br>Zacznij od znaków alfanumerycznych. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | applicationGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | applicationSecurityGroups | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | azureFirewalls | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | bastionHosts | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | Licznik | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | dnsZones | grupa zasobów | 1-63 znaków<br><br>od 2 do 34 etykiet<br><br>Każda etykieta jest zestawem znaków oddzielonych kropką. Na przykład **contoso.com** ma 2 etykiety. | Każda etykieta może zawierać znaki alfanumeryczne, podkreślenia i łączniki.<br><br>Każda etykieta jest oddzielona kropką. |
> | expressRouteCircuits | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | firewallPolicies | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | firewallPolicies / ruleGroups | zasady zapory | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | Usługi frontdoor | global | 5-64 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | frontdoorWebApplicationFirewallPolicies | grupa zasobów | 1-128 | Znaki alfanumeryczne.<br><br>Zacznij od litery. |
> | loadBalancers | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | loadBalancers/inboundNatRules | Moduł równoważenia obciążenia | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | localNetworkGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | networkInterfaces | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | networkSecurityGroups | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | networkSecurityGroups/securityRules | sieciowa Grupa zabezpieczeń | 1-80 |  Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | networkWatchers | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | privateDnsZones | grupa zasobów | 1-63 znaków<br><br>od 2 do 34 etykiet<br><br>Każda etykieta jest zestawem znaków oddzielonych kropką. Na przykład **contoso.com** ma 2 etykiety. | Każda etykieta może zawierać znaki alfanumeryczne, podkreślenia i łączniki.<br><br>Każda etykieta jest oddzielona kropką. |
> | privateDnsZones / virtualNetworkLinks | prywatna strefa DNS | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | Adresów publicipaddress | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | publicIPPrefixes | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | routeFilters | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | routeFilters / routeFilterRules | filtr tras | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | routeTables | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | routeTables/trasy | tabela tras | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | serviceEndpointPolicies | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | trafficmanagerprofiles | global | 1-63 | Alfanumeryczne, łączniki i kropki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | virtualNetworkGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | virtualNetworks | grupa zasobów | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | virtualnetworks/podsieci | Sieć wirtualna | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | virtualNetworks/virtualNetworkPeerings | Sieć wirtualna | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | virtualWans | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | vpnGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | vpnGateways / vpnConnections | Brama sieci VPN | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |
> | vpnSites | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od znaków alfanumerycznych. Kończący alfanumeryczne lub podkreślenie. |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | przestrzeni | global | 6-50 | Alfanumeryczne i łączniki<br><br>Zacznij od litery. Kończy się znakiem alfanumerycznym. |
> | przestrzenie nazw/reguł autoryzacji | namespace | 1-256 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Rozpocznij alfanumeryczne. |
> | przestrzenie nazw/notificationHubs | namespace | 1-260 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Rozpocznij alfanumeryczne. |
> | przestrzenie nazw/notificationHubs/reguł autoryzacji | Centrum powiadomień | 1-256 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Rozpocznij alfanumeryczne. |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | oparty | grupa zasobów | 4-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | obszary robocze | global | 4-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | rozwiązania | obszar roboczy | Nie dotyczy | W przypadku rozwiązań utworzonych przez firmę Microsoft nazwa musi być we wzorcu:<br>`SolutionType(WorkspaceName)`<br><br>W przypadku rozwiązań utworzonych przez inne firmy nazwa musi być we wzorcu:<br>`SolutionType[WorkspaceName]`<br><br>Na przykład prawidłowa nazwa to:<br>`AntiMalware(contoso-IT)`<br><br>W typie rozwiązania jest rozróżniana wielkość liter. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | pulpity nawigacyjne | grupa zasobów | 3-160 | Alfanumeryczne i łączniki.<br><br>Aby użyć znaków z ograniczeniami, Dodaj tag o nazwie **Hidden-title** z nazwą pulpitu nawigacyjnego, której chcesz użyć. Podczas wyświetlania pulpitu nawigacyjnego w portalu zostanie wyświetlona nazwa. |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Alfanumeryczne i łączniki.<br><br>Nie można rozpocząć od łącznika. Nie można używać kolejnych łączników. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | produkcyjnych | region | 3-63 | Małe litery lub cyfry<br><br>Zacznij od małej litery. |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | magazynów | grupa zasobów | 2-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. |
> | magazyny/backupPolicies | magazyn | 3-150 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Nie można kończyć się łącznikiem. |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | przestrzeni | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Wpisywanie zacznij od litery. Kończy się literą lub cyfrą. |
> | przestrzenie nazw/reguł autoryzacji | namespace | 1-50 |  Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | przestrzenie nazw/HybridConnections | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | przestrzenie nazw/HybridConnections/reguł autoryzacji | połączenie hybrydowe | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | przestrzenie nazw/WcfRelays | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | przestrzenie nazw/WcfRelays/reguł autoryzacji | Przekaźnik WCF | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | komputerów | grupa zasobów | 1-64 | Alfanumeryczne, podkreślenia, nawiasy, łączniki i kropki. |
> | ResourceGroups | subskrypcja | 1-90 | Alfanumeryczne, podkreślenia, nawiasy, łączniki, kropki i znaki Unicode, które pasują do [dokumentacji wyrażenia regularnego](/rest/api/resources/resourcegroups/createorupdate).<br><br>Nie może kończyć się kropką. |
> | tagNames | zasób | 1-512 | Nie można użyć:<br>`<>%&\?/` |
> | tagNames/tagValues | nazwa tagu | 1-256 | Wszystkie znaki. |
> | templateSpecs | grupa zasobów | 1-90 | Alfanumeryczne, podkreślenia, nawiasy, łączniki i kropki. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | przestrzeni | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Wpisywanie zacznij od litery. Kończy się literą lub cyfrą.<br><br>Aby uzyskać więcej informacji, zobacz [Tworzenie przestrzeni nazw](/rest/api/servicebus/create-namespace). |
> | przestrzenie nazw/reguł autoryzacji | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i Zakończ z alphnumeric. |
> | przestrzenie nazw/disasterRecoveryConfigs | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się znakiem alfanumerycznym. |
> | przestrzenie nazw/migrationConfigurations | namespace |  | Zawsze powinna być **$default**. |
> | przestrzenie nazw/kolejki | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | przestrzenie nazw/kolejki/reguł autoryzacji | kolejka | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i Zakończ z alphnumeric. |
> | obszary nazw/tematy | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyć znakiem alfanumerycznym. |
> | przestrzenie nazw/tematy/reguł autoryzacji | temat | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i Zakończ z alphnumeric. |
> | obszary nazw/tematy/subskrypcje | temat | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i Zakończ z alphnumeric. |
> | obszary nazw/tematy/subskrypcje/reguły | subskrypcja | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i Zakończ z alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft. servicefabric

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | oparty | region | 4-23 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery. Kończy się małą literą lub cyfrą. |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | signalR | global | 3-63 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub cyfrą.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. <br><br> Nie może zawierać żadnych znaków specjalnych, takich jak `@` . |
> | serwerem | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. |
> | serwery/Administratorzy | serwer |  | Musi być `ActiveDirectory` . <br><br> Nie może zawierać żadnych znaków specjalnych, takich jak `@` .|
> | Serwery/bazy danych | serwer | 1-128 | Nie można użyć:<br>`<>*%&:\/?`<br><br>Nie może kończyć się kropką ani spacją. |
> | Serwery/bazy danych/syncGroups | database | 1-150 | Alfanumeryczne, łączniki i podkreślenia. |
> | serwery/elasticPools | serwer | 1-128 | Nie można użyć:<br>`<>*%&:\/?`<br><br>Nie może kończyć się kropką ani spacją. |
> | serwery/failoverGroups | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. |
> | serwery/firewallRules | serwer | 1-128 | Nie można użyć:<br>`<>*%&:;\/?`<br><br>Nie może kończyć się kropką. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Małe litery i cyfry. |
> | storageAccounts/blobServices | konto magazynu |  | Musi być `default` . |
> | storageAccounts/blobServices/kontenery | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery lub cyfry. Nie można używać kolejnych łączników. |
> | storageAccounts/fileServices | konto magazynu |  | Musi być `default` . |
> | storageAccounts/fileServices/udziały | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. Nie można używać kolejnych łączników. |
> | storageAccounts/managementPolicies | konto magazynu |  | Musi być `default` . |
> | blob | kontener | 1-1024 | Wszystkie znaki adresu URL, z uwzględnieniem wielkości liter |
> | kolejka | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. Nie można używać kolejnych łączników. |
> | tabela | konto magazynu | 3-63 | Znaki alfanumeryczne.<br><br>Zacznij od litery. |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | storageSyncServices | grupa zasobów | 1-260 | Alfanumeryczne, spacje, kropki, łączniki i podkreślenia.<br><br>Nie może kończyć się kropką ani spacją. |
> | storageSyncServices / syncGroups | Usługa synchronizacji magazynu | 1-260 | Alfanumeryczne, spacje, kropki, łączniki i podkreślenia.<br><br>Nie może kończyć się kropką ani spacją. |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | skonfigurowany | grupa zasobów | 2-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się znakiem alfanumerycznym. |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | streamingjobs | grupa zasobów | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs/Functions | zadanie przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs/dane wejściowe | zadanie przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs/dane wyjściowe | zadanie przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs/przekształcenia | zadanie przesyłania strumieniowego | 3-63 | Alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | wiejski | grupa zasobów | 1-90 | Nie można użyć:<br>`'<>%&:\?/#` |
> | środowiska/accessPolicies | środowisko | 1-90 | Nie można użyć:<br> `'<>%&:\?/#` |
> | środowiska/źródła zdarzeń | środowisko | 1-90 | Nie można użyć:<br>`'<>%&:\?/#` |
> | środowiska/referenceDataSets | środowisko | 3-63 | Znaki alfanumeryczne |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | certyfikaty | grupa zasobów | 1-260 | Nie można użyć:<br>`/` <br><br>Nie można kończyć się spacją ani kropką.  | 
> | dopuszczalna | grupa zasobów | 1-40 | Alfanumeryczne i łączniki. |
> | lokacje | global | 2-60 | Zawiera znaki alfanumeryczne i łączniki.<br><br>Nie można rozpocząć ani zakończyć z łącznikiem. |
> | Lokacje/miejsca | lokacja | 2-59 | Alfanumeryczne i łączniki. |

> [!NOTE]
> Azure Functions ma takie same reguły nazewnictwa i ograniczenia jak Microsoft. Web/Sites.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z zaleceniami dotyczącymi określania nazw zasobów, zobacz [gotowe: zalecane konwencje nazewnictwa i tagowania](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
