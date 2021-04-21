---
title: Azure Key Vault dostępności i nadmiarowości — Azure Key Vault | Microsoft Docs
description: Dowiedz się więcej Azure Key Vault o dostępności i nadmiarowości.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 3c5afc92044fcb109bedd38298b0b027ebeb437d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749694"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostępność i nadmiarowość w usłudze Azure Key Vault

Azure Key Vault wiele warstw nadmiarowości, aby mieć pewność, że klucze i wpisy tajne pozostaną dostępne dla aplikacji nawet w przypadku awarii poszczególnych składników usługi.

> [!NOTE]
> Ten przewodnik dotyczy magazynów. Zarządzane pule modułów HSM używają innego modelu wysokiej dostępności i odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [Przewodnik odzyskiwania po awarii zarządzanego modułu HSM.](../managed-hsm/disaster-recovery-guide.md)

Zawartość magazynu kluczy jest replikowana w obrębie regionu i do regionu pomocniczego w odległości co najmniej 150 kilometrów, ale w tej samej lokalizacji geograficznej w celu zachowania wysokiej trwałości kluczy i wpisów tajnych. Aby uzyskać szczegółowe informacje o określonych parach regionów, zobacz [Sparowane regiony platformy Azure](../../best-practices-availability-paired-regions.md). Wyjątkiem od sparowanego modelu regionów jest Brazylia Południowa, która umożliwia tylko utrzymanie danych na terenie Brazylii Południowej. Brazylia Południowa używa magazynu strefowo nadmiarowego (ZRS) do trzykrotnego replikowania danych w obrębie jednej lokalizacji/regionu. W przypadku usługi AKV Premium do replikowania danych z modułów HSM są używane tylko 2 z 3 regionów.  

W przypadku awarii poszczególnych składników w usłudze Key Vault alternatywne składniki w kroku regionu w celu obsługi żądania w celu upewnienia się, że nie ma pogorszenia funkcjonalności. Aby rozpocząć ten proces, nie musisz nic tworzyć. Jest on automatycznie i będzie dla Ciebie niewidoczny.

W rzadkich przypadkach, gdy cały region świadczenia usługi Azure jest niedostępny, żądania kierowane do usługi Azure Key Vault w tym regionie są automatycznie kierowane (w *trybie failed over)* do regionu pomocniczego z wyjątkiem regionu Brazylia Południowa. Gdy region podstawowy jest ponownie dostępny, żądania są kierowane z powrotem (powrót *po awarii)* do regionu podstawowego. Ponownie nie musisz podjąć żadnych działań, ponieważ dzieje się to automatycznie.

W regionie Brazylia Południowa należy zaplanować odzyskiwanie magazynów kluczy platformy Azure w scenariuszu awarii regionu. Aby utworzyć kopię zapasową i przywrócić magazyn kluczy platformy Azure do wybranego regionu, wykonaj kroki opisane w te Azure Key Vault [kopii zapasowej](backup.md). 

Dzięki temu projektowi o wysokiej dostępności Azure Key Vault nie wymaga przestojów w działaniach konserwacyjnych.

Należy pamiętać o kilku zastrzeżeniach:

* W przypadku trybu failover w regionie może mi potrwać kilka minut, aby usługa przesiewała do trybu failover. Żądania, które są dokonywane w tym czasie przed rozpoczęciem pracy w trybu failover, mogą się nie powieść.
* Jeśli używasz linku prywatnego do nawiązywania połączenia z magazynem kluczy, ponowne połączenie może zostać nawiązane ponownie w przypadku trybu failover po 20 minutach. 
* Podczas pracy w trybie failover magazyn kluczy jest w trybie tylko do odczytu. Żądania obsługiwane w tym trybie to:
  * Lista certyfikatów
  * Uzyskiwanie certyfikatów
  * Wyświetlanie listy wpisów tajnych
  * Uzyskiwanie wpisów tajnych
  * Lista kluczy
  * Pobierz (właściwości) kluczy
  * Szyfrowanie
  * Odszyfrowywanie
  * Zawijanie
  * Unwrap
  * Weryfikacja
  * Znak
  * Backup

* Podczas trybu failover nie będzie można wprowadzać zmian właściwości magazynu kluczy. Nie będzie można zmienić ustawień i konfiguracji zasad dostępu ani zapory.

* Po powrocie po awarii wszystkie typy żądań  (w tym żądania odczytu i zapisu) są dostępne.
