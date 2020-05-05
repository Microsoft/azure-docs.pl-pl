---
title: Co zrobić w przypadku przerwania działania usługi platformy Azure, które ma wpływ na Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Dowiedz się, co należy zrobić w przypadku przerw w działaniu usługi platformy Azure, które ma wpływ na Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: sudbalas
ms.openlocfilehash: 4796e6c555ca67794409fb1476f3c4fd0d760719
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780457"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault dostępność i nadmiarowość

Azure Key Vault funkcje wielu warstw nadmiarowości, aby upewnić się, że klucze i wpisy tajne pozostaną dostępne dla aplikacji, nawet jeśli poszczególne składniki usługi nie powiodą się.

Zawartość Twojego magazynu kluczy jest replikowana w regionie i do regionu pomocniczego, co najmniej 150 kilometrów, ale w tej samej lokalizacji geograficznej. Zapewnia to wysoką trwałość kluczy i wpisów tajnych. Aby uzyskać szczegółowe informacje na temat konkretnych par regionów, zobacz dokument dotyczący [sparowanych regionów platformy Azure](../../best-practices-availability-paired-regions.md) .

Jeśli poszczególne składniki w ramach usługi magazynu kluczy zakończą się niepowodzeniem, alternatywne składniki w ramach regionu w celu zapewnienia obsługi żądania, aby upewnić się, że nie ma obniżenia funkcjonalności. Nie trzeba podejmować żadnych działań w celu wyzwolenia tego działania. Odbywa się to automatycznie i będzie niewidoczny dla użytkownika.

W rzadkich przypadkach, gdy cały region platformy Azure jest niedostępny, żądania Azure Key Vault w tym regionie są automatycznie kierowane (przełączone do trybu*failover*) do regionu pomocniczego. Gdy region podstawowy jest dostępny ponownie, żądania są przekierowywane z powrotem (*powrót po awarii*) do regionu podstawowego. Ponownie nie trzeba podejmować żadnych działań, ponieważ dzieje się to automatycznie.

Dzięki temu projektowi o wysokiej dostępności Azure Key Vault nie wymaga przestojów w przypadku aktywności konserwacyjnej.

Należy pamiętać o kilku zastrzeżeniach:

* W przypadku przejścia w tryb pracy awaryjnej w celu przełączenia usługi do trybu failover może upłynąć kilka minut. Żądania wykonywane w tym czasie mogą się nie powieść, dopóki nie zakończy się praca awaryjna.
* Po zakończeniu pracy w trybie failover Magazyn kluczy jest w trybie tylko do odczytu. Żądania obsługiwane w tym trybie są następujące:
  * Utwórz listę magazynów kluczy
  * Pobierz właściwości magazynów kluczy
   * Wyświetlanie listy certyfikatów
  * Pobierz certyfikaty
  * Wyświetl wpisy tajne
  * Pobierz wpisy tajne
  * Klucze list
  * Pobierz (właściwości z) kluczy
  * Szyfrowanie
  * Odszyfrowywanie
  * Zawijanie
  * Unwrap
  * Weryfikuj
  * Znak
  * Backup
* Po przejściu w tryb failover są dostępne wszystkie typy żądań (łącznie *z* żądaniami odczytu i zapisu).

