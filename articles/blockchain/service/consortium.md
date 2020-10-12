---
title: Azure łańcucha bloków Service Consortium
description: Omówienie sposobu implementowania sieci łańcucha bloków konsorcjum przez usługę Azure łańcucha bloków.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712534"
---
# <a name="azure-blockchain-service-consortium"></a>Azure łańcucha bloków Service Consortium

Za pomocą usługi Azure łańcucha bloków można utworzyć sieci prywatnych konsorcjum łańcucha bloków, w przypadku których każda sieć łańcucha bloków może być ograniczona do określonych uczestników sieci. Tylko uczestnicy w sieci prywatnej konsorcjum łańcucha bloków mogą wyświetlać łańcucha bloków i korzystać z nich. Sieci konsorcjum w usłudze Azure łańcucha bloków Service mogą zawierać dwa typy ról uczestników elementu członkowskiego:

* Uczestnicy z uprawnieniami **administratora** , którzy mogą podejmować działania związane z zarządzaniem konsorcjum i mogą uczestniczyć w transakcjach łańcucha bloków.

* **Użytkownicy** — uczestnicy, którzy nie mogą podejmować żadnych działań związanych z zarządzaniem konsorcjum, ale mogą uczestniczyć w transakcjach łańcucha bloków.

Sieci konsorcjum mogą być różnymi rolami uczestników i mogą mieć dowolną liczbę poszczególnych typów ról. Musi istnieć co najmniej jeden administrator.

Na poniższym diagramie przedstawiono sieć konsorcjum z wieloma uczestnikami:

![Diagram prywatnej sieci konsorcjum](./media/consortium/network-diagram.png)

Za pomocą zarządzania konsorcjum w usłudze Azure łańcucha bloków można zarządzać uczestnikami w sieci konsorcjum. Zarządzanie konsorcjum opiera się na jednomyślnym modelu sieci. W bieżącej wersji zapoznawczej usługa Azure łańcucha bloków zapewnia scentralizowany model konsensusu do zarządzania konsorcjum. Każdy uczestnik uprzywilejowany z rolą Administruj może podejmować działania związane z zarządzaniem konsorcjum, takie jak dodawanie lub usuwanie uczestników sieci.

## <a name="roles"></a>Role

Uczestnicy konsorcjum mogą być osobami lub organizacjami i mogą mieć przypisaną rolę użytkownika lub administratora. Poniższa tabela zawiera listę różnic wysokiego poziomu między obiema rolami:

| Akcja | Rola użytkownika | Rola Administrator
|--------|:----:|:------------:|
| Utwórz nowy element członkowski | Tak | Tak |
| Zaproś nowych członków | Nie | Tak |
| Ustaw lub Zmień rolę uczestnika elementu członkowskiego | Nie | Tak |
| Zmień nazwę wyświetlaną elementu członkowskiego | Tylko dla własnej składowej | Tylko dla własnej składowej |
| Usuwanie członków | Tylko dla własnej składowej | Tak |
| Uczestnictwo w transakcjach łańcucha bloków | Tak | Tak |

### <a name="user-role"></a>Rola użytkownika

Użytkownicy są uczestnikami konsorcjum bez możliwości administratora. Nie mogą oni uczestniczyć w zarządzaniu członkami związanymi z konsorcjum. Użytkownicy mogą zmienić nazwę wyświetlaną swojej składowej i mogą usunąć siebie z konsorcjum.

### <a name="administrator"></a>Administrator

Administrator może zarządzać członkami w ramach konsorcjum. Administrator może zapraszać członków, usuwać członków lub aktualizować role członków w ramach konsorcjum.
W ramach konsorcjum musi być zawsze co najmniej jeden administrator. Ostatni administrator musi określić innego uczestnika jako rolę administratora przed opuszczeniem konsorcjum.

## <a name="managing-members"></a>Zarządzanie elementami członkowskimi

Tylko Administratorzy mogą zapraszać innych uczestników do konsorcjum. Administratorzy zapraszali uczestników przy użyciu identyfikatora subskrypcji platformy Azure.

Po zaproszeniu uczestnicy mogą dołączać do konsorcjum łańcucha bloków, wdrażając nową składową w usłudze Azure łańcucha bloków Service. Aby wyświetlić zaproszonych konsorcjum i dołączyć do niego, należy określić ten sam Identyfikator subskrypcji platformy Azure, który jest używany w zaproszeniu przez administratora sieci.

Administratorzy mogą usunąć dowolnego uczestnika z konsorcjum, w tym innych administratorów. Członkowie mogą usuwać tylko siebie z konsorcjum.

## <a name="consortium-management-smart-contract"></a>Inteligentny kontrakt zarządzania konsorcjum

Zarządzanie konsorcjum w usłudze Azure łańcucha bloków Service jest realizowane za pośrednictwem inteligentnych kontraktów zarządzania konsorcjum. Inteligentne kontrakty są automatycznie wdrażane w węzłach podczas wdrażania nowego elementu członkowskiego łańcucha bloków.

Adres inteligentnego kontraktu zarządzania konsorcjum głównego można wyświetlić w Azure Portal. **Adres RootContract** znajduje się w sekcji Przegląd składowej łańcucha bloków.

![Adres RootContract](./media/consortium/rootcontract-address.png)

Można korzystać z inteligentnego kontraktu zarządzania konsorcjum przy użyciu [modułu PowerShell](manage-consortium-powershell.md)zarządzania konsorcjum, Azure Portal lub bezpośrednio za pośrednictwem kontraktu inteligentnego przy użyciu usługi Azure łańcucha bloków, która wygenerowała konto Ethereum.

## <a name="ethereum-account"></a>Konto Ethereum

Po utworzeniu elementu członkowskiego zostaje utworzony klucz konta Ethereum. Usługa Azure łańcucha bloków używa klucza do tworzenia transakcji związanych z zarządzaniem konsorcjum. Klucz konta Ethereum jest zarządzany przez usługę Azure łańcucha bloków automatycznie.

Konto elementu członkowskiego można wyświetlić w Azure Portal. Konto elementu członkowskiego znajduje się w sekcji Przegląd składowej łańcucha bloków.

![Konto elementu członkowskiego](./media/consortium/member-account.png)

Możesz zresetować konto Ethereum, klikając konto elementu członkowskiego i wprowadzając nowe hasło. Zarówno adres konta Ethereum, jak i hasło zostaną zresetowane.  

## <a name="next-steps"></a>Następne kroki

Do akcji zarządzania konsorcjum można uzyskać dostęp za poorednictwem programu PowerShell. Aby uzyskać więcej informacji, zobacz [Zarządzanie członkami konsorcjum w usłudze Azure łańcucha bloków Service przy użyciu programu PowerShell](manage-consortium-powershell.md).
