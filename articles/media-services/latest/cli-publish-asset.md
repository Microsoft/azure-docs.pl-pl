---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — publikowanie elementu zawartości
description: W tym artykule przedstawiono sposób użycia skryptu interfejsu wiersza polecenia platformy Azure do opublikowania elementu zawartości.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: c283c39f1ea90275c42de1481a9cb9006f2b2c5f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897092"
---
# <a name="cli-example-publish-an-asset"></a>Przykład interfejsu wiersza polecenia: publikowanie elementu zawartości

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skrypt interfejsu wiersza polecenia platformy Azure w tym artykule pokazuje sposób tworzenia lokalizatora przesyłania strumieniowego i uzyskiwania z powrotem adresów URL przesyłania strumieniowego. 

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto Media Services](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Następne kroki

[Przegląd Media Services](media-services-overview.md)
