---
title: Automatyzowanie wdrożeń portalu deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak automatycznie migrować zawartość portalu dla deweloperów (self-hosted) między API Management deweloperami.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741920"
---
# <a name="automate-developer-portal-deployments"></a>Automatyzowanie wdrożeń portalu deweloperów

Portal API Management dla deweloperów obsługuje programowy dostęp do zawartości. Umożliwia importowanie danych do usługi zarządzania zawartością lub eksportowanie ich z usługi API Management za pośrednictwem [interfejsu API REST zarządzania zawartością.](/rest/api/apimanagement/) Dostęp do interfejsu API REST działa zarówno w przypadku portali zarządzanych, jak i samodzielnie hostowanych.

## <a name="automated-migration-script"></a>Skrypt migracji automatycznej

Za pomocą interfejsu API można zautomatyzować migrację zawartości między dwoma usługami API Management — na przykład usługą w środowisku testowym i usługą w środowisku produkcyjnym. Skrypt `scripts.v3/migrate.js` w repozytorium [GitHub](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) API Management portal deweloperów upraszcza ten proces automatyzacji.

> [!WARNING]
> Skrypt usuwa zawartość portalu deweloperów w lokalizacji docelowej, w API Management usługi. Jeśli cię to niepokoi, upewnij się, że wykonujesz kopię zapasową.

> [!NOTE]
> Jeśli używasz własnego portalu z jawnie zdefiniowanym niestandardowym kontem magazynu do hostowanie plików multimedialnych (tj. definiujesz ustawienie w pliku konfiguracji), musisz użyć oryginalnego `blobStorageUrl` `config.design.json` `scripts/migrate.js` [skryptu](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js). Oryginalny skrypt nie działa w przypadku portali zarządzanych ani samodzielnie hostowanych z kontem magazynu multimediów zarządzanym przez API Management. W takim przypadku zamiast tego użyj skryptu `/scripts.v3` z folderu .

Skrypt wykonuje następujące kroki:

1. Przechwyć zawartość i multimedia portalu z usługi API Management źródłowej.
1. Usuń zawartość i multimedia portalu z usługi API Management docelowej.
1. Przekaż zawartość i multimedia portalu do usługi API Management docelowej.
1. Opcjonalnie i tylko dla portali zarządzanych — automatycznie opublikuj portal.

Po pomyślnym wykonaniu skryptu usługa docelowa usługi API Management powinna zawierać tę samą zawartość portalu co usługa źródłowa i będzie można ją wyświetlić jako administrator.

* Jeśli używasz portalu zarządzanego, możesz ustawić skrypt do automatycznego publikowania portalu docelowego, aby zmigrowana wersja automatycznie dostępna dla gości. 
* Jeśli używasz własnego portalu, musisz opublikować portal docelowy ręcznie. Postępuj zgodnie z instrukcjami publikowania i hostowania w [samouczku, aby skonfigurować własny portal deweloperów.](developer-portal-self-host.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)
- [Samodzielne hostienie portalu dla deweloperów](developer-portal-self-host.md)