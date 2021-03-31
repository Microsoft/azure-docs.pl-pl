---
title: Zarządzanie modułem HSM zarządzanym przez platformę Azure — zarządzany moduł HSM Azure | Microsoft Docs
description: Moduł HSM zarządzany przez platformę Azure to usługa w chmurze, która chroni klucze kryptograficzne dla aplikacji w chmurze.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 95feeebf009cae22cf3952df80596c12e75cbe4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357576"
---
# <a name="what-is-azure-key-vault-managed-hsm-preview"></a>Co to jest Azure Key Vault zarządzanym modułem HSM (wersja zapoznawcza)?

Azure Key Vault zarządzanym modułem HSM jest w pełni zarządzana usługa w chmurze o wysokiej dostępności, która jest zgodna ze standardami, która pozwala chronić klucze kryptograficzne dla aplikacji w chmurze przy użyciu zweryfikowanych sprzętowych modułów zabezpieczeń **poziomu 3 w trybie FIPS 140-2** .  

## <a name="why-use-managed-hsm"></a>Dlaczego warto używać zarządzanego modułu HSM?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>W pełni zarządzane, wysoce dostępne i jednodostępne moduły HSM jako usługa

- W **pełni zarządzane**: Inicjowanie obsługi, konfiguracja, stosowanie poprawek i konserwacja modułu HSM jest obsługiwane przez usługę. 
- **Wysoce dostępna i odporna na strefy (w** których są obsługiwane strefy dostępności): każdy klaster HSM składa się z wielu partycji modułu HSM, które obejmują co najmniej dwie strefy dostępności. Jeśli sprzęt ulegnie awarii, partycje elementów członkowskich dla klastra HSM zostaną automatycznie zmigrowane do węzłów w dobrej kondycji.
- **Pojedyncze dzierżawca**: każde zarządzane wystąpienie modułu HSM jest przeznaczone dla jednego klienta i składa się z klastra wielu partycji modułu HSM. Każdy klaster HSM używa oddzielnej domeny zabezpieczeń specyficznej dla klienta, która kryptograficznie izoluje klaster modułu HSM każdego klienta.


### <a name="access-control-enhanced-data-protection--compliance"></a>Kontrola dostępu, Ulepszona ochrona danych &

- **Scentralizowane zarządzanie kluczami**: Zarządzaj kluczami o krytycznym znaczeniu w organizacji w jednym miejscu. Z szczegółowymi uprawnieniami na klucz, kontrolują dostęp do każdego klucza w zasadzie "najmniej uprzywilejowany dostęp".
- **Izolowana kontrola dostępu**: zarządzany model modułu HSM "Local RBAC" modelu kontroli dostępu umożliwia Wyznaczeni administratorom klastrów modułu HSM pełną kontrolę nad sprzętowych modułów zabezpieczeńem, że nawet Administratorzy grupy zarządzania, subskrypcji lub grupy zasobów nie mogą przesłonić.
- **Fips 140-2 Level 3 sprawdzony sprzętowych modułów zabezpieczeń**: Chroń dane i spełniaj wymagania dotyczące zgodności z FIPS ((Federal Information Protection standard)) 140-2 zweryfikowane sprzętowych modułów zabezpieczeń poziomu 3. Zarządzane sprzętowych modułów zabezpieczeń używają kart HSM LiquidSecurity firmy Marvell.
- **Monitorowanie i inspekcja**: w pełni zintegrowana z usługą Azure monitor. Pobierz pełne dzienniki wszystkich działań za pośrednictwem Azure Monitor. Użyj usługi Azure Log Analytics na potrzeby analiz i alertów.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integracja z platformą Azure i usługami Microsoft PaaS/SaaS 

- Generuj (lub Importuj przy użyciu [BYOK](hsm-protected-keys-byok.md)) klucze i używaj ich do szyfrowania danych przechowywanych w usługach platformy Azure, takich jak [Azure Storage](../../storage/common/customer-managed-keys-overview.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)i [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Używa tych samych interfejsów API i zarządzania co Key Vault

- Z łatwością Migruj istniejące aplikacje, które używają magazynu (z wieloma dzierżawcami) do korzystania z usługi Managed sprzętowych modułów zabezpieczeń.
- Używaj tych samych wzorców tworzenia aplikacji i wdrażania dla wszystkich aplikacji niezależnie od używanego rozwiązania do zarządzania kluczami: magazyny z wieloma dzierżawcami lub sprzętowych modułów zabezpieczeń zarządzane z jedną dzierżawą

### <a name="import-keys-from-your-on-premise-hsms"></a>Importuj klucze z lokalnego sprzętowych modułów zabezpieczeń

- Generuj klucze chronione przez moduł HSM w lokalnym module HSM i zaimportuj je bezpiecznie do zarządzanego modułu HSM

## <a name="next-steps"></a>Następne kroki
- Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu utworzenia i aktywowania zarządzanego modułu HSM
- Zapoznaj się [z najlepszymi rozwiązaniami przy użyciu Azure Key Vault zarządzanego modułu HSM](best-practices.md)
