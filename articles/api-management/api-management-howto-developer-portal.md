---
title: Omówienie portalu dla deweloperów w usłudze Azure API Management
titleSuffix: Azure API Management
description: Dowiedz się więcej o portalu dla deweloperów w API Management — dostosowywalna witryna internetowa, w której użytkownicy interfejsu API mogą eksplorować Twoje interfejsy API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f5da5ed6c828278eb25a9fa4fa5cfb90e16f8f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815963"
---
# <a name="overview-of-the-developer-portal"></a>Omówienie portalu dla deweloperów

Portal deweloperów to automatycznie wygenerowana, w pełni dostosowywalna witryna internetowa z dokumentacją interfejsów API. Jest to miejsce, w którym użytkownicy interfejsu API mogą odnajdywać Twoje interfejsy API, uczyć się ich używać, żądać dostępu i je wypróbować.

Zgodnie z wprowadzeniem w tym artykule można dostosować i rozszerzyć portal dla deweloperów dla konkretnych scenariuszy. 

![API Management portal dla deweloperów](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migracja ze starszego portalu

> [!IMPORTANT]
> Portal dla deweloperów w starszej wersji jest teraz przestarzały i będzie otrzymywać tylko aktualizacje zabezpieczeń. Możesz nadal z niego korzystać, jak zwykle, do momentu wycofania jej w październiku 2023 r., gdy zostanie ona usunięta ze wszystkich API Management usług.

Migrację do nowego portalu dla deweloperów opisano w [dedykowanym artykule dokumentacji](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Dostosowywanie i style

Portal dla deweloperów można dostosować i zmienić jego styl za pomocą wbudowanego edytora wizualizacji typu "przeciągnij i upuść". Zobacz [ten samouczek,](api-management-howto-developer-portal-customize.md) aby uzyskać więcej szczegółów.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Rozszerzalność

Usługa API Management obejmuje wbudowany, zawsze aktualny, **zarządzany** portal deweloperów. Dostęp do niego można uzyskać z Azure Portal interfejsu.

Jeśli musisz rozszerzyć go za pomocą niestandardowej logiki, która nie jest obsługiwana od czasu do czasu, możesz zmodyfikować jej bazę kodu. Baza kodu portalu jest dostępna [w repozytorium GitHub.](https://github.com/Azure/api-management-developer-portal) Możesz na przykład zaimplementować nowy widżet, który integruje się z systemem pomocy technicznej innej firmy. Podczas implementowania nowej funkcji można wybrać jedną z następujących opcji:

- **Samodzielnie hostuj** wynikowy portal poza twoją API Management usługi. Gdy samodzielnie hostujesz portal, stajesz się jego konserwatorem i ponosisz odpowiedzialność za jego uaktualnienia. pomoc techniczna platformy Azure jest ograniczona tylko do podstawowej konfiguracji portali [hostowanych samodzielnie.](developer-portal-self-host.md)
- Otwórz żądanie ściągnięć dla zespołu API Management, aby scalić nowe funkcje z **bazą** kodu portalu zarządzanego.

Aby uzyskać szczegółowe informacje i instrukcje dotyczące rozszerzalności, zapoznaj się z [repozytorium GitHub](https://github.com/Azure/api-management-developer-portal) i samouczkiem dotyczącym [implementacji widżetu](developer-portal-implement-widgets.md). Samouczek [dotyczący dostosowywania portalu zarządzanego](api-management-howto-developer-portal-customize.md) prowadzi użytkownika przez panel administracyjny  portalu, który jest wspólny dla wersji zarządzanych **i hostowanych samodzielnie.**


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o nowym portalu dla deweloperów:

- [Uzyskiwanie dostępu do portalu dla deweloperów zarządzanych i dostosowywanie go](api-management-howto-developer-portal-customize.md)
- [Konfigurowanie własnej wersji portalu](developer-portal-self-host.md)
- [Implementowanie własnego widżetu](developer-portal-implement-widgets.md)

Przeglądaj inne zasoby:

- [Repozytorium GitHub z kodem źródłowym](https://github.com/Azure/api-management-developer-portal)
- [Często zadawane pytania dotyczące portalu dla deweloperów](developer-portal-faq.md)
