---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/14/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: c142f6a00275b4b21b6bbc64e51a5c4d7d957258
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103468009"
---
- Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej powinny być ograniczone konta magazynu powinny ograniczać dostęp do sieci
- Zmienne konta usługi Automation powinny być zaszyfrowanymi zmiennymi konta usługi Automation.
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
- Tylko bezpieczne połączenia z Redis Cache powinny mieć włączone tylko bezpieczne połączenia z pamięcią podręczną platformy Azure dla Redis powinny być włączone
- Zastępowanie lub wyłączanie profilowania AppArmor profil powinien być ograniczony
- Należy unikać używania kontenerów uprzywilejowanych
- Należy unikać uruchamiania kontenerów jako użytkownik główny
- Bezpieczny transfer do kont magazynu należy włączyć bezpieczny transfer do kont magazynu.
- Service Fabric klastrów powinna mieć ustawioną właściwość ClusterProtectionLevel na EncryptAndSign klastrów Service Fabric powinna mieć Właściwość ClusterProtectionLevel ustawioną na EncryptAndSign
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klienta Service Fabric klastry powinny używać tylko Azure Active Directory do uwierzytelniania klienta
- Usługi powinny nasłuchiwać tylko na dozwolonych portach
- Publiczny dostęp do konta magazynu powinien być niedozwolony
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager kont magazynu należy migrować do nowych zasobów Azure Resource Manager
- Konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej
- Użycie sieci i portów hosta powinno być ograniczone
- Użycie instalacji woluminów HostPath na początku musi być ograniczone do znanej listy, aby ograniczyć dostęp do węzła ze złamanych kontenerów
- Okres ważności certyfikatów przechowywanych w Azure Key Vault nie powinien przekraczać 12 miesięcy
- Maszyny wirtualne powinny być migrowane do nowych zasobów Azure Resource Manager maszyny wirtualne powinny zostać zmigrowane do nowych zasobów Azure Resource Manager
- Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway
- Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure front-drzwi

