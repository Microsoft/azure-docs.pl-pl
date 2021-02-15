---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 02/09/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: b5d33a962078ab7bd49c58fe16f271cdd1c8eea4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379198"
---
- Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej należy ograniczyć
- Zmienne konta usługi Automation powinny być szyfrowane
- Pamięć podręczna platformy Azure dla Redis powinna znajdować się w sieci wirtualnej
- Konta Azure Cosmos DB powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych
- Obszary robocze Azure Machine Learning powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)
- Chmura ze sprężyną na platformie Azure powinna używać iniekcji sieci
- Konta Cognitive Services powinny włączać szyfrowanie danych za pomocą klucza zarządzanego przez klienta (CMK)
- Należy wymusić limity procesora CPU i pamięci kontenera
- Obrazy kontenerów powinny być wdrażane tylko z zaufanych rejestrów
- Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)
- Należy unikać kontenera z eskalacją uprawnień
- Należy unikać obsługi kontenerów chronionych przestrzeni nazw hosta
- Kontenery powinny nasłuchiwać tylko na dozwolonych portach
- Niezmienny (tylko do odczytu) główny system plików powinien zostać wymuszony dla kontenerów
- Klucze Key Vault powinny mieć datę wygaśnięcia
- Wpisy tajne Key Vault powinny mieć datę wygaśnięcia
- W magazynach kluczy powinna być włączona ochrona przed czyszczeniem
- Magazyny kluczy powinny mieć włączone usuwanie nietrwałe
- W przypadku kontenerów powinny być wymuszane najmniej uprzywilejowane funkcje systemu Linux.
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Zastępowanie lub wyłączanie profilowania AppArmor profil powinien być ograniczony
- Należy unikać używania kontenerów uprzywilejowanych
- Należy unikać uruchamiania kontenerów jako użytkownik główny
- Należy włączyć bezpieczny transfer na konta magazynu
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów
- Usługi powinny nasłuchiwać tylko na dozwolonych portach
- Publiczny dostęp do konta magazynu powinien być niedozwolony
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej
- Użycie sieci i portów hosta powinno być ograniczone
- Użycie instalacji woluminów HostPath na początku musi być ograniczone do znanej listy, aby ograniczyć dostęp do węzła ze złamanych kontenerów
- Okres ważności certyfikatów przechowywanych w Azure Key Vault nie powinien przekraczać 12 miesięcy
- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager
- Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway
- Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure front-drzwi

