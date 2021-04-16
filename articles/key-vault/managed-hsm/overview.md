---
title: Omówienie zarządzanego modułu HSM platformy Azure — zarządzane przez platformę Azure moduły HSM | Microsoft Docs
description: Zarządzany moduł HSM platformy Azure to usługa w chmurze, która zabezpiecza klucze kryptograficzne dla aplikacji w chmurze.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: b56031d131743a3dc8c97bcd3e85d4653cdd2833
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484187"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Co to jest zarządzany moduł HSM usługi Azure Key Vault?

Azure Key Vault HSM to w pełni zarządzana, wysoce dostępna, zgodna ze standardami usługa w chmurze z jedną dzierżawą, która umożliwia ochronę kluczy kryptograficznych dla aplikacji w chmurze przy użyciu modułów HSM zweryfikowanych w trybie **FIPS 140-2 poziom 3.**  

## <a name="why-use-managed-hsm"></a>Dlaczego warto używać zarządzanego modułu HSM?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>W pełni zarządzany, wysoce dostępny, jednodostępny moduł HSM jako usługa

- **W pełni zarządzane:** aprowizowanie, konfigurowanie, stosowanie poprawek i konserwacja modułu HSM jest obsługiwane przez usługę. 
- **Wysoka dostępność i odporność** na strefy (gdzie obsługiwane są strefy dostępności): każdy klaster HSM składa się z wielu partycji HSM, które obejmują co najmniej dwie strefy dostępności. Jeśli sprzęt ulegnie awarii, partycje członkowskie klastra HSM zostaną automatycznie zmigrowane do węzłów w dobrej kondycji.
- **Pojedyncza dzierżawa:** każde wystąpienie zarządzanego modułu HSM jest przeznaczone dla jednego klienta i składa się z klastra z wieloma partycjami modułu HSM. Każdy klaster HSM używa oddzielnej domeny zabezpieczeń specyficznej dla klienta, która kryptograficznie izoluje klaster HSM każdego klienta.


### <a name="access-control-enhanced-data-protection--compliance"></a>Kontrola dostępu, rozszerzona ochrona & zgodności

- **Scentralizowane zarządzanie kluczami:** zarządzanie krytycznymi kluczami o wysokiej wartości w całej organizacji w jednym miejscu. Dzięki szczegółowym uprawnień do poszczególnych kluczy możesz kontrolować dostęp do każdego klucza zgodnie z zasadą "najmniej uprzywilejowanego dostępu".
- **Izolowana** kontrola dostępu: model kontroli dostępu "lokalnego RBAC" zarządzanego modułu HSM umożliwia wyznaczonym administratorom klastra HSM pełną kontrolę nad modułami HSM, których nie mogą zastąpić nawet administratorzy grupy zarządzania, subskrypcji lub grupy zasobów.
- Moduły HSM zweryfikowane w trybie **FIPS 140-2 poziom 3:** chronią dane i spełniają wymagania zgodności za pomocą modułów HSM zweryfikowanych w trybie FIPS ((Federal Information Protection Standard)) 140-2 poziom 3. Zarządzane moduły HSM używają adapterów HSM LiquidSecurity.
- **Monitorowanie i inspekcja:** w pełni zintegrowane z usługą Azure Monitor. Pobierz pełne dzienniki wszystkich aktywności za pośrednictwem Azure Monitor. Użyj usługi Azure Log Analytics do analizy i alertów.
- **Rezydencja danych:** zarządzany moduł HSM nie przechowuje/nie przetwarza danych klienta poza regionem, w którym klient wdraża wystąpienie modułu HSM.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integracja z platformą Azure i usługami Microsoft PaaS/SaaS 

- Wygeneruj (lub zaimportuj klucze przy użyciu funkcji [BYOK)](hsm-protected-keys-byok.md)i użyj ich do szyfrowania danych magazynowanych w usługach platformy Azure, takich jak [Azure Storage,](../../storage/common/customer-managed-keys-overview.md) [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)i [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Używa tych samych interfejsów API i interfejsów zarządzania co Key Vault

- Łatwe migrowanie istniejących aplikacji, które używają magazynu (wielodostępu) do korzystania z zarządzanych modułów HSM.
- Używaj tych samych wzorców tworzenia i wdrażania aplikacji dla wszystkich aplikacji niezależnie od rozwiązania do zarządzania kluczami w użyciu: magazynów z wieloma dzierżawami lub zarządzanych modułów HSM z jedną dzierżawą

### <a name="import-keys-from-your-on-premise-hsms"></a>Importowanie kluczy z lokalnego modułu HSM

- Generowanie kluczy chronionych przez moduł HSM w lokalnym modułów HSM i bezpieczne importowanie ich do zarządzanego modułu HSM

## <a name="next-steps"></a>Następne kroki
- Zobacz [Szybki start: aprowizować i aktywować zarządzany moduł HSM](quick-create-cli.md) przy użyciu interfejsu wiersza polecenia platformy Azure w celu utworzenia i aktywowania zarządzanego modułu HSM
- Zobacz [Najlepsze rozwiązania dotyczące korzystania z zarządzanego Azure Key Vault HSM](best-practices.md)
