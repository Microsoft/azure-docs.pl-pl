---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017689"
---
Zawsze używaj [grup zabezpieczeń usługi Azure AD](../articles/active-directory/fundamentals/active-directory-manage-groups.md) jako przypisanego podmiotu zabezpieczeń w pozycji listy ACL. Odporne na możliwość bezpośredniego przypisywania poszczególnych użytkowników lub podmiotów usługi. Użycie tej struktury umożliwi Dodawanie i usuwanie użytkowników lub jednostek usług bez konieczności ponownego stosowania list kontroli dostępu do całej struktury katalogów. Zamiast tego można po prostu dodać lub usunąć użytkowników i jednostki usługi z odpowiedniej grupy zabezpieczeń usługi Azure AD. 

Istnieje wiele różnych sposobów konfigurowania grup. Załóżmy na przykład, że masz katalog o nazwie **/LogData** , który przechowuje dane dziennika, które są generowane przez serwer. Azure Data Factory (ADF) pobiera dane do tego folderu. Określeni użytkownicy z zespołu ds. obsługi technicznej będą przekazywać dzienniki i zarządzać innymi użytkownikami tego folderu, a różne klastry datakostki będą analizować dzienniki z tego folderu. 

Aby włączyć te działania, można utworzyć `LogsWriter` grupę i `LogsReader` grupę. Następnie można przypisać uprawnienia w następujący sposób:

- Dodaj `LogsWriter` grupę do listy ACL katalogu **/LogData** z `rwx` uprawnieniami.
- Dodaj `LogsReader` grupę do listy ACL katalogu **/LogData** z `r-x` uprawnieniami.
- Dodaj do grupy obiekt główny usługi lub tożsamość usługi zarządzanej (MSI) `LogsWriters` .
- Dodaj użytkowników z zespołu inżynierów usługi do `LogsWriter` grupy.
- Dodaj do grupy obiekt główny usługi lub plik MSI dla kostek `LogsReader` .

Jeśli użytkownik w zespole inżynierów usług opuści firmę, można po prostu usunąć ją z `LogsWriter` grupy. Jeśli ten użytkownik nie został dodany do grupy, ale zamiast tego został dodany dedykowany wpis listy kontroli dostępu dla tego użytkownika, należy usunąć ten wpis listy ACL z katalogu **/LogData** . Należy również usunąć wpis ze wszystkich podkatalogów i plików w całej hierarchii katalogów katalogu **/LogData** . 

Aby utworzyć grupę i dodać członków, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md).