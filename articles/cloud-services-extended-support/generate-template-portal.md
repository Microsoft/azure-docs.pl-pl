---
title: Generuj szablon ARM dla Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal
description: Generowanie i pobieranie szablonu ARM i pliku parametrów dla Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077117"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Generuj szablon ARM dla Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal

W tym artykule wyjaśniono, jak pobrać szablon ARM i plik parametrów z [Azure Portal](https://portal.azure.com) dla usługi w chmurze. Szablon ARM i plik parametrów mogą być używane w wdrożeniach za pośrednictwem programu PowerShell w celu utworzenia lub zaktualizowania usługi w chmurze

## <a name="get-arm-template-via-portal"></a>Pobierz szablon ARM za pośrednictwem portalu

  1. Przejdź do Azure Portal i [Utwórz nową usługę w chmurze](deploy-portal.md). Dodaj konfigurację usługi w chmurze, pliki pakietów i definicje. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="Obraz przedstawia sekcję przekazywanie na karcie podstawy podczas tworzenia.":::
  
  2. Po zakończeniu wszystkich pól przejdź na kartę Przegląd i tworzenie, aby sprawdzić poprawność konfiguracji wdrożenia, a następnie kliknij pozycję **Pobierz szablon w celu automatyzacji usługi w** chmurze (obsługa rozszerzona).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="Obraz przedstawia pobieranie szablonu w ramach usługi w chmurze (obsługa rozszerzona) na Azure Portal.":::
  
  3. Pobierz szablony i pliki parametrów. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="Obraz pokazuje pobieranie pliku szablonu na Azure Portal.":::
  
  4. Skopiuj identyfikator URI sygnatury dostępu współdzielonego pakietu i identyfikator URI konfiguracji z karty Przegląd i tworzenie i Dodaj do parameter.jsw pliku. Te pliki mogą być teraz używane do tworzenia nowej usługi w chmurze za pomocą programu PowerShell.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="Obraz przedstawia parametry identyfikatora URI sygnatury dostępu współdzielonego pakietu i identyfikatora URI konfiguracji na Azure Portal.":::
  
## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdrażanie usługi w chmurze (obsługa rozszerzona) przy użyciu [Azure Portal](deploy-portal.md)
  
