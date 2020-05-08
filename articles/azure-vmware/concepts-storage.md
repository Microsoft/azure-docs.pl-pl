---
title: Pojęcia — magazyn
description: Dowiedz się więcej o możliwościach magazynu kluczy w programie Azure VMware w wersji zapoznawczej chmury prywatne.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7e58a829a40d590b7936a58ccdc866211a4f5cb4
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740358"
---
# <a name="azure-vmware-solution-avs-preview-storage-concepts"></a>Azure VMware Solution (wersja zapoznawcza) — koncepcje dotyczące magazynu

Automatyczna synchronizacja chmur prywatnych zapewnia natywny magazyn w całym klastrze przy użyciu programu VMware sieci vSAN. Wszystkie magazyny lokalne z każdego hosta w klastrze są używane w magazynie danych sieci vSAN, a szyfrowanie za pomocą usługi REST jest dostępne i domyślnie włączone. Możesz użyć zasobów usługi Azure Storage, aby zwiększyć możliwości magazynu chmur prywatnych.

## <a name="vsan-clusters"></a>klastry sieci vSAN

Magazyn lokalny na każdym hoście klastra jest używany jako część magazynu danych sieci vSAN. Wszystkie diskgroups używają warstwy pamięci podręcznej interfejsu NVMe o pojemności 1,6 TB z nieprzetworzonymi, na hostach i dyskach SSD o 15,4 TB. Rozmiar pierwotnej warstwy pojemności klastra to pojemność każdego hosta pomnożona przez liczbę hostów. Na przykład cztery klastry hostów udostępniają 61,6 TB pojemności w warstwie pojemności sieci vSAN.

Magazyn lokalny w hostach klastra jest używany w sieci vSAN magazynu danych w całej klastrze. Wszystkie magazyny danych są tworzone w ramach wdrożenia chmury prywatnej i są dostępne do natychmiastowego użytku. Użytkownik cloudadmin i wszyscy użytkownicy w grupie CloudAdmin mogą zarządzać magazynami danych przy użyciu tych sieci vsanch uprawnień:
- Magazyn danych. AllocateSpace
- Datastore.Browse
- Magazyn danych. config
- Magazyn danych. DeleteFile
- Magazyn danych. usługi filemanagement
- Magazyn danych. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Szyfrowanie danych w spoczynku

magazyny danych sieci vSAN korzystają domyślnie z szyfrowania Data-in-Rest. Rozwiązanie do szyfrowania jest oparte na usłudze KMS i obsługuje operacje programu vCenter do zarządzania kluczami. Klucze są przechowywane w postaci zaszyfrowanej, opakowane przez oparty na module HSM klucz główny Azure Key Vault. Gdy host zostanie usunięty z dowolnego powodu, dane na dysków SSD są od razu unieważnione.

## <a name="scaling"></a>Skalowanie

Pojemność magazynu natywnego klastra jest skalowana przez Dodawanie hostów do klastra. W przypadku klastrów, które korzystają z hostów, pierwotna pojemność całego klastra jest zwiększana o 15,4 TB z każdym dodatkowym hostem. W klastrach zbudowanych przy użyciu hostów zasad grupy ich pierwotna pojemność wzrosła o 7,7 TB z każdym dodatkowym hostem. W obu typach klastrów hosty trwają około 10 minut do dodania do klastra. Aby uzyskać instrukcje dotyczące skalowania klastrów, zobacz [Samouczek dotyczący chmury prywatnej.

## <a name="azure-storage-integration"></a>Integracja z usługą Azure Storage

Usług Azure Storage można używać w przypadku obciążeń działających w chmurze prywatnej. Usługi Azure Storage obejmują konta magazynu, Table Storage i Blob Storage. Połączenie obciążeń z usługami Azure Storage nie przechodzi przez Internet. Ta łączność zapewnia dodatkowe zabezpieczenia i umożliwia korzystanie z usług Azure Storage opartych na umowie SLA w obciążeniach chmury prywatnej.

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z [pojęciami dotyczącymi tożsamości w chmurze prywatnej][concepts-identity].

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorials-scale-private-cloud]: ./tutorials-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md