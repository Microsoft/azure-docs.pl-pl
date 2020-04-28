---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 — informacje o wersji | Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla Azure Data Box Gateway i Azure Data Box Edge z uruchomioną wersją 1906.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71099488"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Informacje o wersji Azure Data Box Edge i Azure Data Box Gateway 1906

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących wydania 1906 dla Azure Data Box Edge i Azure Data Box Gateway.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem Data Box Edge/Data Box Gateway należy uważnie zapoznać się z informacjami zawartymi w informacjach o wersji.

Ta wersja jest zgodna z wersjami oprogramowania:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Aktualizację 1906 można zastosować tylko do urządzeń Data Box Edge, na których jest uruchomiona Ogólna dostępność lub 1905 wersja oprogramowania.

## <a name="whats-new"></a>Co nowego

- **Poprawka błędu w przepływie pracy zarządzania kluczami odzyskiwania** — w starszej wersji Wystąpił błąd, z powodu którego nie został zastosowany klucz odzyskiwania. Ta usterka została rozwiązana w tej wersji. Zdecydowanie zalecamy zastosowanie tej aktualizacji, ponieważ klucz odzyskiwania umożliwia odzyskanie danych na urządzeniu w przypadku, gdy urządzenie nie zostanie uruchomione. Aby uzyskać więcej informacji, zobacz jak [zapisać klucz odzyskiwania podczas wdrażania Data Box Edge lub Data Box Gateway](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Udoskonalenia rejestrowania w usłudze Field FPGA (programowalny bramę)** — Rozpoczynanie pracy w wersji 1905, usprawnienia rejestrowania i alertów związanych z FPGA. Jest to aktualizacja wymagana dla Data Box Edge, jeśli używasz funkcji obliczeniowej Edge z FPGA. Aby uzyskać więcej informacji, zobacz jak [przekształcić dane za pomocą obliczeń Edge na Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Znane problemy w wersji GA

W tej wersji nie zaznaczono żadnych nowych problemów. Wszystkie problemy wymienione w wersji zostały przeniesione z poprzednich wersji. Aby wyświetlić listę znanych problemów, przejdź do [znanych problemów w wersji GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Przygotowywanie do wdrażania usługi Azure Data Box Edge](data-box-edge-deploy-prep.md)
