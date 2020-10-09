---
title: Azure Key Vault dostępności i nadmiarowości — Azure Key Vault | Microsoft Docs
description: Dowiedz się więcej o dostępności Azure Key Vault i nadmiarowości.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 3cc4bdc0fabd9d1e209634a88bed1bf063db917c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597881"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostępność i nadmiarowość w usłudze Azure Key Vault

Azure Key Vault funkcje wielu warstw nadmiarowości, aby upewnić się, że klucze i wpisy tajne pozostaną dostępne dla aplikacji, nawet jeśli poszczególne składniki usługi nie powiodą się.

> [!NOTE]
> Ten przewodnik ma zastosowanie do magazynów. Zarządzane pule modułów HSM używają różnych modeli wysokiej dostępności i odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący odzyskiwania po awarii zarządzanego modułu HSM](../managed-hsm/disaster-recovery-guide.md) .

Zawartość Twojego magazynu kluczy jest replikowana w regionie i do regionu pomocniczego, co najmniej 150 kilometrów, ale w tej samej lokalizacji geograficznej, aby zachować wysoką trwałość kluczy i wpisów tajnych. Aby uzyskać szczegółowe informacje na temat konkretnych par regionów, zobacz dokument dotyczący [sparowanych regionów platformy Azure](../../best-practices-availability-paired-regions.md) .

Jeśli poszczególne składniki w ramach usługi magazynu kluczy zakończą się niepowodzeniem, alternatywne składniki w ramach regionu w celu zapewnienia obsługi żądania, aby upewnić się, że nie ma obniżenia funkcjonalności. Nie musisz podejmować żadnych działań w celu uruchomienia tego procesu, dzieje się on automatycznie i będzie on widoczny dla Ciebie.

W rzadkich przypadkach, gdy cały region platformy Azure jest niedostępny, żądania Azure Key Vault w tym regionie są automatycznie kierowane (przełączone do trybu*failover*) do regionu pomocniczego. Gdy region podstawowy jest dostępny ponownie, żądania są przekierowywane z powrotem (*powrót po awarii*) do regionu podstawowego. Ponownie nie trzeba podejmować żadnych działań, ponieważ dzieje się to automatycznie.

Dzięki temu projektowi o wysokiej dostępności Azure Key Vault nie wymaga przestojów w przypadku aktywności konserwacyjnej.

Należy pamiętać o kilku zastrzeżeniach:

* W przypadku przejścia w tryb pracy awaryjnej w celu przełączenia usługi do trybu failover może upłynąć kilka minut. Żądania wykonywane w tym czasie przed przejściem do trybu failover mogą zakończyć się niepowodzeniem.
* Jeśli używasz prywatnego linku do nawiązywania połączenia z magazynem kluczy, ponowne nawiązanie połączenia w przypadku przejścia w tryb failover może potrwać do 20 minut. 
* Podczas pracy w trybie failover Magazyn kluczy jest w trybie tylko do odczytu. Żądania obsługiwane w tym trybie są następujące:
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
