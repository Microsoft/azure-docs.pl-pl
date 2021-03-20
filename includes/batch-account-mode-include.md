---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 51b687dc2a6264eb12362616429746c9b182c3b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68323883"
---
> [!NOTE]
> Podczas tworzenia konta usługi Batch możesz wybrać jeden z dwóch typów *alokacji puli*: **subskrypcję użytkownika** lub **usługę Batch**. W większości przypadków należy użyć domyślnego trybu usługi Batch, który polega na alokowaniu pul w tle w subskrypcjach zarządzanych przez platformę Azure. W alternatywnym trybie subskrypcji użytkownika maszyny wirtualne i inne zasoby usługi Batch są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Tryb subskrypcji użytkownika jest wymagany przy tworzeniu pul usługi Batch przy użyciu usługi [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Aby utworzyć konto usługi Batch w trybie subskrypcji użytkownika, musisz również zarejestrować subskrypcję w usłudze Azure Batch i powiązać konto z usługą Azure Key Vault.