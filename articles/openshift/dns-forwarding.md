---
title: Konfigurowanie przekazywania DNS na platformie Azure Red Hat OpenShift 4
description: Konfigurowanie przekazywania DNS na platformie Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82232636"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Konfigurowanie przekazywania DNS w klastrze Red Hat OpenShift 4 na platformie Azure

Aby skonfigurować przekazywanie DNS w klastrze Red Hat OpenShift systemu Azure, należy zmodyfikować operator DNS. Ta modyfikacja umożliwi aplikacji sieci firmowej działanie w klastrze w celu rozpoznania nazw hostowanych na prywatnym serwerze DNS poza klastrem. Te kroki są udokumentowane dla OpenShift 4,3 w [tym miejscu](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).

Na przykład jeśli chcesz przesłać dalej wszystkie żądania DNS dla *. example.com, które mają zostać rozpoznane przez serwer DNS 192.168.100.10, można edytować konfigurację operatora, uruchamiając polecenie:
 
```bash
oc edit dns.operator/default
```
 
Spowoduje to uruchomienie Edytora i umożliwi zastępowanie `spec: {}` :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Zapisz plik i Zamknij Edytor.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dodatkowymi informacjami na temat przekazywania DNS na OpenShift 4,3 [tutaj](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).