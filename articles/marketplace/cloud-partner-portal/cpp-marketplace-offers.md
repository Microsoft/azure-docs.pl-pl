---
title: Oferty dotyczące platformy Azure i usługi AppSource Marketplace
description: Tworzenie ofert platformy Azure i usługi AppSource Marketplace oraz zarządzanie nimi
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278488"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Oferty dotyczące platformy Azure i usługi AppSource Marketplace

W pierwszej części tej sekcji wprowadzono ogólne operacje umożliwiające tworzenie ofert na platformie Azure i AppSource Marketplace oraz zarządzanie nimi.  Ta część zawiera tło, które należy zrozumieć, aby zarządzać określonymi typami ofert, a także informacjami technicznymi, które są wspólne dla wszystkich typów ofert.  Większość tej sekcji zawiera szczegółowe instrukcje dotyczące tworzenia określonych typów ofert i zarządzania nimi.  

Poniższy film wideo przedstawia różne możliwości i różne typy ofert dostępne w witrynie Azure Marketplace lub AppSource.  Obejmuje to również ważne aspekty techniczne i biznesowe dotyczące publikowania aplikacji lub usługi w ramach tych rynków.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Tworzenie aplikacji i usług dla platformy Azure Marketplace i AppSource — kompilacja 2018**

Aby uzyskać więcej informacji na temat tych rynków, zobacz artykuł [Azure Marketplace i Podręcznik publikowania AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Operacje wspólnej oferty

Proces tworzenia nowej oferty różni się znacznie w różnych typach oferty, na przykład między [ofertą aplikacji platformy Azure](./azure-applications/cpp-azure-app-offer.md) i [ofertą usługi konsultingowej](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  W przeciwieństwie do wielu innych operacji wykonywanych w ramach oferty w [Portal Cloud partner](https://cloudpartner.azure.com) są one dość ustandaryzowane dla różnych typów ofert.  Te Typowe operacje — w tym publikowanie, wyświetlanie stanu, aktualizowanie i usuwanie — są omówione w sekcji [zarządzanie ofertami](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Wersja testowa

*Test Drive* to funkcja portalu Marketplace, która zapewnia klientom "Wypróbuj przed zakupem" opcję demonstracyjną dla każdej oferty, tak aby była włączona.  Możliwości dysku testowego są ograniczone do następujących podzestawów typów ofert: [aplikacje platformy Azure](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 dla zaangażowania klientów](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 dla finansów i operacji](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [aplikacji SaaS](./saas-app/cpp-saas-offer.md)i [maszyn wirtualnych](./virtual-machine/cpp-virtual-machine-offer.md).  Ta funkcja wymaga, aby Wydawca utworzył szablon dysku testowego dostosowany do jego oferty.  Aby uzyskać więcej informacji, zobacz sekcję [dysk testowy](./test-drive/what-is-test-drive.md).

Możesz przeglądać istniejące oferty portalu Marketplace, które mają demonstrację dysku testowego, stosując [Filtr dysku testowego](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace i typy ofert AppSource

W poniższej tabeli wymieniono bieżące typy ofert obsługiwane przez [Portal Cloud partner](https://cloudpartner.azure.com).  Dla każdego typu oferty zawiera listę rynków, w których oferta może być wymieniona, a także ogólny opis technologii rozwiązania oferty.

|                Typ oferty                |  Portal Marketplace  |   Opis                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplikacja platformy Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Rozwiązanie składa się z co najmniej jednej maszyny wirtualnej, opcjonalnie niestandardowego kodu platformy Azure wdrożonego za pomocą szablonu usługi Azure Resource Manager.  Wdrożenie może być przez klienta przez szablon rozwiązania lub zarządzane przez wydawcę. Ten typ służy do zapewniania większej elastyczności niż podany typ oferty maszyny wirtualnej.  |
| [Usługa doradcza](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | oba | Konsultanci wykwalifikowany przez firmę Microsoft mogą wystawiać swoje usługi specyficzne dla domeny w witrynie Azure Marketplace lub AppSource.  Ich wiedza pomaga klientom w ocenie ich problemów oraz tworzeniu i wdrażaniu właściwych rozwiązań w celu spełnienia celów firmy.  |
| [Kontener](./containers/cpp-containers-offer.md)  | Azure | Rozwiązanie to obraz kontenera platformy Docker, który został zainicjowany jako usługa oparta na Kubernetes lub Azure Container Instances. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Pakiet, który rozszerza ten plan zasobów przedsiębiorstwa (ERP) i system zarządzania przedsiębiorstwem. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Pakiet, który rozszerza ten system zarządzania zasobami klienta (CRM) za pomocą usług sprzedaży, usług, projektu i modułów usługi Field Service  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Pakiet rozszerzający tę usługę Enterprise Resource Planning (ERP), która obsługuje zaawansowane funkcje finansów, operacji, produkcji i łańcucha dostaw |
| [Moduł IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Kontener zgodny z platformą Docker, który działa na urządzeniu IoT Edge.  Składa się z małych modułów obliczeniowych, które używają kombinacji kodu niestandardowego, innych usług platformy Azure i usług innych firm. |
| [Aplikacja usługi Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Aplikacja Power BI, która umożliwia dostosowywalne Power BI zawartości, w tym zestawy danych, raporty i pulpity nawigacyjne |
| [Aplikacja SaaS](./saas-app/cpp-saas-offer.md) | Azure | Rozwiązanie to subskrypcja oprogramowania jako usługi, zarządzana przez wydawcę, która loguje się za pomocą dostosowanego interfejsu, który używa Azure Active Directory. |
| [Maszyna wirtualna](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Rozwiązanie jest zawarte w ramach jednej maszyny wirtualnej wdrożonej w ramach subskrypcji klienta.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Aby uzyskać więcej informacji, zobacz [Publikowanie podręcznika według typu oferty](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z ogólnymi operacjami, które można wykonywać na ofertach w witrynie Marketplace oraz ich wspólnych cechach i zasobach technicznych w artykule [zarządzanie ofertami](./manage-offers/cpp-manage-offers.md).
