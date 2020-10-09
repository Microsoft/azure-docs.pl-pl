---
title: Przykładowe dane w różnych lokalizacjach usługi Azure Storage — proces nauki danych zespołu
description: Przykładowe dane w kontenerach obiektów blob platformy Azure, SQL Server i tabelach Hive w celu zmniejszenia ich do mniejszej, ale reprezentatywnej i większej wielkości możliwej do zarządzania.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76718607"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Przykładowe dane w kontenerach obiektów blob platformy Azure, programu SQL Server i tabel programu Hive

W poniższych artykułach opisano sposób próbkowania danych przechowywanych w jednej z trzech różnych lokalizacji platformy Azure:

* [**Dane kontenera obiektów blob platformy Azure**](sample-data-blob.md) są próbkowane przez pobranie go programowo, a następnie próbkowanie go przy użyciu przykładowego kodu w języku Python.
* [**SQL Server dane**](sample-data-sql-server.md) są próbkowane przy użyciu języka SQL i programowania w języku Python. 
* Próbkowanie [**danych tabeli Hive**](sample-data-hive.md) przy użyciu zapytań programu Hive.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Dlaczego dane przykładowe?**

Jeśli zestaw danych, który planujesz analizować jest duży, zazwyczaj dobrym pomysłem jest Przepróbkowanie danych w celu zmniejszenia ich do mniejszej, ale reprezentatywnej i większej możliwej do zarządzania wielkości. Downsizing mogą ułatwić zrozumienie, eksplorację i inżynierowanie danych. Ta rola próbkowania w procesie Cortana Analytics ma na celu umożliwienie szybkiego tworzenia prototypów funkcji przetwarzania danych i modeli uczenia maszynowego.

