---
title: plik dołączania
description: plik dołączania
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90709672"
---
## <a name="create-azure-context"></a>Utwórz kontekst platformy Azure

Aby użyć poleceń platformy Docker do uruchamiania kontenerów w Azure Container Instances, najpierw Zaloguj się do platformy Azure:

```bash
docker login azure
```

Po wyświetleniu monitu wprowadź lub wybierz swoje poświadczenia platformy Azure.


Utwórz kontekst ACI, uruchamiając `docker context create aci` . Ten kontekst kojarzy platformę Docker z subskrypcją platformy Azure i grupą zasobów, dzięki czemu można tworzyć wystąpienia kontenerów i zarządzać nimi. Na przykład, aby utworzyć kontekst o nazwie *myacicontext*:

```
docker context create aci myacicontext
```

Po wyświetleniu monitu wybierz identyfikator subskrypcji platformy Azure, a następnie wybierz istniejącą grupę zasobów lub **Utwórz nową grupę zasobów**. W przypadku wybrania nowej grupy zasobów zostanie ona utworzona z użyciem nazwy wygenerowanej przez system. Wystąpienia kontenerów platformy Azure, tak jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.


Uruchom, `docker context ls` Aby potwierdzić dodanie kontekstu ACI do kontekstów platformy Docker:

```
docker context ls
```