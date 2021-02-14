---
title: Ograniczenia dotyczące skalowania za pomocą usługi Azure Arc PostgreSQL
description: Ograniczenia dotyczące skalowania za pomocą usługi Azure Arc PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417808"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Ograniczenia dotyczące skalowania za pomocą usługi Azure Arc PostgreSQL

W tym artykule opisano ograniczenia dotyczące skalowania PostgreSQL w systemie Azure. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

- Przywracanie do punktu w czasie (na przykład Przywracanie do określonej daty i godziny) do tej samej grupy serwerów nie jest obsługiwane. Podczas przywracania do punktu w czasie należy przywrócić w innej grupie serwerów wdrożonej przed przywróceniem. Po przywróceniu do nowej grupy serwerów można usunąć grupę serwerów pochodzenia.
- Przywrócenie całej zawartości kopii zapasowej (w przeciwieństwie do przywracania do określonego punktu w czasie) do tej samej grupy serwerów jest obsługiwane dla PostgreSQL w wersji 12. Nie jest obsługiwana w przypadku PostgreSQL w wersji 11 z powodu ograniczenia aparatu PostgreSQL z osią czasu. Aby przywrócić całą zawartość kopii zapasowej dla grupy serwerów PostgreSQL w wersji 11, należy przywrócić ją do innej grupy serwerów.


## <a name="databases"></a>Bazy danych

Hostowanie więcej niż jednej bazy danych w grupie serwerów nie jest obsługiwane.


## <a name="security"></a>Zabezpieczenia

Zarządzanie użytkownikami i rolami nie jest obsługiwane. Na razie Kontynuuj korzystanie z standardowego użytkownika programu Postgres.

## <a name="roles-and-responsibilities"></a>Role i obowiązki

Role i obowiązki między firmą Microsoft i jej klientami różnią się w zależności od usług Azure PaaS Services (platformy jako usługi) i hybrydowej platformy Azure (np. Azure ARC z włączonym skalowaniem PostgreSQL). 

### <a name="frequently-asked-questions"></a>Często zadawane pytania

W poniższej tabeli zestawiono odpowiedzi na często zadawane pytania dotyczące ról i obowiązków pomocy technicznej.

| Pytanie                      | Platforma Azure jako usługa (PaaS) | Usługi hybrydowe w usłudze Azure Arc |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Kto oferuje infrastrukturę?  | Microsoft                          | Customer                  |
| Kto dostarcza oprogramowanie? *       | Microsoft                          | Microsoft                 |
| Kto wykonuje operacje? | Microsoft                          | Customer                  |
| Czy firma Microsoft udostępnia umowy SLA?      | Tak                                | Nie                        |
| Kto jest odpowiedzialny za umowy SLA? | Microsoft                          | Customer                  |

\* Usługi platformy Azure

__Dlaczego firma Microsoft nie udostępnia umowy SLA w usłudze Azure Arc hybrydowej?__ Ponieważ firma Microsoft nie jest własnością infrastruktury i nie działa. Klienci.

## <a name="next-steps"></a>Następne kroki

- **Wypróbuj go.** Szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), aparacie Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure. 

- **Utwórz własne.** Wykonaj następujące kroki, aby utworzyć własny klaster Kubernetes: 
   1. [Instalowanie narzędzi klienta](install-client-tools.md)
   2. [Utwórz kontroler danych usługi Azure Arc](create-data-controller.md)
   3. [Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Przeczytaj więcej na temat usług danych z włączonym usługą Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Przeczytaj informacje o usłudze Azure Arc](https://aka.ms/azurearc)
