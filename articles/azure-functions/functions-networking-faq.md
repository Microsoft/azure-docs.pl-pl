---
title: Często zadawane pytania dotyczące sieci w Azure Functions
description: Odpowiedzi na niektóre często zadawane pytania i scenariusze dotyczące sieci z Azure Functions.
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 24afeeee3207127bb9404156dc390433671dd5da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592306"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Często zadawane pytania dotyczące sieci w Azure Functions

Ten artykuł zawiera listę często zadawanych pytań dotyczących sieci w Azure Functions. Aby zapoznać się z bardziej szczegółowym omówieniem, zobacz [Functions Options](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Jak mogę ustawić statyczny adres IP w funkcjach?

Wdrożenie funkcji w App Service Environment jest podstawowym sposobem na statyczne przychodzące i wychodzące adresy IP dla funkcji. Aby uzyskać szczegółowe informacje na temat używania App Service Environment, Zacznij od artykułu [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia z App Service Environment](../app-service/environment/create-ilb-ase.md).

Można również użyć bramy translatora adresów sieciowych sieci wirtualnej do kierowania ruchu wychodzącego za pomocą kontrolowanego publicznego adresu IP. Aby dowiedzieć się więcej, zobacz [Samouczek: kontrola Azure Functions wychodzący adres IP z bramą NAT sieci wirtualnej platformy Azure](functions-how-to-use-nat-gateway.md). 

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak mogę ograniczyć dostęp do Internetu do funkcji My?

Dostęp do Internetu można ograniczyć na kilka sposobów:

* [Ograniczenia adresów IP](../app-service/app-service-ip-restrictions.md): ograniczanie ruchu przychodzącego do aplikacji funkcji według zakresu adresów IP.
    * W obszarze Ograniczenia adresów IP można również skonfigurować [punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md), które ograniczają funkcję do akceptowania ruchu przychodzącego z określonej sieci wirtualnej.
* Usuwanie wszystkich wyzwalaczy HTTP. W przypadku niektórych aplikacji wystarczy uniknąć wyzwalacza protokołu HTTP i użyć innych źródeł zdarzeń do wyzwolenia funkcji.

Należy pamiętać, że Edytor Azure Portal wymaga bezpośredniego dostępu do uruchomionej funkcji. Wszelkie zmiany kodu za pośrednictwem Azure Portal będą wymagały urządzenia, którego używasz do przeglądania portalu w celu dodania adresu IP do zatwierdzonej listy. Można jednak nadal korzystać ze wszystkich elementów na karcie funkcje platformy z ograniczeniami sieci.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Jak mogę ograniczyć moją aplikację funkcji do sieci wirtualnej?

Można ograniczyć ruch **przychodzący** dla aplikacji funkcji do sieci wirtualnej za pomocą [punktów końcowych usługi](./functions-networking-options.md#use-service-endpoints). Ta konfiguracja nadal umożliwia aplikacji funkcji wykonywanie wywołań wychodzących do Internetu.

Aby całkowicie ograniczyć funkcję, która powoduje, że cały ruch przesyłany przez sieć wirtualną, można użyć [prywatnych punktów końcowych](./functions-networking-options.md#private-endpoint-connections) z integracją sieci wirtualnej lub App Service Environment. Aby dowiedzieć się więcej, zobacz [integrowanie Azure Functions z siecią wirtualną platformy Azure za pomocą prywatnych punktów końcowych](functions-create-vnet.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak mogę uzyskać dostęp do zasobów w sieci wirtualnej z aplikacji funkcji?

Możesz uzyskać dostęp do zasobów w sieci wirtualnej z działającej funkcji przy użyciu integracji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [integracja z siecią wirtualną](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak mogę zasobów dostępu chronionych przez punkty końcowe usługi?

Korzystając z integracji z siecią wirtualną, można uzyskać dostęp do zasobów zabezpieczonych za pomocą punktów końcowych z działającej funkcji. Aby uzyskać więcej informacji, zobacz [integracja z siecią wirtualną](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak wyzwolić funkcję z zasobu w sieci wirtualnej?

Można zezwolić na wywoływanie wyzwalaczy protokołu HTTP z sieci wirtualnej za pomocą [punktów końcowych usługi](./functions-networking-options.md#use-service-endpoints) lub [połączeń prywatnych punktów końcowych](./functions-networking-options.md#private-endpoint-connections). 

Możesz również wyzwolić funkcję ze wszystkich innych zasobów w sieci wirtualnej, wdrażając aplikację funkcji do planu Premium, planu App Service lub App Service Environment. Aby uzyskać więcej informacji, zobacz [wyzwalacze sieci wirtualnej inne niż http](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Jak mogę wdrożyć aplikację funkcji w sieci wirtualnej?

Wdrożenie do App Service Environment jest jedynym sposobem tworzenia aplikacji funkcji, która znajduje się w całości w sieci wirtualnej. Aby uzyskać szczegółowe informacje na temat używania wewnętrznego modułu równoważenia obciążenia z App Service Environment, Zacznij od artykułu [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia z App Service Environment](../app-service/environment/create-ilb-ase.md).

W przypadku scenariuszy, w których potrzebujesz tylko jednokierunkowego dostępu do zasobów sieci wirtualnej lub mniej kompleksowej izolacji sieci, zobacz [Omówienie usługi Functions](functions-networking-options.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sieci i funkcji: 

* [Postępuj zgodnie z samouczkiem dotyczącym rozpoczynania pracy z integracją z siecią wirtualną](./functions-create-vnet.md)
* [Dowiedz się więcej o opcjach sieciowych w Azure Functions](./functions-networking-options.md)
* [Dowiedz się więcej o integracji z siecią wirtualną za pomocą App Service i funkcji](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej o sieciach wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączanie większej liczby funkcji sieciowych i kontroli w środowiskach App Service](../app-service/environment/intro.md)
