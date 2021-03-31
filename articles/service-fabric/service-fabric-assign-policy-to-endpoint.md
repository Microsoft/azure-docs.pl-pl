---
title: Przypisywanie zasad dostępu do punktów końcowych usługi
description: Dowiedz się, jak przypisywać zasady dostępu zabezpieczeń do punktów końcowych HTTP lub HTTPS w usłudze Service Fabric.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614659"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS
W przypadku zastosowania zasad Uruchom jako i manifestu usługi deklaruje zasoby punktu końcowego protokołu HTTP, należy określić **SecurityAccessPolicy**.  **SecurityAccessPolicy** gwarantuje, że porty przyłączone do tych punktów końcowych są poprawnie ograniczone do konta użytkownika, na którym działa usługa. W przeciwnym razie **http.sys** nie ma dostępu do usługi i wystąpią błędy wywołań z klienta. Poniższy przykład dotyczy konta Customer1 w punkcie końcowym o nazwie **EndpointName**, który daje pełne prawa dostępu.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

W przypadku punktu końcowego HTTPS wskaż również nazwę certyfikatu do zwrócenia klientowi. Odwoływanie się do certyfikatu za pomocą **EndpointBindingPolicy**.  Certyfikat jest zdefiniowany w sekcji **Certyfikaty** manifestu aplikacji.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> W przypadku korzystania z protokołu HTTPS nie należy używać tego samego portu i certyfikatu dla różnych wystąpień usługi (niezależnie od aplikacji) wdrożonych w tym samym węźle. Uaktualnienie dwóch różnych usług przy użyciu tego samego portu w różnych wystąpieniach aplikacji spowoduje niepowodzenie uaktualnienia. Aby uzyskać więcej informacji, zobacz [Uaktualnianie wielu aplikacji za pomocą punktów końcowych https ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Aby zapoznać się z kolejnymi krokami, przeczytaj następujące artykuły:
* [Omówienie modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
