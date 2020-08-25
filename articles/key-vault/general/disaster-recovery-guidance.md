---
title: Co zrobić, jeśli przerwanie działania usługi platformy Azure ma wpływ na Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Dowiedz się, co należy zrobić, aby obsłużyć Azure Key Vault przerwy w działaniu usługi platformy Azure.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: sudbalas
ms.openlocfilehash: e4364c3c5bcba1a04837f3f9e7a0576579211c72
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796599"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostępność i nadmiarowość w usłudze Azure Key Vault

Azure Key Vault funkcje wielu warstw nadmiarowości, aby upewnić się, że klucze i wpisy tajne pozostaną dostępne dla aplikacji, nawet jeśli poszczególne składniki usługi nie powiodą się.

Zawartość Twojego magazynu kluczy jest replikowana w regionie i do regionu pomocniczego, co najmniej 150 kilometrów, ale w tej samej lokalizacji geograficznej, aby zachować wysoką trwałość kluczy i wpisów tajnych. Aby uzyskać szczegółowe informacje na temat konkretnych par regionów, zobacz dokument dotyczący [sparowanych regionów platformy Azure](../../best-practices-availability-paired-regions.md) .

Jeśli poszczególne składniki w ramach usługi magazynu kluczy zakończą się niepowodzeniem, alternatywne składniki w ramach regionu w celu zapewnienia obsługi żądania, aby upewnić się, że nie ma obniżenia funkcjonalności. Nie trzeba podejmować żadnych działań w celu uruchomienia tego procesu, odbywa się on automatycznie i będzie on widoczny dla Ciebie.

W rzadkich przypadkach, gdy cały region platformy Azure jest niedostępny, żądania Azure Key Vault w tym regionie są automatycznie kierowane (przełączone do trybu*failover*) do regionu pomocniczego. Gdy region podstawowy jest dostępny ponownie, żądania są przekierowywane z powrotem (*powrót po awarii*) do regionu podstawowego. Ponownie nie trzeba podejmować żadnych działań, ponieważ dzieje się to automatycznie.

Dzięki temu projektowi o wysokiej dostępności Azure Key Vault nie wymaga przestojów w przypadku aktywności konserwacyjnej.

Należy pamiętać o kilku zastrzeżeniach:

* W przypadku przejścia w tryb pracy awaryjnej w celu przełączenia usługi do trybu failover może upłynąć kilka minut. Żądania wykonywane w tym czasie przed przejściem do trybu failover mogą zakończyć się niepowodzeniem.
* Podczas pracy w trybie failover Magazyn kluczy jest w trybie tylko do odczytu. Żądania obsługiwane w tym trybie są następujące:
  * Utwórz listę magazynów kluczy
  * Pobierz właściwości magazynów kluczy
  * Wyświetlanie listy certyfikatów
  * Pobierz certyfikaty
  * Wyświetlanie listy wpisów tajnych
  * Pobierz wpisy tajne
  * Klucze list
  * Pobierz (właściwości z) kluczy
  * Szyfrowanie
  * Odszyfrowywanie
  * Zawijanie
  * Unwrap
  * Weryfikacja
  * Znak
  * Backup
* Podczas pracy w trybie failover nie będzie można wprowadzać zmian w właściwościach magazynu kluczy. Nie będzie można zmieniać zasad dostępu ani konfiguracji i ustawień zapory.
* Po przejściu w tryb failover są dostępne wszystkie typy żądań (łącznie *z* żądaniami odczytu i zapisu).

