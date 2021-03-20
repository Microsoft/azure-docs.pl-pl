---
title: Wprowadzenie do akceleratorów rozwiązań IoT — Azure | Microsoft Docs
description: Informacje na temat akceleratorów rozwiązań Azure IoT. Akceleratory rozwiązań IoT to kompletne, kompleksowe oraz gotowe do wdrożenia rozwiązania IoT.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 5012383e64a85ee025273f5339b828f5338e1d4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629072"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Co to są akceleratory rozwiązań Azure IoT?

Oparte na chmurze rozwiązanie IoT zazwyczaj używa niestandardowego kodu i usług w chmurze do zarządzania łącznością urządzeń, przetwarzaniem i analizą danych oraz prezentacją.

Akceleratory rozwiązań IoT to kompletne, gotowe do wdrożenia rozwiązania IoT, w których zaimplementowano typowe scenariusze IoT. Scenariusze obejmują połączoną fabrykę i symulację urządzenia. Podczas wdrażania akceleratora rozwiązania uwzględniane są wszystkie wymagane usługi w chmurze wraz z wszelkim wymaganym kodem aplikacji.

Akceleratory rozwiązań to punkty wyjścia do Twoich własnych rozwiązań IoT. Kod źródłowy wszystkich akceleratorów rozwiązań jest typu open source i jest dostępny w witrynie GitHub. Zachęcamy do pobrania i dostosowania akceleratorów rozwiązań do swoich potrzeb.

Akceleratory rozwiązań mogą również służyć do nauki przed utworzeniem własnego niestandardowego rozwiązania IoT od podstaw. W akceleratorach rozwiązań zaimplementowano sprawdzone praktyki dla opartych na chmurze rozwiązań IoT, do których należy się stosować.

Kod aplikacji każdego akceleratora rozwiązania obejmuje aplikację internetową, która umożliwia zarządzanie akceleratorem rozwiązania.

> [!NOTE]
> Rozwiązania do zdalnego monitorowania i konserwacji predykcyjnej zostały usunięte z witryny [Akceleratory rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators) . Aby dowiedzieć się więcej, zobacz [co to są Akceleratory rozwiązań usługi Azure IoT? (poprzednia wersja)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Obsługiwane scenariusze IoT

Obecnie dostępne są dwa Akceleratory rozwiązań do wdrożenia:

### <a name="connected-factory"></a>Połączona fabryka

[Akcelerator rozwiązań Połączona fabryka](iot-accelerators-connected-factory-features.md) umożliwia zbieranie danych telemetrycznych z zasobów przemysłowych korzystających z interfejsu [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) oraz sterowanie nimi. Zasoby przemysłowe mogą obejmować stanowiska montażu i testowania na fabrycznej linii produkcyjnej.

Pulpit nawigacyjny połączonej fabryki umożliwia monitorowanie urządzeń przemysłowych i zarządzanie nimi:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Zrzut ekranu przedstawiający pulpit nawigacyjny rozwiązania połączonej fabryki." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Symulacja urządzenia

[Akcelerator rozwiązań Symulacja urządzenia](iot-accelerators-device-simulation-overview.md) umożliwia uruchamianie symulowanych urządzeń, które generują realistyczne dane telemetryczne. Za pomocą tego akceleratora rozwiązania możesz przetestować zachowanie innych akceleratorów rozwiązań oraz własnych niestandardowych rozwiązań IoT.

Za pomocą aplikacji internetowej symulacji urządzenia można konfigurować i uruchamiać symulacje:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Zrzut ekranu przedstawiający pulpit nawigacyjny rozwiązania dotyczącego symulacji urządzeń." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Zasady projektowania

Wszystkie akceleratory rozwiązań zaprojektowano zgodnie z tymi samymi zasadami i pod kątem tych samych celów. Zgodnie z projektem mają one być:

* **Skalowalne** — umożliwiać łączenie milionów połączonych urządzeń oraz zarządzać nimi.
* **Rozszerzalne** — dzięki czemu można je dostosowywać do własnych wymagań.
* **Zrozumiałe** — pozwalają poznać sposób, w jaki działają i jak są implementowane.
* **Modułowe** — pozwalają wymieniać usługi w celu skorzystania z alternatyw.
* **Bezpieczne** — łączą zabezpieczenia platformy Azure z wbudowanymi funkcjami łączności i zabezpieczeń urządzeń.

## <a name="architectures-and-languages"></a>Architektury i języki

Pierwotne akceleratory rozwiązań zostały napisane przy użyciu platformy .NET oraz architektury Model-View-Controller (MVC). Firma Microsoft aktualizuje akceleratory rozwiązań do nowej architektury mikrousług. W poniższej tabeli przedstawiono bieżący stan akceleratorów rozwiązań z linkami do repozytoriów GitHub:

| Akcelerator rozwiązania   | Architektura  | Języki     |
| ---------------------- | ------------- | ------------- |
| Połączona fabryka      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Symulacja urządzenia      | Mikrousługi | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Wprowadzenie do architektury referencyjnej usługi Azure IoT](/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Opcje wdrożenia

Akceleratory rozwiązań można wdrażać z witryny [akceleratorów rozwiązań usługi Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) lub przy użyciu wiersza polecenia.

Kosztem działania akceleratora rozwiązań jest łączny [koszt działania używanych przez niego podstawowych usług platformy Azure](https://azure.microsoft.com/pricing). Szczegóły używanych usług platformy Azure są widoczne podczas wybierania opcji wdrażania.

## <a name="next-steps"></a>Następne kroki

Aby wypróbować jeden z akceleratorów rozwiązań IoT, zapoznaj się z [przewodnikiem](quickstart-connected-factory-deploy.md)Szybki Start.
