---
title: Ograniczenia nazewnictwa zasobów
description: Przedstawia reguły i ograniczenia dotyczące nazewnictwa zasobów platformy Azure.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: e260c9055b26d82f2fd2f8458d287a35a838f40f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477795"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Reguły i ograniczenia nazewnictwa zasobów platformy Azure

Ten artykuł zawiera podsumowanie reguł nazewnictwa i ograniczeń dotyczących zasobów platformy Azure. Aby uzyskać zalecenia dotyczące nazewnictwa zasobów, zobacz [Zalecane konwencje nazewnictwa i tagowania.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

W tym artykule wymieniono zasoby według przestrzeni nazw dostawcy zasobów. Aby uzyskać listę sposobu, w jaki dostawcy zasobów pasują do usług platformy Azure, zobacz [Dostawcy zasobów dla usług platformy Azure.](azure-services-resource-providers.md)

W nazwach zasobów nie jest uwzględniania litera, chyba że została zanotowana w kolumnie prawidłowych znaków.

W poniższych tabelach termin alfanumeryczny odnosi się do:

* **a** do **z** (małe litery)
* **Od A** **do Z** (wielkie litery)
* **Od 0** do **9** (liczby)

## <a name="microsoftanalysisservices"></a>Microsoft.analysisservices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | grupa zasobów | 3-63 | Małe litery i cyfry.<br><br>Zacznij od małej litery. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | usługa | global | 1-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery, a kończyj na alfanumeryczne. |
> | usługa/interfejsy API | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / interfejsy API / problemy | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / interfejsy API / problemy / załączniki | issue | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / interfejsy API / problemy / komentarze | issue | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / interfejsy API / operacje | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / interfejsy API / operacje / tagi | operation | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / interfejsy API / wydania | api | 1-80 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym lub podkreśleniem. |
> | usługa / interfejsy API / schematy | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa /apis/tagDescriptions | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / interfejsy API / tagi | api | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / zestawy wersji interfejsu API | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | service/authorizationServers | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/zaplecza | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/certyfikaty | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/diagnostyka | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/grupy | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/grupy/użytkownicy | group | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | service/identityProviders | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/rejestratory | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/powiadomienia | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/powiadomienia/odbiorcaEmails | powiadomienie | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | service /openidConnectProviders | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/zasady | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/produkty | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / produkty / interfejsy API | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/produkty/grupy | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa / produkty / tagi | product | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/właściwości | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/subskrypcje | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/tagi | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/szablony | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |
> | usługa/użytkownicy | usługa | 1-256 | Nie można użyć:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | configurationStores (magazyny konfiguracji) | grupa zasobów | 5-50 | Alfanumeryczne, podkreślenia i łączniki. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Blokad | zakres przypisania | 1-90 | Alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy.<br><br>Nie może kończyć się w czasie. |
> | policyAssignments (przypisania zasad) | zakres przypisania | Nazwa wyświetlana 1–128<br><br>Nazwa zasobu od 1 do 64<br><br>Nazwa zasobu od 1 do 24 w zakresie grupy zarządzania | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie może zawierać i `%` nie może kończyć się spacją ani okresem. |
> | Policydefinitions | zakres definicji | Nazwa wyświetlana 1–128<br><br>Nazwa zasobu od 1 do 64 | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie może zawierać i `%` nie może kończyć się spacją ani okresem. |
> | policySetDefinitions | zakres definicji | Nazwa wyświetlana 1–128<br><br>Nazwa zasobu od 1 do 64<br><br>Nazwa zasobu od 1 do 24 w zakresie grupy zarządzania | Nazwa wyświetlana może zawierać dowolne znaki.<br><br>Nazwa zasobu nie może zawierać i `%` nie może kończyć się spacją ani okresem.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | automationAccounts | grupa zasobów & regionu <br>(Patrz uwaga poniżej) | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery i kończy się znakiem alfanumerycznym. |
> | automationAccounts /certificates | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie może kończyć się spacją.  |
> | automationAccounts /connections | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie może kończyć się spacją. |
> | automationAccounts /credentials | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie może kończyć się spacją. |
> | automationAccounts/runbook | konto usługi Automation | 1-63 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij od litery.  |
> | automationAccounts/schedules | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie może kończyć się spacją. |
> | automationAccounts /variables | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie może kończyć się spacją. |
> | automationAccounts /watchers | konto usługi Automation | 1-63 |  Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij od litery. |
> | automationAccounts /webhook | konto usługi Automation | 1-128 | Nie można użyć:<br> `<>*%&:\?.+/` <br><br>Nie może kończyć się spacją. |

> [!NOTE]
> Nazwy kont usługi Automation są unikatowe dla regionu i grupy zasobów. Nazwy usuniętych kont usługi Automation mogą nie być natychmiast dostępne.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Małe litery i cyfry. |
> | batchAccounts /applications | konto usługi Batch | 1-64 | Alfanumeryczne, podkreślenia i łączniki. |
> | batchAccounts /certificates | konto usługi Batch | 5-45 | Alfanumeryczne, podkreślenia i łączniki. |
> | batchAccounts/pools | konto usługi Batch | 1-64 | Alfanumeryczne, podkreślenia i łączniki. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | blockchainMembers | global | 2-20 | Małe litery i cyfry.<br><br>Zacznij od małej litery. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. |
> | botServices/channels | usługa bota | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. |
> | botServices /Connections | usługa bota | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. |
> | enterpriseChannels | grupa zasobów | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. Kolejne łączniki są niedozwolone. |
> | Redis /firewallRules | Redis | 1-256 | Alfanumeryczne |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Profile | grupa zasobów | 1-260 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | profile/punkty końcowe | global | 1-50 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | certificateOrders | grupa zasobów | 3-30 | Alfanumeryczne. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | grupa zasobów | 2-64 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | availabilitySets | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | diskEncryptionSets | grupa zasobów | 1-80 | Znaki alfanumeryczne i podkreślenia. |
> | Dysków | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia i łączniki. |
> | Galerie | grupa zasobów | 1-80 | Alfanumeryczne i kropki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | galerie/aplikacje | galeria | 1-80 | Alfanumeryczne, łączniki i kropki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | galerie/aplikacje/wersje | aplikacja | 32-bitowa liczba całkowita | Liczby i kropki. |
> | galerie/obrazy | galeria | 1-80 | Alfanumeryczne, podkreślenia, łączniki i kropki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | galerie/obrazy/wersje | image (obraz) | 32-bitowa liczba całkowita | Liczby i kropki. |
> | images | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | Migawki | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | virtualMachines (maszyny wirtualne) | grupa zasobów | 1–15 (Windows)<br>1–64 (Linux)<br><br>Patrz uwaga poniżej. | Nie można użyć spacji lub tych znaków:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Maszyny wirtualne z systemem Windows nie mogą zawierać łącznika ani okresu.<br><br>Maszyny wirtualne z systemem Linux nie mogą kończyć się terminem ani łącznikiem. |
> | virtualMachineScaleSets | grupa zasobów | 1–15 (Windows)<br>1–64 (Linux)<br><br>Patrz uwaga poniżej. | Nie można użyć spacji lub tych znaków:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Nie można rozpocząć od podkreślenia. Nie może kończyć się terminem lub łącznikiem. |

> [!NOTE]
> Maszyny wirtualne platformy Azure mają dwie odrębne nazwy: nazwę zasobu i nazwę hosta. Podczas tworzenia maszyny wirtualnej w portalu ta sama wartość jest używana dla obu nazw. Ograniczenia w powyższej tabeli dotyczą nazwy hosta. Rzeczywista nazwa zasobu może mieć długość maksymalnie 64 znaków.

## <a name="microsoftcommunication"></a>Microsoft.Communication

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | communicationServices | global | 1-63 | Znaki alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | containerGroups | grupa zasobów | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. Następujące po sobie łączniki nie są dozwolone. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Rejestrów | global | 5-50 | Alfanumeryczne. |
> | rejestry/buildTasks | registry | 5-50 | Alfanumeryczne. |
> | rejestry /buildTasks/steps | zadanie kompilacji | 5-50 | Alfanumeryczne. |
> | rejestry/replikacje | registry | 5-50 | Alfanumeryczne. |
> | rejestry/zakresMapy | registry | 5-50 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | rejestry/zadania | registry | 5-50 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | rejestry/tokeny | registry | 5-50 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | rejestry/webhook | registry | 5-50 | Alfanumeryczne. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | managedClusters | grupa zasobów | 1-63 | Alfanumeryczne, podkreślenia i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | openShiftManagedClusters | grupa zasobów | 1-30 | Alfanumeryczne. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | koncentratory, | grupa zasobów | 1-64 | Alfanumeryczne.<br><br>Zacznij od litery.  |
> | hubs/authorizationPolicies | centrum | 1-50 | Alfanumeryczne, podkreślenia i kropki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | koncentratory/łączniki | centrum | 1-128 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory/łączniki/mapowania | łącznik | 1-128 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | centra/interakcje | centrum | 1-128 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory/kluczowe wskaźniki wydajności | centrum | 1-512 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory/linki | centrum | 1-512 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | centra/przewidywania | centrum | 1-512 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | centra/profile | centrum | 1-128 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | koncentratory/relacjeLinki | centrum | 1-512 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | centra/relacje | centrum | 1-512 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | hubs/roleAssignments | centrum | 1-128 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |
> | centra/widoki | centrum | 1-512 | Znaki alfanumeryczne i podkreślenia.<br><br>Zacznij od litery. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Stowarzyszenia | grupa zasobów | 1-180 | Nie można użyć:<br>`%&\\?/`<br><br>Nie może kończyć się spacją lub okresem. |
> | resourceProviders | grupa zasobów | 3-64 | Nie można użyć:<br>`%&\\?/`<br><br>Nie może kończyć się spacją lub okresem. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Zadania | grupa zasobów | 3-24 | Alfanumeryczne, łączniki, podkreślenia i kropki. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | obszary robocze | grupa zasobów | 3-30 | Alfanumeryczne, podkreślenia i łączniki |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Fabryk | global | 3-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | fabryki/przepływy danych | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumeryczne. |
> | fabryki/zestawy danych | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumeryczne. |
> | fabryki/integracjaRuntimes | Fabryki | 3-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | fabryki/połączone usługi | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumeryczne. |
> | fabryki/potoki | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumeryczne. |
> | fabryki/wyzwalacze | Fabryki | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumeryczne. |
> | fabryki/wyzwalacze/ponowne uruchomienieTriggers | Wyzwalacz | 1-260 | Nie można użyć:<br>`<>*#.%&:\\+?/`<br><br>Zacznij od alfanumeryczne. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | global | 3-24 | Małe litery i cyfry. |
> | accounts/computePolicies | account | 3-60 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | accounts/dataLakeStoreAccounts | account | 3-24 | Małe litery i cyfry. |
> | accounts/firewallRules | account | 3-50 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | accounts/storageAccounts | account | 3-60 | Znaki alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | global | 3-24 | Małe litery i cyfry. |
> | accounts/firewallRules | account | 3-50 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | accounts/virtualNetworkRules | account | 3-50 | Znaki alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | services | grupa zasobów | 2-62 | Alfanumeryczne, łączniki, kropki i podkreślenia.<br><br>Zacznij od alfanumeryczne. |
> | usługi/projekty | usługa | 2-57 | Alfanumeryczne, łączniki, kropki i podkreślenia.<br><br>Zacznij od alfanumeryczne. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpoczynać ani kończyć łącznikiem. |
> | serwery/bazy danych | Serwerów | 1-63 | Alfanumeryczne i łączniki. |
> | servers/firewallRules | Serwerów | 1-128 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | servers/virtualNetworkRules | Serwerów | 1-128 | Alfanumeryczne i łączniki. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | serwery/bazy danych | Serwerów | 1-63 | Alfanumeryczne i łączniki. |
> | servers/firewallRules | Serwerów | 1-128 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | servers/virtualNetworkRules | Serwerów | 1-128 | Alfanumeryczne i łączniki. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Serwerów | global | 3-63 | Małe litery, łączniki i cyfry.<br><br>Nie można rozpoczynać ani kończyć łącznikiem. |
> | serwery/bazy danych | Serwerów | 1-63 | Alfanumeryczne i łączniki. |
> | servers/firewallRules | Serwerów | 1-128 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | servers/virtualNetworkRules | Serwerów | 1-128 | Alfanumeryczne i łączniki. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanumeryczne i łączniki.<br><br>Nie może kończyć się łącznikiem. |
> | IotHubs /certificates | Iot | 1-64 | Znaki alfanumeryczne, łączniki, kropki i podkreślenia. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumeryczne, łączniki, kropki i podkreślenia. |
> | provisioningServices | grupa zasobów | 3-64 | Alfanumeryczne i łączniki.<br><br>Kończy się znakiem alfanumerycznym. |
> | provisioningServices/certificates | provisioningServices | 1-64 | Alfanumeryczne, łączniki, kropki i podkreślenia. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Labs | grupa zasobów | 1-50 | Alfanumeryczne, podkreślenia i łączniki. |
> | labs/customimages | Lab | 1-80 | Alfanumeryczne, podkreślenia, łączniki i nawiasy. |
> | laboratoria/formuły | Lab | 1-80 | Znaki alfanumeryczne, podkreślenia, łączniki i nawiasy. |
> | laboratoria/maszyny wirtualne | Lab | 1–15 (Windows)<br>1–64 (Linux) | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. Nie może to być wszystkie liczby. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | databaseAccounts (konta bazy danych) | global | 3-44 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery lub numeru. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Domen | grupa zasobów | 3-50 | Alfanumeryczne i łączniki. |
> | domeny/tematy | domena | 3-50 | Alfanumeryczne i łączniki. |
> | eventSubscriptions | grupa zasobów | 3-64 | Alfanumeryczne i łączniki. |
> | Tematy | grupa zasobów | 3-50 | Alfanumeryczne i łączniki. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | grupa zasobów | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub liczbą. |
> | Obszary nazw | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub liczbą. |
> | namespaces /AuthorizationRules | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj literą lub liczbą. |
> | namespaces/disasterRecoveryConfigs | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj literą lub liczbą. |
> | namespaces/eventhubs | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj literą lub liczbą. |
> | namespaces/eventhubs/authorizationRules | centrum zdarzeń | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj literą lub liczbą. |
> | namespaces/eventhubs/consumergroups | centrum zdarzeń | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj literą lub liczbą. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | global | 3-59 | Alfanumeryczne i łączniki<br><br>Zacznij i kończyj literą lub liczbą. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Zadania | grupa zasobów | 2-64 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | actionGroups | grupa zasobów | 1-260 | Nie można użyć:<br>`/&%\?` <br><br>Nie może kończyć się spacją lub okresem.  |
> | Składniki | grupa zasobów | 1-260 | Nie można użyć:<br>`%&\?/` <br><br>Nie może kończyć się spacją lub okresem.  |
> | scheduledQueryRules | grupa zasobów | 1-260 | Nie można użyć:<br>`*<>%{}&:\\?/#` <br><br>Nie może kończyć się spacją lub okresem.  |
> | metricAlerts | grupa zasobów | 1-260 | Nie można użyć:<br>`*#&+:<>?@%{}\/` <br><br>Nie może kończyć się spacją lub okresem.  |
> | activityLogAlerts | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%{}&:\\?+/#` <br><br>Nie może kończyć się spacją lub okresem.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery lub numeru. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Sklepienia | global | 3-24 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub cyfrą. Nie może zawierać kolejnych łączników. |
> | magazyny/wpisy tajne | Magazyn | 1-127 | Alfanumeryczne i łączniki. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | global | 4-22 | Małe litery i cyfry.<br><br>Zacznij od litery. |
> | /clusters /databases | cluster | 1-260 | Alfanumeryczne, łączniki, spacje i kropki. |
> | /clusters / databases / dataConnections | database | 1-40 | Alfanumeryczne, łączniki, spacje i kropki. |
> | /clusters / databases / eventhubconnections | database | 1-40 | Alfanumeryczne, łączniki, spacje i kropki. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | integrationAccounts | grupa zasobów | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts/assemblies | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts / batchConfigurations | łączniki | 1-20 | Alfanumeryczne. |
> | integrationAccounts /certificates | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts/maps | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts /partners | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts /ttanetprocessconfigurations | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts /schemas (konta/schematy) | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationAccounts /sessions | łączniki | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |
> | integrationServiceEnvironments | grupa zasobów | 1-80 | Alfanumeryczne, łączniki, kropki i podkreślenia. |
> | integrationServiceEnvironments/ managedApis | środowisko usługi integracji | 1-80 | Alfanumeryczne, łączniki, kropki i podkreślenia. |
> | Przepływy pracy | grupa zasobów | 1-80 | Alfanumeryczne, łączniki, podkreślenia, kropki i nawiasy. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | commitmentPlans | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie może kończyć się spacją. |
> | Webservices | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie może kończyć się spacją. |
> | obszary robocze | grupa zasobów | 1-260 | Nie można użyć:<br>`<>*%&:?+/\\`<br><br>Nie może kończyć się spacją. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | obszary robocze | grupa zasobów | 3-33 | Alfanumeryczne i łączniki. |
> | obszary robocze/obliczenia | obszar roboczy | 2–16 | Alfanumeryczne i łączniki. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | userAssignedIdentities (identyfikatory użytkowników) | grupa zasobów | 3-128 | Alfanumeryczne, łączniki i podkreślenia<br><br>Zacznij od litery lub liczby. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Konta | grupa zasobów | 1–98 (w przypadku nazwy grupy zasobów i nazwy konta) | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | mediaservices | grupa zasobów | 3-24 | Małe litery i cyfry. |
> | mediaservices/liveEvents | Media Service | 1-32 | Alfanumeryczne i łączniki.<br><br>Zacznij od alfanumeryczne. |
> | mediaservices/liveEvents/liveOutputs | Wydarzenie na żywo | 1-256 | Alfanumeryczne i łączniki.<br><br>Zacznij od alfanumeryczne. |
> | mediaservices/streamingEndpoints | Usługa multimediów | 1-24 | Alfanumeryczne i łączniki.<br><br>Zacznij od alfanumeryczne. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | applicationGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | applicationSecurityGroups | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | azureFirewalls | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | bastionHosts | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | Połączenia | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | dnsZones | grupa zasobów | Od 1 do 63 znaków<br><br>Od 2 do 34 etykiet<br><br>Każda etykieta jest zestawem znaków oddzielonych kropką. Na przykład **contoso.com** 2 etykiety. | Każda etykieta może zawierać znaki alfanumeryczne, podkreślenia i łączniki.<br><br>Każda etykieta jest oddzielona kropką. |
> | expressRouteCircuits | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | firewallPolicies | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | firewallPolicies/ruleGroups | zasady zapory | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | frontDoors (frontDoors) | global | 5-64 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | frontdoorWebApplicationFirewallPolicies | grupa zasobów | 1-128 | Alfanumeryczne.<br><br>Zacznij od litery. |
> | loadBalancers | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | loadBalancers / inboundNatRules | load balancer | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | localNetworkGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | networkInterfaces | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | networkSecurityGroups | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | networkSecurityGroups / securityRules | sieciowa grupa zabezpieczeń | 1-80 |  Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | networkWatchers | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | privateDnsZones | grupa zasobów | Od 1 do 63 znaków<br><br>Od 2 do 34 etykiet<br><br>Każda etykieta jest zestawem znaków rozdzielonych kropką. Na przykład **contoso.com** 2 etykiety. | Każda etykieta może zawierać znaki alfanumeryczne, podkreślenia i łączniki.<br><br>Każda etykieta jest oddzielona kropką. |
> | privateDnsZones / virtualNetworkLinks | prywatna strefa DNS | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | publicIPAddresses | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | publicIPPrefixes | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | routeFilters | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | routeFilters/routeFilterRules | filtr tras | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | routeTables | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | routeTables /routes | tabela tras | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | serviceEndpointPolicies | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | trafficmanagerprofiles | global | 1-63 | Alfanumeryczne, łączniki i kropki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | virtualNetworkGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | virtualNetworks | grupa zasobów | 2-64 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |
> | virtualnetworks/podsieci | sieć wirtualna | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | virtualNetworks/virtualNetworkPeerings | sieć wirtualna | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | virtualWans | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | vpnGateways | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | vpnGateways / vpnConnections | Brama sieci VPN | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Zakończenie alfanumeryczne lub podkreślenie. |
> | vpnSites | grupa zasobów | 1-80 | Alfanumeryczne, podkreślenia, kropki i łączniki.<br><br>Zacznij od alfanumeryczne. Kończy się znakiem alfanumerycznym lub podkreśleniem. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Obszary nazw | global | 6-50 | Alfanumeryczne i łączniki<br><br>Zacznij od litery. Kończy się znakiem alfanumerycznym. |
> | namespaces /AuthorizationRules | namespace | 1-256 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Rozpocznij alfanumeryczne. |
> | namespaces/notificationHubs | namespace | 1-260 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Rozpocznij alfanumeryczne. |
> | namespaces / notificationHubs / AuthorizationRules | centrum powiadomień | 1-256 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Uruchom alfanumeryczne. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | grupa zasobów | 4-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | obszary robocze | global | 4-63 | Alfanumeryczne i łączniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | rozwiązania | obszar roboczy | Nie dotyczy | W przypadku rozwiązań owanych przez firmę Microsoft nazwa musi być we wzorcu:<br>`SolutionType(WorkspaceName)`<br><br>W przypadku rozwiązań owanych przez inne firmy nazwa musi być we wzorcu:<br>`SolutionType[WorkspaceName]`<br><br>Na przykład prawidłowa nazwa to:<br>`AntiMalware(contoso-IT)`<br><br>W typie rozwiązania jest wielkość liter. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | pulpity nawigacyjne | grupa zasobów | 3-160 | Alfanumeryczne i łączniki.<br><br>Aby użyć znaków z ograniczeniami, dodaj tag o nazwie **hidden-title** z nazwą pulpitu nawigacyjnego, którego chcesz użyć. W portalu ta nazwa jest wyświetlana podczas wyświetlania pulpitu nawigacyjnego. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Alfanumeryczne i łączniki.<br><br>Nie można rozpocząć od łącznika. Nie można używać kolejnych łączników. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Pojemności | region | 3-63 | Małe litery lub cyfry<br><br>Zacznij od małej litery. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Sklepienia | grupa zasobów | 2-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. |
> | magazyny/backupPolicies | magazyn | 3-150 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Nie może kończyć się łącznikiem. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Obszary nazw | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Wpisywanie zacznij od litery. Kończy się literą lub liczbą. |
> | namespaces/AuthorizationRules | namespace | 1-50 |  Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | namespaces/HybridConnections | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | namespaces / HybridConnections/authorizationRules | połączenie hybrydowe | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | namespaces / WcfRelays | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | namespaces / WcfRelays / authorizationRules | Przekaźnik WCF | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Wdrożeń | grupa zasobów | 1-64 | Alfanumeryczne, podkreślenia, nawiasy, łączniki i kropki. |
> | resourcegroups | subskrypcja | 1-90 | Znaki alfanumeryczne, podkreślenia, nawiasy, łączniki, kropki i znaki Unicode zgodne z [dokumentacją wyrażenia regularnego](/rest/api/resources/resourcegroups/createorupdate).<br><br>Nie może kończyć się okresem. |
> | tagNames (nazwy tagów) | zasób | 1-512 | Nie można użyć:<br>`<>%&\?/` |
> | tagNames/tagValues | nazwa tagu | 1-256 | Wszystkie znaki. |
> | templateSpecs | grupa zasobów | 1-90 | Alfanumeryczne, podkreślenia, nawiasy, łączniki i kropki. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Obszary nazw | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Wpisywanie zacznij od litery. Kończy się literą lub liczbą.<br><br>Aby uzyskać więcej informacji, zobacz [Tworzenie przestrzeni nazw](/rest/api/servicebus/create-namespace). |
> | namespaces/AuthorizationRules | namespace | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj na alfnumeryczne. |
> | namespaces/disasterRecoveryConfigs | global | 6-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się znakiem alfanumerycznym. |
> | namespaces/migrationConfigurations | namespace |  | Zawsze powinna **być**$default . |
> | przestrzenie nazw/kolejki | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | namespaces/queues/authorizationRules | kolejka | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj na alfnumeryczne. |
> | przestrzenie nazw/tematy | namespace | 1-260 | Alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki.<br><br>Zacznij i kończyj ciągiem alfanumerycznym. |
> | namespaces/topics/authorizationRules | temat | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj na alfnumeryczne. |
> | przestrzenie nazw/tematy/subskrypcje | temat | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj na alfnumeryczne. |
> | przestrzenie nazw / tematy / subskrypcje / reguły | subskrypcja | 1-50 | Alfanumeryczne, kropki, łączniki i podkreślenia.<br><br>Zacznij i kończyj na alfnumeryczne. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Klastrów | region | 4-23 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery. Kończy się małą literą lub liczbą. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Signalr | global | 3-63 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się literą lub liczbą.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpoczynać ani kończyć łącznikiem. <br><br> Nie może mieć żadnych znaków specjalnych, takich jak `@` . |
> | Serwerów | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpoczynać ani kończyć łącznikiem. |
> | serwery/administratorzy | serwer |  | Musi to być `ActiveDirectory` . |
> | serwery/bazy danych | serwer | 1-128 | Nie można użyć:<br>`<>*%&:\/?`<br><br>Nie może kończyć się spacją lub okresem. |
> | serwery/bazy danych/syncGroups | database | 1-150 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | serwery/pule elasticPool | serwer | 1-128 | Nie można użyć:<br>`<>*%&:\/?`<br><br>Nie może kończyć się spacją lub okresem. |
> | servers/failoverGroups | global | 1-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | servers/firewallRules | serwer | 1-128 | Nie można użyć:<br>`<>*%&:;\/?`<br><br>Nie może kończyć się okresem. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | konta magazynu | global | 3-24 | Małe litery i cyfry. |
> | storageAccounts /blobServices | konto magazynu |  | Musi to być `default` . |
> | storageAccounts / blobServices / containers | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Zacznij od małej litery lub numeru. Nie można używać kolejnych łączników. |
> | storageAccounts / fileServices | konto magazynu |  | Musi to być `default` . |
> | storageAccounts / fileServices / shares | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpoczynać ani kończyć łącznikiem. Nie można używać kolejnych łączników. |
> | storageAccounts /managementZasad | konto magazynu |  | Musi to być `default` . |
> | blob | kontener | 1-1024 | Wszelkie znaki adresu URL, z których jest wzróżniana wielkość liter |
> | kolejka | konto magazynu | 3-63 | Małe litery, cyfry i łączniki.<br><br>Nie można rozpoczynać ani kończyć łącznikiem. Nie można używać kolejnych łączników. |
> | tabela | konto magazynu | 3-63 | Alfanumeryczne.<br><br>Zacznij od litery. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | storageSyncServices | grupa zasobów | 1-260 | Alfanumeryczne, spacje, kropki, łączniki i podkreślenia.<br><br>Nie może kończyć się spacją lub okresem. |
> | storageSyncServices/syncGroups | usługa synchronizacji magazynu | 1-260 | Alfanumeryczne, spacje, kropki, łączniki i podkreślenia.<br><br>Nie może kończyć się spacją lub okresem. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Menedżerów | grupa zasobów | 2-50 | Alfanumeryczne i łączniki.<br><br>Zacznij od litery. Kończy się znakiem alfanumerycznym. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | streamingjobs | grupa zasobów | 3-63 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs/functions | zadanie przesyłania strumieniowego | 3-63 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs/inputs | zadanie przesyłania strumieniowego | 3-63 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs /outputs | zadanie przesyłania strumieniowego | 3-63 | Znaki alfanumeryczne, łączniki i podkreślenia. |
> | streamingjobs /transformations | zadanie przesyłania strumieniowego | 3-63 | Znaki alfanumeryczne, łączniki i podkreślenia. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | Środowiskach | grupa zasobów | 1-90 | Nie można użyć:<br>`'<>%&:\?/#` |
> | środowiska/accessPolicies | środowisko | 1-90 | Nie można użyć:<br> `'<>%&:\?/#` |
> | środowiska/eventSources | środowisko | 1-90 | Nie można użyć:<br>`'<>%&:\?/#` |
> | środowiska/referenceDataSets | środowisko | 3-63 | Alfanumeryczne |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Jednostka | Zakres | Długość | Prawidłowe znaki |
> | --- | --- | --- | --- |
> | certyfikaty | grupa zasobów | 1-260 | Nie można użyć:<br>`/` <br><br>Nie może kończyć się spacją lub okresem.  | 
> | serverfarms | grupa zasobów | 1-40 | Alfanumeryczne i łączniki. |
> | lokacje | globalne lub na domenę. Patrz uwaga poniżej. | 2-60 | Zawiera znaki alfanumeryczne i łączniki.<br><br>Nie można rozpocząć ani zakończyć łącznikiem. |
> | lokacje/miejsca | lokacja | 2-59 | Alfanumeryczne i łączniki. |

> [!NOTE]
> Witryna internetowa musi mieć globalnie unikatowy adres URL. Podczas tworzenia witryny internetowej korzystającej z planu hostingu adres URL to `http://<app-name>.azurewebsites.net` . Nazwa aplikacji musi być globalnie unikatowa. Podczas tworzenia witryny internetowej korzystającej z App Service Environment nazwa aplikacji musi być unikatowa w obrębie domeny dla [App Service Environment](../../app-service/environment/using-an-ase.md#app-access). W obu przypadkach adres URL witryny jest globalnie unikatowy.
>
> Azure Functions mają takie same reguły i ograniczenia nazewnictwa jak Microsoft.Web/sites.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać zalecenia dotyczące nazywania zasobów, zobacz [Ready: Recommended naming and tagging conventions (Gotowe:](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)zalecane konwencje nazewnictwa i tagowania).
