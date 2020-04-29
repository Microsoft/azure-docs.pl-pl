---
title: Karta dysk testowy maszyny wirtualnej w portal Cloud Partner portalu Azure Marketplace
description: Opisuje kartę dysk testowy służącą do tworzenia oferty maszyny wirtualnej portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: adac73d64feb6280c5043776249072e9f7595faa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142989"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta Stacja testowa maszyn wirtualnych

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

Karta **Stacja testowa** na stronie **Nowa oferta** umożliwia klientom potencjalnych klientów korzystanie z praktycznego pokazu najważniejszych funkcji i korzyści Twojego produktu, które przedstawiono w ustandaryzowanym scenariuszu.  Test Drive to opcjonalna funkcja dla typów ofert, które obsługują test na dysku.  Wersja testowa wymaga prawidłowego wdrożenia zasobów pomocniczych.  Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Aby włączyć tę funkcję, na karcie **dysk testowy** kliknij opcję **tak** , aby **włączyć dysk testowy**.  Na karcie **dysk testowy** są wyświetlane pola dostępne do edycji.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest to wymagane.

![Karta Stacja testowa w formularzu Nowa oferta dla maszyn wirtualnych](./media/publishvm_007.png)


## <a name="field-values"></a>Wartości pól

W poniższej tabeli opisano przeznaczenie i zawartość tych pól.  Wymagane pola są indicted przez gwiazdkę (*).


|    Pole                  |       Opis                                                            |
|  ---------                |     ---------------                                                          |
|  *Szczegóły*   |  |
| **Opis\***           | Zadbaj o to, aby zapoznać się z omówieniem scenariusza testowego. Ten tekst będzie widoczny dla użytkownika podczas aprowizacji dysku testowego. To pole obsługuje podstawowy kod HTML, jeśli chcesz udostępnić sformatowaną zawartość.  |
| **Ręczne użytkownika\***           | Przekaż szczegółowy podręcznik użytkownika (. PDF), który pomaga testować, jak korzystać z rozwiązania.  |
| **Wideo demonstracyjne na dysku testowym** | Przekaż wideo, które przedstawia Twoje rozwiązanie.  W przypadku wybrania tej opcji należy podać nazwę, adres URL wideo (hostowane w serwisie YouTube lub Vimeo) oraz miniaturę (533x324 pikseli) dla filmu wideo. |
| *Konfiguracja techniczna* |  |
| **Liczba\***             | Określ dostępność regionów i stosunkowo dostęp do wystąpienia maszyny wirtualnej (kliknij ikonę informacji, aby uzyskać więcej informacji).  <br/>W przypadku potencjalnych współbieżnych sesji dysków testowych nie należy przekroczyć limitu przydziału dla subskrypcji.  Dawniej jest obliczony jako: [liczba wybranych regionów] x [wystąpienia aktywne] + [wybrane regiony] x [rozgrzane wystąpienia] + [liczba wybranych regionów] x [zimne wystąpienia] |
| **Czas trwania dysku testowego\***   | Maksymalny czas trwania sesji w godzinach. Sesja dysku testowego kończy się automatycznie po przekroczeniu tego okresu.  |
|**Szablon ARM dysku testowego\***| Przekaż szablon Azure Resource Manager skojarzony z tym dyskiem testowym. Aby uzyskać więcej informacji, zobacz [transformacjeing a Virtual Machine Deployment Template for Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Informacje o dostępie\***    | Azure Resource Manager dostęp do informacji o logowaniu i wersji próbnej, zapisaną jako zwykły tekst lub prosty kod HTML. |
| *Szczegóły subskrypcji wdrożenia dysku testowego* |  |
| **Identyfikator subskrypcji platformy Azure\*** | Można uzyskać, logując się do [Microsoft Azure Portal](https://ms.portal.azure.com) i klikając pozycję **subskrypcje** na lewym pasku menu. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ten identyfikator powinien być identyfikatorem GUID formularza `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Identyfikator dzierżawy usługi Azure AD\***    | Azure Active Directory identyfikator dzierżawy.  Można uzyskać, logując się do [Microsoft Azure Portal](https://ms.portal.azure.com) i klikając przycisk **Azure Active Directory** na lewym pasku menu, a następnie klikając pozycję **Właściwości** na środkowym pasku menu, a następnie kopiując **Identyfikator katalogu** z formularza.  Ten identyfikator powinien być również identyfikatorem GUID.  Jeśli to pole jest puste, należy utworzyć identyfikator dzierżawy dla swojej organizacji. |
| **Identyfikator aplikacja usługi Azure AD\***       | Identyfikator zarejestrowanego rozwiązania maszyny wirtualnej platformy Azure  |
| **Klucz aplikacja usługi Azure AD\***      | Klucz uwierzytelniania dla zarejestrowanego rozwiązania |
|   |   |


## <a name="next-steps"></a>Następne kroki

Na następnej karcie [Marketplace](./cpp-marketplace-tab.md) znajdziesz informacje marketingowe i prawne dotyczące Twojego rozwiązania.
